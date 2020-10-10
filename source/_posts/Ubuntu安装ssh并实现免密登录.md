---
title: Ubuntu安装ssh并实现免密登录
date: 2020-10-10 21:14:37
tags: ['Ubuntu', 'ssh', '免密登录']
categories: ['Ubuntu']
typora-root-url: ..
---

**修改root用户密码**

```bash
sudo passwd root
```

<!--more-->

**更新apt**

```bash
sudo apt-get update
```

**安装vim**

```bash
sudo apt-get install vim
```

**安装SSH、配置SSH无密码登陆**

```bash
sudo apt-get install openssh-server
```

**修改ssh配置文件**

```bash
vim /etc/ssh/sshd_config
# 在配置文件中添加一行
PermitRootLogin yes
```

**启动ssh服务**

```bash
service ssh start
```

**登录本机测试**

```bash
ssh localhost
```

**添加公钥实现免密登录**

```bash
cd ~/.ssh/          # 若没有该目录，请先执行一次ssh localhost
ssh-keygen -t rsa   # 会有提示，都按回车就可以，生成公钥私钥
vim authorized_keys # 保存公钥的文件
# 将公钥复制到authorized_keys文件中
```

**测试远程登录**

```bash
ssh root@ip地址 -p 端口号
```

