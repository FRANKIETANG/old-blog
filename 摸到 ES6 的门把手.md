---
title: 摸到 ES6 的门把手
date: 2017-10-14 00:01:50
tags: [ES6,JavaScript,Black History]
---
# ES 6标准入门（个人笔记）

## 1.`let` 和 `const`

主要是解决作用域问题。

### 1.1`let`

#### 1.1.1for 循环

```
//在 var 里
var a = 0
for (var i = 0;i < 10;i++) {
  console.log(i)//10
}
console.log(i)//10
```

```
//而在 let 则不一样
let a = 0
for (let i = 0;i < 10;i++) {
  console.log(i)//10
}
console.log(i)//undefined
//等于
var a = 0
for (var _i = 0;_i < 10;_i++) {
  console.log(_i)
}
console.log(i)
```

#### 1.1.2块级作用域

```
function f1() {
  var n = 5
  if (true) {
    var n = 10
  }
  console.log(n)//10
}
f1()
```

```
//而在 let 里面，会偷梁换柱
function f1() {
  let n = 5
  if (true) {
    let n = 10
  }
  console.log(n)//5
}
//相当于
function f1() {
  var n = 5
  if (true) {
    var _n = 10
  }
  console.log(n)//5
}
//换掉了变量名
```

### 1.2`const`

```
//这样写会报错
const b = 0
b = 1
//这样写反而不会报错
const c = {
  a:1
}
c.a = 2
//为什么呢？
//因为 b 指向了另外一个地方
//用 const 赋值的时候，b 是 only 的。
//而 c.a 并没有改变 c 的指向
//如果是
/*
c = {a:2}
*/
//这样就会报错，指向另外一个地方了
```

const 能在块级作用域里吗？

```
	const C = 'c';
	const s = () => {
		const C = 's';
		return {
			a: 'hello world'
		}
	}
	//相当于
    var C = 'c';
    var s = function s() {
        var C = 's';
        return {
            a: 'hello world'
        };
    };	
```

是可以的。

## 2.解构赋值

### 2.1基本用法

ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值。

解构赋值是讲究叶子节点的。

```
//一个简单的例子
let [a,b,c] = [1,2,3]
//相当于
var a = 1,
    b = 2,
    c = 3
```

```
//我们再来看一个例子
let { d,e }={ d:1, e:2 }
//相当于
var _d$e = { d:1,e:2 },
	d = _d$e.d
	e = _d$e.e
```

### 2.2深度解构

```
//例子
let obj = {
  p: [
    'Hello',
    {y:'World'}
  ]
}

let { p:[x,{ y }] } = obj
//相当于
var obj = {
  p: ['Hello',{ y:'World' }]
}

var _obj$p = _slicedToArray(obj.p,2),
	x = _obj$p[0],
	y = _obj$p[1].y
	
//_slicedToArray 只是一个方法，了解就好
//先把 obj 给克隆下来，层级展开，其实就是递归
```

```
//_slicedToArray
var _slicedToArray = function () { function sliceIterator(arr, i) { var _arr = []; var _n = true; var _d = false; var _e = undefined; try { for (var _i = arr[Symbol.iterator](), _s; !(_n = (_s = _i.next()).done); _n = true) { _arr.push(_s.value); if (i && _arr.length === i) break; } } catch (err) { _d = true; _e = err; } finally { try { if (!_n && _i["return"]) _i["return"](); } finally { if (_d) throw _e; } } return _arr; } return function (arr, i) { if (Array.isArray(arr)) { return arr; } else if (Symbol.iterator in Object(arr)) { return sliceIterator(arr, i); } else { throw new TypeError("Invalid attempt to destructure non-iterable instance"); } }; }()
```

### 2.3应用

#### 2.3.1`function add(a,b){return a+b}`

```
//传入数组相加
function add([x,y]){
  return x+y
}
add([1,2])//3
//相当于
function add(_ref){
  var _ref2 = _slicedToArray(_ref,2),
  	  x = _ref2[0],
  	  y = _ref2[1]
  	  
  return x + y
}

add([1,2])//3
```

#### 2.3.2字符串解构

