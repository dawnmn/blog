**安装VMware**
官网选择[Workstation Pro](https://www.vmware.com/products/workstation-pro/workstation-pro-evaluation.html)，下载并安装。


**下载CentOS7.2**
进入CentOS官网下载页面，选择minimal ISO，依次选择，获取下载路径。

**如果提示未开启vt**
进入BIOS，选择带有 virtualization的选项并开启。

**不能联网**
配置ifcfg-ensxxx文件
```
vi /etc/sysconfig/network-scripts/ifcfg-ensxxx
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.152.128
NETMASK=255.255.255.0
GATEWAY=192.168.152.2
DNS1=223.5.5.5
DNS2=114.114.114.114

```
重启虚拟机。

**允许外网访问**
```
systemctl stop firewalld.service
systemctl disable firewalld.service
service iptables stop
chkconfig iptables off

yum -y install vim
vim ~/.vimrc
set nu
vim  /etc/selinux/config
SELINUX=disabled
```
重启虚拟机。

**惯例配置**
```
mkdir /var/download
yum -y install lrzsz zip unzip wget net-tools
```