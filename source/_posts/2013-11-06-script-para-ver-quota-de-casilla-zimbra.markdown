---
layout: post
title: "Script para ver quota de casilla Zimbra"
date: 2013-11-06 17:14
comments: true
categories: zimbra, script
---

A continuación un script que lista la quota que está utilizando una casilla de usuario y cuanto es la quota asignada (esto en el caso de tener) en Zimbra.

{% codeblock lang:objc %}

all_account=`zmprov -l gaa`;
for account in ${all_account}
do
    mb_size=`zmmailbox -z -m ${account} gms`;
    mb_quota=`zmprov ga ${account} |grep -i zimbraMailQuota |cut -d":" -f2`
    if [ $mb_quota != 0 ]; then
            echo "Mailbox size of ${account} = ${mb_size}"
            count=`expr length $mb_quota`
            if [ $count == 8 ]; then
                echo "Mailbox Quota is = `echo ${mb_quota}/1024/1024 | bc` MB "
            else
                echo "Mailbox Quota is = `echo ${mb_quota}/1024/1024/1024 | bc` GB "
            fi
    else
            echo "Mailbox size of ${account}  = ${mb_size}"
            echo "The Mailbox isn't Setting Quota"
    fi
    echo "______________________"
done
{% endcodeblock %}

Lo anterior, arrojará por pantalla valores como los siguientes:

{% codeblock lang:objc %}
______________________
Mailbox size of user@example.com  = 281.51 MB
The Mailbox isn't Setting Quota
______________________
Mailbox size of user2@example.com  = 1.30 GB
The Mailbox isn't Setting Quota
______________________
Mailbox size of user3@example.com = 718.00 KB
Mailbox Quota is = 10 MB
{% endcodeblock %}
