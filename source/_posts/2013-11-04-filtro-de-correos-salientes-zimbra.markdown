---
layout: post
title: "Filtro de correos salientes Zimbra"
date: 2013-11-04 17:38
comments: true
categories: zimbra
---

Aveces es necesario en Zimbra "copiar" los correos salientes desde una casilla hacia otra cuenta (algo así como un forward). Esto en zimbra se puede realizar mediante la configuración del servicio postfix de la siguiente forma:

Editamos el archivo 

{% codeblock lang:objc %}
/opt/zimbra/postfix/conf/main.cf
{% endcodeblock %}

Y al principio del archivo agregamos la siguiente definición

{% codeblock lang:objc %}
sender_bcc_maps = hash:/opt/zimbra/postfix/conf/sender_bcc
{% endcodeblock %}

Creamos el archivo y colocamos los permisos respectivo

{% codeblock lang:objc %}
/opt/zimbra/postfix/conf/sender_bcc
chown zimbra.zimbra /opt/zimbra/postfix/conf/sender_bcc
{% endcodeblock %}

Dentro del archivo tenemos que definir en la primera columna la cuenta de origen y la cuenta de destino a la cual le llegará una copia de los correos salientes.

 <!-- more -->

{% codeblock lang:objc %}
originAccount@example.com destinationAccount@example.com
{% endcodeblock %}

Ahora, ejecutamos postmap sobre el archivo para generar el binario que leerá postfix y reiniciamos el servicio mta de zimbra

{% codeblock lang:objc %}
postmap /opt/zimbra/postfix/conf/sender_bcc
zmcontrol restart
{% endcodeblock %}




