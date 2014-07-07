---
layout: post
title: "Configurando módulo sudo con Puppet y Hiera"
date: 2014-06-18 12:02
comments: true
categories: devops, hiera, puppet, sudo
published: false
---
El siguiente es un módulo de Puppet que trabaj con Hiera y nos permite configurar el servicio <code>sudo</code>. La configuración del módulo fue basada en el módulo [saz/puppet-sudo](https://github.com/saz/puppet-sudo). El funcionamiento del módulo es el siguiente:
<li> Crea el archivo sudoers para cada usuario </li>
<li> Define los permisos sudo para un grupo </li>

####Configurando el módulo

<li>Creamos el directorio de configuración del módulo</li>
```ruby
mkdir /etc/puppet/modules/sudomcoa/{manifests,files,templates}
```
<li>Dentro del directorio <code>manifests</code> creamos los siguientes archivos</li>
```ruby
[root@master manifests]$ ls
add_groups.pp  add_users.pp  config.pp  groups.pp  init.pp  package.pp  users.pp
```
