---
layout: post
title: "Configurar PuppetBoard"
date: 2014-06-01 22:39
comments: true
categories: devops, puppet, hiera, puppetboard
author: Miguel Coa Morales
---
Para facilitar la administración de Puppet (opensource) tenemos una nueva herramienta que se llama PuppetBoard su objetivo es dar accesos a la data que nosotros tenemos almacenada en nuestro PuppetDB de forma gráfica vía web, además de permitirnos ver el funcionamiento y reportes de los nodos al momentos de conectarse al nuestro Master.
PuppetBoard difiere de Pupet Dansboard en:
<li>Esta escrito en Python no en Ruby </li>
<li>No almacena ningún tipo de data, toda información es requerida del PuppetDB</li>
<li>No es posible funcionar sin PuppetDB</li>

La instalación del servicio no es compleja, puedes ver con lujo y detalle todos los pasos [acá](https://github.com/nedap/puppetboard). Para nuestro caso el proceso de instalación es el siguiente:

####Instalación
{% img /images/puppetboard.png %}

Nota: Nuestra instalación será en base a Apache + mod-wsi


Creamos nuestro directorio web de PuppetBoard
```ruby
$ mkdir -p /var/www/puppetboard
```
Clonamos el fuente
```
git clone https://github.com/nedap/puppetboard.git /var/www/puppetboard
```
Instalamos paquetería necesaria
```ruby
yum install mod_wsgi python-pip
```
Copiamos el archivo de ejemplo <i>/usr/lib/python2.6/site-packages/puppetboard/default_settings.py</i> a la ruta <i>/var/www/puppetboard</i>, la nombramos <i>settings.py</i>
```ruby
$ cp /usr/lib/python2.6/site-packages/puppetboard/default_settings.py /var/www/puppetboard
$ mv /var/www/puppetboard /var/www/settings.py
```
Y colocamos el siguiente contenido
```ruby
PUPPETDB_HOST = '127.0.0.1'
PUPPETDB_PORT = 8080
#PUPPETDB_SSL_VERIFY = True
PUPPETDB_KEY = None
PUPPETDB_CERT = None
PUPPETDB_TIMEOUT = 20
DEV_LISTEN_HOST = '127.0.0.1'
DEV_LISTEN_PORT = 5000
UNRESPONSIVE_HOURS = 2
ENABLE_QUERY = True
LOGLEVEL = 'info'
```
En el mismo directorio del PuppetBoard, creamos el archivo <i>wsgi.py</i> con el siguiente contenido
```ruby
from __future__ import absolute_import
import os

# Needed if a settings.py file exists
os.environ['PUPPETBOARD_SETTINGS'] = '/var/www/puppetboard/settings.py'
from puppetboard.app import app as application
```
Ahora, creamos el archivo <i>/etc/httpd/conf.d/wdgi.conf</i> para cargar el módulo en Apache con el siguiente contenido
```ruby
LoadModule wsgi_module modules/mod_wsgi.so
```
Finalmente, creamos el archivo de configuración para el virtualhosts que nos permita el acceso al PuppetBoard vía web
```ruby
$ touch /etc/httpd/conf.d/puppetboard.conf
```
Y colocamos la siguiente configuración
```ruby
<VirtualHost *:80>
    ServerName puppet.example.com
    WSGIDaemonProcess puppetboard user=apache group=apache threads=5
    WSGIScriptAlias / /var/www/puppetboard/wsgi.py
    ErrorLog /var/log/httpd/puppetboard.error.log
    CustomLog /var/log/httpd/puppetboard.access.log combined

    Alias /static /usr/lib/python2.6/site-packages/puppetboard/static/ 

    <Directory /usr/lib/python2.6/site-packages/puppetboard>
        AuthType Basic
        AuthName "Authentication Required"
        AuthUserFile "/var/www/puppetboard/.htpasswd"
        Require valid-user
        Order allow,deny
        Allow from all
       WSGIProcessGroup puppetboard
        WSGIApplicationGroup %{GLOBAL}
      #  Require all granted
#	  Order deny,allow
#        Allow from all
    </Directory>
</VirtualHost>
```
Concluido todo el proceso anterior reiniciamos el servicio apache y nos será posible acceder vía web al PuppetBoard. De forma adicional el acceso al servicio web es mediante usuario y password <i>/var/www/puppetboard/.htpasswd</i> la información de la configuración la pueden ver [acá](http://wiki.apache.org/httpd/PasswordBasicAuth)

{% img /images/puppetboardnodes.png %}
