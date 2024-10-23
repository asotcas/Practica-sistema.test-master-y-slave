
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
### Configuración de Bind9

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