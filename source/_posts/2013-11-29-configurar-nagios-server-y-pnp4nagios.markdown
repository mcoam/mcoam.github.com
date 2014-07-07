---
layout: post
title: "Configurar Nagios server y Pnp4nagios"
date: 2013-11-29 10:46
comments: true
categories: nagios
---
Si tienes un servidor Nagios como plataforma de monitoreo de servidores, es posible que en alguna ocasión puedas necesitar generar reportes de la estadística (mensual, díaria, semanal, etc) para lo cual la versión open de Nagios Server no lo tiene integrado de forma nativa pero podemos solucionar con <a href="http://www.pnp4nagios.org/">pnp4nagios</a> y para realizarlo tenemos que hacer lo siguiente (asumiremos que ya tenemos nuestro Nagios Server funcionando)

Tenemos que tener instalado en el servidor rrdtool, perl-rrdtool y una serie de paquetería 

``` objc    
yum install libdbi rrdtool perl-rrdtool ruby xorg-x11-fonts-Type1
```

Instalación php4nagios

``` objc    
wget http://downloads.sourceforge.net/project/pnp4nagios/PNP-0.6/pnp4nagios-0.6.21.tar.gz?r=http%3A%2F%2Fsourceforge.net%2Fprojects%2Fpnp4nagios%2F&ts=1372937581&use_mirror=jaist
tar -zxvf pnp4nagios-0.6.21.tar.gz
cd pnp4nagios-0.6.21
./configure -prefix=/usr/share/pnp4nagios -with-rrdtool=/usr/bin/rrdtool -with-nagios-user=nagios -with-nagios-group=nagios
```
Lo anterior generará todo el proceso de compilación lo que finalmente arrojará algo similar a lo siguiente:
{% img /assets/images/pnp4nagios.png %}

Y luego lo compilamos e instalamos
<!-- more -->
``` objc    
make all && make install && make install-webconf && make install-config && make install-init
```
Nos aseguramos que no tengamos ningún tipo de error en el proceso de compilación

``` objc
Copiamos los archivos de configuración
cd /usr/share/pnp4nagios/etc
cp misccommands.cfg-sample misccommands.cfg
cp nagios.cfg-sample nagios.cfg
cp rra.cfg-sample rra.cfg
cp /usr/share/pnp4nagios/etc/pages/
cp web_traffic.cfg-sample web_traffic.cfg
cd ../check_commands
cp check_all_local_disks.cfg-sample
check_all_local_disks.cfg
cp check_nrpe.cfg-sample check_nrpe.cfg
cp check_nwstat.cfg-sample check_nwstat.cf 
```
Configuración de servicio npcd

``` objc
chkconfig --add npcd
chkconfig npcd on
service npcd start
```
Test pnp4nagios
``` objc
http://server-ip/pnp4nagios
``` 
Deberemos ver lo siguiente

{% img /assets/images/pnp4nagios-environment.png %}

Como dice al final de la página, podemos eliminar el página de test
``` objc
rm -rf /usr/share/pnp4nagios/share/install.php
```
Configuración de apache de pnp4nagios, donde tenemos instanciar nuestro archivo de password de acceso nagios modificando el valor

``` objc
AuthUserFile /etc/nagios/passwd
``` 
Reiniciamos apache
``` objc
service httpd restart
```
Ahora tenemos que configurar nagios y que pueda interactuar y permitir la visualización de la gráfica. Para esto utilizaremos el modo <i>Bulk Mode with npcdmod</i> de pnp4nagios, para lo cual editamos el archivo de configuración nagios <i>/etc/nagios/nagios.cfg</i> , adicionando/moficiando los siguientes valores

``` objc
broker_module=/usr/share/pnp4nagios/lib/npcdmod.o config_file=/usr/share/pnp4nagios/etc/npcd.cfg
process_performance_data=1
```
Finalmente, reiniciamos los servicios y empezará a ser reflejada la data de forma gráfica
``` objc
service npcd restart && service nagios restart
```

{% img /assets/images/webpnp4.png %}

Referencias

http://www.productionmonkeys.net/guides/nagios/addons/pnp4nagios
http://www.academia.edu/4142933/Install_pnp4nagios_on_Cent_OS
http://eldespistado.com/instalacion-nagios-pnp4nagios-check_mk-nagvis-debian-7-wheezy/

