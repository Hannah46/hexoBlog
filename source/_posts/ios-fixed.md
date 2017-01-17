---
title: ios下微信浏览器fixed布局与软键盘冲突问题
date: 2017-01-15 21:17:25
tags: 移动端
---

这两天在做一个活动页，页面中有一个fixed布局的底部banner和一个input，点击input唤起软键盘进行输入。但开发过程中发现，当软键盘被唤起时，会出现fixed布局的banner出现在页面的中部，而不是按照fixed的表现紧贴在软键盘上部，同时滑动下部的页面，banner也会跟着滑动，也就是说fixed布局失效了      
查阅了一些资料以后发现，ios下确实存在这个问题，android下不会出现，确实ios在处理fixed布局上存在问题，[百度efe的这篇文章](http://efe.baidu.com/blog/mobile-fixed-layout/) 里也提到，不只是type=text的input，其他会唤起软键盘的元素(时间日期选择、select选择等)，都会出现同样的问题。   
主要的解决方案如下：    
#### 方案1: 在唤起软键盘时，隐藏所有fixed的元素    
分别监听focus和blur事件，在唤起软键盘的情况下隐藏fixed元素  

<!-- more -->

```javascript
$phone.addEventListener('focus', function(e) {
  this.fixedVisible = false
})
$phone.addEventListener('blur', function(e) {
  this.fixedVisible = true
})
```
但是这种方式只适用于底部的banner这种会被软键盘遮挡住的部位，对于其他一些必须始终显示的fixed元素（例如顶部banner）就不适用。    
#### 方案2: 包含fixed的元素不出现滚动，只让页面主体的部分出现滚动，页面主体进行绝对定位，内部进行滚动
```html
<body class="layout-scroll-fixed">
    <!-- fixed定位的头部 -->
    <header>

    </header>

    <!-- 可以滚动的区域 -->
    <main>
        <div class="content">
        <!-- 内容在这里... -->
        </div>
    </main>

    <!-- fixed定位的底部 -->
    <footer>
        <input type="text" placeholder="Footer..."/>
        <button class="submit">提交</button>
    </footer>
</body>
```
```css
header {
    position: fixed;
    height: 50px;
    left: 0;
    right: 0;
    top: 0;
}

footer {
    position: fixed;
    height: 34px;
    left: 0;
    right: 0;
    bottom: 0;
}

main {
    /* main绝对定位，进行内部滚动 */
    position: absolute;
    top: 50px;
    bottom: 34px;
    /* 使之可以滚动 */
    overflow-y: scroll;
    /* 增加该属性，可以增加弹性 */
    -webkit-overflow-scrolling: touch;
}

main .content {
    height: 2000px;
}
```
这样就可以实现main的内部内容滚动，fixed元素不会跟随滚动，软键盘也不会影响到fixed布局    
#### 方案3: 适用iScroll
这是一个第三方的库，适用方法之后再补充
