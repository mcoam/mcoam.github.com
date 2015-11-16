---
layout: post
title: "Ansible: Configurando volúmen de datos HSM en Zimbra"
date: 2015-11-12 18:34
comments: true
categories: zimbra, ansible, devops
---
El siguiente <code>Playbook</code> permite configurar un nuevo volúmen para datos (store) en un servidor Zimbra NE. 

```objc
---
- hosts: ne_mailbox
  remote_user: root
  vars:
        zmvolume: "/opt/zimbra/bin/zmvolume"
        zmhsm: "/opt/zimbra/bin/zmhsm"
        zmprov: "/opt/zimbra/bin/zmprov"
        hsm_name: "hsm02"
        hsm_id: "4" # check id zmvolume -l |grep -B1 hsm02
        path: "/opt/zimbra/store_hsm_02/"
        policy_option: " 'message,document,task,appointment,contact:before:-1month is:anywhere' "
  tasks:
        - name: "Configura volumen HSM como volumen secundario"
          command: "{ { zmvolume } } -a -name {{ hsm_name }} -c true -t secondaryMessage -p { { path } }"

        - name: "Activamos nuevo volumen para manejar data"
          command: "{ { zmvolume } } -sc -id { { hsm_id } } " 

        - name: "Configuramos politica HSM: Todo por sobre 1 mes se mueve al nuevo volumen"
          command: "{ { zmprov } } ms { { ansible_fqdn } } zimbraHsmPolicy { { policy_option } }"

        - name: "Arrancamos el proceso para mover correos al nuevo HSM"
          command: "{ { zmhsm } } -t"
```
<b>Donde:<br></b>
`path: "/opt/zimbra/store_hsm_02/"`: Corresponde al punto de montaje del nuevo recurso HSM.<br>
`hsm_id: 4"`: Corresponde al ID que le asigna Zimbra al nuevo volúmen (disco).<br>
`hsm_name: hsm02`: Corresponde al nombre que le colocamos al recurso HSM.<br>
