---
title: react中的extends
date: 2016-11-30 10:37:20
tags: javascript React
---

## extends自定义组件
针对自定义组件，有些场景会使用父子组件的模式，有些场景会使用继承的模式，很多场景两者都可以实现，但是选择使用时，仍然有偏好。
比如下面这两个页面    
![页面1](http://ww1.sinaimg.cn/large/59967359gw1fa9xso3cdnj21kw0tv77y.jpg)

<!-- more -->

![页面2](http://ww2.sinaimg.cn/large/59967359gw1fa9xtczwqxj21kw0ucn1u.jpg)
这两个页面公用相同的接口，只是页面的标题和item的初始化数组不同，拆分页面可以把页面分为图中的几个部分    
![页面结构](http://ww3.sinaimg.cn/large/59967359gw1fa9y7zwo40j21kw0tvth1.jpg)
比较明确的是需要将item元祖抽离成一个组件，但页面主体的items可以通过通用组件的方式实现，也可以通过extends基类的方式实现。   

首先讲一下两种方法的具体实现过程
### 通用组件方式
将页面主体items作为一个通用组件
``` javascript
export default class BaseConfig extends React.Component {
  static propTypes = {
    configData: PropTypes.array.isRequired  // 配置的item信息
  }
  constructor(props) {
    super(props)
    this.state = {
      configs:[]
    }
  }
  // 中间省略了若干方法
  render() {
    return (
      <div>
        { this.state.configs.map(item => <Item key={item.key} item={item} onUpdate={this.updateConfig}  />)}
      </div>
    )
  }
}
```
在页面中可以直接引用组件
``` javascript
export default class Config extends React.Component {
  constructor(props) {
    super(props)
  }
  render() {
    return (
      <div>
        <div><strong>APP各项功能的配置页面。谨慎修改。</strong></div>
        <BaseConfig configData={CONFIG_DATA}></BaseConfig>
      </div>
    )
  }
}
```

### 继承方式
将页面整体定义为一个基类BaseConfig，BaseConfig中不需要props。业务页面只是继承于这个基类，再覆盖title和configData两个属性    
``` javascript
export default class BaseConfig extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      configs:[]
    }
    this.title = ''
    this.configData = []
  }
  // 省略若干方法
  render() {
    return (
      <div>
        <div>{this.title}</div>
        { this.state.configs.map(item => <Item key={item.key} item={item} onUpdate={this.updateConfig}  />)}
      </div>
    )
  }
}
```
``` javascript
class Config extends BaseConfig {
  constructor(props) {
    super(props)
    this.title = 'APP各项功能的配置页面。谨慎修改。'
    this.configData = CONFIG_DATA
  }
}
```

### 结论
最终还是选择了第一种通用组件的方法，extends的继承方式更适用于子类有自己的扩展方法或者需要重写父类定义的同名方法，而此处只需要将初始化items的值传入，并没有子类的自定义方法，没有使用继承方法的必要性，所以最终选用了通用组件的方式
