---
layout: post
title: "Tabulación con editor VIM"
date: 2013-11-27 08:32
comments: true
categories: linux, tips
---
En ocasiones al copiar y pegar texto hacia una archivo que está siendo accedido por medio del editor VIM, este queda con una mala tabulación (excesivamente corrida a la derecha) al momento de pegarla. La solución para eso es que antes de pegar el texto tenemos realizar lo siguiente en la misma ventana del editor

{% codeblock lang:objc %}
type: set paste
{% endcodeblock %}

Lo otro, es agregar la línea <i>set paste</i> dentro de <i>/etc/vim/vimrc</i> . 

