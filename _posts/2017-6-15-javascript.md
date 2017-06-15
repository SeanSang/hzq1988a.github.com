---
layout: post
title: JavaScript基础
categories: [随笔]
tags: []
fullview: true
---

本文主要讲述了JavaScript语言中基本知识和概念以及平时工作时遇到的一些问题。

### 为什么需要深入了解JavaScript

  首先，作为前端工程师，无论在学习新的框架和技术，还是工作开发中，都要不断的阅读JavaScript代码，因此越深入JavaScript的基础，就能更好的理解别人的代码，也能更好的创造出自己的写法，提高自己的开发效率。同时在追踪一些bug时，也能根据JavaScript的基础知识来判断问题的原因（比如异步的一些问题），更快速的定位问题。

本文所讲的JavaScript基础的主要内容包括：

1. 变量和作用域
1. 对象和原型
1. 函数
1. 面向对象编程
1. 异步编程

### 变量和作用域

#### JavaScript是动态类型、直译语言
  动态类型是指在声明变量时，不需要指定类型，也就是说变量可以存放任何类型的数据，直译语言是指在代码是在运行时解释执行的。同时JavaScript也是一个基于原型的语言（下一节会讲到），也是一门多范式语言，它支持面向对象编程，命令式编程以及函数式编程。
  在语法上JavaScript与C语言有很多相似的地方，比如判断和循环等

#### JavaScript的数据类型
  虽然JavaScript的变量没有类型，但是数据是区分类型的。JavaScript的数据类型分为两大类：基本数据类型和引用类型。
  基本数据类型包括：`undefined` `null` `boolean` `number` `string` 五种
  引用类型是除了基本数据类型以外的所有数据类型，包括数组、对象和原生对象等等
  可以通过typeof来判断数据属于哪种基本数据类型，以下表达式的值都为true：

```javascript
typeof undefined === 'undefined'  
typeof null === 'object' //  判断null时可以考虑使用===来判断而不是用typeof
typeof true === 'boolean'
typeof 123 === 'number' 
typeof '123' === 'string'
```

#### 小数运算的问题
  考虑这个表达式的结果`0.7+0.1`，我们期望的结果是`0.8`，但是运算后的结果却是：`0.7999999999`,为什么会出现这个问题呢？
  由于JavaScript只有双精度浮点数字类型（不像Java那样区分整型和浮点型），对于JavaScript来说，`1.0 === 1` 是为true的。在JavaScript内部使用IEEE754格式来存储数值，但是这种格式无法准确的用二进制来表示某些十进制小数，这样就带来了小数运算时的精度问题。
  由于一般情况下我们不需要那么高的精读，所以我们可以通过四舍五入的方式来实现我们想要的结果。比如把上面的结果四舍五入保留一位小数后就会变成我们想要的`0.8`了。

