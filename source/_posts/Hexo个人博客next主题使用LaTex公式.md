---
title: Hexo个人博客next主题使用LaTex公式
date: 2020-08-01 00:08:37
tags: ['Hexo']
category: ['Hexo']
typora-root-url: ..
---

# 第一步 更换Hexo默认渲染引擎

hexo 默认的渲染引擎是 marked，但是 marked 不支持 mathjax。所以需要更换Hexo的markdown渲染引擎为hexo-renderer-kramed引擎，后者支持mathjax公式输出。

```bash
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-kramed --save
```

<!--more-->

# 第二步 在你的hexo主题文件夹下的配置文件中激活mathjax

```bash
#文件路径
/blog/themes/next/config.yml

#修改内容
# Math Formulas Render Support
math:
  # Default (true) will load mathjax / katex script on demand.
  # That is it only render those page which has `mathjax: true` in Front-matter.
  # If you set it to false, it will load mathjax / katex srcipt EVERY PAGE.
  per_page: false # 改为false

  # hexo-renderer-pandoc (or hexo-renderer-kramed) required for full MathJax support.
  mathjax:
    enable: true # 改为true
    # See: https://mhchem.github.io/MathJax-mhchem/
    mhchem: true # 改为true

  # hexo-renderer-markdown-it-plus (or hexo-renderer-markdown-it with markdown-it-katex plugin) required for full Katex support.
  katex:
    enable: false
    # See: https://github.com/KaTeX/KaTeX/tree/master/contrib/copy-tex
    copy_tex: false
```

# 第三步 修改kramed语法解释

```javascript
#文件路径
/blog/node_modules/kramed/lib/rules/inline.js 

#修改内容
#只修改了escape/em
var inline = {
  // escape: /^\\([\\`*{}\[\]()#$+\-.!_>])/,
  escape: /^\\([`*\[\]()#$+\-.!_>])/,  # 修改escape
  autolink: /^<([^ >]+(@|:\/)[^ >]+)>/,
  url: noop,
  html: /^<!--[\s\S]*?-->|^<(\w+(?!:\/|[^\w\s@]*@)\b)*?(?:"[^"]*"|'[^']*'|[^'">])*?>([\s\S]*?)?<\/\1>|^<(\w+(?!:\/|[^\w\s@]*@)\b)(?:"[^"]*"|'[^']*'|[^'">])*?>/,
  link: /^!?\[(inside)\]\(href\)/,
  reflink: /^!?\[(inside)\]\s*\[([^\]]*)\]/,
  nolink: /^!?\[((?:\[[^\]]*\]|[^\[\]])*)\]/,
  reffn: /^!?\[\^(inside)\]/,
  strong: /^__([\s\S]+?)__(?!_)|^\*\*([\s\S]+?)\*\*(?!\*)/,
  // em: /^\b_((?:__|[\s\S])+?)_\b|^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
  em: /^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,  # 修改em
  code: /^(`+)\s*([\s\S]*?[^`])\s*\1(?!`)/,
  br: /^ {2,}\n(?!\s*$)/,
  del: noop,
  text: /^[\s\S]+?(?=[\\<!\[_*`$]| {2,}\n|$)/,
  math: /^\$\$\s*([\s\S]*?[^\$])\s*\$\$(?!\$)/,
};
```

# 第四步 在你的markdown文本开头添加语句

```markdown
#添加在开头
mathjax: true
```

![设置mathjax](/images/Hexo%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2next%E4%B8%BB%E9%A2%98%E4%BD%BF%E7%94%A8LaTex%E5%85%AC%E5%BC%8F/%E8%AE%BE%E7%BD%AEmathjax.png)

# 第五步 完成

```bash
hexo clean
hexo g
hexo s
```

# 参考资料

[hexo个人博客next主题使用LaTeX公式](https://www.jianshu.com/p/24a5abb2be98)