---
layout: post
title: "Error screen: There is no screen to be resumed matching "
date: 2014-11-28 13:14
comments: true
categories: linux, comandos
---
Si al momento de querer recuperar una sesión <code>screen</code> donde tenemos un proceso corriendo y utilizamos el comando <code>scren -r</code>, como resultado de la operación obtenemos el siguiente mensaje:
```ruby
[root@mail-old ~]$ screen -r '1866.zimbra-mta'
There is a screen on:
1866.zimbra-mta (Attached)
There is no screen to be resumed matching 1866.zimbra-mta
```
La solución es pasar el comando con la opción <i>'-D'</i> que nos permite desatachar la sessión remota y atacharla en nuestra tty.
```
[root@mail-old ~]$ screen -r -D '1866.zimbra-mta'
```
