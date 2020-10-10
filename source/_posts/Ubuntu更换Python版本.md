---
title: Ubuntu更换Python版本
date: 2020-10-10 21:04:56
tags: ['Ubuntu', 'Python']
categories: ['Ubuntu']
typora-root-url: ..
---

**Ubuntu更换Python版本**

```bash
sudo add-apt-repository ppa:jonathonf/python-3.6
sudo apt-get update
sudo apt-get install python3.6

# 移除原python 链接
sudo rm /usr/bin/python3

# 更换默认python3 的版本为3.6
sudo ln -s /usr/bin/python3.6 /usr/bin/python3
```

**安装完Python3.6后，pip需要重新安装**

```bash
wget https://bootstrap.pypa.io/get-pip.py --no-check-certificate
sudo python3 get-pip.py
```

