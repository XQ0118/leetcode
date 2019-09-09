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

## 17.常见 web 安全及防护原理

### sql 注入原理

> 就是通过把 SQL 命令插入到 Web 表单递交或输入域名或页面请求的查询字符串，最终达到欺骗服务器执行恶意的 SQL 命令

- 防护
  - 永远不要信任用户的输入，要对用户的输入进行校验，可以通过正则表达式，或限制长度，对单引号和双"-"进行转换等
  - 永远不要使用动态拼装 SQL，可以使用参数化的 SQL 或者直接使用存储过程进行数据查询存取
  - 永远不要使用管理员权限的数据库连接，为每个应用使用单独的权限有限的数据库连接
  - 不要把机密信息明文存放，请加密或者 hash 掉密码和敏感的信息

### XSS 原理及防范

> `Xss(cross-site scripting)`攻击指的是攻击者往`Web`页面里插入恶意`html`标签或者`javascript`代码。比如：攻击者在论坛中放一个看似安全的链接，骗取用户点击后，窃取`cookie`中的用户私密信息；或者攻击者在论坛中加一个恶意表单，当用户提交表单的时候，却把信息传送到攻击者的服务器中，而不是用户原本以为的信任站点

- 防范
  - 首先代码里对用户输入的地方和变量都需要仔细检查长度和对`”<”,”>”,”;”,”’”`等字符做过滤；其次任何内容写到页面之前都必须加以`encode`，避免不小心把`html tag`弄出来。这一个层面做好，至少可以堵住超过一半的`XSS` 攻击

### CSRF 跨站请求伪造

> 冒充用户发起请求（在用户不知情的情况下）,完成一些违背用户意愿的请求（如恶意发帖，删帖，改密码，发邮件等）。只要是伪造用户发起的请求，都可成为`CSRF`攻击

- 防范
  1. 采取验证码，强制用户必须与应用进行交互，才能完成最终请求
  2. 最好的办法是采取 token 验证

## 18.设计模式

## 19.为什么要有同源限制

- 同源策略指的是：两个页面地址中的协议，域名，端口号一致，则表示同源。
- 不能通过`ajax`请求不同域的数据，不能通过脚本操作不同域下的 DOM
- 目的：设置同源限制主要是为了安全，如果没有同源限制存在浏览器中的`cookie`等其他数据可以任意读取，不同域下`DOM`任意操作，`ajax`任意请求的话如果浏览了恶意网站那么就会泄漏这些隐私数据

## 20.JavaScript 定义对象方法

- 对象字面量：`var obj = {};`
- 构造函数：`var obj = new Object();`
- Object.create(): `var obj = Object.create(Object.prototype);`

## 21.对 promise 的了解

- 依照 `Promise/A+` 的定义，`Promise` 有四种状态：
  - `pending`: 初始状态, 非 `fulfilled` 或 `rejected`.
  - `fulfilled`: 成功的操作.
  - `rejected`: 失败的操作.
  - `settled`: `Promise` 已被 `fulfilled` 或 `rejected` ，且不是 `pending`
- `Promise` 对象用来进行延迟(`deferred`) 和异步(`asynchronous`) 计算
- 构造一个 `Promise` ，最基本的用法如下

  ```js
  var promise = new Promise(function(resolve, reject) {
        if (...) {  // succeed
            resolve(result); // resolve 解决
        } else {   // fails
            reject(Error(errMessage)); // reject 拒绝
        }
    });
  ```

- `Promise` 最大的好处是在异步执行的流程中，通过 `then`, `catch` 等方法把执行代码和处理结果的代码清晰地分离了

## 22. NodeJS 的应用场景

- 特点

  1. 它是一个 Javascript 运行环境
  2. 依赖于 Chrome V8 引擎进行代码解释
  3. 事件驱动
  4. 非阻塞 I/O
  5. 单进程 单线程

- 优点

  - 高并发（最重要）
  - 适合 I/O 密集型应用

- 缺点

  - 只支持单核 CPU，不能充分利用 CPU
  - 可靠性低，一旦代码某个环节崩溃，整个系统都崩溃

- 适合 `NodeJS` 场景
  1. `restful API`
  2. 统一 `web` 应用的 `UI` 层
  3. 大量 `Ajax` 请求的应用

## 23. Web 开发中绘画跟踪的方法

- 什么是会话
  > 客户端打开与服务器的连接发出请求到服务器响应客户端请求的全过程称之为会话。
