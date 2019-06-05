#### **不能联网**
`vi /etc/sysconfig/network-scripts/ifcfg-ens33`
将ONBOOT=no这一项改为ONBOOT=yes

#### **允许外网访问**
```
systemctl stop firewalld.service
systemctl disable firewalld.service
service iptables stop
chkconfig iptables off

vim  /etc/selinux/config
```
设置SELINUX=disabled

#### **惯例配置**
`mkdir /var/download`