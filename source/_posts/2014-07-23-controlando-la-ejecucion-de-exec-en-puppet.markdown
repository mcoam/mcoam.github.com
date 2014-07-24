---
layout: post
title: "Controlando la ejecución de Exec en Puppet"
date: 2014-07-23 21:25
comments: true
categories: Pupet, Devops
published: false
---
En Puppet podemos ejecutar comando a nivel del sistema operativo por medio de <code>Exec</code>, en ocasiones sucede que tenemos declarada la sintaxis a ejecutar pero sucede que esta se aplica cada vez que corre el agente Puppet, Ejemplo: 
```ruby
exec{"install-rpm":
	ensure  => present, 
	command => 'rpm -Uvh /root/package.rpm',
}
```
Lo anterior instalará un rpm llamado <code>package.rpm</code> cada vez que corra el agente, lo que pisará las configuraciones ya realizadas en la instalación y post-instalación del rpm. Para evitar que esto suceda <code>Exec</code> maneja dos atributos <code>creates</code> y <code>onlyif</code> ambos como condicionantes para que se puede ejecutar el comando. 
<li>creates</li>
Funciona del modo que para ejecutar el atributo <code>command</code> no tiene que existir "algo" que se define con el atributo <code>creates</code>, ejemplo:

```ruby
exec{"install-rpm":
ensure  => present,
creates => '/opt/rpm-directory/',
command => 'rpm -Uvh /root/package.rpm',
}
```
Lo anterior dice que; para que se ejecute el <code>exec</code> no tiene que estar creado el directorio <code>/opt/rpm-directory/</code>, si este ya esta creado, es por que ya tenemos instalado el rpm y no es necesario volver a instalarlo.

<li>onlyif</li>
Funciona del modo que para ejecutar el atributo <code>command</code> el resultado del atributo <code>onlyif</code> debe retornar value 0 "nada" como respuesta a la condición, ejemplo:

```ruby
exec{"install-rpm":
ensure  => present,
onlyif => "ls /opt/ |grep rpm-directory"
command => 'rpm -Uvh /root/package.rpm',
}
```
Lo anterior dice que; para que se ejecute el <code>exec</code> la salida del <code>onlyif</code> debe retornar 0. Si el rpm ya lo tenemos instalado, este va a crear el directorio por lo que para evitar que se siga instalando solo vemos si este ya fue creado. Si el rpm fue instalado, el <code>onlyif</code> retornará un <i>1</i> por lo que no se volverá a ejecutar nuevamente.

Link [documentación](http://docs.puppetlabs.com/references/latest/type.html#exec-attribute)
