---
layout: page
title: JSON (JavaScript Object Notation)
category: 
description: Learning and Thinking in JSON
---

JSON
====

JSON，JavaScript Object Notation，一种轻量级的文本数据交换格式。

对象object
JSON对象是{string : value, string : value ...}

数组array
JSON数组是[value,value...]

值value
JSON值可以是string、number、object、array、true、false、null

字符串string
JSON字符串，可以看做是C字符串或者Java字符串。

数字number
JSON数字，可以看做是C数字或Java数字，但是八进制和十六进制格式不支持。


示例之一：
```
{
  "name" : { "first" : "Joe", "last" : "Sixpack" },
  "gender" : "MALE",
  "verified" : false,
  "age" : "22"
}
```

