#### **安装VMware**
官网选择[Workstation Pro](https://www.vmware.com/products/workstation-pro/workstation-pro-evaluation.html)，下载并安装

#### **新建操作系统**
**下载CentOS7.2**
进入CentOS官网下载页面，选择minimal ISO，依次选择，获取下载路径

**如果提示未开启vt**
进入BIOS，选择带有 virtualization的选项并开启

#### **不能联网**
配置ifcfg-ensxxx文件
```
vi /etc/sysconfig/network-scripts/ifcfg-ensxxx
ONBOOT=yes
```
重启虚拟机。

**查看IP**
```
yum install net-tools

ifconfig
```

#### **允许外网访问**
```
systemctl stop firewalld.service
systemctl disable firewalld.service
service iptables stop
chkconfig iptables off

yum install vim
vim  /etc/selinux/config
SELINUX=disabled
```
重启虚拟机。

#### **惯例配置**
```
mkdir /var/download
yum install wget
yum install zip unzip
yum install lrzsz
```