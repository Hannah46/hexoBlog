---
title: JS原型链继承中的小玩意
date: 2016-05-02 14:27:32
tags: javascript
---

## 继承方法：

### 1、原型链（主要）

每个构造函数有一个prototype原型对象，prototype指向constructor，constructor中又包含了一个指向prototype的指针（好像有点循环），而实例有一个proto指向prototype。
实现的本质是重写原型对象prototype，原本在SuperType中的属性和方法现在都存在于SubType.prototype中。SubType的原型指向SuperType的实例，SuperType的实例又包含了一个指向prototype的指针（应该是proto）

### 2、最常用的是构造函数和原型链的组合

原型链继承大家公共的方法和属性，构造函数继承实例的方法和属性。用法：
``` bash
function SubType() {
  SuperType.call(this);
}
```
单纯的构造函数的问题就在于方法都在SuperType中定义好了，不能复写了。

<!-- more -->

## 继承后相关变量的变化

看看下面这个代码

``` bash
function SuperType() {
  this.aa = 1;
  this.colors = ['red', 'white'];
}
function SubType() {
  SubType.prototype = new SuperType();
}

var instance1 = new SubType();
instance1.aa = 2;
instance1.colors.push('yellow');

document.write('instance1.aa=' + instance1.aa + '\n' +
                'instance1.colors=' + instance1.colors);

var instance2 = new SubType();
document.write('instance2.aa=' + instance2.aa + '\n' +
                'instance2.colors=' + instance2.colors);
```

这里的输出结果是：
``` bash
instance1.aa=2 instance1.colors=red,white,yellow
instance2.aa=1 instance2.colors=red,white,yellow
```

这里会发现colors改变了，但是aa并没有改变。我们看看instance1和instance2里都有什么东西

![instance内部结构](http://ww3.sinaimg.cn/large/59967359gw1eurz18tbpyj205f028dfp.jpg)

如果我们把instance1的操作改成

``` bash
var instance1 = new SubType();
instance1.aa = 2;
instance1.colors = ['red', 'white', 'yellow'];
```

这里的输出结果是：
``` bash
instance1.aa=2 instance1.colors=red,white,yellow
instance2.aa=1 instance2.colors=red,white,yellow
```

好神奇！colors又不改变了！

再看看这时候两个是啥样子：

![instance内部结构2](http://ww4.sinaimg.cn/large/59967359jw1eurz9khcycj207d03ft8p.jpg)

如果我们再改一下操作
``` bash
var instance1 = new SubType();
SubType.prototype.aa = 2;
```

这里输出结果果然是instance1和instance2的aa值都变成了2。

最后再看看这两个这时候是什么样子：
![instance内部结构2](http://ww4.sinaimg.cn/large/59967359gw1eurz2d69eyj207802mdfs.jpg)

我们终于发现了问题！

继承后创建的实例对于引用类型和基本类型的操作貌似是不太一样的，对于基础类型，不管是赋值操作还是直接＋＋这样的操作，都会在实例中创建一个自己的属性，不会影响到原型，所以也就不用影响到其他的实例。如果想要影响所有实例，必须通过SubType.prototype来修改。

但对于引用类型，如果直接调用引用类型自己的方法，不会创建自己的属性，会通过原型链回溯，因此就修改到了子类型的原型上去了，其他实例也会跟着影响。而如果通过赋值的方式，则会创建一个自己的属性。
