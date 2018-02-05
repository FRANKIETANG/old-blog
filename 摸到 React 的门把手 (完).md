---
title: 摸到 React 的门把手 (完)
date: 2017-10-14 00:11:08
tags: [React,Black History]
---
# 摸到 React 的门把手 (完)

写啊写啊终于把大概功能给写完了 [效果](https://frankietang.github.io/banana-todolist/build/index.html#) ，CSS 真难写啊... 我的同学说，审美这些东西要慢慢培养。

接下来我们来说说这个项目用到了什么知识点。

## 1. JSX / React / webpack / LeanCloud 等知识。

在我博客都有很详细的介绍，这里就不多说了。[我的博客](https://frankietang.github.io/)

## 2. HTML

### 2.1 form 表单

`<input id="xxx">` 和 `<label for="xxx">` 可以对应关联

### 2.2 HTML5 LocalStorage 本地存储

在这个项目里 LocalStorage 的用法是这样的

- 用户提交数据的时候，将所有的 todo 字符串的形式保存在 localStorage
- 重新打开页面的时候，将 localStorage 里面的字符串变为对象赋值给 todoList

```
export function save(key,value){
    return window.localStorage.setItem(key,
        JSON.stringify(value))
}

export function load(key){
    return JSON.parse(window.localStorage.getItem(key))
}
```

## 3. CSS

### 3.1 flex 布局

[Flex 布局教程：实例篇](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)

### 3.2 CSS 选择器

[CSS 选择器 - MDN](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Getting_started/Selectors)

## 4. JavaScript

### 4.1 import...from...

[import - MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/import)

**import 语句 **用于从一个已经导出的外部模块或另一个脚本中导入函数，对象或原始类型。

### 4.2 ES6 - Class

[Class - MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Classes) [Class 的基本语法](http://es6.ruanyifeng.com/#docs/class#Class-的-Generator-方法)

而我的项目最多是用到 `constructor()` / `extends` / `super()` ，这里举一个例子

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

`constructor`方法是类的默认方法，通过`new`命令生成对象实例时，自动调用该方法。一个类必须有`constructor`方法，如果没有显式定义，一个空的`constructor`方法会被默认添加。

`extends` 关键词被用在[类声明](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/class)或者[类表达式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/class)上，以创建一个类是另一个类的子类。

`super` 关键字用于调用一个对象的父对象上的函数。

### 4.3 ES6 - 箭头函数

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

### 4.4 拓展运算符

[扩展语句 - MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Spread_operator)

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

### 4.5 逻辑运算符 || 用来定义默认参数

[逻辑运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Logical_Operators)

```
var link = function (height,color,url) {
  var height = height || 50
  var color = color || 'red'
  var url = url || 'https://frankie.github.io'
}
```

### 4.6 深拷贝

```
//deep Clone
let obj1 = {a: 0, b: {c: 0}};
let obj3 = JSON.parse(JSON.stringify(obj1));
obj1.a = 4;
obj1.b.c = 4;
console.log(JSON.stringify(obj3)); // {a: 0, b: {c: 0}}
```

### 4.7 条件（三元）运算符

[条件运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)

```
condition ? expr1 : expr2
```

### 4.8 apply() / call() / bind() 

```
var xw = {
  name : "小王",
  gender : "男",
  age : 24,
  say : function() {
	  alert(this.name + " , " + this.gender + " ,今年" + this.age);        
	}
}
var xh = {
  name : "小红",
  gender : "女",
  age : 18
}
xw.say();
xw.say.call(xh);
xw.say.apply(xh);
xw.say.bind(xh)();
```

call和apply都是对函数的直接调用，而bind方法返回的仍然是一个函数，因此后面还需要()来进行调用才可以。

```
var xw = {
  name : "小王",
  gender : "男",
  age : 24,
  say : function(school,grade) {
    alert(this.name + " , " + this.gender + " ,今年" + this.age + " ,在" + school + "上" + grade);  
  }
}
var xh = {
  name : "小红",
  gender : "女",
  age : 18
}
xw.say.call(xh,"实验小学","六年级");
xw.say.apply(xh,["实验小学","六年级"]);
xw.say.bind(xh,"实验小学","六年级")();
xw.say.bind(xh)("实验小学","六年级");
```

call后面的参数与say方法中是一一对应的，而apply的第二个参数是一个数组，数组中的元素是和say方法中一一对应的，这就是两者最大的区别。
那么bind怎么传参呢？它可以像call那样传参。

### 4.9 event.preventDefault

如果事件可取消，则取消该事件，而不停止事件的进一步传播。

例子：切换复选框是单击复选框的默认操作。此示例演示如何防止这种情况发生

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>event.preventDefault()</title>
</head>
<body>
    <p>请点击复选框控件</p>
    <form>
        <label for="id-checkbox">Checkbox</label>
        <input type="checkbox" id="id-checkbox" name="checkbox" />
    </form>
    <script>
        document.querySelector("#id-checkbox").addEventListener("click", function(event){
            alert("preventDefault会阻止该复选框被勾选.")
            event.preventDefault();
            //阻止该复选框被勾选
        }, false);
    </script>
</body>
</html>
```

### 4.10 this

[this](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this)

[Javascript的this用法](http://www.ruanyifeng.com/blog/2010/04/using_this_keyword_in_javascript.html)

[this 的值到底是什么？一次说清楚](https://zhuanlan.zhihu.com/p/23804247?refer=study-fe)

[你怎么还没搞懂this](https://zhuanlan.zhihu.com/p/25991271)

### 4.11 JavaScript 的 new

[JS 的 new 到底是干什么的？](https://zhuanlan.zhihu.com/p/23987456?refer=study-fe)

## 5. 最后的一点建议

还是好好的刷一遍《JavaScript 高级程序设计》和《ES6 标准入门》吧，要不然基础差的你很难上手 React 。

## 6. 相关链接

[摸到 ES6 的门把手](https://frankietang.github.io/2017/08/17/%E6%91%B8%E5%88%B0%20ES6%20%E7%9A%84%E9%97%A8%E6%8A%8A%E6%89%8B/)

[摸到 webpack 的门把手](https://frankietang.github.io/2017/08/20/%E6%91%B8%E5%88%B0%20webpack%20%E7%9A%84%E9%97%A8%E6%8A%8A%E6%89%8B/)

[摸到 webpack 的门把手 (2)](https://frankietang.github.io/2017/08/20/%E6%91%B8%E5%88%B0%20webpack%20%E7%9A%84%E9%97%A8%E6%8A%8A%E6%89%8B%20%282%29/)

[摸到 React 的门把手](https://frankietang.github.io/2017/08/18/%E6%91%B8%E5%88%B0%20React%20%E7%9A%84%E9%97%A8%E6%8A%8A%E6%89%8B/)

[摸到 React 的门把手 (2)](https://frankietang.github.io/2017/08/20/%E6%91%B8%E5%88%B0%20React%20%E7%9A%84%E9%97%A8%E6%8A%8A%E6%89%8B%20%282%29/)

[摸到 React 的门把手 (3)](https://frankietang.github.io/2017/08/21/%E6%91%B8%E5%88%B0%20React%20%E7%9A%84%E9%97%A8%E6%8A%8A%E6%89%8B%20%283%29/)

[摸到 React 的门把手 (4)](https://frankietang.github.io/2017/08/22/%E6%91%B8%E5%88%B0%20React%20%E7%9A%84%E9%97%A8%E6%8A%8A%E6%89%8B%20%284%29/)

[摸到 React 的门把手 (5)](https://frankietang.github.io/2017/08/24/%E6%91%B8%E5%88%B0%20React%20%E7%9A%84%E9%97%A8%E6%8A%8A%E6%89%8B%20%285%29/)

[摸到 React 的门把手 (6)](https://frankietang.github.io/2017/08/25/%E6%91%B8%E5%88%B0%20React%20%E7%9A%84%E9%97%A8%E6%8A%8A%E6%89%8B%20%286%29/)

这个项目的 2.0 版将会无限期跳票...