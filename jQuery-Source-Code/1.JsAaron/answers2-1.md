### 2.1 理解上下文this
```
2.1.1 作为对象方法调用
2.1.2 作为函数调用
2.1.3 作为apply 或 call 调用
2.1.4 作为构造函数
```
1. 一般而言，在Javascript中，this指向函数执行时的当前对象。值得注意，this关键字在Javascript中和执行环境有关，而非声明环境。

2. this关键字在哪些地方出现？
  * 1、普通函数中的this
  * 2、闭包中的this
  * 3、new关键字创建新对象
  * 4、call与apply中的this
  * 5、eval中的this
  * 6、没有明确的当前对象时的this
  * 7、dom事件中的this

3. 普通函数中的this
  * 无论this身处何处，第一要务就是要找到函数运行时的位置。
  ```
  //1:
  var name="全局";
  function getName(){
    var name="局部";
    return this.name;
  };
  alert(getName());//全局

  //2:
  var name="全局";
  var xpg={
    name:"局部",
    getName:function(){
      return this.name;
    }
  };
  alert(xpg.getName());//局部

  //3:运行的时候是other.showName，所以this指向other.showName函数的当前对象
  var someone = {
    name: "Bob",
    showName: function(){
      alert(this.name);
    }
  };
  var other = {
    name: "Tom",
    showName: someone.showName
  }
  other.showName();　　//Tom
  ```

4. 闭包中的this
  * 无论this身处何处，一定要找到函数运行时的位置。
  * 此时不能根据函数getName运行时的位置来判断，而是根据匿名函数的运行时位置来判断。

  * 例子：
    ```
    var name="全局";
    var xpg={
      name:"局部",
      getName:function(){
        return function(){
          return this.name;
        };
      }
    };
    alert(xpg.getName()());//全局

    //匿名函数的运行时位置
    function (){
      return this.name;
    };
    ```
  * 如何在闭包中使得this身处在xpg中呢？即缓存this。
    ```
    var name="全局";
    var xpg={
      name:"局部",
      getName:function(){
        var that=this;
        return function(){
          return that.name;
        };
      }
    };
    alert(xpg.getName()());//局部
    ```  
5. new关键字创建新对象
  * new关键字后的构造函数中的this指向用该构造函数构造出来的新对象。
  ```
  function Person(__name){
    this.name = __name;    //这个this指向用该构造函数构造的新对象
  }
  Person.prototype.show = function(){
    alert(this.name);  //this 指向Person，this.name = Person.name;
  }
  var Bob = new Person("Bob");
  Bob.show();    //Bob
  ```
6. call与apply中的this
  * call与apply指定this的位置
  ```
  var name="全局";
  var xpg={
    name:"局部"
  };
  function getName(){
    alert(this.name);
  }
  getName(xpg);//全局
  getName.call(xpg);//局部
  getName.call();//全局
  ```

7. 浅谈eval作用域、以及使用和一些特殊的使用方式
  * eval方法只能在非严格模式中进行使用，在use strict中是不允许使用这个方法的。
  * eval函数接收一个参数s
    * 如果s不是字符串，则直接返回s。
    * 否则执行s语句。
      * 如果s语句执行结果是一个值，则返回此值，否则返回undefined。 
      * 需要特别注意的是对象声明语法“{}”并不能返回一个值，需要用括号括起来才会返回值。因为在JS中，“{}”会被当成一个语句块来处理，将大括号识别为JavaScript代码块的开始 和结束标记。那么{}将会被认为是执行语句。
        * 加上圆括号的目的是迫使eval函数在处理的时候强制将括号内的表达式转化为对象，而不是作为语句来执行。
  ```
  eval(1)//1
  eval(true)//true
  eval()//undefined

  eval('"a"+2')//"a2"
  eval('alert(1)')//undefined

  eval('{a:1}')//1
  eval('{a:1,b:2}')//error
  eval('('+'{a:1,b:2}'+')')//[object Object] {a: 1, b: 2}
  加上括号才会将对象原样返回。
  ```   
  * eval直接在函数内部使用，eval作用域的变量是局部变量 
  ```
  var x = 1;
  (function () {
    eval('var x = 123;');//x是局部变量，只能在该匿名函数方法内使用
  })();
  console.log(x);//1
  ```
  * 使在函数内部使用的eval，eval作用域的变量成为全局变量
  ```
  //1
  var x = 1;
  (function () {
    window.eval('var x = 123;');//x就是全部变量
  })();
  console.log(x);//123
   
  //2
  var x = 1;
  (function () {
    var a=eval;
    a('var x = 123;');//x就是全部变量
  })();
  console.log(x);//123
  ```
