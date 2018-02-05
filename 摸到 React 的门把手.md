---
title: 摸到 React 的门把手
date: 2017-10-14 00:06:56
tags: [React,Black History]
---
# 摸到 React 的门把手

[React 入门实例教程 - 阮一峰](http://www.ruanyifeng.com/blog/2015/03/react.html)

阮一峰真的很适合当老师，这里我们就慢慢的跟上阮一峰老师步伐，摸上 React 的门把手。

## 起步

```
npm init
npm install --save react react-dom
```

而阮一峰的 demo 包自带了 React ，那我们就直接用吧。

```
$ git clone git@github.com:ruanyf/react-demos.git
```

## 第一个 demo

```
<!DOCTYPE html>
<html>
  <head>
    <script src="../build/react.js"></script>
    <script src="../build/react-dom.js"></script>
    <script src="../build/browser.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">
      ReactDOM.render(
        <h1>Hello, world!</h1>,
        document.getElementById('example')
      );
    </script>
  </body>
</html>
```

1. 并非一定要引用 browser.js ，引入它的作用是使浏览器支持`babel`，你可以使用`ES2015`（具体可以看[阮一峰的ECMAScript 6 入门](http://es6.ruanyifeng.com/)）进行编码。如果你用ES5，可以不引入。而在这里的作用是将 JSX 语法转为 JavaScript 语法。

   ```
   // The ES5 way
   var Photo = React.createClass({
     handleDoubleTap: function(e) { … },
     render: function() { … },
   });
   ```

   ```
   // The ES6+ way
   class Photo extends React.Component {
     handleDoubleTap(e) { … }
     render() { … }
   }
   ```


2. 注意 React 是用了 JSX 的语法，跟 JavaScript 不兼容。使用了 JSX 的地方都要加上 `type='text/babel'`
3. `ReactDOM.render` 是一个 API ，将模板转成 HTML 语言，并插入制定的 DOM 节点。

## 第二个 demo

```
<!DOCTYPE html>
<html>
  <head>
    <script src="../build/react.js"></script>
    <script src="../build/react-dom.js"></script>
    <script src="../build/browser.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">
      var names = ['Alice', 'Emily', 'Kate'];

      ReactDOM.render(
        <div>
        {
          names.map(function (name, index) {
            return <div key={index}>Hello, {name}!</div>
          })
        }
        </div>,
        document.getElementById('example')
      );
    </script>
  </body>
</html>
```

1. 在这个例子中我觉得 JSX 语法原来可以这样用，其实就是等于 HTML 和 JavaScript 的混写。
2. 遇到 HTML 标签（以 `<` 开头），就用 HTML 规则解析；遇到代码块（以 `{` 开头），就用 JavaScript 规则解析。

## 第三个demo

```
<!DOCTYPE html>
<html>
  <head>
    <script src="../build/react.js"></script>
    <script src="../build/react-dom.js"></script>
    <script src="../build/browser.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">
      var arr = [
        <h1 key="1">Hello world!</h1>,
        <h2 key="2">React is awesome</h2>,
      ];
      ReactDOM.render(
        <div>{arr}</div>,
        document.getElementById('example')
      );
    </script>
  </body>
</html>
```

1. 这里有一个亮点，就是用了这行代码 `<div>{arr}</div>` 把数组成员全部展现到模板上，如果让我想我只会想到用 for 循环再一个一个加到 HTML 那里。。。

## 第四个demo

```
<!DOCTYPE html>
<html>
  <head>
    <script src="../build/react.js"></script>
    <script src="../build/react-dom.js"></script>
    <script src="../build/browser.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">
      var HelloMessage = React.createClass({
        render: function() {
          return <h1>Hello {this.props.name}</h1>;
        }
      });

      ReactDOM.render(
        <HelloMessage name="John" />,
        document.getElementById('example')
      );
    </script>
  </body>
</html>
```

1. 也就是说，用 `React.createClass` 可以把一个段代码封装成组件，然后像普通的 HTML 标签一样在网页插入这个组件。

2. 要保证自己的组件拥有 `ReactDOM.render` 这个 API 来输出组件。

3. 组件类的第一个字母必须大写，否则会报错。如 `HelloMessage` 不能写成 `helloMessage`。

4. 还有组件类只能包含一个顶层标签，否则会出错 

   ```
   var HelloMessage = React.createClass({
     render: function() {
       return <h1>
         Hello {this.props.name}
       </h1><p>
         some text
       </p>;
     }
   });
   ```

5. 组件的用法和 HTML 的用法是一样的，可以加入任何的属性，比如 `<HelloMessage name="John">` 就是 `HelloMessage` 中加入一个 `name` 属性。组件的属性可以在组件类的 `this.props` 对象上获取，比如 `name` 属性就可以通过 `this.props.name` 读取。

6. 添加组件属性，有一个地方需要注意，就是 `class` 属性需要写成 `className` ，`for` 属性需要写成 `htmlFor` ，这是因为 `class` 和 `for` 是 JavaScript 的保留字。

## 第五个demo

```
<!DOCTYPE html>
<html>
  <head>
    <script src="../build/react.js"></script>
    <script src="../build/react-dom.js"></script>
    <script src="../build/browser.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">
      var NotesList = React.createClass({
        render: function() {
          return (
            <ol>
              {
                React.Children.map(this.props.children, function (child) {
                  return <li>{child}</li>;
                })
              }
            </ol>
          );
        }
      });

      ReactDOM.render(
        <NotesList>
          <span>hello</span>
          <span>world</span>
        </NotesList>,
        document.getElementById('example')
      );
    </script>
  </body>
</html>
```

1. 这一个例子主要是讲解了 `this.props.children` 属性。它表示组件的所有子节点。
2. `this.props.children` 的值有三种可能：如果当前组件没有子节点，它就是 `undefined` ;如果有一个子节点，数据类型是 `object` ；如果有多个子节点，数据类型就是 `array` 。所以，处理 `this.props.children` 的时候要小心。
3. React 提供一个工具方法 [`React.Children`](https://facebook.github.io/react/docs/top-level-api.html#react.children) 来处理 `this.props.children` 。我们可以用 `React.Children.map` 来遍历子节点，而不用担心 `this.props.children` 的数据类型是 `undefined` 还是 `object`。

## 第六的demo

```
<!DOCTYPE html>
<html>
  <head>
    <script src="../build/react.js"></script>
    <script src="../build/react-dom.js"></script>
    <script src="../build/browser.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">

      var data = 123;

      var MyTitle = React.createClass({
        propTypes: {
          title: React.PropTypes.string.isRequired,
        },

        render: function() {
          return <h1> {this.props.title} </h1>;
        }
      });

      ReactDOM.render(
        <MyTitle title={data} />,
        document.getElementById('example')
      );

    </script>
  </body>
</html>
```

1. `PropTypes`属性，就是用来验证组件实例的属性是否符合要求。

2. `Mytitle`组件有一个`title`属性。`PropTypes` 告诉 React，这个 `title` 属性是必须的，而且它的值必须是字符串。`title` 没有通过验证。

   ![](http://upload-images.jianshu.io/upload_images/3191557-a577caebac60a9c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. 另外，阮一峰老师还介绍了`getDefaultProps` 方法可以用来设置组件属性的默认值。

   ```
   var MyTitle = React.createClass({
     getDefaultProps : function () {
       return {
         title : 'Hello World'
       };
     },

     render: function() {
        return <h1> {this.props.title} </h1>;
      }
   });

   ReactDOM.render(
     <MyTitle />,
     document.body
   );
   //上面代码会输出"Hello World"。
   ```

## 第七个demo

```
<!DOCTYPE html>
<html>
  <head>
    <script src="../build/react.js"></script>
    <script src="../build/react-dom.js"></script>
    <script src="../build/browser.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">
      var MyComponent = React.createClass({
        handleClick: function() {
          this.refs.myTextInput.focus();
        },
        render: function() {
          return (
            <div>
              <input type="text" ref="myTextInput" />
              <input type="button" value="Focus the text input" onClick={this.handleClick} />
            </div>
          );
        }
      });

      ReactDOM.render(
        <MyComponent />,
        document.getElementById('example')
      );
    </script>
  </body>
</html>
//这样就能通过点击 button 来获取 input 的 focus
```

1. 组件并不是真实的 DOM 节点，而是存在于内存之中的一种数据结构，叫做虚拟 DOM （virtual DOM）。只有当它插入文档以后，才会变成真实的 DOM 。根据 React 的设计，所有的 DOM 变动，都先在虚拟 DOM 上发生，然后再将实际发生变动的部分，反映在真实 DOM上，这种算法叫做 [DOM diff](http://calendar.perfplanet.com/2013/diff/) ，它可以极大提高网页的性能表现。

   但是，有时需要从组件获取真实 DOM 的节点，这时就要用到 `ref` 属性。

2. 上面代码中，组件 `MyComponent` 的子节点有一个文本输入框，用于获取用户的输入。这时就必须获取真实的 DOM 节点，虚拟 DOM 是拿不到用户输入的。为了做到这一点，文本输入框必须有一个 `ref` 属性，然后 `this.refs.[refName]` 就会返回这个真实的 DOM 节点。

3. 需要注意的是，由于 `this.refs.[refName]` 属性获取的是真实 DOM ，所以必须等到虚拟 DOM 插入文档以后，才能使用这个属性，否则会报错。上面代码中，通过为组件指定 `Click` 事件的回调函数，确保了只有等到真实 DOM 发生 `Click` 事件之后，才会读取 `this.refs.[refName]` 属性。

4. 除了 `Click` 事件以外，还有 `KeyDown` 、`Copy`、`Scroll` 等

## 第八个demo

```
<!DOCTYPE html>
<html>
  <head>
    <script src="../build/react.js"></script>
    <script src="../build/react-dom.js"></script>
    <script src="../build/browser.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">
var LikeButton = React.createClass({
  getInitialState: function() {
    return {liked: false};
  },
  handleClick: function(event) {
    this.setState({liked: !this.state.liked});
  },
  render: function() {
    var text = this.state.liked ? 'like' : 'haven\'t liked';
    return (
      <p onClick={this.handleClick}>
        You {text} this. Click to toggle.
      </p>
    );
  }
});

ReactDOM.render(
  <LikeButton />,
  document.getElementById('example')
);
    </script>
  </body>
</html>
```

1. 组件的状态，用来和用户互动，组件一开始有一个状态，然后用户操作导致状态改变，从而改变状态，根据状态重新渲染页面。
2. `return {liked: false}` 初始化组件的状态
3. `this.setState({liked: !this.state.liked})` 将组件状态设置为当前状态相反状态

## 第九个demo

```
<!DOCTYPE html>
<html>
  <head>
    <script src="../build/react.js"></script>
    <script src="../build/react-dom.js"></script>
    <script src="../build/browser.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">
      var Input = React.createClass({
        getInitialState: function() {
          return {value: 'Hello!'};
        },
        handleChange: function(event) {
          this.setState({value: event.target.value});
        },
        render: function () {
          var value = this.state.value;
          return (
            <div>
              <input type="text" value={value} onChange={this.handleChange} />
              <p>{value}</p>
            </div>
          );
        }
      });

      ReactDOM.render(<Input/>, document.getElementById('example'));
    </script>
  </body>
</html>
```

1. 第一次看这个，呦吼？这难道是传说中的双向绑定？[React的双向绑定](http://www.cnblogs.com/kuailingmin/p/4609721.html)
2. 上面代码中，文本输入框的值，不能用 `this.props.value` 读取，而要定义一个 `onChange` 事件的回调函数，通过 `event.target.value` 读取用户输入的值。

## 第十个demo

```
<!DOCTYPE html>
<html>
  <head>
    <script src="../build/react.js"></script>
    <script src="../build/react-dom.js"></script>
    <script src="../build/browser.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">
      var Hello = React.createClass({
        getInitialState: function () {
          return {
            opacity: 1.0
          };
        },

        componentDidMount: function () {
          this.timer = setInterval(function () {
            var opacity = this.state.opacity;
            opacity -= .05;
            if (opacity < 0.1) {
              opacity = 1.0;
            }
            this.setState({
              opacity: opacity
            });
          }.bind(this), 100);
        },

        render: function () {
          return (
            <div style={{opacity: this.state.opacity}}>
              Hello {this.props.name}
            </div>
          );
        }
      });

      ReactDOM.render(
        <Hello name="world"/>,
        document.getElementById('example')
      );
    </script>
  </body>
</html>
```

1. 这里主要是介绍了关于组件的生命周期

2. React 为每个状态都提供了两种处理函数，`will` 是我要插入了！`did` 是我插进去之后，要不要搞点东西？

3. 阮一峰老师的这个 demo 是在`hello`组件加载以后，通过 `componentDidMount` 方法设置一个定时器，每隔100毫秒，就重新设置组件的透明度，从而引发重新渲染。

4. 注意组件 `style` 属性的设置方式

   ```
   //这样是错的
   style="opacity:{this.state.opacity};"
   //而要写成
   style={{opacity: this.state.opacity}}
   ```

   这是因为 React 组件样式是一个对象，所以第一重大括号表示这是 JavaScript 语法，第二重大括号表示样式对象。

## 第十一个demo

```
<!DOCTYPE html>
<html>
  <head>
    <script src="../build/react.js"></script>
    <script src="../build/react-dom.js"></script>
    <script src="../build/browser.min.js"></script>
    <script src="../build/jquery.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">
var UserGist = React.createClass({
  getInitialState: function() {
    return {
      username: '',
      lastGistUrl: ''
    };
  },

  componentDidMount: function() {
    $.get(this.props.source, function(result) {
      var lastGist = result[0];
      if (this.isMounted()) {
        this.setState({
          username: lastGist.owner.login,
          lastGistUrl: lastGist.html_url
        });
      }
    }.bind(this));
  },

  render: function() {
    return (
      <div>
        {this.state.username}'s last gist is <a href={this.state.lastGistUrl}>here</a>.
      </div>
    );
  }
});

ReactDOM.render(
  <UserGist source="https://api.github.com/users/octocat/gists" />,
  document.getElementById('example')
);
    </script>
  </body>
</html>
```

1. 把数据通过 AJAX 请求的方法从服务器获取，阮一峰老师这里是用了 `componentDidMount` 方法设置 AJAX 请求，再用 `this.setState` 方法重新渲染 UI，`InitialState` 初始数据为空

## 第十二个demo

```
<!DOCTYPE html>
<html>
  <head>
    <script src="../build/react.js"></script>
    <script src="../build/react-dom.js"></script>
    <script src="../build/browser.min.js"></script>
    <script src="../build/jquery.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">
var RepoList = React.createClass({
  getInitialState: function() {
    return {
      loading: true,
      error: null,
      data: null
    };
  },

  componentDidMount() {
    this.props.promise.then(
      value => this.setState({loading: false, data: value}),
      error => this.setState({loading: false, error: error}));
  },

  render: function() {
    if (this.state.loading) {
      return <span>Loading...</span>;
    }
    else if (this.state.error !== null) {
      return <span>Error: {this.state.error.message}</span>;
    }
    else {
      var repos = this.state.data.items;
      var repoList = repos.map(function (repo, index) {
        return (
          <li key={index}><a href={repo.html_url}>{repo.name}</a> ({repo.stargazers_count} stars) <br/> {repo.description}</li>
        );
      });
      return (
        <main>
          <h1>Most Popular JavaScript Projects in Github</h1>
          <ol>{repoList}</ol>
        </main>
      );
    }
  }
});

ReactDOM.render(
  <RepoList promise={$.getJSON('https://api.github.com/search/repositories?q=javascript&sort=stars')} />,
  document.getElementById('example')
);
    </script>
  </body>
</html>
```

1. 数据传来后赋值

   ```
   componentDidMount() {
     this.props.promise.then(
       value => this.setState({loading: false, data: value}),
       error => this.setState({loading: false, error: error}));
   },
   ```

   如果Promise对象报错（rejected状态），组件显示报错信息；如果Promise对象抓取数据成功（fulfilled状态），组件显示获取的数据。

   ```
   getInitialState: function() {
     return {
       loading: true,
       error: null,
       data: null
     };
   },
   ```

   Promise对象正在抓取数据（pending状态），组件显示"正在加载"

## 第十三个demo

- 这里不贴代码

- 这是一个 React 的服务器渲染

- 根据 readme.md 的提示，我们搞好依赖，`node server.js` 来监听 http://localhost:3000 ![](http://upload-images.jianshu.io/upload_images/3191557-888bf6bd6f540557.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 这看着是非常像手写服务器的方法，如果有 JavaScript权威指南 这本书，我建议你看看 P302 如何手写服务器。![](http://upload-images.jianshu.io/upload_images/3191557-bc51896f11363644.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 这里推荐一篇文章 [从零开始 React 服务器渲染](http://www.alloyteam.com/2017/01/react-from-scratch-server-render/)