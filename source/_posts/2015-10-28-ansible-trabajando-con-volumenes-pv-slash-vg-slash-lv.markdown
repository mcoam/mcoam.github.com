---
layout: post
title: "Ansible: Trabajando con volumenes lógicos PV/VG/LV en Linux"
date: 2015-10-28 23:52
comments: true
categories: ansible, devops
---
Ansible cuenta con un módulos para la gestión de discos en Linux los cuales no permiten administrar nuestros volúmenes. 
En el siguiente ejemplo, se crea un volumen sobre un disco `/dev/sdf1` y que será configurado como volumen lógico y finalmente montado para los backup que realiza un servidor de correos Zimbra.

```objc
---
- hosts: ne_mailbox
  vars:
        disk_name: "/dev/sdf1"
        filesystem: "ext4"
        vg_name: "zimbra_data"
        lv_size: "100%FREE" #ocupar todo el espacio libre del VG zimbra_data
        lv_name: "backup"
        path: "/opt/zimbra/backup"
        user: "zimbra"
  tasks:

        - name: "Crear el PV sobre el disco nuevo"
          command: pvcreate { { disk_name } }

        - name: "Crear nuevo VG"
          lvg: vg=\{\{ vg_name \}\} pvs=\{\{ disk_name \}\}

        - name: "Crear nuevo volumen LVM"
          lvol: vg=\{\{ vg_name \}\} lv=\{\{ lv_name \}\} size=\{\{ lv_size \}\}
          tags: crea_lv

        - name: "Formateo de unidad LVM"
          filesystem: fstype=\{\{ filesystem \}\} dev=/dev/\{\{ vg_name \}\}/\{\{ lv_name \}\}
          tags: formatea_lv

        - name: "Montar disco"
          mount: name=\{\{ path \}\} src=/dev/\{\{ vg_name \}\}/\{\{ lv_name \}\} fstype=\{\{ filesystem \}\} state=mounted
          tags: monta_lv

        - name: "Permisos directorio backup"
          file: path=\{\{ path \}\} state=directory mode=0755 owner=\{\{ user \}\} group=\{\{ user \}\}
          tags: permisos_directorio
```
