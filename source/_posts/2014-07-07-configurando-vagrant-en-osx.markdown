---
layout: post
title: "Configurando Vagrant en OSX"
date: 2014-07-07 15:30
comments: true
categories: devops, vagrant, virtualbox
---
En la búsqueda de correr máquinas virtuales de forma mas simple me he decidido instalar <code>Vagrant</code>, que es un entorno para la gestión de máquinas virtuales bajo mbiente VirtualBox. La mágia de Vagrant viene de la mano de VirtualBox ya que este tiene soporte para correr máquinas virtuales del tipo <i>headless</i> (algo así como máquinas que corren en un servidor pero no sabes donde).
####Instalación de Vagrant en OSX 10.9.3 
Lo primero es bajar la versión correspondiente de [Vagrant](http://www.vagrantup.com/downloads) y lo mismo para [VirtualBox](https://www.virtualbox.org/wiki/Downloads). Una vez descargado ambos paquetes (dmg) se instalan como cualquier otra programa para mac.
Es necesario tener instalado una librerias Ruby para el trabajo con Vagrant, proceso que se realiza con el comando:
```ruby
gem install vagrant
```
La gestión de las máquinas virtuales en Vagrant se realizan mediante el comando <code>vagrant</code> <i>(init, up, destroy, ssh, resume, suspend, halt y muchas más)</i>, todas las opciones que tiene este comando figuran en la [documentación](http://docs.vagrantup.com/v2/cli/)

####Instalando nuestro primer Box
Lo primero que tenemos que hacer es bajar nuestro Box, que será la <i>imagen base</i> desde donde crearemos nuestras máquinas virtuales:
```ruby
vagrant box add CentOS  https://github.com/2creatives/vagrant-centos/releases/download/v6.4.2/centos64-x86_64-20140116.box
```
Bajamos y agregamos un nuevo Box de sistema que se llama "Centos" que se encuentra hosteado remotamente, puedes ver la infinidad de Box disponibles [acá](http://www.vagrantbox.es/)
Podemos ver los Box instalados con el siguiente comando
```ruby
vagrant box list
CentOS (virtualbox, 0)
```
####Corriendo nuesta máquina virtual
Teniendo nuestro Box, podemos instanciar nuesta primera máquina virtual para lo cual tenemos que ejecutar el comando <code>vagrant init</code>
```ruby
Miguel:Puppet$ vagrant init
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
```
Lo anterior creará un archivo de configuración base llamado Vagrantfile, donde se definieran una seríe de instrucción con las caracteristicas internas (ip, hostname, puertos, servicios) asociadas al SO, como las externas (memoria, cpu, etc) asociadas a VirtualBox, para el caso la dejaremos por defecto esta configurción.
Luego de tener creado nuestro <code>Vagrantfile</code> creado, podemos iniciar nuestra nueva instancia de máquina virtual:
```ruby
Miguel:Puppet$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'Centos'...
==> default: Matching MAC address for NAT networking...
==> default: Setting the name of the VM: Puppet_default_1404758552103_56627
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 => 2222 (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: Warning: Remote connection disconnect. Retrying...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
==> default: Mounting shared folders...
    default: /vagrant => /Users/Miguel/VagrantVM/Puppet
```
Ahora, la VM se encuentra corriendo en nuestro equipo. Podemos conectarnos con el comando <code>vagrant ssh</code>
```ruby
Miguel:Puppet$ vagrant ssh
[vagrant@vagrant-centos64 ~]$ 
```
Por defecto el nombre con que queda la VM y se puede ver mediante la GUI de VirtualBox no es para nada descriptivo, pero puede ser modificado editando el archivo <code>Vagranfile</code> adicionando la variable <code>name</code> de la sección <code>config.vm.provider</code> (líneas nº 9,10,11)  . 


{% img center /images/vagrantDefaultName.png %}


```ruby
Miguel:Puppet$ cat Vagrantfile
# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "Centos"
  config.vm.provider "virtualbox" do |puppetmaster|
     puppetmaster.name = "puppetmaster"
  end
end
```
Podemos ver el nuevo nombre por línea de comando (Nombre que será también el de la GUI de VirtualBox)
```ruby
Miguel:Puppet$ VBoxManage list runningvms
"puppetmaster" {c980cd9e-3329-4aa8-8d58-b84e389fff68}
Miguel:Puppet$ 
```
No esta demás el link de la [documentación](https://docs.vagrantup.com)

