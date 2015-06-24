---
layout: post
title: "Script para buscar y borrar un correo en zimbra"
date: 2015-01-15 12:49
comments: true
categories: zimbra, script, shell
---
El siguiente script permite buscar y borrar un correo mediante consola desde los mailbox de los usuarios.
```ruby
#!/bin/bash

zimbraAccounts='/var/tmp/cuentas'
zimbraCMD='/opt/zimbra/bin/zmmailbox'
 for a in `cat $zimbraAccounts`
    do
        #user=`$a`
        emailID=( $($zimbraCMD -z -m $a search "de haber superado debido a la alta tasa de correos spam" |grep conv |awk {'print $2'} |sed 's/-//g') )
        echo $user
        #echo ${emailID[1]}
            for i in "${emailID[@]}"
                do
                    $zimbraCMD -z -m $a dm $i
		    echo "cuenta: $a" >> /var/tmp/casillasConEmail
                done
    done
```
En la línea nº8 tenemos que cambiar el texto a buscar, y que corresponde a lo que esta dentro de comillas.
Para el caso, tenemos que tener las cuentas de correos en el archivo `/var/tmp/cuentas` que las podemos obtener con el comando:
```
zmprov -l gaa |egrep -vi 'spam|virus|galsyn|archiv'| sort > /var/tmp/cuentas 
```
Demás esta decir que lo realizado en el comando anterior se puede hacer dentro del mismo script sin tener que hacer esto de forma separada.  Una vez ejecutado el script, podremos ver la información de las cuentas en donde fue borrado el correo:
```
[zimbra@mail1 tmp]$ tail -f /var/tmp/casillasConEmail 
cuenta: ahidalgo@example.com
cuenta: alinay@example.com
cuenta: chernandez@example.com
cuenta: currutia@example.com
cuenta: cvaldes@example.com
cuenta: evalencia@example.com
```