```
const [a1,b1,c1,d1,e1] = 'hello'
//相当于
var _hello = 'hello',
	_hello2 = _slicedToArray(_hello,5),
	a1 = _hello2[0],
	b1 = _hello2[1],
	c1 = _hello2[2],
	d1 = _hello2[3],
	e1 = _hello2[4]
```

#### 2.3.3 rest 解构

```
const { p,...rest } = {p:1,a:2,c:2}
console.log(rest) // {a:2,c:2}
//相当于
var _p$a$c = {p:1,a:2,c:2},
	p = _p$a$c.p,
	rest = _objectWithoutProperties(_p$a$c,['p'])
console.log(rest)//{a:2, c:2}
//_objectWithoutProperties 也是一个方法，知道就好
```

```
//_objectWithoutProperties
function _objectWithoutProperties(obj, keys) { var target = {}; for (var i in obj) { if (keys.indexOf(i) >= 0) continue; if (!Object.prototype.hasOwnProperty.call(obj, i)) continue; target[i] = obj[i]; } return target; }
```

```
//最后一个例子
function add({
  name,
  list:[x,y]
}){
  return name+y
}
add({name:'tangkalun',list:['21','male']})
```

## 3.函数

### 3.1函数参数默认值

```
function log(x, y = 'World') {
  console.log(x, y);
}
log('hello'); // log('Hello') // Hello World
//相当于
function log(x) {
	var y = arguments.length > 1 && arguments[1] !== undefined ? arguments[1] : 'World';
	console.log(x, y);
}
log('hello'); // log('Hello') // Hello World
```

### 3.2 rest 参数

```
function add(a, b, ...values) {
  console.log(values)
}
add(2, 5, 3) // 3
//相当于
function add(a, b) {
	for (var _len = arguments.length, values = Array(_len > 2 ? _len - 2 : 0), _key = 2; _key < _len; _key++) {
		values[_key - 2] = arguments[_key];
	}

	console.log(values);
}
add(2, 5, 3); // 3
```

### 3.3扩展运算符

```
console.log(5,...[1, 2, 3])
//相当于
(_console = console).log.apply(_console, [5].concat([1, 2, 3]));
//注意如果 console.log(...5,[1,2,3]) 会出错
//...要放在最后一项
```

### 3.4箭头函数

```
var f = v => v;
var f1 = v => {
  return v
}
//相当于
var f = function f(v) {
	return v;
};
var f1 = function f1(v) {
	return v;
};
```

```
const pa = () => [1,2,3]
pa()
//[1,2,3]
```

```
	const pa = (...args) => {
		console.log(args);
		return args.reduce((pre,cur) => {
			return pre+cur;
		}, 0);
	};
	pa.apply(this, [1,2,45]);
	//[1,2,45]
	//48
```

#### 3.4.1箭头函数的 this

```
var f1 = v=>{
	console.log(this)
}
var f3 = function(v){
	//this
	return v=>{
		console.log(this);
	}
}
//相当于
var f1 = function f1(v) {
	console.log(undefined);
};
var f3 = function f3(v) {
	var _this = this;
	//this
	return function (v) {
		console.log(_this);
	};
};
//由于函数作用域的关系，this 的指向会指向上级作用域
//作用域没有就 undefined
//这样搞都是假 this
```

```
const s = () => {
  console.log(this)
}
s()
//window
//注意 babel 环境下输出的 this 是 undefined
```

>箭头函数有几个使用注意点。
>
>（1）函数体内的this对象，就是定义时所在的对象。
>
>（2）不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。
>
>（3）不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用Rest参数代替。
>
>（4）不可以使用yield命令，因此箭头函数不能用作Generator函数。

## 4. Promise

```
let p = new Promise((resolve,reject)=>{
	setTimeout(resolve,3000,1)
})
let q = new Promise((resolve, reject) => {
	reject('not good time')
})
let pending = new Promise((resolve,reject)=>{
	
})

// p ==> fulfilled 1
// Promise的状态 fulfilled pending rejected
// Promise的值  

// 3s 后
let p1 = p.then(val=>{
	val += 2;
	//return 2
	return new Promise((res, rej) => {
		res(2)
	})
}).then((val) => {
	console.log(val);
});

//只要 return 的话这个 return 的值就是 p1 的当前的状态
```

