### 2.2 原型的优与弊

1. 原型理论----我的blog
  
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

  －－－－待续