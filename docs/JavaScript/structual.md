# 结构化程序设计和执行过程

## 宏任务与微任务

1. 演示 Object-C 中的 JSContext，了解 JavaScript 引擎。
2. 事件循环是在 JavaScript 引擎之外的，由宿主（浏览器或 node 之类的）实现。

### 宏任务的执行

宏任务是宿主发起的任务，比如 `setTimeout(() => console.log("hello");, 100)` 执行过程如下：

1. JavaScript 引擎在执行到这个代码时，会告诉宿主：100 ms 后告诉我执行回调函数
2. 100 ms 后，宿主告诉 JavaScript 引擎， 100 ms 到了，你有条件的话执行下一个宏任务吧
3. JavaScript 引擎会先执行完当前宏任务中的所有微任务，执行完后才会去执行下一个宏任务中的微任务

宏任务的队列就是事件循环，是 JavaScript 的调用方去使用 JavaScript 的方式。宿主发送代码给 JavaScript 引擎，主要有 3 种方式：

- 普通 JavaScript 脚本

  脚本具有主动性的 JavaScript 代码段，是控制 JavaScript 引擎完成一定任务的代码。

  ```html
  <script>
    var a = 1;
  </script>
  ```

- 普通 JavaScript 模块

  ```html
  <script type="module">
    var a = 1;
  </script>
  ```

  ES6 引入。是被动性的 JavaScript 代码段，是等待被调用的库。包含 `import` 和 `export`：

  - import 声明

    - 不带 from

      `import "mod"; //引入一个模块`，保证了这个模块代码被执行，引用它的模块是无法获得它的任何信息的。

    - 带 from

      引入模块中的一部分信息，可以把它们变成本地的变量，变量实际上仍然可以受到原来模块的控制。分类:

      - `import x from "./a.js"` 引入模块中导出的默认值，这种可以和后两种结合使用
      - `import {a as x, modify} from "./a.js";` 引入模块中的变量
      - `import * as x from "./a.js"` 把模块中所有的变量以类似对象属性的方式引入

  - export 声明

    - 独立使用 export 声明
      - `export {a, b, c};`
    - 直接在声明型语句前添加 export 关键字
      - `export var a = 1;`
    - 跟 default 联合使用

      export default 表示导出一个默认变量值，它可以用于 function 和 class。导出的变量是没有名称的，可以使用 `import x from "./a.js"` 这样的语法，在模块中引入。

      export default 还支持一种语法，后面跟一个表达式：

      ```javascript
      var a = {};
      export default a;
      ```

      这里导出的是值，导出的就是普通变量 a 的值，以后 a 的变化与导出的值就无关了，修改变量 a，不会使得其他模块中引入的 default 值发生改变。

  脚本和模块的区别：

  - 是否包含 import 和 export
  - 浏览器引入脚本和模块，都会执行脚本和模块中的语句。脚本是侵入式的，也就是说，其中的变量会侵入到浏览器的 window 中。模块不会侵入。

- 函数

  ```html
  <script>
    setTimeout(foo, 1000); // JavaScript 引擎执行 foo 函数
  </script>
  ```

### 微任务的执行

JavaScript 引擎根据宿主给它的宏任务，一条一条去执行的代码就是微任务。

在 1 个宏任务中，JavaScript 的 Promise 还会产生异步代码，JavaScript 必须保证这些异步代码在一个宏观任务中完成，因此，每个宏任务中又包含了一个微任务队列。JavaScript 引擎中执行的都是宏任务中的微任务。

Promise 产生的异步执行会在当前微任务队列最后执行。异步执行的顺序:

1. 首先我们分析有多少个宏任务
2. 在每个宏任务中，分析有多少个微任务
3. 根据调用次序，确定宏任务中的微任务执行次序
4. 根据宏任务的触发规则和调用次序，确定宏任务的执行次序

为了更好得表达异步，引入了 async 和 await。async 函数必定返回 Promise，使用 await 来等待一个 Promise。

```javascript
function sleep(duration) {
  return new Promise(function (resolve, reject) {
    setTimeout(resolve, duration);
  });
}

async function foo() {
  console.log("a");
  await sleep(2000);
  console.log("b");
}
```
