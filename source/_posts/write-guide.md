---
title: Hexo写作指南
date: 2018-06-06 17:32:11
comments: true
description: 本文是Hexo博客写作方面的一些建议，包括了基本标签、RSS优化和一些内置标签，初上手Hexo博客，有不全面不恰当之处还请多多指教。
categories: 指南
tags:
  - Hexo
  - RSS
---
Hexo文章主体使用Markdown格式，辅之以"Front-matter"做一些基础的博文配置，Markdown格式在这里不会过多提及，对该格式不是很理解的同志可以访问[这里](https://github.com/guodongxiaren/README)查看详细指南。本篇主要讲一些"Front-matter"的含义和基本使用。

#### Front-matter
**Front-matter**是指每篇Hexo博客内MD格式文档头部的一串配置代码，他以`---`开头、结尾，以本篇指南为例：
```Markdown
---
title: Hexo写作指南
date: 2018-06-06 17:32:11
comments: true
description: 本文是Hexo博客写作方面的一些建议，包括了基本标签、RSS优化和一些内置标签，初上手Hexo博客，有不全面不恰当之处还请多多指教。
categories: 指南
tags:
  - Hexo
  - RSS
---
```
> 本篇博文的Front-matte

代码 | 用途
---- | ---
title | 文章标题
date | 编写时间（自动生成）
comments | 是否允许评论（不填写默认为允许）
description | 文章摘要，作用于主页与RSS
categories | 文章分类，含此配置会被"分类"页面收录至相关分类，每篇文章仅允许一个分类
tags | 文章标签，含此配置会被"标签"页面收录至相关分类，每篇文章允许多个标签

其中`categories`与`tags`在同一篇文章里可以同时出现，`description`可以使用Hexo内置标签取代

