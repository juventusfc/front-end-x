# 类型

类型在**文法**和**运行时**中都会涉及。

## Undefined 类型

语义表示未定义，只有一个值：undefined。由于设计 JavaScript 时的失误，在某个阶段，undefined 作为了一个变量，所以老代码中用 `void 0;` 来表示 undefined。

## Null 类型

语义表示空值，只有一个值：null。

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

词法中，转义符是经常使用的方法。

String 类型永远无法变更，在运行时内存中使用 UTF-16 进行编码。

## Object 类型

见 [Object 类型](./object.md)

## 判断类型的方法

- `typeof`

  判断是何种基本类型。

- `object instanceof constructor`

  针对 Object 类型，获得具体属于 Object 类型下的某一种。判断原型链，判断是不是属于某种 Object 类型。

- `Object.prototype.toString.call(xxx)`

  call 也会产生装箱操作。
