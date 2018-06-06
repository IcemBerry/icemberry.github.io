---
title: JS下多层级JSON数据格式化
date: 2018-05-16 11:07:10
categories: 技术
tags:
  - JS
  - JSON
---
最近开发的项目中涉及到调用Web API并在前台解析的功能需求，Web API返回的数据只有极小部分有用，所以在解析后还需进行数据清洗，之后再调用其他组件再处理。遇到的问题不算复杂，但刚接触JS，还是稍微绕了一些弯路才找到解决方法。

<!-- more -->

#### 开发问题

Web API返回的数据如下: 

```json
{
    "Node": {
        "name": "北京XX科技有限公司",
        "KeyNo": "4659626b1e5e43f1bcad8c268753216e",
        "Category": 1,
        "ShortName": "北京XX",
        "Count": 28,
        "Level": 0,
        "Children": [
            {
                "name": "股东",
                "KeyNo": null,
                "Category": 3,
                "ShortName": "股东",
                "Count": 5,
                "Level": 0,
                "Children": [
                    {
                        "name": "王XX",
                        "KeyNo": "4659626b1e5e43f1bcad8c268753216e_王XX",
                        "Category": 3,
                        "ShortName": "自然人股东",
                        "Count": 0,
                        "Level": 1,
                        "Children": null
                    },
                    {
                        "name": "张XX",
                        "KeyNo": "4659626b1e5e43f1bcad8c268753216e_张XX",
                        "Category": 3,
                        "ShortName": "自然人股东",
                        "Count": 0,
                        "Level": 1,
                        "Children": null
                    }
                ]
            },
            {
                "name": "对外投资",
                "KeyNo": null,
                "Category": 2,
                "ShortName": "对外投资",
                "Count": 23,
                "Level": 0,
                "Children": [
                    {
                        "name": "北京XXX科技有限公司",
                        "KeyNo": "afb3daf1797df272997f22b143c964f6",
                        "Category": 2,
                        "ShortName": "北京XXX",
                        "Count": 0,
                        "Level": 1,
                        "Children": null
                    },
                    {
                        "name": "北京XXX科技有限公司",
                        "KeyNo": "274d9311979595d54821e1d9e8d73e36",
                        "Category": 2,
                        "ShortName": "北京XXX",
                        "Count": 0,
                        "Level": 1,
                        "Children": null
                    }
                ]
            }
        ]
    }
}
```



后期需要调用组件需求的数据结构如下:

```json
{
    "name": "北京XX科技有限公司",
    "children": [
        {
            "name": "股东",
            "children": [
                {
                    "name": "王XX",
                    "value": "自然人股东"
                },
                {
                    "name": "张XX",
                    "value": "自然人股东"
                }
            ]
        },
        {
            "name": "对外投资",
            "children": [
                {
                    "name": "北京XXX",
                    "value": "北京XXX科技有限公司"
                },
                {
                    "name": "北京XXX",
                    "value": "北京XXX科技有限公司"
                }
            ]
        }
    ]
}
```



以上展示的数据仅有两层，但在实际应用中，数据层级可能达到4-6层，由于是初次做前端JSON解析，网络上相关资料也很稀少，所以刚开始写起来还是很费劲的，但经过两小时摸索后，成功写出了解析方法，所以简单记录以下，希望对大家有所帮助。



#### 解析思路

首先，JSON的最外层节点可以确定只有两个，即股东与投资人，所以这部分可以直接固定写好，像是这样：

```javascript
var json = {};
    json.name = name;
    json.children = [];//children作为数组需要提前声明，否则会抛出undefined，下同
    json.children[0] = {};
    json.children[0].name = "股东";
    json.children[0].children = [];
    json.children[1] = {};
    json.children[1].name = "对外投资";
    json.children[1].children = [];
```

之后开始遍历第一层子节点:

```javascript
for (var i = 0; i < gudong.length; i++) {
        //level1
        json.children[0].children[i] = {};
        json.children[0].children[i].name = gudong[i].name;
        json.children[0].children[i].value = gudong[i].ShortName;
```

此时准备便利第二层子节点，但在遍历二层子节点时需要注意判断二层子节点是否存在:

```javascript
if (gudong[i].Children != null) {
            json.children[0].children[i].children = []; //注意事先声明children类型，下同
            for (var ii = 0; ii < gudong[i].Children.length; ii++) {
                //level2
                json.children[0].children[i].children[ii] = {};
                json.children[0].children[i].children[ii].name = gudong[i].Children[ii].name;
                json.children[0].children[i].children[ii].value = gudong[i].Children[ii].ShortName;
```

之后用同样的方法便利第3、4层:

