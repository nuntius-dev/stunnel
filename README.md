# Configuración de stunnel para Redirección de SSH

Este archivo de configuración de `stunnel` permite establecer un túnel seguro para conexiones SSH, redirigiendo conexiones desde el puerto 2443 al puerto 22 en el sistema local. Es ideal para escenarios donde deseas proteger la conexión SSH usando TLS.

---

## Requisitos

1. **stunnel** instalado en el sistema.
2. Un certificado y clave privada en el archivo `/etc/stunnel/stunnel.pem`.
3. Acceso a los puertos 2443 (para conexiones externas) y 22 (para SSH en el sistema local).

---

## Descripción del Archivo de Configuración

```ini
cert = /etc/stunnel/stunnel.pem            # Ruta del certificado y clave privada
sslVersion = TLSv1                         # Versión mínima de TLS
options = NO_SSLv2                         # Deshabilitar SSLv2
options = NO_SSLv3                         # Deshabilitar SSLv3
chroot = /var/run/stunnel                  # Carpeta de chroot para mayor seguridad
setuid = nobody                            # Ejecutar el proceso como el usuario 'nobody'
setgid = nobody                            # Ejecutar el proceso como el grupo 'nobody'
pid = /stunnel.pid                         # Archivo PID para el proceso stunnel
socket = l:TCP_NODELAY=1                   # Optimización TCP en el lado del cliente
socket = r:TCP_NODELAY=1                   # Optimización TCP en el lado del servidor

[sshd]                                     # Sección de configuración para SSH
accept = 2443                              # Puerto de escucha para conexiones TLS
connect = 22                               # Puerto interno al que se redirige (SSH)
TIMEOUTclose = 0                           # Tiempo de espera para cerrar conexiones
```

---

## Pasos de Configuración

### 1. Instalar stunnel
En sistemas basados en Debian/Ubuntu:
```bash
sudo apt update
sudo apt install stunnel4
```

### 2. Crear el archivo `stunnel.pem`
Combina el certificado y la clave privada en un solo archivo:
```bash
cat server.crt server.key > /etc/stunnel/stunnel.pem
chmod 600 /etc/stunnel/stunnel.pem
```

### 3. Configurar stunnel
Guarda el contenido proporcionado en un archivo de configuración, por ejemplo, `/etc/stunnel/stunnel.conf`.

### 4. Iniciar el servicio
Habilita y ejecuta `stunnel`:
```bash
sudo systemctl enable stunnel4
sudo systemctl start stunnel4
```

### 5. Verificar el estado
Asegúrate de que el servicio esté activo:
```bash
sudo systemctl status stunnel4
```

---

## Probando la Conexión

1. Conéctate al puerto 2443 usando un cliente SSH que soporte TLS:
   ```bash
   ssh -p 2443 -o "ProxyCommand=openssl s_client -quiet -connect %h:%p" user@hostname
   ```

2. Verifica que la conexión redirige correctamente al puerto 22 del servidor.

---

## Seguridad

- **TLS 1.0**: Considera actualizar la configuración para soportar versiones más recientes de TLS (por ejemplo, TLS 1.2 o 1.3) si es posible.
- **Certificados**: Utiliza certificados confiables y mantén su confidencialidad.
- **Usuarios y permisos**: Ejecuta `stunnel` con privilegios mínimos para mayor seguridad.

---

¡Gracias por usar `stunnel` para asegurar tus conexiones SSH!, si quieres saber mas de tutoriales visita: https://bit.ly/3Ou1SQ9
