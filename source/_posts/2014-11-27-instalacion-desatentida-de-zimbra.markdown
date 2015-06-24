---
layout: post
title: "Instalación desatentida de Zimbra"
date: 2014-11-27 17:20
comments: true
categories: 
published: false
---
Muchas veces al momento de instalar nuestro servidor de correos hemos pensado como poder automatizar este proceso, ya que en muchos casos las instalaciones son siempre las mismas,  por lo cual optimizar este trabajo mediante Kickstart sería de gran utilidad.  Por suerte en el caso de Zimbra es posible realizar esto mediante un archivo de configuración <i>Kickstart</i> donde le pasamos todos los datos necesarios que permitan realizar esta tarea (dominio, password admin, hostname, etc). 

En el presente caso, vamos a realizar una instalación multi-servidor en un ambiente virtual donde instalaremos 4 servidores con distintos roles:
<li>1 Zimbra Ldap Master</li>
<li>1 Zimbra Ldap Replica</li>
<li>1 Zimbra Mailbox</li>
<li>1 Zimbra MTA/Proxy</li>

La gracia de esta instalación  es que nos permitirá ir creciendo de forma horizontal e ir adiconando servidores en la medida que necesitamos con solo ejecutar un par de comando y de forma totalmente desatendida.

####Explicando el escenario
Como nuestro ambiente de instalación es virtual, todas los servidores serán clonados a partir de servidores que llamamos <i>templates</i> en donde previamente se realizó la instalación de los paquetes para cada tipo de rol. Por ejemplo para el servidor template Zimbra Ldap Master, realizamos una instalación con el comando:

```ruby
./install.sh -s 
```
Donde al momento de la consulta de que paquetes instalar le damos <i>Y</i> solo a la opción <code>zimbra-ldap</code>, por defecto esto también instalará el paquete <code>zimbra-core</code>.

```ruby
zimbra-core-8.5.1_GA_3056.RHEL6_64-20141103151539.x86_64.rpm
zimbra-ldap-8.5.1_GA_3056.RHEL6_64-20141103151539.x86_64.rpm
```
Y así con los restantes servidores, lo anterior tiene por objetivo que cuando clonemos el servidor template, este ya tenga los paquetes instalados y solo con ejecutar un comando se haga todo de forma desatendida.
####Manos a la obra
<li>Nota nº1: Antes que todo, tenemos que tener claro que para realizar una instalación de este tipo, tenemos que realizar la instalación en el siguiente orden: Zimbra Ldap Master -> Zimbra Mailbox -> Zimbra MTA -> Zimbra Ldap Réplica </li>
<li>Nota nº2: Tenemos que tener la precaución de corregir el hostname y la tabla hosts de cada uno de los nuevos servidores a instalar </li>

#####Instalando Zimbra Ldap Master
Para realizar la intalación del servidor Zimbra Ldap Master ejecutamos el comando:
```ruby
/opt/zimbra/libexec/zmsetup.pl  -c /root/zimbra-conf/zcsOpenLdapMasterConfig
```
El contenido del archivo <i>zcsOpenLdapMasterConfig</i> es el siguiente:
```ruby
CREATEADMINPASS="zimbra.passwd"
LDAPROOTPASS="c1bumJP_"
LDAPADMINPASS="c1bumJP_"
LDAPREPPASS="c1bumJP_"
AVDOMAIN="example-open.local"
AVUSER="admin@example-open.local"
CREATEADMIN="admin@example-open.local"
CREATEDOMAIN="example-open.local"
DOCREATEADMIN="no"
DOCREATEDOMAIN="yes"
EXPANDMENU="no"
HOSTNAME="zco-ldapmaster-1.example.com"
HTTPPORT="80"
HTTPPROXY="FALSE"
HTTPPROXYPORT="8080"
HTTPSPORT="443"
HTTPSPROXYPORT="8443"
IMAPPORT="143"
IMAPPROXYPORT="7143"
IMAPSSLPORT="993"
IMAPSSLPROXYPORT="7993"
INSTALL_WEBAPPS="zimlet"
JAVAHOME="/opt/zimbra/java"
LDAPBESSEARCHSET="set"
LDAPHOST="zco-ldapmaster-1.example.com"
LDAPPORT="389"
LDAPREPLICATIONTYPE="master"
LDAPSERVERID="2"
MAILBOXDMEMORY="1971"
MAILPROXY="FALSE"
MODE="https"
MYSQLMEMORYPERCENT="30"
POPPORT="110"
POPPROXYPORT="7110"
POPSSLPORT="995"
POPSSLPROXYPORT="7995"
PROXYMODE="https"
REMOVE="no"
RUNVMHA="no"
SMTPDEST="admin@example-open.local"
SMTPHOST=""
SMTPNOTIFY="yes"
SMTPSOURCE="admin@example-open.local"
SNMPNOTIFY="yes"
SNMPTRAPHOST="zco-ldapmaster-1.example.com"
SPELLURL=""
STARTSERVERS="yes"
SYSTEMMEMORY="7.7"
UPGRADE="yes"
USESPELL="no"
ZIMBRA_REQ_SECURITY="yes"
ldap_bes_searcher_password="ixXXkIh01"
ldap_dit_base_dn_config="cn=zimbra"
ldap_nginx_password="ixXXkIh01"
mailboxd_directory="/opt/zimbra/mailboxd"
mailboxd_keystore="/opt/zimbra/conf/keystore"
mailboxd_keystore_password="U3m8S3yK"
mailboxd_truststore="/opt/zimbra/java/jre/lib/security/cacerts"
mailboxd_truststore_password="changeit"
ssl_default_digest="sha256"
zimbraClusterType="none"
zimbraIPMode="ipv4"
zimbraPrefTimeZoneId="America/Santiago"
zimbra_ldap_userdn="uid=zimbra,cn=admins,cn=zimbra"
zimbra_require_interprocess_security="1"
INSTALL_PACKAGES="zimbra-core zimbra-ldap "
```


 



