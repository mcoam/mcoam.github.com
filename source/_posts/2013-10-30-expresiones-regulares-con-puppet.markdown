---
layout: post
title: "Expresiones regulares con Puppet"
date: 2013-10-30 15:06
comments: true
categories:puppet, devops 
---

Puppet permite la declarición de nodos mediante expresiones regulares, esto permite definir dentro de una sola declaraciones "n" nodos, lo que nos ayudará a manejar de mejor forma nuestra administración de nodos. Por ejemplo: 

{% codeblock lang:objc %}
node /.*\.example\.com/ inherits basenode {
    class ntp
}
{% endcodeblock %}


Lo anterior dice que, todo nodo del tipo *.example.com <i>(www.example.com, ftp.example.com, mail.example.com)</i> aplicará la <i>clase ntp</i> y heredará la <i>clase basenode</i>. Para más infomación puedes ver la doc de puppet <a href="http://docs.puppetlabs.com/puppet/2.7/reference/lang_node_definitions.html">Puppet Docs</a> 

