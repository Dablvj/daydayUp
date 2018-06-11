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

简单工厂模式是由一个方法来决定到底要创建哪个类的实例, 而这些实例经常都拥有相同的接口.
```
var Car = (function () {
    var Car = function (model, year, miles) {
        this.model = model;
        this.year = year;
        this.miles = miles;
    };
    return function (model, year, miles) {
        return new Car(model, year, miles);
    };
})();

var tom = new Car("Tom", 2009, 20000);
var dudu = new Car("Dudu", 2010, 5000);
或
var productManager = {};
productManager.createProductA = function () {
    console.log('ProductA');
}
productManager.createProductB = function () {
    console.log('ProductB');
}        
productManager.factory = function (typeType) {
    return new productManager[typeType];
}
productManager.factory("createProductA");
```
如简单工厂模式在创建ajax对象的时候也非常有用.这个库里提供了几种ajax请求的方式，包括xhr对象的get, post, 也包括跨域用的jsonp和iframe. 为了方便使用, 这几种方式都抽象到了同一个接口里面.
```
var request1 = Request('cgi.xx.com/xxx' , ''get' );
request1.start();
request1.done( fn );
 
var request2 = Request('cgi.xx.com/xxx' , ''jsonp' );
request2.start();
request2.done( fn );
```
Request实际上就是一个工厂方法, 至于到底是产生xhr的实例, 还是jsonp的实例. 是在执行期决定的.

假如我们想在网页面里插入一些元素，而这些元素类型不固定，可能是图片，也有可能是连接，甚至可能是文本，根据工厂模式的定义，我们需要定义工厂类和相应的子类.
```
先来定义子类的具体实现:
var page = page || {};
page.dom = page.dom || {};
//子函数1：处理文本
page.dom.Text = function () {
    this.insert = function (where) {
        var txt = document.createTextNode(this.url);
        where.appendChild(txt);
    };
};

//子函数2：处理链接
page.dom.Link = function () {
    this.insert = function (where) {
        var link = document.createElement('a');
        link.href = this.url;
        link.appendChild(document.createTextNode(this.url));
        where.appendChild(link);
    };
};

//子函数3：处理图片
page.dom.Image = function () {
    this.insert = function (where) {
        var im = document.createElement('img');
        im.src = this.url;
        where.appendChild(im);
    };
};


定义工厂处理函数:
page.dom.factory = function (type) {
    return new page.dom[type];
}

使用方式如下：
var o = page.dom.factory('Link');
o.url = 'http://www.cnblogs.com';
o.insert(document.body);
```

以下情景下工厂模式特别有用：
处理大量具有相同属性的小对象.

### 1.5 无冲突处理机制

1. jQuery在内部先将已经存在的全局变量缓存保存在私有变量_jQuery和_$中.
```
_jQuery = window.jQuery,
_$ = window.$,
```

2. 如果页面在加载jQuery库时，还不存在jQuery和$对象，那么_jQuery和_$都是undefined；否则，它们都会保存对已有jQuery和$的引用.

3. jQuery.noConflict()的作用:
```
noConflict : function (deep) {
    if (window.$ === jQuery) {
        window.$ = _$;
    }
    if (deep && window.jQuery === jQuery) {
        window.jQuery = _jQuery;
    }
    return jQuery;
}
```
  * 如果调用jQuery.noConflict()或是jQuery.noConflict(false)，只有全局变量$会被重置恢复成之前的引用值；

  * 如果调用jQuery.noConflict(true)，那么全局变量jQuery和$都会被重置恢复成之前的引用值。


4. 常用场景：

  * a.在同一个页面加载多个jQuery实例，尤其是不同版本的jQuery。
    * 第一种情况，你的业务代码采用了最新版的jQuery库，而你选用的第三方插件依赖的更早版本的jQuery库；
    * 第二种情况，你正维护着一个系统，它已有的业务代码由于各种原因，引用了较老版本的jQuery库，你新开发的模块采用的是其他版本的jQuery库。
    ```
    <!-- jQuery and $ are undefined -->

    <script src="//ajax.googleapis.com/ajax/libs/jquery/1.10.2/jquery.min.js"></script>
    <!-- jQuery and $ now point to jQuery 1.10.2 -->

    <script src="//ajax.googleapis.com/ajax/libs/jquery/1.7.0/jquery.min.js">
    <!-- jQuery and $ now point to jQuery 1.7.0 -->

    <script>jQuery.noConflict();</script>
    <!-- jQuery still points to jQuery 1.7.0; $ now points to jQuery 1.10.2 -->

    <script src="//ajax.googleapis.com/ajax/libs/jquery/1.6.4/jquery.min.js">
    <!-- jQuery and $ now point to jQuery 1.6.4 -->

    <script>var jquery164 = jQuery.noConflict( true );</script>
    <!-- jQuery now points to jQuery 1.7.0; $ now points to jQuery 1.10.2; jquery164 points to jQuery 1.6.4 -->
    ```  
* b.与第三方库的冲突，如prototype.jsd等JS插件，也使用$。(三种方法)
  * 采用no-conflict模式
  ```
  <!-- 采用no-conflict模式，jquery.js在prototype.js之后被引入. -->
  < script src = "prototype.js" > </script> 
  < script src = "jquery.js" > </script> 
  < script > 
  var $j = jQuery.noConflict();
  //将$的控制权让渡给prototype.js
  // $j 引用了jQuery对象本身.
  $j(document).ready(function () {
    $j("div").hide();
  });
  // $ 被重新指向prototype.js里定义的对象 document.getElementById(). mainDiv below is a DOM
  // element, not a jQuery object.
  window.onload = function () {
    var mainDiv = $("main");
  } 
  < /script>
  ```
    * 采用no-conflict模式配合使用自执行函数封装，使用这种方法，自执行函数内部无法再使用prototype.js定义的$对象了。
    ```
    <!-- jquery.js在prototype.js之后被引入. -->
    <script src="prototype.js"></script>
    <script src="jquery.js"></script>
    <script>
    jQuery.noConflict();
    (function( $ ) {
        // Your jQuery code here, using the $
    })( jQuery );
    </script>
    ```
  * 使用标准jQuery(document).ready()函数,jQuery库在其它库之前引入，那么jQuery内部定义的jQuery和$会被第三方库覆盖，这时候无法再使用noConflict()
  ```
  <!-- jquery.js在prototype.js之前被引入. -->
  <script src="jquery.js"></script>
  <script src="prototype.js"></script>
  <script>
  // Use full jQuery function name to reference jQuery.
  jQuery( document ).ready(function() {
    jQuery( "div" ).hide();
  });

  // 或者
  jQuery(function($){
    // Your jQuery code here, using the $
  });
  // Use the $ variable as defined in prototype.js
  window.onload = function() {
    var mainDiv = $( "main" );
  };
  </script>
  ```
        