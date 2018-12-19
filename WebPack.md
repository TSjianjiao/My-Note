# 1. 模块化发展

# 2. Webpack

## 2.1 什么是Webpack

## 2.2 Webpack特点 

## 2.3 初步使用

```javascript
// 安装命令行
cnpm i webpack@3.8.1 -g
```

```javascript
// 安装开发依赖
cnpm i webpack@3.8.1 -D
```

```javascript
// 打包命令
webpack 入口文件 出口文件
```

## 2.4 配置文件

​	由于直接使用命令行十分不便，尤其是各个模块路径不同，层级嵌套很深。所以使用配置文件会方便很多。

1.  创建配置文件

    ```javascript
    // webpack.config.js
    
    // 引入配置文件依赖
    var webpack = require('webpack');
    var path= require(‘path‘);
    
    module.exports = {
    	//入口文件
    	entry:"./scripts/a",
    	
    	//配置输出
    	output:{
    		//输出的路径
    		path:"static/", // 目录 V2.0-
    		path:path.resolve(__dirname,"static/"), // 目录，V2.0+
    		//输出的文件名
    		filename:"bundle.js",
    	}
    }
    ```

2.  这个时候就可以直接使用命令

    ```visual basic
    $ webpack
    ```

### 2.4.1 观察者配置

​	避免每次修改文件都要重新使用命令打包，可以开启观察者自动识别文件修改进行打包。

```javascript
{
    entry:...
    output:...
    watch:true // 开启观察者模式
}
```

```visual basic
$ webpack
```

### 2.4.2 Loaders

​	默认情况下，`webpack`只能加载`js`模块，想要加载更多的文件，比如`css\image\..`，就需要额外安装加载器

1.  查找需要的加载器

    `webpack`官网有对应的加载器，以`css-loader`为例

2.  下载安装

    ```visual basic
    $ cnpm i css-loader -D
    ```

3.  配置

    配置直接复制粘贴官网上的使用就行了。

    ```javascript
    {
            entry:...,
            output:...,
            watch:...,
        	// 所有加载器的配置都在这里
            module: {
                rules: [{
                    // 文件格式
                    test:/\.css$/,
                    use:['style-loader', 'css-loader'] 
                    // 使用两个加载器，注意顺序,从右到左进行加载器加载
                }]
            }
    }
    ```

### 2.4.3 插件

```javascript
// 头部需要引入Webpack
const webpack = require('webpack')
{
            entry:...,
            output:...,
            watch:...,
            module:....,
            plugin:[
				new webpack.插件名(配置)
            ]
}
```



## 2.5 Webpack-dev-server

​	基于`express`构建的一个本地服务器。

1.  安装命令行

    ```visual basic
    $ cnpm i webpack-dev-server@2.9.4 -g
    ```

2.  开发目录下安装

    ```visual basic
    $ cnpm i webpack-dev-server@2.9.4 -D
    ```

3.  启动服务器

    ```visual basic
    $ webpack-dev-server
    ```

4.  配置服务器路径

    ```javascript
    {
            entry:...,
            output:{
            	path:..., // 这里是打包后的位置，实际存在于硬盘上
                fliename:...,
                publicPath:...// 这里配置服务器的路径，虚拟的
            }
            watch:...,
            module:....,
    }
    ```

## 2.6 深入配置文件

### 2.6.1 `entry`

#### 1. 多入单出

```javascript
// a文件
require('c.js')
// b文件
require('c.js')
// c文件
....
// a、b文件都依赖c文件，但是a、b文件没有任何关系
```

```javascript
// webpack.config.js
{
	entry:["./a", './b'],
	output:{
		path:path.resolve(__dirname,"static/"),
		filename:"bundle.js",
	}
}
    
//=>打包到一个bundle.js文件中
```

#### 2. 多入多出

```java
{
    entry:{
        // 对象的形式
        // 键名就是结果的映射
        aModule:'./a.js',
        bModule:'./b.js',
    },
	output:{
		path:path.resolve(__dirname,"static/"), 
        // 输出文件名需要写成变量，[name]，自动映射entry的键名
		filename:"[name].js",
	}
}
```

## 2.7 异步加载模块

```javascript
// 在需要加载模块的地方
require.ensure([依赖列表,没有就为空], (req)=>{
    // 这里面加载的模块都会变成异步加载
    const a = req('./a.js')
})
```



