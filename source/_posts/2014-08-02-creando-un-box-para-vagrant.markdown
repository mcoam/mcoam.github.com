---
layout: post
title: "Creando un Box para Vagrant"
date: 2014-08-02 23:50
comments: true
categories: vagrant, devops, puppet
---
En mi caso la máquina virtual no fue creada desde cero, ya que bajé un Box de Vagrant y lo customicé por lo que me salté los pasos previos de la creación del box https://docs.vagrantup.com/v2/boxes/base.html. Lo realizado para la creación del box fue lo siguiente:

<li> Optimización de tamaño de la VM </li>
```ruby
$ sudo dd if=/dev/zero of=/EMPTY bs=1M
$ sudo rm -f /EMPTY
$ sudo shutdown -h now
```
<li> Creación del Box </li>
```ruby
Miguel:PuppetMaster$ cd ~/VirtualBox\ VMs/ # directorio donde quedan alojadas las vms
Miguel:VirtualBox VMs$ vagrant package --base puppetserver ##puppetserver es el nombre de la vm en virtualbox
```
<li> Testing del Box</li>
Agreagamos nuestro Box a nuestro Vagrant 
```ruby
Miguel:PuppetMaster$ vagrant box add Centos6PuppetServer ~/VirtualBox\ VMs/package.box
==> box: Adding box 'Centos6PuppetServer' (v0) for provider: 
    box: Downloading: file:///Users/Miguel/VirtualBox%20VMs/package.box
==> box: Successfully added box 'Centos6PuppetServer' (v0) for 'virtualbox'!
```
Verificamos
```ruby
Miguel:PuppetMaster$ vagrant box list
Centos6PuppetServer (virtualbox, 0) # nuestro box
```
Nos dirigimos a nuestro directorio de trabajo de Vagrant y crearemos un nuevo proyecto <i>testing</i>
```ruby
Miguel:VagrantVM$ mkdir testing
Miguel:VagrantVM$ cd testing/
```
Iniciamos una nueva VM
```ruby
Miguel:testing$ vagrant init Centos6PuppetServer ##la iniciamos
Miguel:testing$ vagrant up #la arrancamos
Miguel:testing$ vagrant ssh #nos conectamos
```