* 在非严格的模式下，又多了一种方法将JSON字符串形式转换为对象的形式
```
var m=eval(‘(’+data+’)’)//m是JSON对象。
```
* js中eval()和JSON.parse()的区别
  * 安全性
    * 用eval不仅可以解析json字符串还可以执行参数
    * 用eval会是恶意用户在字符串参数中注入了向页面插入木马链接的脚本
    * 注意：某些低级的浏览器尚不支持JSON.parse() 
    ```
    var str = 'alert(1000.toString())'; 
    eval(str);  
    JSON.parse(str); 
    ```
   * JSON.parse()解析的必须是json格式的字符串，否则报错，而eval()则没有这么严格
     * “json格式的字符串”是指要求指定的字符串必须符合严格的JSON格式(属性名称必须加引号、字符串值也必须用双引号。)
     ```
     JSON.parse('{name:"lulu",sex:"female"}')//error
     eval('('+'{name:"lulu",sex:"female"}'+')')//{name: "lulu", sex: "female"}
     ```
   
8. eval中的this
  * 对于eval函数，实际上其执行时候,其this并非指向window，因为该函数执行时的作用域是当前作用域.
  ```
  var name = "window"; 
  var Bob = {
    name: "Bob",
    showName: function(){
      eval("alert(this.name)");
    }
  };
  Bob.showName();  //Bob
  ```

9. 没有明确的当前对象时的this
  * 当没有明确的执行时的当前对象时，this指向全局对象window。 
  ```
  var name = "Tom";
  var Bob = {
    name: "Bob",
    show: function(){
      alert(this.name);
    }
  }
  var show = Bob.show;
  show();　　//Tom


  var name = "window";
  var Bob = {
    name: "Bob",
    showName: function(){
      alert(this.name);
    }
  };
  var Tom = {
    name: "Tom",
    showName: function(){
      var fun = Bob.showName;
      fun();
    }
  };
  ```
  * 在浏览器中setTimeout、setInterval和匿名函数执行时的当前对象是全局对象window，这可以看成是这一条的一个特殊情况。
  ```
  var name = "Bob"; 
  var nameObj ={ 
    name : "Tom", 
    showName : function(){ 
      alert(this.name); 
    }, 
    waitShowName : function(){ 
      setTimeout(this.showName, 1000); 
    } 
  }; 
  nameObj.waitShowName();//Bob
  ```
  10. dom事件中的this  
   * 你可以直接在dom元素中使用
     * 它为所属的html元素所拥有，直接在它触发的函数里写this，this应该指向该html元素。
   ```
    <input id="btnTest" type="button" value="提交" onclick="alert(this.value))" />
   ``` 
   * 给dom元素注册js函数   
     * 不正确的方式
       * 分析：onclick事件直接调用thisTest函数，程序就会弹出undefined。因为thisTest函数是在window对象中定义的，所以thisTest的拥有者（作用域）是window，thisTest的this也是window。
     ```
      <script type="text/javascript">
      function thisTest(){
      alert(this.value); // 弹出undefined
      }
      </script>
      <input id="btnTest" type="button" value="提交" onclick="thisTest()" />
      ```
        
      * 正确的方式
        * 分析：通过document.getElementById(“btnTest”).onclick=thisTest;这样的形式，将btnTest的onclick属性设置为thisTest函数的一个副本，this归btnTest所有.
         
        ```
        <input id="btnTest" type="button" value="提交" />
        <script type="text/javascript">
        function thisTest(){
          alert(this.value); 
        }
        document.getElementById("btnTest").onclick=thisTest; //给button的onclick事件注册一个函数
        </script>
        ```
      * 多个不同的HTML元素虽然创建了不同的函数副本，但每个副本的拥有者都是相对应的HTML元素，各自的this不会造成混乱。如下：
      ```
      <input id="btnTest1" type="button" value="提交1" onclick="thisTest()" />
      <input id="btnTest2" type="button" value="提交2" />
      <script type="text/javascript">
      function thisTest(){
        this.value="提交中";
      }
      var btn=document.getElementById("btnTest1");
      alert(btn.onclick); //第一个按钮函数
      var btnOther=document.getElementById("btnTest2");
      btnOther.onclick=thisTest;
      alert(btnOther.onclick); //第二个按钮函数
      </script>

      //其弹出的结果是：
      //第一个按钮
      function onclick(){
      thisTest()
      }
      //第二个按钮
      function thisTest(){
      this.value="提交中";
      }
      ```
      * 每新建一个函数的副本，程序就会为这个函数副本分配一定的内存。而实际应用中，大多数函数并不一定会被调用，于是这部分内存就被白白浪费了。所以我们通常都这么写：
        * 因为我们使用了函数引用的方式，程序就只会给函数的本体分配内存，而引用只分配指针。这样给它分配一个（指针）引用，效率就高很多。
      ```
      <input id="btnTest1" type="button" value="提交1" onclick="thisTest(this)" />
      <input id="btnTest2" type="button" value="提交2" onclick="thisTest(this)" />
      <input id="btnTest3" type="button" value="提交3" onclick="thisTest(this)" />
      <input id="btnTest4" type="button" value="提交4" onclick="thisTest(this)" />
      <script type="text/javascript">
      function thisTest(obj){
        alert(obj.value); 
      }
      </script>
      ```

