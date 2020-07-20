---
atitle: Git查看或修改用户名和邮箱并实现GitHub免密登录
date: 2020-07-19 11:55:11
tags: ['Git', 'GitHub']
categories: ['Git']
typora-root-url: ..
---

# 查看用户名

```bash
git config user.name
```

# 查看邮箱

```bash
git config user.email
```

<!--more-->

# 修改用户名

```bash
git config --global user.name 'Jack'
```

# 修改邮箱

```bash
git config --global user.email 'Jack@mail.com'
```

# 生成SSH密钥

```bash
ssh-keygen -t rsa
```

该命令执行完后会在`.ssh`目录下面生成公钥(id_rsa.pub)和私钥(id_rsa)。

# GitHub免密登录

![github中添加ssh keys实现免密登录](/images/Git%E6%9F%A5%E7%9C%8B%E6%88%96%E4%BF%AE%E6%94%B9%E7%94%A8%E6%88%B7%E5%90%8D%E5%92%8C%E9%82%AE%E7%AE%B1%E5%B9%B6%E5%AE%9E%E7%8E%B0GitHub%E5%85%8D%E5%AF%86%E7%99%BB%E5%BD%95/github%E4%B8%AD%E6%B7%BB%E5%8A%A0ssh%20keys%E5%AE%9E%E7%8E%B0%E5%85%8D%E5%AF%86%E7%99%BB%E5%BD%95.png)

在Title处给该公钥取个名称，在Key部分将`id_rsa.pub`文件内容添加进去，然后点击“Add SSH key”按钮完成配置。

# 验证是否成功

```bash
ssh -T git@github.com
```

输出以下内容说明配置成功！

```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

# 重新设置origin

删除并重新添加：

```bash
git remote rm origin
git remote add origin git@github.com:username/repository.git
```

查看origin的地址：

```bash
git remote -v
```

输出以下内容，说明已经改为SSH的方式了！

```
origin git@github.com:username/repository.git (fetch)
origin git@github.com:username/repository.git (push)
```

**这样push和pull操作就不用重新登录GitHub了!**