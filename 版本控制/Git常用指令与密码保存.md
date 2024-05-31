## 远程仓库
**克隆**
```
git clone -b branchName https://xxx/xxx.git
```
**同步信息**
```
git fetch
```
**列出所有远程仓库**
```
git remote -v
```
**查看远程仓库信息**
```
git remote show origin
```
**添加远程仓库**
```
git remote add pb https://github.com/paulboone/ticgit
```
**删除远程仓库**
```
git remote remove pb
```
**修改远程仓库地址**
```
git remote set-url origin http://192.168.2.211/xxx/xxx.git/
```
**同步指定远程仓库信息**
```
git fetch origin
```
**推送到远程仓库**
如果远程分支不存在，则创建
```
git push origin testing
```
**删除远程仓库分支**
```
git push origin --delete testing
```
**多个远程仓库拉取与推送**
```
git pull origin
git push origin
```
## 本地
**保存与提交**
```
git add -A * 
git commit -m '首次提交'
```
**拉取与推送**
```
git pull
git push
```
## 分支
**列出本地分支**
```
git branch -a
git branch -vv
```
**新建本地分支**
从当前分支创建新的分支
```
git branch testing
```
**切换本地分支**
```
git checkout testing
```
**删除本地分支**
```
git branch -d testing
```
**合并本地分支**
将hotfix合并到当前分支
```
git merge hotfix
```
**拉取远端分支并切换**
```
git checkout -b testing origin/testing
```
**合并远程分支**
```
git merge origin/master
```
