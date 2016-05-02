---
title: exports & Module.exports
date: 2016-05-02 20:30:44
tags: javascript AMD
---
- exports对象是Node中的一个对外接口，其实是通过它为模块闭包提供一个有限的接口，只有exports对象导出的属性或者方法才能够被调用。一般有两种写法：exports.xxx= & module.exports = {}

- exports和module.exports大部分情况下是一样的，但是你可以理解成exports是module.exports的一个引用，如果对module.exports进行改变赋值，exports引用的还是之前的地址。就是不管exports设置了多少属性和方法，一旦module.exports进行了赋值，对外暴露的都是module.exports的内容.

- 不可以通过对 exports 直接赋值代替对 module.exports 赋值。exports 实际上只是一个和 module.exports 指向同一个对象的变量，它本身会在模块执行结束后释放，但 module 不会，因此只能通过指定module.exports 来改变访问接口。

- 可以使用module.exports = hello，这样来改变输出的对象（原来是exports，现在是hello），但不能直接用exports = hello，这样仍然会导出module.exports，也就是exports对象，不会导出hello.
