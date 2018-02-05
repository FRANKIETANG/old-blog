---
title: 摸到 TypeScript 的门把手
date: 2017-10-14 00:06:12
tags: [TypeScript,Black History]
---
# 摸到 TypeScript 的门把手

[TypeScript](https://github.com/Microsoft/TypeScript)

## Basic Types（原始数据类型）

```
let a: boolean = false;
a = 'hello'
//这样是错的
//因为类型不兼容 要 a = true
let b: number = 1;
let c: string = '1';
let e: number[] = [1,2,3];
//number[] 等于 Array<number>

//这里有一个新类型 Tuple
//有限长度的有限列表
let f: [string, number] = ['1',2];

//Enum 一个数字的集合
enum g {
  hello = 0,
  world = 1,
}
enum g1 {
  hello,
  world,
}
//是默认012345这样赋值
//转成 JS
(function (g) {
    g[g["hello"] = 0] = "hello";
    g[g["world"] = 1] = "world";
})(g || (g = {}));
var g1;
(function (g1) {
    g1[g1["hello"] = 0] = "hello";
    g1[g1["world"] = 1] = "world";
})(g1 || (g1 = {}));
//TS2.4后可以赋值 string
enum g {
  hello = '0',
  world = '1',
}

//Any
//let the values pass through compile-time checks
let notSure:any = 4;
notSure = "maybe a string instead";
notSure = false;

//Void
function a(): void {
  alert('hello world')
}

//Never
function never1(): never {
  throw Error('Something failed')
}

//assert 断言
//Type assertion
let someValue1: any = "this is a string";
let strLength1: number = (<string>someValue).length;
//让 String 具有 Number 的办法
```

## Variable Declarations

```
var s = 1;
let s1 = 2;
const s2 = 3;
function s3 () {
  return 4;
} 
const s4 = () => {
  return 5；
}
```

## Interfaces（接口）

```
interface IMap {
  a: string;
}
//type contract
let obj1: IMap = {
  a: '1',
  b: 2,   //error 一定要 string
}
function sss (a: IMap): IMap{
  return a;
}

//Optional Properties
interface IOptional1 {
  a: number;
  b?: string;
}
// arg.b 有可能 undefined
function sss1(arg: IOptional1): string{
  return arg.b + ''
}

//Readonly properties
interface Point {
    readonly x: number;
    readonly y: number;
}
let point: IPoint = {
  x: 1, 
  y: 2,
}
//point.x = 23;
//只读属性没法定义

//Function Types
interface ISearchFunc {
    (source: string, subString: string): boolean;
}
//function(source, subString) 没有必要再定义
let mySearch: ISearchFunc = function(source: string, subString: string) {
    let result = source.search(subString);
    return result > -1;
}
//Indexable Types
interface IndexType {
  [key:string]:number
}
let inst1: IndexType {
  a: '1',     //error
}

//type
type aType = IndexType;
//比如说是let啊，就是类型声明对吧 type 就类似

type aaaaa = string;
let ssss: aaaaa = '1'

interface IPeople {
  eyes: number;
}
enum LapTopEnum {
  haiwei,
  lenovo,
  apple,
  IBM,
}
interface ImonkeyProgramer extends IPeople {
  laptop: LapTopEnum
}
let tangkalun: ImonkeyProgramer = {
  laptop: LapTopEnum.apple,
  eyes: 2,
}
```

> In TypeScript, interfaces fill the role of naming these types, and are a powerful way of defining contracts within your code as well as contracts with code outside of your project.

## Classes（类）

```
class A {
    a:string
    constructor() {
        this.a = '1'
    }
    hello(): string {
        return '1';
    }
}
let instA = new A();
instA.a
//转换成 JS
var A = (function () {
    function A() {
        this.a = '1';
    }
    A.prototype.hello = function () {
        return '1';
    };
    return A;
}());
var instA = new A();
instA.a;
//注意 private public protect

//Inheritance
class People {
    name: string
    constructor(name: string) {
        this.name = name;
    }
}
class A extends People {
    a: string;
    constructor() {
        super('tangkalun');
        this.a = '1';
    }
    hello(): string {
        return this.a;
    }
}
// class 不仅可以描述数据结构,同时他还有方法、属性等等的封装
let instA = new A();
instA.a;
instA.name;
//关于super()这个问题，是调用父类的构造函数
```

## Generics（泛型）

```
interface SillyBoy<T> {
  skills: T
}
const tangkalun: SillyBoy<['string','string','string']> = {
  skills: ['eat','fuck','sleep']
}
const frankie: SillyBoy<[string]> = {
  skills: ['fuck'],
}
const tang: SillyBoy<[undefined]> = {
  skills: undefined,
}
const kalun: SillyBoy<string> = {
    skills: 'sleep',
}
```

先到这里吧，我太菜了。