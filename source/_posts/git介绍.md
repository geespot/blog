---
title: git介绍
date: 2016-12-04 21:45:58
tags:
- 技术
categories: [linux,数据库,开发]
---

# 简介
SCM（Source Code Management）起源，linux内核开发一直用一个叫做BitKeeper的SCM。但是后来许可证被更改,可能对内核开发造成障碍，Linus Torvalds就开发了Git,将linus树仓库转移到了git中,Git的维护人员滨野纯(Junio C Hammano)等人持续进行开发.
特点: 分布式仓库型SCM,工作区本省就是仓库。开发者拥有各自的仓库，所有的仓库都是并存的。

##  一、在本地仓库进行操作

### 1、创建新的仓库
```bash
$ mkdir ~/hello -p
$ cd ~/hello
$ git init
```    

### 2、Git 设置

```bash
$  git config --add user.email"geespot@163.com"
$  git config --add user.name "shenji \"Yan \" geespot"
```    

相当于在 .git/config .git/ 添加用户信息
[user]
        email = geespot@163.com
        name = shenji \"Yan \" geespot

### 3、将文件添加到仓库中

```bash
$  vim hello.c
$  git add hello.c
$  git commit  -m "add hello.c"
```   
/\* hello.c \*/
#include <stdio.h>
int main(void)
{
    printf("Hello Word! \n");
    return 1;
}

### 4、确认工作状态
```bash
$  git status
$  git ls-files
$  git diff
$  git diff HEAD
```   
首先将hello.c return 0 改为1；然后新建文件 goodbye.c
/\* goobye.c \*/
#include <stdio.h>
int main(void)
{
    printf("Goodbye world!! \n")
    return 0;
}

git diff 不是显示 最新提交与工作区之间的差异，二十"缓存区"与工作区之间的差异。

### 5、参照提交记录
```bash
$  git log <hash>                   #到提交三列值 hash 为止
$  git log <hash>..                 #从<hash>之后到最新提交为止
$  git log <hash1>..<hash2>         #从<hash1> 到<hash2>为止
$  git log       (git log HEAD)   	#到最新提交为止、即所有提交
$  git log HEAD~ (git log HEAD~1)   #到倒数第二次提交为止
$  git log HEAD~~(git log HEAD~2)   #到倒数第三次提交为止
$  git log HEAD~2.. HEAD~1          #从倒数第三次提交到倒数第二次提交（仅倒数第二次提交）
$  git log -p goodbye.c    #指定文件 仅输出与该文件闲逛的提交
```   

### 6、修改提交
```bash
$  git revert HEAD     # 进行的是最新的一次提交 恢复最新一次提交
$  vi hello.c
$  git add hello.c
$  git commit --amend
$  git log -p #（确认提交信息）
$  git reset --soft HEAD~1
$  git reset --hard HEAD
```   
对提交记录修改是慎重，特别需要注意的是，这个方法如果用在被其他仓库参照的仓库中，会出现不兼容的问题,因此不能在此情况下使用.

### 7、为提交加标签
```bash
$  git tag ver1 HEAD~1    # 倒数第二次提交加标签
$  git tag -l             # 查看标签
```   
###  8、创建分支
```bash
$  git branch ver1x ver1   # 为ver1标签为起点 创建名为ver1x的分支
$  git branch -a           # 查看分支
```   
###  9、rebase命令

```bash
$  git rebase master       #master 的最新提交 如果 --onto 选项指定到任意提交
```   

### 10、合并分支
```bash
$  git barach 
$  vim goodebye.c                                   # return 0 修改成1
$  git commit -a -m "mod goodbye.c return 0 to 1"   # 分支提交
$  git merge ver1x              # 合并分支
```   

### 11、提取补丁
```bash
$  git format-patch ver1..ver2
```   
生成如下补丁
0001-add-goodbye-mod-hello.patch
0002-add-thanks.patch
0003-mod-googbey-return-to-1.patch

###  12、提取代码树
```bash
$  git archive --format=tar --prefix="hello-v2/" ver2 > ../hello-ver2.tar
```   

##  二、与远程仓库进行共同作业
### 1、复制仓库
```bash
$  git clone git://git.kernerl.org/pub/scm/linux/kermer/git/torvalds/linux-2.6
$  git clone hello local   # 因为kernel比较大1G 这里将hello 当做远程仓库 复制到其他位置
```   
### 2、建立本地分支
```bash
$ git branch -b work    # 针对当前分支最低呢修改 创建分支
```   

### 3、追踪分支
```bash
$  git branch -r     #显示追踪分支的列表
$  git remote show origin
```   

### 4、与远程仓库同步
```bash
$ git commit -a
$ git checkout master
$ git pull
```   

###  5、开发分支rebase最新状态
```bash
$  git checkout work
$  git commit -a  -m ""
$  git rebase master
```   


###  6 、git其他有用的命令

git push      本地仓库直接传递到远程仓库,仅在拥有对远程仓库的修改权限有效
git fetch     不进行be你弟分支的合并。git pull 是内部跳出git fetch 和git merge的操作
git stash     将工作区未提交的修改保存并移动到蒂塔分支使用。git stash pop 可以调出状态
git cherry-pick  将其他分支下的1个提交使用到当前分支使用，由于提交会赋值，因此会编程其他的散列值
git gc        删除未使用的对象或文件，优化仓库
git am        从mbox格式的文件导入补丁并提交
git bisect    用来指定导入了bug的提交







