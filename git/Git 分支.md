# Git 分支

## Git 分支创建与合并

**新建分支**  

	git checkout -b newBranch
    
等价于：

	git branch newBranch
    git checkout newBranch

**删除分支**

	git branch -d newBranch;            //deleted branch newBranch

** 合并分支**

	git chekout master;
    git merge newBranch;
    
** 冲突的解决**  

手动合并

## 分支管理

**显示所有分支**

	git branch
    
** 显示所有分支的最后一次提交**

	git branch -v
    

** 使用`--merged`和`--no-merged`选项显示所有已经合并或尚未合并到当前分支的分支**

	git branch --merged
    
## 远程分支
远程引用是对远程仓库的引用（指针），包括分支、标签等。可以通过`git ls-remote`来显示获得远程引用的完整列表，或者通过`git remote show`获得远程分支的更多信息。更常用的方法是利用*远程跟踪分支*。

远程跟踪分支是剁成分支状态的引用，他们不是你不能移动的本地引用，以(remote)/(branch)形式命名

### 推送

	git push origin serverfix
    
把本地仓库`serverfix` 分支推送到远程仓库的`awesomebranch`分支

	git push origin serverfix:awesomebranch

> ** Note**
> 如何避免每次输入密码
> 如果使用HTTP URL 来推送，git服务器每次回讯问用户名和密码。
> 可以通过运行`git config --global credential.helper cache`来设置。
> 

**拉取远程分支**

	git fetch origin

当协作者从服务器上拉取数据时，会在本地生成一个远程分支`origin/serverfix`指向服务器上的`serverfix`分支的引用，特别需要注意的是当拉取到新的远程跟踪分支时，本地不会生成一个可编辑的副本，这种情况下，不会有一个新的serverfix分支，只有一个不可修改的`origin/serverfix`指针。

可以运行`git merge origin/serverfix`将这些工作合并到当前分支，如果想要在本地的`serverfix`分支上工作，可以将其建立在远程分支上：

	git checkout -b serverfix origin/serverfix
    
这会给你一个用于工作的本地分支，并且起点位于`origin/serverfix`.

**跟踪分支**

	git checkout --track origin/serverfix  //创建跟踪分支的快捷方式
    等同于：
    git checkout -b serverfix origin/serverfix
    
如果想要查看设置的跟踪分支，可以使用`git branch` 的`-vv`选项，这会将本地的所有分支列出来并显示更多信息，如某个本地分支正在跟踪哪个远程分支与本地分支是否领先、落后或是都有。

** 删除远程分支**

	git push origin --delete serverfix
    
该命令是从服务器上删除这个指针，Git会从保留数据一段时间直到垃圾回收运行。

## 变基

Git 整合来自不同分支的修改主要有两种方法：`merge`和`rebase`

使用方法：

	git checkout experiment
    git rebase master
    git checkout master
    git merge experiment
    

