# JavaScript 重要知识点

## 1.搞定闭包和 this

### 执行环境（执行上下文）execution context

> 执行环境定义了变量或者函数有权访问的其他数据，决定了他们各自的行为。
> 每个执行环境都有一个与之关联的变量对象。

- 可以简单理解执行上下文是 js 代码执行的环境，当 js 执行一段可执行代码时，会创建对应的执行上下文。

执行环境建立的步骤

创建阶段

1. 初始化作用域链
2. 创建变量对象
   1. 创建 arguments
   2. 扫描函数声明
   3. 扫描变量声明
3. 求 this

执行阶段

1. 初始化变量和函数的引用
2. 执行代码

### this

> this 对象是在运行时基于函数的执行环境绑定的；在全局函数中，this 等于 window，当函数被作为某个对象的方法掉哟个时，this 指向那个对象。

- 指向全局对象

```js
function foo() {
  console.log(this.a)
}
var a = 2 // 全局定义
foo() // 2 这里的 a 是全局对象
```

- 指向调用对象

```js
function foo() {
  console.log(this.a)
}
var obj = {
  a: 3,
  foo: foo
}
obj.foo() // 3 这里 this 指向的是上文的 obj 对象，
// 此处的值为 obj.a = 3
```

- 用 new 构造就指向 new 出来的对象

```js
a = 4
function A() {
  this.a = 3
  this.callA = function() {
    console.log(this.a)
  }
}
A() // 返回undefined, A().callA会报错。callA被保存在window上
var a = new A()
a.callA() // 3, callA在 new A() 构造的对象里, this 指向new 构造的 A
```

- apply/call/bind

> 令 this 指向传递的第一个参数，如果第一个参数为 null，undefined 或是不传，则指向全局变量

```js
a = 3
function foo() {
  console.log(this.a)
}
var obj = {
  a: 2
}
foo.call(obj) // 2
foo.call(null) // 3
foo.call(undefined) // 3
foo.call() // 3

var obj2 = {
  a: 5,
  foo
}
obj2.foo.call() // 3，不是5!

function foo(something) {
  console.log(this.a, something)
  return this.a + something
}
var obj3 = {
  a: 2
}
var bar = foo.bind(obj3)
// bind 把foo里面的this对象绑定到了obj3上
var b = bar(3) // 2 3
console.log(b) // 5
```

- 箭头函数

> 箭头函数比较特殊，没有自己的 this，它使用封闭执行上下文(函数或是 global)的 this 值。

```js
var x = 11
var obj = {
  x: 22,
  say: () => {
    console.log(this.x) //this指向window
  }
}
obj.say() // 11
obj.say.call({ x: 13 }) // 11
x = 14
obj.say() // 14
//对比一下
var obj2 = {
  x: 22,
  say() {
    console.log(this.x) //this指向obj2
  }
}
obj2.say() // 22
obj2.say.call({ x: 13 }) // 13
```

### 闭包

> 闭包是指有权访问另一个函数作用域中的变量的函数

```js
function init() {
  var name = 'Mozilla' // name 是一个被 init 创建的局部变量
  function displayName() {
    // displayName() 是内部函数,一个闭包
    alert(name) // 使用了父函数中声明的变量
  }
  displayName()
}
init() // 打印出'Mozilla'
```

