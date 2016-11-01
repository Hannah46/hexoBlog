---
title: React初体验
tags: javascript React 
---

### components are just state machines
components只是 承载state的容器，通过state的变化来实现components的渲染

### ownership & 父子关系
ownership：props和state是根据这种关系来传递数值，如果一个component在另一个component的render方法里定义，两者就存在ownership ，render中传递的值是当前定义的元素的props中
     父子关系是指在类似dom树结构的两个元素
``` bash
<Parent>
	<Child />
</Parent>
```
在这种关系里，parent可以通过this.props.children来获取children 的 prop，也可以用 React.Children的相关操作对children进行操作

### react dom操作的方式
react在操作dom上充分考虑了性能上的优化，比如
``` bash
<Card>
     <p>Paragraph 1</p>
     <p>Paragraph 2</p>
</Card>
```
如果想要删除第一个p，react不会直接destory这个元素，因为这样会触发下面所有元素的repaint；react采用的方式是destory最后一个元素，然后改变上面所有元素的值。这样的做法只需要一次render，改变值的操作只是一个repaint的方式。尤其是对于list，n个元素只需要做1次render和n-1次repaint。

但是有一种情况，就是当子元素的值是保存在state中的，这里最好不要采用destory的方式，因为当state发生改变时，会强制触发整个页面重新渲染，所以这时推荐的方式是将想要删除的元素display:none
``` bash
<Card>
     <pstyle={{display:'none'}}>Paragraph 1</p>
     <p>Paragraph 2</p>
</Card>
```

当元素是一个list生成时，如果需要操作list中的元素，同时又要保存state中的数据，最好给每个元素都附上一个key值，React在遇到设置了key值的元素时，会重新安排或者直接销毁。注意key值不要写在render内的html元素上，而是写在owner的render方法里
``` bash
// WRONG!
varListItemWrapper=React.createClass({
     render:function(){
          return<li key={this.props.data.id}>{this.props.data.text}</li>;
     }
});
var ListItemWrapper = React.createClass({
     render:function(){
          return<li>{this.props.data.text}</li>;
     }});
     varMyComponent=React.createClass(
          {render:function(){return(
               <ul>{this.props.results.map(function(result){
                     return<ListItemWrapperkey={result.id}data={result}/>;
     })}
     </ul>);
}});
```

### props
  owner组件与owned组件之间数据传输，是一种单向绑定。owned组件通过states来接收props传递的值    
  如果需要判断是否改变数据，重写shouldComponentUpdate(),return false的时候不会做组件更新
尤其当数据量较大的时候，使用shouldComponentUpdate( )可以提升性能。   
注意：
- 初始化的render过程和调用forceUpdate( )时，都不会再调用shouldComponentUpdate()
当调用shouldComponentUpdate( )，元素的componentWillUpdate and componentDidUpdate不会调用。  
     componentWillUpdate，当props或者state改变时，立刻触发，before rendering
     componentDidUpdate，当组件更新时，改变Dom节点  
这些都是生命周期中的方法  
- shouldComponentUpdate( )存在数据的一致性问题，如果数据已经改变，但方法返回false，ui组件仍然不会同步更新，所以需要谨慎使用。

