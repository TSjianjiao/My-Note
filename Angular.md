# Angular.js

## 1.简介

​	是以数据与逻辑为驱动的MVC框架

​	MVC是一种编程模型思想

​	M**:model **处理数据

​	V**:view **展示数据

​	C:**controller** 连接M和V

![1527918955414](C:\Users\dell\AppData\Local\Temp\1527918955414.png)

## 	2. 模块化

​		从实际程序解释，Angular的MVC是怎么操作的

```javascript
// 创建一个模块 一般只有一个
var app = angular.modeule('App', []) // 第一个参数 代表模块名字 第二个参数 以后再解释 
// 创建控制器 也就是C-------------------------------------------C
app.controller('myContro', ['$scope', ($scope)=>{
    // $scope是一个空的对象 所以我们可以对它进行赋值等操作
    // $scope相当于这个app模块对象
    // 也就是说我们在做 M 所做的事情 操作数据 ---------------------M
    $scope.xx = ...;
    $scope.oo = ...;
}]);
```

```html
<!--这就是 V -----------------------------------------------------V                                  -->
<div ng-app="App">  <!--确定模块位置 不能嵌套模块 一般只有一个模块-->
    <div ng-controllor="myContro">{{XX}}{{OO}}</div> 
    <!--确定控制位置 也就是这个div下才能输出正确的数据-->
    <!--和模板引擎一样 使用{{}}为输出-->
</div>
```

## 3. 简单指令

1. ng-app 指定应用根元素，至少有一个元素指定了此属性。
2. ng-controller 指定控制器
3. ng-show 控制元素是否显示，true显示、false不显示 display=none **实际还在**
4. ng-hide 控制元素是否隐藏，true隐藏、false不隐藏  **不占位置 实际还在**
5. ng-if 控制元素是否“存在”，true存在、false不存在**实际不在了**
6. ng-src 增强图片路径**可以用angular操作地址内容**
7. ng-href 增强地址 **同上**
8. ng-class 控制类名 =**{key:true/false}** key就是样式名 通过布尔值控制
9. ng-include 引入模板  **相当于使用ajax请求文件**
10. ng-disabled 表单禁用
11. ng-readonly 表单只读
12. ng-checked 单/复选框表单选中
13. ng-selected 下拉框表单选中

## 4. 自定义html属性

