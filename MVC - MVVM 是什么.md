---
title: MVC - MVVM 是什么
date: 2017-12-06 01:19:34
tags: [JavaScript]
---
# MVC - MVVM 是什么

[谈谈MVC模式 - 阮一峰](http://www.ruanyifeng.com/blog/2007/11/mvc.html)

[MVC，MVP 和 MVVM 的图示 - 阮一峰](http://www.ruanyifeng.com/blog/2015/02/mvcmvp_mvvm.html)

[MVC框架与Backbone.js - JavaScript 标准参考教程（alpha） - 阮一峰](http://javascript.ruanyifeng.com/advanced/backbonejs.html)

基于MVC的JavaScript Web富应用开发 - [网盘](https://pan.baidu.com/s/1qY9hJmG) 密码是 y9jv

以下例子代码的仓库在 https://github.com/FRANKIETANG/mvc-mvvm-example 

代码变动看 [commit](https://github.com/FRANKIETANG/mvc-mvvm-example/commits/master)

## MVC

如果要先实现一个 MVC ，首先要实现的是中间的那一层 Controller

对于 Controller 的作用，绑事件 / 调用方法可以用对象的形式 / 监听 DOM 并且更新 DOM / 操作数据

下面实现一个 MVC 的轮子吧

```
// 举个例子

// index.html 核心代码
    <div class="modules">
        <div class="module1">
            <input type="text" name="number1">
            <button name="button1">点我</button>
        </div>
    </div>

// index.js
import './module1'

// Controller.js
import $ from 'jquery'

class Controller {
    constructor(options) {
        this.$element = $(options.element)
        this.events = options.events
        this.bindEvents()
    }
    bindEvents() {
        for (let key in this.events) {
            let parts = key.split(' ')
            let eventType = parts.shift()
            let selector = parts.join(' ')
            this.$element.on(eventType, selector, this.events[key])
        }
    }
}

export default Controller

// module1.js
import Controller from './Controller'

new Controller({
    element: '.module1',
    events: {
        'change input': e => {
            console.log('change1')
        },
        'click button': e => {
            console.log('click1')
        }
    }
})
```

![](https://us1.myximage.com/2017/12/02/90db865271941bd9a64075d1a667d60e.gif)

比如上面这个例子就是当需要 Controller 的时候只需要写上你要绑的元素，要做的事件就好。至于实现方法全部交给 Controller 去处理。

但是上面的例子还是很简单，假如函数逻辑比较复杂该怎么做？可以用对象的形式调用方法。

```
// 举个例子

new Controller({
    element: '.module1',
    events: {
        'change input': 'onChangeInput',    
        'click button': 'addToCart'
    },
    addToCart() {
        let value = this.$element.find('input').val()
        this.remoteAddCart(value).then(() => { // 假如发送到服务器让服务器知道你已经把什么放到购物车了，下次登录的时候就可以获取数据。
            this.showAnimation() // 加入到购物车的动画
        })  
    },
    remoteAddCart() {
        console.log('远程请求')
        return Promise.resolve(1)
    },
    showAnimation(){
        console.log('展示添加到购物车的动画')
    }
    onChangeInput(e) {
        let input = e.currentTarget
        console.log(input.value)
    }    
})

// 修改一下 Controller

class Controller {
    constructor(options) {
        for (let key in options) {
            this[key] = options[key] // 把用户传的东西都放到 json
        }
        this.$element = $(this.element)
        this.bindEvents()
    }
    bindEvents() {
        for (let key in this.events) {
            let parts = key.split(' ')
            let eventType = parts.shift()
            let selector = parts.join(' ')
            if (typeof this.events[key] === 'function') {
                this.$element.on(eventType, selector, this.events[key])
            } else if (typeof this.events[key] === 'string') {
                let methodName = this.events[key]
                this.$element.on(eventType, selector, this[methodName].bind(this))
            }
        }
    }
}
```

![](https://us1.myximage.com/2017/12/02/9bee130d43526daa3c4794d75047aaa0.gif)

那监听 DOM 并且更新 DOM 呢？要怎么搞？（实际上早期的 MVC 更新 DOM 的确是要自己写，所以很麻烦）

```
// 举个例子

new Controller({
    element: '.module2',
    events: {
        'change input': 'onChangeInput',
        'click button': 'onClickButton'
    },
    onClickButton(e) {
        let value = this.$element.find('input').val()
        this.render(value)
    },
    onChangeInput(e) {
        let input = e.currentTarget
        console.log(input.value)
    },
    render(value) {
        let $output = this.$element.find('.output')
        if ($output.length === 0) {
            $output = $('<div class="output"></div>').text(value)
            $output.appendTo(this.$element)
        } else {
            $output.text(value)
        }
    }
})
```

![](https://us1.myximage.com/2017/12/02/53534b163c052c5f0b9a494c3a11f79c.gif)

然后过了一段时间之后就有了 template，在 JS 写模板。并且操作数据。http://handlebarsjs.com/

```
// 举个例子
// 先修改html
<div class="module2"></div>

// 增加 template 和 data
new Controller({
    element: '.module2',
    template: `
        <input type="text" name="number2" value="{{output}}">
        <button name="button2">点我</button>  
        <div class="output">{{output}}</div>  
    `,
    data: {
        output: ''  // 控制数据
    },
    events: {
        'change input': 'onChangeInput',
        'click button': 'onClickButton'
    },
    onClickButton(e) {
        let value = this.$element.find('input').val()
        this.data.output = value
        this.render()
    },
    onChangeInput(e) {
        let input = e.currentTarget
        console.log(input.value)
    }
})

// Controller
class Controller {
    constructor(options) {
        for (let key in options) {
            this[key] = options[key]
        }
        this.$element = $(this.element)
        if (this.template && this.render) {  // 判断是否有 template
            this.render()
        }
        this.bindEvents()
    }
    bindEvents() {
        for (let key in this.events) {
            let parts = key.split(' ')
            let eventType = parts.shift()
            let selector = parts.join(' ')
            if (typeof this.events[key] === 'function') {
                this.$element.on(eventType, selector, this.events[key])
            } else if (typeof this.events[key] === 'string') {
                let methodName = this.events[key]
                this.$element.on(eventType, selector, this[methodName].bind(this))
            }
        }
    }
    render() {
        let html = Handlebars.compile(this.template)(this.data)  // 渲染 template 和 data
        this.$element.html(html)
    }
}
```

和刚刚看起来效果变化不大，但实际上用上了 template 和 data 已经很接近 vue ... 啊不是，是 MVC 了 

![](https://us1.myximage.com/2017/12/03/5673db0242ef4494b3c9affc904f96a5.gif)

```
// 再举个例子
// index.html 加上模板
    <script id="module3Template" type="text/x-handlerbars">
        <button name="decrease"> - </button>
        <span>{{number}}</span>
        <button name="increase"> + </button>    
    </script>
    <script type="text/javascript" src="bundle.js"></script>

// module3.js
new Controller({
    element: '.module3',
    template: '#module3Template',
    data: {
        number: 0
    },
    events: {
        'click button[name=increase]': 'increase',
        'click button[name=decrease]': 'decrease'
    },
    increase(){
        this.data.number += 1
        this.render()
    },
    decrease(){
        this.data.number -= 1
        this.render()
    }    
})

// Controller.js 的 render 修改成
    render() {
        let template = (this.template[0] === '#') ? $(this.template).html() : this.template
        let html = Handlebars.compile(template)(this.data)
        this.$element.html(html)
    }
```

![](https://us1.myximage.com/2017/12/03/4bf9ab6f7e4d716f90c3b9e5684a710b.gif)

到这一步为止，已经实现 VC 了，视图层"（View）利用了 template 来实现，控制层"（Controller）就是 Controller.js，而数据层"（Model）就是 data，下面来举个例子

因为数据层"（Model）是从服务器来的，所以刚开始要初始化

```
// 举个例子
// 本地模拟数据 data.json
{
    "number": 10000
}

// 用 promise 模拟数据更新
new Controller({
    element: '.module4',
    template: `
        <button name="decrease"> - </button>
        <span>{{number}}</span>
        <button name="increase"> + </button>
    `,
    data: {
        number: 0
    },
    init() {
        $.get('/data.json').then((response) => {
            this.data = response
            this.render()
        })
    },
    events: {
        'click button[name=increase]': 'increase',
        'click button[name=decrease]': 'decrease'
    },
    increase() {
        this.remoteIncrease.then(() => {
            this.data.number += 1
            this.render()
        })
    },
    decrease() {
        this.remoteDecrease.then(() => {
            this.data.number -= 1
            this.render()
        })
    },
    remoteIncrease() {
        return new Promise((resolve, reject) => {
            setTimeout(() => {
                console.log('500ms')
                resolve({
                    number: this.number + 1
                })
            }, 500)
        })
    },
    remoteDecrease() {
        return new Promise((resolve, reject) => {
            setTimeout(() => {
                console.log('500ms')
                resolve({
                    number: this.number - 1
                })
            }, 500)
        })
    }
})
```

![](https://us1.myximage.com/2017/12/03/7d92010a76b32fcc2c0d222194eec2fa.gif)

再优化一下代码把数据层"（Model）抽离出来

```
let model = {
    data: {
        number: 0
    },
    get() {
        return $.get('/data.json').then((response) => {
            this.data = response
            return this.data
        })
    },
    increase() {
        return new Promise((resolve, reject) => {
            setTimeout(() => {
                console.log('500ms')
                this.data.number += 1
                resolve(this.data)
            }, 500)
        })
    },
    decrease() {
        return new Promise((resolve, reject) => {
            setTimeout(() => {
                console.log('500ms')
                this.data.number -= 1
                resolve(this.data)
            }, 500)
        })
    }
}


new Controller({
    element: '.module4',
    template: `
        <button name="decrease"> - </button>
        <span>{{number}}</span>
        <button name="increase"> + </button>
    `,
    model: model,
    events: {
        'click button[name=increase]': 'increase',
        'click button[name=decrease]': 'decrease'
    },
    init() {
        this.model.get().then(() => {
            this.render()
        })
    },
    increase() {
        this.model.increase().then(() => {
            this.render()
        })
    },
    decrease() {
        this.model.decrease().then(() => {
            this.render()
        })
    }
})
```

这样的好处就很明显了，操作永远只是数据，不会操作到 DOM，就是调一下 model 操作一下 view，以上，就实现了MVC 的全部功能 

## MVVM

Object.defineProperty 可以对属性有读写的控制

```
// 举个例子
let frankie = {
    _data: {
        age: 18,
        name: 'frankie'
    }
}

Object.defineProperty(frankie, 'age', {
    get: () => {
        console.log('frankie.age 被读取了')
        return frankie._data.age
    },
    set: xxx => {
        console.log('frankie.age 被设置了')
        frankie._data.age = xxx
    }
})

frankie.age = 19  // frankie.age 被设置了
frankie.age = 20  // frankie.age 被设置了
frankie.age = 21  // frankie.age 被设置了
console.log(frankie.age)  // frankie.age 被读取了 21
```

通过以上代码可以做到监听 input value 的变化去改内存里对象的变化，也可以改 `frankie.age` 来改页面上的数值

例子 => [点击这里](https://jsbin.com/zawapumihe/1/edit?html,js,output)

### 那 MVVM 到底是个啥玩意呢？

以上面这个例子为例：

- M -> frankie (这个是 JS 内存里的)
- VM -> 能让 V 和 M 互相沟通的东西，当 M 变了就通知 V 变，当 V 变了就通知 M 变。（两头互相监听互相变）
- V -> HTML / CSS (内容 / 表现层)

### MVVM 的缺点

第一点要注意的是 `Object.defineProperty` 里的 `get` 和 `set` 是同步还是异步（改了之后马上更新 input？改了之后在下一次任务的时候更新 input？）

![](https://us1.myximage.com/2017/12/05/b34439946bad220c9faf564fdbe2f080.png)

![yin](https://us1.myximage.com/2017/12/05/d8b5e5b60652215f6172f7e54a411110.png)

经过上面的测试是同步的

第二点是 DOM 的操作也是同步的（DOM 不存在异步过程）

这样会有一个问题，假如 view 层加了一个 input 就不能增加效果（以为因为新的 VM 没有 set 这个 input）。例子 => [点击这里](https://jsbin.com/cibunafuro/1/edit?html,js,output)

所以 MVVM 的问题就在这里，只能监听已经存在的 key，新加的 key 没法监听

解决方法还是有的，提供一个 API 为新加的 key 服务。例子 => [点击这里](https://jsbin.com/zagacilade/edit?html,js,output)

JS 操作 DOM 很慢，DOM 操作 JS 是很慢的

因为操作是同步的，所以有一个问题是，会很卡。但是 vue.js 很好的解决了这一个问题。（再也不用操作 DOM，从此 	jQuery 退出历史舞台）

[vue.js 的 Hello world](https://jsbin.com/jeqaqoyeyi/2/edit?html,js,output)