#### 四舍五入的问题
  那一般我们用什么做四舍五入呢？有人会想到原生的`toFixed`方法，这里我们可以看一下从知乎中一个问题的[讨论](https://www.zhihu.com/question/37639441)：

> 通过查看`toFixed`方法在ECMA-262 5.1 Edition标准中的定义
```
15.7.4.5 Number.prototype.toFixed (fractionDigits)

Return a String containing this Number value represented in decimal fixed-point notation with fractionDigits 
digits after the decimal point. If fractionDigits is undefined, 0 is assumed. Specifically, perform the following 
steps: 
1. Let f be ToInteger(fractionDigits). (If fractionDigits is undefined, this step produces the value 0). 
2. If f < 0 or f > 20, throw a RangeError exception. 
3. Let x be this Number value. 
4. If x is NaN, return the String "NaN". 
5. Let s be the empty String. 
6. If x < 0, then 
  a. Let s be "-". 
  b. Let x = –x. 
7. If x >= 10^21, then 
  a. Let m = ToString(x). 
8. Else, x < 10^21
  a. Let n be an integer for which the exact mathematical value of n / 10^f – x is as close to zero as possible. If there are two such n, pick the larger n. 
  b. If n = 0, let m be the String "0". Otherwise, let m be the String consisting of the digits of the decimal representation of n (in order, with no leading zeroes). 
  c. If f != 0, then 
    i. Let k be the number of characters in m. 
    ii. If k ≤ f, then 
      1. Let z be the String consisting of f+1–k occurrences of the character '0'. 
      2. Let m be the concatenation of Strings z and m. 
      3. Let k = f + 1. 
    iii. Let a be the first k–f characters of m, and let b be the remaining f characters of m. 
    iv. Let m be the concatenation of the three Strings a, ".", and b. 
9. Return the concatenation of the Strings s and m.
```

>根据上述步骤
9.655.toFixed(2)的过程在8中
```
1. f = 2
3. x = 9.655
5. s = ''
8.

  a. n = 965
    原因
    当 n = 965 时 n / 10^f – x = -0.004999999999999005
    当 n = 966 时 n / 10^f – x = 0.005000000000000782
    965更靠近0
  b. m = '965'
  c.
    i. k = 3
    iii. a = '9', b = '65'
    iv. m = a + '.' + b = '9.65'
9. Return s + m = '9.65'
```

由上面可以看到，实际上受精度影响，在乘法除法进行移位时，会造成误差，导致`toFixed` 进行四舍五入也是不准确的，所以如果想要精确的运算结果，可以使用MDN上提供的一种[方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/round)来进行四舍五入

```javascript
// Closure
(function() {
  /**
   * Decimal adjustment of a number.
   *
   * @param {String}  type  The type of adjustment.
   * @param {Number}  value The number.
   * @param {Integer} exp   The exponent (the 10 logarithm of the adjustment base).
   * @returns {Number} The adjusted value.
   */
  function decimalAdjust(type, value, exp) {
    // If the exp is undefined or zero...
    if (typeof exp === 'undefined' || +exp === 0) {
      return Math[type](value);
    }
    value = +value;
    exp = +exp;
    // If the value is not a number or the exp is not an integer...
    if (isNaN(value) || !(typeof exp === 'number' && exp % 1 === 0)) {
      return NaN;
    }
    // If the value is negative...
    if (value < 0) {
      return -decimalAdjust(type, -value, exp);
    }
    // Shift
    value = value.toString().split('e');
    value = Math[type](+(value[0] + 'e' + (value[1] ? (+value[1] - exp) : -exp)));
    // Shift back
    value = value.toString().split('e');
    return +(value[0] + 'e' + (value[1] ? (+value[1] + exp) : exp));
  }

  // Decimal round
  if (!Math.round10) {
    Math.round10 = function(value, exp) {
      return decimalAdjust('round', value, exp);
    };
  }
  // Decimal floor
  if (!Math.floor10) {
    Math.floor10 = function(value, exp) {
      return decimalAdjust('floor', value, exp);
    };
  }
  // Decimal ceil
  if (!Math.ceil10) {
    Math.ceil10 = function(value, exp) {
      return decimalAdjust('ceil', value, exp);
    };
  }
})();

// Round
Math.round10(55.55, -1);   // 55.6
Math.round10(55.549, -1);  // 55.5
Math.round10(55, 1);       // 60
Math.round10(54.9, 1);     // 50
Math.round10(-55.55, -1);  // -55.5
Math.round10(-55.551, -1); // -55.6
Math.round10(-55, 1);      // -50
Math.round10(-55.1, 1);    // -60
Math.round10(1.005, -2);   // 1.01 -- compare this with Math.round(1.005*100)/100 above
Math.round10(-1.005, -2);  // -1.01
// Floor
Math.floor10(55.59, -1);   // 55.5
Math.floor10(59, 1);       // 50
Math.floor10(-55.51, -1);  // -55.6
Math.floor10(-51, 1);      // -60
// Ceil
Math.ceil10(55.51, -1);    // 55.6
Math.ceil10(51, 1);        // 60
Math.ceil10(-55.59, -1);   // -55.5
Math.ceil10(-59, 1);       // -50
```
上面代码也可以看到，如果直接使用`Math.round(1.005*100)/100`这种方式也是不精确的，其结果是1，而不是预想的1.01。
这里的代码提供了安全的移位方式，通过科学计数法的方式来进行移位。以上的代码提供了对Math下round、floor和ceil的封装，增强了这三个函数的功能。

####变量的赋值
  基本数据类型的赋值和引用类型的赋值方式有所不同。基本数据类型为值传递，而引用类型是引用传递。请参考下面的代码：

```javascript
  var a = '123'
  var b = a;
  a = a + '456'
  a === b // false

  a = { x : 1 }
  b = a
  a.x = 2;
  a === b // true
```





