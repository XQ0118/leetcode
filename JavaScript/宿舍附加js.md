# 实验室忘记 push 了

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
var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
arr.sort(function() {
  return Math.random() - 0.5;
});
console.log(arr);
```

## 如何渲染几万条数据并不卡住界面

> 不能一次性将几万条都渲染出来，而应该一次渲染部分 `DOM` ，那么就可以通过 `requestAnimationFrame` 来每 `16 ms` 刷新一次

```js
setTimeout(() => {
  // 插入十万条数据
  const total = 100000;
  // 一次插入 20 条，如果觉得性能不好就减少
  const once = 20;
  // 渲染数据总共需要几次
  const loopCount = total / once;
  let countOfRender = 0;
  let ul = document.querySelector("ul");
  function add() {
    // 优化性能，插入不会造成回流
    const fragment = document.createDocumentFragment();
    for (let i = 0; i < once; i++) {
      const li = document.createElement("li");
      li.innerText = Math.floor(Math.random() * total);
      fragment.appendChild(li);
    }
    ul.appendChild(fragment);
    countOfRender += 1;
    loop();
  }
  function loop() {
    if (countOfRender < loopCount) {
      window.requestAnimationFrame(add);
    }
  }
  loop();
}, 0);
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
createDocumentFragment(); //创建一个DOM片段
createElement(); //创建一个具体的元素
createTextNode(); //创建一个文本节点
```

### 添加、移除、替换、插入 DOM 节点

```js
appendChild(); //添加
removeChild(); //移除
replaceChild(); //替换
insertBefore(); //插入
```

### 查找节点

```js
getElementsByTagName(); //通过标签名称
getElementsByClassName(); //通过元素的class的值
getElementById(); //通过元素Id，唯一性
```

## 数组去重方法

- ES6 Set 方法

  ```js
  let arr = [1, 1, 2, 2, 3, 3];
  arr = [...new Set(arr)];
  console.log(arr); // [1, 2, 3]
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
    first: "FE"
  }
};
let b = JSON.parse(JSON.stringify(a));
a.jobs.first = "native";
console.log(b.jobs.first); // FE
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
let arr = [3, 1, 4, 6, 5, 7, 2];

function bubbleSort(arr) {
  for (let i = 0; i < arr.length - 1; i++) {
    for (let j = 0; j < arr.length - i - 1; j++) {
      if (arr[j + 1] < arr[j]) {
        let temp;
        temp = arr[j];
        arr[j] = arr[j + 1];
        arr[j + 1] = temp;
      }
    }
  }
  return arr;
}

console.log(bubbleSort(arr));
```

## 快速排序

- 采用二分法，取出中间数，数组每次和中间数比较，小的放到左边，大的放到右边

```js
let arr = [3, 1, 4, 6, 5, 7, 2];

function quickSort(arr) {
  if (arr.length == 0) {
    return []; // 返回空数组
  }

  let cIndex = Math.floor(arr.length / 2);
  let c = arr.splice(cIndex, 1);
  let left = [];
  let right = [];

  for (let i = 0; i < arr.length; i++) {
    if (arr[i] < c) {
      left.push(arr[i]);
    } else {
      right.push(arr[i]);
    }
  }

  return quickSort(left).concat(c, quickSort(right));
}

console.log(quickSort(arr));
```

## 输出今天的日期

```js
// '2019-09-08'
let d = new Date();
// 获取年，getFullYear()返回4位的数字
let year = d.getFullYear();
// 获取月，月份比较特殊，0是1月，11是12月
let month = d.getMonth() + 1;
// 变成两位
month = month < 10 ? "0" + month : month;
// 获取日
let day = d.getDate();
day = day < 10 ? "0" + day : day;
alert(year + "-" + month + "-" + day);
```

## 用 js 实现随机选取 10–100 之间的 10 个数字，存入一个数组，并排序

```js
let arr = [];
function getRandom(start, end) {
  let choice = end - start + 1;
  return Math.floor(Math.random() * choice + start);
}
for (var i = 0; i < 10; i++) {
  arr.push(getRandom(10, 100));
}
arr.sort();
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
  if (typeof input !== "string") return false;
  return (
    input
      .split("")
      .reverse()
      .join("") === input
  );
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
