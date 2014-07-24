---
layout: post
title: "Path Global para todos los Execs en Puppet"
date: 2014-07-24 14:37
comments: true
categories: puppet, devops
---
En Puppet si tenemos varios <code>Exec</code> para ejecutar comandos del sistema operativo podemos configurar el atributo <code>path</code> de forma global dentro del <code>site.pp</code> y con ello evitamos repetir código. 
```ruby
exec { "restart-dns":
command => "service named restart"
creates => "/var/named",
refreshonly => true
}
```
site.pp</li>
```ruby
Exec { path => [ "/bin/", "/sbin/" , "/usr/bin/", "/usr/sbin/" ] }
```



