---
title: 闭包不完全指西
date: 2017-10-30 16:04:00
tags: Javascript
---

> 理解闭包是前端开发工程师的基本。 ——鲁迅

## 1. 闭包是什么

要讲清楚闭包，首先要先从js的作用域说起。

js在运行之前会建立起一条**作用域链**：

```javascript
var glob = 'Global';

function Outer() {
    var outer = 'Outer';
    function Inner() {
        var inner = 'Inner';
    }
}
```
上述代码的作用域链为`Global => Outer => Inner`，作用域链越靠近末端，可以访问的变量范围越大。任何一个函数可以访问其自己所在作用域中的变量以及父作用域中的变量，比如在`Inner`函数内部可以访问到`glob`、`outer`、`inner`三个变量。

函数无法访问自己子作用域中的任何变量，除非定义为全局的（不推荐）：

```javascript
var glob = 'Global';

function Outer() {
    var outer = 'Outer';
    // console.log(inner); 无法访问
    function Inner() {
        var inner = 'Inner';
    }
}

// console.log(outer); 无法访问
```
js中函数是一等公民，也就是说函数可以作为任何值被传递，所以看上边的例子，虽然在全局环境无法访问`outer`变量，但由于`Inner`函数可以访问，如果在全局环境中获取`Inner`函数的话，就可以访问`outer`变量了:

```javascript
var glob = 'Global';

function Outer() {
    var outer = 'Outer';
    function Inner() {
        var inner = 'Inner';
    }
    return Inner;
}

var func = Outer(); //这个func就等于Inner函数，这样func就能访问outer的值了。
```

上述的`func`接受了`Outer`函数返回的`Inner`函数，所以`func`即可访问`Outer`函数中定义的`outer`变量了。举个简单的例子：

```javascript
function Outer() {
    var outer = 0;
    function Inner() {
        outer++;
        console.log(outer);
    }
    return Inner;
}

var func = Outer();

func(); // 输出1
func(); // 输出2
```

这就是外部读取内部变量的经典方式。这个`Inner`的内部函数，就叫做**闭包**。`闭包`简单来说就是函数内部的函数，如果一旦该内部函数被返回到别的作用域，那么这个内部函数就形成一个闭包。因为引用了函数内部的一些变量，所以可以在别的作用域中进行访问。

同一个闭包可以同时存在多个，例如：

```javascript
function Outer() {
    var outer = 0;
    function Inner() {
        outer++;
        console.log(outer);
    }
    return Inner;
}

var func = Outer();

func(); // 输出1
func(); // 输出2

var anotherFunc = Outer();
anotherFunc(); // 输出1
anotherFunc(); // 输出2
```
所以，在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。

## 2. 闭包的副作用

首先由于闭包会携带包含它的作用域，所以闭包会比其他函数占用更多的内存。过度使用可能会导致内存泄漏。

另一方面，闭包还有一个副作用，就是闭包只能取得包含函数中任何变量的最后一个值。

```javascript
function createFunctions() {
    var result = [];
    for (var i = 0; i < 10; i++) {
        result[i] = function() { // 闭包
            return i;
        }
    }
    return result;
}
```

你会发现，`result`这个数组其实是一串闭包的数组，因为都引用了`i`这个变量。

表面上看，数组里的每一项运行后都能返回它们各自的索引值，从0-9，但其实，每次运行的结果都是10。因为这10个函数中都引用了`createFunctions`里边的`i`变量，这个变量在函数执行完成的时候为10，所以所有的结果都是10。如果想要改成预期的结果，则可以这样做来避免上述情况：

```javascript
function createFunctions() {
    var result = [];
    for (var i = 0; i < 10; i++) {
        result[i] = (function (num) {
            // 定义一个立即执行函数，赋值i的一份副本到num，这个时候，每个函数都会引用num了，num从0-9
            return function() {
                return num;
            };
        })(i);
    }
    return result;
}
```
另外，关于`this`对象，闭包有时候也会有一些需要注意的地方，举个例子：

```javascript
var name = 'The window';

var object1 = {
    name: 'Monar',
    getName: function() {
        return this.name;
    }
};

var object2 = {
    name: 'Sandy',
    getName: function() {
        return function() {
            return this.name;
        }
    }
};

var object3 = {
    name: 'Jack',
    getName: function() {
        var that = this;
        return function() {
            return that.name;
        }
    }
};

console.log(object1.getName()); // Monar
console.log(object2.getName()()); // The window
console.log(object3.getName()()); // Jack
```
上述三个例子，`object1`是正常情况，`object2`调用`getName()()`后发现`this`的指向是全局的，因为`object2.getName()`执行后返回的是匿名函数`function(){return this.name}`，因为**匿名函数的执行环境具有全局性**，所以这个`this`指向全局环境中的`name`。如果想要返回的函数返回对象里边的`name`，那么就要像`object3`一样，构建一个闭包，引用内部的`this`，将值放到`that`中，然后返回调用即可。


## 3. 闭包的例子

1 实现私有变量

```javascript
var app = (function() {
    var privateCounter = 0;
    return {
        // add和get即为两个闭包，可以访问匿名函数内部的privateCounter属性
        add: function() {
            privateCount++;
        },
        get: function() {
            return privateCounter;
        }
    }
})();

// 由于privateCounter存在于匿名函数内部，所以除了app对象中包含的两个闭包方法外，没有任何方式可以访问privateCounter变量
app.add();
console.log(app.get()); // 1
```

2 knockout中的observable

```javascript
var far = ko.observable('LinWei');

far(); // LinWei， 取值操作

far('ZiLiang'); // 赋值操作

far(); // ZiLiang，取值操作

// 源码
$.observable = function(value) {
    // 闭包变量，保存在内存中，存储ko对象的值
    var v = value;
    // 返回闭包函数
    function closure(val) {
        // 如果有参数传进来，为赋值操作
        if (arguments.length) {
            // 且与上次的值不同的话，重新赋值
            if (v !== val) {
                v = val;
            }
            // 返回闭包供下次调用
            return closure;
        // 如果没有参数传进来，为取值操作，直接返回闭包存储的值
        } else {
            return v;
        }
    }
    // 返回闭包
    return closure;
};
```

3 未完待续...

## 4. 参考文献

1. [学习Javascript闭包(Closure) 阮一峰](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)
2. Javascript高级程序设计(第五版) 第七章-函数表达式