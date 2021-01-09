Реализованный функционал:
 Отказоустойчивая ферма centos8 с wordpress на базе HA-Cluster Mysql , использованные модули:
    - wordpress
    - apache 
    - php 
    - rsyslog 
    - bacula 
    - haproxy 
    - keepalived
    - innodb mysql 
    - firewalld 
Схема проекта находится arch.drowio.

Для запуска проекта выполнить следующее:
  - выполнить в консоле :
    - bash startprj.sh
    - sudo su
    - echo "192.168.10.80 wp-project.org" > /etc/hosts
    - exit
    - curl --insecure -I https://192.168.10.80
    - curl --insecure -I https://192.168.10.102/
    - curl --insecure -I https://192.168.10.101/
    - curl http://192.168.10.101:8080/stats
    - curl http://192.168.10.102:8080/stats
    - curl http://192.168.10.2/bacula-web/test.php
    - curl http://192.168.10.2/bacula-web/
    - vagrant ssh haproxy1
        - [root@haproxy1 vagrant]# netstat -ntupl | grep :443
        - [root@haproxy1 vagrant]# netstat -nlp | grep 80
        - [root@haproxy1 vagrant]# netstat -nlp | grep 8080
        - [root@haproxy1 vagrant]# firewall-cmd --list-all
    - vagrant ssh mon
        - [vagrant@mon ~]$ sudo su
        - [root@mon vagrant]# ls /var/log
        - [root@mon vagrant]# cd /var/www/html/bacula-web #для доступа к удаленному управлению bacula
        - [root@mon vagrant]# -u apache php bwc setupauth #для доступа к удаленному управлению bacula, сгенерим бд пользователей
        - [root@mon vagrant]# bacula-dir -c /etc/bacula/bacula-dir.conf -t
        - [root@mon vagrant]# bconsole
          * status client
          * run job=BackupFrontend
          * messages
          * status dir
          * run job=BackupMysql
          * messages
          * status dir
          * quit
        

    




Протестировано на следующем оборудовании:
victory@edg-xps:~$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 20.04.1 LTS
Release:	20.04
Codename:	focal
victory@edg-xps:~$ hostnamectl
   Static hostname: edg-xps
         Icon name: computer-laptop
           Chassis: laptop
        Machine ID: 4727008b6c614650bf9b988f31cb7b8b
           Boot ID: 60075c323e8e4663a340c6d8340fd854
  Operating System: Ubuntu 20.04.1 LTS
            Kernel: Linux 5.4.0-58-generic
      Architecture: x86-64
victory@edg-xps:~$ 
victory@edg-xps:~/otus/linux_prj$ vagrant vbguest --status
[mon] GuestAdditions 6.1.16 running --- OK.
[sqlnode1] GuestAdditions 6.1.16 running --- OK.
[sqlnode2] GuestAdditions 6.1.16 running --- OK.
[sqlnode3] GuestAdditions 6.1.16 running --- OK.
[frontend1] GuestAdditions 6.1.16 running --- OK.
[frontend2] GuestAdditions 6.1.16 running --- OK.
[haproxy1] GuestAdditions 6.1.16 running --- OK.
[haproxy2] GuestAdditions 6.1.16 running --- OK.

victory@edg-xps:~/otus/linux_prj$  ansible --version
ansible 2.9.6
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/victory/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 3.8.5 (default, Jul 28 2020, 12:59:40) [GCC 9.3.0]