- 什么是会话跟踪
  > 对同一个用户对服务器的连续请求 i 和接受响应的监视
- 为什么需要会话跟踪
  > 浏览器与服务器之间的通信是通过 HTTP 协议进行通信的，而 HTTP 协议是”无状态”的协议，它不能保存客户的信息，即一次响应完成之后连接就断开了，下一次的请求需要重新连接，这样就需要判断是否是同一个用户，所以才应会话跟踪技术来实现这种要求

1. url (统一资源定位符) 重写
   > 在 `URL` 结尾添加一个附加数据以标识该会话,把会话 `ID` 通过 `URL` 的信息传递过去，以便在服务器端进行识别不同的用户 。
2. Cookie
   > `Cookie` 是 `Web` 服务器发送给客户端的一小段信息，客户端请求时可以读取该信息发送到服务器端，进而进行用户的识别。
   > 对于客户端的每次请求，服务器都会将 `Cookie` 发送到客户端,在客户端可以进行保存,以便下次使用。
3. 隐藏表单域 `<input type="hidden">` 非常适合步需要大量数据存储的会话应用
4. session
   > 在服务器端会创建一个 `session` 对象，产生一个 `sessionID` 来标识这个 `session` 对象，然后将这个 `sessionID` 放入到 `Cookie` 中发送到客户端, 下次访问时, `sessionID` 会发送到服务器，在服务器端进行识别不同的用户

## 24. JS 不同类型的值

- 栈: 原始(基本)数据类型: `Undefined、Null、Boolean、Number、String、Symbol (ES6 新增，表示独一无二的值)`
- 堆: 引用数据类型: 统称为 `Object` 对象，主要包括对象、数组和函数
- 两种类型的区别：存储位置不同
- 基本数据类型直接存储在栈 (`stack`) 中的简单数据段，占据空间小，大小固定，属于被频繁使用的数据，因此放入栈中；
- 引用数据类型存储在堆 (`heap`) 中的对象，占据空间大，大小不固定，如果存储在栈中，将会影响程序运行的性能；引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。当解释器寻找引用值时，会首先检索其在栈中的地址，取得地址后从堆中获得实体。

## 25. JavaScript 创建对象的几种方式

- 对象字面量

```js
person = {
  name: 'xq',
  age: '21'
}
```

- 用 function 来模拟无参的构造函数

```js
function Person() {}
var person = new Person() //定义一个function，如果使用new"实例化",该function可以看作是一个Class
person.name = 'Mark'
person.age = '25'
person.work = function() {
  alert(person.name + ' hello...')
}
person.work()
```

- 用 function 来模拟参数构造函数来实现（用 this 关键字定义构造的上下文属性）

```js
function Pet(name, age, hobby) {
  this.name = name //this作用域：当前对象
  this.age = age
  this.hobby = hobby
  this.eat = function() {
    alert('我叫' + this.name + ',我喜欢' + this.hobby + ',是个程序员')
  }
}
var maidou = new Pet('麦兜', 25, 'coding') //实例化、创建对象
maidou.eat() //调用eat方法
```

- 用工厂方式来创建（内置对象）

```js
var wcDog = new Object()
wcDog.name = '旺财'
wcDog.age = 3
wcDog.work = function() {
  alert('我是' + wcDog.name + ',汪汪汪......')
}
wcDog.work()
```

- 用原型方式来创造

```js
function Dog() {}
Dog.prototype.name = '旺财'
Dog.prototype.eat = function() {
  alert(this.name + '是个吃货')
}
var wc = new Dog()
wc.eat()
```

- 用混合方式来创建

```js
function Car(name, price) {
  this.name = name
  this.price = price
}
Car.prototype.sell = function() {
  alert('我是' + this.name + '，我现在卖' + this.price + '万元')
}
var camry = new Car('凯美瑞', 27)
camry.sell()
```

## 26. eval() 函数功能

- 把对应的字符串解析成 `JavaScript` 代码并运行
- 应该避免使用 `eval()` ，不安全，非常耗性能

## 27. null, undefined 区别

- `undefined` 表示不存在这个值
- `undefined` :是一个表示"无"的原始值或者说表示"缺少值"，就是此处应该有一个值，但是还没有定义。当尝试读取时会返回 `undefined`
- 例如变量被声明了，但是没有赋值，就是 `undefined`
- `null` 表示一个对象被定义了，值为空值
- `null` 是一个对象（空对象，没有任何属性和方法）
- 在验证 `null` 时，一定要用 `===` , `==` 无法区别 `null` 和 `undefined`

