# 语句

- Grammar
  - 简单语句
  - 复合语句
  - 声明语句
  - 预处理中变量提升
  - 指令序言
- Runtime
  - Completion Record
  - Lexical Environment

## 简单语句

- `ExpressionStatement` 表达式语句

  真正能干活的就只有表达式语句，其它语句的作用都是产生各种结构，来控制表达式语句执行，或者改变表达式语句的意义。表达式语句实际上就是一个表达式，它是由运算符连接变量或者直接量构成的。

  ```javascript
  a = 1 + 2;
  ```

- `EmptyStatement` 空语句

  空语句的存在仅仅是从语言设计完备性的角度考虑，允许插入多个分号而不抛出错误。

  ```text
  ;;;
  ```

- `DebuggerStatement` debugger 语句

  debugger 语句通知调试器在此断点。在没有调试器挂载时，它不产生任何效果。

  ```javascript
  debugger;
  ```

- `ThrowStatement` throw 语句

  throw 语句用于抛出异常。

  ```javascript
  throw new Error("error");
  ```

- `ContinueStatement` continue 语句

  continue 语句结束本次循环并继续循环。可以带标签。

  ```javascript
  continue;
  ```

- `BreakStatement` break 语句

  break 语句跳出循环语句或者 switch 语句。可以带标签。

  ```javascript
  break;
  ```

- `ReturnStatement` return 语句

  return 语句用于函数中，它终止函数的执行，并且指定函数的返回值。

  ```javascript
  return "OK";
  ```

## 复合语句

- `BlockStatement` 块语句

  块语句会产生块作用域。正常情况下，返回的 [[type]] 是 normal。如果块中有语句产生了非 normal 的 [[type]]，那么语句块中接下来的语句就不执行了。

  ```javascript
  {
    // ...
  }
  // [[type]]: normal
  // [[value]]: --
  // [[target]]: --
  ```

- `IFStatement` if 语句

  ```javascript
  if (a < 10) {
    //...
  } else if (a < 20) {
    //...
  } else {
    //...
  }
  ```

- `SwitchStatement` switch 语句

  ```javascript
  switch(num) {
    case 1:
        print 1;
        break;
    default:
        print 2;
        break;
  }
  ```

- `IterationStatement` 循环语句

  ```javascript
  // for 循环
  for (i = 0; i < 100; i++) console.log(i);

  // for in 循环。循环枚举对象的属性，这里体现了属性的 enumerable 特征
  let o = { a: 10, b: 20 };
  Object.defineProperty(o, "c", { enumerable: false, value: 30 });
  for (let p in o) console.log(p);

  // for of 循环。背后的机制是 iterator 机制
  for (let e of [1, 2, 3, 4, 5]) console.log(e);

  // for await of 循环
  for await (let e of [1, 2, 3, 4, 5]) console.log(e);

  // while 循环
  let a = 100;
  while (a--) {
    console.log("*");
  }

  //do while 循环
  let a = 101;
  do {
    console.log(a);
  } while (a < 100);
  ```

  所有 for 语句，都会和声明型语句产生关联。比如：

  ```javascript
  for (let i = 0; i < 100; i++) {
    console.log(i);
  }
  ```

  在上面的代码中，会产生两个作用域。也就是说，for 循环会额外产生一个作用域，类似于：

  ```javascript
  {
    let i = 0;
    {
      console.log(i);
    }
  }
  ```

- `WithStatement` with 语句

  with 语句会改变当前的作用域，**不推荐使用**。

  ```javascript
  let o = { a: 1, b: 2 };
  with (o) {
    console.log(a, b);
  }
  ```

- `LabelStatement` 标签语句

- `TryStatement` try 语句

  throw 抛出异常，try 捕获异常代码段，catch 用于处理异常，finally 用于清理。

  ```javascript
  try {
    throw new Error("error");
  } catch (e) {
    console.log(e);
  } finally {
    console.log("finally");
  }
  ```

## 声明语句

- `FunctionDeclaration`

  ```javascript
  // 函数声明
  function foo() {}

  // 函数表达式
  var o = function foo() {};
  ```

- `AsyncFunctionDeclaration`

  ```javascript
  // 异步函数声明
  async function foo() {}

  // 异步函数表达式
  var o = async function foo() {};
  ```

- `GeneratorDeclaration`

  ```javascript
  // generator 声明
  function* foo() {
    yield 1;
  }

  // generator 表达式
  var o = function* foo() {
    yield 1;
  };
  ```

