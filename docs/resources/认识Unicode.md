# 认识 Unicode

[Unicode](http://www.fileformat.info/info/unicode/index.htm) 是一种字符集，每个字符对应着一个码点（Unicode Point，可以用 `U+XXXX` 表示）。比如， A 的码点就是 65（`U+0041`）。

- ASCII，最早的一款字符集
- Unicode 现行最流行的字符集
- UTF-8、UTF-16 等都是 Unicode 的一种实现方式，是一种存储方式。一般的，存储在磁盘上或进行网络交换时都会采用 UTF-8，而在程序内部进行处理时则转换为 UTF-16/32。JavaScript 就采取了 UTF-16 的方式。

## Unicode 相关的 API 比较

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

## 各个 Block 简述

- Basic Latin：常用的英文及其他字符，可以理解为 ASCII
- CJK：中日韩三国字符所在的 Block
- BMP：基本字符平面，从 `U+0000` ~ `U+FFFF`，其中的字符兼容性不错。**JavaScript 是 UTF-16 编码的，只支持这里面的**。
