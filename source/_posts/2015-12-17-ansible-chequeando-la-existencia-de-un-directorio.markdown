---
layout: post
title: "Ansible: chequeando la existencia de un directorio"
date: 2015-12-17 16:55
comments: true
categories: ansible, devops
---
Si necesitamos chequear la existencia de un directorio antes de realizar cualquier tipo de operación (copiear, instalar, etc) se puede utilizar el valor que nos trae el comando <code>stat</code>. Por ejemplo, el el siguiente ejemplo, se realiza lo siguiente:
<ol>
<li>Se instala apache</li>
<li>Se chequea que el directorio <code>conf.d</code> exista</li>
<li>Se copia el archivo <code>site.conf</code></li>
<li>Reiniciar apache</code></li>
</ol>
```objc
- name: Install apache
  yum: name=httpd state=latest

- name: "Check apache path"
  stat: path=/etc/httpd/conf.d
  register: apache_path

- name: "Copy configuration file"
  copy: src=site.conf dest=/etc/httpd/conf.d
  when: apache_path.stat.exists == True

- name: "Restart apache"
  service: name=httpd state=restarted
  when: apache_path.stat.exists == True
```
