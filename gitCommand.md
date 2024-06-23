[廖雪峰 Git 教程][https://www.liaoxuefeng.com/wiki/896043488029600]



## 初次使用

```sh
# 看系统有没有安装Git
$ git

# 每个机器都必须自报家门：你的名字和Email地址
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"

# 把当前目录变成Git可以管理的仓库
$ git init  
```



## 增、改、删

```sh
# 把文件修改添加到暂存区
$ git add a.txt b.txt
# 把暂存区的所有内容提交到当前分支
$ git commit -m "some discription..."

# 仓库当前的状态
$ git status

# 看看具体修改了什么内容
$ git diff readme.txt
$ git diff HEAD -- readme.txt

# 提交日志
$ git log
$ git log --pretty=oneline  # 简化日志
# 版本变动日志
$ git reflog

# 退回版本
$ git reset --hard HEAD^  # 退回到上个版本
$ git reset --hard 1094a  # 退回到指定版本  commit id

# 丢弃工作区的修改，使之回到最近一次git commit或git add时的状态
$ git checkout -- readme.txt
## 命令中的--很重要，没有--，就变成了“切换到另一个分支”的命令

# 取消暂存（stage）文件的操作
$ git reset HEAD <file>       # 删除stage中的相关内容
$ git restore --staged <file> # 用暂存区的内容覆盖工作区，再删除stage

# 删除文件
$ git rm test.txt  # 然后用 commit 提交或者用 checkout 恢复

```



## 远程库

```sh
# 创建SSH Key
$ ssh-keygen -t rsa -C "youremail@example.com"
```

可以在用户主目录里找到`.ssh`目录，里面有`id_rsa`和`id_rsa.pub`两个文件，这两个就是SSH Key的秘钥对，`id_rsa`是私钥，`id_rsa.pub`是公钥。

```sh
# 把本地仓库与远程仓库关联
$ git remote add origin https://github.com/Xiaomin-Bai/learngit.git  # 远程库命名为 origin
# 把本地库的所有内容推送到远程库上
$ git push -u origin master
```

由于远程库是空的，我们第一次推送`master`分支时，加上了`-u`参数，Git不但会把本地的`master`分支内容推送的远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来，在以后的推送或者拉取时就可以简化命令。

```sh
# 查看远程库信息
$ git remote -v
# 解除本地和远程的绑定关系
$ git remote rm origin
```



## 分支
