---
layout: post
title: "Eliminar y purgar nodo de PuppetDB (storeconfig)"
date: 2014-08-26 11:43
comments: true
categories: puppet, devops, puppetdb
---
Error:
```ruby
Error: Could not retrieve catalog from remote server: Error 400 on SERVER: A duplicate resource was found while collecting exported resources, with the type and title Nagios_host[next-haproxy-1.domain.org] on node nagios.domain.org
Warning: Not using cache on failed catalog
Error: Could not retrieve catalog; skipping run
```
Lo anterior era por que el node estaba registrado dos veces en PuppetDB una con el fqdn completo <i>next-haproxy-1.domain.org</i> y otra sin el dominio <i>next-haproxy-1</i> lo que generaba error al exportar los recursos. La solución fue eliminar uno y luego purgar el contenido de la base de datos:

<li>Remover el nodo de PuppetDB</li>
```ruby
[root@nagios servers]$ puppet node deactivate next-haproxy-1
```
Remover el nodo de la DB
```ruby
[root@puppet ~]# psql -h 127.0.0.1 puppetdb puppetdb
Password for user puppetdb: <colocar password>
#EJECUTAR
puppetdb=> delete from catalogs where certname in (select name from certnames where deactivated is not null);
DELETE 1
```


