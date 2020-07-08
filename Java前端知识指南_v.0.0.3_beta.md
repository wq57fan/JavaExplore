# Java前端知识指南

[TOC]

## 一、前端常用技术

### 1、UI框架

* Ant-Design：阿里巴巴的基于React的UI框架；
* ElementUI、iview、ice：饿了么的基于Vue的UI框架；
* Bootstrap：Twitter的开源前端开发工具包；
* AmazeUI：基于HTML5的跨屏前端框架。

### 2、JavaScript构建工具

* Babel：JS编译工具，主要用于浏览器不支持的ES新特性，比如用于编译TypeScript；
* WebPack：模块打包器，主要用于打包、压缩、合并、按序加载。

### 3、前端需要掌握的后端技术

1. NodeJS，他是一种JavaScript的运行环境，能够使得JavaScript脱离浏览器运行。他自带的框架和项目管理工具如下：
   * Express：框架；
   * Koa：Express简化版；
   * NPM：项目综合管理工具，类似于Maven；
   * YARN：NPM的替代方案。
2. Deno：JavaScript和TypeScript的安全运行时环境，他是将要替代NodeJS的方案。

## 二、架构发展

### 1、有哪些MV*的模式？

* MVC(同步通信为主)：Model、View、Controller

  **对于 View 来说，它不是和 Model 完全分离的。**如果用户的操作是访问数据，那么可以在 View 中向 Model 要数据。MVC 的不足是 View 和 Controler 在处理完成后，会有机制通知 View，一般采用“观察监听”设计模式。

  <img src="C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200607143204830.png" alt="image-20200607143204830" style="zoom:80%;" />

* MVP(异步通信为主)：Model、View、Presenter

  可以看出，MVC 的不足是 View 和 Model 之间不是严格意义的完全分离。MVP 正是对 MVC 这一点做出了改进。MVP 中的 P 是 Presenter 的缩写，代表“展示器”。所有的消息（客户端请求、用户事件）都统一交给 Presenter 来处理，由 Presenter 来向 Model 进行数据查询或者更新。**而 Presenter 和 View 之间，一般会约定好接口调用的格式。**

  <img src="C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200607143231157.png" alt="image-20200607143231157" style="zoom:80%;" />

* MVVM(异步通信为主)：Model、View、ViewModel

  可以看出，MVP 虽然实现了 V 和 M 的分离，但是开发者必须提前规定 P 和 V 的交互接口，对开发来说并不友好。有没有办法能够实现，当 Model 发生改变的时候，立即就下发到视图，并且实现视图更新呢？

  MVVM 通过“双向绑定”实现了这个要求。

  <img src="C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200607143250158.png" alt="image-20200607143250158" style="zoom:80%;" />

### 2、什么是MVVM？

> **Model**

指数据模型，即后端进行的各种业务逻辑处理和数据操控，主要围绕数据库展开。难点：需要和前端约定统一的接口规则。

> **View**

指视图层，即用户界面。主要由HTML和CSS来构建，用于展示ViewModel或者Model层的数据。已经有很多前端模板语言如`FreeMarker`、`Thymeleaf`等，各大MVVM框架如`Vue.js`，`Angular.js`等也有自己用来构建用户界面的内置模板语言。

> **ViewModel**

指视图数据层，由前端人员组织生成和维护。这里，前端人员对从后端获取的Model数据进行转换处理，做二次封装，以生成符合View层使用的视图数据模型。封装后的数据模型包括：视图的状态(展示)+视图的行为(交互)。而Model层的数据模型只包含`状态`。

视图的状态和行为都封装在了ViewModel里。由于采用了了`虚拟DOM技术`和实现了`双向绑定`(从界面的操作能实时反映到数据，数据的变更能实时展现到界面)，前端人员则不必再通过操纵DOM去更新视图。

### 3、为什么要使用MVVM？

因为MVVM实现了解耦：View层展现的不是Model层的数据，而是ViewModel层的数据，**由于ViewModel层负责与Model层交互，这就完全解耦了View层和Model层**。好处：

* 低耦合：View可以独立于Model变化和修改，一个ViewModel可以绑定到不同的View上；
* 可复用：可以把一些视图逻辑放在一个ViewModel里，让多个View重用这段逻辑；
* 独立开发：开发人员专注于业务逻辑和数据的开发(ViewModel)，设计人员专注于页面设计；
* 易测试：测试可以针对ViewModel来写。

### 4、怎么实现MVVM？

<img src="C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200607150245153.png" alt="image-20200607150245153" style="zoom:80%;" />

* View：表示DOM(HTML操作的元素)；
* ViewModel：表示连接视图和数据的中间件，`Vue.js就是MVVM中该层的实现者`；
* Model：表示JavaScript对象。

其中ViewModel定义了一个Observer观察者：

1. ViewModel能够观察到数据(M)的变化，并对视图对应的内容进行更新；
2. ViewModel能够监听到视图(V)的变化，并能够通知数据发生改变。

## 三、Vue

学习vue我们必须之到它的7个属性，4个方法，以及8个指令。

### 1、7个属性

1. **el**

   提供一个在页面上已存在的DOM元素作为Vue实例的挂载目标。可以是CSS选择器，也可以是HTMLElement实例。

   ```js
   var app = new Vue({
      el : '#app',
      data : {
       message : 'hello world' 
    }
   })
   ```

2. **data**
    用来组织从view中抽象出来的属性，可以说将视图的数据抽象出来存放在data中。

