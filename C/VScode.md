**远程开发**
windows生成本地ssh密钥，保存在`C:\Users\dawnmn\.ssh`目录：
```
ssh-keygen -t rsa
```
远程服务器，进入根目录下的`.ssh`目录，如果没有就创建。创建`authorized_keys`文件，内容为`C:\Users\dawnmn\.ssh\id_rsa.pub`
```
cd
cd .ssh
# rz 上传文件C:\Users\dawnmn\.ssh\id_rsa.pub
mv id_rsa.pub authorized_keys
```