- `AsyncGeneratorDeclaration`

  ```javascript
  // 异步 generator 声明
  async function* foo() {
    yield 1;
  }

  // 异步 generator 表达式
  var o = async function* foo() {
    yield 1;
  };
  ```

- `VariableStatement`

  ```javascript
  var a = 0;
  ```

- `ClassDeclaration`

  ```javascript
  // class 声明
  class foo() {}

  // class 表达式
  var o = class foo() {};
  ```

- `LexicalDeclaration`

  ```javascript
  let a = 0;
  const b = 0;
  ```

## 预处理中变量提升

JavaScript 执行前，会对脚本、模块和函数体中的语句进行预处理。预处理过程将会提前处理声明型语句，以确定其中变量的意义。变量提升就是其中一个比较重要的点。

### var 声明

var 声明在预处理阶段，不关心赋值的部分，只管在当前作用域声明这个变量，var 的作用能够穿透一切语句结构，它只认脚本、模块和函数体三种语法结构。

var 会穿透一切语句，比如穿透 if 语句:

```javascript
var a = 1;

function foo() {
  console.log(a);
  if (false) {
    var a = 2;
  }
}

foo();
/* 输出
undefined
*/
```

**大部分情况下，var 声明可以用 let 或 const 声明替代**。如果实在想用，可遵循:

1. 声明同时必定初始化
2. 尽可能在离使用位置近的地方声明
3. 不要在意重复声明

### function 声明

在全局（脚本、模块和函数体）情况下，function 声明表现跟 var 相似，**不同之处在于，function 声明不但在作用域中加入变量，还会给它赋值**。

```javascript
console.log(foo);
function foo() {}
/* 输出
function foo() {}
*/
```

function 声明出现在 if 等语句中的情况有点复杂，它仍然作用于脚本、模块和函数体级别，在预处理阶段，仍然会产生变量，但它不再被提前赋值：

```javascript
console.log(foo);
if (true) {
  function foo() {}
}
/* 输出
undefined
*/
```

### class 声明

```javascript
var c = 1;
function foo() {
  console.log(c);
  class c {}
}
foo();
/* 输出
抛出错误
*/
```

这说明，class 声明也是会被预处理的，它会在作用域中创建变量，并且要求访问它时抛出错误。

class 的声明作用不会穿透 if 等语句结构，所以只有写在全局环境才会有声明作用。

class 内部，可以使用 constructor 关键字来定义构造函数。还能定义 getter/setter 和方法。需要注意，class 默认内部的函数定义都是 `use strict` 模式的。

```javascript
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
  // Getter
  get area() {
    return this.calcArea();
  }
  // Method
  calcArea() {
    return this.height * this.width;
  }
}
```

### let 和 const 声明

let/const 和 class 有点像，也是会被预处理的，它会在作用域中创建变量，并且要求访问它时抛出错误。

```javascript
var c = 1;
function foo() {
  console.log(c);
  let c = 100;
}
foo();
/* 输出
抛出错误
*/
```

let 和 const 的作用范围是 if、for 等结构型语句。

## 指令序言

指令序言机制规定了一种给 JavaScript 代码添加元信息的方式。

`"use strict"` 是 JavaScript 标准中规定的唯一一种指令序言，只能出现在脚本、模块和函数体的最前面。但是设计指令序言的目的是留给 JavaScript 的引擎和实现者一些统一的表达方式，在静态扫描时可以指定 JavaScript 代码的一些特性。

```javascript
// 输出 null
"use strict";
function f() {
  console.log(this);
}
f.call(null);

// 输出 global
function f() {
  console.log(this);
}
f.call(null);
```

除了`"use strict"`之外，常见的还有 lint 检查指令。比如 eslint 中的 `/* eslint-disable */` 放在文件首行，表示该文件不进行 lint 检查。

## Completion Record

JavaScript 中用 Completion Record 类型来描述语句执行的完成状态。然后，JavaScript 引擎通过这个返回值来决定程序接下来的执行方向。

Chrome 控制台显示的正是语句的 Completion Record 中的 `[[value]]`。

Completion Record 表示一个语句执行完之后的结果，它有三个字段：

- `[[type]]` 表示完成的类型，有 `break`、`continue`、`return`、`throw`、`normal` 五种类型
- `[[value]]` 表示语句的返回值。如果语句没有返回值，则是 empty。这也是为什么在浏览器控制台打印出莫名其妙的 undefined 的原因。
- `[[target]]` 表示语句的目标，通常是一个 JavaScript 标签

### 普通语句的执行

普通语句执行后，会得到 `[[type]]` 为 normal 的 Completion Record，JavaScript 引擎遇到这样的 Completion Record，会继续执行下一条语句。

