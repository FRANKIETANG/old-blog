---
title: 补基础：React-Redux-ES6-webpack-babel
date: 2017-11-05 21:19:16
tags: [React]
---
# 补基础：React-Redux-ES6-webpack-babel

[印记中文](https://www.docschina.org/)

先看一遍中文文档，第二遍中英文对着看，第三遍才看英文文档

## React

[React 官方文档](https://facebook.github.io/react/)

````
/**
 * JSX : xml in JavaScript
 * 1、tagName
 * 2、attributes(props)
 * 3、children
 */
/**
 * 组件化: 
 * 1、函数式组件props => JSX ; 
 * 2、类组件:class A extends Component;
 */ 
/**
 * 数据源 : state  + props
 * 更新数据: setState
 * 方案: 当数据越来越复杂的时候，我们需要一个数据解决方案 ==> redux
 * 发起数据变更(click etc.) ==> action
 * 生成新的数据结构(state[store])  ==> redux的reducer生成react的state
 * 渲染(render) ==> react来做
 */
````

### DOM 对比

```
// 传统方式
<div data-id='1'>
	hello world
</div>

// vue 
<div data-id={{id}}>
	{{name}}
</div>
{
  	data: ()=> {
      	return {
          	name: 'hello world',
          	id: 1
      	}
  	}
}
// 注意 vue 是一个 MVVM 框架
// 特点就是数据和模板分离

// 实际上 react 也可以认为是一个 MVVM 框架
var JSX = <div data-id='1'>
	hello world
</div>;
render(JSX,document.getElementById('root'));
// 数据和模板绑定在一起

Virtual dom

<div width='100px'>a</div>
==>
tagname: div
attributes:{width: '100px'}
children: a

==> IOS / Android
tagname: UIButton
attributes:{display: flex}
children: ...
```

### 用 react 问候世界

```
import react, { Component } from 'react' // 基础库
import { render } from 'react-dom'  // 平台库 这是 web 库
// 为什么要引两个库呢？
// 为了跨平台
// 比如在 ios/android 用上 react-native
// import from 'react-natiev'
render(
	<h1>Hello, world</h1>              // 要塞的代码
	document.getElementById('root')    // 容器
)

// 接下来看看用 webpack 编译后的代码
_react2.default.createElement(
  'h1',
  null,
  'Hello, world!'

/*
 也就是说 react 会把 JSX 分成三个部分
 * tagname
 * attributes
 * children
*/
```

### JSX

```
// 这种模板语法就叫做 JSX
import React, { Component } from 'react'
import { render } from 'react-dom'
const RootDom = document.getElementById('root')
let JSX = <div name="frankie">
	Hello world<span>你好</span>
</div>
render(JSX, RootDom)

// 以下是编译出来的结果
/*
var JSX = _react2.default.createElement(
  'div',
  { name: 'jirengu' },
  'Hello world',
  _react2.default.createElement(
    'span',
    null,
    '\u4F60\u597D'
  )
);
*/

// 结构就相当于这个
/*
tagname: 'div'
attributes: {
  	name: 'frankie'
}
children: 'hello world',{
  	tagname: 'span'
  	attributes: null
  	children: '你好'
}
*/
```

```
// 值得注意的地方
// JSX 可以防止 XSS 漏洞
// 比如写在 JSX 里会直接输出，写在 HTML 会把 &gt; 转成 >

render() {
  let b = 'First &gt; Second'
  return (<div> {b} </div>)
} 

// 如果要不转译该怎么办？
/*
dangerouslySetInnerHTML函数
dangerouslySetInnerHTML是React提供的替换浏览器DOM中的innerHTML接口的一个函数。一般而言，使用JS代码设置HTML文档的内容是危险的，因为这样很容易把你的用户信息暴露给跨站脚本攻击.所以，你虽然可以直接在React中设置html的内容，但你要使用 dangerouslySetInnerHTML 并向该函数传递一个含有__html键的对象，用来提醒你自己这样做很危险。例如：
function createMarkup() {
  return {__html: 'First &middot; Second'};
}

function MyComponent() {
  return <div dangerouslySetInnerHTML={createMarkup()} />;
}
*/

// 实际上，JSX 里面输入 false ，null ，undefined 都是不渲染的

render() {
  return (<div> {false} {null} {undefined} {0} </div>)
} 

// 那如果有个空格在中间呢？

render() {
  let b = 'First         Second'  // 这里这个空格会打印么？ 不会的
  return (<div> {b} </div>)
} 

// 还有一些属性啊，比如 onChange onClick 都要用驼峰命名
```

[XSS是什么](https://zhuanlan.zhihu.com/p/22500730?refer=study-fe) [CSRF是什么](https://zhuanlan.zhihu.com/p/22521378?refer=study-fe)

### JS-in-JSX（动态化）

```
// 记住要用大括号来包裹变量
import React,{Component} from 'react';
import {render} from 'react-dom';
const RootDom= document.getElementById('root');
let a = 1;
let jsx1 = <div>{a}</div>;
let b = { id : 2 };
let jsx2 = <div>{b.id}</div>;
let jsx3 = ['i','love','react'].map((name) => {
    return <div>{name}</div>
});
render(<div>
    {jsx1}{jsx2}{jsx3}
    </div>, RootDom);
// 事实上 render 还有第三个参数 callback
```

### Component

```
import React,{Component} from 'react';
import {render} from 'react-dom';
const RootDom = document.getElementById('root');

/*
 * pure functional components 
 * it must never modify its own props
 */
 
const A = (props) => {
    return <div>{ props.gender } + { props.name }</div>
};
render(<A gender='male name='frankie'/>,RootDom);

// tagname  A, ==> 不是传统的html标签，而是个函数
// attributes {   ==> 函数的情况下 attributes === props
//     gender: 'male',
//     name: "frankie"
// }
// children: null

/*
 * class components
 * - Adding Local State to a Class
 * - Adding Lifecycle Methods to a Class
 */
 class FisstComponent extends Component{
   	 constructor() {
       	 super()
       	 this.state = {
           	 b: 1
       	 }
   	 }
   	 render() {
       	 return(
       	 	 <div>
       	 	 	 I am a component
       	 	 	 {this.state.b}
       	 	 </div>
       	 )
   	 }
 }
 render(<FirstComponent />,RootDom);
 
 // class 组件必须有 render 方法
 // class 组件必须继承 Component
 
 // 组件为什么要大写？区分 HTML 和 组件 
```

### life-cycle

https://facebook.github.io/react/docs/state-and-lifecycle.html

```
// 写代码的时候更具有控制力
import React,{Component} from 'react';
import {render} from 'react-dom';
const RootDom= document.getElementById('root');
class FirstComponent extends Component{
    constructor(){
        super();
        this.state = {
            b:1
        }
    }
    // shouldComponentUpdate / componentWillReceiveProps / componentDidMount 用得比较多
	shouldComponentUpdate(){   // 组件是不是应该被更新
		console.log('shouldComponentUpdate');
        return true;
	}
	componentWillUnmount(){   // 组件将会移除
		console.log('componentWillUnmount')
	}
	componentDidUpdate(){   // 组件更新好了
		console.log('componentDidUpdate')
	}
	componentWillUpdate(){   // 组件将会更新
		console.log('componentWillUpdate')
	}
	componentWillReceiveProps(){   // 组件获得了新的 props
		console.log('componentWillReceiveProps')
	}
	componentWillMount(){   // 组件将被加载
		console.log('componentWillMount')
	}
	componentDidMount(){   // 组件加载完成
        this.setState({b:1})
		console.log('componentDidMount')
	}
    render(){   // 组件将被渲染
    	console.log('render')
    	let a = '10/26'
        return <div>
           	I am a component
           		{a }
                {this.state.b}
            </div>
    }
 };

render(<FirstComponent />,RootDom);

// componentWillMount
// render
// componentDidMount
// shouldComponentUpdate
// componentWillUpdate
// render
// componentDidUpdate
```

### props

```
// <A a='1'> ==> props = {a:'1'}

import React,{Component} from 'react';
import {render} from 'react-dom';
const RootDom= document.getElementById('root');
class Welcome extends Component {
  render() {
    return <div>{this.props.gender} + {this.props.name}</div>
  }
}
/** props.children
 * React uses this.props.children to access a component's children nodes.
 * ==== ! props should be pure === // 不应该做任何修改
 */
class ChildComponent extends Component{
    render(){
        return (
        <div>
            {this.props.children}   // 如果 render 写成传统的 html 标签，那“我是个孩子”就是 {this.props.children}
            <Welcome gender='male' name='frankie' />
        </div>
        );
    }
 };
render(<ChildComponent>我是个孩子</ChildComponent>,RootDom);

 // 设置默认值 defaultProps
 // 方法 1
 Welcome.defaultProps = {
  	gender: 'male',
  	name: 'frankie'
 }
 // 方法 2
 class Welcome extends Component {
	static defaultProps = {
       gender: 'male',
  	   name: 'frankie'
	}
 }
```

```
 // 类型检测
 import PropTypes from 'prop-types';
 
 class Welcome extends Component {
	static defaultProps = {
       gender: PropTypes.string,
  	   name: PropTypes.string
	}
	render(){
      	return <div>{this.props.gender} + {this.props.name}</div> 
	}
 }
 
 // 假设这里传了数字怎么办？
 ReactDOM.render(<Welcome gender='0234' name='0234'/>,RootDom)
```

https://reactjs.org/docs/typechecking-with-proptypes.html

### setState

```
// setState 是一个异步的操作
// 改变数据只有一种方法 setState
import React,{Component} from 'react';
import {render} from 'react-dom';
const RootDom= document.getElementById('root');
/**
 * 数据源: state + props
 * props: parent ==> child 【pure不能修改】
 * state: 自身维护的数据状态
 */
class PropState extends Component{
    constructor(){
        super();
        this.state={a:'I am state'}
    }
    click(){
        /**
         * setState ==> 本组件重新render
         */
    	this.setState({
    		a:'我更新啦 哈哈哈'
    	})
    }
    render(){
        return <div onClick={()=>this.click()}>
                {this.state.a}
                <A name= {this.state.a} />
              </div>
    }
 };
const A = (props) => {
    return <div>{props.name}</div>
}
render(<PropState/>,RootDom);
```

```
// 定时器
// 记住在 numount 要取消定时器，要不然很容易会造成内存泄露
class Timer extends Component {
	constructor() {
      	super()
      	this.state = {
          	count: 0,
          	time: (new Date()).toLocaleTimeString()
      	}
	}
	tick() {
        this.setState({
            count: 1,
            time: (new Date()).toLocaleTimeString()
        })
        console.log(this.state.count)  // 0,因为 setState() 是异步函数    	
	}
	componentWillMount() {
		this.interval = setInterval(() => this.tick(),1000)
	}
	componentWillUnmount() {
      	clearInterval(this.interval)
	}
	shouldComponentUpate(nextProps, nextState) {
      	return true;
	}
	render() {
      	return (<div>the time is {this.state.time}</div>)
	}
}
 
ReactDOM.render(<Timer />,RootDom)
```

### 值得注意的地方

```
// 绑定 this 的方法
// 箭头函数和 constructor 都可以
class Name extends Component {
	constructor() {
      	super()
      	this.state = {
          	name: 'frankie'
      	}
      	// 构造函数绑 this
      	// this.handleClick = this.handleClick.bind(this)
	}
	// 箭头函数绑 this
	handleClick = () => {
      	alert(this.state.name)
	}
	render() {
      	return (<div>my name is {this.state.name}</div>)
	}
}

// 最后一种 ReactDOM.render(<Name onClick={this.handleClick.bind(this)}/>,RootDom)
// 这种是不建议的，会触发 componentWillReceiveProps 和 shouldComponentUpdate，假如在定时器里，子组件就会一直 render
// 也不能在 render 里用箭头函数
ReactDOM.render(<Name onClick={this.handleClick}/>,RootDom)
```

```
// 阻止事件冒泡
/*
先考虑一个东西
写在 React 的 div 和原生的 div 是不一样的
那它们的 event 是一样的吗？
// 
<div onclick=""></div>
function test(event){
  return false
}
// react
handleClick = (event) => {
  
}

其实是不一样的
react 的 event 是被封装过的，叫做 SyntheticEvent 能实现百分之九十的 event 原生事件 
通过 ev.nativeEvent === event 封装
ev.nativeEvent.stopImmediatePropagation()

handleClick = (event) => {
  setTimeout(()=> {
    console.log(event.type)
  })
  console.log(event.type)
}

react 的 event 是不能异步执行的
react 的 event 有一个事件值，触发完成之后就会销毁
*/

class Name extends Component {
	constructor() {
      	super()
      	this.state = {
          	name: 'frankie'
      	}
	}
	handleClick() {
      	alert(this.state.name)
      	// 可以直接调用
        // event.stopPropagation()
        // event.preventDefault()
	}	
	render() {
      	return (<div>my name is {this.state.name}</div>)
	}
}

ReactDOM.render(<Name onClick={this.handleClick.bind(this)}/>,RootDom)
```

### ref 和 DOM

https://reactjs.org/docs/refs-and-the-dom.html

```
// 利用 ref 操作 DOM
// react 不建议直接操作 DOM 元素，性能不好
class Name extends Component {
	constructor() {
      	super()
      	this.state = {
          	name: 'frankie'
      	}
	}
	handleClick = (event) => {
      	// var el = document.getElementById('content')
      	// this.refs.style.color = 'red'  这是旧的
      	this.contentRef.style.color = 'red'
	}	
	render() {
      	return (
      	// <div ref="content">  这是旧的
      	<div onClick={this.handleClick}>
            <div ref={(content) => {this.contentRef = content}}>
            my name is {this.state.name}
            </div>
      	</div>)
	}
}

ReactDOM.render(<Name />,RootDom)

/*
其实上面这种做法 React 是不推荐的
class Name extends Component {
	constructor() {
      	super()
      	this.state = {
      		color: '',
          	name: 'frankie'
      	}
	}
	handleClick(event){
		this.setState({
          	color: 'red'
		})
	}	
	render() {
      	return (
      	/*
      	直接在标签上使用style属性时，
      	要写成style={{}}是两个大括号，
      	外层大括号是告知jsx这里是js语法，
      	和真实DOM不同的是，属性值不能是字符串而必须为对象，
      	需要注意的是属性名同样需要驼峰命名法。即margin-top要写成marginTop。
      	*/
      	<div style={{color: this.state.color}} onClick={this.handleClick.bind(this)}>
            <div ref={(content) => {this.contentRef = content}}>
            my name is {this.state.name}
            </div>
      	</div>)
	}
}

ReactDOM.render(<Name />,RootDom)

*/
```

### 渲染

```
class Name extends Component {
	constructor() {
      	super()
      	this.state = {
             let arr = [{
              id: '1',
              name: 'dalao1'
            }, {
              id: '2',
              name: 'dalao2'
            }, {
              id: '3',
              name: 'dalao3'
            }]         	
      	}
	}
	render() {
      	return (
      	<div onClick={this.handleClick}>
			<ul>
				{arr.map((item, i) => {
					// return <li key={i}>{item.name}</li>
					// 这个 key 的作用是见下面
                  	return <li>{item.name}</li>
				})}
			</ul>
      	</div>)
	}
}

ReactDOM.render(<Name />,RootDom)

/*
key 的作用 react 做 diff 算法的时候使用
如果 key 能保持稳定，DOM 内容不变就可以避免重新渲染
key 不要用 index 不要用随机数

class Name extends React.Component {
	constructor() {
      	super()
      	this.state = {
             people : [{
              id: '1',
              name: 'dalao1'
            }, {
              id: '2',
              name: 'dalao2'
            }, {
              id: '3',
              name: 'dalao3'
            }]         	
      	}
	}
	handleClick(event) {
		// 这里要用深拷贝
		let people = this.state.people
		let newPeople = people.map((person => {
          	let newPerson = {...person}  // 这里相当于浅拷贝
          	if (newPerson.id == '2') {
              	newPerson.name += 'haha'
          	}
          	return newPeople  // 返回了新的地址相当于深拷贝
		}))
		this.setState({
			people : newPeople
		})
	}	
	render() {
      	return (
      	<div onClick={this.handleClick.bind(this)}>
			<ul>
				{this.state.people.map((item, i) => {
					// return <Person key={Math.random() * 100} item={item}></Person>
					// 如果像上面一样，因为 key 是不一样的，所以 react 以为这个组件没有使用过，要重新构造一份
					return <Person key={i} item={item}></Person>
				})}
			</ul>
      	</div>)
	}
}

class Person extends React.Component{
	shouldComponentUpdate(nextProps, nextState) {
      	return nextProps.item.name !== this.props.item.name
      	// 如果不用深拷贝，nextProps.item.name === this.props.item.name 是 true，相等的原因是因为 item 是引用类型
	}
  	render(){
  		console.log('render' + this.props.item.id)
  		/* 
  		这里打印的是
  		render1
  		render2
  		render3
  		避免 1 3 渲染的方法是
  		key 值要稳定
  		深拷贝
  		*/
      	return <li>{this.props.item.name}</li>
  	}
}

ReactDOM.render(<Name />,mountNode)
*/
```

[JavaScript 深入了解基本类型和引用类型的值](https://segmentfault.com/a/1190000006752076)

### 条件渲染

```
/*
方法有：三元 if else 组件 方法 &&判断
*/

class Name extends React.Component{
  	render(){
  		let isLogin = this.props.isLogin
  		let comp = null
  		if(isLogin){
          comp = <Logout />
  		}else{
          comp = <Login />
  		}
      	return(<div>
      		{comp}
      		// {isLogin ? <Logout /> : <Login />}  // 主要用三元运算
      	</div>)
  	}
}

class Login extends React.Component{
  	render(){
      	return <div>login</div>
  	}
}

class Logout extends React.Component{
  	render(){
      	return <div>logout</div>
  	}
}

ReactDOM.render(<Name isLogin={true}/>,mountNode)

/*
可以封装成一个组件
function SignIn(isLogin) {
  	if(isLogin){
      return <Logout/>
  	}else{
      return <Login/>
  	}
}

// 在 render 写
render(){
  return(<SignIn isLogin={isLogin}/>)
}
*/

/*
也可以做成一个方法
getLogIn(isLogin) {
  if(isLogin){
    return <Logout />
  }else{
    return <login/>
  }
}

render(){
  return(<div>
  	{this.getLogin(isLogin)}
  </div>)
}
*/

/*
还有一种用 && 判断
{isLogin && <Logout/>}
{isLogin && <LoginIn/>}
*/
```

### HOC

```
/*
传一个组件出一个组件
其实可以理解为一个给基础组件加方法的处理器
比如说有两个组件 Ad1 Ad2 他们有 abc 三个方法

Ad1Component{
  a(){};b(){};c(){};
}
Ad2Component{
  a(){};b(){};c(){};
}

那我们可以把方法抽离出一个 Basic 组件
BasicComponent{
  a(){};b(){};c(){};
}

然后写到 HOC 转换
function wrapper(WrapperComponent){
  return BasicComponent{
    a(){};b(){};c(){};
    render(){
      return <WrapperComponent />
    }
  }
}
*/

// 写个例子
// 在 input 组件的名字改变的时候加上一些日志的方法但是不能 input 组件上写（为什么会有这个需求？因为有可能这个组件不是你自己写的）
/*
想到一个方法用 prototype 但是会影响原型链
 function logProps(InputComponent) {
    InputComponent.prototype.componentWillReceiveProps = function(nextProps, nextState) {
      console.log('============')
      console.log('Current props: ', this.props);
      console.log('Next props: ', nextProps);
    };
    return InputComponent;
}
*/

// 正确的方法应该是利用 HOC 封装方法

class InputComponent extends React.Component{
    componentWillReceiveProps() {

    }
    render() {
        return <div>{this.props.name}</div>
    }
}

function logProps(WrappedComponent) {
    return class extends React.Component {  // 匿名组件，要实现的共有方法都在这里做
      componentWillReceiveProps(nextProps) {    
        console.log('Current props: ', this.props);
        console.log('Next props: ', nextProps);
      }
      render() {
        return <WrappedComponent {...this.props} />;
      }
    }
}

const EnhancedComponent = logProps(InputComponent);

 let name = 'dalao';
 setInterval(() => {
    name += ' hah';
    ReactDOM.render(<EnhancedComponent name={name} />, document.getElementById('root'));
 }, 1000)
```

## Redux

[Redux 官方文档](http://www.redux.org.cn/)

https://github.com/slashhuang/redux-annotation

![](https://ooo.0o0.ooo/2017/11/04/59fd9c6002509.jpg)

### 第一个简单的例子

[Object.assign()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)

```
import { createStore } from 'redux'
const initState = {}
const reducer = (state, action) => {
	return action
}
const store = createStore(reducer, initState)
store.dispatch({
	type: 'init',
	payload: 'hello world'
})
console.log(store.getState())

// redux 的整体架构
// action (动作) ===> 发起 AJAX
// reducer (Handler处理器) ===> JSON 处理
// state (最后的状态) ===> JSON 保存起来

// 通常从后端拉数据的流程是这样的
// AJAX ===> JSON ===> UI

// 所以上面这个代码拆分开来就是
const action = {
  	type: 'init',
  	payload: 'hello world',
}
const reducer = (state, action) => {  // state 是前一次保存的数据状态
  	return Object.assign({}, state, action);  // 生成下一个数据状态
}
const store = createStore(reducer, initState)
store.dispatch(action)
console.log(store.getState())
```

### createStore 源码

```
// 需要注意的是第一个参数和第二个参数（reducer, 初始化的 state）
export default function createStore(reducer, preloadedState, enhancer)

/*
判断...
*/

var currentReducer = reducer;  // 当前的处理器
var currentState = preloadedState;   // 当前的state ===> 初始化的 state
var currentListeners = []; 
var nextListeners = currentListeners;
var isDispatching = false;  // 不执行

/*
往下看
*/

function getState() {
	return currentState;  // 直接 return currentState
}

/*
往下看
*/

  function dispatch(action) {

    try {
      isDispatching = true;
      currentState = currentReducer(currentState, action);  
      // 用当前的 reducer 处理当前的 state 和 action
      // 只要 dispatch(action) 就会处理一遍生成一个 state
    } finally {
      isDispatching = false;
    }

    var listeners = currentListeners = nextListeners;
    for (var i = 0; i < listeners.length; i++) {
      var listener = listeners[i];
      listener();
    }

    return action;  // 返回一个 action
  }
  
/*
往下看
*/

  return _ref2 = {
    dispatch: dispatch,
    subscribe: subscribe,
    getState: getState,
    replaceReducer: replaceReducer
  }, _ref2[$$observable] = observable, _ref2;
  // 最后也是返回出来
```

### 回看例子分析 createStore 源码

```
import { createStore } from 'redux'
const initState = {}
const action = {
  	type: 'init',
  	payload: 'hello world',
}
const reducer = (state, action) => {   // currentState = currentReducer(currentState, action);  
  	return Object.assign({}, state, action);  // 当前的数据状态
}
debugger
const store = createStore(reducer, initState)  // reducer = (state, action) => {...}, initState = {}
debugger
store.dispatch(action)  // 所以 store 有 dispatch 方法
console.log(store.getState())  // return currentState
// Object {type: "init", payload: "hello world"}

// debugger 看过程
// var currentReducer = reducer;  // currentReducer = function reducer(state, action), reducer = function reducer(state, action)
// var currentState = preloadedState;  // currentState = Object {}, preloadedState = Object {}
  
/*
跳到 dispatch return 一个 API 集合
*/

/*
下一个 debugger
currentState = currentReducer(currentState, action); 进去看一下
return Object.assign({}, state, action);  做一个覆盖
currentState 变成了 Object {type: "init", payload: "hello world"}
*/

/*
看一下 console.log(store.getState())
function getState() {
    return currentState; // 返回 currentState
}
*/

--------------------------------------------------------------------

// 修改一下代码
import { createStore } from 'redux'
const initState = {}
const action = {
  	type: 'init',
  	payload: 'hello world',
}
const reducer = (state, action) => {
  	return Object.assign({}, state, action);
}
const store = createStore(reducer, initState)
debugger
store.subscribe(() => {
  	console.log('我注册啦')
})
store.dispatch(action)
console.log(store.getState())

// store.subscribe 进去看看
/*
  function subscribe(listener) {
	
	//...
	
    nextListeners.push(listener);    // nextListeners 是一个长度为零的数组，把 listener push 进去
    return function unsubscribe() {
    	// ...
    };
  }
*/

// store.dispatch(action) 进去看看
/*
  function dispatch(action) {
    //...
	// 这里会读当前的观察者数组
    var listeners = currentListeners = nextListeners;
    for (var i = 0; i < listeners.length; i++) {
      var listener = listeners[i];
      listener();
    }

    return action;
  }
  
  ---------------------
  
  在 console 打 listeners 会打印出 [function]
  listeners[0] 是
  function () {
  	console.log('我注册啦');
  }
*/

/*
需要注意的一点是
  // When a store is created, an "INIT" action is dispatched so that every
  // reducer returns their initial state. This effectively populates
  // the initial state tree.
  dispatch({ type: ActionTypes.INIT })
  也就是说，每调用一遍 createStore 就会执行一遍 dispatch({ type: ActionTypes.INIT })
  所以初始化的 action 是 type: "@@redux/INIT"
*/
```

### 怎么改变 dom

```
// HTML
    <div id='root'>
        1
    </div>
// JS
import { createStore } from 'redux'
const ROOTDOM = document.getElementById('root')
const initState = {}
const action = {
  	type: 'init',
  	payload: 'hello world',
}
const reducer = (state, action) => {  
  	return Object.assign({}, state, action); 
}
const store = createStore(reducer, initState) 
store.subscribe(() => {
  	ROOTDOM.innerHTML = JSON.stringify(store.getState())
})
let counter = 0
ROOTDOM.addEventListener('click', () => {
  	counter++
  	const action = {
      	type: 'click',
      	payload: counter
  	}
  	store.dispatch(action)
})
```

### 实现一个MVVM

```
// HTML
<input id="name"/>
数据预览区
<div id="preview"></div>
<div id='root'>
1
</div>
// JS
import { createStore } from 'redux';
const initState = {};
const action = {
	type: 'init',
	payload: 'hello world',
};
const reducer = (state, action) => {
	return Object.assign({}, state, action);
};
const store = createStore(reducer, initState);
const INPUTDOM = document.getElementById('name');
const PREVIEWDOM = document.getElementById('preview');
const digestUI = () => {
	PREVIEWDOM.innerHTML = store.getState().payload;
	if (PREVIEWDOM.innerHTML.length > 20) {
		alert('length is 20')
	}
};
const inputChange = () =>{
	let val = INPUTDOM.value;
	const action = {
		type: 'input_change',
		payload: val,
	};
	store.dispatch(action);
};
let counter = 0;
INPUTDOM.addEventListener('input', inputChange)
store.subscribe(digestUI);

// 可以看出好处就是行为都是分离的
```

### 中间件

```
// redux中的applyMiddleware中间件
// Middleware makes it easier for software developers
// to implement 【communication and input/output】,
// so they can focus on the 【specific purpose of their application】.
// 更专注 service 服务 input/output service 输入和输出
// ajax ==> json(乱得一笔) =service转换(中间件)=> UI(整理成好的)
// express/Koa

// 前端
// ajax ==http==>
// httpRequest(head,cookie,body)
// middlewares(解析cookie, 拿到post请求的数据)
// 数据就是好的一笔的数据
// 后端(node.js)

// 看一个例子
// 注意下面这些很多箭头的叫做高阶函数
// 例如 const highFunction = a => b => c => console.log(a+b+c); highFunction(1)(2)(3) // 6 // 一个函数执行完之后返回值是一个函数
// 多参函数 ===> 单参函数

// 如果在 createStore 用上 enhancer 逻辑就会被 applyMiddleware 控制
// return enhancer(createStore)(reducer, preloadedState)

// 前一个 next 指向下一个 action=> { next(action) }; 最后一个 next 指向 dispatch 

// 没有中间件 action ==> ==dispatch==> reducer ==> nextState;
// 有中间件 action ==middlewares==> ==dispatch==> reducer ==> nextState;

// applyMiddleware 的思想是把一堆函数封装成一个函数

import { createStore, applyMiddleware } from 'redux';
const logger1 = store => next => action => {
    console.log('current dipatch' + JSON.stringify(action));
    next(action);
};
const logger2 = store => next => action=> {
    next(action);
};
const logger3 = store => next => action=> {
    next(action);
};

const enhancer = applyMiddleware(logger1, logger2, logger3);
const reducer = (state, action) => state;
const store = createStore(reducer, {}, enhancer);
store.dispatch({type:1});
store.dispatch({type:2});
store.dispatch({type:3});
```

action 的另一种写法 https://github.com/acdlite/flux-standard-action

[What's the '@' (at symbol) in the Redux @connect decorator?](https://stackoverflow.com/questions/32646920/whats-the-at-symbol-in-the-redux-connect-decorator)

[React 实践心得：react-redux 之 connect 方法详解](http://taobaofed.org/blog/2016/08/18/react-redux-connect/)

## ES6

[ES6 教程](http://es6.ruanyifeng.com/)

[摸到 ES6 的门把手](https://frankietang.github.io/2017/10/14/%E6%91%B8%E5%88%B0%20ES6%20%E7%9A%84%E9%97%A8%E6%8A%8A%E6%89%8B/)

Stage 0 - Strawman（展示阶段）

- Stage 1 - Proposal（征求意见阶段）
- Stage 2 - Draft（草案阶段）
- Stage 3 - Candidate（候选人阶段）
- Stage 4 - Finished（定案阶段）

配 babel 的时候有用 

### Set 和 Map 的数据结构

[set-map](https://github.com/ruanyf/es6tutorial/blob/2ac6e76b38f117f2acf6c465ab70709275b4241a/docs/set-map.md)

```
// 向 Set 加入值的时候，不会发生类型转换，所以5和"5"是两个不同的值。Set 内部判断两个值是否不同，使用的算法叫做“Same-value equality”，它类似于精确相等运算符（===），主要的区别是NaN等于自身，而精确相等运算符认为NaN不等于自身。
let s = new Set()
s.size // 0
s.add(1) // {1}
s.size // 1
s.add(1) // {1}
s.size // 1
s.add('1') // {1,'1'}
s.size // 2

// 如何快速去除数组里的重复元素
[...new Set([1,2,3,4,5,4,3,2,1])]  // (5) [1, 2, 3, 4, 5]
```

### Decorators

```
// 对类做一个封装
// 懂了，大概就是在 class 上面绑定方法可以直接调用

function divide(target){
  target.prototype.divide = function(a,b) {return a/b}
  return target
}

@divide
class Math{
  add(a,b){
    return a+b
  }
}

let m = new Math()
console.log(m.divide(6,3)) // 2

// 再封装多一个判断

function divide(needAdd){
  return function(target){
    if(needAdd){
      target.prototype.divide = function(a,b) {return a/b}
    }
  }
  return target
}

@divide(true)
class Math{
  add(a,b){
    return a+b
  }
}

let m = new Math()
console.log(m.divide(6,3)) // 2
```

## webpack

[webpack 官方文档](https://webpack.js.org/)

### webpack 与 react

```
// 主要是配 loaders
{
    test: /\.js[x]?$/,        // 符合 js 或者 jsx
    loader: "babel-loader",   // 运行 babel-loader
    exclude: /node_modules/
},
```

说实话 webpack 这东西翻翻文档就好，不用死记硬背的。要什么功能直接 Google ，`npm i -D <package>`

[webpack：从入门到真实项目配置](https://juejin.im/post/59bb37fa6fb9a00a554f89d2)

## babel

[babel 官方文档](http://babeljs.io/)

### babel

```
// 因为 react 用的是 JSX 所以需要用到 babel
// 不止 JSX 还有高阶组件(HOC) 要用到 es7 的 Decorator
// 把这一堆代码转换成 JS
// 以下是配法 
{
  "presets": [
     "stage-0", // 草案 0
     "es2015",  // es6
     "react"    // react
  ],
   "plugins": ["transform-decorators-legacy"]  // 翻译 Decorator
}
```