```javascript
if (gudong[i].Children[ii].Children != null) {
                    json.children[0].children[i].children[ii].children = [];
                    for (var iii = 0; iii < gudong[i].Children[ii].Children.length; iii++) {
                        //level3
                        json.children[0].children[i].children[ii].children[iii] = {};
                        json.children[0].children[i].children[ii].children[iii].name = gudong[i].Children[ii].Children[iii].name;
                        json.children[0].children[i].children[ii].children[iii].value = gudong[i].Children[ii].Children[iii].ShortName;
                        if (gudong[i].Children[ii].Children[iii].Children != null) {
                            json.children[0].children[i].children[ii].children[iii].children = [];
                            for (var iiii = 0; iiii < gudong[i].Children[ii].Children[iii].Children.length; iiii++) {
                                //level4
                                json.children[0].children[i].children[ii].children[iii].children[iiii] = {};
                                json.children[0].children[i].children[ii].children[iii].children[iiii].name = gudong[i].Children[ii].Children[iii].Children[iiii].name;
                                json.children[0].children[i].children[ii].children[iii].children[iiii].value = gudong[i].Children[ii].Children[iii].Children[iiii].ShortName;
                            }
                        }
                    }
                }
```

JS做JSON解析与数据清洗很繁复，尤其涉及到这种多层复杂数据时更为明显，但只要足够谨慎和耐心，还是可以很顺利写出令人满意的方法的，以上就是我的解决方法，其实在第一层没有必要固定写，在实际使用中已经做出了修正，在这里就不再贴出。



#### 完整解决方法

```javascript
function formatJSON(data) {
    data = data.Node;
    ////level0
    var json = {};
    json.name = data.name;
    json.value = data.ShortName;
    if (data.Children != null) {
        json.children = [];
        for (var i = 0; i < data.Children.length; i++) {
            //level1
            json.children[i] = {};
            json.children[i].name = data.Children[i].name;
            json.children[i].value = data.Children[i].ShortName;
            if (data.Children[i].Children != null) {
                json.children[i].children = [];
                for (var ii = 0; ii < data.Children[i].Children.length; ii++) {
                    //level2
                    json.children[i].children[ii] = {};
                    json.children[i].children[ii].name = data.Children[i].Children[ii].name;
                    json.children[i].children[ii].value = data.Children[i].Children[ii].ShortName;
                    if (data.Children[i].Children[ii].Children != null) {
                        json.children[i].children[ii].children = [];
                        for (var iii = 0; iii < data.Children[i].Children[ii].Children.length; iii++) {
                            //level3
                            json.children[i].children[ii].children[iii] = {};
                            json.children[i].children[ii].children[iii].name = data.Children[i].Children[ii].Children[iii].name;
                            json.children[i].children[ii].children[iii].value = data.Children[i].Children[ii].Children[iii].ShortName;
                            if (data.Children[i].Children[ii].Children[iii].Children != null) {
                                json.children[i].children[ii].children[iii].children = [];
                                for (var iiii = 0; iiii < data.Children[i].Children[ii].Children[iii].Children.length; iiii++) {
                                    //level4
                                    json.children[i].children[ii].children[iii].children[iiii] = {};
                                    json.children[i].children[ii].children[iii].children[iiii].name = data.Children[i].Children[ii].Children[iii].Children[iiii].name;
                                    json.children[i].children[ii].children[iii].children[iiii].value = data.Children[i].Children[ii].Children[iii].Children[iiii].ShortName;
                                    if (data.Children[i].Children[ii].Children[iii].Children[iiii].Children != null) {
                                        json.children[i].children[ii].children[iii].children[iiii].children = [];
                                        for (var iiiii = 0; iiiii < data.Children[i].Children[ii].Children[iii].Children[iiii].Children.length; iiiii++) {
                                            //level5
                                            json.children[i].children[ii].children[iii].children[iiii].children[iiiii] = {};
                                            json.children[i].children[ii].children[iii].children[iiii].children[iiiii].name = data.Children[i].Children[ii].Children[iii].Children[iiii].Children[iiiii].name;
                                            json.children[i].children[ii].children[iii].children[iiii].children[iiiii].value = data.Children[i].Children[ii].Children[iii].Children[iiii].Children[iiiii].ShortName;
                                            if (data.Children[i].Children[ii].Children[iii].Children[iiii].Children[iiiii].Children != null) {
                                                json.children[i].children[ii].children[iii].children[iiii].children[iiiii].children = [];
                                                for (var iiiiii = 0; iiiiii < data.Children[i].Children[ii].Children[iii].Children[iiii].Children[iiiii].Children.length; iiiiii++) {
                                                    //level6
                                                    json.children[i].children[ii].children[iii].children[iiii].children[iiiii].children[iiiiii] = {};
                                                    json.children[i].children[ii].children[iii].children[iiii].children[iiiii].children[iiiiii].name = data.Children[i].Children[ii].Children[iii].Children[iiii].Children[iiiii].Children[iiiiii].name;
                                                    json.children[i].children[ii].children[iii].children[iiii].children[iiiii].children[iiiiii].value = data.Children[i].Children[ii].Children[iii].Children[iiii].Children[iiiii].Children[iiiiii].ShortName;
                                                }
                                            }
                                        }
                                    }
                                }
                            }
                        }
                    }
                }
            }
        }
    }
    console.log("format: " + JSON.stringify(json));
}
```

