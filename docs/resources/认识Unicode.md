# 认识 Unicode

因为计算机只能处理 0 和 1，如果要处理文本，就必须先把文本转换为数字才能处理。最早的计算机在设计时采用 **8 个比特（bit）作为一个字节（byte）**，所以，一个字节能表示的最大的整数就是 255（二进制 11111111=十进制 255），如果要表示更大的整数，就必须用更多的字节。比如 2 个字节可以表示的最大整数是 65535，4 个字节可以表示的最大整数是 4294967295。

为了能让计算机正确处理文本，需要一种机制和标准，通过编码和解码，能让使用者和计算机和谐共处。

所有的编码标准实际上都做了两件事情，第一件就是为所有需要编码的字符进行一个编号或标识，第二件就是指定一个规则统一得将这个编号或标识与二进制串进行一个映射。

## ASCII --> Unicode --> UTF-8、UTF-16

由于计算机是美国人发明的，因此，最早只有 127 个字符被编码到计算机里，也就是大小写英文字母、数字和一些符号，这个编码表被称为 ASCII 编码，比如大写字母 A 的编码是 65，小写字母 z 的编码是 122。

但是要处理中文显然一个字节是不够的，至少需要两个字节，而且还不能和 ASCII 编码冲突，所以，中国制定了 GB2312 编码，用来把中文编进去。

你可以想得到的是，全世界有上百种语言，日本把日文编到 Shift_JIS 里，韩国把韩文编到 Euc-kr 里，各国有各国的标准，就会不可避免地出现冲突，结果就是，在多语言混合的文本中，显示出来会有乱码。

