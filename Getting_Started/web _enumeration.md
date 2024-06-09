# Web Enumeration
Al realizar el escaneo del servicio, a menudo nos encontraremos con servidores web que se ejecutan en los puertos 80 y 443. Los servidores web alojan aplicaciones web (a veces más de 1) que a menudo proporcionan una superficie de ataque considerable y un objetivo de muy alto valor durante una prueba de penetración. La enumeración web adecuada es crítica, especialmente cuando una organización no está exponiendo muchos servicios o esos servicios están parcheados adecuadamente.

## Gobuster
Podemos utilizar una herramienta como [ffuf] o [GoBuster] para realizar esta enumeración de directorios.
### Directorio/Enumeración de Archivos
GoBuster es una herramienta versátil que permite <<realizar DNS, vhost y fuerza bruta de directorio >>. La herramienta tiene funcionalidad adicional, como la enumeración de cubos públicos AWS S3. Para los propósitos de este módulo, estamos interesados en los modos de fuerza bruta de directorio (y archivo) especificados con el interruptor dir. Ejecutemos un simple escaneo usando el dirb common.txt lista de palabras.
### Enumeración Web
#### Comando
gobuster dir -u http://10.10.10.121/ -w /usr/share/dirb/wordlists/common.txt
#### Response
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
[+] Url:            http://10.10.10.121/
[+] Threads:        10
[+] Wordlist:       /usr/share/dirb/wordlists/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
2020/12/11 21:47:25 Starting gobuster
/.hta (Status: 403)
/.htpasswd (Status: 403)
/.htaccess (Status: 403)
/index.php (Status: 200)
/server-status (Status: 403)
/wordpress (Status: 301)
2020/12/11 21:47:46 Finished

### Enumeración de subdominio DNS
Primero, clonemos las SecLists GitHub repo, que contiene muchas listas útiles para fuzzing y explotación:
#### Instalar SecLists
git clone https://github.com/danielmiessler/SecLists
sudo apt install seclists -y
#### Agregamos servidor DNS
A continuación, agregue un servidor DNS como 1.1.1.1 al /etc/resolv.conf archivo. Apuntaremos al dominio inlanefreight.com, el sitio web de una empresa ficticia de carga y logística.
#### Comando
gobuster dns -d inlanefreight.com -w /usr/share/SecLists/Discovery/DNS/namelist.txt
#### Response
obuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
[+] Domain:     inlanefreight.com
[+] Threads:    10
[+] Timeout:    1s
[+] Wordlist:   /usr/share/SecLists/Discovery/DNS/namelist.txt
2020/12/17 23:08:55 Starting gobuster
Found: blog.inlanefreight.com
Found: customer.inlanefreight.com
Found: my.inlanefreight.com
Found: ns1.inlanefreight.com
Found: ns2.inlanefreight.com
Found: ns3.inlanefreight.com
2020/12/17 23:10:34 Finished

## Consejos de Enumeración Web
### Banner Grabbing / Encabezados de Servidor Web
Pueden revelar el marco de aplicación específico en uso, las opciones de autenticación y si al servidor le faltan opciones de seguridad esenciales o si se ha configurado mal.
Podemos usar cURL para recuperar la información del encabezado del servidor desde la línea de comandos.
#### Enumeración Web
#### Comando
curl -IL https://www.inlanefreight.com
#### Response  
HTTP/1.1 200 OK
Date: Fri, 18 Dec 2020 22:24:05 GMT
Server: Apache/2.4.29 (Ubuntu)
Link: <https://www.inlanefreight.com/index.php/wp-json/>; rel="https://api.w.org/"
Link: <https://www.inlanefreight.com/>; rel=shortlink
Content-Type: text/html; charset=UTF-8

Otra herramienta útil es EyeWitness, que se puede usar para tomar capturas de pantalla de las aplicaciones web de destino, tomarles huellas digitales e identificar posibles credenciales predeterminadas.

### WhatWeb
Podemos extraer la versión de servidores web, marcos de soporte y aplicaciones utilizando la herramienta de línea de comandos whatweb.
#### Comando
whatweb 10.10.10.121
#### Response
http://10.10.10.121 [200 OK] Apache[2.4.41], Country[RESERVED][ZZ], Email[license@php.net], HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.10.10.121], Title[PHP 7.4.3 - phpinfo()]

Whatwebes una herramienta útil y contiene mucha funcionalidad para automatizar la enumeración de aplicaciones web en una red.
#### Comando
whatweb --no-errors 10.10.10.0/24
#### Response
http://10.10.10.11 [200 OK] Country[RESERVED][ZZ], HTTPServer[nginx/1.14.1], IP[10.10.10.11], PoweredBy[Red,nginx], Title[Test Page for the Nginx HTTP Server on Red Hat Enterprise Linux], nginx[1.14.1]
http://10.10.10.100 [200 OK] Apache[2.4.41], Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.10.10.100], Title[File Sharing Service]
http://10.10.10.121 [200 OK] Apache[2.4.41], Country[RESERVED][ZZ], Email[license@php.net], HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.10.10.121], Title[PHP 7.4.3 - phpinfo()]
http://10.10.10.247 [200 OK] Bootstrap, Country[RESERVED][ZZ], Email[contact@cross-fit.htb], Frame, HTML5, HTTPServer[OpenBSD httpd], IP[10.10.10.247], JQuery[3.3.1], PHP[7.4.12], Script, Title[Fine Wines], X-Powered-By[PHP/7.4.12], X-UA-Compatible[ie=edge]

### Certificados
Estos podrían usarse potencialmente para llevar a cabo un ataque de phishing si esto está dentro del alcance de una evaluación.
![Alt text](image.png)
https://academy.hackthebox.com/storage/modules/77/cert.png

### Robots.txt
 Es común que los sitios web contengan un robots.txt archivo, cuyo propósito es instruir a los rastreadores web de motores de <<búsqueda como Googlebot>> a qué recursos se puede y no se puede acceder para la indexación. El robots.txt el archivo puede proporcionar información valiosa, como la ubicación de archivos privados y páginas de administración. En este caso, vemos que el robots.txt el archivo contiene dos entradas no permitidas.

user-agents: *
disallow: /private
disallow: /uploaded_files

 http://10.10.10.121/private Muestra una pagina de inicio de sesion

 ### Codigo Fuente
 https://academy.hackthebox.com/storage/modules/77/source.png