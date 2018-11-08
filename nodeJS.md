# 1. 模块化

最简单的模块方法

```javascript
// 模块1
function a() {
    
}
function b() {
    
}
// exports就相当于一个对象
exports.a = a;
exports.b = b;
```

```javascript
// 主文件
// 这里映入的也就是exports对象
let module1 = require('./xxx');
module1.a()
module1.b()
```

模块第一次引用后就被缓存，其他地方引用使用的是同一个缓存

## 1.1 模块是怎么实现的

有几个问题：

-   为什么模块文件和其他文件的变量不会冲突？
-   模块是如何传递的？

### 1.1.1 模块的实现

```javascript
// 在任意文件中写
console.log(arguments.callee.toString)
// 会发现返回一个匿名函数
function (exports, require, module, __filename, __dirname) { 
    // 可以看到代码被包含到里面
	console.log(arguments.callee.toString())
}
```

上面代码就可以回答第一个问题，因为所有代码被包含到一个看不见的函数中，所以所有的函数声明和变量声明都变成了局部的，也就不会干扰其他模块了。

==但是==也有例外

```javascript
// 在xxx.js文件中书写
val1 = 'xxx'
//相当于
function(exports, require, module, __filename, __dirname) {
    val1 = 'xxx'
}
```

```javascript
// 引入上面的
let a = requier('./xxx')
console.log(val1)//=> 'xxx'
```

可以看到虽然没有暴露模块使用`exports`，但是引入这个模块的文件仍然可以访问这个变量，这是因为在`js`中没有使用`var/let/const`关键字声明的变量会被当做全局变量，所以能被访问到。

### 1.1.2 模块函数的参数

上面代码可以看到模块函数的参数有5个，分别答应就会发现

-   `exports`：`module.exports`的引用，等会在探讨
-   `require`：一个函数，执行引入
-   `module`：暴露对象
-   `__filename`：当前文件的绝对地址
-   `__dirname`：文件所在的目录

### 1.1.3  exports和module

先看看`module`和`expots`打印出来是什么

```javascript
function a() {
    
}
expots.a = a
console.log(module)
console.log(expots)

// module
Module {
  id: '当前文件绝对地址',
  exports: { a: [Function] },// 暴露的内容
  parent:
   Module {
     id: '.',
     exports: {},
     parent: null,
     filename: '引用者地址',
     loaded: false,
     children: [ [Circular] ],
     paths:
      [ 'E:\\workespace\\node_modules',
        'E:\\workespace\\node_modules',
        'E:\\node_modules' ] },
  filename: '当前文件绝对地址',
  loaded: true,
  children: [],
  paths:
   [ 'E:\\workespace\\node_modules',
     'E:\\workespace\\node_modules',
     'E:\\node_modules' ] }
// expots
{ a: [Function] }
```

可以看到最主要的是`expots`的内容和`module.exports`是一样的，说白了就是在某处

```javascript
let exports = module.exports;
// 因为是引用类型复制，所以改变一个另外一个就会更改
module.exports === exports //=> true
```

很明显，开头使用的那种包引用方法十分的不方便，如果有成千上百个函数，那会相当繁琐，所以可以直接覆写`module.exports`

```javascript
module.exports = {
    function a() {
    	////
	}
    function b() {
    	////
	}
    function c() {
    	////
	}
}
// 也可以替换成一个函数
module.exports = function() {
    ////////////
}
```

==注意==

​	当使用上面的方法暴露时，会导致`module.exports != exports`

​	原因很简答，`module.exports`被完全重新覆写，也就是说指向了一个			全新的内存地址，而原理来的`exports`仍然指向之前的地址，所以不再相等。

==但是==这样却不可以

​	原因也很明显，完全重写`exports`相当于让`exports`指向了一个全新的和`module.exports`完全不同的地址，所以这样设置没有任何作用。

​	也就是说`exports`并不是真正的对象

```javascript
exports = {
    a() {
       /////
    },
    b() {
        /////
    },
    c() {
        /////
    }
}
// 同样的这样也不可以
exports = fn
```



## 1.2 三种模块的引用

1.  文件模块

    也就是引入外部文件

    ```javascript
    require('文件相对路径')
    ```

2.  系统内置模块

    ```javascript
    require('模块名')
    ```

3.  第三方模块

    ```javascript
    require('模块名')
    ```

## 2. npm

常用命令

        1) npm init       初始化 生成 包描述文件 package.json
    
        2) npm i 模块名   本地安装（安装在当前目录下 自动生成node_modules 所有模块都安装在里面）
    
        3）npm i 模块名 -g     全局安装（安装在c盘npm的目录下 主要安装命令）
    
        4）npm i 模块名 --save  本地安装 且 把模块的信息 写入package.json的依赖列表
    
        5）npm remove 模块名    删除模块
    
        6）npm update 模块名    更新模块
    
        7）npm view 模块名 versions 显示模块可以使用的版本
# 2. NodeJS事件编程

## 2.1  事件模块使用

相当于自定义事件和触发

**ES5**

```javascript
// 引入events模块
var eventEmitter = require('events').EventEmitter;
// 实例EventEmitter对象
var event = new eventEmitter（）；
// 注册自定义事件
// 参数一：事件名称
// 参数二：事件处理函数
event.on('myEvent', function () {/////});
// 触发事件
// 参数一：触发事件类型
// 参数二：传递的参数
event.emit('myEvent', '参数')
```

**ES6**

```javascript
// 引入events模块
let Events = require('events');
// 继承Events类
class Event extends Events{};
// 实例Event对象
let event = new Event();

event.on('myEvent', function () {/////});
event.emit('myEvent', '参数')
```

本质上`ES5`的`eventEmitter`和`ES6`的`Events`是一样的

## 2.2 其他方法

```javascript
// 在注册事件之前设置最大注册事件数
event.setMaxListeners(20)

// 注册一个只能触发一次的事件
event.once(type, fn)

// 移除所有监听器
removeAllListeners()// 移除所有监听器
removeAllListeners(type)// 移除所有该类型的监听器

// 移除监听器
removeListener()
```





