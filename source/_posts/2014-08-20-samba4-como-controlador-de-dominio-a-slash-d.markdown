---
layout: post
title: "SAMBA4.localo controlador de dominio A/D"
date: 2014-08-20 10:57
comments: true
categories: samba, samba4, active, controlador de dominio, samba3, active rirectory
published: true
---
Para Centos 6 tenemos que complilar las fuentes para instalar Samba4:

Instalar paqueteria necesaria
```ruby
[root@samba ~]$ yum -y install gcc make wget python-devel gnutls-devel openssl-devel libacl-devel krb5-server krb5-libs krb5-workstation bind bind-libs bind-utils
```
Descargamos y lo compilamos
```ruby
[root@samba ~]$ git clone git://git.samba.org/samba.git samba4
[root@samba ~]$ cd samba4
[root@samba ~]$ ./configure --enable-selftest
[root@samba ~]$ make && make install #Esto demora bastante
```
Terminado lo anterior y si no arroja ningún error, tendremos instalado Samba4. Ahora necesitamos configurar el controlador de dominio, lo primiero que tenemos que hacer es el <i>Provisioning</i> para el nuevo dominio.

Unas cosas antes a considerar:
Configuramos el hostname en la tabla hosts <code>/etc/hosts</code>
```ruby
[root@samba ~]$ cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

10.10.0.200 samba.example.local samba
```
Y el servidor de nombres en el <code>/etc/resolv.conf</code> 
```ruby
[root@samba ~]$ cat /etc/resolv.conf 
search example.local
domain example.local
nameserver 127.0.0.1
```
Esto es necesario para los servicios Samba y Kerberos, además, tenemos que configurar el parámetro <code>dns forwarder</code> dentro del archivo de configuración del servicio (esto si no lo hacemos dentro del proceso del provisionamiento).


NOTA: El servidor tiene que tener correctamente sincronizada la hora (utilizar NTP)

####Provisionando el nuevo dominio
```ruby
/usr/local/samba/bin/samba-tool domain provision
```
Y completamos con los datos solicitados
```ruby
Realm [EXAMPLE.LOCAL]: (press Enter)
Domain [EXAMPLE]: (press Enter) #CON MAYUSCULAS
Server Role (dc, member, standalone) [dc]: (press Enter)
DNS backend (SAMBA_INTERNAL, BIND9_FLATFILE, BIND9_DLZ, NONE) [SAMBA_INTERNAL]: (press Enter)
DNS forwarder IP address (write 'none' to disable forwarding) [10.10.0.1]: 10.10.0.1
Administrator password: <set_password>
Retype password:
```
Con todo el proceso anterior realizado, al final de todo veremos algo como lo siguiente
```ruby
Fixing provision GUIDs
A Kerberos configuration suitable for Samba 4 has been generated at /usr/local/samba/private/krb5.conf
Once the above files are installed, your Samba4 server will be ready to use
Server Role:           active directory domain controller
Hostname:              samba
NetBIOS Domain:        EXAMPLE
DNS Domain:            example.local
DOMAIN SID:            S-1-5-21-3438009546-7273XXXX-XXXXXXXXX
```
Y luego reiniciamos el servidor

El path de instalación de Samaba4 queda bajo <code>/usr/local/samba/</code> los mismo sucede con los binarios <code>/usr/local/samba/bin/</code> y <code>/usr/local/samba/sbin/</code> para lo cual vamos a configurar la variable <code>$PATH</code> en nuestro bash.
```ruby
[root@samba ~]# vim ~/.bashrc
#Dentro colocamos
export PATH="/usr/local/samba/sbin:/usr/local/samba/bin:$PATH"
```
Para ver que quedó todo en orden ejecutamos
```ruby
[root@samba ~]# samba -V
Version 4.1.11
[root@samba ~]# smbclient -V
Version 4.1.11
```
Chequeamos la configuración local al Samba y los procesos corriendo
```ruby
[root@samba ~]#  ps -ef | grep samba
root     23010     1  1 12:32 ?        00:00:00 samba
root     23011 23010  0 12:32 ?        00:00:00 samba
root     23012 23010  0 12:32 ?        00:00:00 samba
root     23013 23010  0 12:32 ?        00:00:00 samba
root     23014 23010  0 12:32 ?        00:00:00 samba
root     23015 23011  1 12:32 ?        00:00:00 /usr/local/samba/sbin/smbd -D --option=server role check:inhibit=yes --foreground
root     23016 23010  2 12:32 ?        00:00:00 samba
root     23017 23010  0 12:32 ?        00:00:00 samba
```
```ruby
[root@samba ~]# /usr/local/samba/bin/smbclient -L localhost -U%
Domain=[EXAMPLE] OS=[Unix] Server=[Samba 4.1.11]

        Sharename       Type      Comment
        ---------       ----      -------
        netlogon        Disk
        sysvol          Disk
        IPC$            IPC       IPC Service (Samba 4.1.11)
Domain=[EXAMPLE] OS=[Unix] Server=[Samba 4.1.11]

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
```
El proceso de configuración del Samba va a crear un archivo kerberos de la configuración relaizada, tenemos que enlazarlo al directorio <code>/etc</code>
```ruby
[root@samba ~]$ mv /etc/krb5.conf /etc/krb5.conf.ORG
[root@samba ~]$ ln -s /usr/local/samba/private/krb5.conf  /etc/
```
Comprobamos el funcionamiento en kerberos
```ruby
[root@samba ~]# host -t SRV _ldap._tcp.example.local.
_ldap._tcp.example.local has SRV record 0 100 389 samba.example.local.
```
Comprobamos la autentificación contra kerberos
```ruby
[root@samba ~]# kinit administrator@EXAMPLE.LOCAL 
Password for administrator@EXAMPLE.LOCAL: 
Warning: Your password will expire in 41 days on Wed Oct  1 16:28:11 2014
```