### 组件中可定义的方法
- render：required，调用时会检查this.props & this.state，返回一个单一的子元素。
- getInitialState：组件被添加前调用，返回值作为this.state的初始值
- getDefaultProps：class被创建时调用一次，之后被缓存，如果父组件未初始化props，则返回值作为props的初始值。因此在该方法调用时，取不到this.props，同时方法返回的复杂对象在实例之间是共享的，而不是复制的
- propTypes：检测传递给组件的props的数据类型
[propTypes](https://facebook.github.io/react/docs/reusable-components.html)
- mixins：不同的组件之间共享的设置，是一个array  mixins: [SetIntervalMixin]
- statics：class可以直接调用的静态方法，与props&state无关，实例创建之前即可调用
- displayName：用于debug，其实是JSX转化的时候使用

### 生命周期（这个对于React来说相当重要）
1、Mount    
     componentWillMount：client & server均执行。render方法调用前立刻触发，setState在这个方法中调用，如果设置了setState，render会接收到更新的state值，然后只调用一次   
     componentDidMount：only on client。render方法调用后立刻触发，在这个方法里获取refs绑定的子元素，调用过程中子元素先于父元素调用。引入其他js框架，设置计时器，发送ajax请求都在这个方法中做  
2、Update(初始化的render过程都不会触发)  
     componentWillReceiveProps：接收新的props调用，可能的调用场景：父元素通过setState改变子元素的props时调用，所有因为props改变而引发的操作都在这个方法里
props的旧值通过this.props调用，这时还没有完成改变的动作。  
当props发生改变时会触发这个方法，但是并不可以在这个方法中检测props是否发生了改变，这并不是一个相互的过程。如果需要检测state的变化，可以用componentWillUpdate  
     shouldComponentUpdate：接收到新的props和state之后，在render之前触发。
当props和state改变不需要刷新组件时，return false，render( )不会调用，componentWillUpdate和componentDidUpdate也都不会触发   
     componentWillUpdate：接收到新props和states之后立刻触发，此时还未执行render
方法内不能使用setState( )，如果需要根据新的props修改state，需要在componentWillReceiveProps里   
     componentDidUpdate：组件的变化传递到dom时立刻触发，这里可以执行DOM上的一些操作  
     componentWillUnmount：组件从DOM中移除前立刻触发  

### 与ES6结合
- 使用ES6 create class
``` bash
class HelloMessage extends React.Component {
     render(){
          return<div>Hello{this.props.name}</div>;
     }
}
ReactDOM.render(<HelloMessagename="Sebastian"/>,mountNode);
```
这样引用也可以使用ES6的import   
- 使用ES6创建的组件，不会自动绑定this，使用时
``` bash
<div onClick={this.tick.bind(this)}>
```
也可以在construtor里面使用.bind(this)，这样就可以在dom中直接使用this.xxx    
- … 扩展操作符
es6中的... 可以将一个对象展开，拆成若干个用，隔开的数据，外层包一个[ ]  则转化为了数组，外层包一个{ }则转化了对象   
可以使用...来控制props中传递的值   
var { checked, ...other } = props;   
这样props中就不会包括checked属性，如果又想要传递给内部的元素，可以使用显示的传递
``` bash
<input {...other}
     checked={checked}
     className={fancyClass}
     type="checkbox"
/>
```
- => 箭头操作符
相当于.bind(this)

### virtual DOM
- react中类似DOM中的东西（render 中构建的）都是virtual DOM，react会有一个虚拟试图（react browser），用来呈现virtual DOM，所有的更新都发生在virtual DOM上，之后react会采取效率最高的方式将virtual      DOM更新到真正的DOM上。react不直接操作DOM，所以效率很快   
- 如果想要取到真正的DOM，可以给dom指定ref值    
ref既可以绑定到真正的DOM节点上，返回一个DOM节点，也可以绑定在一个react组件上，返回的是一个React class。常用的场景是查找DOM    
ref也可以是一个回调函数，当组件被挂载（mount）到dom节点中时立刻触发，被标记了ref的dom节点作为回调函数的参数  
``` bash
render: function() {
    return (
      <TextInput
        ref={function(input) {
          if (input != null) {
            input.focus();
          }
        }} />
    );
  },
```

也可以是个string，标识整个组件的backing instance
``` bash
<input ref="myInput" />
var input = this.refs.myInput;
var inputValue = input.value;
var inputRect = input.getBoundingClientRect();
```
注意：ref不要使用在render方法中
  ref其实指向的是backing instance，所以不能给stateless的组件标记ref，因为stateless不存在backing instance

### tips
1、尽量不要在getInitialState中使用this.props  
如果这样，可能会导致render中的this.state还没有拿到getInitialState中的值，导致数据不一致   
最佳做法是直接在render中调用this.props，而不要 使用this.state   
``` bash
// bad
var MessageBox = React.createClass({
     getInitialState:function(){
          return{ nameWithQualifier:'Mr. '+this.props.name};
     },
     render:function(){
          return <div>{ this.state.nameWithQualifier}</div>;
     }
});
ReactDOM.render(<MessageBoxname="Rogers"/>,mountNode);
// better
var MessageBox = React.createClass({
     render:function(){
          return <div>{ 'Mr. '+this.props.name }</div>;
     }
});
ReactDOM.render(<MessageBoxname="Rogers"/>,mountNode);
```
2、this.props.children       当有多个子组件时，会返回一个array，但当只有一个的时候，不会是一个array    
3、如果在初始化组件时，是通过ajax请求拿到的数据，要在组件unmount的时候终止所有的请求    
``` bash
componentDidMount: function() {
     this.serverRequest=$.get(this.props.source,function(result){
          var lastGist=result[0];
          this.setState({
               username:lastGist.owner.login,
               lastGistUrl:lastGist.html_url
          });
     }.bind(this));
},
componentWillUnmount:function(){
     this.serverRequest.abort();
},
```
4、通过给子元素指定ref，可以直接在父元素中通过this.refs.(ref的值)取到子元素，然后直接调用子元素中的方法    
![](http://ww3.sinaimg.cn/large/59967359gw1f802xei8ozj20xo0aktay.jpg)
![](http://ww2.sinaimg.cn/large/59967359gw1f802xojycyj20z20usafs.jpg)