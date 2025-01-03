
# Práctica de Servidores DNS Maestro y Esclavo

Este repositorio contiene la configuración para desplegar un entorno de servidores DNS utilizando Vagrant y Bind9. La práctica incluye un servidor DNS maestro y un servidor DNS esclavo, ambos configurados para manejar zonas directas e inversas en la red 192.168.57.0/24.

## Estructura del Proyecto

El proyecto tiene la siguiente estructura de archivos:

dns-practica/ 
 Vagrantfile # Configuración de las máquinas virtuales 
 .gitignore # Archivos y directorios que no se deben seguir 
 README.md # Documentación del proyecto
 LICENSE # Licencia del proyecto

## Configuración de las Máquinas Virtuales

El archivo `Vagrantfile` contiene la configuración para dos servidores:

1. **Servidor DNS Maestro (dns_master)**
   - FQDN: `master.sistema.test`
   - IP: `192.168.57.101`
   
2. **Servidor DNS Esclavo (dns_slave)**
   - FQDN: `slave.sistema.test`
   - IP: `192.168.57.102`

### Instrucciones para Crear las Máquinas Virtuales

1. Clona este repositorio:

   git clone <URL_DEL_REPOSITORIO>
   cd dns-practica

2. Inicia las maquinas  virtuales con vagrant
    vagrant up

3. Accede a las máquinas virtuales:

    Para el servidor maestro:
        vagrant ssh dns_master

    Para el servidor esclavo:
        vagrant ssh dns_slave
## Configuración de Bind9

1. Edita el archivo de configuración de zonas:
     sudo nano /etc/bind/named.conf.local

2. Crea el archivo de zona directa:
    sudo cp /etc/bind/db.local /etc/bind/db.sistema.test
    sudo nano /etc/bind/db.sistema.test

3. Crea el archivo de zona inversa:
    sudo cp /etc/bind/db.127 /etc/bind/db.192
    sudo nano /etc/bind/db.192

4. Edita el archivo de configuración de zonas en el esclavo:
    sudo nano /etc/bind/named.conf.local

## Datos del DNS

1. 
Hacemos sudo systemctl restart bind9. Dentro de este archivo, agrega la opción options, y asegúrate de que Bind9 escuche únicamente en direcciones IPv4.options {
    // Escuchar solo en IPv4
    listen-on { any; };   
    listen-on-v6 { none; };  
    ...
};

2. 
Para habilitar la opción DNSSEC y establecer la opción de validación (dnssec-validation) en yes en Bind9, debes modificar el archivo de configuración principal de Bind9, generalmente el archivo named.conf.options.
Dentro del archivo, en la sección options, agrega o modifica la línea correspondiente a DNSSEC. 

options {
    ...
    dnssec-validation yes;  
    ...
}; 

3. 
Para permitir que los servidores DNS realicen consultas recursivas únicamente para los ordenadores en las redes 127.0.0.0/8 y 192.168.57.0/24, puedes utilizar la opción de listas de control de acceso (ACL) en la configuración de Bind9.

Dentro del archivo, agrega una sección para las ACLs antes de la sección options.

bash

acl "clientes" {
    127.0.0.0/8;              (localhost)
    192.168.57.0/24;         
};

options {
    ...
    recursion yes;         
    allow-recursion { clientes; };  
    ...
};

4. 
ara configurar el servidor maestro como tierra.sistema.test y asegurarte de que tenga autoridad sobre la zona directa e inversa, debes realizar algunas modificaciones en la configuración de Bind9. 
Dentro de /etc/bind/named.conf.local,configuramos:

// Zona directa
zone "sistema.test" {
    type master;  
    file "/etc/bind/db.sistema.test";  
    allow-transfer { 192.168.57.102; };  
};

// Zona inversa
zone "57.168.192.in-addr.arpa" {
    type master;  
    file "/etc/bind/db.192"; 
    allow-transfer { 192.168.57.102; };  
};

