---
title: 摸到 Promise 的门把手
date: 2017-10-14 00:03:09
tags: [ES6,JavaScript,Black History]
---
# Promise

这其实是异步的一种方法。

### 回调函数

```
//假定有两个函数f1和f2，后者等待前者的执行结果。
f1()
f2()
//如果f1是一个很耗时的任务，可以考虑改写f1，把f2写成f1的回调函数。
function f1(callback){
  setTimeout(function(){
    //f1 的任务代码
    callback()
  },1000)
}
//是不是有点像 AJAX
//执行代码就变成下面这样：
f1(f2)
//例子
function f2(){
  let b = 2
  console.log(b)
}

function f1(callback){
  setTimeout(function(){    
    let a = 1
    console.log(a)
    callback()
  },3000)
}

f1(f2)
```

### Promises对象

>Promises对象是CommonJS工作组提出的一种规范，目的是为异步编程提供[统一接口](http://wiki.commonjs.org/wiki/Promises/A)。
>
>简单说，它的思想是，每一个异步任务返回一个Promise对象，该对象有一个then方法，允许指定回调函数。

注意这个 `then` 

```
//f1 的回调函数 f2
f1().then(f2)
//看下面这个例子
f1().then(f2).then(f3)
//比如说
//先执行 f1() 函数
//执行完后获取一个对象，叫 promise 对象
//这个 promise 对象呢，它上面有个方法叫做 then
//所以就调用了 then(f2)
//如果数据到来之后就会执行 then(f2)
//这个数据的参数传给 f2
//then(f2) 执行完后再执行 then(f3)
f1().then(f2).fail(f3)
//如果执行失败就执行 fail(f3)
```

### 为什么要用 Promise

```
$.get(url, function(data){
  console.log(data)
  $.get(url2, function(data){
    console.log(data)
    $.get(url3, function(date){
      ........
    })
  })
})
//Promise
$.get(url)
 .then()
 .then()
 .then()
```

### 如何实现一个 Promise

```
class Promise{
  constructor(){
    this.callbacks = []    //这个数组存储的是对象
  }
  
  then(onsuccess,onfail){
    this.callbacks.push({
      resolve: onsuccess,
      reject: onfail
    })
    return this
  }
  
  resolve(result){
    this.complete('resolve',result)
  }
  
  reject(result){
    this.complete('reject',result)
  }
  
  complete(type,result){
    var callbackObj = this.callbacks.shift()
    callbackObj[type](result)     //callbackObj[resolve] 等于 fn1 所以就是 fn1(result)
  }
  
}

// function Promise(){
  
// }

// Promise.prototype.then = function(){
  
// }

var p = new Promise()

function fn() {
  console.log('fn1')
  setTimeout(function(){
    p.resolve('data1')
  },1000)
  return p
}

function fn1(result) {
  console.log('fn1',result)
  setTimeout(function(){
    p.resolve('data2')
//     p.reject()
    fn2()
  },2000)
}

function fn2(result) {
  console.log('fn2',result)
}

fn().then(fn1).then(fn2)
//[{resolve:fn1,reject:undefined},{resolve:fn2,reject:undefined}]
```