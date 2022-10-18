## 1.Git
Git是分布式的，Git不需要中心服务器,我们每台电脑拥有的东西都是一样的，每台电脑都拥有完整的版本库，我们实用的Git中心服务器只是为了方便交换大家的修改，这么服务器的地位和我们每台pc是一样的，因此工作的时候无需联网。
## 2.常用命令
linux命令都能用
#### 2.1设置和查看用户基本信息
##### 2.1.1 设置用户签名
git config --global user.name "lxf"
git config --global user.email "xxx@xx.com"
##### 2.1.2 查看
git config --global user.name
git config --global user.email
#### 2.2 起别名
2.2.1 在用户目录下创建一个 .bashrc文集
2.2.2 在里面填写别名信息：alias ll='ls -al'
2.2.3 source ~/.bashrc
#### 2.3 创建本地仓库
2.3.1 创建一个目录
2.3.2 进入该目录，执行git init命令
2.3.3 如果常见成功生成一个隐藏的.git目录
#### 2.4 基础操作指令
git工作目录下对文件进行修改（增加、修改、删除）会存在几个状态，这些修改的指令会随着我们执行git指令而改变
![[git状态图1.png]]
2.4.1 查看修改的状态
```git:
git status
```
2.4.2 将文件从工作区添加到暂存区
```git:
git add 单个文件名|通配符
```
2.4.3 提交暂存区文件到本地仓库
```git:
git commit -m "描述内容"
```
2.4.4 查看提交日志
```git:
git log [option]

options:
	--all 显示所有分支
	--pretty=oneline 将提交信息显示为一行
	--abbrev-commit 使显示的commitid更简短
	--graph 以图形化显示

git reflog 查看可引用的历史版本记录
```
2.4.5 版本切换
```git:
 git reset --hard commitid
```
2.4.6 让git不管理某些文件：在工作目录下创建 .gitignore文件，在里面配置不希望git管理的文件
2.4.7 删除
```git:
git rm --cached path 删除暂存区中的文件
```
## 3.分支
在版本控制过程中，同时推进多个任务，为每个任务，我们就可以创建每个任务单独的分支
#### 1.常用命令
```git:
git branch 分支名       #创建分支
git branch -v          #查看分支
git checkout 分支名     #切换分支
git merge 分支名        #将指定分支合并到当前分支
```
## 4.远程仓库
可以在码云或者github等平台上开一个远程仓库
#### 1.常用命令
```git:
git remote -v               #查看当前所有远程地址别名
git remote add 别名 远程地址 #起别名
git push 别名 分支           #推送本地分支上的内容到远程库
git clone 远程地址           #将远程仓库的内容克隆到本地,不需要登录账号，会拉取代码，初始化本地仓库和起别名
git pull 远程地址别名 远程分支 #将远程仓库对于分支最新内容拉下来后与当前本地分支直接合并
```


