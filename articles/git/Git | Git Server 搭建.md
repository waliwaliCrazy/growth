<h1> Git | Git Server 搭建 </h1>

# 系列文章目录

---

**Table of Contents**

- [系列文章目录](#系列文章目录)
- [前言](#前言)
- [操作](#操作)
  - [1. 创建 git 用户](#1-创建-git-用户)
  - [2. 创建 .ssh 目录](#2-创建-ssh-目录)
  - [3. 自定义仓库的根目录](#3-自定义仓库的根目录)
  - [4. 在服务器上创建个裸仓库](#4-在服务器上创建个裸仓库)
  - [5. 手动配置一个公钥](#5-手动配置一个公钥)
  - [6. 在本地测试一下能否操作](#6-在本地测试一下能否操作)
- [总结](#总结)

---

# 前言

在前后端分离的项目中，经常会遇到跨域问题，遇到问题该如何解决呢？！

# 操作

> **说明:** 本示例在一台云服务器上搭建的 git 服务，其中 1，2，3，4，5 均为在云服务器进行的操作。
> 当然也可以在虚拟机或者 docker 中进行操作

## 1. 创建 git 用户

```sh
adduser \
   --system \
   --shell /bin/bash \
   --gecos 'Git Version Control' \
   --group \
   --disabled-password \
   --home /home/git \
   git
```

## 2. 创建 .ssh 目录

```
/home# su git
/home# cd git
/home/git# mkdir .ssh && chmod 700 .ssh
/home/git# touch .ssh/authorized_keys && chmod 600 .ssh/authorized_keys
```

## 3. 自定义仓库的根目录

```
/home/git# mkdir code_repository
```

**注意：** 这里需要使用 git 用户进行操作

## 4. 在服务器上创建个裸仓库

```
/home/git# cd code_repository/
/home/git/code_repository# mkdir abc.git
/home/git/code_repository# cd abc.git/
/home/git/code_repository/abc.git# git init --bare
```

**注意：** 这里需要使用 git 用户进行操作

## 5. 手动配置一个公钥

将我们自己本地的公钥文件加入 `/home/git/.ssh/authorized_keys` 中

## 6. 在本地测试一下能否操作

```
$ mkdir abc
$ cd abc
$ git init
$ touch README.md
$ git add .
$ git commit -m "first add reademe.md"
$ git remote add origin ssh://git@xxx.xx.xx.xx:/home/git/code_repository/abc.git
$ git push -u origin master
```

**注意：** 这里在本机运行的，不是在服务器上

# 总结

这种方式创建的仓库，并没有设置任何权限，只要在 `authorized_keys` 的公钥用户就可以对仓库进行读写操作