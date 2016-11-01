---
title: JS中4种function调用方法
date: 2016-05-02 20:55:16
tags: javascript
---

JS中函数调用时会接收两个参数，this & arguments，有4种调用方式，四种方式的区别在于this不同。

## 方法模式
将函数作为一个对象的属性，即为一个方法。调用时，通过对象来调用。这里的this是该对象。

![call1](http://ww2.sinaimg.cn/large/59967359gw1f3hc778u1lj207g042aa2.jpg)

![call1 result](http://ww4.sinaimg.cn/large/59967359gw1f3hc7wbac6j204v01rq2u.jpg)

## 函数模式
直接调用某个函数，this是全局对象window

![call2](http://ww2.sinaimg.cn/large/59967359gw1f3hcak6o62j2088049q30.jpg)

![call2 result](http://ww3.sinaimg.cn/large/59967359gw1f3hcbpxgjrj20840443z5.jpg)

<!-- more -->

## 构造函数模式
用new func()的方式来调用函数，实际是创建了一个连接到该函数的prototype成员的新对象。这里的this是函数自己。

![call3](http://ww2.sinaimg.cn/large/59967359gw1f3hccz50sqj208v05174i.jpg)

![call3 result](http://ww2.sinaimg.cn/large/59967359gw1f3hcdj050zj207h02hjrg.jpg)

## Apply调用模式
也可以用call，参数不同而已，apply是数组，call是一个逐个列出
apply允许设置this，即使对象并没有这个方法，仍然可以通过apply指定作用域，然后在某个对象上调用这个方法。
