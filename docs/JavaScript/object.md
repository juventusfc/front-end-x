# Object 类型

语言和宿主的基础设施由对象（Object 类型）来提供，并且 JavaScript 程序即是一系列互相通讯的对象集合。面向对象的实现方式：

- Java 等使用**类的方式**

  - 总是先有类，再从类去实例化一个对象
  - 类与类之间又可能会形成继承、组合等关系

- JavaScript 等使用**原型的方式**

  基于原型的面向对象系统通过“复制”的方式来创建新对象。两种方式实现复制：

  - 并不真的去复制一个原型对象，而是使新对象持有一个原型的引用（JavaScript 采用的方式）
  - 真实地复制对象，从此两个对象再无关联

## 对象的特征

- 对象具有**唯一标识性**：即使完全相同的两个对象，也并非同一个对象
- 对象有**状态**：对象具有状态，同一对象可能处于不同状态之下
- 对象具有**行为**：即对象的状态，可能因为它的行为产生变迁

在 JavaScript 中，状态和行为统一抽象为属性。同时，JavaScript 赋予了使用者在运行时为对象添加、修改属性（状态和行为）的能力。

## 狗咬人的对象设计模式

假设现在设计狗咬人这个功能，那么，“咬”这个行为该如何使用对象进行抽象？

答案是需要在人的对象上进行抽象。因为在设计对象的状态和行为时，我们需要遵循**行为改变状态**的原则，而狗咬人，咬只是业务需求，人会感到痛，改变的是人的状态，所以行为也需要在人这个对象上定义。

```javascript
class Human {
  hurt(damage) {
    // ...
  }
}
```

## 对象是属性的集合

对象是属性（key: value）的集合。其中，key 可以是 String 类型或 Symbol 类型，属性值 value 可以是任何类型。

JavaScript 用一组特征值来描述属性值 value：

- 数据属性特征值

  - value
  - writeable
  - enumerable
  - configurable

- 访问器属性特征值

  - getter
  - setter
  - enumerable
  - configurable

一般来说，数据属性用于描述状态，访问器属性则用于描述行为。数据属性中如果存储函数也可以用于描述行为。

获得属性特征值的方法是 `Object.getOwnPropertyDescripter()`，设置属性特征值的方法是 `Object.defineProperty()`。

定义属性值时，就会定义属性特征值：

```javascript
let a = {
  name: "frank",
};
a.age = 10086;
var o = {
  get a() {
    return 1;
  },
};
```

## 原型

如果所有对象都有私有字段 [[prototype]]，也就是就是对象的原型，在很多浏览器中，使用 `__proto__` 可获得。

注意私有字段 [[prototype]] 与 `构造器对象.prototype` 的区别:

- prototype 是构造函数的一个属性
- [[prototype]] 是对象的一个私有字段，也就是原型

当我们访问属性时，如果当前对象没有，则会沿着原型找原型对象是否有此名称的属性，而原型对象还可能有原型。因此，会有**原型链**这一说法。这一算法保证了每个对象只需要描述自己和原型的区别即可。

## ES6 中的类

ES6 引入 class 之后，基于类的编程方式也成为了 JavaScript 的官方编程范式,可以正大光明使用 `new class()` 的写法。但是注意，底层还是基于原型并且提供了继承能力。

为了避免蹩脚的 `new 构造器对象`的形式 ，令 function 回归原本的函数语义,当我们使用类的思想来设计代码时，应该尽量使用 class 来声明类，而不是用旧语法拿函数来模拟对象。class 关键字和箭头运算符可以完全替代旧的 function 关键字，这更明确地区分了定义函数和定义类两种意图

## 面向对象的 API

- 基本的对象能力
  - 创建对象
    - 字面量
      - `var o = []`
      - `var o = {}`
      - `var o = /abc/`
    - new
      - `var o = new Object();`
    - dom api
      - `var o = document.createElement("div")`
    - 通过内置对象
      - `var o = Object.create()`
      - `var o = Object.assign({},{name: "frank"})`
      - `var o = JSON.parse("{}")`
    - 通过装箱
      - `var o = Object()`
  - 访问属性
    - `a.b`
    - `a[b]`
  - 定义属性
    - `Object.defineProperty()`
- 原型相关 API
  - 根据指定的原型创建新对象，原型可以是 null
    - `Object.create()`
  - 获得原型
    - `Object.getPrototypeOf()`
  - 设置原型
    - `Object.setPrototypeOf()`
- new + class + extends 基于类的 API
- new + function + prototype 模拟 Java 的 API，在现在最好不要用了

## 对象分类

- 宿主对象

  由 JavaScript 宿主环境提供的对象，它们的行为完全由宿主环境决定。

- 内置对象

  - 固有对象

    由标准规定，随着 JavaScript 运行时创建而自动创建的对象实例。在任何 JavaScript 代码执行前就已经被创建出来了。

  - 原生对象

    通过语言本身的构造器对象创建的对象，我们可以用 new 运算创建新的对象，所以我们把这些新创建出来的对象称作原生对象。无法用 class/extend 语法来继承。

    - 函数对象

      除了一般对象的属性和原型，函数对象还具有 [[call]] 私有字段，可以去作为函数被调用。

      我们用 JavaScript 中的 function、箭头函数、Function 构造器创建的对象，都具有 [[call]] 这个行为。

      当我们用类似 `f()` 等语法把对象当作函数执行时，会访问到 [[call]] 这种行为。如果对象没有 [[call]] 这种行为，则会报错。

    - 构造器对象

      除了一般对象的属性和原型，构造器对象还具有私有字段 [[construct]]，可以作为构造器被调用。

      执行 `new 构造器对象(参数)` 之后发生的事情:

      1. 以构造器对象的 prototype 属性（注意与私有字段[[prototype]]的区分）为原型，创建新对象
      2. 将 this 和调用参数传给构造器对象，执行
      3. 如果构造器对象返回的是对象，则返回，否则返回第一步创建的对象

    用户用 function 关键字创建的对象必定同时是函数对象和构造器对象。

  - 普通对象

    由{}语法、构造器对象(开发者自己通过 function 构建)或者 class 关键字定义类创建的对象，它能够被原型继承。