## 28. ["1", "2", "3"].map(parseInt) 答案是多少

- parseInt() 函数可解析一个字符串，并返回一个整数
  - 语法：`parseInt(string, radix)`
    - `string` 必需。要被解析的字符串
    - `radix` 可选。表示要解析的数字的基数。该值介于 `2 ~ 36` 之间。
      > 如果该参数小于 2 或者大于 36，则 parseInt() 将返回 NaN;
      > 如果省略该参数或其值为 0，则数字将以 10 为基础来解析。
- `map` 方法在调用 `callback` 函数时,会给它传递三个参数:当前正在遍历的元素,元素索引, 原数组本身 `(currentValue, index, array)`
- 第三个参数 `parseInt` 会忽视, 但第二个参数不会,也就是说, `parseInt` 把传过来的索引值当成进制数来使用.从而返回了 `NaN` (对应的 `radix` 不合法导致解析失败)
- 理解：`["1", "2", "3"].map(parseInt)` 应该对应的是： `[parseInt("1", 0), parseInt("2", 1), parseInt("3", 2)] parseInt("3", 2)` 的第二个参数是界于 `2-36` 之间的，之所以返回 `NaN` 是因为 字符串 "3" 里面没有合法的二进制数，所以 `NaN`

## 29. javascript 代码中的"use strict";是什么意思

- `use strict` 是一种 `ECMAscript 5` 添加的（严格）运行模式,这种模式使得 `Javascript` 在更严格的条件下运行,使 `JS` 编码更加规范化的模式,消除 `Javascript` 语法的一些不合理、不严谨之处，减少一些怪异行为
- 变量必须声明后再使用
- 函数的参数不能有同名属性
- 不能使用 `with` 语句
- 禁止 `this` 指向全局对象

## 30. 同步和异步的区别

- 同步：浏览器访问服务器请求，用户看得到页面刷新，重新发请求,等请求完，页面刷新，新内容出现，用户看到新内容,进行下一步操作
- 异步：浏览器访问服务器请求，用户正常操作，浏览器后端进行请求。等请求完，页面不刷新，新内容也会出现，用户看到新内容

## 31. defer 和 async

- `defer` 并行加载 `js` 文件，会按照页面上 `script` 标签的顺序执行
- `async` 并行加载 `js` 文件，下载完成立即执行，不会按照页面上 `script` 标签的顺序执行

## 32. attribute 和 property 的区别是什么

- `attribute` 是 `dom` 元素在文档中作为 `html` 标签拥有的属性
- `property` 就是 `dom` 元素在 `js` 中作为对象拥有的属性
- 对于 `html` 的标准属性来说, `attribute` 和 `property` 是同步的，是会自动更新的;但是对于自定义的属性来说，他们是不同步的

## 33.谈谈你对 ES6 的理解

- 块级作用域的出现以及变量定义的改变
  - 块级作用域: 大括号包裹的部分形成的局部作用域。
  - `let` 代替 `var` 用于定义变量,重复定义时会报错.
  - `const` 用于定义常量,且必须初始化,一旦设定后不可更改,否则会报错
- 模块 `module` 的导出和导入
  - `export` 作为导出符,可以导出变量、函数、类等
  - `import` 作为导入符，可以单个导入（`import { a } from '/example.js'`）,多个导入（`import { a，b } from '/example.js'`）、导入整个模块（不常用）、导入时重命名、默认值导入（`import a from 'example.js'`）
- 对象的扩展

  - `Object.is()` 全等判断, 相对 `==` 和 `===` 功能更强大; `==` 会进行类型转换, `===` 对 `+0 -0 NAN` 无作用

    ```js
    console.log(+0 === -0) //true
    console.log(Object.is(+0, -0)) //false
    console.log(NAN === NAN) //false
    console.log(Object.is(NAN, NAN)) //true
    ```

  - 提供原生的 `promise` 对象

- 新增模板字符串
- 箭头函数

## 34. JS 类型判断

- `jQuery` 的方法，比较全面， 比 `typeof instanceof constructor` 功能强大

```js
// 利用这个方法，可以写一个返回数据类型的方法
var isType = function(obj) {
  return Object.prototype.toString.call(obj).slice(8, -1)
}
```

## map 与 forEach 的区别