[闭包 MDN 详细讲解](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures#Practical_closures)

1. 闭包的实际应用：调整字体
2. 用闭包模拟私有方法

闭包特性：

- 函数内再嵌套函数
- 参数和变量不会被垃圾回收机制回收
- 内部函数可以引用外层的参数和变量

闭包的理解：

- 使用闭包主要是为了设计私有的方法和变量。闭包的优点是可以避免全局变量的污染，缺点是闭包会常驻内存，会增大内存使用量，使用不当很容易造成内存泄露。在 js 中，函数即闭包，只有函数才会产生作用域的概念。
- 闭包 的最大用处有两个，一个是可以读取函数内部的变量，另一个就是让这些变量始终保持在内存中。
- 闭包的另一个用处，是封装对象的私有属性和私有方法。

## 2.作用域链的理解

- 作用域链的作用是保证执行环境里有权访问的变量和函数的有序访问，作用域链的变量只能向上访问，变量访问到 window 对象即被终止，作用域链向下访问变量是不被允许的
- 简单的说，作用域就是变量与函数的可访问范围，即作用域控制着变量与函数的可见性和生命周期
- 作用域链的本质上是一个指向变量对象的指针列表

## 3.JavaScript 原型，原型链 ? 有什么特点

> 每个对象都会在其内部初始化一个属性，就是 **prototype(原型)**，当我们访问一个对象的属性时,如果这个对象内部不存在这个属性，那么他就会去 **prototype** 里找这个属性，这个 **prototype** 又会有自己的 **prototype**，于是就这样一直找下去，也就是我们平时所说的原型链的概念

- 关系: `instance.constructor.prototype = instance.__proto__`

- 特点

  - JavaScript 对象是通过引用来传递的，我们创建的每个新对象实体中并没有一份属于自己的原型副本。当我们修改原型时，与之相关的对象也会继承这一改变
  - 当我们需要一个属性的时，Javascript 引擎会先看当前对象中是否有这个属性， 如果没有的就会查找他的 Prototype 对象是否有这个属性，如此递推下去，一直检索到 Object 内建对象

## 4.请解释什么是事件代理

> 事件代理（Event Delegation），又称之为事件委托。是 JavaScript 中常用绑定事件的常用技巧。顾名思义，“事件代理”即是把原本需要绑定的事件委托给父元素，让父元素担当事件监听的职务。事件代理的原理是 DOM 元素的事件冒泡。使用事件代理的好处是可以提高性能

- 问题：父级那么多子元素，怎么区分事件本应该是哪个子元素的？
- 答案是：`event` 对象里记录的有“事件源”，它就是发生事件的子元素。
- 它存在兼容性问题，在老的 IE 下，事件源是 `window.event.srcElement`，其他浏览器是 `event.target`

假设有一个 UL 的父节点，包含了很多个 Li 的子节点：

```html
<ul id="ul1">
  <li>111</li>
  <li>222</li>
  <li>333</li>
  <li>444</li>
</ul>
```

```js
var oUl = document.getElementById('ul1')
var aLi = oUl.children
console.log(aLi)

//传统方法，li身上添加事件，需要用for循环，找到每个li
for (var i = 0; i < aLi.length; i++) {
  aLi[i].onmouseover = function() {
    this.style.background = 'red'
  }
  aLi[i].onmouseout = function() {
    this.style.background = ''
  }
} //for结束
```

现在用事件委托的方式，onmouseover、onmouseout 方法要加在 ul 身上了，再通过找事件源的方式，改变 li 背景，代码如下：上面 ul 的 html 代码不变，js 部分变为

```js
var oUl = document.getElementById('ul1')
oUl.onmouseover = function(ev) {
  var ev = ev || window.event
  var oLi = ev.srcElement || ev.target
  oLi.style.background = 'red'
}

oUl.onmouseout = function(ev) {
  var ev = ev || window.event
  var oLi = ev.srcElement || ev.target
  oLi.style.background = ''
}
```

优点:

1. 可以大量节省内存占用，减少事件注册，比如在 `ul` 上代理所有 `li` 的 `click` 事件就非常棒
2. 可以实现当新增子对象时无需再次对其绑定

## 5.JavaScript 继承

1. 原型式继承
2. 构造继承
3. 组合继承
4. 寄生继承

## 6. this 对象的理解

- `this`总是指向函数的直接调用者（而非间接调用者）
- 如果有`new`关键字，`this`指向`new`出来的那个对象
- 在事件中，`this`指向触发这个事件的对象，特殊的是，`IE`中的`attachEvent`中的`this`总是指向全局对象`Window`

## 7.事件模型

> 事件的发生经历三个阶段:捕获阶段（`capturing`）、目标阶段（`targetin`）、冒泡阶段（`bubbling`）

- 冒泡型事件：当你使用事件冒泡时，子级元素先触发，父级元素后触发
- 捕获型事件：当你使用事件捕获时，父级元素先触发，子级元素后触发
- `DOM`事件流：同时支持两种事件模型：捕获型事件和冒泡型事件
- 阻止冒泡：在 `W3c` 中，使用`stopPropagation()`方法；在 `IE` 下设置`cancelBubble = true`
- 阻止捕获：阻止事件的默认行为，例如 `click - <a>` 后的跳转。在 `W3c` 中，使用 `preventDefault()` 方法，在 `IE` 下设置 `window.event.returnValue = false`

## 8. new 操作符具体干了什么

1. 创建一个空对象 `var obj = new Object();`
2. 让空对象的原型属性指向原型链 `obj.__proto__ = foo.prototype;`
3. 让构造函数 `foo` 的 `this` 指向 `obj`, `foo.call(obj);`

```js
var obj = {} // 1
obj.__proto__ = foo.prototype // 2
foo.call(obj) // 3
```

## 9.Ajax 原理

- `Ajax` 的原理简单来说是在用户和服务器之间加了—个中间层(`AJAX` 引擎)，通过 `XmlHttpRequest` 对象来向服务器发异步请求，从服务器获得数据，然后用 `javascript` 来操作 `DOM` 而更新页面。使用户操作与服务器响应异步化。这其中最关键的一步就是从服务器获得请求数据

```js
/** 1. 创建连接 **/
var xhr = null
xhr = new XMLHttpRequest()
/** 2. 连接服务器 **/
xhr.open('get', url, true)
/** 3. 发送请求 **/
xhr.send(null)
/** 4. 接受请求 **/
xhr.onreadystatechange = function() {
  if (xhr.readyState == 4) {
    // readyState 该属性表示请求/响应过程的当前活动阶段
    // readyState == 4 表示完成
    if (xhr.status == 200) {
      success(xhr.responseText)
    } else {
      /** false **/
      fail && fail(xhr.status)
    }
  }
}
```

## 10.如何解决跨域问题

1. 跨域资源共享（CORS）

   > CORS（Cross-Origin Resource Sharing）跨域资源共享，定义了必须在访问跨域资源时，浏览器与服务器应该如何沟通。CORS 背后的基本思想就是使用自定义的 HTTP 头部让浏览器与服务器进行沟通，从而决定请求或响应是应该成功还是失败。

   ```js
   // 服务器端对于CORS的支持，主要就是通过设置Access-Control-Allow-Origin来进行的。如果浏览器检测到相应的设置，就可以允许Ajax进行跨域的访问
   //指定允许其他域名访问
   'Access-Control-Allow-Origin:*' //或指定域
   //响应类型
   'Access-Control-Allow-Methods:GET,POST'
   //响应头设置
   'Access-Control-Allow-Headers:x-requested-with,content-type'
   ```

2. 通过 jsonp 跨域
3. 通过 WebSocket 进行跨域

   > web sockets 是一种浏览器的 API，它的目标是在一个单独的持久连接上提供全双工、双向通信。
   > web sockets 原理：在 js 创建了 web socket 之后，会有一个 HTTP 请求发送到浏览器以发起连接。取得服务器响应后，建立的连接会使用 HTTP 升级从 HTTP 协议交换为 web sockt 协议。

   ```js
   var socket = new WebSockt('ws://www.baidu.com') //http->ws; https->wss
   socket.send('hello WebSockt')
   socket.onmessage = function(event) {
     var data = event.data
   }
   ```

4. 图像 ping（单向）
5. nginx 代理跨域
6. nodejs 中间件代理跨域

## 11.模块化开发怎么做

1. 私有公有成员分离

   - 利用此种方式将函数包装成一个独立的作用域，私有空间的变量和函数不会影响到全局作用域
   - 立即执行函数,不暴露私有成员

   ```js
   var module1 = (function() {
     var _count = 0
     // 这里形成一个单独的私有的空间
     // 私有成员的作用：
     //   1、将一个成员私有化
     //   2、抽象公共方法（其他成员中会用到的）
     var m1 = function() {
       //...m1 执行方法
     }
     var m2 = function() {
       //...m2 执行方法
     }
     return {
       m1: m1,
       m2: m2
     }
   })()
   ```

## 12.异步加载 JS 的方式有哪些

1. defer 值支持 IE
2. async async="async"
3. 创建 script，插入到 DOM 中，加载完毕后 callBack，见代码

## 13. 那些操作会造成内存泄漏

> 内存泄漏指任何对象在你不在拥有或者需要它之后依然存在

- `setTimeout` 的第一个参数使用字符串而非函数，会引发内存泄漏
- 闭包使用不当

## 14. XML 和 JSON 的区别

- 数据体积：JSON 相对于 XML 来讲，数据的体积小，传递的速度更快些。
- 数据交互：JSON 与 JavaScript 的交互更加方便，更易解析处理
- 数据描述：JSON 对数据的描述性比 XML 差
- 传输速度：JSON 的速度远远快于 XML

## 15.webpack 看法

## 16.AMD CMD CommonJs ES6:Class 对比

> 都是用于模块化定义中的模块化编程的方案；import/export 是 ES6 中新增的

1. AND 异步模块定义
   > AMD 规范则是非同步加载模块，允许指定回调函数; AMD 推荐的风格通过返回一个对象做为模块对象
2. CMD
   > 同步模块定义，是 SeaJS 的一个标准，SeaJS 是 CMD 概念的一个实现，SeaJS 是淘宝团队提供的一个模块开发的 js 框架.(通过 `define()`定义，没有依赖前置，通过 require 加载 jQuery 插件，CMD 是依赖就近，按需加载)
3. CommonJs 规范
   > CommonJS 是服务器端模块的规范，Node.js 采用了这个规范。CommonJS 规范加载模块是同步的，也就是说，只有加载完成，才能执行后面的操作。
   > CommonJS 的风格通过对 module.exports 或 exports 的属性赋值来达到暴露模块对象的目的
4. ES6 特性，模块化---export/import 对模块进行导出导入的
