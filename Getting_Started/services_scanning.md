# Service Scanning
## NMAP
### Comando
### Escanea los primeros 1000 puertos mas comunes
nmap 10.129.42.253 
Si no se especifica TCP o UDP escanea solo TCP
### Response
Starting Nmap 7.80 ( https://nmap.org ) at 2021-02-25 16:07 EST
Nmap scan report for 10.129.42.253
Host is up (0.11s latency).
Not shown: 995 closed ports
PORT    STATE SERVICE
21/tcp  open  ftp
22/tcp  open  ssh
80/tcp  open  http
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds

Nmap done: 1 IP address (1 host up) scanned in 2.19 seconds
-------------------------------------------------------
### Comando
nmap -sV -sC -p- 10.129.42.253
-sc solicita mas info de los puertos
-sV muestra la version que estan corriendo en los puertos
-p Escanea todos los puertos 65535 puertos
### Response 
Starting Nmap 7.80 ( https://nmap.org ) at 2021-02-25 16:18 EST
Nmap scan report for 10.129.42.253
Host is up (0.11s latency).
Not shown: 65530 closed ports
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 ftp      ftp          4096 Feb 25 19:25 pub
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.14.2
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp  open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
80/tcp  open  http        Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: PHP 7.4.3 - phpinfo()
139/tcp open  netbios-ssn Samba smbd 4.6.2
445/tcp open  netbios-ssn Samba smbd 4.6.2
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_nbstat: NetBIOS name: GS-SVCSCAN, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-02-25T21:21:51
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 233.68 seconds
## NMAP Script
### Correr un scrip especifico
nmap --script <script name> -p<port> <host>
-sC corre los script por defecto
si queremos correr uno en particular podemos localizarlos en 
facuuni8@htb[/htb]$ locate scripts/citrix

## Attacking Network Services
### Probando Netcat
### comando
nc -nv 10.129.42.253 21
### respuesta
(UNKNOWN) [10.129.42.253] 21 (ftp) open
220 (vsFTPd 3.0.3) version del servicio ftp

### FTP
#### Comando
nmap -sC -sV -p21 10.129.42.253
-p21 especifica el puerto
#### Response
Starting Nmap 7.80 ( https://nmap.org ) at 2020-12-20 00:54 GMT
Nmap scan report for 10.129.42.253
Host is up (0.081s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 ftp      ftp          4096 Dec 19 23:50 pub
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.14.2
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1.78 seconds
### Conectandonos al servicio ftp
#### Comando
ftp -p 10.129.42.253
#### Response
Connected to 10.129.42.253.
220 (vsFTPd 3.0.3)
Name (10.129.42.253:user): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.

ftp> ls

227 Entering Passive Mode (10,129,42,253,158,60).
150 Here comes the directory listing.
drwxr-xr-x    2 ftp      ftp          4096 Feb 25 19:25 pub
226 Directory send OK.

ftp> cd pub

250 Directory successfully changed.

ftp> ls

227 Entering Passive Mode (10,129,42,253,182,129).
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp            18 Feb 25 19:25 login.txt
226 Directory send OK.

#### extraigo el archivo de manera remota
ftp> get login.txt

local: login.txt remote: login.txt
227 Entering Passive Mode (10,129,42,253,181,53).
150 Opening BINARY mode data connection for login.txt (18 bytes).
226 Transfer complete.
18 bytes received in 0.00 secs (165.8314 kB/s)

ftp> exit
## SMB
#### Comando
nmap --script smb-os-discovery.nse -p445 10.10.10.40
Ejecuta el script puntual en el puerto especificado
#### Response
Starting Nmap 7.91 ( https://nmap.org ) at 2020-12-27 00:59 GMT
Nmap scan report for doctors.htb (10.10.10.40)
Host is up (0.022s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-os-discovery: 
|   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
|   Computer name: CEO-PC
|   NetBIOS computer name: CEO-PC\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2020-12-27T00:59:46+00:00

Nmap done: 1 IP address (1 host up) scanned in 2.71 seconds
#### Comando
nmap -A -p445 10.129.42.253
-A Habilite la detección de sistema operativo, detección de versión, escaneo de scripts y traceroute
#### Response
Starting Nmap 7.80 ( https://nmap.org ) at 2021-02-25 16:29 EST
Nmap scan report for 10.129.42.253
Host is up (0.11s latency).

PORT    STATE SERVICE     VERSION
445/tcp open  netbios-ssn Samba smbd 4.6.2
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 2.6.32 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Adtran 424RG FTTH gateway (92%), Linux 2.6.39 - 3.2 (92%), Linux 3.1 - 3.2 (92%), Linux 3.2 - 4.9 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops

Host script results:
|_nbstat: NetBIOS name: GS-SVCSCAN, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-02-25T21:30:06
|_  start_date: N/A

TRACEROUTE (using port 445/tcp)
HOP RTT       ADDRESS
1   111.62 ms 10.10.14.1
2   111.89 ms 10.129.42.253

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.72 seconds
### Shares
#### Comando
smbclient -N -L \\\\10.129.42.253
-L Especifica que queremos una lista de los recursos compartidos el el host remoto
-N Suprime la solicitud de contraseña
#### Response

	Sharename       Type      Comment
	---------       ----      -------
	print$          Disk      Printer Drivers
	users           Disk      
	IPC$            IPC       IPC Service (gs-svcscan server (Samba, Ubuntu))
SMB1 disabled -- no workgroup available

#### Comando
smbclient \\\\10.129.42.253\\users
#### Response
Enter WORKGROUP\users's password: 
Try "help" to get a list of possible commands.

smb: \> ls
NT_STATUS_ACCESS_DENIED listing \*

smb: \> exit
### This reveals the non-default share users. Let us attempt to connect as the guest user.

### Usando credenciales especificas
smbclient -U bob \\\\10.129.42.253\\users 
-U especifica un usuario
bob el nombre del usuario
\\user especifica la carpeta usuario en el host
#### response 
smb: \> ls
  .                                   D        0  Thu Feb 25 16:42:23 2021
  ..                                  D        0  Thu Feb 25 15:05:31 2021
  bob                                 D        0  Thu Feb 25 16:42:23 2021

		4062912 blocks of size 1024. 1332480 blocks available
		
smb: \> cd bob

smb: \bob\> ls
  .                                   D        0  Thu Feb 25 16:42:23 2021
  ..                                  D        0  Thu Feb 25 16:42:23 2021
  passwords.txt                       N      156  Thu Feb 25 16:42:23 2021

		4062912 blocks of size 1024. 1332480 blocks available
		
smb: \bob\> get passwords.txt 
getting file \bob\passwords.txt of size 156 as passwords.txt (0.3 KiloBytes/sec) (average 0.3 KiloBytes/sec)
## SNMP
Las cadenas de comunidad SNMP proporcionan información y estadísticas sobre un enrutador o dispositivo, ayudándonos a obtener acceso a él. Las cadenas de comunidad [públicas] y [privadas] predeterminadas del fabricante a menudo no se modifican. En las versiones 1 y 2c de SNMP, el acceso se controla mediante una cadena de comunidad de texto sin formato y, si conocemos el nombre, podemos acceder a ella. El cifrado y la autenticación sólo se agregaron en la versión 3 de SNMP. Se puede obtener mucha información de SNMP. El examen de los parámetros del proceso podría revelar las credenciales pasadas en la línea de comando, que podrían reutilizarse para otros servicios accesibles externamente, dada la prevalencia de la reutilización de contraseñas en entornos empresariales. También se puede revelar información de enrutamiento, servicios vinculados a interfaces adicionales y la versión del software instalado.
#### Comando
snmpwalk -v 2c -c public 10.129.42.253 1.3.6.1.2.1.1.5.0
#### response
iso.3.6.1.2.1.1.5.0 = STRING: "gs-svcscan"
#### Comando
snmpwalk -v 2c -c private  10.129.42.253 
#### Response
Timeout: No Response from 10.129.42.253
### onesixtyone
Se puede utilizar una herramienta como [onesixtyone] para aplicar fuerza bruta a los nombres de cadenas de comunidad utilizando un archivo de diccionario de cadenas de comunidad comunes, como el archivo dict.txt incluido en el repositorio de GitHub de la herramienta
#### Comando
onesixtyone -c dict.txt 10.129.42.254
#### response
Scanning 1 hosts, 51 communities
10.129.42.254 [public] Linux gs-svcscan 5.4.0-66-generic #74-Ubuntu SMP Wed Jan 27 22:54:38 UTC 2021 x86_64

### Conclucion
El escaneo y la enumeración de servicios es un tema muy amplio sobre el que aprenderemos más a medida que avancemos. Los aspectos que hemos cubierto aquí se aplican a muchas redes, incluidas las máquinas HTB.