这些语句中，只有表达式语句会产生 `[[value]]`，当然，从引擎控制的角度，这个 value 并没有什么用处。

表达式语句中， `&&` 运算的返回值是比较奇怪的，我们来谈谈逻辑与的执行。

- 当两个操作数都是 Boolean 类型，返回 Boolean 类型

  ```javascript
  var a = true && false;
  a; // false
  ```

- 当两个操作数中有一个不是 Boolean 类型，不一定返回 Boolean 类型。先将第一个操作数转化为 Boolean 类型，然后根据结果是 true/false 来决定是否返回第二个操作数。

  - 如果第一个操作数是 Object，则返回第二个操作数

    ```javascript
    var a = {};
    var b = "hello";
    var c = a && b;
    c; // "hello"
    ```

  - 如果第二个操作数是 Object，则只有第一个操作数是 true 的情况才会返回第二个操作数

    ```javascript
    var a = "forza juven";
    var b = { name: "frank" };
    var c = a && b;
    c; // { name: "frank" }
    ```

  - 如果第一个操作数是 null，返回 null
  - 如果第一个操作数是 NaN，返回 NaN
  - 如果第一个操作数是 undefined，返回 undefined

### 语句块的执行

语句块就是大括号括起来的一组语句，既包括没有 if 等控制语句的{}，也包括跟在 if 等控制语句后的{}。它是一种语句的复合结构，可以嵌套。

```javascript
// type, value, target
{
  var i = 1; // normal, empty, empty
  i++; // normal, 1, empty
  console.log(i); //normal, undefined, empty
} // normal, undefined, empty
```

如果在语句块中某个语句产生了非 normal 的 `[[type]]` ，那么整个语句块就会变成非 normal 的 `[[type]]`。这个保证了非 normal 的 `[[type]]` 可以穿透复杂的语句嵌套，产生控制效果，也就是说能和控制型语句产生关联:

```javascript
// type value target
if {
  var i = 1; // normal, empty, empty
  return i; // return, 1, empty
  i++;
  console.log(i);
} // return, 1, empty
```

在这个例子中，执行到 return 语句后，`return i` 产生 `[[type]]` 为 return 的 Compleomption Record。当 JavaScript 引擎看到这个 Compleomption Record，会将它与 if 语句进行组合。详细的请参考下文。

### 控制型语句的执行

控制型语句带有 if、switch 关键字，它们会对不同类型的 Completion Record 产生反应。

控制类语句分成两部分:

- 一类是对其内部造成影响，如 `if`、`switch`、`while/for`、`try`。
- 另一类是对外部造成影响如 `break`、`continue`、`return`、`throw`。

这两类语句的配合，会产生控制代码执行顺序和执行逻辑的效果，这也是我们编程的主要工作。

语句嵌套的组合，会产生不同的结果：

![control-statements](../assets/img/control-statements.png)

上图中，第一行表示某些控制性语句节点的 Completion Record，第一列表示这个 Compleomption Record 在哪个控制型语句中。消费、穿透、报错可以这么理解：

- 消费: 消费掉当前的 `[[type]]` ,继续执行接下来的代码
- 穿透: 将当前的 `[[type]]` 传递给上一层，上一层节点和这个穿透了的 `[[type]]` 来决定如何处理
- 报错: 出错

第一个例子：

```javascript
function test() {
  // point c
  if (true) {
    // point b
    console.log("111");
    break; // point a
  }
  if (true) {
    console.log("222");
  }
}

test(); // SyntaxError: Illegal break statement
```

我们可以这么分析：

1. 执行到 point a 处的 `break` 语句，该语句返回的 Completion Record 中的 `[[type]]` 是 break。由上表可知，point b 处的 if 和 break 相遇，break 穿透至 function
2. point c 处的 function 和 break 相遇，报错

第二个例子：

```javascript
function test() {
  var a = 0;
  switch (a) {
    case 0:
      if (true) {
        console.log("111");
        break;
      }
  }

  if (true) {
    console.log("222");
  }
}

test();
// 111
// 222
```

我们可以这么分析：

1. if 和 break 相遇，break 穿透至 switch
2. swicth 和 break 相遇，消费掉 break
3. 接着执行之后的代码

### 带标签的语句的执行

Completion Record 中的 `[[target]]` 字段主要在带标签的语句中使用。

```javascript
outer: while (true) {
  inner: while (true) {
    break outer;
  }
}
console.log("finished");
```

break/continue 语句如果后跟了关键字，会产生带 target 的完成记录。一旦完成记录带了 target，那么只有拥有对应 label 的循环语句会消费它。