3. template

     用来设置模板，会替换页面元素，包括占位符。

4. **methods**

   放置页面中的业务逻辑，js方法一般都放置在methods中。

5. render

   创建真正的Virtual Dom。

6. computed

   Vue中独有的！定义方法和methods一样，但是他是用来计算的属性，结果被存到属性中，类似于缓存。

   ```js
   	// 计算属性
       var app8 = new Vue({
           el:'#app-8',
           data:{
               message:'计算属性'
           },
           methods: {
               currentTime1:function () {
                   return Date.now();
               }
           },
           //类似于缓存->虚拟DOM，节省了浏览器成本
           //message刷新后会重新计算
           computed:{
               currentTime2:function () {
                   this.message;
                   return Date.now();
               }
           }
       });
   ```

   计算属性可以将不常变化的计算结果进行缓存，以节约系统开销。

7. watch

   watch:function(new,old){} 监听data中数据的变化 两个参数，一个返回新值，一个返回旧值。


### 2、4个方法

1. component()

   构建组件。

   ```js
   // 组件化应用构建
       Vue.component('todo-item', {
           props: ['todo'],
           template: '<li>{{todo.id}}-{{ todo.text }}</li>'
       })
       var app7 = new Vue({
           el: '#app-7',
           data: {
               groceryList: [
                   {id: 0, text: '蔬菜'},
                   {id: 1, text: '奶酪'},
                   {id: 2, text: '随便其它什么人吃的东西'}
               ]
           }
       })
   ```

   ```html
   <div id="app-7">
       组件化应用构建 app7!
       <ol>
           <!--
             现在我们为每个 todo-item 提供 todo 对象,
             todo 对象是变量，即其内容可以是动态的。
             我们也需要为每个组件提供一个“key”，稍后再
             作详细解释。
           -->
           <todo-item
                   v-for="item in groceryList"
                   v-bind:todo="item"
           ></todo-item>
       </ol>
   </div>
   ```

2. $watch()

   监听函数。

   ```js
   var data2 = {a: 1}
       var app2 = new Vue({
           el: "#app-2",
           data: data2
       })
       data2.a = "声明式渲染,app2!"
       app2.$watch('a', function (newVal, oldVal) {
           console.log(newVal, oldVal);
       });
   ```

   ```html
   <div id="app-2">
       {{a}}
   </div>
   ```

3. mounted()

   钩子函数。html加载完成后执行。例子详见Axios异步通信中。

4. data()

   组件中的data是函数，不是属性。例子详见Axios异步通信中。

### 3、8个指令

1. **v-bind**
   功能：绑定变量。

   ```vue
   <span v-bind:title="message">
   ```

2. v-text

   功能：绑定标签内显示内容。

3. v-html

   功能：以html形式渲染变量内容。

4. v-show

   功能：隐藏节点的显示。

   ```vue
   <div v-show="flase"></div>
   ```

5. **v-if**

   功能：隐藏节点的显示。

   ```vue
   <div v-if="true"></div>
   ```

6. **v-for**

   功能：循环产生同一个组件。

   ```vue
   <li v-for="todo in todos">
       {{ todo.text }}
   </li>
   ```

7. **v-on**

   功能：为节点绑定事件，可简写成@。

   ```vue
   <button v-on:click="reverseMessage">反转消息</button>
   <button @click="reverseMessage">反转消息</button>
   ```

8. **v-model**

   功能：为input提供双向绑定功能。

   ```vue
   <input v-model="message">
   ```

### 4、Vue实例生命周期

![Vue-instance-lifecycle](C:\Users\49769\AppData\Roaming\Typora\typora-user-images\Vue-instance-lifecycle.jpg)

### 5、Axios异步通信

> 什么是Axios？

Axios是一个基于Promise 的，支持node端和浏览器端的高效http库。他能够从浏览器中创建 XMLHttpRequests，或者从 node.js 创建 http 请求。

> 为什么要使用Axios？

由于Vue.js是一个视图层框架且遵守SoC原则，所以他并不包含AJAX的通信功能，为了解决通信问题，我们使用Axios框架。jQuery也可以但是不推荐，因为操作DOM太频繁！

> 怎么通过Axios进行通信？

以获取json字符串为例：

假如data.json为：

```json
{
  "name":"wqf",
  "url":"https://www.bilibili.com/video/BV18E411a7mC?p=9",
  "page":1,
  "isNonProfit":true,
  "address":{
    "street":"航空港",
    "city":"成都",
    "country":"中国"
  },
  "links":[
    {
      "name":"bilibili",
      "url":"https://www.bilibili.com"
    },
    {
      "name":"baidu",
      "url":"https://www.baidu.com/"
    }
  ]
}
```

前端获取json的代码：

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<div id="vue">
    <div>{{info.name}}</div>
    <div>{{info.address.street}}</div>
    <div>{{info.address.city}}</div>
    <div>{{info.address.country}}</div>
    <a v-bind:href="info.url">Click me！</a>
</div>

<script src="vue.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data() {
            return {
                // 请求的返回参数必须和json字符串一样，此处返回的是格式
                info: {
                    name: null,
                    address: {
                        street: null,
                        city: null,
                        country: null
                    },
                    url: null
                }
            }
        },
        mounted() {// 钩子函数、链式编程、ES6新特性
            // axios.get('../data.json').then(response=>(console.log(response.data)));
            axios.get('../data.json').then(response => (this.info = response.data));
        }
    });
</script>
</body>
</html>
```

