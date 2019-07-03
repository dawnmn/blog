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