- `forEach` 方法，是最基本的方法，就是遍历与循环，默认有 3 个传参：分别是遍历的数组内容 `item` 、数组索引 `index` 、和当前遍历数组 `Array` ; 没有返回值
- `map` 方法，基本用法与 `forEach` 一致，但是不同的，它会返回一个新的数组，所以在 `callback` 需要有 `return` 值，如果没有，会返回 `undefined`

## let var const

### let

- 允许声明一个作用域被限制在块级中的变量、语句或者表达式
- let 绑定不受变量提升的约束，这意味着 let 声明不会被提升到当前
- 该变量处于从块开始到初始化处理的“暂存死区”

### var

- var 变量关键字的真正问题在于其在方法内部的定义变量开始的任何地方都可以访问到这个变量。
- 声明变量的作用域限制在其声明位置的上下文中，而非声明变量总是全局的
- 由于变量声明（以及其他声明）总是在任意代码执行之前处理的，所以在代码中的任意位置声明变量总是等效于在代码开头声明

### const

- 声明创建一个值的只读引用 (即指针)
- const 用来专门声明一个常量，它跟 let 一样作用于块级作用域，没有变量提升，重复声明会报错，不同的是 const 声明的常量不可改变，声明时必须初始化（赋值）

## 快速的让一个数组乱序

```js
var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
arr.sort(function() {
  return Math.random() - 0.5
})
console.log(arr)
```

## 如何渲染几万条数据并不卡住界面

> 不能一次性将几万条都渲染出来，而应该一次渲染部分 `DOM` ，那么就可以通过 `requestAnimationFrame` 来每 `16 ms` 刷新一次

```js
setTimeout(() => {
  // 插入十万条数据
  const total = 100000
  // 一次插入 20 条，如果觉得性能不好就减少
  const once = 20
  // 渲染数据总共需要几次
  const loopCount = total / once
  let countOfRender = 0
  let ul = document.querySelector('ul')
  function add() {
    // 优化性能，插入不会造成回流
    const fragment = document.createDocumentFragment()
    for (let i = 0; i < once; i++) {
      const li = document.createElement('li')
      li.innerText = Math.floor(Math.random() * total)
      fragment.appendChild(li)
    }
    ul.appendChild(fragment)
    countOfRender += 1
    loop()
  }
  function loop() {
    if (countOfRender < loopCount) {
      window.requestAnimationFrame(add)
    }
  }
  loop()
}, 0)
```

## 希望获取到页面中所有的 checkbox 怎么做

```js
var domList = document.getElementsByTagName(‘input’)
 var checkBoxList = [];
 var len = domList.length;　　//缓存到局部变量
 while (len--) {　　//使用while的效率会比for循环更高
 　　if (domList[len].type == ‘checkbox’) {
     　　checkBoxList.push(domList[len]);
 　　}
 }
 // 2
var resultArr= [];
var input = document.querySelectorAll('input');
for( var i = 0; i < input.length; i++ ) {
    if( input[i].type == 'checkbox' ) {
        resultArr.push( input[i] );
    }
}
```

## 怎样添加、移除、移动、复制、创建和查找 DOM 节点

### 创建新节点

```js
createDocumentFragment() //创建一个DOM片段
createElement() //创建一个具体的元素
createTextNode() //创建一个文本节点
```

### 添加、移除、替换、插入 DOM 节点

```js
appendChild() //添加
removeChild() //移除
replaceChild() //替换
insertBefore() //插入
```

### 查找节点

```js
getElementsByTagName() //通过标签名称
getElementsByClassName() //通过元素的class的值
getElementById() //通过元素Id，唯一性
```

## 数组去重方法

- ES6 Set 方法

  ```js
  let arr = [1, 1, 2, 2, 3, 3]
  arr = [...new Set(arr)]
  console.log(arr) // [1, 2, 3]
  ```

## 浏览器缓存

> 浏览器缓存分为强缓存和协商缓存

- 先根据这个资源的一些 `http header` 判断它是否命中强缓存，如果命中，则直接从本地获取缓存资源，**不会发请求到服务器**；
- 当强缓存没有命中时，客户端会发送请求到服务器，服务器通过另一些`request header` 验证这个资源是否命中协商缓存，称为 http 再验证，如果命中，**服务器将请求返回**，但不返回资源，而是告诉客户端直接从缓存中获取，客户端收到返回后就会从缓存中获取资源；
- 强缓存和协商缓存共同之处在于，如果命中缓存，服务器都不会返回资源； 区别是，强缓存不对发送请求到服务器，但协商缓存会。
- 当协商缓存也没命中时，服务器就会将资源发送回客户端。
- 当 `ctrl+f5` 强制刷新网页时，直接从服务器加载，跳过强缓存和协商缓存；
- 当 `f5` 刷新网页时，跳过强缓存，但是会检查协商缓存；

