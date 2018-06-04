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

### 1.4 立即表达式与工厂模式

I.立即表达式:

解释器把function关键字当作是函数声明语句,把()中的内容当作表达式;

解释器以「函数表达式」而不是「函数声明」来处理匿名函数的立即执行.

```
function () {console.log("f1");}(); 
(function(){ console.log("test");})();(function(){ console.log("test");}());
```

II.为什么要用立即执行函数表达式呢？

a.模拟块作用域,防止变量覆盖

ES5只有全局作用域和函数作用域，没有块级作用域.

b.解决闭包冲突

闭包就是在函数内部所定义的函数可以持有外层函数的执行环境。因为闭包中变量是引用传递；
立即执行函数表达式中，function的参数是值传递而不是引用传递。

```
var f1 = function() {
    var res = [];
    var fun = null;
    for(var i = 0; i < 10; i++) {
        fun = function() { 
            console.log(i);
        };
        //产生闭包
        res.push(fun);
    }
    return res;
}

// 会输出10个10
var res = f1();
for(var i = 0; i < res.length; i++) {
    res[i]();
}

//修改一下
var f1 = function() {
    var res = [];
    for(var i = 0; i < 10; i++) {
        // 添加一个IIFE
        (function(index) {
            fun = function(){
                console.log(index);
            };
            res.push(fun);
        })(i);
    }

    return res;
}

// 输出结果为0 1 2 3 4 5 6 7 8 9
var res = f1();
for(var i = 0; i < res.length; i++) {
    res[i]();
}
```

c.模拟单例

单例模式只保存一个实例，就是一个全局变量。

在JavaScript的OOP中，我们可以通过IIFE来实现
```
var counter = (function(){
    var i = 0; 
    return {
        get: function(){
            return i;
        },
        set: function( val ){
            i = val;
        },
        increment: function() {
            return ++i;
        }
    };
}());

counter.get(); // 0
counter.set( 3 );
counter.increment(); // 4
counter.increment(); // 5
```
单例模式的思路是：一个类能返回一个对象的引用，并且永远是同一个。
```
(function () {
    function Person(name) {
        this.name = name;
    }
    Person.prototype.work = function () {
        console.log(this.name + " is working");
    }
    var p1 = new Person("Darren");
    p1.work();
    var p2 = new Person("Jack");
    p2.work();
}());
//每次new出一个Person对象都是不同的。

(function () {
    var PersonSingleton = (function () {
        var instance;
        function init() {
            return {
                name: 'Anonymous',
                work: function () {
                    console.log(this.name + ' working');
                }
            };
        }
        return {
            getInstance: function () {
                if (!instantiated) {
                    instantiated = init();
                }
                return instantiated;
            }
        }
    })();
    var p1 = PersonSingleton.getInstance();
    p1.work();
    var p2 = PersonSingleton.getInstance();
    p2.work();
}());
```

III.工厂模式