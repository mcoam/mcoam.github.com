---
layout: post
title: "Desactivar nodo PuppetDB"
date: 2014-06-03 18:44
comments: true
categories: puppet, devops, puppetdb
author: Miguel Coa Morales.
---
En el caso de querer <i>desactivar</i> un nodo de PuppetDB lo podemos realizar con el comando:
```ruby
$ puppet node deactivate node1.example.com
```
Con lo anterior, el nodo "desaparecerá" de nuestro Dashboard . 

Referencia [acá](http://docs.puppetlabs.com/puppetdb/1/maintain_and_tune.html#deactivate-decommissioned-nodes)
