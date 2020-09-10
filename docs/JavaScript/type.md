# 类型

类型在**文法**和**运行时**中都会涉及。JavaScript 有七种数据类型，其中除了 `Object`，其余都是基本数据类型。

## Undefined 类型

语义表示未定义，只有一个值：undefined。任何变量在赋值之前，变量类型就是 Undefined，值是 `undefined`。

以前的代码里中，经常能看到在 `<a>` 标签上使用 JavaScript 代码：

```html
<script>
  function openBaidu() {
    window.open("http://www.baidu.com");
  }
</script>

<!-- 代码为 undefiend，所以该标签不进行任何跳转。 -->
<a href="javascript: void(0)">Stay here!</a>

<!-- 代码为 openBaidu()，执行跳转 -->
<a href="javascript:openBaidu()">Go to Baidu</a>
```

在旧的浏览器中，由于 JavaScript 的设计缺陷，`undefined` 是一个变量，而不是一个关键字，也就是说 `undefined` 可以被覆盖。为了弥补这个缺陷，引入 `void`。`void` 是一种操作符，作用是执行 `void` 之后的代码并会返回 `undefined`。所以，为了向前兼容，一般用 `void 0` 得到 `undefined`。

在新的浏览器中，可以直接使用 undefined。

```javascript
void 0 == undefined; // true
```

## Null 类型

语义表示空值，只有一个值：null。Null 类型表示“定义并赋值了，但是值为空”。

## Boolean 类型

只有两个值：false 和 true，表示逻辑上的真和假

## Number 类型

Number 类型符合 IEEE 754-2008 规定的双精度 64 位浮点数规则。

从 262 标准可知，在词法解析阶段，Number 类型主要词法有：

```text
NumericLiteral::
    DecimalLiteral // 0 ,0. ,.2 ,1e3
    BinaryIntegerLiteral // 0b1111
    OctalIntegerLiteral // 0o10
    HexIntegerLiteral // 0xFF
```

数字直接量的表示方法分为：

- 支持小数点和科学记数法的表示方法

  - 十进制

    ```javascript
    0.01;
    12;
    12.01;

    10.24e2;
    10.24e-2;
    10.24e2;
    ```

- 不支持小数点的表示方法

  - 二进制

    ```javascript
    0b10000;
    ```

  - 八进制

    ```javascript
    0o73;
    ```

  - 十六进制

    ```javascript
    0xfa;
    ```

由于浮点数表示数值会丢失精度，所以有一个经典的 [`0.1 + 0.2` 与 `0.3` 不相等问题](http://0.30000000000000004.com/)：

1. 0.1 的二进制表示为 `1.1001100110011001100110011001100110011001100110011001 1(0011)+ * 2^-4`；

2. 当 64bit 的存储空间无法存储完整的无限循环小数，而 IEEE 754 Floating-point 采用 round to nearest, tie to even 的舍入模式，因此 0.1 实际存储时的位模式是 0-01111111011-1001100110011001100110011001100110011001100110011010；

3. 0.2 的二进制表示为 `1.1001100110011001100110011001100110011001100110011001 1(0011)+ * 2^-3`；

4. 当 64bit 的存储空间无法存储完整的无限循环小数，而 IEEE 754 Floating-point 采用 round to nearest, tie to even 的舍入模式，因此 0.2 实际存储时的位模式是 0-01111111100-1001100110011001100110011001100110011001100110011010；

5. 实际存储的位模式作为操作数进行浮点数加法，得到 0-01111111101-0011001100110011001100110011001100110011001100110100。转换为十进制即为 0.30000000000000004。

所以在碰到这类问题时，需要指定精度：`Math.abs(0.1 + 0.2 - 0.3) <= Number.EPSILON);`

## String 类型

从 262 标准可知，在词法解析阶段，String 类型主要词法有：

```text
StringLiteral::
  "DoubleStringCharacters opt"
  'SingleStringCharacters opt'

Template::
  NoSubstitutionTemplate
  TemplateHead
```

单双引号的区别仅仅在于写法，

1. 在双引号字符串直接量中使用双引号，双引号必须转义
2. 在单引号字符串直接量中使用单引号，单引号必须转义

字符串中其他必须转义的字符是 `\` 和所有换行符。

JavaScript 中的字符串(也就是 String 类型的值)是永远无法变更的，一旦字符串构造出来，无法用任何方式改变字符串的内容。所以字符串具有值类型的特征。，在运行时内存中使用 UTF-16 进行编码。

在 JavaScript 中，使用 UTF-16 编码，中文字符/英文字符都用一个 Unicode 字符表示。

String 类型的意义并非是“字符串”，而是字符串的 UTF-16 编码，字符串的操作 `charAt`、`charCodeAt`、`length` 等针对的都是 UTF-16 编码。也就是说，String 类型用于表示零个或多个 16 位 Unicode(UTF-16) 的字符组成的字符序列。

关于字符、字节、位数，可以这么理解：

- 一个字符就是一个单元，占据的存储空间用比特位数表示。
- 一般来说，一个中文字符表示两个字节，一个英文字符表示一个字节。
- 一个字节占据 8 个比特位数。

当中英文字符混杂组成 String 类型，如果计算要字节个数，可以使用：

```javascript
function strLen(str) {
  var count = 0;
  for (var i = 0, len = str.length; i < len; i++) {
    count += str.charCodeAt(i) < 256 ? 1 : 2;
  }
  return count;
}

strLen("hello尤文"); // 9

"hello尤文".length; // 7
```

## Object 类型

见 Object 类型。

## Symbol 类型

见 Symbol 类型。

## 判断类型的方法

- 使用 `typeof` 判断属于七种类型中的哪种

  ![typeof](../assets/img/typeof.png)

  由上图可知，typeof 判断类型是有瑕疵的。

  ```javascript
  'example string' instanceof String; // false
  typeof 'example string' === 'string'; // true

  'example string' instanceof Object; // false
  typeof 'example string' === 'object'; // false

  true instanceof Boolean; // false
  typeof true === 'boolean'; // true

  99.99 instanceof Number; // false
  typeof 99.99 === 'number'; // true

  function() {} instanceof Function; // true
  typeof function() {} === 'function'; // true

  typeof null; // object

  ```

- 使用 `instanceof` 判断是否是 Object 类型的实例，涉及到原型链

  ```javascript
  var ClassFirst = function() {};
  var ClassSecond = function() {};
  var instance = new ClassFirst();
  typeof instance; // object
  typeof instance == "ClassFirst"; // false
  instance instanceof Object; // true
  instance instanceof ClassFirst; // true
  instance instanceof ClassSecond; // false

  /regularexpression/ instanceof RegExp; // true
  typeof /regularexpression/; // object

  [] instanceof Array; // true
  typeof []; //object

  {} instanceof Object; // true
  typeof {}; // object
  ```

- 使用 `Object.prototype.toString()` 得到变量是 Object 类型的何种子类型

  在 JavaScript 中，没有任何方法可以更改私有的 Class 属性，因此 `Object.prototype.toString()` 是可以准确识别变量对应的 Object 类型子类型的方法，它比 instanceof 更加准确。但需要注意的是，call 本身会产生装箱操作，所以需要配合 typeof 来区分基本类型还是对象类型

  ```javascript
  Object.prototype.toString.call([1, 2, 3]); //"[object Array]"
  Object.prototype.toString.call(new Date()); //"[object Date]"
  Object.prototype.toString.call(/a-z/); //"[object RegExp]"
  ```
