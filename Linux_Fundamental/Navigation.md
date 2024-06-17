# Linux Fundamental
## Navigation
En que ubicacion nos encontramos **pwd**
```
cry0l1t3@htb[~]$ pwd

/home/cry0l1t3
```
Listar contenido del directorio **ls**
```
cry0l1t3@htb[~]$ ls

Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos
```
**ls -l** Muestra el listado con mas informacion 
```
cry0l1t3@htb[~]$ ls -l

total 32
drwxr-xr-x 2 cry0l1t3 htbacademy 4096 Nov 13 17:37 Desktop
drwxr-xr-x 2 cry0l1t3 htbacademy 4096 Nov 13 17:34 Documents
drwxr-xr-x 3 cry0l1t3 htbacademy 4096 Nov 15 03:26 Downloads
```
**Contenido de Columna**	**Descripción**
drwxr-xr-x	            Tipo y permisos
2                       Número de enlaces duros al archivo/directorio
cry0l1t3	            Propietario del archivo/directorio
htbacademy	            Propietario del grupo del archivo/directorio
4096	                Tamaño del archivo o el número de bloques utilizados para almacenar la inf. del directorio
Nov 13 17:37            Fecha y hora
Desktop	                Nombre del directorio

**ls -la** Esta lista todo *list all*
cry0l1t3@htb[~]$ ls -la

total 403188
drwxr-xr-x 2 cry0l1t3 htbacademy 4096 Nov 13 17:37 .bash_history
drwxr-xr-x 2 cry0l1t3 htbacademy 4096 Nov 13 17:37 .bashrc
...SNIP...

*Listar desde otro directorio*
No es necesario estar parado en en mismo directorio para listar su contenido
```
cry0l1t3@htb[~]$ ls -l /var/
```
*Ingresar a una carpeta/directorio*
```
cry0l1t3@htb[~]$ cd <directorio>
```
Ejemplos
cry0l1t3@htb[~]$ cd /dev/shm/
cry0l1t3@htb[~]$ cd dev