Ahora, necesitas asegurarte de que los archivos de zona existen y están configurados correctamente: sudo nano /etc/bind/db.sistema.test
Esto tambienen el de zona inversa.

5. 
Para configurar el servidor esclavo como venus.sistema.test y hacer que tenga como maestro a tierra.sistema.test, debes configurar Bind9 en el servidor esclavo.
sudo nano /etc/bind/named.conf.local
// Zona directa
zone "sistema.test" {
    type slave;  
    file "/var/cache/bind/db.sistema.test"; 
    masters { 192.168.57.103; };  
};

// Zona inversa
zone "57.168.192.in-addr.arpa" {
    type slave;  
    file "/var/cache/bind/db.192";  
    masters { 192.168.57.103; };  
};

6. 
Para establecer el tiempo en caché de las respuestas negativas de las zonas (tanto directa como inversa) a dos horas en Bind9, debes modificar los archivos de configuración de las zonas donde se definen estos parámetros. El tiempo de caché para respuestas negativas se establece utilizando la directiva negative-cache TTL.
Asegúrate de que el archivo tenga el siguiente contenido y que negative-cache TTL esté configurado en 7200 segundos (2 horas):

7. 
En el servidor maestro (tierra.sistema.test), abre el archivo de configuración de Bind9 (/etc/bind/named.conf.options). 
Dentro del archivo,  la sección options debe contener las siguientes directivas para reenviar las consultas no autorizadas al DNS de OpenDNS (208.67.222.222).

8. 
Para configurar alias en Bind9, puedes usar registros CNAME en los archivos de zona.
Abre el archivo de la zona directa, /etc/bind/db.sistema.test, donde se configurarán los alias y agrega los registros CNAME para los alias ns1.sistema.test y ns2.sistema.test al final del archivo.
9. 
Para configurar el alias mail.sistema.test como un CNAME de marte.sistema.test.
Agrega el registro CNAME para el alias mail.sistema.test al final del archivo /etc/bind/db.sistema.test.
mail    IN      CNAME   marte.sistema.test
10. 
Para configurar marte.sistema.test como el servidor de correo para el dominio sistema.test, necesitas agregar un registro MX (Mail Exchange) en el archivo de zona del dominio en el servidor DNS maestro (tierra.sistema.test).
 Este registro indica a otros servidores de correo electrónico que los correos dirigidos a @sistema.test deben ser manejados por el servidor marte.sistema.test.
@       IN      MX      10 marte.sistema.test.
## Comprobaciones

1. 
dig @192.168.57.103 mercurio.sistema.test A
dig @192.168.57.103 venus.sistema.test A
dig @192.168.57.103 tierra.sistema.test A
dig @192.168.57.103 marte.sistema.test A

2. 
dig @192.168.57.103 -x 192.168.57.101
dig @192.168.57.103 -x 192.168.57.102
dig @192.168.57.103 -x 192.168.57.103
dig @192.168.57.103 -x 192.168.57.104

3. 
dig @192.168.57.103 ns1.sistema.test
dig @192.168.57.103 ns2.sistema.test

4. 
dig @192.168.57.103 sistema.test NS

5. 
dig @192.168.57.103 sistema.test MX

6. 
dig @192.168.57.103 sistema.test AXFR

7. 
dig @192.168.57.103 mercurio.sistema.test A
dig @192.168.57.102 mercurio.sistema.test A
dig @192.168.57.103 venus.sistema.test A
dig @192.168.57.102 venus.sistema.test A
dig @192.168.57.103 sistema.test MX
dig @192.168.57.102 sistema.test MX
dig @192.168.57.103 sistema.test NS
dig @192.168.57.102 sistema.test NS
dig @192.168.57.103 ns1.sistema.test CNAME
dig @192.168.57.102 ns1.sistema.test CNAME
dig @192.168.57.103 ns2.sistema.test CNAME
dig @192.168.57.102 ns2.sistema.test CNAME


