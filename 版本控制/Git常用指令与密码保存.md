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