Si por el contrario nos arroja algún error, tenemos que ver las configuraciones realizadas o reconfigurar el servicio samba

####Script de arranque 
Para el control del servicio creamos el siguiente script
```ruby
[root@samba ~]$ cat /etc/init.d/samba4
#! /bin/bash
#
# samba4 Bring up/down samba4 service
#
# chkconfig: - 90 10
# description: Activates/Deactivates all samba4 interfaces configured to#
# chkconfig: - 90 10
# description: Activates/Deactivates all samba4 interfaces configured to
# start at boot time.
#
### BEGIN INIT INFO
# Provides:
# Should-Start:
# Short-Description: Bring up/down samba4
# Description: Bring up/down samba4
### END INIT INFO
# Source function library.
. /etc/init.d/functions
 
if [ -f /etc/sysconfig/samba4 ]; then
. /etc/sysconfig/samba4
fi
 
CWD=$(pwd)
prog="samba4"
 
start() {
# Attach irda device
echo -n $"Starting $prog: "
/usr/local/samba/sbin/samba
sleep 2
if ps ax | grep -v "grep" | grep -q /samba/sbin/samba ; then success $"samba4 startup"; else failure $"samba4 startup"; fi
echo
}
stop() {
# Stop service.
echo -n $"Shutting down $prog: "
killall samba
sleep 2
if ps ax | grep -v "grep" | grep -q /samba/sbin/samba ; then failure $"samba4 shutdown"; else success $"samba4 shutdown"; fi
echo
}
status() {
/usr/local/samba/sbin/samba --show-build
}
 
# See how we were called.
case "$1" in
start)
start
;;
stop)
stop
;;
status)
status irattach
;;
restart|reload)
stop
start
;;
*)
echo $"Usage: $0 {start|stop|restart|status}"
exit 1
esac
 
exit 0
```
Configuramos los permisos
```ruby
[root@samba ~]$ chmod +x /etc/init.d/samba4 
```
Finalmente dejamos los servicios para correr por defecto
```ruby
[root@samba ~]$ chkconfig --levels 235 samba4 on
[root@samba ~]$ chkconfig --levels 235 ntp on
[root@samba ~]$ chkconfig --levels 235 named on
```
Ahora ya tenemos configurado el Samba, desde ahora podemos empezar a conectar a los usuarios.

####Uniendo una máquina al dominio


####Getión remota del controlado de dominio (Remote Server Administration Tool)
Para la gestión remota del controlador de dominio descargamos <i>Remote Server Administration Tool</i> desde [acá](http://www.microsoft.com/es-es/download/details.aspx?id=7887):
 Instalación
<ol>
<li>Lo instalamos como cualquier exe</li>
<li>Panel de control </li>
<li>Programas y caracteristicas</li>
<li>Activar o desactivar caracteristicas</li>
<li>Seleccionamos las caracteristicas asociadas al A/D</li>
<li>Reinciamos la máquina Windows</li>
<li>Inicio -> Ejecutar -> "Usuarios y Equipos de Active Directory" y veremos el arbol de nuestro Samba4</li>
</ol>

Para mas detalles del RSTA [acá](https://wiki.samba.org/index.php/Installing_RSAT_on_Windows_for_AD_Management)




