# dns
В вагранте раскатываем через ансибл 4 виртуалки. 2 - сервера DNS и остальные 2 - клиенты.
По дз должно быть:
```добавить еще один сервер client2
завести в зоне dns.lab
имена
web1 - смотрит на клиент1
web2 смотрит на клиент2

завести еще одну зону newdns.lab
завести в ней запись
www - смотрит на обоих клиентов

настроить split-dns
клиент1 - видит обе зоны, но в зоне dns.lab только web1

клиент2 видит только dns.lab
```
Заходим на client1 (client) машину и проверяем доступность или недоступность зон
```
[root@client vagrant]# nslookup dns.lab 192.168.50.10
Server:         192.168.50.10
Address:        192.168.50.10#53

Name:   dns.lab
Address: 192.168.50.10
Name:   dns.lab
Address: 192.168.50.11

[root@client vagrant]# nslookup web1.dns.lab 192.168.50.10
Server:         192.168.50.10
Address:        192.168.50.10#53

Name:   web1.dns.lab
Address: 192.168.50.15


[root@client vagrant]# nslookup web1.dns.lab 192.168.50.11
Server:         192.168.50.11
Address:        192.168.50.11#53

Name:   web1.dns.lab
Address: 192.168.50.15


[root@client vagrant]# nslookup web2.dns.lab 192.168.50.10
Server:         192.168.50.10
Address:        192.168.50.10#53

** server can't find web2.dns.lab: NXDOMAIN
[root@client vagrant]# nslookup web2.dns.lab 192.168.50.11
Server:         192.168.50.11
Address:        192.168.50.11#53

** server can't find web2.dns.lab: NXDOMAIN



[root@client vagrant]# nslookup newdns.lab 192.168.50.10
Server:         192.168.50.10
Address:        192.168.50.10#53

Name:   newdns.lab
Address: 192.168.50.10
Name:   newdns.lab
Address: 192.168.50.11
[root@client vagrant]# nslookup newdns.lab 192.168.50.11
Server:         192.168.50.11
Address:        192.168.50.11#53

Name:   newdns.lab
Address: 192.168.50.10
Name:   newdns.lab
Address: 192.168.50.11

[root@client vagrant]# nslookup www.newdns.lab 192.168.50.10
Server:         192.168.50.10
Address:        192.168.50.10#53

Name:   www.newdns.lab
Address: 192.168.50.15
Name:   www.newdns.lab
Address: 192.168.50.16
[root@client vagrant]# nslookup www.newdns.lab 192.168.50.11
Server:         192.168.50.11
Address:        192.168.50.11#53

Name:   www.newdns.lab
Address: 192.168.50.15
Name:   www.newdns.lab
Address: 192.168.50.16
```
Из запросов видно (как на 10, так и на 11й адрес), что работает DNS сервер и зоны реплицируются с мастера на ведомого, отрабатывают одинаково. Также видно, что работает разделение зон (split-dns)
Заходим на client2 (client2) машину и проверяем доступность или недоступность зон
```
[vagrant@client2 ~]$ sudo su
[root@client2 vagrant]# nslookup dns.lab 192.168.50.10
Server:         192.168.50.10
Address:        192.168.50.10#53

Name:   dns.lab
Address: 192.168.50.11
Name:   dns.lab
Address: 192.168.50.10

[root@client2 vagrant]# nslookup dns.lab 192.168.50.11
Server:         192.168.50.11
Address:        192.168.50.11#53

Name:   dns.lab
Address: 192.168.50.10
Name:   dns.lab
Address: 192.168.50.11

[root@client2 vagrant]# nslookup web1.dns.lab 192.168.50.10
Server:         192.168.50.10
Address:        192.168.50.10#53

Name:   web1.dns.lab
Address: 192.168.50.15

[root@client2 vagrant]# nslookup web1.dns.lab 192.168.50.11
Server:         192.168.50.11
Address:        192.168.50.11#53

Name:   web1.dns.lab
Address: 192.168.50.15

[root@client2 vagrant]# nslookup web2.dns.lab 192.168.50.10
Server:         192.168.50.10
Address:        192.168.50.10#53

Name:   web2.dns.lab
Address: 192.168.50.16

[root@client2 vagrant]# nslookup web2.dns.lab 192.168.50.11
Server:         192.168.50.11
Address:        192.168.50.11#53

Name:   web2.dns.lab
Address: 192.168.50.16

[root@client2 vagrant]# nslookup newdns.lab 192.168.50.10
Server:         192.168.50.10
Address:        192.168.50.10#53

** server can't find newdns.lab: NXDOMAIN

[root@client2 vagrant]# nslookup newdns.lab 192.168.50.11
Server:         192.168.50.11
Address:        192.168.50.11#53

** server can't find newdns.lab: NXDOMAIN

[root@client2 vagrant]# nslookup www.newdns.lab 192.168.50.10
Server:         192.168.50.10
Address:        192.168.50.10#53

** server can't find www.newdns.lab: NXDOMAIN

[root@client2 vagrant]# nslookup www.newdns.lab 192.168.50.11
Server:         192.168.50.11
Address:        192.168.50.11#53

** server can't find www.newdns.lab: NXDOMAIN
```

ДЗ выполнено!
