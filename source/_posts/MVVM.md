---
title: MVVM
date: 2016-12-09 23:24:00
tags: MVVM
---
# 概述
![](http://ohugkp1mj.bkt.clouddn.com/mvvm.jpg)
+ MVVM 框架 有几大优点
1. 低耦合。视图（View）可以独立于Model变化和修改，一个ViewModel可以绑定到不同的"View"上，当View变化的时候Model可以不变，当Model变化的时候View也可以不变。
2. 可重用性。你可以把一些视图逻辑放在一个ViewModel里面，让很多view重用这段视图逻辑。
3. 独立开发。开发人员可以专注于业务逻辑和数据的开发（ViewModel），设计人员可以专注于页面设计，使用Expression Blend可以很容易设计界面并生成xml代码。
4. 可测试。界面素来是比较难于测试的，而现在测试可以针对ViewModel来写
<!-- more -->
## 架构
1. Vue
2. WPF MvvmLight
