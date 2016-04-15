---
layout: post
title: "Configurando fail2ban para autentificaciones SMTP fallidas en Mailcleaner"
date: 2016-04-15 13:30
comments: true
categories: mailcleaner, fail2ban
---
Para prevenir que estando nuestro servidor `SMTP` en internet tengamos bloqueos por ataques `DoS` al puerto 25 podemos implementar el servicio `fail2ban` para bloquear todo tipo de conexión que falle en la autentificación de forma reiterativa realizando lo siguiente:

* Instalar `fail2ban` en el servidor Mailcleaner
```objc
aptget update; aptget install fail2ban
```
Nos dirigimos al directorio `/etc/fail2ban`, donde creamos/editamos los siguientes archivos:

<ol type="a">
<li>Archivo <code>filter.d/exim2.conf</code>: Contiene la reglas para el match del jail</li>
```objc
# Fail2Ban filter for exim
#
# This includes the rejection messages of exim. For spam and filter
# related bans use the exim-spam.conf
#


[INCLUDES]

# Read common prefixes. If any customizations available -- read them from
# exim-common.local
# before = exim-common.conf

[Definition]

failregex = \[<HOST>\]: 535 Incorrect authentication data

ignoreregex =
```
<li>Archivo <code>action.d/iptables-repeater.conf</code>: Configura toda la acción a realizar con las ip que fallan con la auth</li>
```objc
# Fail2ban configuration file
#
# Author: Phil Hagen <phil@identityvector.com>
#

[Definition]

# Option:  actionstart
# Notes.:  command executed once at the start of Fail2Ban.
# Values:  CMD
#
actionstart = iptables -N fail2ban-REPEAT-<name>
              iptables -A fail2ban-REPEAT-<name> -j RETURN
              iptables -I INPUT -j fail2ban-REPEAT-<name>
              # set up from the static file
              cat /etc/fail2ban/ip.blocklist.<name> |grep -v ^\s*#|awk '{print $1}' | while read IP; do iptables -I fail2ban-REPEAT-<name> 1 -s $IP -j DROP; done

# Option:  actionstop
# Notes.:  command executed once at the end of Fail2Ban
# Values:  CMD
#
actionstop = iptables -D INPUT -j fail2ban-REPEAT-<name>
             iptables -F fail2ban-REPEAT-<name>
             iptables -X fail2ban-REPEAT-<name>

# Option:  actioncheck
# Notes.:  command executed once before each actionban command
# Values:  CMD
#
actioncheck = iptables -n -L INPUT | grep -q fail2ban-REPEAT-<name>

# Option:  actionban
# Notes.:  command executed when banning an IP. Take care that the
#          command is executed with Fail2Ban user rights.
# Tags:    <ip>  IP address
#          <failures>  number of failures
#          <time>  unix timestamp of the ban time
# Values:  CMD
#
actionban = iptables -I fail2ban-REPEAT-<name> 1 -s <ip> -j DROP
            # also put into the static file to re-populate after a restart
            ! grep -Fq <ip> /etc/fail2ban/ip.blocklist.<name> && echo "<ip> # fail2ban/$( date '+%%Y-%%m-%%d %%T' ): auto-add for repeat offender" >> /etc/fail2ban/ip.blocklist.<name>

# Option:  actionunban
# Notes.:  command executed when unbanning an IP. Take care that the
#          command is executed with Fail2Ban user rights.
# Tags:    <ip>  IP address
#          <failures>  number of failures
#          <time>  unix timestamp of the ban time
# Values:  CMD
#
actionunban = /bin/true

[Init]

# Defaut name of the chain
#
name = REPEAT
```
<li>Archivo <code>ip.blocklist.exim2</code>: Contiene las direcciones ip que se van bloqueando por intentos fallidos y que se va autocompletando con los bloqueos propios del `fail2ban` ej: <li>

```objc
122.154.29.30 # fail2ban/2016-04-15 00:21:45: auto-add for repeat offender
95.183.52.100 # fail2ban/2016-04-15 00:35:51: auto-add for repeat offender
110.84.129.110 # fail2ban/2016-04-15 02:11:53: auto-add for repeat offender
86.98.40.247 # fail2ban/2016-04-15 09:07:46: auto-add for repeat offender
91.106.93.21 # fail2ban/2016-04-15 13:36:51: auto-add for repeat offender
```
</ol>

La configuración del `jail` la realizamos dentro del archivo `/etc/fail2ban/jail.conf` , quedando así:
```objc
[exim2-repeater]
      enabled  = true
      filter   = exim2
      action   = iptables-repeater[name=exim2]
      logpath  = /var/mailcleaner/log/exim_stage1/mainlog
      maxretry = 10
      findtime = 31536000
      bantime  = 31536000
```
Lo anterior leerá del archivo `/var/mailcleaner/log/exim_stage1/mainlog` todos los errores del tipo `535 Incorrect authentication data` si la authenticación falla al intento 10 y además la ip será almacenada en el archivo `iptables-repeater.conf` .
