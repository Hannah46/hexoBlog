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