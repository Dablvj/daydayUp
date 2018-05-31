### 1.2 库与框架的区别

![1.2 库与框架的区别](https://img-blog.csdn.net/20170710180908634)

框架：即一套架构,项目整体解决方案.如：angular、backbone、vue

库：即封装好的特定的集合，提供给开发者使用.如：Query、react、underscore

React和react-router, react-redux结合起来才叫框架，本身只是充当一个前端渲染的库而已；react就是纯粹写UI组件的，没有什么异步处理机制、模块化、表单验证这些.

框架中我们完全可以自由的使用库，同时也可以没有框架的基础之上使用库，都是很自由.

### 1.3 jQuery对象与dom对象的区别

jQuery对象就是通过jQuery包装DOM对象后产生的对象。

```
var v=document.getElementById(“v”);  //DOM对象  
var $v=$(v);    //jQuery对象

var $v =$(“#v”) ; //jQuery对象  
var v=$v[0];    //DOM对象
```