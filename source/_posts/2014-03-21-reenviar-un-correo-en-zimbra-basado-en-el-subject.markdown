---
layout: post
title: "Reenviar un correo en zimbra basado en el Subject"
date: 2014-03-21 14:10
comments: true
categories: zimbra, postfix
---
Para reenviar un que contenga un <i>Subject</i> en particular hacia otra casilla en Zimbra podemos realizar los siguiente

En el archivo de configuración de postfix <i>/opt/zimbra/postfix/conf/main.cf.default</i> colocamos la referencia al hash para header_checks
``` objc
header_checks = regexp:/opt/zimbra/postfix/conf/header_checks
```
Dependiendo de la versión de Zimbra, la anterior configuración funcionará sin problemas, de lo contrario la configuración referente al header_checks tenemos que hacerla en el archivo <i>/opt/zimbra/postfix/conf/zmmta.cf</i> donde adicionaremos la siguiente línea (antes del parámetro RESTART mta)
``` objc
POSTCONF header_checks  regexp:/opt/zimbra/postfix/conf/header_checks
``` 
En el archivo  <i>/opt/zimbra/postfix/conf/header_checks</i> adicionamos la expresión regular que tendrá el subject

``` objc
/^Subject: MIGUELCOA/ REDIRECT mcoa@example.com
```
Recargarmos el servicio MTA
``` objc
zmmtactl reload
```
Y desde ahora, cada vez que desde el servidor zimbra salga un correo que en su asunto contenga "MIGUELCOA" será enviada una copia hacia la casilla mcoa\@example.com

