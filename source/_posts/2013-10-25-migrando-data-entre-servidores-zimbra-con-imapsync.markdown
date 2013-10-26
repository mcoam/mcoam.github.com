---
layout: post
title: "Migrando data entre servidores zimbra con ImapSync"
date: 2013-10-25 13:39
comments: true
categories: zimbra, correo, imapsync
---

En ocaciones es necesario migrar un servidor de correos Zimbra hacia otro servidor Zimbra o simplemente deseamos migrar la data entre usuario de un mismo servidor. Bueno, para eso existe el mágico del comando imapsync. Básicamente, lo que hace este comando es conectarse entre los servidores de correos mediante el protocolo imap (también puede ser imap SSl) y migrar la data de una casilla hacia otra. 
La sintaxis del comando a utilizar dependedará de las configuraciones de los servidores Ej: si acepta conexeón texto plano, solo ssl, etc. Por defecto el comando siguiente es para una instalación estandar y con conección al puerto 143.

El comando mágico es:

{% codeblock lang:objc %}
imapsync --buffersize 18192000 --nosyncacls --subscribe \ 
--syncinternaldates --noauthmd5 --authmech2 PLAIN \ 
--host1 192.168.0.5 --user1 source@domain.com \
--password1 SourcePassword --host2 192.168.0.5 \ 
--user2 destination@domain.com --authmech2 PLAIN \
--password2 DestinationPassword 
{% endcodeblock %}

Donde: 

{% codeblock %}
--host1 : Es el servidor de origen
--user1 : Es el usuario de origen
--password1 : Es la password de la cuenta origen
--host2 : Es el servidor de destino
--user2 : Es el usuario de destino
--password2 : Es la password de la cuenta de destino
{% endcodeblock %}

Para más detalle del eso del comando ir al wiki de Zimbra <a href="http://wiki.zimbra.com/wiki/Guide_to_imapsync">Wiki de Zimbra</a>
