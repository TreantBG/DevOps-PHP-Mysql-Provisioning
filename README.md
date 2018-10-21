Docker swarm nginx,php,mysql
=====================
 A simple Docker stack that includes:
  - Nginx
  - Mysql
  - PHP 7 FPM 
  
 And for monitoring:
  - Grafana
  - Prometeus
  - Alert Manager
  - ELK
  
  
# Requirements
  - VirtualBox
  - Vagrant
  
 
# Installation

install vagrant plugin to access host shell
 - vagrant plugin install vagrant-host-shell
 
Setup the machines with swarm (may take up to 30+ minutes)
 - vagrant up

Wait services to go UP (mysql database stars a bit slover then others)

Go to http://192.168.10.2:8080/ to see simple php/msql app

Go to http://192.168.10.2:3000 to see monitoring system.
Login with admin/admin
Grafana need datasources to display dashboards

