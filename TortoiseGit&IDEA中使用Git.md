# TortoiseGit

## 本地仓库操作

**创建本地仓库**

创建空文件夹进入  --->  右键点击Git Create repository here

**克隆仓库**

复制网址  --->  右键点击Git clone

**添加新文件到暂存区 --> 版本库**

点击文件  --->  右键TortoiseGit--Add..  (文件左下角出现加号)  --->   右键Git Commit -> "master",在message中写描述.

也可以直接右击空白地方  -->  Git Commit -> "master"  -->  选择想加入的文件,添加描述  -->  添加至版本库

## 远程仓库操作

### 推送本地至远程

当本地仓库是从远程上克隆的时候:

右击空白处  --->  右击TortoiseGit--push  --->  选择本地,远程分支;  --->   推送完成

本地新创建仓库推送到仓库:

右击空白处  --->  右击TortoiseGit--push  --->  选择本地,远程分支;配置远程仓库url(manage);  --->   推送完成

### 拉取远程至本地

右击空白处  --->  右击TortoiseGit--pull  --->  拉取完成

## 分支

创建:

TortoiseGit--Create Branch..  --->  写入分支名称  --->   选择base on: 此分支基于什么创建  --->  创建完成

切换:

TortoiseGit--Switch/Checkout...   --->  Switch to:选择切换分支   --->   切换完成

合并:  (只在本地合并了分支,并没有推送至远程,需要再推送一次合并的分支才能推至远程)

在要合并的分支中  --->   TortoiseGit--merge   --->   from:选择被合并分支   --->   合并完成

推送指定分支到远程仓库:

TortoiseGit--push  --->   local:选择本地&远程分支(一般和本地分支保持一致,没有的话会在远程创建)





# IDEA中使用Git

## IDEA中配置Git

如果Git在默认路径,idea会找到它,如果是自己配置的则需要手动操作配置路径:

选择file -> settings打开设置窗口,找到Version Control 下的git选项,选择git安装目录后点击test按钮检测是否正确.如果正确会显示当前版本号.

## IDEA基础操作

### idea创建工程并将工程添加至idea