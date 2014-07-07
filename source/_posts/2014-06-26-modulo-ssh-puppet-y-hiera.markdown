---
layout: post
title: "Módulo SSH Puppet y Hiera"
date: 2014-06-26 21:28
comments: true
categories: puppet, devops, hiera
---
El siguiente es un módulo de Puppet para la administración del servicio ssh en Linux (Rhel/Debian). La idea, de módulo es tomar los valores de las variables <code>port</code> <code>usedns</code> y <code>permit_root_login</code>.

<li>Clase init.pp</li>
Esta clase lo que hace es heredar las definición de variables que nosotros tenemos en nuestra clase <code>params.pp</code> e instancia el orden de la ejecución de las clases.
```ruby
[root@master manifests]$ cat init.pp 
class sshmcoa (
    $port               = $sshmcoa::params::port,
    $usedns             = $sshmcoa::params::usedns,
    $ensure             = $sshmcoa::params::ensure,
    $permit_root_login  = $sshmcoa::params::permit_root_login,
    $port     		= $sshmcoa::params::port,
    $service_name	= $sshmcoa::params::service_name,
    $config_file	= $sshmcoa::params::config_file,
    $package_name	= $sshmcoa::params::package_name,
    $config_template    = $sshmcoa::params::config_template,

) inherits sshmcoa::params {
	class {'::sshmcoa::install': } ->
	class {'::sshmcoa::config':}  ->
	class {'::sshmcoa::service':}  ->
	Class ['sshmcoa']
 #notify {"EL PUERTO ES: $port":}
}
```

<li>Clase params.pp</li>
En esta clase se definen todos los parámetros a configurar por la clase dependiendo del sistema operativo (familia RedHat o familia Debian)
```ruby
[root@master manifests]$ cat params.pp 
class sshmcoa::params{
    $port		= ''
    $usedns		= ''
    $ensure             = 'present'
    $permit_root_login  = ''
    $listen_address     = '0.0.0.0'
    $config_template    = 'sshmcoa/sshd_config.conf.erb'


case $::osfamily {
      	'Debian':{ 
		$config_file  = '/etc/ssh/sshd_config'
		$service_name = 'ssh'
		$package_name = 'ssh'
	}
      	default:{
		$config_file  = '/etc/ssh/sshd_config'
		$service_name = 'sshd'
		$package_name = 'openssh-server'
	}
     }
}
```
<!-- more -->
<li>Clase install.pp</li>
Esta es la clase encargada de la instalación del paquete para el servicio ssh, dependiendo del <code>osfamily</code> se instalará el pauete <code>ssh</code> o <code>openssh-server</code>
```ruby
[root@master manifests]$ cat install.pp 
class sshmcoa::install inherits sshmcoa {

case $::osfamily {
        'Debian':{
                package {"$package_name":
                        ensure => 'installed'
                }
        }
        default:{
                package {"$package_name":
                        ensure => 'installed'
        	}
      }
   }
}
```
<li>Clase service.pp</li>
Se configurar las opciones propias del servicio ssh
```ruby
[root@master manifests]$ cat service.pp 
class sshmcoa::service inherits sshmcoa {

	service {"$service_name":
			ensure     => running,
			hasstatus  => true,
			hasrestart => true,
			enable 	   => true,
			require    => Class[sshmcoa::config]
	}	
}
```
<li>Clase config.pp</li>
Configuramos los valores del archivo sshd_config. El nombre del archivo viene heredado de la de la clase principal bajo la variable <code>$config_file</code>, y el contenido <code>content</code> que tendrá el archivo es creado mediante un template <code>.erb</code>
```ruby
[root@master manifests]# cat config.pp 
class sshmcoa::config inherits sshmcoa {

	file { $config_file:
    		ensure  => $ensure,
    		owner   => 'root',
    		group   => 'root',
    		mode    => '600',
    		content => template($config_template),
		notify  => Service["$service_name"]
  	}
}
```
<li>Archivo sshd_config.conf.erb</li>
Este es el arhivo .erb que se crea con el contenido dinámico con el valor de las variables que fueron ingresadas desde <code>Hiera</code>. Para el caso de la clase, los valores que modificamos son <code>port</code> <code>usedns</code> y <code>permit_root_login</code> y para importalos al template tenemos que utilizar el formato <code><%= @NOMBRE_DE_VARIABLE %></code>
```ruby
[root@master manifests]$ cat ../templates/sshd_config.conf.erb 
# sshd.conf: Managed by Puppet.
#

#Port Puppet
Port <%= @port %>
Protocol 2
SyslogFacility AUTHPRIV
PasswordAuthentication yes
ChallengeResponseAuthentication no
GSSAPIAuthentication yes
GSSAPICleanupCredentials yes
##PermitRootLogin Puppet
PermitRootLogin <%= @permit_root_login %>
UsePAM yes
#UseDNS Puppet
UseDNS <%= @usedns %>
AcceptEnv LANG LC_CTYPE LC_NUMERIC LC_TIME LC_COLLATE LC_MONETARY LC_MESSAGES
AcceptEnv LC_PAPER LC_NAME LC_ADDRESS LC_TELEPHONE LC_MEASUREMENT
AcceptEnv LC_IDENTIFICATION LC_ALL LANGUAGE
AcceptEnv XMODIFIERS
X11Forwarding yes
Subsystem	sftp	/usr/libexec/openssh/sftp-server
```
<li>Archivo Hiera.yaml</li>
Dentro de nuestro <code>common.yaml</code> configuramos los valores que tendrán las variables del servicio ssh.

