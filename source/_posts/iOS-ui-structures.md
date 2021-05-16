---
title: iOS APP界面层的结构(MVC、MVP、MVVM)
date: 2021-05-17 00:16:56
tags:
- iOS
description:  APP的界面层的结构指的是平常所说的MVC、MVVM这些。整个APP一般用的还是3层结构：界面层、服务层、数据层。
---
APP的界面层的结构指的是平常所说的`MVC`、`MVVM`这些。
> 整个APP的话，一般用的还是3层结构：界面层、服务层、数据层。

### 标准的MVC

![MVC结构](/images/MVC结构.png)

当`Controller`从`Service`得到新的数据后，把数据更新给`View`。注意，`View`是完全不知道`Model`的。

### 改良的MVC

这个`ViewModel`是指`View`的数据模型，跟`MVVM`的`ViewModel`是不一样的。只是为了把`View`需要的数据**结构化**。

![改良的MVC结构](/images/改良的MVC结构.png)

### MVP

`Controller`持有多个`Presenter`，每个`Presenter`相当于上面的`MVC`或`改良的MVC`。  
这样做可以防止`Controller`过于臃肿，也可方便界面的模块组合，以及可能的复用。

![MVP结构](/images/MVP结构.png)

### MVVM

强调**动态绑定**，以使用`KVOController`为例。
`ViewModel`把`view`需要的数据作为属性开放出来，`view`利用`KVOController`来监听这些属性。`ViewModel`的这些属性修改时，`view`的数据跟着改变。

![MVVM结构](/images/MVVM结构.png)