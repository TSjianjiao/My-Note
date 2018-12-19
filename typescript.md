# 1 使用

安装

```
$ npm install -g typescript
```

使用

```
$ tsc 文件名.ts
```

或者，先创建一个`typescript`初始化文件

```
$ tsc init
$ tsc
```

# 2 基础类型

大部分都和`js`一样

## 2.1 布尔值

在变量后边加个`:数据类型`，表示为类型注解，代表这个变量只能是布尔值，其他数据类型赋值会报错

```typescript
const isTrue:boolean = false 
```

## 2.2 数字

```typescript
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;
let binaryLiteral: number = 0b1010;
let octalLiteral: number = 0o744;
```

## 2.3 字符串

```typescript
// 和es6中的js一样
const str:string = 'aaa';
const age:number = 10;
const setence:string = `...${str}...${number}`//同样可以使用模板字符串
```

## 2.4 数组

数组使用和`js`一样，只不过增加了成员的类型注解，语法有点不一样

```typescript
// 数组成员必须是数字类型
const arr:number[] = [1, 2, 3]
// 或者使用泛型来表示
const arr:Array<number> = [1, 2, 3]
```

泛型后面章节解释，这里需要注意泛型需要首字母大写，不能写成`array`

## 2.5 元组`Tuple`

元组就是一个**已知元素数量和类型**的**数组**。

```typescript
let arr: [number, string];
// 元组定义 这里定义了一个有两个元素的数组，第一个元素只能是数字类型，第二个元素只能是字符串类型。
// 如果这样赋值会报错
arr = ['a', 'b']// 因为第一个元素不是数值类型
```

访问元素和普通的数组没有任何区别

```typescript
arr[0] >>> 'a'
```

访问越界的元素会默认使用`联合类型`代替，后面章节解释联合类型

```typescript
x[3] = 'world'; // OK, 字符串可以赋值给(string | number)类型

console.log(x[5].toString()); // OK, 'string' 和 'number' 都有 toString

x[6] = true; // Error, 布尔不是(string | number)类型
```

## 3 枚举

```typescript
enum Color {'Red', 'Green', 'Blue'}; // 定义一个枚举类型
const a:Color = Color.red;//>>> 0
const b:string = Color[1];//>>> 'Green'
```

注意两个类型注解的地方。

变量`a`类型为枚举类型`Color`，获取的值也是该值在枚举中的**索引**

变量`b`类型为字符串类型，因为枚举元素是字符串类型的，当然也可以不写类型`后面再讨论这种情况`

```typescript
// 上面的代码索引默认是从0开始的
// 显然这不是数组，所以可以自定义索引
enum Color { 'red' = 1, 'blue', 'green' } // 索引变成从1开始递增
const a:Color = Color.blue //>>> 2

// 也可以全部都指定
enum Color { 'red' = 1, 'blue' = 7, 'green' = 11 } // 索引变成设置的对应数字
const a:Color = Color.blue //>>> 7
```

## 4 任意值

```typescript
let notSure: any = 4; // 可以赋予任何值
notSure.ifItExists(); // 也能调用对应类型的方法
notSure.toFixed(); 

// 注意区分Object类型
let prettySure: Object = 4;// 虽然也可以赋予任何值
prettySure.toFixed(); // 但是不能调用方法
```

