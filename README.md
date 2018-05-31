# jquery-source-study

分析源码有助于我们增加api，js，常见的设计模式的熟时度，扩展思路。

先从整体理解架构，然后各个攻破依赖模块（回调函数、异步加载、数据缓存、动画队列），再整体分析选择器、DOM处理、事件、AJAX\动画模块。

对于浏览器兼容、逻辑流程、性能等等都是是前端开发的基础核心，jquery都做到了。拥有强大的选择器，优雅的链式，简洁的API。

jQuery2.0及后续版本将不再支持IE6/7/8浏览器。

## jQuery 整体架构图
![jQuery整体架构脑图，jQuery整体架构](https://raw.githubusercontent.com/chokcoco/jQuery-/master/mindMap/jQuery%E6%95%B4%E4%BD%93%E6%9E%B6%E6%9E%84.png)

