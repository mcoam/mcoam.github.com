---
layout: post
title: "Error Kerberos y Bind con Samba4"
date: 2014-08-20 18:17
comments: true
categories: samba4, bind  
published: true
---
Al momento de validar la configuración de kerberos del usuario admin arrojaba lo siguiente:

```ruby
[root@samba ~]$ kinit administrator@EXAMPLE.LOCAL
kinit: Cannot resolve servers for KDC in realm "EXAMPLE.LOCAL" while getting initial credentials
```
Al chequear las configuraciones DNS de de Samba arrojaba 
```ruby
[root@samba ~]$ samba_dnsupdate --verbose --all-names
IPs: ['10.0.2.15', '10.10.0.200']
Traceback (most recent call last):
  File "/usr/local/samba/sbin/samba_dnsupdate", line 511, in <module>
    get_credentials(lp)
  File "/usr/local/samba/sbin/samba_dnsupdate", line 124, in get_credentials
    raise e
RuntimeError: kinit for SAMBA$@EXAMPLE.LOCAL failed (Cannot contact any KDC for requested realm)
```

####Solución ELIMINAR Bind
```ruby
[root@samba ~]$ yum remove bind -y
```
####Comprobamos
```ruby
[root@samba ~]# kinit administrator@EXAMPLE.LOCAL
Password for administrator@EXAMPLE.LOCAL: 
Warning: Your password will expire in 41 days on Wed Oct  1 17:54:00 2014
```
Y chequeamos la configuracion de DNS de Samba
```ruby
[root@samba ~]# samba_dnsupdate --verbose --all-names
IPs: ['10.0.2.15', '10.10.0.200']
Calling nsupdate for A example.local 10.0.2.15
Outgoing update query:
;; ->>HEADER<<- opcode: UPDATE, status: NOERROR, id:      0
;; flags:; ZONE: 0, PREREQ: 0, UPDATE: 0, ADDITIONAL: 0
;; UPDATE SECTION:
example.local.		900	IN	A	10.0.2.15

; TSIG error with server: tsig verify failure
Failed nsupdate: 2
Calling nsupdate for A samba.example.local 10.0.2.15
Outgoing update query:
;; ->>HEADER<<- opcode: UPDATE, status: NOERROR, id:      0
;; flags:; ZONE: 0, PREREQ: 0, UPDATE: 0, ADDITIONAL: 0
;; UPDATE SECTION:
samba.example.local.	900	IN	A	10.0.2.15
```