```ruby
[root@master hieradata]$ cat common.yaml
---
classes: 
     - sshmcoa

sshmcoa::port: '2222'
sshmcoa::usedns: 'no'
sshmcoa::permit_root_login: 'no'
```

Finalmente cuando conectemos el nodo veremos como se ejecuta la clase con los valores que le hemos pasado
```ruby
[root@node1 etc]# !puppet
puppet agent --test 
Info: Retrieving plugin
Info: Loading facts in /var/lib/puppet/lib/facter/root_home.rb
Info: Loading facts in /var/lib/puppet/lib/facter/puppet_vardir.rb
Info: Loading facts in /var/lib/puppet/lib/facter/pe_version.rb
Info: Loading facts in /var/lib/puppet/lib/facter/hosts_managed.rb
Info: Loading facts in /var/lib/puppet/lib/facter/facter_dot_d.rb
Info: Caching catalog for node1.example.com
Info: Applying configuration version '1401042981'
Notice: /Stage[main]/Sshmcoa::Install/Package[openssh-server]/ensure: created
Notice: /File[/etc/ssh/sshd_config]/content: 
--- /etc/ssh/sshd_config	2013-11-22 19:40:03.000000000 -0300
....
....
....
Info: FileBucket adding {md5}53ad75eb1f2269d23f6e4228353cbca3
Info: /File[/etc/ssh/sshd_config]: Filebucketed /etc/ssh/sshd_config to puppet with sum 53ad75eb1f2269d23f6e4228353cbca3
Notice: /File[/etc/ssh/sshd_config]/content: content changed '{md5}53ad75eb1f2269d23f6e4228353cbca3' to '{md5}22a041c1bbfd208de0259a2403bf3cbb'
Info: /File[/etc/ssh/sshd_config]: Scheduling refresh of Service[sshd]
Notice: /Stage[main]/Sshmcoa::Service/Service[sshd]/ensure: ensure changed 'stopped' to 'running'
Info: /Stage[main]/Sshmcoa::Service/Service[sshd]: Unscheduling refresh on Service[sshd]
Notice: EL PUERTO ES: 2222
Notice: /Stage[main]/Sshmcoa/Notify[EL PUERTO ES: 2222]/message: defined 'message' as 'EL PUERTO ES: 2222'
Notice: Finished catalog run in 16.27 seconds
```
Si vemos el contenido observaremos que se creó tal cual esta definido en nuestro template erb.
```ruby
[root@node1 etc]# cat /etc/ssh/sshd_config 
# sshd.conf: Managed by Puppet.
#

#Port Puppet
Port 2222
Protocol 2
SyslogFacility AUTHPRIV
PasswordAuthentication yes
ChallengeResponseAuthentication no
GSSAPIAuthentication yes
GSSAPICleanupCredentials yes
##PermitRootLogin Puppet
PermitRootLogin no
UsePAM yes
#UseDNS Puppet
UseDNS no
AcceptEnv LANG LC_CTYPE LC_NUMERIC LC_TIME LC_COLLATE LC_MONETARY LC_MESSAGES
AcceptEnv LC_PAPER LC_NAME LC_ADDRESS LC_TELEPHONE LC_MEASUREMENT
AcceptEnv LC_IDENTIFICATION LC_ALL LANGUAGE
AcceptEnv XMODIFIERS
X11Forwarding yes
Subsystem	sftp	/usr/libexec/openssh/sftp-server
```
