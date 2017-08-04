---
layout: post
title: JavaScript基础1
categories: [随笔]
tags: []
fullview: true
---

本文所讲的JavaScript基础的主要内容包括：

1. 面向对象编程
1. 异步编程

#### 面向对象编程

之前说到JavaScript属于多范式语言，所以JavaScript也支持面向对象编程，但是与Java不同的是，在JavaScript中是只有对象，没有类，所以在JavaScript中就需要用`构造函数`来模拟类的行为。

#### 构造函数

在Java中构造函数是属于类的，但是JavaScript的构造函数和普通的函数是一样的，换句话说，如果当任何一个函数通过`new`关键字调用的话，我们就称之为构造函数。
那当通过`new`关键字调用时和普通的函数调用有什么区别呢，我们来看下`new`调用时发生了什么：

1. 创建一个新对象
1. 这个新对象会被执行[[原型]]连接，相当于把构造函数的prototype赋值给新对象的原型__proto__
1. 将新对象作为构造函数的上下文(将this绑定为新对象)，并执行构造函数
1. 