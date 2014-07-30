---
layout: post
title: "Red pública para máquinas virtuales en Vagrant "
date: 2014-07-30 15:05
comments: true
categories: vagrant
---
Por defecto las máquinas virtuales creadas en Vagrant quedan configuradas con una ip "privada" y el acceso a la VM es por medio de NAT. Si deseamos configurar una ip "publica" en nuestra máquina virtual tenemos que editar nuestro <code>Vagranfile</code> y adicionar el parámetro <code>vm.network</code> dentro de nuestro apartado <code>config.vm.define</code>.
```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.define :puppet do |puppet_config|
     puppet_config.vm.box = "Centos"
     puppet_config.vm.hostname  = "puppetmaster.example.com"
     puppet_config.vm.network :public_network,  bridge: 'en1: Wi-Fi (AirPort)', ip: "192.168.1.36"
    
   config.vm.provider "virtualbox" do |vboxpuppet|
        vboxpuppet.name = "puppetmaster"
    end ##cierra config.vm.provider
  end ##cierra config.vm.define

end ##cierra file
```
Y si nos conectamos

```ruby
Miguel:Puppet$ vagrant ssh
[vagrant@puppetmaster ~]$ ifconfig 
eth0      Link encap:Ethernet  HWaddr 08:00:27:97:35:D8  
          inet addr:192.168.1.36  Bcast:192.168.1.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fe97:35d8/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:36 errors:0 dropped:0 overruns:0 frame:0
          TX packets:15 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:5256 (5.1 KiB)  TX bytes:998 (998.0 b)
```

Ref: https://docs.vagrantup.com/v2/networking/public_network.html
