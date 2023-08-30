**常用指令**
```
# 查看配置，按q键退出
git config --list --show-origin

# 配置账号 --global可选，当你想针对特定项目使用不同的用户名称与邮件地址时，可以在那个项目目录下运行没有 --global 选项的命令来配置。
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com

git init

# 将新增、修改的文件放入暂存区
git add * 
git commit -m '首次提交'

# 查看状态
git status -s

# 查看日志
git log

# 删除文件
git rm -r -f file.txt
git rm --cached log

# 移动文件
git mv from.txt to.txt

# 查看分支
git branch -a

# 在当前分支上创建一个testing分支
git branch testing
# 切换分支前应当做一次commit
git checkout testing
# HEAD 当前分支的别名

# 处理临时分支
git checkout master
git checkout -b hotfix
git checkout master
git merge hotfix
# 删除临时分支
git branch -d hotfix
git checkout testing

# 当合并分支产生冲突时，Git做了合并，但是没有自动地创建一个新的合并提交。 Git 会暂停下来，等待你去解决合并产生的冲突。 

# 查看所有包含未合并工作的分支
git branch --no-merged

# origin git服务器名称
git remote show origin

# 克隆分支
git clone -b branchName https://xxx/xxx.git

# 将本地分支的更改推送到远程分支
git push origin master
# 与远程仓库同步数据
git fetch origin

# 创建一个继承自origin/serverfix的跟踪分支，并且将pull与push绑定
git checkout -b serverfix origin/serverfix
# 删除远程分支
git push origin --delete serverfix

# git仅拉取一个目录下面文件的示例
git init
git remote add -f origin https://gitee.com/xxx/xxx.git
git config core.sparsecheckout true
echo "yourpath/" >> .git/info/sparse-checkout
cat .git/info/sparse-checkout
git pull origin master
```

**linux保存账号密码**
```
vim /.git-credentials
https://{username}:{password}@github.com

git config --global credential.helper store
find / -name .gitconfig

#可以看到.gitconfig文件，会多了一项：
[credential]
helper = store
```

**windows保存账号密码**
配置HOME系统变量，设定一个目录，如E:/git，在HOME变量对应的目录下新增\_netrc文件，内容如下
```
machine  gitee.com
login  youremail@126.com
password  yourpassword
```