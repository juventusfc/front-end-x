# 表达式

## 表达式分类以及优先级

表达式优先级由树的结构来实现。以下优先级从高到低排序：

- Left-Hand-Side Expressions

  语法上表示 `=` 左边的表达式，运行时一定要是 Reference 类型。

  - Member Expression

    Member Expression 返回值是 [Reference 类型](http://www.ecma-international.org/ecma-262/10.0/#sec-reference-specification-type)。Reference 类型是一种引用，类似于指针，由 Object 和 Key 组成，是 `delete` 和 `assign` 的语言基础。

    - `a.b`
    - `a[b]`
    - foo\`string`
    - `super.b`
    - `super['b']`
    - `new.target`：用于区分函数是否被 `new` 调用。

      > The new.target pseudo-property lets you detect whether a function or constructor was called using the new operator. In constructors and functions invoked using the new operator, new.target returns a reference to the constructor or function. In normal function calls, new.target is undefined.

    - `new Foo()`

  - New Expression

    - `new Foo`

  - Call Expression

    - `foo()`
    - `super()`
    - `foo()['b']`
    - `foo().b`
    - foo()\`abc`

- Right-Hand-Side Expression

  语法上表示 `=` 右边的表达式。

  - Update Expression

    - `a++`
    - `a--`
    - `--a`
    - `++a`

  - Unary Operator

    - `delete a.b`
    - `void foo()`
    - `typeof a`
    - `+a`
    - `-a`
    - `~a`
    - `!a`
    - `await a`

  - Exponentiation Operator

    - `**` 是右结合的。

  - Multiplicative Operator

    - `*`
    - `/`
    - `%`

  - Additive Operator

    - `+`
    - `-`

  - Bitwise Shift Operator

    - `<<`
    - `>>`
    - `>>>`

  - Relational Operator

    - `<`
    - `>`
    - `<=`
    - `>=`
    - `instanceof`
    - `in`

  - Equality Operator

    - `==`
    - `===`
    - `!=`
    - `!==`

  - Binary Bitwise Operator

    - `&`
    - `^`
    - `|`

  - Binary Logical Operator

    - `&&`
    - `||`

  - Conditional Operator

    - `? :`

  - Assignment Operator

    - `*=`
    - `/=`
    - `%=`
    - `+=`
    - `-=`
    - `<<=`
    - `>>=`
    - `>>>=`
    - `&=`
    - `^=`
    - `|=`
    - `**=`

  - Comma Operator

    - `,`
