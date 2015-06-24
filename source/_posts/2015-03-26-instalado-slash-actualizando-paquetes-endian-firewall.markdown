---
layout: post
title: "Instalado/Actualizando paquetes Endian Firewall"
date: 2015-03-26 18:35
comments: true
categories: endian firewall, efw, smart
---
Para instalar paquetes en Endian Firewall podemos utilizar el comando `smart` que es un administrador de paquetes que viene incorporado en la distro. Lo primero que hay que realizar es instalar el repositorio:

<li>Para Endian 2.4.x</li>
```ruby
smart channel --add CentOS_4.9 type=rpm-md name="CentOS_4.9" \baseurl="http://vault.centos.org/4.9/os/i386/"
```
<li>En el caso de Endian 2.5.x</li>
```ruby
smart channel --add CentOS_5.9 type=rpm-md name="CentOS_5.9" \baseurl="http://vault.centos.org/5.9/os/i386/"
```
Instalando paquetes
```
smart update
smart install wget nmap vim-enhanced -y
```
En el caso de ver el error:
```
warning: rpmts_HdrFromFdno: Header V3 DSA signature: NOKEY, key ID 443e1821
error: vim-common-6.3.046-2.el4.1.i386.rpm: public key not available                                                                                 
Saving cache...
```
Ejecutar
```
smart config --set  rpm-check-signatures=false
```
