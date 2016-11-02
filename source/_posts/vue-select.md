---
title: 使用vue2.0编写的一个下拉框组件
tags: javascript vue
---

## vue2.0初体验
vue已经大火了很久，尤其是在2.0横空出世之后，加了virtual DOM和ssr（哎？都是ssr）的vue感觉又强大了许多。特此奉上知乎专栏上尤雨溪对于vue2.0的介绍，以及勾股的分析，如果大家感兴趣，可以看一看    
[尤雨溪－Announcing Vue.js 2.0](https://zhuanlan.zhihu.com/p/20814761)    
[勾股－Code Review for Vue 2.0 Preview](http://jiongks.name/blog/code-review-for-vue-next/)

## vue2.0的component
vue2.0官网的guide写的很赞，通俗易懂。    
一般component有两种方式，一种是全局组件(global component)，一种是实例化组件

<!-- more -->

### 全局组件
全局组件通过Vue.component( )注册为一个组件，可以通过<component></component>直接在html中调用
``` bash
<div class="line-basic area" id="area">
    <country-select></country-select>
</div>
```

这种方式不需要在每个页面中实例化，直接相当于dom使用就可以，也可以将某个Vue类注册为组件
``` bash
Vue.component('country-select', {
  template: template,
  data: function() {
    return {
      hideOpts: true,
      isLogin: this.isLogin || false,
      showValue: this.isLogin ? '+86' : '中国+86',
      isOverLength: false,
      options: {}
    }
  }
})
```

### 实例化组件
通过Vue.extend( {} )来创建一个Vue的子类，内部的处理和全局组件类似。在需要使用的时候，通过实例化一个对象再挂载到相应的dom节点上
``` bash
var CountrySelectVue = Vue.extend({
  template: template,
  data: function() {
    return {
      hideOpts: true,
      isLogin: this.isLogin || false,
      showValue: this.isLogin ? '+86' : '中国+86',
      isOverLength: false,
      options: {}
    }
  }
})

// two ways to mount a component
new CountrySelectVue({ el: '#area-content' })
new CountrySelectVue().$mount('#area-content')
```
挂载时，这两种方式都可以，但是这里要注意的是，这里并不是简单挂载在dom节点的子元素，而是替换掉这个dom元素，组件挂载成功后，该元素就不存在了。
