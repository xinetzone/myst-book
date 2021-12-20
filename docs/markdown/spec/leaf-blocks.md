# 叶块

本节描述组成 Markdown 文档的不同类型的叶块。

## 主题断点

由最多三个缩进空格组成的行，后跟三个或更多匹配的 `-`、`_` 或 `*` 字符序列，每个字符后跟任意数量的空格或制表符，形成一个主题断点。

````````````````````````````````
***
---
___
````````````````````````````````

错误的字符：

````````````````````````````````
+++
````````````````````````````````


````````````````````````````````
===
````````````````````````````````

没有足够的字符：

````````````````````````````````
--
**
__
````````````````````````````````

最多允许三个空格的缩进：

````````````````````````````````
 ***
  ***
   ***
````````````````````````````````


缩进的四个空格太多了：

````````````````````````````````
    ***
````````````````````````````````

````````````````````````````````
Foo
    ***
````````````````````````````````

可以使用超过三个字符：

````````````````````````````````
_____________________________________
````````````````````````````````
