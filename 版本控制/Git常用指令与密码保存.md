## 远程
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
## 信息
**状态**
```
git status
```
**日志**
```
git log
```
操作
```
# 翻页
ctrl+f ctrl+b
# 上下滑动行
j k
# 退出
q
```
仅显示添加或删除内容匹配指定字符串的提交
```
git log -S function_name
```
**HEAD**
指向当前所在的本地分支，是当前所在的本地分支的别名。
**.gitignore**
语法如下：
* 所有空行或者以#开头的行都会被 Git 忽略。
* 可以使用标准的 glob 模式匹配，它会递归地应用在整个工作区中。
* 匹配模式可以以（/）开头防止递归。
* 匹配模式可以以（/）结尾指定目录。
* 要忽略指定模式以外的文件或目录，可以在模式前加上叹号（!）取反。


glob模式是指shell所使用的简化了的正则表达式：
* `*`匹配零个或多个任意字符。
* `?`只匹配一个任意字符。
* `[abc]`、`[a-z]`匹配任何一个列在方括号中的字符。
* `**`匹配任意中间目录，比如 a/**/z 可以匹配 a/z 、 a/b/z 或 a/b/c/z 等。

