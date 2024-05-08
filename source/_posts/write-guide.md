---
title: Hexo写作指南
date: 2018-06-06 17:32:11
categories:
- 指南
tags:
- Hexo
language: zh-CN
toc: true
---
Hexo文章主体使用Markdown格式，辅之以"Front-matter"做一些基础的博文配置，Markdown 格式在这里不会过多提及，如果你对该格式不是很理解请定位到最后一小节查看详细指南。本篇主要讲一些"Front-matter"的含义和基本使用。

<!-- more -->

### Front-matter

**Front-matter**是指每篇 Hexo 博客内 Markdown 格式文档头部的一串配置代码，他以`---`开头、结尾，以本篇指南为例：
```text
---
title: Hexo写作指南
date: 2018-06-06 17:32:11
categories:
- 指南
tags:
- Hexo
language: zh-CN
toc: true
---
````
> 本篇博文的Front-matte

在这篇文章中涉及的 Hexo Front-matter 具体有以下用途:

代码 | 用途
---- | ---
title | 文章标题
date | 编写时间（如不声明，可自动生成）
categories | 文章分类，含此配置会被"分类"页面收录至相关分类，每篇文章仅允许一个分类
tags | 文章标签，含此配置会被"标签"页面收录至相关分类，每篇文章允许多个标签
language | 文章语言，在文章存在多语言版本时可以进行分类
toc | 是否开启文内目录

_*常用 Front-matte 用途对照_

### 摘要信息

设置摘要信息请使用Hexo内置标签`<!-- more --> `，其使用方法如下:

````text
----
Front-matter
----
摘要
<!-- more -->
正文
````

### Tag Plugin 

除了上节介绍的`<!-- more --> `内置标签外，Hexo还有很多内置标签，他们提供了各种实用功能，在这里我给出相关的官方文档，如果有兴趣的话可进一步了解:

- [Hexo 标签插件（Tag Plugins）](https://hexo.io/zh-cn/docs/tag-plugins.html)
- [hexo-component-inferno](https://github.com/ppoffice/hexo-component-inferno)

### Markdown

正文部分的书写为严格匹配的 Markdown 格式，其基本语法如下：
```Markdown
# 一级标题
## 二级标题
### 三级标题
#### 四级标题

- 无序列表1
- 无序列表2
- 无序列表3

1. 有序列表1
2. 有序列表2
3. 有序列表3

![图片](http://图片地址 "图片描述")

[链接](http://链接地址 "悬停显示")

> 引用

`文本高亮`

**文本加粗**

*斜体*

~~删除线~~
```
更多更详细的语法说明请参见 [Markdown 基本语法](https://github.com/younghz/Markdown)

