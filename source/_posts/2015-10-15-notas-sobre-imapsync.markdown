---
layout: post
title: "Notas sobre imapsync"
date: 2015-10-15 23:00
comments: true
categories: imapsync
---
En el caso de migrar la data de correos vía `imapsync` es importante tener en consideración (si se va a tener corriendo varios días el comando) utilizar la opción `--delete2` para que todo correo copiado al <i>(hosts2)</i> y que no tenga copia en el origen <i>(host1)</i> sea borrado, de lo contrario el usuario cuando se conecte al nuevo servidor tendrá correos que ya había eliminado y el buzón pesará mas de lo normal. 
De todas formas, lo anterior se puede corregir configrando el <code>imapsyn</code> con el valor <code>--minage</code> que sincroniza el buzón desde <i>X</i> días para atrás. 
```ruby
imapsync --buffersize 8192000 --nosyncacls --subscribe --syncinternaldates --authmech1 PLAIN /
--host1 192.168.10.10 --user1 foo@origen.dev --password1 'foo' --host2 192.168.10.11 /
--user2 foo@destino.dev --password2 'foo' --authmech2 PLAIN --nofoldersizes --skipsize --fast /
--exclude '(?i)\b(Junk|Spam|Trash|IMIP|Drafts)\b' --exclude 'INBOX_(.*)' --exclude 'Trash_(.*)'/
 --exclude 'Drafts_(.*)' --exclude 'Spam_(.*)' --exclude 'Sent Items_(.*)' --regextrans2 /
's#^Sent Items#Sent#' --delete2duplicates --delete2 --minage 5
``` 

Por ejemplo, hoy estamos a 10 y tiramos el imapsync en el destino empezará a sincronizar (borrando la data que no este en el host1) desde el día 5, con ello evitamos borrar los correos nuevos en el host2.
