---
layout: post
title: "Jugando Con Docker"
date: 2014-10-02 16:01
comments: true
categories: Docker, devops
published: false
---
####Imagenes, Contenedores y Capas con Docker
Al momento de empezar a trabajar con Docker aparecen varios items que si bien no son nuevos en la virtualización tradicional (mediante hypervisores) de la forma que los trabaja Docker tienen un valor agregado no por si mismos sino por la suma de todos a la vez.

####Capas en Docker  
Las capas en Docker son los <i>bloques</i> en donde se van almacenando todos los cambios que puede tener un contenedor (Ej. instalación de paquetes, update del SO, etc) por cada cambio que se realiza en el contenedor se crea una capa nueva de escritura que va solapando las capas anteriores que quedan de solo lectura, es por ello que una capas al momento en que trabajamos con ella.

####Docker image  Images never change once they are created.  Images can be linked.  A image that is stacked on another image can be referred to in a child parent analogy with the lower image being the parent.  The bottommost image is referred to as the base image and has no parent image.

A docker container – When you start a container you reference an image.  Docker pulls the required image and pulls the images parent image.  It continues to pull the parent images until it reaches the base image.  In addition, it creates a read-write layer for changes to be stored in when the container is running.  All of these layers as well as other container configuration information is stored in the container.

Lo primero es crear el archivo Dockerfile con el siguiente contenido
```ruby
# Version: 0.0.2
From centos:latest
MAINTAINER Miguel Coa "mcoa@example.com"
#RUN apt-get update
RUN yum install httpd -y
RUN echo 'Hello world' > /var/www/html/index.html
CMD service httpd start

EXPOSE 80
```
Lo anterior va a crear un contenedor en centos <i>latest</i>, le instalará apache, creará el index.html y dejara el servicio corriendo. Una vez que tengamos creado el Dockerfile, lanzamos el comando para crear el contenedor.
```ruby
[root@vagrant-centos7 static_web]# docker build -t="mcoa01/static_web:v2" . 
Uploading context  2.56 kB
Uploading context 
Step 0 : From centos:latest
 ---> 87e5b6b3ccc1
Step 1 : MAINTANER Miguel Coa "mcoa@example.com"
# Skipping unknown instruction MAINTANER
Step 2 : RUN yum install httpd -y
 ---> Using cache
 ---> 9ed85d674aab
Step 3 : RUN echo 'Hi, I am in you container' > /var/www/html/index.html
 ---> Using cache
 ---> a7cac0dce7d7
Step 4 : CMD service httpd start
 ---> Using cache
 ---> 72e4df843d86
Step 5 : CMD service sshd start
 ---> Running in 475c04052b5e
 ---> 88515c279ac0
Removing intermediate container 475c04052b5e
Step 6 : EXPOSE 80
 ---> Running in 5b3b8cc184ed
 ---> e080d67b10f4
Removing intermediate container 5b3b8cc184ed
Successfully built e080d67b10f4
```
Y vemos la imagen creada
```ruby
[root@vagrant-centos7 static_web]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
mcoa01/static_web   v2                  e080d67b10f4        12 minutes ago      321.8 MB
```
####Verificando nuestra imagen


####Creando el contenedor a partir de la imagen creada
Creando el contenedor
```ruby
[root@vagrant-centos7 static_web]# docker run -t -i -d -p 10.10.0.50:8080:80 --name static_web  mcoa01/static_web:v3  /bin/bash 
0501489ccabc4ace605069fa0de4ade00bf9720913b137248650c66d17ce25b5
```
Listamos los contenedores
```ruby
[root@vagrant-centos7 static_web]# docker ps -a
CONTAINER ID        IMAGE                  COMMAND             CREATED             STATUS              PORTS                     NAMES
0501489ccabc        mcoa01/static_web:v3   /bin/bash           2 seconds ago       Up 2 seconds        10.10.0.50:8080->80/tcp   static_web   
```
