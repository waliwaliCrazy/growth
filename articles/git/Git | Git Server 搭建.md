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

---

# 前言

在前后端分离的项目中，经常会遇到跨域问题，遇到问题该如何解决呢？！

# 操作

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

## 4. 在服务器上创建个裸仓库

```
/home/git# cd code_repository/
/home/git/code_repository# mkdir abc.git
/home/git/code_repository# cd abc.git/
/home/git/code_repository/abc.git# git init --bare
Initialized empty Git repository in /home/git/code_repository/abc.git/
```