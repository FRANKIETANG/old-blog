---
title: 补基础：JS 单元测试
date: 2017-11-13 18:07:52
tags: [JavaScript]
---
# 补基础：JS 单元测试

## 单元测试有啥用？

修改函数的实现方式难以保证输出的东西是自己想要的。

比如 GitHub 上较大的开源项目在别人贡献代码的时候要保证输出的东西和原来的一样。

单元测试就是保证这个。

而且单元测试也是一个文档，告诉别人怎么使用这个文档。

[教练我要写单元测试](https://github.com/n0ruSh/blogs/issues/2)

[Mocha](http://mochajs.org/)

[Should](http://shouldjs.github.io/)

### 例子1

现在做一个方法 `takeWhile` ，传一个数组和函数通过所有的 Mocha 测试

```
/**
 * @param {Array} arr - base array
 * @param {Function} pred - predicate
 * @returns {Array}
 */

function takeWhile(arr, pred) {
	// todo...
	/*
	第一个测试
	return []
	*/
}

module.exports = takeWhile;
```

上面的 `@param` 看这个了解就好。[@param](http://www.css88.com/doc/jsdoc/tags-param.html)

Mocha 的测试写法

```
let takeWhile = require('../../src/array/takeWhile'),
    should = require('should');  // 比较库或者叫断言库，用来看输出后的东西是不是一样的。

describe('array takeWhile', () => {  // describe 里面可以写一些小方法
    it('should be okay with empty array', () => {  // it 接一些小的用例，后面最好就给一些描述啥的
        let res = takeWhile([], (it) => {
            return it <= 8;
        });
        res.should.be.eql([]);  // 用 should 来比较是不是一个空数组
    });

    it('should be okay for normal array less than or equal 8', () => {
        let res = takeWhile([1,2,3,8,10,6], (it) => {
            return it <= 8;
        });
        res.should.be.eql([1,2,3,8]);
    });
    
});
```

运行 `mocha takeWhile.js` 实现第一个测试 `return []` 

![image](http://upload-images.jianshu.io/upload_images/3191557-c3d18a2b8f498851.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第一个通过了，第二个没有通过并且提示给了我们要输出 `[1,2,3,8]`

事实上测试用例越多，则说明这个方法越全面。

```
// 继续增加单元测试的用例

let takeWhile = require('../../src/array/takeWhile'),
    should = require('should');

describe('array takeWhile', () => {
    it('should be okay with empty array', () => {
        let res = takeWhile([], (it) => {
            return it <= 8;
        });
        res.should.be.eql([]);
    });

    it('should be okay for normal array less than or equal 8', () => {
        let res = takeWhile([1,2,3,8,10,6], (it) => {
            return it <= 8;
        });
        res.should.be.eql([1,2,3,8]);
    });

    it('should be okay for normal array greater than 3', () => {
        let res = takeWhile([1,2,6], (it) => {
            return it > 3;
        });
        res.should.be.eql([]);  // 第一个 1 就比 3 小直接返回空数组
    });

    it('should be okay for normal array greater than 3 with normal array', () => {
        let res = takeWhile([4,2,6], (it) => {
            return it > 3;
        });
        res.should.be.eql([4]);  // 第一个 4 就比 3 大直接返回 [4]
    });

    it('should be okay for normal array with objects', () => {
        let res = takeWhile([{a: 3}, {a: 4}, {a: 5}], (it) => {
            return it.a >= 3;
        });
        let obj = res[0];
        obj.should.be.eql({a: 3});  // 看看第一个对象是不是 {a: 3}
    });

    it('should be okay for normal array with objects that has property a', () => {
        let res = takeWhile([{a: 3}, {a: 4}, {c: 5}], (it) => {
            return it.hasOwnProperty('a');
        });
        res.length.should.be.eql(2);  // 有 a 的数组是否长度是 2
    });
});
```

那现在就实现方法 `takeWhile` 通过所有的单元实例

```
/**
 * @param {Array} arr - base array
 * @param {Function} pred - predicate
 * @returns {Array}
 */

function takeWhile(arr, pred) {
	// todo...
    let temp = [];
    arr.some((it) => {
        if(pred(it)) {
            temp.push(it);
        } else {
            return true;
        }
    });
    return temp;
    /**
     * let temp = [];
     * for(let it of arr) {
     *     if(pred(it)){
     *         temp.push(it);
     *     } else {
     *         return temp;
     *     }
     * }
     * return temp;
     */	
}

module.exports = takeWhile;
```

[Array.prototype.some()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/some) [for...of](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...of)

```
// 彩蛋 --- some() 的用法
let arr = [1,2,3]
arr.some(function(it) {return it < 4})  // true
arr.some(function(it) {return it === 3})  // true
arr.some(function(it) {return it > 5})  // false

arr.some(function(it) {console.log(it); return it < 4})  // 1 true
// 只要有一个返回 true 就马上截止返回 true
```

### 例子2

判断对象是不是空的对象（并不是指完全空的，原型上的东西还是要有的）

```
let isEmptyObject = require('../../src/object/isEmptyObject'),
    should = require('should');

describe('isEmptyObject', () => {
    it('empty object', () => {
        isEmptyObject({}).should.eql(true);
    });
    
    it('non-empty object', () => {
        isEmptyObject({a: 'a'}).should.eql(false);
    });

    it('should be okay for array', () => {
        isEmptyObject([]).should.eql(false);
    });

    it('should be okay for number', () => {
        isEmptyObject(1).should.eql(false);
    });

    it('should be okay for string', () => {
        isEmptyObject("abc").should.eql(false);
    });

});
```

```
/**
 * @param {Any} obj
 * @returns {Boolean}
 */

function isEmptyObject(obj) {
	// todo...
	// Object.prototype.toString.call(obj).toLowerCase() === '[object object]'
    return obj.constructor === Object && Object.keys(obj).length === 0;
}

module.exports = isEmptyObject;
```

[Object.prototype.constructor](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/constructor)

### 例子3

实现一个 trim 方法去掉前后空格（单词中间的空格不能省略）

```
let trim = require('../../src/string/trim'),
    should = require('should');

describe('string trim', () => {
    it('trim string with no spaces at beginning and end', () => {
        trim('hello world').should.eql('hello world');
    });

    it('trim string with space(s) at beginning', () => {
        trim('  hello world').should.eql('hello world');
    });

    it('trim string with tab(s) at beginning', () => {
        trim('\thello world').should.eql('hello world');
    });

    it('trim string with new line(s) at beginning', () => {
        trim('\nhello world').should.eql('hello world');
    });

    it('trim string with space(s) at beginning', () => {
        trim('hello world  ').should.eql('hello world');
    });

    it('trim string with tab(s) at beginning', () => {
        trim('hello world\t').should.eql('hello world');
    });

    it('trim string with new line(s) at beginning', () => {
        trim('hello world\n').should.eql('hello world');
    });

    it('trim string with spaces/tabs/new lines at beginning and end', () => {
        trim(' \t\nhello world \t\n').should.eql('hello world');
    });
});
```

```
/**
 * @param {String} str
 * @returns {String}
 */

function trim(str) {
    return str.replace(/^\s+|\s+$/g, "");
}

module.exports = trim;
```

[String.prototype.trim()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/Trim) 的 Polyfill 是 

```
if (!String.prototype.trim) {
  String.prototype.trim = function () {
    return this.replace(/^[\s\uFEFF\xA0]+|[\s\uFEFF\xA0]+$/g, '');
  };
}
```

### 例子4

变成小驼峰

```
let toCamel = require('../../src/string/toCamel'),
    should = require('should');

describe('string to camel case', () => {
    it('empty string', () => {
        toCamel('').should.eql('');
    });

    it('underscore string', () => {
        toCamel('hello_world').should.eql('helloWorld');
    });

    it('underscore string', () => {
        toCamel('hello_World').should.eql('helloWorld');
    });

    it('underscore string', () => {
        toCamel('hello-world').should.eql('hello-world');
    });

    it('normal string', () => {
        toCamel('helloWorld').should.eql('helloWorld');
    });
});
```

```
/**
 * @param {String} str
 * @returns {String}
 */

function toCamel(str) {
    return str.replace(/_(.)/,(whole, matched) => {  // whole 就是 _(.) matched 就是 (.) // 括号在正则表达式里就是一个捕获的关系
        return matched.toUpperCase();
    });
}

module.exports = toCamel;

console.log(whole, matched)  // _w w _W W
```

## 怎么做集成

相当于有中央服务器做集成，我提交代码，服务器自动帮我跑

[Travis CI](https://www.travis-ci.org/)

[Travis setup guide](http://blog.csdn.net/buptgshengod/article/details/39578353)

可以在 github 上做一个仓库，我一提交代码就会自动帮忙跑。（这个了解就好，其实都差不多的，就是人工跑代码和机器跑代码的区别）