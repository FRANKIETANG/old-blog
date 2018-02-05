---
title: Express
date: 2017-12-13 01:04:24
tags: [Node]
---
# Express

一个文档很完善的框架

## 中间件

中间件是一种封装，对请求处理流程某一小块逻辑的封装。

```
// express 版服务器
const express = require('express')
const http = require('http')
const app = express()

app.use((req, res, next) => {
    console.log('this is middleware no.1')  // 1
    next()
})

app.use((req, res) => {
    console.log('this is middleware no.2')  // 2
    res.end('hello world')
})

const server = http.createServer(app)

server.listen('9292')

// 这样子会打印出 1 2 而不是 2 1，是按顺序执行的
```

当服务器接收到了请求之后一直没有返回（也就是 `app.use` 之后怎么都不做），会有超时错误。

### 中间件的生命周期

看下面这两段代码

```
app.use((req, res, next) => {
    req.number = 1
    next()
})

app.use((req, res) => {
    console.log(`req.number: ${req.number}`)
    res.end('end')
})
```

```
app.use((req, res, next) => {
    console.log(`req.number: ${req.number}`)
    next()
})

app.use((req, res) => {
    req.number = 1
    res.end('end')
})
```

第一段打印的是`req.number: 1`，第二段打印的是`req.number: undefined`

他们的赋值也是有循序性的

也可以说互不影响吧，每个中间件只用处理复杂逻辑的一小块，通过 `next()` 把控制权交给下一个中间件，中间件不需要知道前一个中间件做了什么或者后一个中间件做了什么，只要处理好自己的逻辑就可以了。

### 中间件的作用

可以做个鉴权啥的

```
// auth.js
module.exports =  function auth(req, res, next) {
    console.log(req.query)
    if (req.query.username === 'frankie') {  // req.query.username 一定要等于 frankie，要不然就go away
        next()
    } else {
        res.end('please go away')
    }
}

// index.js
app.use(require('./middlewares/auth'))  // 这里的 auth 可以理解为 OAuth 2
```

不通过就停止执行下面的逻辑

