---
layout: post
title: "Montar, leer y escribir en USB con formato NTFS en OSX Capitan"
date: 2015-11-12 18:17
comments: true
categories: tips, osx, 
---
Con una terminal, ejecutar:

```objc
sudo vim /etc/fstab
```

Y dentro colocar 
```
LABEL=DRIVE_NAME none ntfs rw,auto,nobrowse
```
Donde:
El <code>DRIVE_NAME</code> corresponde el nombre del dispositivo que conectamos al equipo. En el caso de que el nombre contenga espacios, tenemos que anteponer el backslash <i>"\"</i> antes del mismo Ej: 
```
LABEL="NO\ NAME" none ntfs rw,auto,nobrowse
```

