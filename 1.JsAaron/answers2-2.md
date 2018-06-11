### 2.2 原型的优与弊

1. 原型理论
  
  * [原型理论----我的blog](https://www.jianshu.com/p/a699092d3fbd)

2. 原型的优与弊
  * 优点：信息共享；
    * 原型方法和属性在内存中是共享的，可以用于节约内存，不会因为new一个对象，就开辟一块内存用于存放相同的方法。
  * 缺点：无法保持独立、无法传参、共享属性相同改变；
  * 实例
    ```
    function Box() {}
    Box.prototype = {
        constructor : Box,
        name : 'caibaojian',
        age : 100,
        family : ['哥哥','姐姐','妹妹'],
        run : function () {
          return this.name + this.age + '运行中...'
        }
    };
    var box1 = new Box();
    alert(box1.family);//'哥哥','姐姐','妹妹'
    box1.family.push('弟弟'); //在第一个实例修改后引用类型，保持了共享
    alert(box1.family);//'哥哥','姐姐','妹妹','弟弟'

    var box2 = new Box();
    alert(box2.family);//'哥哥','姐姐','妹妹','弟弟'
    ```  

3. 解决方法
  * 原型模式和构造函数的组合模式
    ```
    //保持独立的用构造函数
    function Box(name, age) { 
      this.name = name;
      this.age = age;
      this.family = ['哥哥','姐姐','妹妹'];
    }
    //保持共享的用原型
    Box.prototype = { 
      constructor : Box,
      run : function () {
        return this.name + this.age + '运行中...'
      }
    };

    var box1 = new Box('caibaojian', 100);
    alert(box1.family);
    box1.family.push('弟弟');
    alert(box1.family);

    var box2 = new Box('Jack', 200);
    alert(box2.family); 
    //引用类型没有使用原型，所以没有共享
    ``` 
  * 动态原型模式(将原型封装到构造函数里)  
    ```
    function Box(name, age) {
      this.name = name;
      this.age = age;
      this.family = ['哥哥','姐姐','妹妹'];
      //判断this.run是否存在
      if (typeof this.run != 'function') { 
        Box.prototype.run = function({
          return this.name + this.age + '运行中...'
        };
      }
    }

    //这么做，是因为多次new Box时，会初始化多次run，为了不让它初始化多次。

    var box1 = new Box('Lee', 100);
    box1.family.push("弟弟");
    alert(box1.family); //哥哥,姐姐,妹妹,弟弟

    var box2 = new Box('Jack', 200);
    alert(box2.family); //哥哥,姐姐,弟弟
    ```
  * 寄生构造函数 = 工厂模式+构造函数
      ```
      function Box(name, age) {
        var obj = new Object();
        obj.name = name;
        obj.age = age;
        obj.run = function () {
          return this.name + this.age + '运行中...'
        };
        return obj;
      }

      var box1 = new Box('caibaojian', 100);
      alert(box1.run());

      var box2 = new Box('Jack', 200);
      alert(box2.run());
      ```
  * 在一些安全环境中，比如禁止使用this和new，即在构造函数里不使用this，在外部实例化构造函数时不使用new  
    ```
    function Box(name, age) {
    var obj = new Object();
    obj.name = name;
    obj.age = age;
    obj.run = function () {
      return this.name + this.age + '运行中...'
    };
    return obj;
    }

    var box1 = Box('Lee', 100);
    alert(box1.run());

    var box2 = Box('Jack', 200);
    alert(box2.run());
    ```
