---
layout: post
title: "Migrando usuarios/password Postfix to Zimbra"
date: 2014-01-15 09:58
comments: true
categories: zimbra, python, script
---
La idea del siguiente script es; primero crear la cuenta con una password temporal y luego configurar la password original del usuario, ambos valores son sacados del <i>/etc/shadow</i>

``` python
#!/usr/bin/python
##Miguel Coa M.

import csv
with open ('shadow', 'rb') as f:

        reader = csv.reader (f, delimiter=':' ) 
        for row in reader:

                user     = row [0]
                passwd   = "'crypt{" + row [1] +"}'"

                print "ca {}@domain.com tmpPSW".format(user)
                print "ma {}@domain.com userPassword  {}  ".format(user,passwd)
```

Y como resultado nos arrojará por pantalla 

``` python
ca varaya@domain.com tmpPSW
ma varaya@domain.com userPassword  'crypt{$1$6RT62jZZ$sFlv.MIII4PuKISBaap030}'
ca cgalaz@domain.com tmpPSW
ma cgalaz@domain.com userPassword  'crypt{$1$bAEOlC0N$4t.3zCGDb.RFc0vmxd2UB0}'
ca ctagle@domain.com tmpPSW
ma ctagle@domain.com userPassword  'crypt{$1$L7vlV7Me$orB2TCnqn.JeoVQpKsr6P0}'
``` 
Valor que los podemos almacenar en un archivo y los ejecutamos pasandolo como parámetro al comando <i>zmprov</i>. Si ya tenemos creadas las cuentas podemos comentar el primer print, para que solo las modifique.

