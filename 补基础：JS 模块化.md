---
title: 补基础：JS 模块化
date: 2017-12-08 19:59:10
tags: [JavaScript]
---
# 补基础：JS 模块化

为什么要用到模块化

- 解耦
- 复用

先举一个简单的例子

```
// html
<body>
    <script src="./js/main.js"></script>
</body>

// js
class Person {
    constructor(name) {
        this.name = name
    }
    sayHi() {
        console.log(`hello I'm ${this.name}`)
    }
}

class Employee extends Person {
    constructor(name, salary) {
        super(name)
        this.salary = salary
    }
    work() {
        console.log(`I'm working, My salary is ${formatSalary(this.salary)}`)
    }
}

function formatSalary(salary) {
    return salary + ' RMB'
}

let frankie = new Employee('frankie', 5000)
frankie.sayHi() // hello I'm frankie
frankie.work() // I'm working, My salary is 5000 RMB
```

如果像上面那么样引入会直接暴露所有接口，而且起名字也要很小心，如果使用模块化就可以解决这些问题。

## namespace

在没有 es6 的时代，用一种叫 namespace 的方法

解决暴露接口的问题可以用 namespace ，创造一个命名空间

```
// html 注意顺序（JS 加载）
<script src="./js/person.js"></script>
<script src="./js/util.js"></script>
<script src="./js/employee.js"></script>
<script src="./js/main.js"></script>

// person.js
(function (global) {
    class Person {
        constructor(name) {
            this.name = name
        }
        sayHi() {
            console.log(`hello I'm ${this.name}`)
        }
    }

    let namespace = global.MYAPP = global.MYAPP || {}  // 初始化
    namespace.Person = Person  // 导出 Person

})(window)

// util.js
(function (global) {
    let namespace = global.MYAPP  // 因为 Person.js 是先引的所以页面上已经存在 global.MYAPP

    namespace.UTILS = {  // 公有方法
        formatSalary: function(salary) {
            return salary + ' RMB'
        }
    }
})(window)

// employee.js
(function (global) {

    let namespace = global.MYAPP  // 同理
    let Person = namespace.Person  // 引入 Person
    let formatSalary = namespace.UTILS.formatSalary

    class Employee extends Person {
        constructor(name, salary) {
            super(name)
            this.salary = salary
        }
        work() {
            console.log(`I'm working, My salary is ${formatSalary(this.salary)}`)
        }
    }

    namespace.Employee = Employee  // 导出 Employee

})(window)

// main.js
(function (global) {
    let namespace = global.MYAPP

    namespace.UTILS = {  // 公有方法
        formatSalary: function(salary) {
            return salary + ' RMB'
        }
    }
})(window)
```

问题也是很明显的，JS 的执行顺序很让人头疼

## AMD

于是就有了新的规范 AMD -> [requirejs](http://requirejs.org/) [Javascript模块化编程（三）：require.js的用法](http://www.ruanyifeng.com/blog/2012/11/require_js.html)

```
// 举个例子
// html 引入 requirejs
<script src="../node_modules/requirejs/require.js" data-main="./js/main.js"></script>

// person.js
define(function () {
    class Person {
        constructor(name) {
            this.name = name
        }
        sayHi() {
            console.log(`hello I'm ${this.name}`)
        }
    }
    return Person  // 导出 Person 模块
})

// util.js
define(function () {
    return {  // 导出方法
        formatSalary: function (salary) {
            return salary + ' RMB'
        }
    }
})

// employee.js
define(['./person', './util'], function (Person, UTILS) {  // 引入 Person 和 util 两个依赖，记住是异步加载
    class Employee extends Person {
        constructor(name, salary) {
            super(name)
            this.salary = salary
        }
        work() {
            console.log(`I'm working, My salary is ${UTILS.formatSalary(this.salary)}`)  // 使用 UTILS 的 formatSalary 方法
        }
    }
    return Employee  // 导出 Employee 模块
})

