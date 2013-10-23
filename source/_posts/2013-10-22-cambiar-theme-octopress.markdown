---
layout: post
title: "Cambiar Theme Octopress"
date: 2013-10-22 23:17
comments: true
categories: octopress
---
Si deseamos cambiar el theme por defecto que instala Octopress, hacemos lo siguiente

{% codeblock lang:objc %}
cd octopress
git clone git://github.com/panks/fabric.git .themes/fabric
rake install['fabric']
rake generate
{% endcodeblock %}

<!-- more --> 

Vemos como queda

{% codeblock lang:objc %}
rake preview
{% endcodeblock %}

Lo anterior borrará el antiguo theme y dejará el nuevo, si deseamos volver atrás tenemos que reinstalar el theme anterior.
