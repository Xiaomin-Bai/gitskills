[廖雪峰 Git 教程][https://www.liaoxuefeng.com/wiki/896043488029600]



## 初次使用 Git

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
$ git reset --hard HEAD^  # 退回到 上 个版本
$ git reset --hard HEAD^2 # 退回到 上上 个版本
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



## 分支

### 创建、切换、删除

注：下面示例中使用 `dev` 为分支名

```sh
# 创建分支
$ git branch dev
# 切换分支
$ git checkout dev
$ git checkout -b dev  # 创建并切换到 "dev" 分支

# 查看当前分支
$ git branch
# 删除分支
$ git branch -d dev
$ git branch -D dev # 当一个分支没有被合并时 -d 无法删除，需使用 -D 强制删除
```

用 `switch` 命令取代 `checkout` 命令

```sh
# 创建并切换到 "dev" 分支
$ git switch -c dev  # use `-c` to create a new branch 
```



### 合并分支

```sh
# 合并指定分支到当前分支
$ git merge dev
```

通常，合并分支时 Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息。如果要强制禁用`Fast forward`模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。

```sh
# --no-ff参数，表示禁用 Fast forward
$ git merge --no-ff -m "merge with no-ff" dev
```

解决冲突：修改冲突部分内容后重新 add 并 commit 即可。

```sh
# 查看提交日志
$ git log --graph --pretty=oneline --abbrev-commit # --graph 查看分支合并图
```



### 分支管理策略

在实际开发中，我们应该按照几个基本原则进行分支管理：

- `master`分支。应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；
- `dev`分支。干活都在`dev`分支上，也就是说，`dev`分支是不稳定的，到某个时候，比如1.0版本发布时，再把`dev`分支合并到`master`上，在`master`分支发布1.0版本；
- 个人分支。每个开发者都有自己的分支，时不时地往`dev`分支上合并就可以了。



### Bug分支

有时候，当前分支上的修改还没有提交（如：任务只完成了一半），但是不得不切换分支（如：修复紧急bug），那便需要在切换分支前将没有提交的修改“储藏”起来

```sh
# 把当前工作现场“储藏”起来，等以后恢复现场后继续工作
$ git stash

# 查看 stash 中的内容
$ git stash list
```

然后就可以放心切换分支了。

切换回来后，需要恢复场景，

```sh
$ git stash apply  # 恢复场景，但是 stash 内容还没有删除
$ git stash drop   # 删除 stash 内容

# 下面这条命令效果等于上面两条命令之和
$ git stash pop
```



将其他分支的 commit 应用到此分支上

```sh
$ git cherry-pick 4c805e2  # 将其他分支上的 commit (id=4c805e2) 应用到此分支上
```

应用场景：假设 `commit 4c805e2` 解决了一个bug，那么理所当然应当应用到各个分支上，该命令提供了一个便捷方法，只需修改一处并提交，即可在其他分支上提交相同内容。





## 远程库

```sh
# 创建SSH Key
$ ssh-keygen -t rsa -C "youremail@example.com"
```

可以在用户主目录里找到`.ssh`目录，里面有`id_rsa`和`id_rsa.pub`两个文件，这两个就是SSH Key的秘钥对，`id_rsa`是私钥，`id_rsa.pub`是公钥。

```sh
# 把本地仓库与远程仓库关联
$ git remote add origin https://github.com/.../learngit.git  # 远程库命名为 origin
# 把本地库的所有内容推送到远程库上
$ git push -u origin master
```

由于远程库是空的，我们第一次推送`master`分支时，加上了`-u`参数，Git不但会把本地的`master`分支内容推送的远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来，在以后的推送或者拉取时就可以简化命令。

```sh
# 查看远程库信息
$ git remote
$ git remote -v  # 加上 -v 表示查看详细信息，可看到 push 地址
# 解除本地和远程的绑定关系
$ git remote rm origin
```



### 推送分支

```sh
$ git push origin master # 推送主分支，要时刻与远程同步
$ git push origin dev    # 推送开发分支。所有成员都在上面工作，所以也需要与远程同步
```

bug 分支 和 feature 分支是否推到远程，看心情。



### 抓取分支

若远程库有 dev 分支而本地没有，则创建远程`origin`的`dev`分支到本地

```sh
# 则创建远程`origin`的`dev`分支到本地
$ git checkout -b dev origin/dev 
```

然后就可以愉快地在 dev 分支上开发了。

将远程库上的变更 pull 到本地：

```sh
$ git pull
```

没有指定本地`dev`分支与远程`origin/dev`分支的链接，`git pull` 就会失败，此时需要设置`dev`和`origin/dev`的链接：

```sh
# 建立远程分支和本地分支的链接
$ git branch --set-upstream-to=origin/dev dev
```

若 pull 之后有冲突，则需要手动解决，这和 merge 带来的冲突解决方法完全一样。



### Rebase

多人在同一个分支上协作时，很容易出现冲突。即使没有冲突，后push的童鞋不得不先pull，在本地合并，然后才能push成功。于是提交历史上会有很多分支，不好看。`rebase` 命令可以将分支整理成一条直线。

```sh
$ git log --graph --pretty=oneline --abbrev-commit
$ git rebase  # 把本地 未push 的分叉提交历史整理成直线
$ git log --graph --pretty=oneline --abbrev-commit
```



## 标签管理

tag 就是一个让人容易记住的有意义的名字，相当于 commit id 的一个别名。通常用 tag 来管理版本号。

