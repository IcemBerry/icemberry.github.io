---
title: Fastjson反序列化JSON对象时保留null值的key
date: 2018-05-16 11:44:10
categories: 技术
tags:
  - Java
  - Fastjson
  - JSON
---

最近开发中遇到调用第三方Web API的功能，后端在处理JSON数据时使用Fastjson来做反序列化，由于调用API返回的数据格式主体部分过于繁杂且没有太多可抽象的特征，所以只对头部(返回JSON最外层请求状态部分)进行了简单的分割，之后把剩下的主题内容进行数据库存储操作，并将结果返回给前端，由前端根据不同页面再做解析。

<!-- more -->

#### 问题

这样做的好处是，如果未来需求有变动(现在看来变动基本没跑了)，我们不需要重新添加数据库表格、添加针对的实体类，且前端获得原始数据后可以更灵活的展示数据(并不是甩锅，前端也是我写)。

但在开发过程中遇到了后端解析后，部分value为null的数据key直接被Fastjson干掉的情况，导致前端再次解析时遇到undefined的情况。

数据格式细节如下图所示:

![](http://p31evsni6.bkt.gdipper.com/201805160.jpg)

> 注意其中的null数据，如果对应的key在反序列化后被删除，由于无法确定key是否存在，按照固定格式取值会抛出undefined错误，当然也可以在每次取值时都进行undefined判断

查了很多资料，也咨询了几位前辈，得到的结论是，Fastjson在序列化时可以保留null值，闷声发大财，但在反序列化时，没有相关的方法，如果想实现相关方法，只能使用以下几种解决方案:

- 更改Fastjson源码(我至今未找到Fastjson完整文档，放弃了)
- 弃用Fastjson，改用Jackson或Gson

你看，不论如何，总有解决方案，对吧？我充满希望的开始切换到Jackson，在各种错误报一遍之后，我终于意识到，Jackson的反序列化要求对应的实体类与JSON数据节点对节点，根本不适用于当前项目，所以一切又回到了起点。

#### 解决方法

在瞎琢磨了一天后，经前辈指点，想到了一个非常奇幻的方法，就是:

```java
String responseState = HttpRequestUtil.get(fullHttpURL);
responseState = responseState.replaceAll("null","'-'");//responseState为json正文
```

之后再正常调用：

```java
HttpState response = JSONObject.parseObject(responseState, HttpState.class);
```

哇，是不是一切问题都迎刃而解了？ 

我们只需要把json数组在反序列化前，把null替换成"-"即可，这样，文本中便没有了null，自然value为空的key也得以保留，而对这个"-"的处理也可以被前端灵活的处理，再也没有烦人的undefined错误了，爽耶。 