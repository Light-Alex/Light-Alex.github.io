---
title: Typora插入图片
date: 2020-07-19 10:28:33
tags: ['Typora']
categories: ['Typora']
typora-root-url: ..
---

# Typora插入图片配置

首先在 `hexo > source`目录下建一个文件夹叫images，用来保存博客中的图片。每篇博客建立相应的文件夹，将这篇博客的图片放入对应的文件夹中。例如将这篇博客的图片放入`hexo > source > images > Typora插入图片`文件夹中。

打开Typora的 `文件 > 偏好设置`，进行如下设置。

<!--more-->

![图象偏好设置](/images/Typora%E6%8F%92%E5%85%A5%E5%9B%BE%E7%89%87/%E5%9B%BE%E8%B1%A1%E5%81%8F%E5%A5%BD%E8%AE%BE%E7%BD%AE.png)

但是仅仅这样设置还不够，这样设置在Typora中倒是能看图片了，但是使用的却是相对于当前md文件的相对路径，可是如果启动hexo，是要用服务器访问的，而服务器显然无法根据这个相对路径正确访问到图片，因此还需要在Typora中进行进一步设置。

在Typora菜单栏点击 `格式->图像->设置图片根目录`，将`hexo/source`作为其根目录。

**一定要先设置了图片根目录后再插入图片，否则图片路径会不正确喔！**

