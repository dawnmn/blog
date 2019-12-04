#### **linux保存账号密码**
```
vim /.git-credentials
https://{username}:{password}@github.com

git config --global credential.helper store
find / -name .gitconfig

#可以看到.gitconfig文件，会多了一项：
[credential]
helper = store
```

#### **windows保存账号密码**
配置HOME系统变量，设定一个目录，如E:/git，在HOME变量对应的目录下新增\_netrc文件，内容如下
```
machine  gitee.com
login  youremail@126.com
password  yourpassword
```

#### **gitlab**
配置Two-Factor Authenticator：
手机开VPN，在应用宝下载Google Play，在Google Play里面装google-authencator，进行配置。

配置SSH Keys：
git bash
```
cd ~/.ssh   #检查是否生成过KEY
ssh-keygen -t rsa -C "邮箱"
#一路回车，生成文件
```
点击头像->settings->SSH Keys

配置Personal Access Tokens：
点击头像->settings->Access Tokens，账号是邮箱，密码是生成的token
```
machine  gitee.com
login  youremail@126.com
password  yourpassword
```
