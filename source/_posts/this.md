---
title: This Summary
tags: Javascript
---

```javascript
/**
 * This Summary
 * by Monar
 */
 
// This 指向当前函数正在执行的上下文环境

// 1. 函数调用
// 函数调用的this指向全局对象，在浏览器环境中是window对象，在node环境下为global对象
// 如果函数被定义为严格模式，那么内部所有的this全部为undefined

// example 1.1
function func1_1 () {
  return this
}

console.log('this: ' + func1_1()) // this: [object global]

// example 1.2
function func1_2 () {
  this.someNum = 2 // 为全局环境添加变量someNum
}

func1_2()
console.log('someNum: ' + someNum) // someNum: 2

// example 1.3
// 闭包的执行环境具有全局性

let obj1_3 = {
  num1: 1,
  num2: 2,
  sum: function () {
    console.log('this === obj1_3: ' + (this === obj1_3)) // true
    // cal() 构成一个闭包，内部执行环境其实是全局global
    function cal() {
      console.log('this === obj1_3: ' + (this === obj1_3))
      return this.num1 + this.num2
    }
    // return cal.call(this)  // 正确打开方式
    return cal() // 错误打开方式
  }
}

console.log(obj1_3.sum()) // NaN

console.log('*******************************')

// 2. 方法调用
// 方法是作为一个对象存储的函数
// 当在一个对象里调用方法时，this代表对象本身

// example 2.1
let obj2_1 = {
  foo: function () {
    console.log(this)
  }
}

let func2_1 = obj2_1.foo

obj2_1.foo() // obj2_1
func2_1() // global

// example 2.2
class Class2_1 {
  constructor (attr) {
    this.attr = attr
  }

  show () {
    console.log(this === obj2_2)
  }
}

let obj2_2 = new Class2_1()
obj2_2.show() // true

console.log('*******************************')

// 3. 构造函数调用
// 构造函数调用使用new关键字，构造函数调用的上下文环境是新构建的对象
// 当构造器函数失去new关键字的时候即为函数调用，this的指向会是全局环境

// example 3.1
function func3_1 () {
  console.log(this instanceof func3_1)
  this.attr = '3_1'
}

let obj3_1 = new func3_1 // true

console.log('*******************************')

// 4. 间接调用
// 间接调用一般为一个函数使用了.call()和.apply()方法
// .call()和.apply()函数用来配置调用函数的上下文环境
// 间接调用的上下文为传给方法的第一个参数

console.log('*******************************')

// 5. 绑定函数调用
// 绑定函数调用为将函数绑定一个执行对象，即调用.bind()方法
// .bind() 返回一个新方法


// example 5.1
function func5_1 (num) {
  return this * num
}

let double = func5_1.bind(2) // 2 绑定到func5_1的this上
double(3) // 6
double(5) // 10


// .bind() 创建了一个永恒的上下文链并不可修改
// 一个绑定函数即使使用 .call() 或者 .apply()传入其他不同的上下文环境，
// 也不会更改它之前连接的上下文环境，重新绑定也不会起任何作用

// example 5.2
function func5_2 () {
  return this
}

let one = func5_2.bind(1)

one() // 1
one.call(2) //1
one.apply(3) // 1
one.bind(4)() // 1
new one() // object

console.log('*******************************')

// 6. 箭头函数
// 箭头函数绑定定义时候的上下文环境
// 箭头函数并不创建它自身执行的上下文，使得 this 取决于它在定义时的外部函数

// example 6.1
let func6_1 = () => this

console.log(func6_1()) // {},浏览器环境返回window

// example 6.2
let obj6_1 = {
  num: 1,
  add() {
    console.log(this)
    let sum = () => {
      return this.num += 1
    }
    return sum()
  }
}

console.log(obj6_1.add()) // 2
```