![](https://i.loli.net/2017/12/10/5a2d4f70bb5a9.gif)

```
app.use(require('./middlewares/auth'))

app.use((req, res, next) => {
    next('something wrong')  // next 里面如果有传东西就会当做错误处理掉，直接跑到下面的错误处理中间件
})

// 错误处理中间件
app.use((err, req, res, next) => {
    res.end(err)
})

// 注意传进去的四个参数一定要写全，如果只写 err, req, res 会被识别成 req, res, next
```

### 中间件的写法

```
function mw1(req, res, next) {
    console.log('mw1')
    next()
}

function mw2(req, res, next) {
    console.log('mw2')
    next()
}

function mw3(req, res, next) {
    console.log('mw3')
    res.end('done')
}

// 第一种
app.use(mw1)
app.use(mw2)
app.use(mw3)

// 第二种
// app.use(mw1, mw2, mw3)

// 第三种
// app.use([mw1, mw2], mw3)

app.use((err, req, res, next) => {
    res.end(err)
})
```

### body-parser

```
// 另一种写法
app.use(bodyParser.json())
app.use(bodyParser.urlencoded({ extended: true }))

// extended 是这样用的，如 users[0]=123&users[1]=456 解析成数组，users[age]=18&users[name]=frankie 解析成对象

function mw0(options) {
    return function (req, res, next) {
        console.log(req.body)
        next()
    }
}

app.use(mw0())
```

这个其实是解决 post 的处理，不过只能检测两种格式 `application/json` 和 `x-www-form-urlencoded`

![](https://i.loli.net/2017/12/11/5a2d5f3d9c67c.gif)

更多中间件模块 http://expressjs.com/en/resources/middleware.html

### 中间件的运行条件

```
app.use((req, res, next) => {
    req.middlewares = []
    next()
})

function mw1(options) {
    return function (req, res, next) {
        req.middlewares.push('mw1')
        next()
    }
}

function mw2(req, res, next) {
    req.middlewares.push('mw2')
    next()
}

function mw3(req, res, next) {
    req.middlewares.push('mw3')
    res.end(JSON.stringify(req.middlewares))
}

app.use('/', mw1())
app.get('/article', mw2)
app.post('/user', mw2)
app.use(mw3)
```

![](https://i.loli.net/2017/12/11/5a2d636d08062.gif)

也就是说中间件可以通过路由、HTTP 请求的方法、body-parser、query 来做一些精细的控制

中间件不完全正则 http://expressjs.com/en/4x/api.html#path-examples

## Express 的其他细节

用 express-generator 生成一个例子

```
// Express 的 app.js
const express = require('express');
const path = require('path');
// const favicon = require('serve-favicon');
const logger = require('morgan');
const cookieParser = require('cookie-parser');
const bodyParser = require('body-parser');

const index = require('./routes/index');
const users = require('./routes/users');

const app = express();

// view engine setup
app.set('views', path.join(__dirname, 'views')); // 设置 views 文件夹为视图文件的目录，存放模板文件，__dirname 为全局变量，存储着当前正在执行脚本所在文件夹的绝对路径
app.set('view engine', 'ejs'); // 设置视图模版引擎为 ejs

// uncomment after placing your favicon in /public
// app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
/**
 *  Express 依赖于 connect，提供了大量的中间件，可以通过  app.use 启用
 *
 *  app.use([path], function)：使用中间件 function，可选参数path默认为'/'
 *  app.use(express.favicon())：connect 内建的中间件，使用默认的 favicon 图标，
 *  如果想使用自己的图标，需改为app.use(express.favicon(__dirname + '/public/images/favicon.ico'));
 *  这里我们把自定义的 favicon.ico 放到了 public/images 文件夹下。
 */
app.use(logger('dev'));
/**
 *  connect 内建的中间件，在开发环境下使用，在终端显示简单的不同颜色的日志，比如在启动 app.js 后访问 localhost:3000，终端会输出：
 *  Express server listening on port 3000 GET / 200 21ms - 206b GET /stylesheets/style.css 304 4ms
 *  数字200显示为绿色，304显示为蓝色。假如你去掉这一行代码，不管你怎么刷新网页，终端都只有一行 Express server listening on port 3000。
 */
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
/**
 * bodyParser作用是对post请求的请求体进行解析
 * bodyParser用于解析客户端请求的body中的内容,内部使用JSON编码处理,url编码处理以及对于文件的上传处理
 */
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public')));
/**
 *  设置静态文件目录
 *  express.static指定了静态页面的查找目录，如果定义express.static('/var/www')，
 *  当用户向node请求http://server/file.html，node将会自动查找/var/www下面找server/file.html
 */

//  是一个路由控制器，用户如果访问“ / ”路径，则由 routes.index 来控制。
app.use('/', index);
app.use('/users', users);

// catch 404 and forward to error handler
// 上面全部走完没有结果就 404
app.use((req, res, next) => {
  const err = new Error('Not Found');
  err.status = 404;
  next(err);
});

// error handler
// 错误信息会经过 error.ejs 渲染出来
app.use((err, req, res) => {
  // set locals, only providing error in development
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  // render the error page
  res.status(err.status || 500);
  res.render('error');
});

module.exports = app;
```

入口看起来是 `bin/www`，但实际上真正的入口是 `app.js`

接下来看看中断的例子

```
router.use('/', (req, res, next) => {
  console.log('mw1')
  next('router')
});

router.use('/', (req, res, next) => {
  console.log('mw2')
  next()
});

// 这个只会打印 mw1
// 如果在 next() 传一个字符串，就会中断代码
```

## Express 中的 MVC

```
// controller
const express = require('express');

const router = express.Router();
const User = require('../models/in-demo/user');

/* GET users listing. */
router.get('/', (req, res) => {
  const u = new User(req.query.firstName, req.query.lastName, req.query.age);
  res.locals.user = u;
  res.render('user');
});

module.exports = router;

// model
class User {
  constructor(firstName, lastName, age) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.age = age;
  }
  getName() {
    return `${this.firstName} ${this.lastName}`;
  }
}

module.exports = User;

// view
<h1><%= user.lastName %></h1>
<h1><%= user.age %></h1>
```

![](https://i.loli.net/2017/12/11/5a2e792b19f57.gif)

model -> 保存变量

controller -> 接收用户的请求，根据 model 来进行数据拼装

view -> 显示给用户

```
// 完善 model
const users = [];
class User {
  constructor(firstName, lastName, age) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.age = age;
  }
  getName() {
    return `${this.firstName} ${this.lastName}`;
  }

  static insert(firstName, lastName, age) {
    const u = new User(firstName, lastName, age);
    User.users.push(u);
    return u;
  }
  static getOneByName(firstName, lastName) {
    return User.users.find(element => element.firstName === firstName && element.lastName === lastName);
  }

  static list(query) {
    return User.users;
  }

  // 访问 User.users 的时候返回 users，等于 console.log(User.users)
  static get ['users']() {
    return users;
  }
}

module.exports = User;

// 测试代码
console.log(User.list());
console.log(User.insert('kalun', 'tang', 21));
console.log(User.list());
console.log(User.insert('frankie', 'tang', 21));
console.log(User.list());
console.log(User.getOneByName('kalun', 'tang'));
```

![](https://i.loli.net/2017/12/12/5a2fc8cd9f5cb.png)

http://es6.ruanyifeng.com/#docs/class

但是要考虑一个问题，每次重启 `users` 都是一个空数组，并没有持久化下来，因为是存在内存里。

```
// controller
const UserService = require('../services/user-service');
router.get('/', (req, res) => {
  const users = UserService.getAllUsers();
  res.locals.users = users;
  res.render('user');
});

router.post('/', (req, res) => {
  const { firstName, lastName, age } = req.body;
  const u = UserService.addNewUser(firstName, lastName, age);
  res.json(u);
});

// model
const User = require('../models/in-demo/user');  // 见上面的 user

module.exports = {
  getAllUsers() {
    return User.list();
  },
  addNewUser(firstName, lastName, age) {
    return User.insert(firstName, lastName, age);
  },
};

// view
<% for(let i = 0; i < users.length; i++){ %>
  <h1><%= users[i].firstName %></h1>
<% } %>
```

![](https://i.loli.net/2017/12/12/5a2fda4fdf08b.gif)

上面这个例子通过 post 数据储存到内存里，然后在通过 get 把数据渲染到页面上。

MVC 肯定也有发布订阅模式

[先给用户增加ID](https://github.com/FRANKIETANG/express-demo/commit/b359514b3dbe998e35aac0a1af799f411abb1a74)（代码量太多不方便贴了）

用户发生一些行为，并且和数据进行交互

[subscription](https://github.com/FRANKIETANG/express-demo/commit/cca1519a3fb29de469a5cb9a98d6500ed93b6c73)（代码量太多不方便贴了）

![](https://i.loli.net/2017/12/13/5a3001a5cb36f.gif)

所以说，Express 的 MVC 的结构是

```
routes/
views/
models/
services/
```

- 其中，由于 express 的特点，根据设置，views 目录下的文件会被模板引擎在调用`res.render('view_name')`的时候自动渲染
- view 层可以理解为模板引擎 + views 文件夹中的文件
- 而 routes 可以理解为 controller，负责根据用户的请求，调取相关的 service，最终得到 model 并用于渲染
- models 则代表了 model 和相关逻辑
- services 则有些特别，由于同层 model 之间解耦的需要，单个 model 往往不应该包含太多对其他 model 的操作，我们应该在 services 中对一系列逻辑上有关的 model 进行统一操作

## 彩蛋

### 如何设置 eslint ？

[eslint-config-airbnb](https://www.npmjs.com/package/eslint-config-airbnb)

```
// 按着第一行命令走
npm info "eslint-config-airbnb@latest" peerDependencies  // 显示的那一串是要你一个一个装的，太麻烦

// 第二个方法（这个最好）
(
  export PKG=eslint-config-airbnb;
  npm info "$PKG@latest" peerDependencies --json | command sed 's/[\{\},]//g ; s/: /@/g' | xargs npm install --save-dev "$PKG@latest"
)

// 安装完后开始配置
npm install -g eslint  // 全局安装
eslint --init  // 到目标文件夹初始化
// 然后出现下面这个
? How would you like to configure ESLint? (Use arrow keys)
❯ Answer questions about your style 
  Use a popular style guide  // 选这个，然后选 airbub
  Inspect your JavaScript file(s) 
// 接下来就要哪个选那个
// 配置文件选 JSON

// 配完后就跟着报错一个一个改
```

[使用 VSCode + ESLint 实践前端编码规范](https://segmentfault.com/a/1190000009077086)

`var` 改 `const`，用箭头函数，去掉没用的空格，要加分号，去掉没用的引用啊啥的，改到 0 报错就好。

其实就是为了统一代码格式

### query 里的冒号

```
router.get('/:userId/subscription/:subscriptionId', (req, res, next) => { // 前面加一个冒号就代表这个是作为一个参数处理
  res.json({
    userId: req.params.userId,
    subscriptionId: req.params.subscriptionId,
  });
});
```

![](https://i.loli.net/2017/12/15/5a336d474c910.png)