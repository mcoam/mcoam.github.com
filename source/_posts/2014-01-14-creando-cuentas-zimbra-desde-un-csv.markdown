---
layout: post
title: "Creando cuentas Zimbra desde un CSV"
date: 2014-01-14 17:38
comments: true
categories: zimbra, script
---
El siguiente script nos permite crear las cuentas en Zimbra desde un CSV y automatizar el proceso. El CSV en donde tenemos las cuentas, tiene el siguiente formato:

``` objc
cponce@example.com;Claudia;Ponce;TMPpasswd
cquintana@example.com;Cristofer;Quintana;TMPpasswd
ctello@example.com;Cristian;Tello;TMPpasswd
eperalta@example.com;Edgar;Peralta;TMPpasswd
```

Y el script

``` objc 
#!/usr/bin/python

import csv
with open ('Europarts.csv', 'rb') as f:

        reader = csv.reader (f, delimiter=';' ) #delimiter tabulador \t
        for row in reader:

                mail     = row [0]
                name     = row [1]
                lastname = row [2]
                name2    = row [1] + ' ' + row [2]
		passwd	 = row [3]

		print "ca {0} '{1}' displayName '{2}' sn '{3}' cn '{4}'".format(mail,passwd,name2.title(),lastname.title(),name2.title())
```

Lo anterior, imprimerá por pantalla lo siguiente:

```
ca cponce@example.com 'TMPpasswd' displayName 'Claudia Ponce' sn 'Ponce' cn 'Claudia Ponce'
ca cquintana@example.com 'TMPpasswd' displayName 'Cristofer Quintana' sn 'Quintana' cn 'Cristofer Quintana'
ca ctello@example.com 'TMPpasswd' displayName 'Cristian Tello' sn 'Tello' cn 'Cristian Tello'
ca eperalta@example.com 'TMPpasswd' displayName 'Edgar Peralta' sn 'Peralta' cn 'Edgar Peralta'
```

La salida se puede mandar de forma directa dentro del script hacia un archivo y luego pasarlo por parametro al comando zmprov

```
zmprov < zmCreateAccounts
```

