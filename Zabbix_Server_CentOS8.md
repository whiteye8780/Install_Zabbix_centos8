# Install_Zabbix_centos8
# Update General package
dnf -y update

# Update Repository
dnf -y install epel-release.noarch

# Stop SELinux
vi /etc/selinux/config  
SELINUX=disabled  

# reboot
reboot

# Installation maria,httpd,php 
dnf -y install mariadb-server httpd php

# Installation Zabbix5.0
dnf -y install https://repo.zabbix.com/zabbix/5.0/rhel/8/x86_64/zabbix-release-5.0-1.el8.noarch.rpm  
dnf clean all  
dnf -y install zabbix-server-mysql zabbix-web-mysql zabbix-apache-conf

# 日本語をインストール install Japanese
dnf -y install zabbix-web-japanese

# Installation Zabbix-Agent2
dnf -y install zabbix-agent2  

# Change Your Timezone
vi /etc/php-fpm.d/zabbix.conf  
php_value[date.timezone] = Asia/Tokyo

# Start and configure mariadb
systemctl enable mariadb.service  
systemctl start mariadb.service  

# Create Database
mysql -uroot

create database zabbix character set utf8 collate utf8_bin;   

GRANT ALL ON zabbix.* TO zabbix@localhost identified by 'password';  

show databases;  
select user from mysql.user where user='zabbix';

exit;


# Import initial schema and data
zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p zabbix

# Configure the zabbix
vi /etc/zabbix/zabbix_server.conf  
DBPassword=password

# Configure the zabbix-agent
vi /etc/zabbix/zabbix_agent2.conf  
Server=127.0.01  
ServerActive=127.0.0.1  
HostName=Zabbix server  


# Setting Firewall
firewall-cmd --add-port=10051/tcp --zone=public --permanent  
firewall-cmd --add-port=10050/tcp --zone=public --permanent   
firewall-cmd --add-service=http --zone=public --permanent  
  
firewall-cmd --reload  

# Start services
systemctl restart zabbix-server httpd php-fpm  
systemctl enable zabbix-server httpd php-fpm   

# Start Agent service
systemctl start zabbix-agent2  
systemctl enable zabbix-agent2  

# Access  Zabbix from the browser
192.168.0.10/zabbix