因此，Unicode 应运而生。[Unicode](http://www.fileformat.info/info/unicode/index.htm) 是一种字符集，每个字符对应着一个码点（Unicode Point，可以用 `U+XXXX` 表示）。比如， A 的码点就是 65（`U+0041`）。Unicode 把所有语言都统一到一套编码里，这样就不会再有乱码问题了。

Unicode 标准也在不断发展，但最常用的是用**两个字节表示一个字符**（如果要用到非常偏僻的字符，就需要 4 个字节）。现代操作系统和大多数编程语言都直接支持 Unicode。

ASCII 编码和 Unicode 编码的区别：

**ASCII 编码是 1 个字节，而 Unicode 编码通常是 2 个字节**。

字母 A 用 ASCII 编码是十进制的 65，二进制的 01000001；

字符 0 用 ASCII 编码是十进制的 48，二进制的 00110000，注意字符'0'和整数 0 是不同的；

汉字“中”已经超出了 ASCII 编码的范围，用 Unicode 编码是十进制的 20013，二进制的 01001110 00101101。

你可以猜测，如果把 ASCII 编码的 A 用 Unicode 编码，只需要在前面补 0 就可以，因此，A 的 Unicode 编码是 00000000 01000001。

新的问题又出现了：如果统一成 Unicode 编码，乱码问题从此消失了。但是，如果你写的文本基本上全部是英文的话，用 Unicode 编码比 ASCII 编码需要多一倍的存储空间，在存储和传输上就十分不划算。

所以，**本着节约的精神，又出现了把 Unicode 编码转化为“可变长编码”的 UTF-8 编码**。UTF-8 编码把一个 Unicode 字符根据不同的数字大小编码成 1-6 个字节，常用的英文字母被编码成 1 个字节，汉字通常是 3 个字节，只有很生僻的字符才会被编码成 4-6 个字节。如果你要传输的文本包含大量英文字符，用 UTF-8 编码就能节省空间：

| 字符 | ASCII    | Unicode           | UTF-8                      |
| ---- | -------- | ----------------- | -------------------------- |
| A    | 01000001 | 00000000 01000001 | 01000001                   |
| 中   | 无       | 01001110 00101101 | 11100100 10111000 10101101 |

从上面的表格还可以发现，UTF-8 编码有一个额外的好处，就是 **ASCII 编码实际上可以被看成是 UTF-8 编码的一部分**，所以，大量只支持 ASCII 编码的历史遗留软件可以在 UTF-8 编码下继续工作。

搞清楚了 ASCII、Unicode 和 UTF-8 的关系，我们就可以总结一下现在计算机系统通用的字符编码工作方式：

> 在内存中使用定长的编码（UTF-16 是其中一种）会有更优的性能，具体用什么编码不是一定的，看怎么实现而已。而传输和存储要节省空间的话，当然就采用可变长度的 UTF-8 更好。

所以你看到很多网页的源码上会有类似`<meta charset="UTF-8" />`的信息，表示该网页正是用的 UTF-8 编码。

文件书写时选择的编码方式，一般显示在编辑器最下面

![encode-vscode](../assets/img/encode-vscode.png)

**JavaScript 的处理机制会将字符编码方式转化为 UTF-16**。无论 JavaScript 文件是用哪种编码编写的，在执行前 JavaScript 都会将编码变为 UTF-16。

```javascript
const s1 = "\u00E9"; //é
const s2 = "\u0065\u0301"; //é
const s3 = "e\u0301"; //é
s3.length === 2; //true
s2 === s3; //true
s1 !== s3; //true
```

当我们编写文件的时候，会指定编码(比如： UTF-8)。然后，我们开始打字(比如：“A”)，屏幕上显示出来的是就是这个编码下对应的字符(比如：“A”)。由于计算机真正存储的是一串 0 和 1，编码中使用经常使用十六进制的码点来表示字符，所以**实际存储进内存/硬盘的是这个编码中该字符对应的码点对应的二进制**，也就是这一串 0 和 1(比如：`0100 0001`)。

| 屏幕上显示的字符 | 编码中对应的码点(十六进制) | 真正存储进文件的内容(二进制) |
| ---------------- | -------------------------- | ---------------------------- |
| A                | 41                         | 0100 0001                    |

简要的来说：

- ASCII 是最早的一款字符集
- Unicode 是现行最流行的字符集
- UTF-8、UTF-16 等都是 Unicode 的一种实现方式，是一种存储方式。一般的，存储在磁盘上或进行网络交换时都会采用 UTF-8，而在程序内部进行处理时则转换为 UTF-16/32。JavaScript 就采取了 UTF-16 的方式。

## 各个 Block 简述

- Basic Latin：常用的英文及其他字符，可以理解为 ASCII
- CJK：中日韩三国字符所在的 Block
- BMP：基本字符平面，从 `U+0000` ~ `U+FFFF`，其中的字符兼容性不错。**JavaScript 是 UTF-16 编码的，只支持这里面的**。

## JavaScript 中的 API

### `str.codePointAt(pos)`

返回 pos 位置处字符的码点，适用于 Unicode。

**Parameters**

`pos`

Position of an element in str to return the code point value from.

**Return value**

A number representing the code point value of the character at the given pos. If there is no element at pos, returns undefined.

### `str.charCodeAt(index)`

返回 index 位置处字符的码点，适用于 UTF-16。

**Parameters**

`index`

An integer greater than or equal to 0 and less than the length of the string. If index is not a number, it defaults to 0.

**Return value**

A number representing the UTF-16 code unit value of the character at the given index. If index is out of range, charCodeAt() returns NaN.

### `str.charAt(index)`

返回 index 码点对应的字符，适用于 UTF-16。

**Parameters**

`index`

An integer between 0 and str.length - 1. If the index cannot be converted to the integer or no index is provided, the default is 0, so the first character of str is returned.

**Return value**

A string representing the character (exactly one UTF-16 code unit) at the specified index. If index is out of range, charAt() returns an empty string.

### `String.fromCharCode(num1[, ...[, numN]])`

返回 num1[, ...[, numN]] 码点对应的字符串，适用于 UTF-16。

**Parameters**

`num1, ..., numN`

A sequence of numbers that are UTF-16 code units. The range is between 0 and 65535 (0xFFFF). Numbers greater than 0xFFFF are truncated. No validity checks are performed.

**Return value**

A string of length N consisting of the N specified UTF-16 code units.