[为什么要加 setTimeout](https://www.zhihu.com/question/36972010) 神坑，这里我也不是很懂。

其实我们只用知道这只是一个异步过程

```
let p = new Promise((resolve,reject)=>{
	setTimeout(resolve,3000,1)
})
p
//Promise {[[PromiseStatus]]: "pending", [[PromiseValue]]: undefined}
//3s 后
//Promise {[[PromiseStatus]]: "resolved", [[PromiseValue]]: 1}
```

## 5. Iterator

```
let t = [1,2,3];

for(let val of t){
	console.log(val)
};
//1 2 3
```

```
const s= function(){
	for(let val of arguments){
		console.log(val)
	};
}
s(1,2,34);
//for...of 可以同时处理数组和类数组对象
//就是说，可以循环一个数据结构
```

## 6. Class

```
//例子
class Test{
  constructor(){
    this.a = 'a'
    this.b = 'b'    
  }
}
//等于
let Test = function(argument){
  this.a = 'a'
  this.b = 'b'
}

let inst = new Test()
console.log(inst.a)
//这个例子是用 class 来实现语法转换的一个例子
```

```
//实现面向对象
let Test = function(argument){
  this.a = 'a'
  this.b = 'b'
}
Test.prototype.c =()=> {console.log('c')}

let inst = new Test()
console.log(inst.c)
//用 class 来实现
class Test{
  constructor(){
    this.a = 'a'
    this.b = 'b'    
  }
  c(){console.log('c')}
}
let inst = new Test()
console.log(inst.c())
//这里会有个 undefined ，因为 console.log('c') 并没有返回值
```

```
//以前我们写构造函数会这样写
let Test = function(argument){
  this.a = a
  this.b = b
}
Test.prototype.c = function(){
  return 'ddddd'
}
//用 class
class Test{
  constructor(){
    this.a = 'a'
    this.b = 'b'
  }
  c(){
    return 'ddddd'
  }
}
```

关于 `super()` 的问题

```
class TestSuper {
  constructor() {
    this.a = 'a'
  }
}
class Test extends TestSuper {
  constructor() {
    super()
    this.b = 'b'
  }
}
let inst = new Test()
console.log(inst.a)   //a
console.log(inst)     //Test{a:'a',b:'b'}
```

## 7. Module 

```
//a.js
function f1(){
  let a = 1
}
export {f1}
```

```
//module.js
import {f1} from './let_const.js'
//相当于
var _let_const = require('./a.js')
//只有 nodejs 有 require 函数
//先把 import...form 转换成 require 的形式
//用 webpack 来处理 require 函数
```

```
a.js ==>  require('./b.js').kkkkk
b.js ==>  require('./c.js').kkkkk
c.js ==>  require('./a.js').kkkkk

AMD  define + require

webpack ==> node.js的fs io体系来把所有的require依赖放在一个文件里面
bundle.js

(function(moduleArr) {
    // XXX
}[
    a.js ,
    c.js,
    d.js
])()
```

webpack3 会自己封装一层 require

## 8. Generator

是一种异步编程解决方案

`yield` 产出

```
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
};
//最好仅在 nodejs 上使用 generator
//yield 执行完之后是停止的
//需要手动执行 .next()
var hw = helloWorldGenerator();
let a = hw.next();
let b = hw.next();
let c = hw.next();
console.log(a,b,c)
/*
{ value: 'hello', done: false } { value: 'world', done: false } { value: 'ending', done: true }
*/
```

## ES6 语法测试

- 安装babel命令行

```bash
$ cnpm install babel-cli
```

[参考文档](http://es6.ruanyifeng.com/)

第一次接触 `'use strict'` 

看来踩的坑还有点少。

[Javascript 严格模式详解 - 阮一峰](http://www.ruanyifeng.com/blog/2013/01/javascript_strict_mode.html)

这只是笔记，多看看阮一峰的 ES6 文档吧。