---
layout: post
title: "Configurar Octopress y Github para blog"
date: 2013-10-23 18:00
comments: true
categories: Octopress
keywords: octopress, github, blog
---

Si quieres montar un blog con Octopress (framework para montar blog) y Github (repo donde almacenaremos nuestro sitio) , hay que hacer lo siguiente:

**Descargando Octopress**

1. Descargamos la versión de octopress desde Github

{% codeblock lang:objc %}
git clone git://github.com/imathis/octopress.git octopress 
cd octopress    
{% endcodeblock %}

2. Instalamos el tema por default y las dependencias necesarias

{% codeblock lang:objc %}
gem install bundler
bundle install
rake install
{% endcodeblock %}

**Configuración de Github**

Lo primero es que tenemos que crear una cuenta en <a href="https://www.github.com/">Github</a>. Una vez creada la cuenta creamos un repositorio con el formtato: <i>"nombredeusuario.github.com"</i>,  en el caso de mi repositorio queda así: https://github.com/mcoam/mcoam.github.com

1. Configurar octopress para acceder a este respositorio

{% codeblock lang:objc %}
 rake setup_github_pages
{% endcodeblock %}

2. Ejecutado el comando anterior nos pedirá la url del repositorio. En el caso nuestro, es <i>git@github.com:mcoam/mcoam.github.com.git</i>


3. Para efectos de subir los post es necesario copiar la llave ssh de la máquina local (donde tenemos octopress) hacia nuestro sitio en Github.

<!-- more -->
{% codeblock lang:objc %}
  cat ~/.ssh/id_rsa.pub
{% endcodeblock %}

4. La copiamos en nuestro repo en Github, Seleccionamos nuestro repo -> Setting -> Deploy Keys -> Add deploy key

Con lo realizado hasta ahora, ya tenemos nuestro sitio Octopress sincronizado con Github. Ahora, queda ver lo referente a la configuración del DNS y apuntar nuestro repositorio hacia nuestro domino real. 


**Configuración de DNS**

En este caso es necesario configurar en registro CNAME para el host "www" (puede ser blog, wiki, etc) hacia el sitio de nuestro repositorio en GitHub.

{% codeblock lang:objc %}
www             IN      CNAME   mcoam.github.io.
{% endcodeblock %}

5. Y customizamos el CNAME en nuestro blog

{% codeblock lang:objc %}
echo 'mcoam.github.io' >> source/CNAME
{% endcodeblock %}


Con todo lo anterior realizado y configurado de forma correcta nuestro DNS, es cosa de ingresar en <i>www.your-damain.com</i> o <i>repositorio.github.com</i>. Donde la idea de es que ingresando el repo de Github se redireccione al sitio www.your-damain.com.
Con eso ya estaría todo para correr el sitio, ahora es solo cosa de crear post y sincronizarlos con nuestro repositorio de github. 

Para más información la página de <a href="http://octopress.org/docs/deploying/github/">Octopress</a> la lleva  
