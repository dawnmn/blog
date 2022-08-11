创建本地版本库：
进入新的空目录，TortoiseSVN->create repository here

本地仓库首次提交：TortoiseSVN->import
进入新的空目录(副本目录)，空白地方右键：
svn->Checkout
TortoiseSVN->add
svn->update
svn->commit

例如你的版本库包含了图片，两个人同时编辑这个文件，没有办法将这两个修改合并，Harry 或 Sally 会丢失他们的修改。