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

# 3. 缓存区

老版本：

```javascript
// 创建缓存区
let buf = new Buffer(10)//指定10字节大小的缓存区
	    = new Buffer([97, 98, 99])//指定按编码存入字符（只能是数字
		= new Buffer('xxx', 'utf-8')// 指定存入字符串
```

新版本：

```javascript
//参数只能是数字
let buf = Buffer.alloc(128)// 指定128字节的缓存区

// 等价于上面几个方法
const buf4 = Buffer.from([1, 2, 3]);
const buf5 = Buffer.from('tést');
const buf6 = Buffer.from('xxx', 'utf-8');
```

共同的

```javascript
buf.write(str) //写入
buf.toString() // 读取
buf.copy(target) // 把buf的内容赋值给target
```

# 4. fs模块

```javascript
const fs = require('fs')
```

## 1. `readFile 全部读取到内存`

```javascript
fs.readFile('路径', (err, data)=>{
    if(err){
        throw err
    }
    console.log(data)
})
// 同步
let data = fs.readFileSync(路径)
```

## 2.  `stat 文件信息`

```javascript
fs.stat(路径, (err, stat)=>{
    if(err){
        throw err
    }
    stat.isFile() // 是否是文件
    stat.isDirectory() // 是否是文件夹
})
// 同步
let stat = fs.stat(路径)
```

## 3.  `writeFile 写文件 `

```javascript
fs.writeFile(路径， 数据， err=>{})
```

## 4. `unlink 删除文件`

```javascript
fs.unlink(路径， err=>{})
```

## 5. `readdir 获取目录`

```javascript
fs.readdir(路径, (err, files)=>{
    // files是一个数组，不包含子文件夹
})
```

## 6. `mkdir 创建文件夹`

```javascript
fs.mkdir(路径, err=>{})
```

## 7. `rmdir 删除空文件夹`

```javascript
fs.rmdir(路径 err=>{})
```

## 8. 读写流

```javascript
// 读取流
let readStream = fs.createReadStream()
// 每64kb触发一次
readStream.on('data', (data)=>{})
readStream.on('end', ()=>{})
readStream.on('error', ()=>{})
// 写入流
let writeStream = fs.createWriteStream()
// 注意写入完成事件时finish
writeStream.on('finish', ()=>{})
writeStream.on('error', ()=>{})
```

## 9. 管道流

基于读写流,， 在读取流和写入流之间左连接作用。

```javascript
readStream.pipe(writeStream)//读取写入
```

## 10. 链式流

读取和链式流之间的中间操作

```javascript
let zlib = require('zlib');// 压缩模块
let zip = zlib.createGzip();
readStream.pipe(zip).pipe(writeStream)//中间压缩再写入
```

# 5. Path模块

```javascript
normalize()  规范化路径 
join()
dirname      当前文件所在目录
basename     文件名 
extname      文件扩展名
```



# 6. URL模块

```javascript
let url = require('url')
let urlObj = url.parse('https://xxxx')
```

```javascript
url对象属性方法：
// href 完整地址
// protocol 协议 https:
// host 主机名不带端口 www.baidu.com
// hostname 主机名带端口  www.baidu.com:443
// port 端口 443
// search 查询字符串 ?name=xxx&pwd=123
// hash 哈希 #sda
// pathname 文件路径 /sd/ssd/
```

# 7. http 服务器

## 1. 常见状态码

```javascript
1) 200  请求成功（***）

2）304  缓存 （***）

3）403  服务器拒绝访问（没有权限）
4）404  找不到资源（地址url错误）（***）
5）405  服务器不支持请求方式

6）500 内部服务器错误
```

## 2. 创建服务器

```javascript
const http = require('http');
const server = http.createServer((req, res)=>{
    
});
server.listen(3000, ()=>console.log(....))
```

```javascript
res对象响应时
//响应数据
// 纯文本  
res.writeHead(200, {"Content-Type":"text/plain"});
// html
res.writeHead(200, {"Content-Type":"text/html; charset=utf-8"});
// json（*****）
res.writeHead(200, {"Content-Type":"text/json"});
// 图片
res.writeHead(200, {"Content-Type":"image/png"}); 
或者：
res.writeHead(200, {"Content-Type":"image/jpg"}); 
// 然后发送数据
res.write()
// 不要忘了结束
res.end()
```

## 3. 发送请求

```javascript
// 发送get请求
http.get(url, (response)=>{
    let res = '';
    // 这里的响应是一个数据流不能直接获取
    // 要在data事件中获取
    response.on('data', (data)=>{
        res += data
    })
    response.on('end', ()=>{
        
    })
    // 或者使用数据流写入文件
    response.pipe(writeStream)
})
```

