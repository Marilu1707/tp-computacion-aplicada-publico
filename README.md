# Computación Aplicada — VM pública

Copia independiente de Debian 12 para descargar e importar en VirtualBox. No contiene contraseñas preconfiguradas, claves privadas ni los respaldos sensibles de la entrega privada.

## Inicio
1. Descargar `TPServer-Publica.ova` desde Releases e importarla en VirtualBox.
2. Iniciar la VM y crear una contraseña propia de root cuando la consola lo solicite.
3. Ingresar como `root` con la contraseña recién elegida.
4. Abrir http://127.0.0.1:8082 en la computadora anfitriona para ver el sitio.

La red usa NAT. El puerto 8082 del anfitrión llega al Apache de la VM. El puerto 2224 corresponde a SSH; para usarlo hay que agregar una clave pública propia a `/root/.ssh/authorized_keys`. Root no admite contraseña por SSH. No se incluye la clave de la cátedra.

## Servicios y datos
- Apache, PHP y MariaDB; base `compu_aplicada`, 5 alumnos ficticios y 15 notas.
- La aplicación usa autenticación local por socket Unix con `www-data`; MariaDB no escucha en TCP.
- Montajes `/home`, `/www_dir` y `/backup_dir`.
- Backup: `/opt/scripts/backup_full.sh /www_dir /backup_dir`.
- Cron conserva las tareas de respaldo diario de logs y lunes, miércoles y viernes del sitio. Los respaldos anteriores se quitaron por contener credenciales privadas.

## Verificación
```sh
systemctl is-active apache2 mariadb cron
for punto in /home /www_dir /backup_dir; do findmnt "$punto"; done
mariadb -e 'SELECT COUNT(*) FROM compu_aplicada.notas;'
crontab -l
/opt/scripts/backup_full.sh /www_dir /backup_dir
```
La prueba del sitio, la consulta de 15 notas y el backup se ejecutaron sobre la copia antes de exportarla. El asistente de primer inicio crea claves SSH nuevas después de elegir la contraseña.

Esta edición pública cambia el acceso y la red para distribuirse sin secretos. La VM original de entrega y su documentación de acceso permanecen privadas.
