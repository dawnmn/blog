#### **安装VMware**
官网选择Workstation Player，下载并安装

#### **新建操作系统**
**下载CentOS7.2**
进入CentOS官网下载页面，选择minimal ISO，依次选择，获取下载路径：[http://mirrors.aliyun.com/centos/7/isos/x86\_64/CentOS-7-x86\_64-DVD-1810.iso]()

**如果提示未开启vt**
进入BIOS，选择带有 virtualization的选项并开启

#### **不能联网**
`vi /etc/sysconfig/network-scripts/ifcfg-ens33`
将ONBOOT=no这一项改为ONBOOT=yes，重启虚拟机。

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

vim  /etc/selinux/config
```
设置SELINUX=disabled

#### **惯例配置**
`mkdir /var/download`