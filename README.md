## Documentación

> Esta basado para Denodo Express 8.0
{.is-warning}

Basado en los tutoriales

- How to create your own Docker images for Denodo Platform Containers: https://community.denodo.com/kb/en/view/document/How%20to%20create%20your%20own%20Docker%20images%20for%20Denodo%20Platform%20Containers?category=Operation
- Unattended Installation of the Denodo Platform: https://community.denodo.com/docs/html/browse/latest/en/platform/installation/unattended_installation_of_the_denodo_platform/unattended_installation_of_the_denodo_platform
- Data Persistence in Containers: https://community.denodo.com/kb/en/view/document/Data%20Persistence%20in%20Containers

## Requerimientos

1. Se realizo en distribución Ubuntu
1. Se uso la imagen docker debian
1. Se uso la instalación Denodo Express para Linux
1. Es necesario tener instalado Docker y Docker-Compose

## Descargas

> Es necesario hacer cuenta en Community Denodo, lo solicitará para poder acceder a la descarga y para poder descargar la licencia
{.is-info}


Se accede a la Url https://community.denodo.com/express/download/ y se descarga el archivo de **Licencia** junto con la versión **Denodo Express Linux 64 Bits**

Los archivos que tendrémos son:

1. denodo-express-lic-8_0-202105.lic
1. denodo-express-install-8_0.zip

## Preparación

### Repositorio
Se creo un repositorio con el **Dockerfile** y **docker-compose.yml** en:

https://github.com/cbesmit/pet-docker_compose_Denodo_8_0

### Estructura de archivos

Carpeta que contiene descomprimido el archivo de **denodo-express-install-8_0.zip** (previamente descargado, no viene en el repositorio)

- /denodo-install-8.0-linux64


Archivo de la licencia Denodo Express 8.0 (previamente descargado, no viene en el repositorio)

- /denodo-express-lic-8_0-202105.lic

Necesarios  para ejecutar el contenedor con docker-compose
- /Dockerfile
- /docker-compose.yml

Archivo para generado para la instalación desatendida

- /install.xml

Conectores de Mysql 5.0 que se usan como librerías externas

- mysql-connector-java-5.1.49.jar
- mysql-connector-java-5.1.49-bin.jar

Estoy intentando hacer persistencia usando Postgresql con la opción de **regenerateMetadata** ejecutando desde el bash del contenedor:

    ./bin/regenerateMetadata.sh -f bin/metadata_database_configuration.properties -y
    
Pero aun no funciona correctamente, por lo que **NO SIRVE LA PERSISTENCIA EN ESTE CONTENEDOR**

Archivo de configuración de metadata, apunta hacia la conexión Postgres del docker-compose

- metadata_database_configuration.properties

Carpeta para tener persistencia 

- /db-data

## Ejecución
Usando la terminal

### Usando docker-compose (recomendado)

**Iniciar el contenedor**

    docker-compose up -d

**Detener el contenedor**

    docker-compose down

### Sin usar docker-compose (no recomendado)
**Crear la imagen a partir del Dockerfile**

    docker build -t denodo-image:latest .

**Crear el contenedor**

> Se debe remplazar la ruta **/home/besmit/denodo/** por tu ruta donde esta el archivo de la licencia y donde quieras mantener la persistencia de Denodo
{.is-warning}


    docker run -d --name denodo -h localhost -p 9999:9999 -p 9996:9996 -p 9995:9995 -p 9090:9090 -v /home/besmit/denodo/denodo-express-lic-8_0-202105.lic:/opt/denodo/conf/denodo.lic -v /home/besmit/denodo/opt:/opt/denodo denodo-image

**Iniciar el contenedor**

    docker start denodo

**Detener el contenedor**

    docker stop denodo

## Iniciar servicios Denodo

Primero es necesario acceder al bash de docker

    docker exec -it denodo8.0 bash

> Todo sobre la carpeta **bin/**
{.is-info}

### Iniciar el servidor Virtual DataPort Server

    ./vqlserver.sh startup
    
### Servicio de Data Catalog
**Iniciar** el servicio

    ./datacatalog_startup.sh
    
Acceder usando la url: http://localhost:9090/denodo-data-catalog/AutoLogin

**Detener** el servicio

    ./datacatalog_shutdown.sh

### Servicio de Web Design Studio
**Iniciar** el servicio

    ./designstudio_startup.sh
    
Acceder usando la url: http://localhost:9090/denodo-design-studio/#/

**Detener** el servicio

    ./designstudio_shutdown.sh

## Credenciales
Para poder acceder a los servicios los accesos por defecto son:

    Username: admin
    Password: admin


