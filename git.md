# Git

## 基本操作指令

```shell
#对于本地已有的代码，初始化库
git init
#禁用换行符自动转换
git config --global core.autocrlf false
#克隆远程库
git clone url
#检查本地仓库文件状态
git status
#暂存文件，建立跟踪
git add 具体文件
#提交当前目录所有文件
git add .
#提交一个版本到本地仓库
git commit -m "commit info"
#将本地仓库的提交推送到远程仓库
git push 远程仓库 远程分支
git push origin master
#解决版本冲突，拉取远程库的版本，合并到本地库
git pull 远程仓库origin 远程分支master
#查看版本更新日志(空格 下翻页 b上翻页 q退出)
git log
git log --online
git reflog #解释回退版本的步数
#版本回退
git reset --hard 局部索引值
#版本回退上一版本
git reset --hard HEAD^ 
```

## 配置信息

```shell
#配置提交人姓名
git config --global user.name "huang"
#配置提交人email地址
git config --global user.email "mail@huanghong.top"

#查看系统配置
git config --system --list

#查看当前用户配置
git config --global  --list

#查看当前仓库配置
git config --local --list

git config --global user.name "Huang"
git config --global user.email "mail@huanghong.top"
```

## 分支管理

```shell
#创建分支
git branch

#查看分支
git branch -v

#切换分支
git checkout 分支名

#分支合并
切换至被合并的分支 git checkout 被合并分支名(eg.master)
合并 git merge 合并分支名(eg.huang)

#分支合并冲突解决
编辑冲突文件
git add 文件名
git commit -m "提交备注信息" （不能带文件名）

#分支删除
#切换至其他分支(本地分支)
git branch -D 分支名

#查看远程分支
git branch -r

#删除远程分支
git push origin --delete 分支名
```

## 远程仓库

```shell
#查看当前项目远程仓库
git remote -v

#添加远程库(创建远程库url 别名)
git remote add alias 远程库的url

#推送上传
git push alias 推送的分支

#拉取代码
git pull alias 分支
```

## 新建仓库提交代码流程

```shell
#初始化仓库
git init  

#添加到本地暂存区   或用  git add   (文件name)
git add .

#提交到本地仓库
git commit -m “first commit”

#添加远程仓库 
git remote add origin 远程仓库地址

#把远程仓库master分支拉取到本地仓库master分支
git pull origin master

#把本地仓库的master分支推送到远程仓库master分支
git push -u origin master                       
```

## 开发分支代码合并至主干

```she
git checkout master
git pull origin master
git merge dev
```

## 撤销commit

```shell
#连同add一起撤销
git reset --hard HEAD^ 

HEAD^ 表示上一个版本，即上一次的commit，也可以写成HEAD~1
如果进行两次的commit，想要都撤回，可以使用HEAD~2

--soft
不删除工作空间的改动代码 ，撤销commit，不撤销git add file

--hard
删除工作空间的改动代码，撤销commit且撤销add

如果commit注释写错了，先要改一下注释
git commit --amend
这时候会进入vim编辑器，修改完成你要的注释后保存即可。
```

## 异常记录

```shell
git reset --hard HEAD^：

fatal: ambiguous argument 'HEAD^': unknown revision or path not in the working tree.
Use '--' to separate paths from revisions, like this:
'git <command> [<revision>...] -- [<file>...]'

查了下原因可能有两种：

因为cmd控制台中换行符默认是^，而不是\ ，所以^符号被git编译为换行符了，解决方案：
git reset --hard "HEAD^"
git reset --hard HEAD~[return times]
该仓库到目前为止只有commit过一次代码，故已经是head版本，也会报这样的错，无需关心直接commit或者rm即可

创建仓库后将.git文件夹复制进待上传项目，后无法拉取推送
github拉取推送关闭电脑手动代理功能
Proxy CONNECT aborted
git pull origin main --allow-unrelated-histories
```