## websocket

> 由于 `http` 存在一个明显的弊端（消息只能有客户端推送到服务器端，而服务器端不能主动推送到客户端），导致如果服务器如果有连续的变化，这时只能使用轮询，而轮询效率过低，并不适合。于是 `WebSocket` 被发明出来

- 支持双向通信，实时性更强
- 发送文本，二进制文件
- 协议标识符 `ws` ; 加密后 `wss`
- 较少的控制开销
- 无跨域问题

## 尽可能多的说出你对 Electron 的理解

> `Electron` 提供了一个 `Nodejs` 的运行时，专注于构建桌面应用，同时使用 `web` 页面来作为应用的 `GUI` ，你可以将其看作是一个由 `JavaScript` 控制的迷你版的 `Chromium` 浏览器。

- 主进程（Main Process）和渲染进程（Render Process）
- Electron 内集成了 Nodejs，大大的方便了开发, 渲染进程也有了操作系统底层 API 的能力，Nodejs 中常用的 Path、fs、Crypto 等模块在 Electron 可以直接使用，方便我们处理链接、路径、文件 MD5 等，同时 npm 还有成千上万的模块供我们选择。
- Electron 使用 Chromium 来展示 web 页面，也就是我们开发只需要兼容 Chromium 浏览器即可，也就是说好多属性可以肆无忌惮的用：播放语音直接使用 HTML5 audio、大量数据存储使用数据库 IndexedDB、难搞的布局直接使用 Flexbox、方便的检测在线离线等等

## 深浅拷贝

> **浅拷贝**只复制指向某个对象的指针，而不复制对象本身，新旧对象还是共享同一块内存。但**深拷贝**会另外创造一个一模一样的对象，新对象跟原对象不共享内存，修改新对象不会改到原对象

### 浅拷贝

- `Object.assign`
- ES6 `...` 展开运算符

### 深拷贝

- 可以通过 `JSON.parse(JSON.stringify(object))` 来解决

```js
let a = {
  age: 1,
  jobs: {
    first: 'FE'
  }
}
let b = JSON.parse(JSON.stringify(a))
a.jobs.first = 'native'
console.log(b.jobs.first) // FE
```

- 函数库 `lodash` ，也有提供 `_.cloneDeep`

## 防抖和节流

> 防抖动是将**多次执行变为最后一次执行**，节流是将**多次执行变成每隔一段时间执行**

## 大概描述 this

> `this` ，函数执行的上下文，可以通过 `apply，call，bind` 改变 `this` 的指向。对于匿名函数或者直接调用的函数来说， `this` 指向全局上下文（浏览器为 `window，NodeJS` 为 `global` ），剩下的函数调用，那就是谁调用它， `this` 就指向谁。当然还有 `es6` 的箭头函数，箭头函数的指向取决于该箭头函数声明的位置，在哪里声明， `this` 就指向哪里

## 现在要你完成一个 Dialog 组件，说说你设计的思路？它应该有什么功能

- 该组件需要提供 hook 指定渲染位置，默认渲染在 body 下面。
- 然后改组件可以指定外层样式，如宽度等
- 组件外层还需要一层 mask 来遮住底层内容，点击 mask 可以执行传进来的 onCancel 函数关闭 Dialog。
- 另外组件是可控的，需要外层传入 visible 表示是否可见。
- 然后 Dialog 可能需要自定义头 head 和底部 footer，默认有头部和底部，底部有一个确认按钮和取消按钮，确认按钮会执行外部传进来的 onOk 事件，然后取消按钮会执行外部传进来的 onCancel 事件。
- 当组件的 visible 为 true 时候，设置 body 的 overflow 为 hidden，隐藏 body 的滚动条，反之显示滚动条。
- 组件高度可能大于页面高度，组件内部需要滚动条。
- 只有组件的 visible 有变化且为 ture 时候，才重渲染组件内的所有内容

## 冒泡排序

- 每次比较相邻的两个数，如果后一个比前一个小，换位置

