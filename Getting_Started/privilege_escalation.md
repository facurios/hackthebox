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
Escalada Privilegio

facuuni8@htb[/htb]$ ./linpeas.sh
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

### Discutamos algunas de las vulnerabilidades que debemos buscar en la salida de estos scripts.
### Explotacion de Kernel