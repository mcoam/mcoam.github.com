---
layout: post
title: "Configurar Octopress y Github para blog"
date: 2013-10-23 18:00
comments: true
categories: Octopress
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

{% codeblock lang:objc %}
  cat ~/.ssh/id_rsa.pub
{% endcodeblock %}

4. Y le copiamos en nuestro repo en Github, Seleccionamos nuestro repo -> Setting -> Deploy Keys -> Add deploy key


