---
layout: post
title: GIT使用经验汇总
tags: Linux
categories: Common
---

  Git安装、创建版本库、同步操作、分支管理、查看版本记录、远程仓库相关操作



**安装**

sudo apt-get install git

设置用户名和邮箱

git config --global user.name "your name"

git config --global user.email "email@example.com"



**创建版本库**

`1`.创建目录

mkdir learniggit

cd learninggit

`2`.初始化

git init



**同步操作**

`1`.编辑文档

`2`.查看修改内容

git diff 123.txt

`3`.修改添加暂存库

git add 123.txt

`4`.提交

git commit -m"修改备注"

将暂存区内容提交到当前分支



**分支管理**

| 操作                 | 意义                 |
| -------------------- | -------------------- |
| git checkout -b dev  | 创建并切换到dev分支  |
| git branch           | 查看当前分支         |
| git switch 分支名    | 切换分支             |
| git merge 分支名     | 合并某分支到当前分支 |
| git branch -d 分支名 | 删除分支             |



**查看版本记录**

git log

git log --pretty=oneline



**版本回退**

git reset --hard 版本号 *回退到某一版本*

git reset --HEAD^ *向前回退一步*



**撤销暂存区修改**

git checkout --文件名



**删除文件**

git rm 文件名

删除缓冲区文件

git rm --cached 文件名



**远程仓库**

连接库

`1`.将生成本机的公钥

cd ~/.ssh

ssh-keygen

`2`.将本机的公钥添加到github的ssh库中

`3`.添加远程库

git remote add origin ssh地址

`4`.查看库状态

cat .git/config

库操作

| 操作               | 意义                     |
| ------------------ | ------------------------ |
| git pull           | 从远程库拉取             |
| git push           | 将当前本地库提交到远程库 |
| git remote -v      | 查看当前连接的远程库     |
| git clone 地址     | 将远程库克隆到本地       |
| git remote rm 库名 | 删除远程库               |

