---
title: Hexo博客本地文件迁移到其他电脑
date: 2020-07-19 09:55:09
tags: ['Hexo']
categories: ['Hexo']
typora-root-url: ..
---

# Github分支结构

<img src="/images/Hexo%E5%8D%9A%E5%AE%A2%E6%9C%AC%E5%9C%B0%E6%96%87%E4%BB%B6%E8%BF%81%E7%A7%BB%E5%88%B0%E5%85%B6%E4%BB%96%E7%94%B5%E8%84%91/github%E5%88%86%E6%94%AF%E7%BB%93%E6%9E%84.png" alt="github分支结构" style="zoom:67%;" />

<!--more-->

完成Hexo本地运行后，会在本地文件里生成一个`public`文件夹。`public`文件夹内是根据`.md`生成的`html`文件，也就博客的静态文件。

通常情况下，我们执行：

```bash
hexo d
```

就是把`public`文件夹下的文件同步到`github`，然后就能通过[https://username.github.io/](https://links.jianshu.com/go?to=https%3A%2F%2Flink.zhihu.com%2F%3Ftarget%3Dhttps%3A%2F%2Fkokofe.github.io%2F)访问博客了。所以，我们的思路其实就是把`静态文件`和`Hexo环境`，分别存在`username.github.io`的`master`和`hexo`分支上。

# 需要转移的文件

| 文件(夹)     | 说明                 |
| :----------- | :------------------- |
| scaffolds/   | 博客文章模板         |
| source/      | 所有的博客文章       |
| themes/      | 网站主题             |
| .gitignore   | push时需忽略的文件   |
| _config.yml  | 站点配置文件         |
| package.json | 依赖包的名称和版本号 |

# .gitignore文件配置

在Hexo根目录下存在`.gitignore`文件，用来忽略一些不需要的文件，如果没有的话，自己新建一个，在里面写上如下，表示这些类型文件不需要git：

```
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
```

# 删除主题文件中的版本控制文件(.git)

如果你之前克隆过themes中的主题文件，那么应该把主题文件中的`.git`文件夹删掉，因为git不能嵌套上传，最好是显示隐藏文件，检查一下有没有，否则上传的时候会出错，导致你的主题文件无法上传，这样你的配置在别的电脑上就用不了了。例如，需要删除以下文件。

![主题文件版本控制文件删除](/images/Hexo%E5%8D%9A%E5%AE%A2%E6%9C%AC%E5%9C%B0%E6%96%87%E4%BB%B6%E8%BF%81%E7%A7%BB%E5%88%B0%E5%85%B6%E4%BB%96%E7%94%B5%E8%84%91/%E4%B8%BB%E9%A2%98%E6%96%87%E4%BB%B6%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6%E6%96%87%E4%BB%B6%E5%88%A0%E9%99%A4.png)

如果clone了一些插件，这些插件的.git文件也需要删除，使用以下命令可以删除主题文件中的所有.git文件(以next主题为例)：

**Linux:**

```bash
find themes/next -name ".git" | xargs rm -f
```

# 创建分支

初始化一个新本地仓库，它在工作目录下生成一个名为.git的隐藏文件夹(如果初始化过了就不用再初始化了)。

```bash
git init
```

创建名为hexo的分支

```bash
git checkout -b hexo
```

保存所有文件到暂存区

```bash
git add --all
```

提交变更

```bash
git commit -m "Hexo本地文件"
```

设置远程仓库的地址

```bash
git remote add origin git@github.com:username/username.github.io.git
```

推送分支到github，并用–set-upstream与origin创建关联，将hexo设置为默认分支

```bash
git push --set-upstream origin hexo
```

在这个仓库的settings中，选择默认分支为hexo分支（这样每次同步的时候就不用指定分支，比较方便）。

![切换github默认分支](/images/Hexo%E5%8D%9A%E5%AE%A2%E6%9C%AC%E5%9C%B0%E6%96%87%E4%BB%B6%E8%BF%81%E7%A7%BB%E5%88%B0%E5%85%B6%E4%BB%96%E7%94%B5%E8%84%91/%E5%88%87%E6%8D%A2github%E9%BB%98%E8%AE%A4%E5%88%86%E6%94%AF.png)

# 迁移Hexo到新电脑

在任意文件夹下，将GitHub上的Hexo文件克隆下来

```bash
git clone git@github.com:username/username.github.io.git
```

进入克隆下来的文件夹，并安装依赖以及git部署插件

```bash
cd xxx.github.io
npm install
npm install --save hexo-deployer-git
```

然后就可以开始写新博客了！

```bash
hexo n "xxx"
```

注意：需要使用git push把源文件推到分支上

```bash
git add .
git commit -m "xxxx"
git push origin hexo
```

```bash
hexo clean # 清理
hexo g # 生成
hexo d # 发布文章, 发布到GitHub上
```

**可选插件**

支持`本地搜索`功能：

```bash
npm install hexo-generator-searchdb --save
```

支持`网站字数`和`阅读时长`统计功能：

```bash
npm i --save hexo-wordcount
```

支持相关文章推荐功能：

```bash
npm install hexo-related-popular-posts --save
```

支持快速连接技术：通过在空闲时间预取视区内链接来加快后续页面加载速度

```bash
npm install --save quicklink
```

**参考链接：**

<https://www.jianshu.com/p/153490a029a5>

<https://fl4g.cn/2018/08/03/Hexo博客迁移到其他电脑/>