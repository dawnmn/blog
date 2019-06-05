#### **安装**
```
curl -s https://packagecloud.io/install/repositories/akopytov/sysbench/script.rpm.sh | sudo bash
sudo yum -y install sysbench
sysbench --version
```
#### **CPU**
```
sysbench cpu --cpu-max-prime=200000 run
sysbench threads --thread-yields=100 --thread-locks=2 --num-threads=64 run
```
#### **IO**
```
mkdir /var/download/temp
cd /var/download/temp
sysbench fileio --num-threads=16 --file-total-size=3G --file-test-mode=rndrw prepare
sysbench fileio --num-threads=16 --file-total-size=3G --file-test-mode=rndrw run
sysbench fileio --num-threads=16 --file-total-size=3G --file-test-mode=rndrw cleanup
```