# 语句

- Grammar
  - 简单语句
  - 复合语句
  - 声明语句
  - 预处理（变量提升）
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

- `LabelStatement` 标签语句

- `TryStatement` try 语句

  ```javascript
  try {
  } catch (e) {
    // e 只在这个作用域中有用
  } finally {
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

## 预处理（变量提升）

JavaScript 执行前，会对脚本、模块和函数体中的语句进行预处理。预处理过程将会提前处理 var、函数声明、class、const 和 let 这些语句，以确定其中变量的意义。

- var 声明

  var 声明在预处理阶段，不关心赋值的部分，只管在当前作用域声明这个变量，var 的作用能够穿透一切语句结构，它只认脚本、模块和函数体三种语法结构。

- function 声明

  在全局（脚本、模块和函数体），function 声明表现跟 var 相似，不同之处在于，function 声明不但在作用域中加入变量，还会给它赋值。

- class、const 和 let 声明

  class 声明也是会被预处理的，它会在作用域中创建变量，并且要求在未声明前访问它时抛出错误。

## Completion Record

Completion Record 表示一个语句执行完之后的结果。由三个部分组成：

- [[type]] 表示完成的类型
  - normal
  - break
  - continue
  - return
  - throw
- [[value]] 表示语句的返回值，如果语句没有，则是 empty
  - Types
- [[target]] 表示语句的目标，通常是一个 JavaScript 标签
  - label

![control-statements](../assets/img/control-statements.png)

穿透表示将非 normal 传递给外层，消费表示将非 normal 消化掉，接着执行对应的逻辑。
