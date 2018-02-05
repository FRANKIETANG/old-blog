---
title: 补基础：原型链和 prototype
date: 2017-10-17 01:58:32
tags: [JavaScript]
---
# 补基础：原型链和 prototype

先搞清楚两个东西

- `__proto__`
- `prototype`

>实例化对象的原型(`__proto__`)指向了构造函数的prototype属性

例如

```
let arr = [1,2]
let arr2 = new Array(3,4)

arr.__proto__ === Array.prototype
```

再举个例子

```
// Array 实际是一个构造函数
Array.__proto__ === Function.prototype

// 我们经常用到的 Array.forEach Array.push 实际上是 Function 的方法
Array.prototype.forEach
Array.prototype.push

// 举个例子
let arr = [1,2]
arr.push(3)
console.log(arr) // [1,2,3]
// 利用 hasOwnProperty 的方法看看
console.log(arr.hasOwnProperty('push')) // false
console.log(Array.prototype.hasOwnProperty('push'))
```

所以说，本身 `arr` 是没有 `push` 这个方法的 ，于是就会从他的原型上找。先会找它的原型 (`arr.__proto__` 等价与在 `Array.prototype` 里面找) 。

 那 `arr.hasOwnProperty` 又是怎么来的呢？

```
console.log(Object.prototype.hasOwnProperty.call(arr, 'hasOwnProperty')) //false
console.log(Object.prototype.hasOwnProperty.call(arr.__proto__, 'hasOwnProperty')) //false
console.log(Object.prototype.hasOwnProperty.call(Array.prototype, 'hasOwnProperty')) //false
// 既然找不到，那我们可以往上一级找
// arr.__proto__.__proto___ === Array.prototype.__proto__ === Object.prototype
/*
为什么 Array 是一个对象呢？
因为是对象格式
Array.prototype = {
  push: function() {
    
  }
}
*/

console.log(Object.prototype.hasOwnProperty.call(Object.prototype, 'hasOwnProperty')) // true


/*
实际上是有尽头的
一直往原型上找的话
最后是一个 null
*/
```

## 例子

```
function Person(name) {
    this.name = name;
}

Person.prototype.sayHi = function() {
    console.log(`hello I'm ${this.name}`);
};

let mike = new Person('mike');
console.log(mike.hasOwnProperty('sayHi')) // false
```

```
function Person(name) {
    this.name = name;
    this.sayHi = function() {
        console.log(`hello I'm ${this.name}`);
    };
}

let mike = new Person('mike');
console.log(mike.hasOwnProperty('sayHi')) // true
```

所以说，`prototype` 会给每个实例对象赋予一个新的 `sayHi()` 

```
function Person(name) {
    this.name = name;
	this.sayHi = function() {
    	console.log(`hello I'm ${this.name}`);
	};
}


let mike = new Person('mike');
let mike2 = new Person('mike2');
mike.sayHi();
mike2.sayHi();

console.log(mike.sayHi === mike2.sayHi) // false
```

```
function Person(name) {
    this.name = name;
}

Person.prototype.sayHi = function() {
    console.log(`hello I'm ${this.name}`);
};

let mike = new Person('mike');
let mike2 = new Person('mike2');
mike.sayHi();
mike2.sayHi();

console.log(mike.sayHi === mike2.sayHi) // true
```

```
function Person(name) {
    this.name = name;
}

Person.prototype.sayHi = function() {
    console.log(`hello I'm ${this.name}`);
};

let mike = new Person('mike');
let mike2 = new Person('mike2');
mike.sayHi();
mike2.sayHi();

Person.prototype.sayHi = function() {
    console.log(`hi I'm ${this.name}`);
};

mike.sayHi();
mike2.sayHi();

// hello I'm mike
// hello I'm mike2
// hi I'm mike
// hi I'm mike2
```

```
function Person(name) {
    this.name = name;
}

Person.prototype.sayHi = function() {
    console.log(`hello I'm ${this.name}`);
};

let mike = new Person('mike');
let mike2 = new Person('mike2');
mike.sayHi();
mike2.sayHi();

mike.sayHi = function() {
    console.log(`hi I'm ${this.name}`);
};

mike.sayHi();
mike2.sayHi();

