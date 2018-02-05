---
title: 补基础：this
date: 2017-10-19 21:23:13
tags: [JavaScript]
---
# 补基础：this

> [this 的值到底是什么？一次说清楚](https://zhuanlan.zhihu.com/p/23804247?refer=study-fe)
>
> [你怎么还没搞懂 this？](https://zhuanlan.zhihu.com/p/25991271)
>
> this的值是在函数调用的时候决定，而不是定义的时候决定

##  this 的四种绑定规则

```
// 默认绑定
window.a = 3;
function f() {
    let a = 4;
    console.log(this.a);
}

f(); // 3
// 通过上面的文章我们得出其实是 f().call(undefined) 默认为 global
```

```
// 隐式绑定
window.a = 3;
let obj = {
    a: 4,
    f: function() {
        console.log(this.a);
    }
};

let obj1 = {
    a: 4,
    obj: {
        a: 5,
        f: function() {
            console.log(this.a);
        }
    }
};

obj.f() // obj.f.call(obj) 所以是 4
obj1.obj.f(); // obj1.obj.f.call(obj1.obj) 是 5

let f = obj1.obj.f;
f(); // f.call(undefined) 是 3
```

```
// 显式绑定
window.a = 3;
let obj = {
    a: 4,
    f: function() {
        console.log(this.a);
    }
};

obj.f.call({a: 6}); // 6
let f = obj.f;
f.call({a: 5}); // 5
```

```
// new绑定
function Person(age) {
    this.age = age;
}

let mike = Person(12); // Person.call(12) 如果在全局作用域写一个 12 那就会有 12 ,但是 mike.age 还是 undefined
console.log(mike.age); // undefined

// 所以应该这样写
// let mike = new Person(12)
// 关于 new 这个方法，我在原型链实现过，内部应该会有一个 call() 的用法来调用 this
```

## forEach 方法

```
Array.prototype._forEach = function(fn) {
	// 实现一个 forEach 方法
    for(let i = 0; i < this.length; i++) {
        let it = this[i];
        fn(it, i, this); // 看这里，调用的时候是这样的 fn.call(it,i,this) fn 没有绑定任何的上下文，所以是全局变量
    }
};

function Person(age) {
    this.age = age;
    [3,5,10]._forEach(function(it){
    	// console.log(this) 这里其实会打印出 global 属性, 原因看上面
        console.log(`${it} year later I'm ${this.age + it} year old`);
    });
}

let mike = new Person(12);
// 3 year later I'm 15 year old
// 5 year later I'm 17 year old
// 10 year later I'm 22 year old
```

所以真正实现方法是：

```
// 实际上原生的 forEach 可以传两个参数，一个是 callback ，还有一个其实是 this，所以其实 forEach 他本来就考虑到这种情况，所以可以直接传一个 this 进去
Array.prototype._forEach = function(fn, ctx) {
	// 实现一个 forEach 方法
    for(let i = 0; i < this.length; i++) {
        let it = this[i];
        fn.call(ctx || this, it, i, this); // 所以可以这样实现
    }
};

/*
Array.prototype._forEach = function(fn) {
    for(let i = 0; i < this.length; i++) {
        let it = this[i];
        fn(it, i, this);
    }
};
*/

function Person(age) {
    this.age = age;
    [3,5,10]._forEach(function(it){
        console.log(`${it} year later I'm ${this.age + it} year old`);
    }, this);
}

/*
function Person(age) {
    this.age = age;
    [3,5,10]._forEach(function(it){
        console.log(`${it} year later I'm ${this.age + it} year old`);
    }.bind(this)); // bind(this) 会绑定成上下文的 this
}
*/

/*
function Person(age) {
    this.age = age;
    let that = this
    [3,5,10]._forEach(function(it){
        console.log(`${it} year later I'm ${that.age + it} year old`);
    });  // 使 this 变成一个普通的变量，其实和 bind(this) 是一样的套路
}
*/

/*
function Person(age) {
    this.age = age;
    [3,5,10]._forEach((it) => {
        console.log(`${it} year later I'm ${this.age + it} year old`);
    }); // 
}
*/

let mike = new Person(12);
// 3 year later I'm 15 year old
// 5 year later I'm 17 year old
// 10 year later I'm 22 year old
```

```
// 这里说一下箭头函数的 this
Array.prototype._forEach = function(fn) {
    for(let i = 0; i < this.length; i++) {
        let it = this[i];
        fn(it, i, this);
    }
};

function Person(age) {
    [3,5,10]._forEach((it) => {
        console.log(this);
    });
}

Person.call({a: 'a'}); // 绑定了 call 值 输出 => {a: 'a'}
Person(); // Person 的 this 输出 => global
let obj = {
  b: 1,
  f: Person
}

obj.f(); // 绑定了 obj 所以是 { b: 1, [Function: Person] }
```

这里说一个很可能会出现的 bug ，在 `this.age = age` 这里如果不加分号会出现 bug ，变成了 `this.age = age[3,4,5]._forEach()` `age[3,4,5]` 返回是一个 `undefined`

## bind 方法

```
// 先看 _bind 的 this 值是什么
/*
Function.prototype._bind = function(ctx) {
	console.log(this)
};

function Person(age) {
    this.age = age;
	(function(){})._bind(this)
	console.log(this)
}

let mike = new Person(12);
// Function 因为上下文的关系
// Person { age: 12 } 
*/

Function.prototype._bind = function(ctx) {
	// 实现一个 bind 方法
	// return fn
	// 注意不能用箭头函数，因为箭头函数没有 arguments
	let that = this
	let args = Array.prototype.slice.call(arguments, 1) // [1,3]
	return function() {
		let args2 = Array.prototype.slice.call(arguments) // [4,5]
      	let all = args.concat(args2); 
        return that.apply(ctx, all);
	}
};

/*
Function.prototype._bind = function(ctx) {
	// 如果要用箭头函数，用 ... 取剩余的值
	let args = Array.prototype.slice.call(arguments, 1)
	return (...args2) {
		console.log(args2)
      	let all = args.concat(args2); 
      	cosole.log(all)
        return that.apply(ctx, all);
	}
};
*/

function Person(age) {
    this.age = age;
    [3,5,10].forEach(function(it){
        console.log(`${it} yeas later I'm ${this.age + it} yeas old`);
    }._bind(this, 1, 2));
    // let fn = function(it) {
    // }._bind(this, 1, 3);
    // fn(4,5)
    // 所以用了 _bind() 方法后就是等于 fn(this, 1, 3, 4, 5)
    // let fn = function(it) {
    // }
    // let fn2 = fn._bind(this, 1, 2)
    // fn2(4, 5, 6, 7, 8)
    // 相当于 fn(1, 2, 4, 5, 6, 7, 8)
}

let mike = new Person(12);
```

如何摊平数组?

```
// 正常来说 数组 push 一个数组那就是数组内嵌数组
let arr = [1,2]
arr.push([3,4])
arr // [1, 2, [3, 4]]
arr.concat([3, 4]) // [1, 2, [3, 4], 3, 4]
arr.push.apply(arr, [5, 6]) // [1, 2, [3, 4], 3, 4, 5, 6]
```