// main.js
require(['./employee'], function (Employee) {  // 引入依赖 Employee
    let frankie = new Employee('frankie', 5000)
    frankie.sayHi() // hello I'm frankie
    frankie.work() // I'm working, My salary is 5000 RMB
})
```

上面这个例子全部是依赖于 requirejs 来实现模块化，requirejs 定义了两个方法，define 和 require。他们都可以传两个参数，第一个是依赖，用数组表示，而且是异步加载，加载完后当做参数传给你要写逻辑的函数。

AMD -> Asynchronous Module Definition（异步的模块定义）

## CommonJS

是后端的模块化，主要是由 `require` 引入，`module.exports` 导出

```
// person.js
class Person {
    constructor(name) {
        this.name = name
    }
    sayHi() {
        console.log(`hello I'm ${this.name}`)
    }
}
module.exports = Person  // 导出 Person

// util.js
module.exports = {  // 导出方法
    formatSalary: function (salary) {
        return salary + ' RMB'
    }
}

// employee.js
let Person = require('./person.js')
    UTILS = require('./util.js')  // 引入两个依赖

class Employee extends Person {
    constructor(name, salary) {
        super(name)
        this.salary = salary
    }
    work() {
        console.log(`I'm working, My salary is ${UTILS.formatSalary(this.salary)}`)
    }
}
module.exports = Employee  // 导出 Employee

// main.js
let Employee = require('./employee.js')  // 引入依赖

let frankie = new Employee('frankie', 5000)
frankie.sayHi() // hello I'm frankie
frankie.work() // I'm working, My salary is 5000 RMB
```

CommonJS 这个规范不用把模块用 `function` 包起来，因为 node.js 定义就是每个文件就是模块

## UMD

假如在 AMD 规范里加载 CommonJS 规范的模块肯定是不识别，在 CommonJS 规范里加载 AMD 规范也肯定不行

那怎么 AMD 和 CommonJS 都通吃呢？

```
(function (gl) {
    class Person {
        constructor(name) {
            this.name = name
        }
        sayHi() {
            console.log(`hello I'm ${this.name}`)
        }
    }

    // 这里是通用方法
    if (gl.hasOwnProperty('define')) {  // 看全局变量有没有 define，有执行 amd 规范
        define(function () {
            return Person
        })
    } else if (module != null && typeof module.exports === 'object') {  // module 在不在 module.exports 在不在，在就执行 commonjs 规范
        module.exports = Person
    } else {
        gl.Person = Person  // 如果都没有就挂到全局变量
    }
})(this)  // 传一个全局变量
```

[vue-class-components](https://github.com/vuejs/vue-class-component) 也有这种判断规范的方法

![](https://us1.myximage.com/2017/12/08/f296231a6848467fa1ad1c777079553c.png)

## es6 里的 import 和 export

```
<body>
    <script src="./bundle.js"></script>
</body>

// person.js
export default class Person {
    constructor(name) {
        this.name = name
    }
    sayHi() {
        console.log(`hello I'm ${this.name}`)
    }
}

// util.js
export default function formatSalary(salary) {
    return salary + ' RMB'
}

// employee.js
import Person from './person.js'
import formatSalary from './util.js'

export default class Employee extends Person {
    constructor(name, salary) {
        super(name)
        this.salary = salary
    }
    work() {
        console.log(`I'm working, My salary is ${formatSalary(this.salary)}`)
    }
}

// main.js
import Employee from './employee.js'

let frankie = new Employee('frankie', 5000)
frankie.sayHi() // hello I'm frankie
frankie.work() // I'm working, My salary is 5000 RMB

// 设置好 webpack 打包
```

值得一提的是，webpack 兼容所有模块规范的打包，各种规范混搭也可以。

## 彩蛋

### 做一个 require

```
// person.js
function Person(name) {
    this.name = name
}

Person.prototype.sayHi = function () {
    console.log(`hello I'm ${this.name}`)
}

test.exports = Person  // 这里这个 test 要和下面一样

// myrequire.js
let fs = require('fs')

let modul = (function () {
    let mod = {
        exports: {}
    }
    function myrequire(filePath) {
        let fnInText = fs.readFileSync(filePath, 'utf8')
        let fn = new Function('test', fnInText)
        fn(mod)
    }

    myrequire('./person.js')

    return mod
})()

let Person = modul.exports
let frankie = new Person('frankie')
frankie.sayHi()
```

[exports 和 module.exports 的区别](https://cnodejs.org/topic/5231a630101e574521e45ef8)

[代码](https://github.com/FRANKIETANG/module)