// hello I'm mike
// hello I'm mike2
// hi I'm mike
// hello I'm mike2
```

所以，如果要找 a 里面有没有 b 这个玩意，就一直 `a.__proto__.__proto__` ... 这个子子孙孙无穷尽也 

## instanceof

> 判断自定义对象的类型

```
let arr = [1,2]
console.log(arr instanceof Array)
// 实际上就是 arr.__proto__ === Array.prototype
```

```
let arr = [1,2]

// 实现一个 instanceof, in 是实例 fn 是构造函数
function io(ins, fn) {
    if(ins.__proto__){
        if(ins.__proto__ === fn.prototype){
            return true;
        } else {
            return io(ins.__proto__, fn);
        }
    }else {
        return false;
    }
}

/*
function io(ins, fn) {
	while(ins.__proto__ !== null){
      if(ins.__proto__ === fn.prototype){
        return true
      }
      ins = ins.__proto__
	}
	return false
}
*/

/*
function io(ins, fn) {
	if(ins.__proto__ === fn.prototype) return true
	else if(ins.__proto__ === null){
      	return false
	}
	else return io(ins.__proto__, fn)
}
*/

console.log(io(arr, Array)); //true
console.log(io(arr, Object)); //true
console.log(io(arr, Number)); //false
```

## new

>创建一个实例化对象，继承构造函数的一些实例和方法
>
>- 新生成一个对象
>- 将构造函数的 this 指向这个新生成的对象
>- 设置新生成对象的原型
>- 执行构造函数
>- 返回这个对象

```
function Person(name,age) {
    this.name = name;
	this.age = age;
}

Person.prototype.sayHi = function() {
    console.log(`hello I'm ${this.name}`);
};

let mike = new Person('mike');
mike.sayHi()

// 实现一个 new 方法
function myNewObject(f, ...arg) {
    var obj, ret, proto;
    proto = f.prototype;
    obj = Object.create(proto);
    ret = f.apply(obj, arg);
    return obj;
}

// function myNewObject(fn, ...arg) {
// 	let obj = {}
// 	obj.__proto__ = fn.prototype
// 	fn.call(obj,arg)
// 	return obj
// }

let mike2 = myNewObject(Person, 'mike2', 12);
console.log(mike2.name) // mike2
mike2.sayHi(); // hello I'm mike2
console.log(mike2 instanceof Person); // true
```

```
function Person(name) {
	// 看这里，因为 this 不是那个实例化对象了
	if(!(this instanceof Person)) {
        return new Person(name);
    }
    this.name = name;
}

Person.prototype.sayHi = function() {
    console.log(`hello I'm ${this.name}`);
};

let mike3 = Person('mike3');
console.log(mike3) //undefined

// 怎么让 mike3 也成为一个正常的实例化对象呢？
// 看上面

console.log(mike3.name); //mike3
mike3.sayHi(); //hello I'm mike3
console.log(mike3 instanceof Person); //true
```

## inheritance

继承方法

```
function Person(name) {
    this.name = name;
}

Person.prototype.sayHi = function() {
    console.log(`hello I'm ${this.name}`);
};

function Student(name, grade) {
	// to-do
	Person.call(this,name);
    this.grade = grade;
}

Student.prototype = Object.create(Person.prototype); //to-do
// Student.prototype.__proto__ = Person.prototype
// Student.prototype.constructor = Student;

// 注意如果用以下这种方法
// Student.prototype = new Person()
// xiaohong.__proto__ === Student.prototype
// xiaohong.__proto__.name === undefined // 多余的东西 
// 需要的条件是
// xiaohong.__proto__ // Student.prototype
// xiaohong.__proto__.__proto__ // Person.protoype

Student.prototype.study = function(){
    console.log("I'm studying");
};

let xiaohong = new Student('xiaohong', 6);
xiaohong.sayHi(); //hello I'm xiaohong
xiaohong.study(); //I'm studying
console.log(xiaohong.grade); //6
console.log(xiaohong.name); //xiaohong
console.log(xiaohong instanceof Person); //true
console.log(xiaohong instanceof Student); //true
console.log(xiaohong.constructor === Person); //true
```

```
// 彩蛋 做一个 Object.create()
if(typeof Object.create !== 'function'){
  	Object.create = function(obj){
      function F(){}
      F.prototype = obj
      return new F()
  	}
}
```

注意 `__proto__` 不是规范里面的，是浏览器的规范 `Object.getPrototype(Array) === Array.__proto__`