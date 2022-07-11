[手写 Promise 原理，最通俗易懂的版本](https://mp.weixin.qq.com/s/w5RF1LKkX1F77CQQgR4pnA)
[带你复习 50 个 JavaScript「基础」知识点](https://mp.weixin.qq.com/s/-VLnevAo5jay_8t_9vvWhg)
[复习 50 个 JavaScript「进阶」知识点](https://mp.weixin.qq.com/s/sSbDY0qDXoBF95QBMgzJsA)

#### 一、闭包

一个函数和其周围状态（lexical environment，词法环境）的引用捆绑在一起（或者说函数被引用包围），这样的组合就是闭包（closure）。也就是说，闭包让你可以在一个内层函数中访问到其外层函数的作用域。在 JavaScript 中，每当创建一个函数，闭包就会在函数创建的同时被创建出来。

```js
function makeFunc() {
  var name = "Mozilla";
  function displayName() {
    alert(name);
  }
  return displayName;
}
var myFunc = makeFunc();
myFunc();
```

简单理解：

- 闭包基本上就是一个函数内部返回一个函数；
- 闭包就是能够读取其他函数内部变量的函数；

好处：

- 1.可以读取函数内部的变量；
- 2.将变量始终保持在内存中；
- 3.可以封装对象的私有属性和私有方法；

坏处：

- 1.比较耗费内存、使用不当会造成内存溢出的问题；

#### 二、this 的理解

```
在面向对象语言中 this 表示当前对象的一个引用。
但在 JavaScript 中 this 不是固定不变的，它会随着执行环境的改变而改变。
this对象是在运行时基于函数的执行环境绑定的：在全局函数中，this等于window，而当函数被作为某个对象的方法调用时，this等于那个对象。不过，匿名函数具有全局性，因此this对象同常指向window
```

- 1.如果一个函数作为一个对象的方法来调用时，this 指向这个对象；
  obj.fun() fun 中的 this->obj ，自动指向当前的对象。
- 2.当一个函数不是一个对象的属性时，直接作为函数来调用时，this 指向全局对象;
  fun() fun 中的 this 默认->window，函数内部的 this，this 默认是指向 window
- 3.匿名函数自调用时，this 指向全局对象;
- 4.如果一个函数用 new 调用时，函数执行前会新创建一个对象，this 指向这个新创建的对象;
  new Fun() Fun 中的 this->正在创建的新对象，new 改变了函数内部的 this 指向，导致 this 指向实例化 new 的对象
- 5.apply 、 call 和 bind 调用模式，这三个方法都可以显示的指定调用函数的 this 指向;
  其中 apply 方法接收两个参数：一个是 this 绑定的对象，一个是参数数组。
  call 方法接收的参数，第一个是 this 绑定的对象，后面的其余参数是传入函数执行的参数。也就是说，在使用 call() 方法时，传递给函数的参数必须逐个列举出来。
  bind 方法通过传入一个对象，返回一个 this 绑定了传入对象的新函数。
  这个函数的 this 指向除了使用 new 时会被改变，其他情况下都不会改变。
- 6.回调函数中的 this 指向;

```js
var Bob = {
  sname: "鲍勃",
  friends: ["Jack", "Rose", "Tom", "Jerry"],
  intr() {
    this.friends.forEach(function (ele) {
      console.log(this.sname + "认识" + ele);
    });
  },
};
Bob.intr();
```

看结果：
undefined 认识 Jack
undefined 认识 Rose
undefined 认识 Tom
undefined 认识 Jerry
回调函数中的 this 默认是指向 window 的，因为本质上是在函数内 callback,并没有当前的对象调用

```js
var Bob = {
  sname: "鲍勃",
  friends: ["Jack", "Rose", "Tom", "Jerry"],
  intr() {
    this.friends.forEach((ele) => {
      console.log(this.sname + "认识" + ele);
    });
  },
};
Bob.intr();
```

结果是：
鲍勃认识 Jack
鲍勃认识 Rose
鲍勃认识 Tom
鲍勃认识 Jerry
可以看出箭头函数内的 this 自动指向了回调函数外层的 this 。
箭头函数中的 this:
函数体内的 this 对象，就是定义时所在的对象，而不是使用时所在的对象。
this 指向的固定化，并不是因为箭头函数内部有绑定 this 的机制，实际原因是箭头函数根本没有自己的 this，导致内部的 this 就是外层代码块的 this。正是因为它没有 this，所以也就不能用作构造函数。

#### 三、 谈谈你对 webpack 的看法

```
我认为 webpack 的主要原理是，它将所有的资源都看成是一个模块，并且把页面逻辑当作一个整体，通过一个给定的入口文件，webpack 从这个文件开始，找到所有的依赖文件，将各个依赖文件模块通过 loader 和 plugins 处理后，然后打包在一起，最后输出一个浏览器可识别的 JS 文件。

Webpack 具有四个核心的概念，分别是 Entry（入口）、Output（输出）、loader 和 Plugins（插件）。

Entry 是 webpack 的入口起点，它指示 webpack 应该从哪个模块开始着手，来作为其构建内部依赖图的开始。

Output 属性告诉 webpack 在哪里输出它所创建的打包文件，也可指定打包文件的名称，默认位置为 ./dist。

loader 可以理解为 webpack 的编译器，它使得 webpack 可以处理一些非 JavaScript 文件。在对 loader 进行配置的时候，test 属性，标志有哪些后缀的文件应该被处理，是一个正则表达式。use 属性，指定 test 类型的文件应该使用哪个 loader 进行预处理。常用的 loader 有 css-loader、style-loader 等。

插件可以用于执行范围更广的任务，包括打包、优化、压缩、搭建服务器等等，要使用一个插件，一般是先使用 npm 包管理器进行安装，然后在配置文件中引入，最后将其实例化后传递给 plugins 数组属性。
```

#### 四、 异步编程的实现方式？

相关资料：

```
回调函数
优点：简单、容易理解
缺点：不利于维护，代码耦合高

事件监听（采用时间驱动模式，取决于某个事件是否发生）：
优点：容易理解，可以绑定多个事件，每个事件可以指定多个回调函数
缺点：事件驱动型，流程不够清晰

发布/订阅（观察者模式）
类似于事件监听，但是可以通过‘消息中心’，了解现在有多少发布者，多少订阅者

Promise 对象
优点：可以利用 then 方法，进行链式写法；可以书写错误时的回调函数；
缺点：编写和理解，相对比较难

Generator 函数
优点：函数体内外的数据交换、错误处理机制
缺点：流程管理不方便

async 函数
优点：内置执行器、更好的语义、更广的适用性、返回的是 Promise、结构清晰。
缺点：错误处理机制
```

回答：

```
js 中的异步机制可以分为以下几种：

第一种最常见的是使用回调函数的方式，使用回调函数的方式有一个缺点是，多个回调函数嵌套的时候会造成回调函数地狱，上下两层的回调函数间的代码耦合度太高，不利于代码的可维护。

第二种是 Promise 的方式，使用 Promise 的方式可以将嵌套的回调函数作为链式调用。但是使用这种方法，有时会造成多个 then 的链式调用，可能会造成代码的语义不够明确。

第三种是使用 generator 的方式，它可以在函数的执行过程中，将函数的执行权转移出去，在函数外部我们还可以将执行权转移回来。当我们遇到异步函数执行的时候，将函数执行权转移出去，当异步函数执行完毕的时候我们再将执行权给转移回来。因此我们在 generator 内部对于异步操作的方式，可以以同步的顺序来书写。使用这种方式我们需要考虑的问题是何时将函数的控制权转移回来，因此我们需要有一个自动执行 generator 的机制，比如说 co 模块等方式来实现 generator 的自动执行。

第四种是使用 async 函数的形式，async 函数是 generator 和 promise 实现的一个自动执行的语法糖，它内部自带执行器，当函数内部执行到一个 await 语句的时候，如果语句返回一个 promise 对象，那么函数将会等待 promise 对象的状态变为 resolve 后再继续向下执行。因此我们可以将异步逻辑，转化为同步的顺序来书写，并且这个函数可以自动执行。
```

#### 五、原型，原型链？

```
在 Javascript 中，每个函数都有一个原型属性 prototype 指向自身的原型，而由这个函数创建的对象也有一个 proto 属性指向这个原型，而函数的原型是一个对象，所以这个对象也会有一个 proto 指向自己的原型，这样逐层深入直到 Object 对象的原型，这样就形成了原型链。
```

```
在 js 中我们是使用构造函数来新建一个对象的，每一个构造函数的内部都有一个 prototype 属性值，这个属性值是一个对象，这个对象包含了可以由该构造函数的所有实例共享的属性和方法。当我们使用构造函数新建一个对象后，在这个对象的内部将包含一个指针，这个指针指向构造函数的 prototype 属性对应的值，在 ES5 中这个指针被称为对象的原型。一般来说我们是不应该能够获取到这个值的，但是现在浏览器中都实现了 **proto** 属性来让我们访问这个属性，但是我们最好不要使用这个属性，因为它不是规范中规定的。ES5 中新增了一个 Object.getPrototypeOf() 方法，我们可以通过这个方法来获取对象的原型。

当我们访问一个对象的属性时，如果这个对象内部不存在这个属性，那么它就会去它的原型对象里找这个属性，这个原型对象又会有自己的原型，于是就这样一直找下去，也就是原型链的概念。原型链的尽头一般来说都是 Object.prototype 所以这就是我们新建的对象为什么能够使用 toString() 等方法的原因。

特点：

JavaScript 对象是通过引用来传递的，我们创建的每个新对象实体中并没有一份属于自己的原型副本。当我们修改原型时，与之相关的对象也会继承这一改变。
```

#### 六、作用域和作用域链？

```
在 Javascript 中，作用域分为 全局作用域 和 函数作用域
    全局作用域：
　　　　代码在程序的任何地方都能被访问，window 对象的内置属性都拥有全局作用域。
    函数作用域：
　　　　在固定的代码片段才能被访问,最常见的例如函数内部

作用域有上下级关系，上下级关系的确定就看函数是在哪个作用域下创建的。如上，fn作用域下创建了bar函数，那么“fn作用域”就是“bar作用域”的上级。
作用域最大的用处就是隔离变量，不同作用域下同名变量不会有冲突。
变量取值：到创建 这个变量 的函数的作用域中取值

作用域链:
　　一般情况下，变量取值到 创建 这个变量 的函数的作用域中取值。
　　但是如果在当前作用域中没有查到值，就会向上级作用域去查，直到查到全局作用域，这么一个查找过程形成的链条就叫做作用域链。
作用域链本质上是一个链表，链表存储了从当前执行环境到上级执行环境直至全局执行环境所关联的变量对象（所对应执行环境中定义的所有变量和函数都保存在这个对象中）。
详情参考下面链接：
```

详细资料可以参考：
[《JavaScript 关于作用域、作用域链和闭包的理解》](https://blog.csdn.net/whd526/article/details/70990994)

#### 七、js 延迟加载的方式有哪些？

相关知识点：

```
js 延迟加载，也就是等页面加载完成之后再加载 JavaScript 文件。 js 延迟加载有助于提高页面加载速度。
```

一般有以下几种方式：

- defer 属性
- async 属性
- 动态创建 DOM 方式
- 使用 setTimeout 延迟方法
- 让 JS 最后加载

回答：

```
js 的加载、解析和执行会阻塞页面的渲染过程，因此我们希望 js 脚本能够尽可能的延迟加载，提高页面的渲染速度。

我了解到的几种方式是：

第一种方式是我们一般采用的是将 js 脚本放在文档的底部，来使 js 脚本尽可能的在最后来加载执行。

第二种方式是给 js 脚本添加 defer 属性，这个属性会让脚本的加载与文档的解析同步解析，然后在文档解析完成后再执行这个脚本文件，这样的话就能使页面的渲染不被阻塞。多个设置了 defer 属性的脚本按规范来说最后是顺序执行的，但是在一些浏览器中可能不是这样。

第三种方式是给 js 脚本添加 async 属性，这个属性会使脚本异步加载，不会阻塞页面的解析过程，但是当脚本加载完成后立即执行 js 脚本，这个时候如果文档没有解析完成的话同样会阻塞。多个 async 属性的脚本的执行顺序是不可预测的，一般不会按照代码的顺序依次执行。

第四种方式是动态创建 DOM 标签的方式，我们可以对文档的加载事件进行监听，当文档加载完成后再动态的创建 script 标签来引入 js 脚本。
```

#### 八、数组(array)方法

```
map : 遍历数组，返回回调返回值组成的新数组
forEach : 无法 break ，可以用 try/catch 中 throw new Error 来停止
filter : 过滤
some : 有一项返回 true ，则整体为 true
every : 有一项返回 false ，则整体为 false
join : 通过指定连接符生成字符串
push / pop : 末尾推入和弹出，改变原数组， 返回推入/弹出项
unshift / shift : 头部推入和弹出，改变原数组，返回操作项
sort(fn) / reverse : 排序与反转，改变原数组
concat : 连接数组，不影响原数组， 浅拷贝
slice(start, end) : 返回截断后的新数组，不改变原数组
splice(start,number,value…): 返回删除元素组成的数组，value 为插入项，改变原数组
indexOf / lastIndexOf(value, fromIndex) : 查找数组项，返回对应的下标
reduce / reduceRight(fn(prev, cur) ，defaultPrev) : 两两执行，prev 为上次化简函数的return 值，cur 为当前值(从第二项开始)
```

#### 九、图片的懒加载和预加载

相关知识点：

```
预加载：提前加载图片，当用户需要查看时可直接从本地缓存中渲染。

懒加载：懒加载的主要目的是作为服务器前端的优化，减少请求数或延迟请求数。

两种技术的本质：两者的行为是相反的，一个是提前加载，一个是迟缓甚至不加载。 懒加载对服务器前端有一定的缓解压力作用，预加载则会增加服务器前端压力。
```

回答：

```
懒加载也叫延迟加载，指的是在长网页中延迟加载图片的时机，当用户需要访问时，再去加载，这样可以提高网站的首屏加载速度，提升用户的体验，并且可以减少服务器的压力。它适用于图片很多，页面很长的电商网站的场景。懒加载的实现原理是，将页面上的图片的 src 属性设置为空字符串，将图片的真实路径保存在一个自定义属性中，当页面滚动的时候，进行判断，如果图片进入页面可视区域内，则从自定义属性中取出真实路径赋值给图片的 src 属性，以此来实现图片的延迟加载。

预加载指的是将所需的资源提前请求加载到本地，这样后面在需要用到时就直接从缓存取资源。通过预加载能够减少用户的等待时间，提高用户的体验。我了解的预加载的最常用的方式是使用 js 中的 image 对象，通过为 image 对象来设置 scr 属性，来实现图片的预加载。

这两种方式都是提高网页性能的方式，两者主要区别是一个是提前加载，一个是迟缓甚至不加载。懒加载对服务器前端有一定的缓解压力作用，预加载则会增加服务器前端压力。
```

详细资料可以参考：
[《懒加载和预加载》](https://juejin.im/post/5b0c3b53f265da09253cbed0)
[《网页图片加载优化方案》](https://juejin.im/entry/5a73f38cf265da4e99575be3)
[《基于用户行为的图片等资源预加载》](https://www.zhangxinxu.com/wordpress/2016/06/image-preload-based-on-user-behavior/)

#### 十、let 和 const 的注意点？

- 1.声明的变量只在声明时的代码块内有效
- 2.不存在声明提升
- 3.存在暂时性死区，如果在变量声明前使用，会报错
- 4.不允许重复声明，重复声明会报错

#### 十一、什么是 Promise 对象，什么是 Promises/A+ 规范？

```
Promise 对象是异步编程的一种解决方案，最早由社区提出。Promises/A+ 规范是 JavaScript Promise 的标准，规定了一个 Promise 所必须具有的特性。

Promise 是一个构造函数，接收一个函数作为参数，返回一个 Promise 实例。一个 Promise 实例有三种状态，分别是 pending、resolved 和 rejected，分别代表了进行中、已成功和已失败。实例的状态只能由 pending 转变 resolved 或者 rejected 状态，并且状态一经改变，就凝固了，无法再被改变了。状态的改变是通过 resolve() 和 reject() 函数来实现的，我们可以在异步操作结束后调用这两个函数改变 Promise 实例的状态，它的原型上定义了一个 then 方法，使用这个 then 方法可以为两个状态的改变注册回调函数。这个回调函数属于微任务，会在本轮事件循环的末尾执行。
```

详细资料可以参考：
[《Promises/A+ 规范》](http://www.ituring.com.cn/article/66566)
[《Promise》](http://es6.ruanyifeng.com/#docs/promise#Promise-resolve)
[《Promise》](https://www.jianshu.com/p/1b63a13c2701)

#### 十二、内存泄漏

```
定义：程序中己动态分配的堆内存由于某种原因程序未释放或无法释放引发的各种问题。
js 中可能出现的内存泄漏结果：变慢，崩溃，延迟大等
js 中可能出现的内存泄漏原因：
    全局变量；
    dom 清空时，还存在引用；
    定时器未清除；
    子元素存在引起的内存泄露；
    闭包
```

#### 十三、JS 垃圾回收机制是怎样的？

```
1.概述
js 的垃圾回收机制是为了防止内存泄漏（已经不需要的某一块内存还一直存在着），垃圾回收机制就是不停歇的寻找这些不再使用的变量，并且释放掉它所指向的内存。
在 JS 中，JS 的执行环境会负责管理代码执行过程中使用的内存。
2.变量的生命周期
当一个变量的生命周期结束之后，它所指向的内存就会被释放。js 有两种变量，局部变量和全局变量，局部变量是在他当前的函数中产生作用，当该函数结束之后，该变量内存会被释放，全局变量的话会一直存在，直到浏览器关闭为止。
3.js 垃圾回收方式
有两种方式： 标记清除、引用计数
标记清除：大部分浏览器使用这种垃圾回收，当变量进入执行环境(声明变量）的时候，垃圾回收器将该变量进行了标记，当该变量离开环境的时候，将其再度标记，随之进行删除。
引用计数：这种方式常常会引起内存的泄露，主要存在于低版本的浏览器。它的机制就是跟踪某一个值得引用次数，当声明一个变量并且将一个引用类型赋值给变量得时候引用次数加 1，当这个变量指向其他一个时引用次数减 1，当为 0 时出发回收机制进行回收。
```

#### 十四、Hash 和 History 路由的区别和优缺点？

```

    Hash路由模式的实现主要是基于以下几个特征：

1. url 中 hash 值只是客户端的一种状态，也就是说当向服务器端发出请求时，hash 部分不会被发送；
2. hash 值的改变，都会在浏览器的访问历史中增加一个记录。因此我们通过浏览器的回退，前进按钮控制 hash 的切换；
3. 可以通过 a 标签，并设置 href 属性，当用户点击这个标签后，URL 的 hash 值会发生改变；或者使用 javaScript 来对 location.hash 进行赋值，改变 url 的 hash 值；
4. 我们可以使用 hashchange 事件来监听 hash 值的变化，从而对页面进行跳转（渲染）
   history 路由模式的实现主要基于存在下面几个特征：
   1） pushState 和 replaceState 两个 API 来操作实现 URL 的变化；
   2） 我们可以使用 popstate 事件来监听 url 的变化，从而对页面进行跳转（渲染）;
   3） history.pushState()或者 history.replaceState()不会触发 popstate 事件，这是我们需要手动触发页面跳转（渲染）

```

#### 十五、Symbol 有了解吗？

```
Symbol
是 ES6 的特性，具体使用场景有：

充当对象的属性名，实现私有属性
充当变量，实现单独变量
用来定义类里的私有属性
```

#### 十六、迭代器有了解吗，哪些是可迭代的

```
迭代器：Iterator，可迭代对象有Array、Set、Map，想将不可迭代对象变成可迭代对象，可以设置Symbol.iterator属性
```

```js
const t = {
  name: "林三心",
  age: 12,
};

t[Symbol.iterator] = function () {
  let index = 0,
    self = this,
    keys = Object.keys(this);

  return {
    next() {
      if (index < keys.length) {
        return {
          value: self[keys[index++]],
          done: false,
        };
      } else {
        return {
          value: undefined,
          done: true,
        };
      }
    },
  };
};
for (let value of t) {
  console.log(value);
}
```

#### 十七、for in 与 for-of

```
for in 的特点：
for ... in 循环返回的值都是数据结构的 键值名。
遍历对象返回的对象的 key 值,遍历数组返回的数组的下标(key)。
for ... in 循环不仅可以遍历数字键名,还会遍历原型上的值和手动添加的其他键。特别情况下, for ... in 循环会以任意的顺序遍历键名
总结一句: for in 循环特别适合遍历对象。
for of 特点：
for of 循环用来获取一对键值对中的值,而 for in 获取的是 键名
一个数据结构只要部署了 Symbol.iterator 属性, 就被视为具有 iterator 接口, 就可以使用 for of 循环。
for of 不同与 forEach, 它可以与 break、continue 和 return 配合使用,也就是说 for of 循环可以随时退出循环。
提供了遍历所有数据结构的统一接口
哪些数据结构部署了 Symbol.iteratoer 属性了呢?
只要有 iterator 接口的数据结构,都可以使用 for of 循环。
数组 Array
Map
Set
String
arguments 对象
Nodelist 对象, 就是获取的 dom 列表集合
以上这些都可以直接使用 for of 循环。 凡是部署了 iterator 接口的数据结构也都可以使用数组的 扩展运算符(...)、和解构赋值等操作。
```

#### 十八、null 和 undefined 的异同点有哪些？

```
相同点
1、都是空类型
2、转布尔值都是 false，都是假值
3、null == undefined 为 true
不同点
1、typeof，前者为 object，后者为 undefined
2、null 转数字为 0，undefined 转数字为 NaN
3、null === undefined 为 false
```

#### 十九、绑定点击事件有几种方式？

```
三种
xxx.onclick = function (){}
<xxx onclick=""></xxx>
xxx.addEventListener('click', function(){}, false)
```

#### 二十、什么是事件委托？

```
当子元素都需要绑定相同事件时，可以将事件绑在父元素上，优点有：
绑定在父元素，则只需绑定一次，节省性能
后续新增的子元素也可以触发父元素绑定的事件
```

#### 二十一、JavaScript 有几种数据类型？

number：数字类型
string：字符串类型
boolean：布尔值类型
undefined：未定义类型
null：空类型
object：对象类型
symbol：symbol 类型
bigint：大数字类型

#### 二十二、undeclared 与 undefined 的区别？

undefined：声明了变量，但是没有赋值
undecalared：没有声明变量就直接使用
var a; //undefined
b; // b is not defined

#### 二十三、let & const 与 var 的区别？

- 1、var 存在变量提升，可重复声明同一变量，声明的变量均可改
- 2、let 没有变量提升，不可重复声明同一变量，声明的变量均可改
- 3、const 没有变量提升，不可重复声明同一变量，声明的基本数据类型不可改，引用类型可改属性，不可只声明变量而不赋值

#### 二十四、Object.defineProperty(target, key, options)，options 可传什么参数？

value：给 target[key]设置初始值
get：调用 target[key]时触发
set：设置 target[key]时触发
writable：默认 false，为 true 时此属性才能被赋值运算符修改
enumerable：默认 false，为 true 时此属性才能被枚举
configurable：默认为 false，为 true 时此属性的描述符才能被修改，才能被删除

#### 二十五、箭头函数与普通函数的区别？

1、箭头函数不能作为构造函数，不能 new
2、箭头函数没有自己的 this
3、箭头函数没有 arguments 对象
4、箭头函数没有原型对象

#### 二十六、Commonjs 和 ES6 Module 的区别？

1、前者是拷贝输出，后者是引用输出
2、前者可修改引入值，后者只读
3、前者是运行时，后者是编译时

#### 二十七、Ajax 是什么? 如何创建一个 Ajax？

相关知识点：

2005 年 2 月，AJAX 这个词第一次正式提出，它是 Asynchronous JavaScript and XML 的缩写，指的是通过 JavaScript 的异步通信，从服务器获取 XML 文档从中提取数据，再更新当前网页的对应部分，而不用刷新整个网页。

具体来说，AJAX 包括以下几个步骤。

- 1.创建 XMLHttpRequest 对象，也就是创建一个异步调用对象
- 2.创建一个新的 HTTP 请求，并指定该 HTTP 请求的方法、URL 及验证信息
- 3.设置响应 HTTP 请求状态变化的函数
- 4.发送 HTTP 请求
- 5.获取异步调用返回的数据
- 6.使用 JavaScript 和 DOM 实现局部刷新

一般实现：

```js
const SERVER_URL = "/server";

let xhr = new XMLHttpRequest();

// 创建 Http 请求
xhr.open("GET", SERVER_URL, true);

// 设置状态监听函数
xhr.onreadystatechange = function () {
  if (this.readyState !== 4) return;

  // 当请求成功时
  if (this.status === 200) {
    handle(this.response);
  } else {
    console.error(this.statusText);
  }
};

// 设置请求失败时的监听函数
xhr.onerror = function () {
  console.error(this.statusText);
};

// 设置请求头信息
xhr.responseType = "json";
xhr.setRequestHeader("Accept", "application/json");

// 发送 Http 请求
xhr.send(null);

// promise 封装实现：

function getJSON(url) {
  // 创建一个 promise 对象
  let promise = new Promise(function (resolve, reject) {
    let xhr = new XMLHttpRequest();

    // 新建一个 http 请求
    xhr.open("GET", url, true);

    // 设置状态的监听函数
    xhr.onreadystatechange = function () {
      if (this.readyState !== 4) return;

      // 当请求成功或失败时，改变 promise 的状态
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(new Error(this.statusText));
      }
    };

    // 设置错误监听函数
    xhr.onerror = function () {
      reject(new Error(this.statusText));
    };

    // 设置响应的数据类型
    xhr.responseType = "json";

    // 设置请求头信息
    xhr.setRequestHeader("Accept", "application/json");

    // 发送 http 请求
    xhr.send(null);
  });

  return promise;
}
```

回答：

```
我对 ajax 的理解是，它是一种异步通信的方法，通过直接由 js 脚本向服务器发起 http 通信，然后根据服务器返回的数据，更新网页的相应部分，而不用刷新整个页面的一种方法。

创建一个 ajax 有这样几个步骤

首先是创建一个 XMLHttpRequest 对象。

然后在这个对象上使用 open 方法创建一个 http 请求，open 方法所需要的参数是请求的方法、请求的地址、是否异步和用户的认证信息。

在发起请求前，我们可以为这个对象添加一些信息和监听函数。比如说我们可以通过 setRequestHeader 方法来为请求添加头信息。我们还可以为这个对象添加一个状态监听函数。一个 XMLHttpRequest 对象一共有 5 个状态，当它的状态变化时会触发onreadystatechange 事件，我们可以通过设置监听函数，来处理请求成功后的结果。当对象的 readyState 变为 4 的时候，代表服务器返回的数据接收完成，这个时候我们可以通过判断请求的状态，如果状态是 2xx 或者 304 的话则代表返回正常。这个时候我们就可以通过 response 中的数据来对页面进行更新了。

当对象的属性和监听函数设置完成后，最后我们调用 sent 方法来向服务器发起请求，可以传入参数作为发送的数据体。
```

详细资料可以参考：
[《XMLHttpRequest 对象》](https://wangdoc.com/javascript/bom/xmlhttprequest.html)
[《从 ajax 到 fetch、axios》](https://juejin.im/post/5acde23c5188255cb32e7e76)
[《Fetch 入门》](https://juejin.im/post/5c160937f265da61180199b2)
[《传统 Ajax 已死，Fetch 永生》](https://segmentfault.com/a/1190000003810652)

#### 二十八、 Ajax 解决浏览器缓存问题？

- 1.在 ajax 发送请求前加上 anyAjaxObj.setRequestHeader("If-Modified-Since","0")。

- 2.在 ajax 发送请求前加上 anyAjaxObj.setRequestHeader("Cache-Control","no-cache")。

- 3.在 URL 后面加上一个随机数： "fresh=" + Math.random();。

- 4.在 URL 后面加上时间戳："nowtime=" + new Date().getTime();。

- 5.如果是使用 jQuery，直接这样就可以了\$.ajaxSetup({cache:false})。这样页面的所有 ajax 都会执行这条语句就是不需要保存缓存记录。

详细资料可以参考：
[《Ajax 中浏览器的缓存问题解决方法》](https://www.cnblogs.com/cwzqianduan/p/8632009.html)
[《浅谈浏览器缓存》](https://segmentfault.com/a/1190000012573337)

#### 二十九、ajax 和 axios 的区别式什么？使用场景有哪些？

axios 和 ajax 的区别：

- 1、axios 是一个基于 Promise 的 HTTP 库，而 ajax 是对原生 XHR 的封装；
- 2、ajax 技术实现了局部数据的刷新，而 axios 实现了对 ajax 的封装。
  axios 和 ajax 的区别及优缺点：
  ajax:
- 1、什么是 ajax？
  Ajax 是对原生 XHR 的封装，为了达到我们跨域的目的，增添了对 JSONP 的支持。
  异步的 JavaScript 和 xml，Ajax 不是一门新技术，而是多种技术的组合，用于快速的创建动态页面，能够实现无刷新更新数据从而提高用户体验。
- 2、Ajax 的原理？
  由客户端请求 ajax 引擎，再由 ajax 引擎请求服务器，服务器作出一系列响应之后返回给 ajax 引擎，由 ajax 引擎决定将这个结果写入客户端的什么位置。实现页面无刷新更新数据。
- 3、核心对象？
  XMLHttpRequest
- 4、ajax 优缺点？
  优点：
  - 1、无刷新更新数据；
  - 2、异步与服务器通信；
  - 3、前端和后端负载平衡；
  - 4、基于标准被广泛支持；
  - 5、界面与应用分离
    缺点：
  - 1、ajax 不能使用 Back 和 history 功能，即对浏览器机制的破坏；
  - 2、安全问题 ajax 暴露了服务器交互的细节；
  - 3、对受索引擎的支持比较弱；
  - 4、破坏程序的异常处理机制；
  - 5、违背 URL 和资源定位的初衷；
  - 6、ajax 不能很好的支持移动设备；
  - 7、太多客户端代码造成开发上的成本。
- 5、Ajax 适用场景
  - 1、表单驱动的交互；
  - 2、深层次的树的导航；
  - 3、快速的用户与用户间的交流响应；
  - 4、对数据进行过滤和操纵相关数据的场景；
  - 5、普通的文本输入提示和自动完成的场景
- 6、Ajax 不适应的场景
  - 1、部分简单的表单；
  - 2、搜索；
  - 3、基本的导航；
  - 4、替换大量的文本；
  - 5、对呈现的操纵。
- 7、ajax 请求的五个步骤：
  - 1、创建 XMLHttpRequest 异步对象；
  - 2、设置回调函数；
  - 3、使用 open 方法与服务器建立连接；
  - 4、向服务器发送数据；
  - 5、使用回调函数中针对不同的响应状态进行处理。
    axios:
- 1、axios 是什么？
  axios 是一个基于 Promise 的 HTTP 库，可以用在与浏览器和 node.js 中。
- 2、axios 有那些特性？
  - 1、在浏览器中创建 XMLHttpRequests；
  - 2、从 node.js 创建 http 请求；
  - 3、支持 Promise API；
  - 4、拦截请求和响应；
  - 5、转换请求数据和响应数据；
  - 6、取消请求；
  - 7、自动转换 JSON 数据；
  - 8、客户端支持防御 XSRF。
- 3、执行 get 请求，有两种方式

```js
//第一种方式：将参数直接写在url中
axios
  .get("getMainInfo?id=123")
  .then((res) => {
    console.log(res);
  })
  .catch((err) => {
    console.log(err);
  });

//第二种方式 将参数直接写在params中
axios
  .get("/getMainInfo", { params: { id: 123 } })
  .then((res) => {
    console.log(res);
  })
  .catch((err) => {
    console.log(err);
  });
```

- 4、执行 post 请求，注意执行 post 请求的入参，不需要写在 params 字段中，这个地方要注意与 get 请求的第二种方式进行区别

```js
axios
  .post("/getMainInfo", { id: 123 })
  .then((res) => {
    console.log(res);
  })
  .catch((err) => {
    console.log(err);
  });
```

#### 三十、async/await 对比 promise 的优缺点？

async/await 优点：
1、做到了真正的串行的同步写法，代码阅读相对容易；
2、对于条件语句和其他流程语句比较友好，可以直接写在判断条件里面

```js
function a() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(222);
    }, 2000);
    setTimeout(() => {
      reject(111);
    }, 1000);
  });
}
async function f() {
  try {
    if ((await a()) === 222) {
      console.log("yes,it is");
    } else {
      throw new Error("no,it is not");
    }
  } catch (err) {
    console.log(err);
  }
}
```

3、处理复杂流程时，在代码清晰度方面有优势；
async/await 缺点：
1、无法处理 promise 返回的 reject 对象，要借助 try...catch...；
2、用 await 可能会导致性能问题，因为 await 会阻塞代码，也许之后的异步代码并不依赖于前者，但仍然需要等待前者完成，导致代码失去并发性；
3、try...catch...内部的变量无法传递给下一个 try...catch...，promise 和 then/catch 内部定义的变量，能通过 then 链条的参数传递到下一个 then/catch，但是 async/await 的 try 内部的变量，如果用 let 和 const 定义则无法传递到下一个 try...catch...，只能在外层作用域先定义好。
promise 的一些问题：
1、一旦执行，无法中途取消，链式调用多个 then 中间不能随便跳出来；
2、错误无法在外部被捕捉到，只能在内部进行预判处理，如果不设置回调函数，promise 内部抛出的错误，不会反馈到外部；
3、promise 内部调用如何进行，检测起来很难，当处于 pending 状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将结束）。
