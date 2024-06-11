# Privilege Escalation

Nuestro acceso inicial a un servidor remoto suele estar en el contexto de un usuario de ***bajo privilegio***, lo que no nos daría acceso completo sobre la caja. Para obtener acceso completo, tendremos que encontrar una vulnerabilidad interna/local que **escalaría** nuestros privilegios a la root usuario en Linux o el administrator/SYSTEM usuario en Windows. Caminemos a través de algunos métodos comunes para escalar nuestros privilegios.

## Listas de verificacion de PrivEsc
Una vez que obtengamos acceso inicial a un cuadro, queremos enumerar a fondo el cuadro para encontrar cualquier vulnerabilidad potencial que podamos explotar.
Estas plataforma tienen muchas opciones para escalamiento de privilegios
1. [HackTricks](https://book.hacktricks.xyz/) que tiene una excelente lista de verificación para ambos Linux y Windows escalada de privilegios locales. .
1. [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings) también tiene listas de verificación para ambos Linux y Windows. 

## Enumeration Scrips
Muchos de los comandos anteriores se pueden ejecutar automáticamente con un script para revisar el informe y buscar cualquier debilidad. 
Algunos de los scripts de enumeración de Linux comunes incluyen [LinEnum](https://github.com/rebootuser/LinEnum) y [linuxprivchecker](https://github.com/sleventyeleven/linuxprivchecker), y para Windows incluyen [Seatbelt](https://github.com/GhostPack/Seatbelt) y [JAWS](https://github.com/411Hall/JAWS).
Otra herramienta útil que podemos utilizar para la enumeración de servidores es la [PrivilegeEscalationAwesomeScriptsSUITE(PEASS)](https://github.com/peass-ng/PEASS-ng) , ya que está bien mantenido para mantenerse actualizado e incluye scripts para enumerar Linux y Windows.

>[!NOTE]
>Nota: Estos scripts ejecutarán muchos comandos conocidos por identificar vulnerabilidades y crear mucho "ruido" eso puede desencadenar software antivirus o software de monitoreo de seguridad que busca este tipo de eventos. Esto puede evitar que los scripts se ejecuten o incluso activar una alarma de que el sistema se ha visto comprometido. En algunos casos, es posible que deseemos hacer una enumeración manual en lugar de ejecutar scripts.
### Ejemplo
script de Linux desde PEASS llamado LinPEAS:
####Escalada Privilegio

```
user@htb[/htb]$ ./linpeas.sh
...SNIP...

Linux Privesc Checklist: https://book.hacktricks.xyz/linux-unix/linux-privilege-escalation-checklist
 LEYEND:
  RED/YELLOW: 99% a PE vector
  RED: You must take a look at it
  LightCyan: Users with console
  Blue: Users without console & mounted devs
  Green: Common things (users, groups, SUID/SGID, mounts, .sh scripts, cronjobs)
  LightMangenta: Your username

====================================( Basic information )=====================================
OS: Linux version 3.9.0-73-generic
User & Groups: uid=33(www-data) gid=33(www-data) groups=33(www-data)
...SNIP...
```
### Explotacion de Kernel
Revisar kernel parches actualizaciones, la falta de estas ultimas, etc para poder ver que puerta vulnerable aparece.
Un servidor no actualizado o la falta de un parche de seguridad

### Software vulnerable
podemos usar el siguiente comando en Linux
``` 
dpkg -l
```
 o mirar en Windows
 ```
 C:\Program Files
 ```
 para ver qué software está instalado en el sistema. Y tener encuenta las versiones viejas o las parcheadas o crackeadas....

 ## Privilegios de Usuario
 1. Sudo
 1. SUID
 1. Windows Token Privileges
 ```
 facuuni8@htb
[/htb]
$ 
sudo -l

[sudo] password for user1:
...SNIP...

User user1 may run the following commands on ExampleServer:
    (ALL : ALL) ALL
 ```
### Privilege Escalation
```
facuuni8@htb[/htb]$ sudo su -

[sudo] password for user1:
whoami
root
```
El comando anterior requiere una contraseña para ejecutar cualquier comando con sudo. Hay ciertas ocasiones en las que se nos permite ejecutar ciertas aplicaciones, o todas las aplicaciones, sin tener que proporcionar una contraseña:
```
facuuni8@htb[/htb]$ sudo -l

    (user : user) NOPASSWD: /bin/echo
```
Una vez que encontramos una aplicación en particular, podemos ejecutar con sudo, podemos buscar formas de explotarlo para obtener un shell como usuario raíz. [GTFOBinas](https://gtfobins.github.io/) contiene una lista de comandos y cómo pueden ser explotados a través de sudo. Podemos buscar la aplicación que tenemos sudo privilegio sobre, y si existe, puede decirnos el comando exacto que debemos ejecutar para obtener acceso root utilizando el sudo privilegio que tenemos.
[LOLBAS](https://lolbas-project.github.io/#) también contiene una lista de aplicaciones de Windows que podemos aprovechar para realizar ciertas funciones, como descargar archivos o ejecutar comandos en el contexto de un usuario privilegiado.

## Tareas Programadas
Crear tareas programadas para que se ejecuten automaticamente cada cierto tiempo
1. Añadir nuevas tareas programadas/trabajos cron
1. Engañarlos para ejecutar un software malicioso
Hay directorios específicos que podemos utilizar para agregar nuevos trabajos cron si tenemos el write permisos sobre ellos.
1. /etc/crontab
1. /etc/cron.d
1. /var/spool/cron/crontabs/root
Si podemos escribir en un directorio llamado por un trabajo cron, podemos escribir un script bash con un comando shell inverso, que debería enviarnos un shell inverso cuando se ejecuta.

## Credenciales Expuestas
A continuación, podemos buscar archivos que podamos leer y ver si contienen credenciales expuestas. Esto es muy común con **configuration** archivos, **log** archivos y archivos de historial de usuario (**bash_history** en Linux y **PSReadLine** en Windows)
#### Ejemplo
```
...SNIP...
[+] Searching passwords in config PHP files
[+] Finding passwords inside logs (limit 70)
...SNIP...
/var/www/html/config.php:$conn = new mysqli(localhost, 'db_user', 'password123');
```
Como podemos ver, **la contraseña de la base de datos** 'password123' está expuesto, lo que nos permitiría iniciar sesión en el local mysql bases de datos y buscar información interesante.
Podemos probar la misma contraseña para ingresar como usuario ROOT
```
facuuni8@htb[/htb]$ su -

Password: password123
whoami

root
```
## Keys SSH
Si tenemos acceso de lectura al directorio **.ssh**
podemos leer sus claves ssh privadas que se encuentran en /home/user/.ssh/id_rsa o /root/.ssh/id_rsa
y podemos copiar el contenido y crear nuestra propia key e ingresar como root con el comando **-i**

```
facuuni8@htb[/htb]$ vim id_rsa
facuuni8@htb[/htb]$ chmod 600 id_rsa
facuuni8@htb[/htb]$ ssh root@10.10.10.10 -i id_rsa

root@10.10.10.10#
```
1. **vim id_rsa** con este comando creamos el archivo y copiamos el contenido extraido de la maquina remota
1. **chmod 600 id_rsa** 
1. **ssh root@10.10.10.10 -i id_rsa** accedemos a la maquina remota.
Si nos encontramos con acceso de escritura a un usuario/.ssh/ directorio, podemos colocar nuestra clave pública en el directorio ssh del usuario en **/home/user/.ssh/authorized_keys**.
Esta técnica generalmente se usa para obtener acceso ssh después de obtener un shell como ese usuario

Debemos crear una nueva clave con ssh-keygen y el -f

```
facuuni8@htb[/htb]$ ssh-keygen -f key

Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase): *******
Enter same passphrase again: *******

Your identification has been saved in key
Your public key has been saved in key.pub
The key fingerprint is:
SHA256:...SNIP... user@parrot
The key's randomart image is:
+---[RSA 3072]----+
|   ..o.++.+      |
...SNIP...
|     . ..oo+.    |
+----[SHA256]-----+
```
Esto nos dará dos archivos: key (que usaremos con **ssh -i**) y key.pub, que copiaremos a la máquina remota. Vamos a copiar **key.pub**, luego en la máquina remota, la agregaremos **/root/.ssh/authorized_keys**:
```
user@remotehost$ echo "ssh-rsa AAAAB...SNIP...M= user@parrot" >> /root/.ssh/authorized_keys
```
Ejecutamos
```
facuuni8@htb[/htb]$ ssh root@10.10.10.10 -i key

root@remotehost#
```