```js
let arr = [3, 1, 4, 6, 5, 7, 2]

function bubbleSort(arr) {
  for (let i = 0; i < arr.length - 1; i++) {
    for (let j = 0; j < arr.length - i - 1; j++) {
      if (arr[j + 1] < arr[j]) {
        let temp
        temp = arr[j]
        arr[j] = arr[j + 1]
        arr[j + 1] = temp
      }
    }
  }
  return arr
}

console.log(bubbleSort(arr))
```

## 快速排序

- 采用二分法，取出中间数，数组每次和中间数比较，小的放到左边，大的放到右边

```js
let arr = [3, 1, 4, 6, 5, 7, 2]

function quickSort(arr) {
  if (arr.length == 0) {
    return [] // 返回空数组
  }

  let cIndex = Math.floor(arr.length / 2)
  let c = arr.splice(cIndex, 1)
  let left = []
  let right = []

  for (let i = 0; i < arr.length; i++) {
    if (arr[i] < c) {
      left.push(arr[i])
    } else {
      right.push(arr[i])
    }
  }

  return quickSort(left).concat(c, quickSort(right))
}

console.log(quickSort(arr))
```

## 输出今天的日期

```js
// '2019-09-08'
let d = new Date()
// 获取年，getFullYear()返回4位的数字
let year = d.getFullYear()
// 获取月，月份比较特殊，0是1月，11是12月
let month = d.getMonth() + 1
// 变成两位
month = month < 10 ? '0' + month : month
// 获取日
let day = d.getDate()
day = day < 10 ? '0' + day : day
alert(year + '-' + month + '-' + day)
```

## 用 js 实现随机选取 10–100 之间的 10 个数字，存入一个数组，并排序

```js
let arr = []
function getRandom(start, end) {
  let choice = end - start + 1
  return Math.floor(Math.random() * choice + start)
}
for (var i = 0; i < 10; i++) {
  arr.push(getRandom(10, 100))
}
arr.sort()
```

## 写一段 JS 程序提取 URL 中的各个 GET 参数

> 有这样一个 `URL：http://item.taobao.com/item.htm?a=1&b=2&c=&d=xxx&e` ，请写一段 JS 程序提取 URL 中的各个 GET 参数(参数名和参数个数不确定)，将其按 `key-value` 形式返回到一个 json 结构中，如 `{a:'1', b:'2', c:'', d:'xxx', e:undefined}`

```js
function getUrl(url){
  let result = {}
  url = url.split("?").[1]
  let map = url.split("&")
  for(let i = 0, len = map.length; i < len; i++){
    result[map[i].split("=")[0]]=map[i].split("=").[1]
  }
  return result
}
```

## 实现一个函数，判断输入是不是回文字符串

```js
function run(input) {
  if (typeof input !== 'string') return false
  return (
    input
      .split('')
      .reverse()
      .join('') === input
  )
}
```

## 负载均衡

> 多台服务器共同协作，不让其中某一台或几台超额工作，发挥服务器的最大作用，提高性能和可靠性

- Nginx 的负载均衡：
  - 负载：就是 Nginx 接受请求
  - 均衡：Nginx 将收到的请求按照一定的规则分发到不同的服务器进行处理

### 正向代理

客户端想要访问一个服务器，但是它可能无法直接访问这台服务器，这时候这可找一台可以访问目标服务器的另外一台服务器，而这台服务器就被当做是代理人的角色 ，称之为代理服务器，于是客户端把请求发给代理服务器，由代理服务器获得目标服务器的数据并返回给客户端。**客户端是清楚目标服务器的地址的，而目标服务器是不清楚来自客户端，它只知道来自哪个代理服务器，所以正向代理可以屏蔽或隐藏客户端的信息**。

### 反向代理

从上面的正向代理，你会大概知道代理服务器是为客户端作代理人，它是站在客户端这边的。其实反向代理就是代理服务器为服务器作代理人，站在服务器这边，它就是对外屏蔽了服务器的信息，常用的场景就是多台服务器分布式部署，像一些大的网站，由于访问人数很多，就需要多台服务器来解决人数多的问题，这时这些服务器就由一个反向代理服务器来代理，**客户端发来请求，先由反向代理服务器，然后按一定的规则分发到明确的服务器，而客户端不知道是哪台服务器**。常常用 nginx 来作反向代理。

## CDN (content delivery network) 内容分发网络

- CDN 使用户就近获取所需内容，降低网络拥塞，提高用户访问响应速度和命中率; 空间换时间思想