# 8.express

## 1. 简单使用

```javascript
const express = require('express');
const app = express();
app.get('/请求的url', (req, res) => {
    // req是请求对象
    // res是响应对象
    // res.send() 后端响应（发送）数据给前端
})
app.lisetn(3000, ()=>console.log())
```

## 2. 使用脚手架

`express-generator`

```javascript
1. 安装 cnpm i express-generator -g
2. 使用 express 项目文件夹名 -e 
3. cd 项目目录
4. cnpm i 安装模块
5. npm start启动
```

路由配置

```javascript
// routes文件夹 >>> 
router.get(url, (req, res)=>{
    
})
router.post(url, (req, res)=>{
    
})
```

## 3. 响应对象

`res.send()` 用于给前端发送数据 可以是各种格式的数据(*****)
`res.json()` 响应json格式数据给前端
`res.jsonp()` 响应json格式的数据给前端 主要用于跨域请求
`res.render('ejs模板文件', {json格式的数据})`;  ejs模板 和 数据合并渲染 发送给前端（浏览器端）(***)
`res.downLoad('文件的路径', '文件重命名')`
`res.redirect()`  重定向  你请求这个网址 直接跳转到另外一个网址

## 4. 请求对象

主要两个属性

```javascript
// 对于get请求
req.query // 获取参数
// 对其有post
req.body // 获取参数
```

==注意==

`req.body`只能正确获取表单数据格式也就是`key=value&key=value`

```javascript
// 这样可以获取数据
xhr.send('key=value&key=value')
// 如果这样 就获取不到数据
xhr.send(JSON.stringfiy({name:'xx',age:12}))
// 需要使用其他模块 body-parser
```

# 9. mongoDB

## 1. 常用命令

-   查看数据库 `show dbs`
-   切换数据库 `use database` 如果没有就会创建 没有数据就看不见
-   查看当前数据库集合 `show collections`  没有数据就看不见
-   查找数据 `db.集合名.find(查询条件)`不写条件查询的是所有
-   删除数据 `db.集合名.remove(条件)`不写条件，只写一个大括号删除全部
-   删除集合 `db.集合名.drop()`
-   删除数据库 `db.runCommand({dropDatabase:1})`
-   修改 `db.集合名.update(条件, 新值)`将旧值全部替换为新值

## 2. 高级命令

-   大于值 `db.集合名.find({ age:{$gt:30} })`
-   小于值 `db.集合名.find({ age:{$lt:30} })`
-   大于等于值 `db.集合名.find({ age:{$gte:30} })`
-   小于等于值 `db.集合名.find({ age:{$lte:30} })`
-   两个值之间 `db.集合名.find({ age:{$gt:30, $lt:40} })`
-   不等于值 `db.集合名.find({ age:{$ne:30} })`
-   除10余1的值 `db.集合名.find({ age:{$mod:[10, 1]} })`
-   值在10, 20, 30中的 `db.集合名.find({ age:{$in:[10, 20, 30]} })`
-   值不在10, 20, 30中的 `db.集合名.find({ age:{$nin:[10, 20, 30]} })`
-   值是数组且长度为3  `db.集合名.find({ age:{$size:3} })`
-   存在age属性的 `db.集合名.find({ age:{$exists:true} })`
-   age为30或者name为xxx的某个值  `db.集合名.find({ $or: [{age:30}, {name:xxx}] })`
-   内嵌属性  `db.集合名.find({relation.parents: xxxx})`
-   排序 `db.collection.find().sort({ "key1" : -1 ,"key2" : 1 })   `降序-1，升序1=

## 3. 编程操作数据库

主要使用`mongoose`

```javascript
const mongoose = require('mongoose');
// 连接数据库
// 链接地址后面写的是数据库名，没有就会创建
mongoose.connect('mongodb://localhost:27017/database',(err)=>{
    if(err) {
        throw err
    }else {
        console.log('成功')
    }
});
// 定义骨架
const schema = new mongoose.Schema({
    name:String,
    age:{
        type:Number,
        required:true
    }
})
// 发布模型
const model = mongoose.model('xxx', schema, 'xxx')// 第三个参数是集合名 没有就会创建
```

数据库主要操作

```javascript
// 增加
const instance = new model({
    name:'ooo',
    age:1121231
})
instance.save(err=>{
    if(err){
        throw err
    }else {
        console.log('成功')
    }
})

// 删除
model.findByIdAndRemove('id').then(err=>{
    ...
})//或者先查找出在remove

// 修改
model.findByIdAndUpdate('id').then()
// 查询
model.find({条件，和dos指令一样}).tenh((err.data)=>{})
model.findById('id').sort().skip().limit().exec((err, data)// exec链式操作
```

