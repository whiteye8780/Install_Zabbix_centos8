# Youtube_Command
# Update General package
dnf -y update  

# Stop SELinux
vi /etc/selinux/config  
SELINUX=disabled  

# reboot
reboot

# Update Repository
dnf -y install epel-release.noarch  

# Installation Zabbix5.0
dnf -y install https://repo.zabbix.com/zabbix/5.0/rhel/8/x86_64/zabbix-release-5.0-1.el8.noarch.rpm  
dnf clean all  
dnf -y install zabbix-agent2  

# Edit config
vi /etc/zabbix/zabbix_agent2.conf  
Server=192.168.0.10  
ServerActive=192.168.0.10  
Hostname=TestServer  


# Setting Firewall
firewall-cmd --add-port=10050/tcp --zone=public --permanent
firewall-cmd --reload

# Start service
systemctl enable zabbix-agent2
systemctl restart zabbix-agent2
