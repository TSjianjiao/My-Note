# :athletic_shoe:第二章 在html中使用js

---

`<script>`标签的`src`属性可以接受来自==外部域==的`js`文件，（跨域请求

`defer`：延迟加载，只能用于外部脚本，等待整个页面解析完成了遇到`html`才执行，并不一定会按照顺序执行

`async`：异步加载，只能用于外部脚本，不保证先后顺序

`怪异模式(quirks mode)`和`标准模式(standards mode)`，除了会影响`css`样式的表现，也会影响`js`的表现：

`<noscript>`标签：

	满足下列触发方式之一就会显示标签内的内容。

> 触发方式：
>
> - 浏览器不支持脚本
> - 脚本被禁用

---

# :european_post_office:第三章 基本概念



---

## 1. 语法

- 区分大小写
- 可以使用符号作为名称
- `//`单行注释 `/***/`多行注释
- 严格模式，使用编译指示（`pragma`）`use strict`
- 语句结束加入`;`，不推荐省略，解释器不需要推测分号应该在哪里

## 2. 数据类型

基本数据类型：`undefined`、`null`、`string`、`number`、`boolean`

复杂（引用）数据类型：`Object`、`Symbol`

### 2.1 `undefined`

	一个变量未赋值时。

```javascript
let message
//尽管age没有声明 但是使用typeof的时候仍然返回undefined
typeof message >>>> undefined
typeof age >>>>> undefined
```

### 2.2 `null`

	表示空指针，对象属性值为空。这也是为什么 typeof null >>> object

- 实际上`undefined`派生自`null`，所以`null == undefined >>>> true`

### 2.3 `boolean`

	布尔类型

- 为`fasle`的值，`0/NaN`、`null`、`undefined`、`''`

### 2.4 `number`

#### 2.4.1 浮点数

	0.1+0.2 != 0.3，由于使用 IEEE754 标准计算，所有特定的浮点数计算会有误差

#### 2.4.2数值范围

	isFinite 判断是否超出了范围

#### 2.4.3 NaN

	非数值， 0/0 >>> NaN

- `NaN`参与运算的结果都是`NaN`
- `NaN`与任何数都不相等，包括它本身
- `isNaN()`判断一个数能不能转化为数值

#### 2.4.4 数值转换

	三种函数`Number()`、`parseInt()`、`parseFloat()`
	
	第一个可以用于任何类型的数据，后两个针对字符串

- `parseInt()`

  转换过程：依次转换字符串每个字符，直到遇到非数值字符（`空格`  `.`）

  非数值字符（包括 `null` `undefined` `true`）转化为`NaN`

  可以给第二个参数，表示按照几进制的方式读取，`('10',2) >>> 2`

- `parseFloat()`

  转化过程和`parseInt()`一样，只是可以识别第一个`.`

- `Number()`

```javascript
// 布尔
true >>>> 1
false >>>> 0
// null 和 undefined
null >>>> 0
undefined >>>> NaN
// 字符串 只能转换 纯数字 字符串 ‘123abc’输出NaN
十六进制  >>>> 十进制
空字符串 >>>> 0
```

### 2.5 `string`

#### 2.5.1  特点

字符串的值是不可变的，只有先销毁再重新赋值。销毁过程由后台完成

#### 2.5.2 转化为字符串

两种方式`toString()` `String()`

- `toString()`

  除了`undefined`和`null`都有这个方法，包括一个字符串中的每一个字符

  和`parseInt`一样可以写参数，但是这里是将字符串转化为对应的进制数

  `10.(2) >>>> '1010'`，而`parseInt` :`('10', 2) >>>> '2'`

- `String()`

  本质也是调用`toString`方法，而对于`toString`无法转换的`null` `undefined` `true`

  `null >>>>'null'`  `undefined >>>> 'undefined'` ``true >>> 'true'`

### 2.6 `object`

一组数据和功能的集合

`Object`的实例都包含如下属性和方法:

- `constructor`
- `hasOwnProperty(propertyName)`：检查属性在当前实例中是否存在，不检查原型链
- `isPrototypeOf(object)`：
- `propertyIsEnumerable(propertyName)`
- `toLocaleString()`
- `toString()`
- `valueOf()`

## 3. 操作符

### 3.1 自增自减

经过自增自减后，先使用`Number()`转化操作数

- 纯数字字符串，使用自增自减，会被当做数字，最后输出为`number`类型
- 非纯数字字符串，使用后输出`NaN`
- `true`先转换为1再操作，`false`一样

### 3.2 一元加减

```javascript
//一元加：和Number一样
var s1 = "01"; 
var s2 = "1.1"; 
var s3 = "z"; 
var b = false; 
var f = 1.1; 
var o = { 
  valueOf: function() { 
  return -1; 
  } 
}; 
s1 = +s1; // 值变成数值 1 
s2 = +s2; // 值变成数值 1.1 
s3 = +s3; // 值变成 NaN 
b = +b; // 值变成数值 0 
f = +f; // 值未变，仍然是 1.1 
o = +o; // 值变成数值-1 
// 一元减：也和Number一样
```

### 3.3 位操作符

```javascript
// 按位非 ~ 操作数的负值减一
var num1 = 25; // 二进制 00000000000000000000000000011001 
var num2 = ~num1; // 二进制 11111111111111111111111111100110 
alert(num2); // -26 

// 按位与 &
var result = 25 & 3; 
alert(result); //1 

// 按位或 |
var result = 25 | 3; 
alert(result); //27 

// 按位异或 不相同就为真
var result = 25 ^ 3; 
alert(result); //26 

// 左移右移
>> <<
// 无符号左右移 将整个32位整体移动
>>> <<<

```

### 3.4 布尔操作符

```javascript
// 逻辑非 ！ 任何值都可以取反 先转化为布尔值再取反
alert(!"blue"); // false 
alert(!""); // true 
alert(!NaN); // true 
alert(!12345); // false 

//  逻辑与 && 应用于任何数值
// 逻辑与是短路操作，如果第一个数为真/假就不判断后面一个了
// 只要有一个错作数不是布尔型 就不一定返回布尔值
null && xxx >>> null
undefined && xxx >>> undefined
NaN && xxx >>> NaN

// 逻辑或 || 和逻辑与一样，也是短路操作
// 如果preferredObject为null或者undefined 就会将backupObject赋值给myObject
var myObject = preferredObject || backupObject; 

null && null >>> null
undefined && undefined >>> undefined
NaN && NaN >>> NaN
```

### 3.5 乘性操作符

乘法、除法、求模（取余）。使用乘性操作符的时候会自动使用`Number()`转化操作数

```javascript
// 乘法 *
NaN * xxx = NaN
Infinity * 0 = NaN

// 除法 / 和乘法一样

// 求模（取余）
```

### 3.6 加性操作符

```javascript
// 加法
NaN + xxx = NaN
'' + xxx = 'xxx'
对象、布尔、undefined、null先使用toString()转化为字符串在相加

// 减法
对象、布尔、undefined、null先使用Number()转化为字符串在相加
```

### 3.7 关系操作符

```javascript
// > < >= <=
// 两个都是字符串就进行 编码值比较
// 其中一个是数值 另外一个使用Number()转化
// 任何数跟null比较都是false
```

### 3.8 相等操作符

```javascript
// == != 比较之前会强制转换操作数
// === !== 除了比较数值，还会比较类型
// 基本上都会先转化为number类型 true=1 ‘123’=123 然后再比较
NaN == xxx  >>>> false
null == undefined >>>> true
true == 2  >>> false
```

### 3.9 条件操作符（三元表达式）

```javascript
判断句？表达式1：表达式2
```

### 3.10 赋值操作

### 3.11 逗号操作

```javascript
let num = (1, 2, 3, 4) >>>> num = 4
// 返回表达式最后一项
```

## 4. 语句

### 4.1 `if`

### 4.2 `do-while`

### 4.3 `while`

### 4.4 `for`

```javascript
var count = 10; 
for (var i = 0; i < count; i++){ 
     alert(i); 
} 
alert(i); //10  使用var定义的变量 在for外面也能访问
// 使用let就不能被外部访问 因为let有块级作用域
```

### 4.5 `for-in`

```javascript
// 遍历顺序是不一定的
```

### 4.6 `lable`

```javascript
xxx:for(..) {
    break xxx
    // continue xxx
}
```

### 4.7 `break` `continue`

### 4.8 `with` 

```javascript
var qs = location.search.substring(1); 
var hostName = location.hostname; 
var url = location.href; 
// 使用with改写
with(location){ 
 var qs = search.substring(1); 
 var hostName = hostname; 
 var url = href; 
} 
// 不建议使用 降低性能
```

### 4.9 `switch`

js中可以使用任何类型的数据

switch 语句在比较值时使用的是==全等==操作符，因此不会发生类型转换（例如，
字符串"10"不等于数值 10）

```javascript
switch ("hello world") { 
 case "hello" + " world": 
 alert("Greeting was found."); 
 break; 
 case "goodbye": 
 alert("Closing was found."); 
 break; 
 default: 
 alert("Unexpected message was found."); 
} 
```

## 5. 函数

### 5.1 参数

函数的参数不一定非要传完，原因是函数内部把参数用数组表示的。

函数内部有一个对象`arguments`，相当于一个参数数组

```javascript
function sayHi() { 
 alert("Hello " + arguments[0] + "," + arguments[1]); 
} 
arguments.length
// 修改arguments中的值也会修改形参的值  但是内存空间不同
// 虽然没有指定后两个形参 但是仍可接收两个参数 并组合在hello后面

```

### 5.2 没有重载

重载：两个函数名字可以相同，只要形参类型和数量不同就可以

js中两个相同名称的函数，后面一个为准

---

# :articulated_lorry:第四章 变量、作用域、内存问题

---

## 1.基本类型和引用类型

基本类型：变量就代表一个内存区域，数值就存在这个区域里面

引用类型：变量就只是一个代号，代表对象内部的所有数据的集合，但是数据是在某个内存（堆）位置的。

### 1.1  复制变量值

- 基本类型

  复制值至另一个变量，两个变量完全独立，参与计算互不影响

- 引用类型

  复制对象的值，实际上是把`指针`复制了，也就是说副本指针的指向和源数据一样，所以改变其中任意一个也会影响到另外一个。

### 1.2 传递参数

	函数的参数传递，实际上就是将实参的值==复制给`arguments`对象中的元素==	
	
	可以把函数的参数看成是局部变量，运行完成后就被销毁了，除非`return`

### 1.3 检测类型

	`typeof`对于==引用类型的值==判断只会返回`object`，因为所有引用类型的值都是`Object`的实例。
	
	使用`instanceof`

```javascript
alert(person instanceof Object); // 变量 person 是 Object 吗？
alert(colors instanceof Array); // 变量 colors 是 Array 吗？
alert(pattern instanceof RegExp); // 变量 pattern 是 RegExp 吗？
```

## 2. 执行环境及作用域

### 2.1 执行环境

	分为`全局执行环境`和`局部执行环境`
	
	执行上下文(`context`)，全局作用域，每个函数也有自己的作用域，每个作用域可以访问的变量、数据包括自己的作用域和它父级、父级的父级...的数据，这些数据都存储在一个`变量对象`里。

```javascript
var color = "blue"; 
function changeColor(){ 
 var anotherColor = "red"; 
 	function swapColors(){ 
 		var tempColor = anotherColor; 
 		anotherColor = color; 
 		color = tempColor; 
 
 		// 这里可以访问 color、anotherColor 和 tempColor 
 	} 
 // 这里可以访问 color 和 anotherColor，但不能访问 tempColor 
 swapColors(); 
} 
// 这里只能访问 color 
```

### 2.2 延长作用域链

	`try-catch`和`with`会临时添加变量对象在前端

```javascript
function buildUrl() { 
 	var qs = "?debug=true"; 
 		with(location){ 
 			var url = href + qs; 
 		} 
 	return url; 
} 
// 可以这么想， with不是function所以不算局部作用域，因此能url被访问到
```

### 2.3  变量声明

	未使用`var`等字符声明的变量，默认为全局变量

### 2.3  查询标识符

```javascript
var color = "blue"; 
function getColor(){ 
 var color = "red"; 
 return color; 
} 
alert(getColor()); //"red" 
// 这里想要在函数内部访问全局变量color是不可能的
// 搜索标识符的时候，也就是搜索定义的时候是从自己的作用域开始向上搜索
// 而自己的作用域里面有一个标识符，所以只有这个局部变量color有效
// 想要在函数里面访问到全局变量，使用window.color，因为window是全局作用域的定级对象
// 只有使用var定义的变量才能被window访问，let定义的不行
```

## 3. 垃圾收集

	js拥有自动垃圾回收机制，按照一定间隔找出不再使用的变量并释放其内存。
	
	没用的变量会被打上标记，有两种实现策略

- 标记清除

  在变量进入环境和离开环境时分别做标记，如`反转某一位`。

  大部分浏览器都是标记清除

- 引用计数

  当声明了一个变量并将一个引用类型值赋给该变量时，则这个值的引用次数就是 1。如果同一个值又被赋给另一个变量，则该值的引用次数加 1。相反，如果包含对这个值引用的变量又取得了另外一个值，则这个值的引用次数减 1。当这个值的引用次数变成 0 时，则说明没有办法再访问这个值了，因而就可以将其占用的内存空间回收回来

> `BOM`和`DOM`，是使用`C++`的`COM（Component Object Model，组件对象模型）`对象的形式实现的，而这个对象的回收机制就是使用的引用计数
>
> ==所以==当循环引用时就不能释放内存

- 性能问题

  `IE`早期策略是达到固定临界值才触发垃圾收集，当有程序一直超过临界值，垃圾回收就必须一直运行，严重影响的性能。

- 管理内存

  `解除引用`：一个数据不再使用，手动赋值`null`释放引用
	```javascript
	function createPerson(name){ 
	 	var localPerson = new Object(); 
	 	localPerson.name = name; 
	 	return localPerson; 
} 
var globalPerson = createPerson("Nicholas"); 
// 手工解除 globalPerson 的引用
globalPerson = null; 
	```

---

# :biking_woman:第五章 引用类型

---

`引用类型`是一种数据结构，也被称为`类`，但是js并不完全是面向对象的语言，所以和类不相同

引用类型的值也就是对象，是引用类型的一个实例。

对象是某个特定引用类型的实例

```javascript
var person = new Object(); 
// 这行代码创建了 Object 引用类型的一个新实例，然后把该实例保存在了变量 person 中。使用的构造函数是 Object，它只为新对象定义了默认的属性和方法。
```

## 1. `Object`类型

- `new`字符创建

  ```javascript
  var person = new Object(); 
  person.name = "Nicholas"; 
  person.age = 29; 
  ```

- 字面量创建

  ```javascript
  var person = { 
   name : "Nicholas", 
   age : 29 
  }; 
  // 使用字面量判断可选参数有没有输入
  // typeof 若没有就返回null也就是false
  function displayInfo(args) { 
    var output = ""; 
    if (typeof args.name == "string"){ 
  ```

对象属性除了通过`.`来访问也可以使用`[]`来访问，使用`[]`可以在里面填入变量。

`[]`可以访问如`first name`这种有非法字符的属性名。

## 2. `Array`类型

与很多语言不同，每一个数组元素可以保存任何数据类型的数据。

- 构造器创建

  ```javascript
  let a = new Array();
  let a = new Array(10);// 创建一个长度为10的数组
  let a = new Array('name', 'age', 12);// 直接写入数据
  let a = Array() // 可以省略new
  ```

- 字面量

  ```javascript
  let a = ['name', 12, 'age']
  let a = ['name', 12,] // 这样表示有两项或者三项 不要这样
  ```


- 数组长度

  使用`.length`属性可以访问，同时也能更改长度，删除数组最后一项或者增加最后一项。

### 2.1 检测数组

- `instanceof`

  问题是，当网页有多个框架`frame`的时候，会有多个全局作用域，不止一个构造函数。

- `Array.isArray()`

  这个方法只关心是不是数组，不关心是哪个构造函数创造的。

### 2.2 转换方法

- `.toString()`

  对每一项都使用`.toString()`，输出以逗号分隔的字符串

- `join()`

  区别就是可以指定分隔符

### 2.3 栈方法

`pop()`：返回弹出数据

`push()`：返回数组长度

### 2.4 队列方法

`shift()`：返回移除数据

`unshift()`：返回数组长度

### 2.5 重排序方法

- `reverse()`

  反转数组

- `sort()`

  默认从小到大（字符编码）排序，==原理==是将数组每项`toString()`，然后比较字符串

  ==比较函数==：

> 1. 如果第一个参数应该在第二个参数之前，返回负数
> 2. 如果相等返回0
> 3. 如果应该在后面返回正数

```javascript
function compare(value1, value2) { 
 if (value1 < value2) { 
 return 1; 
 } else if (value1 > value2) { 
 return -1; 
 } else { 
 return 0; 
 } 
} 
// 对于数值类型
function compare(value1, value2){ 
	 return value2 - value1; 
} /// 从大到小
```

### 2.6 操作方法

- `concat()`

  连接两个数组，参数不是数组也可以

  ==原理==：先复制当前数组，然后将参数数组添加到副本中。如果不给参数就只会复制当前数组。

  单纯复制的数组不会影响原数组。

- `slice(start, end)`

  切割数组，不会影响原数组

  包含起点，不包含终点

- `splice(start, num, insert)`

  1. 删除功能

     从`start`开始删除`num`个

  2. 插入功能

     第三个参数插入的数据，先删除再插入

  3. 替换功能

     和插入一样，插入的时候不给删除数就行，替换的时候指定删除几个。

  返回一个数组，包含被删除的项

### 2.7 位置方法

`indexOf()` `lastIndexOf()`，接收两个参数，要查找的项和查找的起点位置

没有匹配的返回`-1`，搜索到返回所在索引

查找的时候是按照全等`===`的方式

### 2.8 迭代方法

	总共五种方法，每种方法都接收两个参数，每一项运行的函数和运行该函数的作用域对象`this`。第一个参数的函数可以传入三个参数，`item` `index` `本身`。

直接操作`item`是不会影响原数组的，除非`arr[index]`

- `every()`：每一项都满足返回`true`否则`false`
- `filter()`：返回满足条件的项组成的数组
- `forEach()`：不返回，只是每一项调用函数
- `map()`：返回每一项调用后组成的数组
- `some()`：有一项满足就返回`true`否则为`false`

### 2.9 归并方法

`reduce()` `reduceRight()` 两个参数，第一个是操作函数，对二个是基准值

函数有三个参数`（prev, cur, index, arr）`，返回所有项经过这个函数操作后的值

## 3. `Date`类型

对象创建方式

```javascript
let now = new Date();// 当前时间 毫秒
let time = new Date(2017/1/1)// 指定时间
//获取当前时间更好的是
let now = Date.now()

```

## 4.`RegExp`类型

`var expression = / pattern / flags ; `这样创建

`flags`：可以插入三个标志`/g`全局匹配`/i`不区分大小写`/m`多行模式

## 5.`Function`类型

每个函数实际上是`function`类型的实例，都具有其他引用类型的属性和方法。

```javascript
function a() {
    // 函数声明
}
let a = function() {
    // 函数表达式
}
let a = new Fuction('a', 'b', 'return a+b')
// 函数名就是指针 函数体是对象
```

### 5.1 函数声明和函数表达式

```javascript
// 使用函数声明的时候，就算声明在使用之后，解释器会有  函数声明提升的过程，自动将声明提到代码顶部
alert(sum(10,10)); 
function sum(num1, num2){ 
 return num1 + num2; 
} 
// 下面使用函数表达式就会报错
// 因为使用之前 sum这个变量并没有值
alert(sum(10,10)); 
var sum = function(num1, num2){ 
 return num1 + num2; 
}; 
```

### 5.2 可以作为值

函数也可以作为值，传入另一个函数，或者作为返回值

### 5.3 内部属性

只能在函数内部使用

有两个特殊对象`arguments`和`this`

- `arguments`

  类数组对象，包含传入函数的所有参数

  `callee`属性，是一个指针，指向拥有这个`arguments`的函数

```javascript
function factorial(num){ 
 	if (num <=1) { 
 		return 1; 
 	} else { 
        return num * factorial(num-1) 
 	} 
} // 递归函数 阶乘
// 但是这样想改变函数名称就需要改变里面的名称
// 所以使用 callee属性
function factorial(num){ 
	 if (num <=1) { 
 		return 1; 
     } else { 
 		return num * arguments.callee(num-1) 
 	} 
}
```

- `this`

  引用的是==函数执行的环境*对象*==

  ```javascript
  window.color = "red"; 
  var o = { color: "blue" }; 
  function sayColor(){ 
   	alert(this.color); 
  } 
  sayColor(); //"red" 
  o.sayColor = sayColor; 
  o.sayColor(); //"blue" 
  
  // 首先
  // 在全局环境中 调用了函数sayColor 所以函数的执行环境对象为全局对象window
  // 其次
  // 对象o里面的方法sayColor，执行环境对象为o，所以this指向o
  ```

函数还有一个特殊的对象属性`caller`

```javascript
function outer(){ 
 	inner(); 
} 
function inner(){ 
 	alert(inner.caller); 
} 
outer(); 
// outer调用了inner，所以输出为outer的源代码
```

### 5.4 函数属性和方法

- `length`，函数希望接收的参数个数
- `prototype`，原型属性

两个方法

	都是在特定的作用域中调用函数，相当于设置`this`的值

- `apply()`

  两个参数，函数作用域和参数数组

- `call()`

  两个参数，只是第二个参数不能给数组或者参数对象，只能将参数一 一列出

```javascript
window.color = "red"; 
var o = { color: "blue" }; 
function sayColor(){ 
  alert(this.color); 
} 
sayColor(); //red 
sayColor.call(this); //red 
sayColor.call(window); //red 
sayColor.call(o); //blue 
//扩充作用域的最大好处，就是对象不需要与方法有任何耦合关系
// 这里的sayColor不用像上一个例子一样，赋值到对象里面
var objectSayColor = sayColor.bind(o); 
objectSayColor(); //blue 
```

- `bind()`

## 6. 基本包装类型

还有三种特殊的引用类型：`Boolean` `Number` `String`

每当读取一个基本类型值的时候，后台就会创建一个对应的基本包装类型的对象

```javascript
// 通常会这样
var s1 = "some text"; 
var s2 = s1.substring(2);
// 相当于
var s1 = new String("some text"); // 创建实例
var s2 = s1.substring(2); // 调用方法
s1 = null; // 销毁实例
```

### 6.1 引用类型与包装类型的区别

主要区别就是==生命周期==。

使用 new 操作符创建的引用类型的实例，在执行流离开当前作用域之前都一直保存在内存中。

自动创建的基本包装类型的对象，则只存在于一行代码的执行瞬间，然后立即被销毁。

==因此==我们不能在运行时为基本类型值添加属性和方法

对包装类型使用`typeof`会返回`object`

==注意==：

> `new Number()`和`Number(val)`并不是一样的

### 6.2 `boolean`类型

```javascript
var falseObject = new Boolean(false); 
var result = falseObject && true; 
alert(result); //true 
// 因为逻辑判断的时候 会先进行转换 所有对象都是true
```

### 6.3 `Number`类型

`toFixed()`：按照参数返回几位小数

```javascript
var num = 10; 
alert(num.toFixed(2)); //"10.00" 
var num = 10.005; 
alert(num.toFixed(2)); //"10.01" 
```

`toExponential()`：科学计数法`e`

`toPrecision()`：参数表示用几位数表示数字

### 6.4 `String`类型

#### 字符方法：

`charAt()` `charCodeAt()` 接收位置，返回字符串/编码

#### 字符串操作方法：

`concat()`：连接字符，可以给多个参数

```javascript
var stringValue = "hello "; 
var result = stringValue.concat("world"); 
alert(result); //"hello world" 

var stringValue = "hello "; 
var result = stringValue.concat("world", "!"); 
alert(result); //"hello world!" 
```

`slice()`、`subString()、 substr()`，三种基于字符串创建新字符串的方法。

第一个参数：都是指定起始位置

第二参数：前两个指定结束位置，第三个决定几个字符

三个的区别只在参数为负值的时候处理方式不同

#### 字符串位置方法

`indexOF()` `lastIndexof()`，未搜索到返回`-1`

#### `trim()`

创建一个字符串副本，删除前后空格

#### 字符串大小转换方法

`toLowerCase()`、`toLocaleLowerCase()`、`toUpperCase()` `toLocaleUpperCase()`

#### 字符串匹配

`RegExp.exec(str)`和`str.match(reg)`一样

```javascript
str.match(reg) // 返回数组
str.match(str) // 也可以输入字符串当参数
str.search()// 参数和match一样
			// 返回匹配字符串的位置
			// 没有匹配的返回-1

// replace
var text = "cat, bat, sat, fat"; 
var result = text.replace("at", "ond");
// 字符串当参数只能替换第一个
alert(result); //"cond, bat, sat, fat" 
result = text.replace(/at/g, "ond"); 
// 正则表达式可以全部替换
alert(result); //"cond, bond, sond, fond" 

// split
var colorText = "red,blue,green,yellow"; 
var colors1 = colorText.split(","); //["red", "blue", "green", "yellow"] 
var colors2 = colorText.split(",", 2); //["red", "blue"] 
var colors3 = colorText.split(/[^\,]+/); //["", ",", ",", ",", ""] 

// localeCompare
比较两个字符串在字母表上的大小
var stringValue = "yellow"; 
alert(stringValue.localeCompare("brick")); //1 // b在y之前
alert(stringValue.localeCompare("yellow")); //0 
alert(stringValue.localeCompare("zoo")); //-1  // z在y之后
//注意大小写的区别 这个比较和地区有关系

// fromCharCode
将编码转化为字符
```

## 7.单体内置对象

不依赖于宿主环境的对象，在程序执行前就已经存在了

### 7.1 `Global`对象

	所有全局变量和函数实际上就是`Global`的属性和方法，而`js`中没有访问它的实现。

##### 1. `URI`方法

`encodeURI()`和`encodeURIComponent()`都可以对字符串编码成浏览器能理解的。

他们的区别是前者不会编码`/` `:`等特殊字符，而后者全部都会编码。

常用的是后者，用来编码查询字符串

##### 2. `eval()`方法

只接受一个参数，一段`js`代码字符串。执行的时候相当于将其中的代码插入到对应的位置。

```javascript
eval("function sayHi() { alert('hi'); }"); 
sayHi(); 
// 但是这样会报错
// 因为eval里面的变量和函数不会被提升，只有在执行eval的时候才会有这行代码。
sayHi(); 
eval("function sayHi() { alert('hi'); }"); 
```

##### 3. 属性

![](E:\培训\笔记图\global属性.png)

##### 4. `window`对象

在浏览器中，全局对象的是通过`window`对象来实现的，所以全局变量和函数就是`window`对象的一部分。

### 7.2  `Math`对象

##### 1. 属性

![](E:\培训\笔记图\math属性.png)

##### 2. `min()`和`max()`方法

```javascript
// 因为apply可以接受参数数组 所以用apply来传递数组
// 第一个参数 作用域就写成Math
var values = [1, 2, 3, 4, 5, 6, 7, 8]; 
var max = Math.max.apply(Math, values); 
// es6
let max = Math.max(...values)
```

##### 3.舍入方法

`ceil()`向上舍入、`floor()`向下舍入、`round()`四舍五入

##### 4.`random()`方法

```javascript
var num = Math.floor(Math.random() * 9 + 2); 
 2 数到 10 要数 9 个数，因此可能值的总数就是 9，而第一个可能的值就是 2
```

##### 5. 其他方法

![](E:\培训\笔记图\math其他方法.png)

---

# :man_juggling:第六章   面向对象的程序设计

---

传统面向对象的语言，都拥有`类`的概念`类 >>> 对象 >>>  实例`，而`js`没有。`ECMA`把对象定义为一个包含任意数据类型的无序集合。

## 1.属性类型

对象的属性值拥有两种属性类型。不能访问的内部值用`[[ ]]`表示

### 1. 1 数据属性：

针对对象属性来说的，包含四个特性

- `[[configurable]]`：可定义性，1.能否通过`delete`删除该属性 2. 能否修改为访问器属性
- `[[writable]]`：可写性，1. 能否修改属性值
- `[[enumerable]]`：可枚举性，2. 能否通过for-in返回key
- `[[value]]`：该属性包含的值

```javascript
let a = {
    name:'xxx'
};
Object.getOwnPropertyDescriptor(a, 'name')
// 使用object内置方法getOwnPropertyDescriptor，获取某个对象的某个属性的数据属性
```

```javascript
// configurable
delete a.name
console.log(a.name) ====> undefined
// writable
a.name = 'OOO';
console.log(a.name) ====> OOO
// enumerable
for(i in a) ====> "name"
// value
```

#### 1 修改数据属性的方法

`Object.defineProperty()`：被修改的属性所在的对象，属性名，一个描述符对象

如果没有指定某些数据属性，默认为`false`

如果设置`configurable`为`false`会导致下次重新设置失败

```javascript
Object.defineProperty(person,'job',{
    emumerable:false,//不可枚举   ======> for-in执行到job属性时会报错
    value:'singer',
    writable:false,//不可写  =====> a.job="xx" 会报错
    configurable:true
});
```

### 1.2访问器 / 存储器属性

这个属性没有包含数据值，包含的是get / set方法。

访问时触发`get`，修改时触发`set`

同样拥有四个特性：

- `[[configurable]]`：可定义性， 1. 能否被`delete`删除 2. 能否修改特性
- `[[enumerable]]`：枚举性， 1. 能否被for-in遍历到key值
- `[[Get]]`：在读取属性时调用的函数，只指定get代表此属性默认为只读
- `[[Set]]`：在写入时调用的函数

==访问器属性==不能直接定义，只能通过`Object.defineProperty()`定义。

```javascript
let a = {
    _name: 'XXX',// 必须书写下划线 表示是内部属性只能通过 对象的方法 来读写
    age: 12
}
// 将属性_name定义为访问器属性
Object.defineProperty(a, 'name', {
    get() {
        return this._name
    },
    set(newName) { // set传入一个参数 即新值
        if(newName !== this._name) {
            this._name = newName;
        }
    }
})
// 第二种定义方法
let a = {
    age:12,
    get _name() {
        return this._name
    }
    set _name(newName) {
    	if(newName !== this._name) {
            this._name = newName;
        }
	}
}
```

### 1.3 使用情况

- 需要设置一个属性的同时要改变其他属性值
- 监听数据变化，类似`vue`

### 1.4 定义多个属性

`Object.defineProperties()`

```javascript
var book = {}; 
Object.defineProperties(book, { 
     _year: { 
     	value: 2004 
     }, 
 
     edition: { 
     	value: 1 
     }, 
     year: { 
     	get: function(){ 
            return this._year; 
 		}, 
 		set: function(newValue){ 
 			if (newValue > 2004) { 
 				this._year = newValue; 
 				this.edition += newValue - 2004; 
 			} 
 		} 
 	}	 
}); 
```

## 2. 创建对象

使用`Object`构造函数或者字面量创建对象==缺点==

- 创建一个属性方法相似的对象会产生大量重复代码

### 2.1  工厂模式

- 每次都实例一个`Object`
- 创建的对象都是拥有相同属性的，只是值不同

```javascript
function createPerson(name, age, job){ 
     var o = new Object(); 
     o.name = name; 
     o.age = age; 
     o.job = job; 
     o.sayName = function(){ 
        alert(this.name); 
     }; 
     return o; 
} 
// 注意这里没有使用new操作符
var person1 = createPerson("Nicholas", 29, "Software Engineer"); 
```

==缺点==：如上述例子，所有对象的类型都是`Object`，不能实现区分

### 2.2 构造函数模式

- 没有显示地创建对象

- 属性和方法给了`this`对象

- 没有`return`

  这样创建的对象既是`Object`的实例也是`Person`的实例，实现了区分不同类型的对象

```javascript
function Person(name, age, job){ 
     this.name = name; 
     this.age = age; 
     this.job = job; 
     this.sayName = function(){ 
        alert(this.name); 
     }; 
} 
var person1 = new Person("Nicholas", 29, "Software Engineer"); 
var person2 = new Person("Greg", 27, "Doctor"); 
```

`new`过程：

1.  创建一个新对象
2.  `this`指向新对象
3.  执行构造函数代码，给新对象添加属性
4.  返回新对象

#### 1. 构造函数当做函数

	构造函数和普通函数的区别只是调用方法不同。

```javascript
// 当作构造函数使用
var person = new Person("Nicholas", 29, "Software Engineer"); 
person.sayName(); //"Nicholas" 

// 作为普通函数调用
Person("Greg", 27, "Doctor"); // 添加到 window 
window.sayName(); //"Greg" 


// 在另一个对象的作用域中调用
// 这个例子相当于new的执行步骤
var o = new Object(); 
Person.call(o, "Kristen", 25, "Nurse"); 
o.sayName(); //"Kristen" 
```

#### 2. 构造函数的问题

==缺点==：对于方法，因为函数本身也是对象，所以每次`new`一个对象的时候，里面的方法也会实例一次，对于执行同样功能的函数，多次实例化没有必要。

将函数声明（实例函数对象）放在构造函数外，可以解决这个问题

```javascript
function Person(name, age, job){ 
     this.name = name; 
     this.age = age; 
     this.job = job; 
     this.sayName = sayName; 
} 
function sayName(){ 
     alert(this.name); 
} 
var person1 = new Person("Nicholas", 29, "Software Engineer"); 
var person2 = new Person("Greg", 27, "Doctor"); 
```

==缺点==：表面看这个函数是全局的，但是只能由某个对象调用，写在构造函数外毫无封装性。

### 2.3 原型模式

	每个**函数**都有一个`prototype`属性，这个属性是一个`指针`，指向原型对象，**原型对象**包含实例所共享的所有属性和方法。

```javascript
function Person(){ 
} 
Person.prototype.name = "Nicholas"; 
Person.prototype.age = 29; 
Person.prototype.job = "Software Engineer"; 
Person.prototype.sayName = function(){ 
 alert(this.name); 
}; 
var person1 = new Person(); 
person1.sayName(); //"Nicholas" 
var person2 = new Person(); 
person2.sayName(); //"Nicholas" 
alert(person1.sayName == person2.sayName); //true 
```

#### 1. 理解原型对象

 1.  只要创建了函数，就会创建一个`prototype`属性

 2.  `prototype`默认会拥有一个`constructor`属性

 3.  `prototype`属性是一个指针，指向`原型对象`。

 4.  `constructor`属性也是一个指针，指向包含`prototype`属性的函数。

 5.  每个实例有一个无法被访问的属性`[[prototype]]`，浏览器里显示为`_proto_`或者`<prototype>`，这个属性也是一个指针，指向`原型对象`，不是构造函数。

     ![](E:\培训\笔记图\原型对象.png)

```javascript
// 通过isPrototypeOf检查 [[prototype]]和原型对象之间的关系
alert(Person.prototype.isPrototypeOf(person1)); //true 
alert(Person.prototype.isPrototypeOf(person2)); //true 
// 通过getPrototypeOf 得到[[prototype]]值
alert(Object.getPrototypeOf(person1) == Person.prototype); //true 
alert(Object.getPrototypeOf(person1).name); //"Nicholas" 
```

当代码读取某一个属性时，先检查自身有无此属性，然后再检查原型，依次向上查找。

```javascript
function Person(){ 
} 
Person.prototype.name = "Nicholas"; 
Person.prototype.age = 29; 
Person.prototype.job = "Software Engineer"; 
Person.prototype.sayName = function(){ 
  alert(this.name); 
}; 
var person1 = new Person(); 
var person2 = new Person(); 
person1.name = "Greg"; 
alert(person1.name); //"Greg"——来自实例
alert(person2.name); //"Nicholas"——来自原型
// 因为person1自身已经可以找到name这个属性，所以就不会再往上查找了
```

就算将实例里面设置的属性值设置为`null`也不能回复访问原型的属性，只能通过`delete`删除属性才能恢复与原型的连接。

```javascript
// hasOwnProperty只返回实例属性 不返回原型属性
alert(person1.hasOwnProperty("name")); //false 
alert(person1.name); //"Greg"——来自实例
alert(person1.hasOwnProperty("name")); //true 
```

#### 2. 原型与in操作符

```javascript
// 无论来自实例还是原型都会返回true
person1.name = "Greg"; 
alert(person1.name); //"Greg" ——来自实例
alert(person1.hasOwnProperty("name")); //true 
alert("name" in person1); //true 
alert(person2.name); //"Nicholas" ——来自原型
alert(person2.hasOwnProperty("name")); //false 
alert("name" in person2); //true 
delete person1.name; 
alert(person1.name); //"Nicholas" ——来自原型
alert(person1.hasOwnProperty("name")); //false 
lert("name" in erson1) //true 
// hasPrototypeProperty只有来自原型的属性才会返回true
var person = new Person(); 
alert(hasPrototypeProperty(person, "name")); //true 
person.name = "Greg"; 
alert(hasPrototypeProperty(person, "name")); //false 
// for-in
返回所有可枚举的属性（但是开发人员自己屏蔽枚举的属性仍然可以被循环），无论在原型还是实例里面
// Object.keys() 返回对象上的所有可枚举属性
//Object.getOwnPropertyNames() 无论可否被枚举都能返回
```

#### 3. 更简洁的原型语法

```javascript
function Person(){ 
} 
// 将原型对象直接重写
Person.prototype = { 
    name : "Nicholas", 
    age : 29, 
    job: "Software Engineer", 
    sayName : function () { 
        alert(this.name); 
    } 
}; 
// 通过字面量创建对象 相当于new Object()
// 这样原型对象成为了Object的一个实例
// 而prototype指向了原型对象(一个Object的实例)
// 包含prototype属性的函数变成了Object的构造函数
// 相当于跟着原型对象走
// constructor 指向了包含prototype属性的函数

var friend = new Person(); 
alert(friend instanceof Object); //true 
alert(friend instanceof Person); //true 
alert(friend.constructor == Person); //false 
alert(friend.constructor == Object); //true 

// 可以单独设置constructor属性
function Person(){ 
} 
Person.prototype = { 
  constructor : Person, 
  name : "Nicholas", 
  age : 29, 
  job: "Software Engineer", 
  sayName : function () { 
  alert(this.name); 
  } 
}; 
```

#### 4. 原型的动态性

```javascript
var friend = new Person(); 
Person.prototype.sayHi = function(){ 
  alert("hi"); 
}; 
friend.sayHi(); //"hi"（没有问题！）

// 这样就会报错
function Person(){ 
} 
var friend = new Person(); 
// 完全重写原型
Person.prototype = { 
  constructor: Person, 
  name : "Nicholas", 
  age : 29, 
  job : "Software Engineer", 
  sayName : function () { 
  	alert(this.name); 
  } 
}; 
friend.sayName(); //error 
```

1.  实例的`[[prototype]]`属性指向原型对象
2.  ==注意==`[[prototype]]`只是一个指针，指向一块内存，而非副本
3.  所以 当完全重写原型的时候，相当于`new Object()`，相当于重新开辟了一块内存，所以原始的`[[prototype]]`指向的内存位置将不再正确。
4.  所以检查原型链的时候，无法查找到`syaName()`这个方法。

#### 5. 原生对象的原型

#### 6. 原型对象的问题

	构造函数不能传递参数
	
	所有的属性都是共享的

### 2.4 组合构造函数和原型模式

```javascript
function Person(name, age, job){ 
  this.name = name; 
  this.age = age; 
  this.job = job; 
  this.friends = ["Shelby", "Court"]; 
} 
Person.prototype = { 
  constructor : Person, 
  sayName : function(){ 
  	alert(this.name); 
  } 
} 
var person1 = new Person("Nicholas", 29, "Software Engineer"var person2 = new Person("Greg", 27, "Doctor"); 
person1.friends.push("Van"); 
alert(person1.friends); //"Shelby,Count,Van" 
alert(person2.friends); //"Shelby,Count" 
alert(person1.friends === person2.friends); //false 
alert(person1.sayName === person2.sayName); //true 
```

### 2.5 动态原型模式

	前面说过构造函数模式有个缺点，实例对象的时候，其中的方法每次都会实例一次，因为函数也是对象。
	
	这里增加了一个判断，检查原型里面有没有这个方法，只需要检查一个就行了，已经有了就不用再实例这个函数了。

```javascript
function Person(name, age, job){ 
  //属性
  this.name = name; 
  this.age = age; 
  this.job = job;
  //方法
  if (typeof this.sayName != "function"){ 
 	Person.prototype.sayName = function(){ 
        alert(this.name); 
	 }; 
   } 
} 
```

### 2.6 寄生构造函数模式

```javascript
function Person(name, age, job){ 
  var o = new Object(); 
  o.name = name; 
  o.age = age; 
  o.job = job; 
  o.sayName = function(){ 
  	alert(this.name); 
  }; 
  return o; 
} 
// 和工厂模式唯一的不同就是 这里使用了new实例对象
// 所以Person函数不再叫包装函数 叫构造函数
var friend = new Person("Nicholas", 29, "Software Engineer"); 
friend.sayName(); //"Nicholas" 
```

构造函数如果不写返回值，默认在`new`之后返回新对象的实例。这里直接返回新对象`o`，重写调用构造函数时的返回值。

```javascript
// 如果现在需要创建一个有特殊方法的数组，但是不能直接修改Array的构造函数
// 
function SpecialArray(){ 
  //创建数组
  var values = new Array(); 
  //添加值
  //push只能接受数组不能接受 arguments类数组
  // [...arguments]也可以
  values.push.apply(values, arguments); 
  //添加方法
  values.toPipedString = function(){ 
  	return this.join("|"); 
  }; 
  //返回数组
  return values; 
} 
// 实例了一个特殊的数组对象
var colors = new SpecialArray("red", "blue", "green"); 
alert(colors.toPipedString()); //"red|blue|green" 
```

寄生构造函数和对象之间没什么关系。

### 2.7 稳妥构造函数模式

	没有公共属性，也没有`this`对象，不使用`new`调用构造函数

```javascript
function Person(name, age, job){ 
 	 //创建要返回的对象
  	var o = new Object(); 
  	//可以在这里定义私有变量和函数
 	//添加方法
 	o.sayName = function(){ 
 		alert(name); 
 	}; 
 	//返回对象
 	return o; 
} 
var friend = Person("Nicholas", 29, "Software Engineer"); 
friend.saName() //"Nicholas" 

// 属性name只能由saName调用
```

## 3.继承

传统OO语言中有两种继承方式，`接口继承`和`实现继承`，接口继承只继承函数签名，一个外壳，而实现继承，继承实际的方法

而`JS`只能通过原型链进行实现继承

### 3.1 原型链

```javascript
function SuperType(){ 
  this.property = true; 
} 
SuperType.prototype.getSuperValue = function(){ 
  return this.property; 
}; 
function SubType(){ 
  this.subproperty = false; 
} 
//继承了 SuperType 
SubType.prototype = new SuperType(); 
SubType.prototype.getSubValue = function (){ 
  return this.subproperty; 
}; 
var instance = new SubType(); 
alert(instance.getSuperValue()); //true 
```

![1537079984563](E:\培训\笔记图\原型链.png)

如上图所示，关键在于第`11`行代码，子类的原型对象，完全等于父类的实例的原型，而实例(子类原型对象)的`[[prototype]]`指向了父类的原型对象，子类的实例中的`[[prototype]]`又指向子类的原型对象。

子类的实例`[[prototype]]`  -----> 子类的原型对象，父类的实例(子类)的原型 -----> 父类的原型对象

`getSuperValue() `方法仍然还在`SuperType.prototype `中，但 `property `则位于 `SubType.prototype `中。这是因为 `property `是一个**实例属性**，而 `getSuperValue()`则是一个**原型方法**。既然 `SubType.prototype `现在是 `SuperType`的实例，那么 `property `当然就位于该实例中了。

此外，要注意 `instance.constructor` 现在指向的是 `SuperType`，这是因为 `instance`的`[[prototype]]`指向子类的原型对象，而原型对象（父类的实例）的`[[prototype]]`指向父类的原型对象，最后在父类的原型对象中找到`constructor`属性，指向父类的构造函数。

#### 1.默认原型

	所有函数都是`Object`的实例，都继承了`Object`。

![1537082733872](E:\培训\笔记图\整个原型链.png)

#### 2. 确定原型和实例的关系

`instanceof`  `isPrototype()`

```javascript
alert(instance instanceof Object); //true 
alert(instance instanceof SuperType); //true 
alert(instance instanceof SubType); //true 
alert(Object.prototype.isPrototypeOf(instance)); //true 
alert(SuperType.prototype.isPrototypeOf(instance)); //true 
alert(SubType.prototype.isPrototypeOf(instance)); //true 
```

#### 3.谨慎定义方法

定义新方法和重写超类方法要在替换原型语句（下方代码第二行）之后

```javascript
//继承了 SuperType 
SubType.prototype = new SuperType(); 
//添加新方法
SubType.prototype.getSubValue = function (){ 
  return this.subproperty; 
}; 
//重写超类型中的方法
// 实际上就是在子类里面写一个和超类名字一样的方法
// 因为原型链查找的关系，在子类里面查找到了就不会再往超类查找了
SubType.prototype.getSuperValue = function (){ 
  return false; 
}; 

//使用字面量添加新方法，会导致替换原型代码无效
// 字面量赋值会导致子类的原型是Object的实例
// 和超类没有关系
SubType.prototype = { 
 getSubValue : function (){ 
 return this.subproperty; 
 }, 
 someOtherMethod : function (){ 
 return false; 
 } 
}; 
```

#### 4.原型链问题

==注意==前面的例子中，都是添加或修改方法，没有怎么出现修改**引用类型**值的例子，因为**引用类型**值会被所有实例共享，一个实例改变了值，也会反映到另外一个实例上。

```javascript
function SuperType(){ 
  this.colors = ["red", "blue", "green"]; 
} 
function SubType(){ 
} 
//继承了 SuperType 
SubType.prototype = new SuperType(); 
var instance1 = new SubType(); 
// 修改引用类型数组的值
instance1.colors.push("black"); 
alert(instance1.colors); //"red,blue,green,black" 
var instance2 = new SubType(); 
alert(instance2.colors); //"red,blue,green,black" 
```

还有一个问题就是，不能在不影响其他实例的情况下，向超类传递参数。

### 3.2 借用构造函数

为了解决**引用类型**值的问题，使用借用构造（伪造对象或经典继承）。

子类构造函数内部调用超类构造函数。

==为什么使用call/apply==，因为每个子类实例都应该有自己的，来自超类的属性`colors`，所以（将来）每次调用超类构造函数，应该是在当前实例的环境下。

```javascript
function SuperType(){ 
  this.colors = ["red", "blue", "green"]; 
} 
function SubType(){ 
  //继承了 SuperType 
  SuperType.call(this); 
} 
var instance1 = new SubType(); 
instance1.colors.push("black"); 
alert(instance1.colors); //"red,blue,green,black" 
var instance2 = new SubType(); 
alert(instance2.colors); //"red,blue,green" 
```

借用构造函数可以向超类传递参数

```javascript
function SuperType(name){ 
  this.name = name; 
} 
function SubType(){ 
  //继承了 SuperType，同时还传递了参数
  SuperType.call(this, "Nicholas"); 
  
  //实例属性
  this.age = 29; 
} 
var instance = new SubType(); 
alert(instance.name); //"Nicholas"; 
alert(instance.age); //29 
```

==缺点==：

所有方法都需要在构造函数中定义。

超类在原型中定义的方法，子类是不可见的。因为实例的是子类对象，只是借用了超类的构造函数，偷偷得到了超类的属性，所以实例的`[[prototype]]`不指向超类而是指向子类的原型对象。

### 3.3 组合继承

将原型链和借用构造函数的技术组合在一块。用原型链继承方法，用借用构造函数继承属性。

```javascript
function SuperType(name){ 
  this.name = name; 
  this.colors = ["red", "blue", "green"]; 
} 
SuperType.prototype.sayName = function(){ 
  alert(this.name); 
}; 
function SubType(name, age){ 
  //继承属性
  SuperType.call(this, name); 
  this.age = age; 
} 
//继承方法
SubType.prototype = new SuperType(); 
SubType.prototype.constructor = SubType; 
SubType.prototype.sayAge = function(){ 
  alert(this.age); 
}; 
var instance1 = new SubType("Nicholas", 29); 
instance1.colors.push("black"); 
alert(instance1.colors); //"red,blue,greeninstance1.sayName(); //"Nicholas"; 
instance1.sayAge(); //29 
var instance2 = new SubType("Greg", 27); 
alert(instance2.colors); //"red,blue,greeninstance2.sayName(); //"Greg"; 
instance2.sayAge(); //27 
```

### 3.4 原型式继承

使用一个模板对象，继承，不需要构造函数

```javascript
 // 在object函数里面定义一个临时的函数
// 这个临时函数的原型被完全重写成模板对象
// 最后返回这个临时函数的实例。
// 本质就是一次浅复制
function object(o){ 
  function F(){} 
  F.prototype = o; 
  return new F(); 
}
// 所有的属性都是共享的 修改一个就会影响其他的
```

`Object.creat()`就是基于此

```javascript
var person = { 
  name: "Nicholas", 
  friends: ["Shelby", "Court", "Van"] 
}; 
var anotherPerson = Object.create(person); 
anotherPerson.name = "Greg"; 
anotherPerson.friends.push("Rob"); 
  
var yetAnotherPerson = Object.create(person); 
yetAnotherPerson.name = "Linda"; 
yetAnotherPerson.friends.push("Barbie"); 
alert(person.friends); //"Shelby,Court,Van,Rob,Barbie" 
// 第二个参数
var anotherPerson = Object.create(person, { 
  name: { 
  	value: "Greg" 
  } 
}) 
```

### 3.5 寄生式继承

```javascript
function createAnother(original){ 
  var clone = object(original); //通过调用函数创建一个新对象
  clone.sayHi = function(){ //以某种方式来增强这个对象
  	alert("hi"); 
  }; 
  return clone; //返回这个对象
} 
```

### 3.6 寄生组合模式

组合继承模式有个缺点，需要调用两次超类构造函数，一次是将超类实例赋值给子类原型的时候，一次是子类构造函数内部调用了超类构造函数。

```javascript
// 1. 复制超类对象
// 2. 修改原型custuctor属性
// 3. 超类原型赋值给子类 
// 这三步相当于完成了组合继承里面的subType.prototype = new superType()，减少了一次超类构造函数调用。
function inheritPrototype(subType, superType){ 
  var prototype = object(superType.prototype); //创建对像
  prototype.constructor = subType; //增强对像
  subType.prototype = prototype; //指定对像
} 
function SuperType(name){ 
  this.name = name; 
  this.colors = ["red", "blue", "green"]; 
} 
SuperType.prototype.sayName = function(){ 
  alert(this.name); 
}; 
function SubType(name, age){ 
  SuperType.call(this, name); 
  
  this.age = age; 
} 
inheritPrototype(SubType, SuperType); 
SubType.prototype.sayAge = function(){ 
  alert(this.age); 
}; 
```

---

# :hammer_and_pick: 第七章   函数表达式

---

```javascript
//不要这样做！ 
//大多数浏览器会返回第二个声明，忽略condition；Firefox 会在 condition 为 true 时返回第一个声明
if(condition){ 
  function sayHi(){ 
  alert("Hi!"); 
  } 
} else { 
  function sayHi(){ 
  alert("Yo!"); 
  } 
} 
//可以这样做
var sayHi; 
if(condition){ 
  sayHi = function() alert("Hi!"); 
  }; 
} else { 
  sayHi = function() alert("Yo!"); 
  }; 
} 
```

## 1. 闭包

有权访问**另一个**函数作用域中的变量的函数

下面代码，`createComparisonFunction`里面返回了一个内部匿名函数，而内部函数使用了外部参数`propertyName`，而且无论这个内部函数被返回了还是在其他地方被调用了，都可以使用这个参数`propertyName`。

其原因，因为这个内部函数的作用域中**包含**了`createComparisonFunction`作用域。

```javascript
function createComparisonFunction(propertyName)  
  return function(object1, object2){ 
      var value1 = object1[propertyName]; 
      var value2 = object2[propertyName]; 
  	  if (value1 < value2){ 
  		return -1; 
  	  } else if (value1 > value2){ 
  		return 1; 
  	  } else { 
  		return 0; 
      } 
   }; 
} 
```

### 1.1 作用域链创建 和  作用域链细节

某个函数被调用时：

1.  创建`执行环境（execution context）`和相应`作用域链`
2.  使用`arguments`和其他`命名参数`的值来初始化函数的`活动对象（activation object）`
3.  在`作用域链`中**外部**函数的活动对象处于第二位，外部的外部函数活动对象处于第三位.....直到全局作用环境

==例1：==

	假如有个函数`compare()`，在全局的环境中调用它，作用域链如下图

![1537319520787](E:\培训\笔记图\函数作用域链1.png)

1.  如上文所说，在调用函数时会创建一个执行环境`compare的执行环境`和一个`作用域链`
2.  其中`作用域链`在**创建的时候**就有包含`全局变量对象`，这个初始对象存在`[[Scope]]`属性中，当**调用函数时**复制`[[Scope]]`里的对象构建起当前执行环境的作用域链。
3.  然后创建一个`活动对象`，被推入作用域链前端。
4.  作用域链实际上就是一个指针列表，不包含任何对象。
5.  访问变量的时候从作用域链搜索，函数执行完毕后局部活动对象就被销毁。

==例2：==

	以上文`createComparisonFunction()`函数为例，一个`内部函数`会将`外部函数`的活动对象添加到它的作用域中，所以`createComparisonFunction()`里的匿名函数将包含`createComparisonFunction()`的活动对象。

![1537323757952](E:\培训\笔记图\闭包作用域链.png)

1.  首先`createComparisonFunction()`创建的作用域链过程和例1一样

2.  里面的匿名函数将包含函数，也就是`createComparisonFunction()`的`活动对象`添加到自己的作用域链里面，位置在自己的活动对象之后，全局对象之前。

3.  当`createComparisonFunction()`返回时，也就是它执行完毕时，因为内部函数，也就是匿名函数的作用域链仍然连接着`createComparisonFunction()`的活动对象，所以`createComparisonFunction()`的活动对象并不会被销毁

    ```javascript
    //创建函数
    var compareNames = createComparisonFunction("name"); 
    //调用函数
    var result = compareNames({ name: "Nicholas" }, { name: "Greg" }); 
    //解除对匿名函数的引用（以便释放内存）
    compareNames = null; 
    ```

### 1.2 闭包与变量

闭包所保存的是`变量对象`而不是某个特定的变量，如下代码

```javascript
function createFunctions(){ 
  var result = new Array(); 
  for (var i=0; i < 10; i++){ 
      result[i] = function(){ 
      return i; }; 
  } 
  return result; 
} 
// 期望输出的是一个数组，位置 0 的函数返回 0，位置 1 的函数返回 1，以此类推
```

事实上并不能达到预期，如上节所描述的，**每个内部函数**，也就是这里的匿名函数的作用域链里面**都包含外部函数**的活动对象，也就是说每个匿名函数都包含着同一个也就是`createFunctions()`的活动对象，或者说是它的**所有变量**，所以当循环完成后，都只能输出`10`

```javascript
// 使用立即执行闭包函数并将当时的变量i传入，就能正确返回
function createFunctions(){ 
    var result = new Array(); 
    for (var i=0; i < 10; i++){ 
        result[i] = function(num){ 
            return num
        }(i);
    } 
    return result; 
} 
```

### 1.3  `this`对象

	`this`是基于函数执行环境绑定的。而匿名函数通常指向`window`，而闭包会有点不同。

```javascript
var name = "The Window"; // 全局变量name
var object = { 
  name : "My Object", // 对象属性name
  getNameFunc : function(){ 
  	return function(){ 
  		return this.name; 
  	}; 
  } 
}; 
alert(object.getNameFunc()()); //"The Window"（在非严格模式下）
```

	之所以输出的是全局变量，

1.  每个函数被调用时，会自动取得两个变量`this`和`arguments`
2.  取得的过程就是按照前几节所叙述的作用域链顺序搜索
3.  按照之前的描述，作用域链最前端是函数自己的活动对象
4.  所以这里的匿名函数最先搜索到的`this`是自己的活动对象，也就是指向全局的`this`

```javascript
var name = "The Window"; 
var object = { 
 name : "My Object", 
 getNameFunc : function(){ 
    var that = this; // 保存外部函数的this
  	return function(){ 
 		 return that.name; // 引用外部函数的this
  	}; 
  } 
}; 
alert(object.getNameFunc()()); //"My Object" 
```

	这里的主要操作时将外部函数的`this`保存，闭包使用了外部函数的`this`
	
	因为内部函数，这里的闭包包含了外部函数的活动对象，所以能访问变量`that`。
	
	`this`值有时会意外的发生改变

```javascript
var name = "The Window"; 
var object = { 
  name : "My Object", 
  getName: function(){ 
      return this.name; 
  } 
}; 
object.getName(); //"My Object" 
(object.getName)(); //"My Object" ()()定义和object.getName()相同
(object.getName = object.getName)(); //"The Window"，在非严格模式下
// 因为赋值是函数本身
```

### 1.4 闭包导致内存泄露

在`IE9`之前对`js对象`和`COM对象`使用不同的垃圾收集例程，所以如果闭包作用域链里面保存着一个`HTML`元素，那么该元素将无法被销毁。

```javascript
function assignHandler(){ 
  var element = document.getElementById("someElement"); 
  element.onclick = function(){ 
      alert(element.id); 
  }; 
} // 因为闭包创建了一个循环引用，匿名函数又包含着assignHandler的活动对象，所以只要匿名函数存在就永远不会被回收
```

```javascript
// 使用第四行代码消除循环引用
// 使用第十一行消除活动对象中对html元素的引用
// 因为闭包包含着外部函数的活动对象
function assignHandler(){ 
  var element = document.getElementById("someElement"); 
  var id = element.id; 
  
  element.onclick = function(){ 
  	alert(id); 
  }; 
  
  element = null; 
} 
```

## 2. 模仿块级作用域

	`JS`中没有块级作用域的概念，如在`for`循环外也可以访问循环变量。可以使用匿名函数来模仿块级作用域。

```javascript
(function(){ 
  //这里是块级作用域
})();

// 相当于
let xx = function() {
    //这里是块级作用域
};
xx();
// 函数调用无非就是函数名跟着一对括号，而函数名也可以直接使用值也就是右侧代替，但是直接使用会出错，所以用括号包裹转化一下
```

```javascript
function outputNumbers(count){ 
      (function () { 
          for (var i=0; i < count; i++){ 
            alert(i); 
          } 
      })(); 
  alert(i); //导致一个错误
} 
```

	这里使用立即执行的匿名函数包裹`for`循环，因为函数执行完成后会销毁自己的`局部活动对象`，所以当这里面的循环结束后，外面无法访问里面的变量，而又以为这个匿名函数是一个闭包，作用域包含了外部函数`outputNumbers`的活动对象，所以能访问变量`count`

## 3. 私有变量

任何在函数中定义的变量，都可以认为是私有变量，因为不能在函数的外部访问这些变量。私有变量包括函数的参数、局部变量和在函数内部定义的其他函数。

把**有权访问**私有变量和私有函数的公有方法称为**特权方法**`privileged method`，有两种方法

```javascript
function MyObject(){ 
  //私有变量和私有函数
  var privateVariable = 10; 
  function privateFunction(){ 
 	 return false; 
  } 
  //特权方法
  this.publicMethod = function (){ 
  	privateVariable++; 
 	 return privateFunction(); 
  }; 
} 
//变量 privateVariable 和函数 privateFunction()只能通过特权方法 publicMethod()来访问

function Person(name){ 
  this.getName = function(){ 
  	return name; 
  }; 
  this.setName = function (value) { 
  	name = value; 
  }; 
} 
var person = new Person("Nicholas"); 
alert(person.getName()); //"Nicholas" 
person.setName("Greg"); 
alert(person.getName()); //"Greg" 
```

==缺点==：

像上面代码一样使用构造函数来定义特权方法，有个缺点，就是每次实例化的时候都会创建一组新的方法，因为函数也是对象。

### 3.1 静态私有变量

	为了解决构造函数定义特权方法的问题。在`私有作用域`中定义私有函数和变量

```javascript
(function(){ 
  //私有变量和私有函数
  var privateVariable = 10; 
  function privateFunction(){ 
  	return false; 
  } 
  //构造函数
  MyObject = function(){ 
  }; 
  //公有/特权方法
  MyObject.prototype.publicMethod = function(){ 
  	privateVariable++; 
  	return privateFunction(); 
  }; 
})(); 
```

这个模式中：

1.  创建了一个私有作用域，然后定义了私有属性。
2.  第八行定义一个构造函数，并使用原型模式定义了特权方法，注意这里的构造函数没有使用`var`，目的是让它成为全局变量，方便外部访问私有属性。
3.  有一个实例改变了属性，所有实例都会体现

### 3.2  模块模式

	主要是针对单例创建私有变量和特权方法，单例就是只有一个实例的对象。

```javascript
// 典型的单例 字面量创建的对象
var singleton = { 
 name : value, 
 method : function () { 
 	//这里是方法的代码
 	} 
}; 
// 模块模式
var singleton = function(){  
  //私有变量和私有函数
  var privateVariable = 10; 
  
  function privateFunction(){ 
 	 return false; 
  } 
 //特权/公有方法和属性
// 返回的就是一个单例
 return { 
 	publicProperty: true, 
 	publicMethod : function(){ 
 		privateVariable++; 
 		return privateFunction(); 
 	} 
 }; 
}(); 
```

上面的代码，创建了一个匿名函数内部有私有变量和函数，然后返回一个字面量对象，包含公开的变量和方法。

这种方法对于，需要对单例进行某些初始化，同时又需要维护其私有变量时非常有用。

```javascript
// 使用例子
var application = function(){ 
  //私有变量和函数
  var components = new Array(); 
  //初始化
  components.push(new BaseComponent()); 
  //公共
  return { 
  	getComponentCount : function(){ 
  		return components.length; 
  	}, 
  	registerComponent : function(component){ 
  		if (typeof component == "object"){ 
  			components.push(component); 
 	 	} 
  	} 
  }; 
}(); 
```

如果需要对一个对象进行初始化同时需要一些公开且能够访问这些私有数据的方法，就可以使用模块模式。

### 3.3 增强模块模式

单例必须是某种类型的实例，同时还必须添加某些属性和（或）方法对其加以增强的情况

```javascript
var application = function(){ 
  //私有变量和函数
  var components = new Array(); 
  //初始化
  components.push(new BaseComponent()); 
  //创建 application 的一个局部副本
  var app = new BaseComponent(); 
  //公共接口
  app.getComponentCount = function(){ 
  	return components.length; 
  }; 
  app.registerComponent = function(component){ 
  	if (typeof component == "object"){ 
  		components.push(component); 
  	} 
  }; 
  //返回这个副本
     return app; 
}(); 
```

---

# :bridge_at_night:第八章  BOM

---

## 1. `window`对象

`window`对象有双重身份，既是访问浏览器的一个接口，也是所规定的的`Global`对象。

### 1.1 全局作用域

	在全局作用域中声明的变量函数都会变成`window`对象的属性和方法（仅限于`var`声明的变量，`let`不行）。
	
	全局变量不能通过`delete`删除，直接在`window`上定义的可以，主要原因是使用`var`创建的全局变量被设置了`[[configurable]]`为`false`，也就是不能被`delete`删除。

### 1.2 窗口关系及框架

	如果页面有框架`frame`，那么每个框架都有自己的`window`对象。保存在`window.frames`集合中，可以通过索引值或者框架名来访问对应的`window`。

```html
<html> 
  <head> 
  	<title>Frameset Example</title> 
  </head> 
  <frameset rows="160,*"> 
  	<frame src="frame.htm" name="topFrame"> 
  	<frameset cols="50%,50%"> 
  		<frame src="anotherframe.htm" name="leftFrame"> 
  		<frame src="yetanotherframe.htm" name="rightFrame"> 
  	</frameset> 
  </frameset> 
</html> 
```

	这里创建了一个框架居上，两个框架局下，可以通过`window.frames[0]`或`window.frames["topFrame"]`来引用上方框架。建议使用`top`而非`window`来引用框架，`top`永远指向最外层框架，也就是浏览器窗口。
	
	因为如果你想在一个框架里面引用另外一个框架，使用`window`是无法实现的，因为每个框架里的`window`都是特指当前的框架，无法访问另一个框架。

![1537407495892](E:\培训\笔记图\访问框架.png)

	与`top`相对的一个`parent`，当前框架的上一层。有可能`top`等于`parent`。
	
	最高层窗口的`window`对象默认是没有`name`属性的，除非是使用`window.open()`打开的。
	
	`self`对象指向`window`，实际上就是相同的

### 1.3 窗口位置

	表示窗口离电脑屏幕左边和上面的距离，火狐只能支持`screenX/screenY`，谷歌和`safari`除了能够支持这个还支持`screenLeft/screenTop`。

```javascript
var leftPos = (typeof window.screenLeft == "number") ? 
  	window.screenLeft : window.screenX; 
var topPos = (typeof window.screenTop == "number") ? 
  	window.screenTop : window.screenY; 
```

	在`IE、Opera`中`screenLeft /screenTop`表示页面**可见区域**到屏幕边缘的距离，也就是说还得加上如工具栏之类的宽度高度。跨浏览器获得精确位置是不可能的。

### 1.4 窗口大小

主流浏览器都有`innerWidth / innerHeight / outerWidth / outerHeight`属性，

**`outerWidth\outerHeight`：**

-   `IE9+\Safari\Firefox`中`outerWidth\outerHeight`返回浏览器窗口本身的尺寸
-   `Opera`则返回单个标签的对应的浏览器窗口尺寸

**`innerWidth\innerHeight`：**

-   返回页面试图去大小，除去浏览器边框、工具栏之类的。
-   谷歌浏览器返回的大小和`outer`一样

**`clientWidth\clientHeight`：**

-   页面视口信息

### 1.5 导航和打开窗口

	`window.open()`方法可以导航到特定的`URL`或者打开一个新的浏览器窗口。这个方法可以接受4个参数要加载的 URL、窗口目标、一个特性字符串以及一个表示新页面是否取代浏览器历史记录中当前加载页面的布尔值。

```javascript
//等同于< a href="http://www.wrox.com" target="topFrame"></a> 
window.open("http://www.wrox.com/", "topFrame"); 
```

如果有一个名叫`topFrame`的窗口或者框架，就会在该窗口或框架加载这个 `URL`，否则，就会创建一个新窗口并将其命名为`topFrame`。此外，第二个参数也可以是下列任何一个特殊的窗口名称：`_self`、`_parent`、`_top` 或`_blank`

#### 1.5.1 弹出窗口

-   如果无法找到第二个参数所描述的窗口或者框架，那么浏览器会以第三个参数的字符串为配置，创建一个新窗口或者标签。

-   如果没有第三个参数，就创建一个默认设置的窗口。

-   不创建新的窗口就忽略第三个参数
-   第三个参数按照逗号分隔的字符串，就像标签上的属性

![1537414815954](E:\培训\笔记图\window.open第三参数.png)

```javascript
window.open("http://www.wrox.com/","wroxWindow", 
  "height=400,width=400,top=10,left=10,resizable=yes"); 
```

`window.open()`返回一个指向新窗口的引用，大部分和`window`相同，但可以对创建的新窗口进行大小或者位置的调整。

```javascript
var wroxWin = window.open("http://www.wrox.com/","wroxWindow", 
  "height=400,width=400,top=10,left=10,resizable=yes"); 
//调整大小
wroxWin.resizeTo(500,500); 
//移动位置
wroxWin.moveTo(100,100); 
```

使用`.close()`可以关闭由`open()`打开的窗口，关闭后的窗口还能使用`.closed`检测是否关闭，没有其他的作用了。

新创建的 `window `对象有一个 `opener `属性，其中保存着打开它的原始窗口对象。这个属性只在弹出窗口中的最外层 `window `对象`（top）`中有定义，而且指向调用` window.open()`的窗口或框架

```javascript
7var wroxWin = window.open("http://www.wrox.com/","wroxWindow", 
  "height=400,width=400,top=10,left=10,resizable=yes"); 
alert(wroxWin.opener == window); //true 
```

原始窗口并没有指向新窗口的引用，在谷歌浏览器中将`opener`设置为`null`表示独立进程中运行此标签

```javascript
var wroxWin = window.open("http://www.wrox.com/","wroxWindow", 
  "height=400,width=400,top=10,left=10,resizable=yes"); 
wroxWin.opener = null; 
```

#### 1.5.2 安全限制

	各种浏览器对弹窗有着不同的限制

#### 1.5.3 弹窗屏蔽程序

	对于浏览器有屏蔽功能的，可以使用如下代码检测是否被屏蔽

```javascript
var wroxWin = window.open("http://www.wrox.com", "_blank"); 
if (wroxWin == null){ 
  alert("The popup was blocked!"); 
} 

var blocked = false; 
 // 有可能会抛出一个错误 需要使用try-catch接收
try { 
  var wroxWin = window.open("http://www.wrox.com", "_blank"); 
  if (wroxWin == null){ 
  	blocked = true; 
  } 
} catch (ex){ 
  blocked = true; 
} 
if (blocked){ 
  alert("The popup was blocked!"); 
} 
```

### 1.6 间歇调用和超时执行

`JavaScript `是单线程语言，但它允许通过设置**超时值**和**间歇时间值**来调度代码在特定的时刻执行

**超时调用**：调用`setTimeout()`，第一个参数是执行函数，第二的参数是等待多少毫秒后执行，<u>但是经过指定时间后代码不一定会执行</u>，因为`js`是单线程的，所以实际上`setTimeout()`只是在设置的毫秒数之后将任务添加在**任务队列**里，如果添加的时候是空的那么会立即执行，相反，则等待前面的任务执行完成后执行。

```javascript
// 不建议传递字符串！
// 会造成性能损失
setTimeout("alert('Hello world!') ", 1000); 
//推荐的调用方式
setTimeout(function() { 
  alert("Hello world!"); 
}, 1000); 
```

`setTimeout`返回一个`ID`，使用`clearTimeout()`清除定时器，实际上当`setTimeout`执行完毕，就自定清除了。

```javascript
//设置超时调用
var timeoutId = setTimeout(function() { 
  alert("Hello world!"); 
}, 1000); 
//注意：把它取消
clearTimeout(timeoutId); 
```

`setTimeout`的代码都是在全局作用域中执行的，`this`指向`window`。

**间歇调用**：`setInterval()`，使用`clearInterval()`清除定时器，一般使用超时调用来模拟间歇调用，防止启动多个间歇调用。

```javascript
//不建议传递字符串！
setInterval ("alert('Hello world!') ", 10000); 
//推荐的调用方式
setInterval (function() { 
  alert("Hello world!"); 
}, 10000); 
```

### 1.7  系统对话框

	如调用`alert()` `confirm()` `prompt()`方法可以调用一些系统对话框。显示这些对话框代码会停止执行，而关掉又会恢复执行。

## 2. `location`对象

提供了加载的文档的有关信息，还提供了一些导航功能。

`window.location`和`document.location`是同一个对象。

![1537426336453](E:\培训\笔记图\location对象属性.png)

### 2.1 查询字符串参数

	虽然通过`location.search`可以返回所有的从问号到末尾的内容，但是没办法获得查询参数的每一项

```javascript
// 获取查询参数的每一项
function getQueryStringArgs(){ 
  //取得查询字符串并去掉开头的问号
  var qs = (location.search.length > 0 ? location.search.substring(1) : ""), 

  //保存数据的对象
  args = {}, 
  
  //取得每一项
  items = qs.length ? qs.split("&") : [], 
  item = null, 
  name = null, 
  value = null, 
  //在 for 循环中使用
  i = 0, 
  len = items.length; 
  //逐个将每一项添加到 args 对象中
  for (i=0; i < len; i++){ 
      item = items[i].split("="); 
      name = decodeURIComponent(item[0]); 
      value = decodeURIComponent(item[1]); 
      if (name.length) { 
        args[name] = value; 
      } 
  } 
  
  return args; 
} 
```

### 2.2 位置操作

`assign()`方法传递`URL`

```javascript
// 立即打开新的URL 生成一条浏览记录
location.assign("http://www.wrox.com"); 
// 调用如下方法 也会调用assign
window.location = "http://www.wrox.com"; 
location.href = "http://www.wrox.com"; 
```

最常用的是`href`属性，其他属性的修改也能改变当前的页面

```javascript
//假设初始 URL 为 http://www.wrox.com/WileyCDA/ 
//将 URL 修改为"http://www.wrox.com/WileyCDA/#section1" 
location.hash = "#section1"; 
//将 URL 修改为"http://www.wrox.com/WileyCDA/?q=javascript" 
location.search = "?q=javascript"; 
//将 URL 修改为"http://www.yahoo.com/WileyCDA/" 
location.hostname = "www.yahoo.com"; 
//将 URL 修改为"http://www.yahoo.com/mydir/" 
location.pathname = "mydir"; 
//将 URL 修改为"http://www.yahoo.com:8080/WileyCDA/" 
location.port = 8080; 
```

每次修改 location 的属性（hash 除外）页面都会以新 URL 重新加载

以上任何一种方法都会在浏览器历史中产生记录，可以使用`replace()`方法避免增加历史记录。

`reload`，在这个方法之后写的代码有可能不执行，因为如果网络不好会一直阻塞

```javascript
location.reload(); //重新加载（有可能从缓存中加载）
location.reload(true); //重新加载（从服务器重新加载）
```

## 3. `navigator`对象

#### 3.1 检测插件

使用`plugins`数组来检测，数组的每一项都包含下列属性

-   `name`：插件名字
-   `description`：插件描述
-   `filename`：插件的文件名
-   `length`：查检所处理的`MIME`类型数量

```javascript
//检测插件（在 IE 中无效）
function hasPlugin(name){ 
  name = name.toLowerCase(); 
  for (var i=0; i < navigator.plugins.length; i++){ 
  	if (navigator. plugins [i].name.toLowerCase().indexOf(name) > -1){ 
  	return true; 
  	} 
  } 
  return false; 
} 
//检测 Flash 
alert(hasPlugin("Flash")); 
//检测 QuickTime 
alert(hasPlugin("QuickTime")); 
```

## 4. `screen`对象

![1537432395707](E:\培训\笔记图\screen对象属性)

![1537432412361](E:\培训\笔记图\screen属性2)

## 5. `history`对象

每个浏览器窗口、每个标签、每个框架都有自己的`history`对象与特定的`window`对象相连。开发者无法得知用户浏览过的`URL`，但是仍然可以控制前进后退

```javascript
//后退一页
history.go(-1); 
//前进一页
history.go(1); 
//前进两页
history.go(2); 
//跳转到最近的 wrox.com 页面
history.go("wrox.com"); 
//跳转到最近的 nczonline.net 页面
history.go("nczonline.net"); 

//另外，还可以使用两个简写方法 back()和 forward()来代替 go()
//后退一页
history.back(); 
//前进一页
history.forward(); 
// 历史记录条数
if (history.length == 0){ 
 //这应该是用户打开窗口后的第一个页面
} 
```

---

# :vibration_mode:第九章  客户端检测

---

不到万不得已，就不要使用客户端检测。只要能找到更通用的方法，就应该优先采用更通用的方法。一言以蔽之，先设计最通用的方案，然后再使用特定于浏览器的技术增强该方案

## 1. 能力检测

	又称`特性检测`，不用确定是何种浏览器，只测试是否拥有此项能力

```javascript
if (object.propertyInQuestion){ 
  //使用 object.propertyInQuestion 
    // 若没有此功能 object.propertyInQuestion == undefined
    // 则判断为false
} 
```

使用能力检测主要注意两个方面

-   先检测最常用的特性
-   一个特性存在不证明另外一个也存在

```javascript
function getWindowWidth(){ 
  if (document.all){ //假设是 IE
  return document.documentElement.clientWidth; //错误的用法！！！
  } else { 
  return window.innerWidth; 
  } 
} 
// 是 document.all 存在也不一定表示浏览器就是 IE
// Opera 支持 document.all
```

### 1.1 更可靠的能力检测

单单检测一个对象成员是否存在是会有小问题的，如果这个成员是函数而不是属性就很难区别。

```javascript
//不要这样做！这不是能力检测——只检测了是否存在相应的方法
function isSortable(object){ 
  return !!object.sort; // 比较运算符会自动类型转换为布尔
} 						// 除了undefined/null 0 空字符串 NaN都是true
// 所以这里相当于 取反取反true
```

更好的方法是直接检测`sort`是不是一个函数

```javascript
//这样更好：检查 sort 是不是函数
function isSortable(object){ 
  return typeof object.sort == "function"; 
} 
```

### 1.2 怪异检测`quirks detection`

与能力检测相反，这里是测试浏览器某个功能没有。

```javascript
var hasDontEnumQuirk = function(){ 
	var o = { toString : function(){} }; 
    for (var prop in o){ 
        if (prop == "toString"){ 
            return false; // 正常浏览器应该执行到此
        } 
    } 
    return true; // 怪异模式执行此
}(); 
```

### 1.3 用户代理检测

通过检测**用户代理字符串**来确定使用的浏览器。

每次`HTTP`请求中，用户代理字符串是作为响应头部发送的，可以通过`navigator.userAgent`属性访问。用户代理检测万不得已才使用。因为很容易被**电子欺骗**利用，通过修改用户代理字符串欺骗服务器。

#### 1.3.1 用户代理字符串历史

`HTTP`规范规定，浏览器应该在用户代理字符串中指明浏览器名称和版本号。

-   第一款浏览器`Mosaic`的用户代理字符串`Mosaic/0.9`

-   `Netscape `的`Mozilla`

    用户代理字符串`Mozilla/版本号 [语言] (平台; 加密类型) `

-   `Netscape Navigator 3`和`Internet Explorer 3 `

    用户代理字符串`Mozilla/版本号 (平台; 加密类型 [; 操作系统或 CPU 说明]) `。

    由于`IE3`才出来，需要兼容` Netscape `所以将用户代理字符串改成

    `Mozilla/2.0 (compatible; MSIE 版本号; 操作系统) `

-   `Netscape Communicator 4 `和` IE4～IE8 `省略

总之不断变复杂的用户代理字符串，除了满足一些功能识别外，还为了兼顾之前的字符串格式。

-   `Gecko`呈现引擎出现后

    `Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.8.1.11) Gecko/20071127 Firefox/2.0.0.11 `

    `Mozilla`版本自从`Gecko`出现后就一直没有改变过

-   `Firefox 4`发布后简化 `windows\Gecko`都是固定的了

    `Mozilla/5.0 (Windows NT 6.1; rv:2.0.1) Gecko/20100101 Firefox 4.0.1`

-   `WebKit`出现

    是`KHTML`的一个分支，同样为了兼容伪装了用户代理字符串

    `Mozilla/5.0 (Macintosh; U; PPC Mac OS X; en) AppleWebKit/124 (KHTML, like Gecko) 
      Safari/125.1 `

#### 1.3.2 用户代理字符串检测技术

##### 1. 识别呈现引擎

主要识别`IE、Gecko、WebKit、KHTML、Opera。`

```javascript
// 使用模块增强模式
var client = function(){  
    var engine = { 
        //呈现引擎
        ie: 0, 
        gecko: 0, 
        webkit: 0, 
        khtml: 0, 
        opera: 0, 
        //具体的版本号
        ver: null 
    }; 
  //在此检测呈现引擎、平台和设备
    return { 
        engine : engine 
    }; 
}(); 
// 使用
if (client.engine.ie) { //如果是 IE，client.ie 的值应该大于 0 
  //针对 IE 的代码
} else if (client.engine.gecko > 1.5){ 
  if (client.engine.ver == "1.8.1"){ 
  //针对这个版本执行某些操作
  } 
}

```

检测顺序很重要

```javascript
// 第一步显示别Opera 主要是window.opera对象
if (window.opera){ 
  engine.ver = window.opera.version(); 
  engine.opera = parseFloat(engine.ver); 
} 
// 第二位检测WebKit 检测AppleWebKit字符串
var ua = navigator.userAgent; 
if (window.opera){ 
  engine.ver = window.opera.version(); 
  engine.opera = parseFloat(engine.ver); 
} else if (/AppleWebKit\/(\S+)/.test(ua)){ 
  engine.ver = RegExp["$1"]; 
  engine.webkit = parseFloat(engine.ver); 
} 
// 完整的
var ua = navigator.userAgent; 
if (window.opera){ 
  engine.ver = window.opera.version(); 
  engine.opera = parseFloat(engine.ver); 
} else if (/AppleWebKit\/(\S+)/.test(ua)){ 
  engine.ver = RegExp["$1"]; 
  engine.webkit = parseFloat(engine.ver); 
} else if (/KHTML\/(\S+)/.test(ua)) { 
  engine.ver = RegExp["$1"]; 
  engine.khtml = parseFloat(engine.ver); 
} else if (/rv:([^\)]+)\) Gecko\/\d{8}/.test(ua)){ 
  engine.ver = RegExp["$1"]; 
  engine.gecko = parseFloat(engine.ver); 
} else if (/MSIE ([^;]+)/.test(ua)){ 
  engine.ver = RegExp["$1"]; 
  engine.ie = parseFloat(engine.ver); 
```

##### 2. 识别浏览器

主要也是识别诸如`Chrome`之类的关键词

##### 3. 识别平台

使用`navigator.platform `

```javascript
var p = navigator.platform; 
system.win = p.indexOf("Win") == 0; 
system.mac = p.indexOf("Mac") == 0; 
system.x11 = (p.indexOf("X11") == 0) || (p.indexOf("Linux") == 0); 
```

##### 4. 识别移动设备

```javascript
//检测 iOS 版本
if (system.mac && ua.indexOf("Mobile") > -1){ 
  if (/CPU (?:iPhone )?OS (\d+_\d+)/.test(ua)){ 
  system.ios = parseFloat(RegExp.$1.replace("_", ".")); 
  } else { 
  system.ios = 2; //不能真正检测出来，所以只能猜测
  } 
} 
//检测 Android 版本
if (/Android (\d+\.\d+)/.test(ua)){ 
  system.android = parseFloat(RegExp.$1); 
} 
```

##### 5. 识别游戏设备

```javascript
system.wii = ua.indexOf("Wii") > -1; 
system.ps = /playstation/i.test(ua); 
```



---

# :building_construction:第十章  DOM

---

`DOM`是针对`HTML`和`XML`的一个`API`。尤其注意`IE`中的`DOM`对象与原生`JS`对象的行为有诸多不同。

## 1. 节点层次

	文档节点是每个文档的根节点，文档节点只有唯一个节点`<html>`，称之为文档元素。总共有12种节点类型，比如`注释节点` `特性节点` `文档节点`....。
	
	元素`element`和节点`node`的区别就是，节点还能包含文字之类的。

### 1 `Node`类型

-   除了`IE`之外，其他所有浏览器都能访问到这个类型
-   `JS`中所有的节点类型都继承自`Node`类型

每个节点都有一个`nodeType`，节点类型由一个常数表示

>   Node.ELEMENT_NODE(1)；
>   Node.ATTRIBUTE_NODE(2)；
>   Node.TEXT_NODE(3)；
>   Node.CDATA_SECTION_NODE(4)；
>   Node.ENTITY_REFERENCE_NODE(5)；
>   Node.ENTITY_NODE(6)；
>   Node.PROCESSING_INSTRUCTION_NODE(7)；
>   Node.COMMENT_NODE(8)
>   Node.DOCUMENT_NODE(9)；
>   Node.DOCUMENT_TYPE_NODE(10)；
>   Node.DOCUMENT_FRAGMENT_NODE(11)；
>   Node.NOTATION_NODE(12)。

```javascript
if (someNode.nodeType == 1){ //适用于所有浏览器
  alert("Node is an element."); 
} 
```

#### 1.1 `nodeName`和`nodeValue`属性

```javascript
if (someNode.nodeType == 1){ 
  value = someNode.nodeName; //nodeName 的值是元素的标签名
} 
```

#### 1.2 节点关系

	每个节点之间都有各种关系，如`父子`关系。每个节点都有`childNodes`属性，保存着一个类数组对象`NodeList`，虽然有`length`属性也可以用方括号访问元素，但是并没有`Array`的方法。
	
	它是`DOM`动态查询的结果，不是在我们第一次访问它们的某个瞬间拍摄下来的一张快照。

```javascript
var firstChild = someNode.childNodes[0]; 
var secondChild = someNode.childNodes.item(1); 
var count = someNode.childNodes.length; 
// 注意访问长度只是 当时  的节点长度
```

```javascript
// 转化为数组
function convertToArray(nodes){ 
    var array = null; 
    try { 
        array = Array.prototype.slice.call(nodes, 0); //针对非 IE 浏览器
    } catch (ex) { 
        array = new Array(); 
        for (var i=0, len=nodes.length; i < len; i++){ 
            array.push(nodes[i]); 
        } 
    } 
    return array; 
} 
```

在`childNode`列表里的所有节点都有相同的父节点，而列表里的所有元素右互相为同胞节点，通过`previousSibling`和`nextSibling`访问前后兄弟节点。

==注意==：

	这两个属性返回的是**节点**，如下代码，`id`为`AA`的的`div`的下一个兄弟并不是`BB`，而是**text节点**`aaa`

```html
    <div id="AA">aaa
        <div id="BB">bbb</div>
    </div>
```

`firstChild`和`lastChild`返回父节点的第一个和最后一个子节点。

返回文档顶端，使用`ownerDocument`属性

#### 1.3 操作节点

-   `appendChild()`向末尾添加一个节点
-   `insertBefore(new, ref)`在参考节点之前插入新节点

-   `replaceChild(new, old)`替换节点
-   `removeChild()`移除节点
-   无论替换还是移除，实际上还存在在文档中，只不过没有了他们的位置
-   上面的方法都需要知道父节点才能操作

其他方法

-   `cloneNode()`接收一个布尔参数，是否深复制：复制整个子节点树，浅复制只复制节点本身。返回的副本并没有父元素，所以需要制定父元素加入到其中。不会复制`JS`代码，如事件处理函数
-   `normalize()`处理文本节点，删除重复的文本节点

### 2 `Document`类型

就是经常使用的`document`对象

`document`是`HTMLDocument`的一个实例，而`HTMLDocument`又继承自`Document`,`document`特点

-   `nodeType`：`9`
-   `nodeName`：`‘#document’`
-   `nodeValue`：`null`
-   `parentNode`：`null`
-   `ownerDocument`：`null`
-   子节点只能是：`DocumentType`、`Element`、`ProcessingInstruction` `Comment`

```javascript
var html = document.documentElement; //取得对<html>的引用
alert(html === document.childNodes[0]); //true 
alert(html === document.firstChild); //true 

var body = document.body; //取得对<body>的引用

var doctype = document.doctype; //取得对<!DOCTYPE>的引用
```

#### 1. 文档信息

取得网页标题

```javascript
//取得文档标题
var originalTitle = document.title; 
//设置文档标题
document.title = "New page title"; 
```

获取网页请求相关

`URL `与 `domain `属性是相互关联的。例如，如果 `document.URL` 等于 `http://www.wrox.com/WileyCDA/`，那么 `document.domain `就等于` www.wrox.com`

```javascript
//取得完整的 URL
var url = document.URL; 
//取得域名
var domain = document.domain; 
//取得来源页面的 URL
var referrer = document.referrer; 

// 只有domain能被设置 但是只能设置同域的
//假设页面来自 p2p.wrox.com 域
document.domain = "wrox.com"; // 成功
document.domain = "nczonline.net"; // 出错！
```

在将 `document.domain `设置为`"wrox.com"`之后，就不能再将其设置回`"p2p.wrox.com"`，否则将会导致错误

```javascript
//假设页面来自于 p2p.wrox.com 域
document.domain = "wrox.com"; //松散的（成功）
document.domain = "p2p.wrox.com"; //紧绷的（出错！）
```

#### 2. 查找元素

-   `getElementById()`

    多个相同`ID`返回第一个

-   `getElementsByTagName()`

    返回的是`NodeList`，在`HTML`中返回的是一个`HTMLCollection`对象，也是一个类数组对象。`HTMLCollection`有一个特有方法`nameItem()`

    ```javascript
    <img src="myimage.gif" name="myImage"> 
    // 那么就可以通过如下方式从 images 变量中取得这个<img>元素：
    var myImage = images.namedItem("myImage"); 
    var myImage = images["myImage"]; 
    // 返回全部
    var allElements = document.getElementsByTagName("*"); 
    ```

-   `getElementsByName()`

    也是返回一个`HTMLCollection`

#### 3. 特殊集合

	都是`HTMLCollection`对象

-   `document.anchors`，包含文档中所有带 `name` 特性的`<a>`元素；
-   `document.forms`，包含文档中所有的`<form>`元素
-   `document.images`，包含文档中所有的`<img>`元素，
-   `document.links`，包含文档中所有带` href `特性的`<a>`元素

#### 4. 文档写入

	`write()`、`writeln()加个换行`、`open()`和 `close()`

写入`</script>`会被当做与前面匹配的标签，后面的代码不会执行

文档加载完成再写入，会覆盖整个文档

### 3. `Element`类型

-   `nodeType `：1
-   `nodeName `：大写元素名如`DIV`
-   `nodeValue `： null
-   `parentNode `： `Document` 或 `Element`
-   其子节点可能是 `Element`、`Text`、`Comment`、`ProcessingInstruction`、`CDATASection`、`EntityReference`

获取标签名字时，常使用`tagName`两者返回的值是一样的，主要是为了清晰。

```javascript
// 虽然在HTML文档里面，标签名是大写
// 但是在其他如XML文档里面则不一定
// 所以转化为小写进行比较
if (element.tagName.toLowerCase() == "div"){ //这样最好（适用于任何文档）
  //在此执行某些操作
} 
```

#### 1. `HTML`元素

	所有`HTML`元素都由`HTMLElement`类型表示，就算不是直接通过这个类型，也是通过它的子类来表示。所有元素都有下列特性HTMLElement继承自`Element`

-   `id`：唯一标识符
-   `title`：鼠标悬停的提示
-   `lang`：元素内容的语言代码，很少使用
-   `dir`：语言的方向，值为"ltr"（left-to-right，从左至右）或"rtl"（right-to-left，从右至左），也很少使用
-   `className`：CSS类

这些属性不仅可以读取，也能赋值

```javascript
div.id = "someOtherId"; 
div.className = "ft"; 
div.title = "Some other text"; 
div.lang = "fr"; 
div.dir ="rtl"; 
```

每二个标签基本上都是由一个更具体的，继承于`HTMLElement`的对象来表示的，如`div`是`HTMLDivElement`类型的，`body`是`HTMLBodyElement`类型的

#### 2. 取得属性

	`getAttribute() setAttribute() removeAttribute()`

如果没有找到对应的属性，返回`null`，属性名是不区分大小写的，自定义属性应该在前面加上`data-`。

也能直接访问标签属性如，`div.style`

==特别的==

`div.style`返回一个`css`对象，`div.getAttribute()`返回一个字符串，这个字符串就是`css`对象里面的`cssText`属性。

`div.onclick`返回的是一个函数，而`getAttribute()`返回的是一个代码字符串。

#### 3. 设置属性

`setAttribute()`，会覆盖相同属性名的值。自动将大写转换为小写。

`removeAttribute()`，删除属性

#### 4. `attributes`属性

`div.attributes >>>> NamedNodeMap[]`

`Element`类型是唯一一个拥有`attributes`属性的节点类型。`attributes`属性中包含一个`NamedNodeMap`，也是一个动态的集合，每一项都是一个`Attr`节点（继承自`Node`），每个节点都包含在`NamedNodeMap`中。

-   `getNamedItem(name)`：返回对应`name`的属性
-   `removeNamedItem(name)`：移除属性，区别就是会返回被删除的`Attr`节点
-   `setNamedItem(node)`：添加属性节点（需要先.createAttribute），设置值还需要下面的代码
-   `item(pos)`：返回`pos`处的属性

也可以通过`div.attributes['name']`访问，也可以使用`nodeValue`设置属性值

```javascript
element.attributes["id"].nodeValue = "someOtherId"; 
```

基本上很少用，但是需要遍历元素的所有属性则可以使用。

#### 5. 创建元素

`document.createElement()`

#### 6. 元素子节点

在`IE`中会忽略父元素的文本子节点

```javascript
for (var i=0, len=element.childNodes.length; i < len; i++){ 
  if (element.childNodes[i].nodeType == 1){ 
  //执行某些操作
  } 
} 
// 通过判断nodeType来区分是否是元素节点
```

### 4. `Text`类型

没有包含`html`代码的文字部分，转义之后的`html`字符也算文本。

- `nodeType `： 3
- `nodeName` ："#text"
- `nodeValue` ：节点所包含的文本
- `parentNode` ：某个Element
- 不支持（没有）子节点
- 可以通过`nodeValue`或者`data`属性访问

可以使用下面的方法（`Text`类型的节点才有）操作节点中的文本

- `appendData(text)`：将 `tex`t 添加到节点的末尾。
- `deleteData(offset, count)`：从 `offset` 指定的位置开始删除 `count` 个字符。
- `insertData(offset, text)`：在 `offset` 指定的位置插入 `text`。
- `replaceData(offset, count, text)`：用 `text` 替换从 `offset` 指定的位置开始到 `offset+count` 为止处的文本。
- `splitText(offset)`：从 `offset` 指定的位置将当前文本节点分成两个文本节点。
- `substringData(offset, count)`：提取从 `offset` 指定的位置开始到 `offset+count` 为止处的字符串
- 通过`length`访问文本长度

#### 1. 创建文本节点

`document.createTextNode()`

添加的字符串在网页中是被转义后的，也就是说就算输入的`<div>`也只是一个字符串而不是实际的标签。

```javascript
var textNode = document.createTextNode("<strong>Hello</strong> world!"); 
// 同样必须加入文档中
document.body.appendChild(textNode)
// 加入多个文本节点 如果是两个兄弟节点就会连起来
```

#### 2. 归一化（规范化）文本节点

出现兄弟文本节点会让人分不清哪个节点是什么字符串，归一化文本节点就是将多个相邻文本合并的方法，在所有类型的节点中都有的方法`normalize()`

#### 3. 分割文本节点

`splitText()`按照指定的位置把文本分割成两个文本节点。

### 5. `Comment`类型

注释通过`Comment`类型实现的

- `nodeType` ： 8
- `nodeName` ：`"#comment"`
- `nodeValue` ：注释的内容
- `parentNode` ： `Document` 或 `Element`
- 不支持（没有）子节点

因为`Comment`和`Text`继承自相同的类，所以拥有除了`splitText()`之外的所有字符串操作方法

### 6. ` CDATASection`类型

只会出现在`XML`文档中，和注释一样的，` nodeType = 4`

### 7. ` DocumentType`类型

就是`<!DOCTYPE>`标签，` nodeType = 10`

### 8. ` DocumentFragment`类型

可以看做是一个仓库，只有在需要是时候在将其整体添加到文档中

```javascript
var fragment = document.createDocumentFragment(); 
var ul = document.getElementById("myList"); 
var li = null; 
for (var i=0; i < 3; i++){ 
    li = document.createElement("li"); 
    li.appendChild(document.createTextNode("Item " + (i+1))); 
    fragment.appendChild(li); 
} 
ul.appendChild(fragment); 
```

### 9. `Attr`类型

元素的属性由`Attr`类型节点表示

- `nodeTyp`e` ： 2
- `nodeName` ：属性名
- `nodeValue` ：属性值
- `parentNode` ： `null`
- 在 `HTML` 中不支持（没有）子节点
- 在 `XML` 中子节点可以是 `Text` 或 `EntityReference`

`document.createAttribute()`创建`Attr`节点

```javascript
var attr = document.createAttribute("align"); 
attr.value = "left"; 
element.setAttributeNode(attr); 
```

## 2. `DOM`的操作

#### 2.1 动态添加脚本

```javascript
function loadScript(url){ 
    var script = document.createElement("script"); 
    script.type = "text/javascript"; 
    script.src = url; 
    document.body.appendChild(script); 
} 
```

#### 2.2 动态样式

```javascript
// 加载样式表示异步的
function loadStyles(url){ 
    var link = document.createElement("link"); 
    link.rel = "stylesheet"; 
    link.type = "text/css"; 
    link.href = url; 
    var head = document.getElementsByTagName("head")[0]; 
    head.appendChild(link); 
} 
// style
var style = document.createElement("style"); 
style.type = "text/css"; 
style.appendChild(document.createTextNode("body{background-color:red}")); 
var head = document.getElementsByTagName("head")[0]; 
head.appendChild(style); 

// 对于IE可以使用
style.styleSheet.cssText = "body{background-color:red}"; 
```

#### 2.3 操作表格

#### 2.4 使用`NodeList`

透彻理解`DOM`的核心在于理解`NodeList` `NamedNodeMap` `HTMLCollection`之间的关系和区别。他们都是动态的，随着文档的结构变化而变化。

他们三个几乎一样。

`NodeList`：`getElementsByName`和`querySelectorAll`，有`forEach`方法

`HTMLCollection`：没有遍历方法，只能转化为普通数组。还有一个意思的地方，这个集合会把元素的`ID`和`name`也放进去

![1537692589551](E:\培训\笔记图\HTMLCollection.png)

```javascript
var divs = document.getElementsByTagName("div"), 
    i, 
    div; 
for (i=0; i < divs.length; i++){ 
    div = document.createElement("div"); 
    document.body.appendChild(div); 
} 
// 上面代码会导致死循环，因为每次循环条件判断的时候都会重新获取HTMLCollection的长度，但是由于上一次循环增加了一个元素所以，循环的停止条件永远不会满足。
// 下面的条件 将长度赋给了一个变量
// 尽量减少访问NodeList的次数，每一次访问都会有一次文档的查询
var divs = document.getElementsByTagName("div"), 
    i, 
    len, 
    div; 
for (i=0, len=divs.length; i < len; i++){ 
    div = document.createElement("div"); 
    document.body.appendChild(div); 
} 
```

---

# :couch_and_lamp:第十一章 DOM扩展

---

## 1. 选择符`API`

### 1.1 `querySelector()`

	找到第一个符合的元素，没有找到返回`null`

### 1.2 `querySelectorAll()`

	找到所有符合的元素的一个`NodeList`对象，没有就是一个空的`NodeList`

### 1.3 ` matchesSelector()`

	参数也是`CSS`选择符，找到返回`true`否则返回`false`。几乎没有浏览器实现这个方法

## 2. 元素遍历

`childElementCount`：返回子元素（不包括文本节点和注释）的个数。
`firstElementChild`：指向第一个子元素；`firstChild`的元素版。
`lastElementChild`：指向最后一个子元素；`lastChild` 的元素版。
`previousElementSibling`：指向前一个同辈元素；`previousSibling` 的元素版。
`nextElementSibling`：指向后一个同辈元素；`nextSibling` 的元素版

## 3. HTML5

此章节着重于`DOM`有关的内容

### 3.1 与类操作相关的

-   ` getElementsByClassName()`

    返回`NodeList`，传入多个类名时，不区分先后。注意`NodeList`的性能问题

-   `classList`属性

    ```javascript
    //<div class="bd user disabled">...</div> 
    // 传统做法
    
    
    //删除"user"类
    //首先，取得类名字符串并拆分成数组
    var classNames = div.className.split(/\s+/); 
    //找到要删的类名
    var pos = -1, 
        i, 
        len; 
    for (i=0, len=classNames.length; i < len; i++){ 
        if (classNames[i] == "user"){ 
            pos = i; 
            break; 
        } 
    } 
    //删除类名
    classNames.splice(i,1); 
    //把剩下的类名拼成字符串并重新设置
    div.className = classNames.join(" "); 
    ```

    `HTML5`新增的方法，为所有元素添加了`classList`属性，该属性是一个是一个`DOMTokenList`集合，它有一些独有的方法

    -   `add()`：添加字符串至列表
    -   `contains()`：手否存在匹配的值，返回布尔值
    -   `remove()`：删除指定字符串
    -   `toggle()：`如果存在对应的值删除，否则添加

```javascript
// 之前的代码可以这样些
div.classList.remove("user"); 

//删除"disabled"类
div.classList.remove("disabled"); 
//添加"current"类
div.classList.add("current"); 
//切换"user"类
div.classList.toggle("user"); 
//确定元素中是否包含既定的类名
if (div.classList.contains("bd") && !div.classList.contains("disabled")){ 
  //执行操作
) 
//迭代类名
for (var i=0, len=div.classList.length; i < len; i++){ 
  doSomething(div.classList[i]); 
}
// 有了 classList 属性，除非你需要全部删除所有类名，或者完全重写元素的 class 属性，否则也就用不到 className 属性了。
```

### 3.2  焦点管理

-   `document.activeElement`

    获取文档中所有获得焦点的元素

-   `focus()`

    对元素调用此方法，获得焦点

-   `hasFocus()`

    判断是否获得了焦点

### 3.3 `HTMLDocument`的变化

	`HTML5`加入了新的`Document`属性

-   `readyState`

    `loading`、`complete`表示文档加载完毕否

-   `compatMode`

    `CSS1Compat`：标准模式

    `BackCompat`：怪异模式

-   `head`

    获取`<head>`标签

-   `charset`

    字符集属性

-   自定义属性

    `dataset`属性

    ```javascript
    // 自定义属性需要前缀data-
    //<div id="myDiv" data-appId="12345" data-myname="Nicholas"></div> 
    //本例中使用的方法仅用于演示
    var div = document.getElementById("myDiv"); 
    //取得自定义属性的值
    var appId = div.dataset.appId; 
    var myName = div.dataset.myname; 
    //设置值
    div.dataset.appId = 23456; 
    div.dataset.myname = "Michael"; 
    //有没有"myname"值呢？
    if (div.dataset.myname){ 
        alert("Hello, " + div.dataset.myname); 
    } 
    ```

-   插入标记

    -   `innerHTML`

        可读写，读时返回所有的子节点，写时创建新的`DOM`数，会覆盖之前的书写。

        ```javascript
        div.innerHTML = "<script defer>alert('hi');<\/script>"; //无效
        ```

        不支持 innerHTML 的元素有：<col>、<colgroup>、
        <frameset>、<head>、<html>、<style>、<table>、<tbody>、<thead>、<tfoot>和<tr>。此
        外，在 IE8 及更早版本中，<title>元素也没有 innerHTML 属性。

        在`IE8`中使用`window.toStaticHTML()`将一个`HTML`字符串中的所有脚本节点和事件都删除。

    -   `outerHTML`

        ```html
        <div id="content"> 
            <p>This is a <strong>paragraph</strong> with a list following it.</p> 
            <ul> 
                <li>Item 1</li> 
                <li>Item 2</li> 
                <li>Item 3</li> 
            </ul> 
        </div> 
        ```

        读时，如读取`div.outerHTML`，会返回包括`div`本身的，和上面一模一样的代码。

        写时，表示替换节点

        ```javascript
        div.outerHTML = "<>This is a ararah.</>" 
        // 相当于
        var p = document.createElement("p"); 
        p.appendChild(document.createTextNode("This is a paragraph.")); 
        div.parentNode.replaceChild(p, div); 
        ```

    -   `insertAdjacentHTML()`

        两个参数，插入的位置和要插入的文本。第一个参数必须是以下几个值

        -   `beforebegin`：在元素之前插入
        -   `afterbegin`：在第一个子元素之前插入
        -   `beforeend`：在最后一个元素之后插入
        -   `afterrend`：在元素之后插入

    -   内存和性能问题

        如果一个元素在被删除替换前没有手动清除事件，就算使用上面的属性清除了该元素，绑定的事件仍然在内存中。

        ```javascript
        for (var i=0, len=values.length; i < len; i++){ 
            ul.innerHTML += "<li>" + values[i] + "</li>"; //要避免这种频繁操作！！
        } 
        // 上面每次循环访问了两次innerHTML
        // 最好先组合好字符串再输出
        var itemsHtml = "";
        for (var i=0, len=values.length; i < len; i++){ 
            itemsHtml += "<li>" + values[i] + "</li>"; 
        } 
        ul.innerHTML = itemsHtml; 
        ```

### 3.4 `scrollIntoView()`

	任何元素都能调用`scrollIntoView()`，默认或者`true`为参数时，会使元素的**顶部**与视口平齐，为`false`时会使元素**底部**与视口平齐

## 4. 专有扩展

### 4.1 文档模式

	只有`IE`才有这种东西，也就是决定了你可以使用哪个级别的`CSS`或者哪些`javascript API`。
	
	可以通过修改`meta`
	
	`document.documentMode` 查询

```html
<meta htt-euiv="X-UA-Comatible" content="IE=IEVersion"> 
<!--IE版本可以设置很多 
Edge：始终使用最新的文档模式
6：按照ie6
7：按照ie7-->
```

### 4.2 `children`属性

	与`childNodes`没什么区别，只是`children`只包含元素，不包含文本节点之类的。

### 4.3 `contains()`

	判断一个节点是不是另外一个节点的子代（儿子，孙子..都是）。

```javascript
alert(document.documentElement.contains(document.body)); //true 
```

### 4.4 `innerText`和`outerText`

-   `innerText`：

    ```html
    <div id="content"> 
        <p>This is a <strong>paragraph</strong> with a list following it.</p> 
        <ul> 
            <li>Item 1</li> 
            <li>Item 2</li> 
            <li>Item 3</li> 
        </ul> 
    </div> 
    <!--使用innerText返回 除了标签以外的-->
    This is a paragraph with a list following it. 
    Item 1 
    Item 2 
    Item 3 
    ```

-   `outerText`

    和`innerText`没多大区别，参考`outerHTML`

---

# :fleur_de_lis: 第十二章  DOM2和DOM3

---

	`DOM1`主要定义的是`HTML`和`XML`的底层结构，二级和三级增加了许多新的交互功能。

## 1. 命名空间

## 2. 样式

	支持`style`属性的元素，在`JS`中都有对应的属性，`style`是` CSSStyleDeclaration `的一个实例短线分割的属性如`background-image`变成	`style.backgroundColor `。`float`是`js`的保留字，所以只能使用`cssFloat`。

### 2.1 `style`对象属性和方法

属性：

-   `cssText`：访问`style`中的`css`代码
-   `length`：`css`规则条数
-   `parentRule`：`CSSRule`对象，在后一节

方法：

`propertyName`都是带短横线的名字

-   `getPropertyCSSValue(propertyName)`：返回指定属性值的`CSSValue`对象==好像已经失效了==
-   `getPropertyPriority(propertyName)`：给定的属性使用了`!important` 设置，则返回`important`；否则，返回空字符串
-   `getPropertyValue(propertyName)`：返回指定属性的字符串值
-   `item(index)`：返回给定位置的`CSS`属性名称
-   `removeProperty(propertyName)`：从样式中删除给定属性
-   `setProperty(propertyName,value,priority)`：将给定属性设置为相应的值，并加上优先权标志（`important`或者一个空字符串）

1.  `cssText`

    可读写，读取的时候返回`style`的内容字符串

    写入的时候，会完全覆盖之前的代码

2.  `length`和`item()`

    方便迭代`style`里面的属性

    ```javascript
    for (var i=0, len=myDiv.style.length; i < len; i++){ 
        alert(myDiv.style[i]); //或者 myDiv.style.item(i) 
    } 
    // 输出的属性名是和css里面一样的，带着短横线
    // 如：输出border-top-color
    ```

### 2.2 计算样式

	就是整个样式，包括`style`属性上的`style`标签内的以及外联样式，通过计算(就是优先级计算)后的样式值

```javascript
<style type="text/css"> 
     #myDiv { 
     background-color: blue; 
     width: 100px; 
     height: 200px; 
     } 
 </style> 
<div id="myDiv" style="background-color: red; border: 1px solid black"></div>

var myDiv = document.getElementById("myDiv"); 
var computedStyle = document.defaultView.getComputedStyle(myDiv, null); 
alert(computedStyle.backgroundColor); // "red" 
alert(computedStyle.width); // "100px" 
alert(computedStyle.height); // "200px" 
alert(computedStyle.border); // 在某些浏览器中是"1px solid black" 
```

### 2.3 操作样式表

	`CSSStyleSheet `类型表示样式表，内联式查询不了

```javascript
document.styleSheets //获取所有样式表
// 输出
StyleSheetList(2)[]
0: StyleSheet 
1: StyleSheet 

```

包含如下属性：

-   `disabled`：`true`禁用此样式表
-   `href`：只有外联样式表才有值
-   `media`：支持的媒体类型集合
-   `ownerNode`：拥有当前样式的指针
-   ` parentStyleSheet`：通过`@import`导入情况下才有
-   ` title`：`ownerNode `中 `title `属性的值
-   ` type`：表示样式表类型的字符串
-   ` cssRules`：样式表中包含的样式规则的集合
-   `deleteRule(index)`：删除 `cssRules `集合中指定位置的规则
-   `insertRule(rule,index)`： `cssRules `集合中指定的位置插入 `rule `字符串

#### 2.3.1 `CSSRule`

	`StyleSheet`里面有属性`CSSRule`

```javascript
var sheet = document.styleSheets[0]; 
var rules = sheet.cssRules || sheet.rules; //取得规则列表
var rule = rules[0]; //取得第一条规则
alert(rule.selectorText); //"div.box" 
alert(rule.style.cssText); //完整的 CSS 代码
alert(rule.style.backgroundColor); //"blue" 
alert(rule.style.width); //"100px" 
alert(rule.style.height); //"200px" 
// 除了获取 也能直接赋值修改
```

#### 2.3.2 `insertRule()`

	向现有的规则中添加新规则。两个参数：规则和添加位置索引。

只能增加整个样式，不能单条增加

```javascript
sheet.insertRule("body { background-color: silver }", 0); //DOM 方法
// 插入到了样式表第一条位置

// 删除
sheet.deleteRule(0); //DOM 方法
```

### 2.4 元素大小

	可以确定元素的大小

#### 2.4.1 偏移量

	元素距离**屏幕**的一些参数，设置box-sizeing对数值有影响，会按照标准/怪异盒子模型来计算
	offsetLeft 和 offsetTop
	父元素没有定位和没有父元素时 表示相对于浏览器的距离
	父元素有定位时，相对于父元素的距离

![1537962383707](E:\培训\笔记图\偏移量.png)

#### 2.4.2 客户区大小

元素**内容**及其**内边距**所占据的空间大小。滚动条不算在内

![1538016826307](E:\培训\笔记图\客户区大小.png)

#### 2.4.3 滚动大小

![1538016982453](E:\培训\笔记图\滚动大小.png)

#### 2.4.4 确定元素大小

	`getBoundingClientRect()`包含`left、top、right、bottom`，元素在页面中相对于视口的位置

## 3. 遍历

`NodeIterator`和`TreeWalker` p346

![1538018853786](E:\培训\笔记图\节点遍历.png)

## 4. 范围

选择一定范围的节点，`createRange()`返回一个`range`实例，每个实例都有下面的属性和方法，这些属性没搞懂

-   `startContainer`：范围起点
-   `startOffset`：(范围在 `startContainer` 中起点的偏移量，如果 `startContainer` 是文本节点、注释节点或` CDATA `节点，那么 `startOffset `就是范围起点之前跳过的字符数量。) 否则，
  `startOffset` 就是范围中第一个子节点的索引
-   `endContainer`：范围终点
-   `endOffset`：范围在 `endContainer`中终点的偏移量，规则与`startOffset`相同
-   ` commonAncestorContainer`：`startContainer` 和 `endContainer` 共同的祖先节点在文档树
  中位置最深的那个

### 4.1 `DOM`范围简单选择

	两个方法都接受一个参数，一个`DOM`节点作参考。

-   ` selectNode()`选择节点包括子节点
-   ` selectNodeContents()`选择节点不包括子节点

```html
<!DOCTYPE html> 
<html> 
    <body> 
        <p id="p1"><b>Hello</b> world!</p> 
    </body> 
</html> 
```

```javascript
// 创建range实例
var range1 = document.createRange(); 
range2 = document.createRange(); 
// 参考节点
p1 = document.getElementById("p1"); 
// 创建范围
range1.selectNode(p1); // <p><b></b></p>
range2.selectNodeContents(p1); // <b></b>
// range1
startContainer: body
startOffset: 1 
endContainer: body
endOffset: 2 // 
collapsed: false
commonAncestorContainer: body
// range2
startContainer: p#p1
startOffset: 0
endContainer: p#p1
endOffset: 3
collapsed: false
commonAncestorContainer: p#p1 }
```

![1538138531804](E:\培训\笔记图\范围选择dom.png)

其他更精确的选择方法：

-   `setStartBefore(refNode)`：将起点设置在ref之前
-   ` setStartAfter(refNode)`：起点设置在之后
-   ` setEndBefore(refNode)`：结束设置在之前
-   ` setEndAfter(refNode)`：结束设置在之后

### 4.2 复杂范围选择

可以通过下面两个方法，主动设置起点和终点，以及偏移量。

-   `setStart()`
-   `setEnd()`
-   都接受两个参数，参考节点和偏移量

### 4.3 操作范围

-   `deleteContents()`
-   `extractContents()`
-   两个都是从文档中移除范围，第二个会返回移除的范围
-   `cloneContents()`克隆范围
-   `insertNode()`向范围中插入节点
-   `collapse()`：`true`折叠到起点，`false`折叠到终点

![1538139726156](E:\培训\笔记图\折叠范围.png)

-   `compareBoundaryPoints()`：确定两个范围有没有公共边界
-   `detach()`：清理范围

---

# :camel:第十三章  事件

---

## 1. 捕获和冒泡

冒泡：从事件源开始依次向父元素触发

捕获：冒泡反过来

## 2. DOM事件流

事件流包括三个阶段：

-   捕获阶段：为截获提供机会
-   目标阶段：目标接收到事件
-   冒泡阶段：在这个阶段做出响应 

![1538183469705](E:\培训\笔记图\DOM事件流.png)

比如，点击`div`首先捕获阶段（不会接受事件），捕获阶段从`document`开始到`body`就停止了，下一个阶段，目标阶段，在`div`上发生事件，同时`div`又是冒泡事件的一部分，进行冒泡事件传回文档。

## 3. 事件处理程序

### 3.1 HTML事件处理程序

```html
<input type="button" value="Click Me" onclick="alert(&quot;Clicked&quot;)" /> 
<!--因为是在html中书写 所以要转移字符-->
<input type="button" value="Click Me" onclick="fn()" /> 
```

在标签中定义事件有如下特点：

-   为什么在标签中添加函数要加括号？为什么阻止默认事件需要在标签上再写一次`return fn()`？

    这样定义事件，会动态创建一个包含这个元素属性的函数，将事件函数包裹

    ```javascript
    function(){ 
        with(document){ 
            with(this){ 
                //元素属性值
                // fn()
            } 
        } 
    } 
    // 如果有表单
    function(){ 
        with(document){ 
            with(this.form){ 
                with(this){ 
                    //元素属性值
                    // fn()
                } 
            } } 
    } 
    ```

    这样做有个好处，能直接在事件函数里面访问`event`对象，元素属性，表单元素..

    ```html
    // 直接获取属性
    <input type="button" value="Click Me" onclick="alert(value)"> 
    // 直接获取事件函数
    <input type="button" value="Click Me" onclick="alert(event)"> 
    // 直接获取表单元素
    <input type="button" value="Click Me" onclick="alert(myform)"> 
    ```

-   缺点，事件触发与程序运行

    当元素一出现，用户就有可能触发事件，但是事件函数有可能不具备运行条件，比如：页面还没完全加载完，事件函数需要调用后面的元素，这样就有可能获取不到

-   缺点，让`html`和`js`耦合了，不便于维护调试

### 3.2 DOM0级事件处理程序

```javascript
var btn = document.getElementById("myBtn"); 
btn.onclick = function(){ 
    alert("Clicked"); 
}; 
// 删除事件
btn.onclick = null
```

注意区分在元素上注册事件，在这里访问元素属性需要`this`。

以这种方式注册的事件会在==冒泡阶段==处理

### 3.3 DOM2级事件处理程序

增加两个方法

-   `addEventListener()`：事件类型，函数，是否捕获
-   `removeEventListener()`：事件类型，函数，是否捕获

```javascript
var btn = document.getElementById("myBtn"); 
btn.addEventListener("click", function(){ 
    alert(this.id); 
}, false); 
btn.addEventListener("click", function(){ 
    alert("Hello world!"); 
}, false); 
// 会在冒泡阶段触发
// 按照注册顺序
```

### 3.4 IE事件处理程序

-   `attachEvent()`
-   `detachEvent()`

==注意==：

这种方式注册的函数，作用域为全局`this==window`

按照相反的顺序触发

## 4. 事件对象

### 4.1 标准事件对象

| bubbles                    | 是否冒泡                                                     |
| -------------------------- | ------------------------------------------------------------ |
| cancelable                 | 是否可以取消默认事件                                         |
| currentTarget              | 正在处理时间的元素                                           |
| defaultPrevented           | true表示调用了preventDefault（）                             |
| detail                     | 细节信息                                                     |
| evevtPhase                 | 时间阶段1：捕获2：处于目标3：冒泡                            |
| preventDefault（）         | 阻止默认行为                                                 |
| stopImmediatePropagation() | 取消事件的进一步捕获或冒泡，同时阻止任何事件处理程序被调用   |
| stopPropagation()          | 取消事件的进一步捕获或冒泡                                   |
| target                     | 事件的目标                                                   |
| trusted                    | 为true表示事件是浏览器生成的。为false表示事件是由开发人员通过 JavaScript 创建的 |
| type                       | 被触发的事件的类型                                           |
| view                       | 与事件关联的抽象视图。等同于发生事件的window对象             |

`this`始终等于`currentTarget`，也就是给谁绑定就是谁

`target`只包含事件实际目标。

```javascript
//如果直接将事件处理程序指定给了目标元素，则 this、currentTarget 和 target 包含相同的值
var btn = document.getElementById("myBtn"); 
btn.onclick = function(event){ 
    alert(event.currentTarget === this); //true 
    alert(event.target === this); //true 
}; 
// 如果事件处理程序存在于按钮的父节点中（例如 document.body），那么这些值是不相同的
document.body.onclick = function(event){ 
    alert(event.currentTarget === document.body); //true 
    alert(this === document.body); //true 
    alert(event.target === document.getElementById("myBtn")); //true 
}; 
```

### 4.2 IE中的事件对象

特有的属性

| cancelBubble | 取消冒泡                |
| ------------ | ----------------------- |
| returnValue  | 设置为fasle阻止默认事件 |
| srcElement   | 相当于target            |

```javascript
var btn = document.getElementById("myBtn"); 
btn.onclick = function(){ 
 alert(window.event.srcElement === this); //true 
}; 
// 最好还是使用 event.srcElement 比较保险
btn.attachEvent("onclick", function(event){ 
 alert(event.srcElement === this); //false 
}); 
```

## 5. 事件类型

### 5.1 UI事件

不一定与用户操作有关的事件

-   `load`：当页面加载完后在`window`上面触发，或者在框架加载完后再框架集上面触发，或者在图像加载完后在`<img>`上面触发，当嵌入的内容加载完毕时在`<object>`元素上面触发
-   `unload`：页面卸载后触发
-   `abort`：在用户停止下载过程时，如果嵌入的内容没有加载完，则在`<object>`元素上面触发
-   `error`：当发生 `JavaScript` 错误时在 `window` 上面触发，当无法加载图像时在`<img>`元素上面触发，当无法加载嵌入内容时在`<object>`元素上面触发，或者当有一或多个框架无法加载时在框
  架集上面触发
-   `select`：选择时触发
-   `resize`：窗口或框架大小变化时触发
-   `scroll`：滚动条滚动

### 5.2 焦点事件

结合`document.hasFocus()`和`document.activeElement`知晓有哪些元素被激活

-   `blur`：失去焦点触发，这个事件==不会冒泡==
-   `focus：`获得焦点，不冒泡
-   `focusin`：冒泡版获得焦点
-   `focusout`：冒泡版失去焦点事件

### 5.3 鼠标与滚轮事件

-   ` click：`点击和按下回车键
-   ` dblclick：`双击
-   `mousedown：`不能通过键盘触发
-   ` mouseenter：` **不冒泡**，
-   ` mouseover：`要冒泡，
-   ` mouseleave：`**不冒泡**
-   ` mousemove：`不能通过键盘触发
-   ` mouseout：`不能通过键盘触发
-   ` mouseup：`不能通过键盘触发

虽然鼠标事件可以被取消，但是取消一件事件可能会影响其他的事件。如：取消`mousedown`或者`mouseup`会让`click`事件无效

#### 5.3.1 鼠标坐标

-   `clientX`
-   `clientY`

#### 5.3.2 页面坐标

能够知道事件是在页面什么位置发生的

-   `pageX`
-   `pageY`

#### 5.3.3 屏幕坐标位置

鼠标相对于电脑屏幕的位置

-   `screenX`
-   `screenY`

![1538277264631](E:\培训\笔记图\screenX.png)

#### 5.3.4 修改键

`Shift`、`Ctrl`、`Alt` 和 `Meta` `（在 Windows 键盘中是 Windows 键，在苹果机中是 Cmd 键）`

对应的有如下属性：`shiftKey、ctrlKey、altKey 和 metaKey`

#### 5.3.5 相关元素

比如：`mouseover`，事件的主目标是被移入的元素，而那个失去光标的元素就是相关元素。` relatedTarget `来获取

#### 5.3.6 触摸设备

-   不支持双击事件
-   轻击单击元素会触发`mousemove`事件，如果没有导致屏幕内容变化，会一次发生`mousedown` `mouseup` `click`
-   `mousemove`也会触发`mouseover\mouseout`
-   两个手指放在屏幕上会触发`mousewheel\scroll`

### 5.4 键盘文本事件

-   `keydown`：**任意键**触发，按住不放持续触发
-   `keypress`：**字符键**触发，按住不放持续触发
-   `keyup`：释放按键触发

#### 5.4.1 键码

当发生` keydown `和` keyup `事件时，`keyCode`属性会包含按键的`ASCII`码

当发生` keypress `时，`charCode`返回字符的`ASCII`码，`keyCode`通常为0

在`DOM3`中新增了`key`和`char`，直接接返回按键实际值。

新增`location`属性，表示按下了什么位置上的键：0 表示默认键盘，1 表示左侧位置（例如左位的 Alt 键），2 表示右侧位置（例如右侧的 Shift 键），3 表示数字小键盘，4 表示移动设备键盘（也就是虚拟键盘），5 表示手柄（如任天堂 Wii 控制器）。

### 5.5 复合事件

用于处理`IME(输入法编辑器)`输入序列，可以让用户输入键盘上找不到的字符。也就是同时按多个键，但是输入的是一个字符。也可以用于侦测中文输入法下是否被按下之类的......。英文输入法无法触发。

-   ` compositionstart：` `IME`使用时触发
-   ` compositionupdate：`在输入字段中插入时触发
-   ` compositionend：` `IME`结束使用时触发

` compositionupdate`事件肯定会发生，感觉没什么用

### 5.6 变动事件(mutation event)

不针对特定的语言，本身就设计好的。其中很多都被废弃了。

`DOMSubtreeModified`：在 DOM 结构中发生任何变化时触发。这个事件在其他任何事件触发后都会触发。
`DOMNodeInserted`：在一个节点作为子节点被插入到另一个节点中时触发。
`DOMNodeRemoved`：在节点从其父节点中被移除时触发。
`DOMNodeInsertedIntoDocument`：在一个节点被直接插入文档或通过子树间接插入文档之后
触发。这个事件在 `DOMNodeInserted` 之后触发。
`DOMNodeRemovedFromDocument`：在一个节点被直接从文档中移除或通过子树间接从文档中移除之前触发。这个事件在 `DOMNodeRemoved` 之后触发。
`DOMAttrModified`：在特性被修改之后触发。
`DOMCharacterDataModified`：在文本节点的值发生变化时触发。

#### 5.6.1 删除节点

使用`removeChild()`或者`replaceChild()`时，在节点**还未删除时**，会首先触发`DOMNodeRemoved`事件，会**冒泡**。

如果被移除的节点拥有子节点，这个节点以及它多有的子节点都会触发`DOMNodeRemovedFromDocument`，这个事件**不会冒泡**。

随后触发`DOMSubtreeModified `事件。

#### 5.6.2 插入节点

在使用`appendChild()、replaceChild()或 insertBefore()`时，元素**被插入到节点之后**，首先会触发`DOMNodeInserted`事件，会**冒泡**。

 在新插入的节点上面触发`DOMNodeInsertedIntoDocument`事件，**不冒泡**插入节点之前为它添加这个事件处理程序

最后一个触发的事件是`DOMSubtreeModified`，触发于新插入节点的**父节点**。

### 5.7 HTML5事件

#### 1.  `contextmenu`事件

使用`event.preventDefalut()`来屏蔽浏览器右键菜单

#### 2.  `beforeunload `事件

关闭页面之前触发

```javascript
window.onbeforeunload = function (e) {
    var message = "I'm really going to miss you if you go."; 
    e.returnValue = message; // 针对ie和火狐
    return message; // 针对谷歌和苹果
}
// 不写return 谷歌也能产生对话框
// 就算写了字符串 也不能显示出自定义的文字
// 页面加载完了才可以触发这个事件
```

#### 3. `DOMContentLoaded `事件

只要`DOM`树加载完毕就能触发，不会等待外部加载资源。

#### 4. `readystatechange` 事件

当文档以及它的元素`state`发生改变时触发，每个对象有个`readyState`属性，其值又5个

-   `uninitialized（未初始化）`：对象存在但尚未初始化。
-    `loading（正在加载）`：对象正在加载数据
-   `loaded（加载完毕）`：对象加载数据完成
-   `interactive（交互）`：可以操作对象了，但还没有完全加载
-   `complete（完成）`：对象已经加载完毕

不是所有的对象都会触发完整的这几个状态。

#### 5. `pageshow 和 pagehide` 事件

因为有的浏览器如火狐和`Opera`一个往返缓存功能，当页面返回时并不会触发`load`事件，所以使用这两个事件来判断是否显示或者关闭了页面。必须设置在`window`上

#### 6.` hashchange `事件

`hash`字符串改变时触发，也就是`#`之后的所有字符串，必须给`window`注册这个事件，`event`对象会新增两个值`oldURL`和`newURL`分别保存变化前后的两个`hash`值。

这两个属性不同浏览器支持度不一样，最好使用`location.hash`来确定当前的`hash`值。

### 5.8 设备事件

#### 5.8.1 ` orientationchange `事件

移动端`Safari`的`window.orientation`，包含三个值

![1538995352092](E:\培训\笔记图\orientation事件.png)

#### 5.8.2 ` deviceorientation `事件

告诉开发者设备朝向哪儿，而不是如何移动的。

`x`轴方向永远是从左向右

`y`轴方向从下倒上

`z`轴方向从里向外

总共5个属性

-   `alpha`：设备在左右旋转时（绕z轴旋转），`0~360`之间的浮点数

    ![1538995971354](E:\培训\笔记图\alpha属性.png)

-   `beta`：设备在前后旋转时（绕x轴旋转），`-180~180`之间

    ![1538996417994](E:\培训\笔记图\beta属性.png)

-   `gama`：设备扭转（绕y轴旋转），`-90~90`之间

    ![1538996486154](E:\培训\笔记图\gama属性.png)

-   `absolute`：布尔值，是否返回一个绝对值

-   ` compassCalibrated`：布尔值，设备指南针有无校准过

#### 5.8.3 `devicemotion`事件

可以读取加速度值。有如下属性

-    `acceleration：`包含每个方向`x、y、z`的加速度，不考虑重力
-   ` accelerationIncludingGravity：`考虑重力时每个方向的加速度
-   ` interval：`必须在另一个` devicemotion `事件触发前传入，读取加速度值的间隔事件，毫秒为单位
-   ` rotationRate：`包含的`alpha、beta 和 gamma`

如果没有读取到这三个属性值，他们的值应该为`null`使用前应该先判断是不是为`null`

### 5.9  触摸于手势事件

#### 5.9.1 触摸事件

以下事件：

- `touchstart`：当手指触摸屏幕时触发；即使已经有一个手指放在了屏幕上也会触发。
- `touchmove`：当手指在屏幕上滑动时连续地触发。在这个事件发生期间，调用`preventDefault()`可以阻止滚动。
- `touchend`：当手指从屏幕上移开时触发。
- `touchcancel`：当系统停止跟踪触摸时触发。关于此事件的确切触发时间，文档中没有明确说明。
- 上面这几个事件都会冒泡，也都可以取消。

虽然这些触摸事件没有在 `DOM` 规范中定义，但它们却是以兼容 `DOM` 的方式实现的。

因此，每个触摸事件的 `event` 对象都提供了在鼠标事件中常见的属性：
`bubbles、cancelable、view、clientX、clientY、screenX、screenY、detail、altKey、shiftKey、ctrlKey 和 metaKey`。
除了常见的 `DOM` 属性外，触摸事件还包含下列三个**用于跟踪触摸的属性**。

- `touches`：表示当前跟踪的触摸操作的 `Touch` 对象的数组。
- `targetTouchs`：特定于事件目标的 `Touch` 对象的数组。
- `changeTouches`：表示自上次触摸以来发生了什么改变的 `Touch` 对象的数组

上面这三种`touch`对象包含下列属性：

- `clientX`：触摸目标在视口中的 `x` 坐标。
- `clientY`：触摸目标在视口中的 `y` 坐标。
- `identifier`：标识触摸的唯一 `ID`。
- `pageX`：触摸目标在页面中的 `x` 坐标。
- `pageY`：触摸目标在页面中的 `y` 坐标。
- `screenX`：触摸目标在屏幕中的 `x` 坐标。
- `screenY`：触摸目标在屏幕中的 `y` 坐标。
- `target`：触摸的 `DOM` 节点目标。

`touchend`事件发生后，`touch`对象就消失了，只能使用`changeTouch`

#### 5.9.2 手势事件

-   `gesturestart`：当一个手指已经按在屏幕上而另一个手指又触摸屏幕时触发。
-   `gesturechange`：当触摸屏幕的任何一个手指的位置发生变化时触发。
-   `gestureend`：当任何一个手指从屏幕上面移开时触发。
-   这些事件时冒泡事件，只有两只手指都在设事件的对象上才能触发。

==触发顺序==：

-   **一个手指**放在屏幕上时，先触发**`touchstart`事件**
-   **另一个手指**放在屏幕上，触发**`gesturestart`事件**
-   随后触发该手指的**`touchstart`事件**
-   两个手指在屏幕上滑动，触发**` gesturechange `事件**
-   只要有一个手指移开就会**触发`gestureend`**随后**触发`touchend`**

手势事件的事件对象，除了所那些标准的鼠标属性，还会包含两个特别的属性。

-   `rotation`：手指变化引起的旋转角度，负值表示逆时针旋转，正值表示顺时针旋转
-   `scale`：两个手指间距离的变化情况，值从 1 开始

## 6 内存和性能

比如：给每个按钮加上一个事件处理方法，会有如下问题。

-   每个**函数都是对象**，会**占用内存**
-   延迟整个页面的交互就绪时间

#### 6.1 事件委托

利用事件冒泡，关键就是利用事件对象的`e.target`属性

#### 6.2 移除事件处理程序

当某些事件不需要的时候，可以移除该事件处理程序，释放内存。

==注意==：

-   使用`innerHTML`清除元素时，有可能会导致元素上的事件处理函数移除，所以建议在清除之前手动清除`ele.onclick=null`。
-   页面关闭的时候，也有可能会让事件处理程序滞留在内存中，使用`onunload`事件移除所有事件，很明显，使用事件代理可以快速移除所有事件。

## 7 模拟事件

### 7.1 标准事件模拟

`createEvent()`方法创建对象，接收一个字符串参数，表示事件类型

- `UIEvents`：一般化的 `UI` 事件。鼠标事件和键盘事件都继承`UI` 事件。`DOM3` 级中是 `UIEvent`。
- `MouseEvents`：一般化的鼠标事件。`DOM3` 级中是 `MouseEvent`。
- `MutationEvents`：一般化的 `DOM` 变动事件。`DOM3` 级中是 `MutationEvent`。
- `HTMLEvents`：一般化的 `HTML` 事件。没有对应的 `DOM3` 级事件（`HTML` 事件被分散到其他类别中）。
- `KeyboardEvent`：`DOM3`中才有的，键盘事件

定义好事件对象后，还需要初始化发生事件时的事件对象参数，每个种类的事件初始方法都不同`initMouseEvent` `initKeyboardEvent`....

#### 7.1.1 鼠标事件模拟

1.  使用`createEvent('MouseEvents')`

2.  返回的对象有一个`initMouseEvent()`，指定事件的有关信息，包含15个参数

    >   `type（字符串）`：表示要触发的事件类型，例如`"click"`。
    >   `bubbles（布尔值）`：表示事件是否应该冒泡。为精确地模拟鼠标事件，应该把这个参数设置为`true`。
    >   `cancelable（布尔值）`：表示事件是否可以取消。为精确地模拟鼠标事件，应该把这个参数设置为 `true`。
    >   `view（AbstractView）`：与事件关联的视图。这个参数几乎总是要设置为 `document.defaultView`。
    >   `detail（整数）`：与事件有关的详细信息。这个值一般只有事件处理程序使用，但通常都设置为 0。
    >   `screenX（整数）`：事件相对于屏幕的 `X` 坐标。
    >   `screenY（整数）`：事件相对于屏幕的 `Y` 坐标。
    >   `clientX（整数）`：事件相对于视口的 `X` 坐标。
    >   `clientY（整数）`：事件想对于视口的 `Y` 坐标。
    >   `ctrlKey（布尔值）`：表示是否按下了 `Ctrl` 键。默认值为 `false`。
    >   `altKey（布尔值）`：表示是否按下了 `Alt` 键。默认值为 `false`。
    >   `shiftKey（布尔值）`：表示是否按下了 `Shift` 键。默认值为 `false`。
    >   `metaKey（布尔值）`：表示是否按下了` Meta` 键。默认值为 `false`。
    >   `button（整数）`：表示按下了哪一个鼠标键。默认值为 0。
    >   `relatedTarget（对象）`：表示与事件相关的对象。这个参数只在模拟 `mouseover` 或 `mouseout`时使用

```javascript
var btn = document.getElementById("myBtn"); 
//创建事件对象
var event = document.createEvent("MouseEvents"); 
//初始化事件对象
event.initMouseEvent("click", true, true, document.defaultView, 0, 0, 0, 0, 0, 
                     false, false, false, false, 0, null); 

//触发事件
btn.dispatchEvent(event); 
```

#### 7.1.2 键盘模拟事件

`initKeyEvent()`接收一下参数

>   `type（字符串）`：表示要触发的事件类型，如`"keydown"`。
>   `bubbles（布尔值）`：表示事件是否应该冒泡。为精确模拟鼠标事件，应该设置为 `true`。
>   `cancelable（布尔值）`：表示事件是否可以取消。为精确模拟鼠标事件，应该设置为 `true`。
>   `view（AbstractView）`：与事件关联的视图。这个参数几乎总是要设置为 `document. defaultView`。
>  ` key（布尔值）`：表示按下的键的键码。
>  ` location（整数）`：表示按下了哪里的键。0 表示默认的主键盘，1 表示左，2 表示右，3 表示数字键盘，4 表示移动设备（即虚拟键盘），5 表示手柄。
>   `modifiers（字符串）`：空格分隔的修改键列表，如`"Shift"`。
>   `repeat（整数）`：在一行中按了这个键多少次。

```javascript
var textbox = document.getElementById("myTextbox"), 
    event; 
//以 DOM3 级方式创建事件对象
if(document.implementation.hasFeature("KeyboardEvents", "3.0")){ 
    event = document.createEvent("KeyboardEvent"); 
    //初始化事件对象
    event.initKeyboardEvent("keydown", true, true, document.defaultView, "a", 0, "Shift", 0); 
} 
//触发事件
textbox.dispatchEvent(event); 
// 按下小写a的事件
// 按住 Shift 的同时又按下 A 键
```

#### 7.1.3 模拟其他事件

变动事件`initMutation()`，参数包括`type、bubbles、
cancelable、relatedNode、preValue、newValue、attrName 和 attrChange`

```javascript
// 模拟 DOMNodeInserted 事件
var event = document.createEvent("MutationEvents"); 
event.initMutationEvent("DOMNodeInserted", true, false, someNode, "","","",0); 
taret.disatchEvent(event) 

// 模拟focus事件
var event = document.createEvent("MutationEvents"); 
event.initMutationEvent("DOMNodeInserted", true, false, someNode, "","","",0); 
taret.disatchEvent(event) 
```

#### 7.1.4 自定义DOM事件

调用`createEvent("CustomEvent")`，返回`initCustomEvent()`方法，接收如下四个参数：

>   `type（字符串）`：触发的事件类型，例如`"keydown"`。
>   `bubbles（布尔值）`：表示事件是否应该冒泡。
>   `cancelable（布尔值）`：表示事件是否可以取消。
>   `detail（对象）`：任意值，保存在 `event` 对象的` detail `属性中

```javascript
var div = document.getElementById("myDiv"), 
    event; 
EventUtil.addHandler(div, "myevent", function(event){ 
    alert("DIV: " + event.detail); 
}); 
EventUtil.addHandler(document, "myevent", function(event){ 
    alert("DOCUMENT: " + event.detail); 
}); 
if (document.implementation.hasFeature("CustomEvents", "3.0")){ 
    event = document.createEvent("CustomEvent"); 
    event.initCustomEvent("myevent", true, false, "Hello world!"); 
    div.dispatchEvent(event); 
} 
//这个例子创建了一个冒泡事件"myevent"。而 event.detail 的值被设置成了一个简单的字符串，然后在<div>元素和 document 上侦听这个事件。因为 initCustomEvent()方法已经指定这个事件应该冒泡，所以浏览器会负责将事件向上冒泡到 document
```



### 7.2 IE中的事件模拟

---

# :honey_pot:第十四章 表单脚本

---

## 1. 表单基础知识

表单对象是`HTMLFormElement`类型，继承与`HTMLElement`，有如下独有属性和方法

>   `acceptCharset`：服务器能够处理的字符集；等价于 `HTML`中的 `accept-charset` 特性。
>   `action`：接受请求的`URL`；等价于`HTML`中的 `action` 特性。
>   `elements`：表单中所有控件的集合`（HTMLCollection）`。
>   `enctype`：请求的编码类型；等价于 `HTML` 中的 `enctype` 特性。
>   `length`：表单中控件的数量。
>   `method`：要发送的 `HTTP`请求类型，通常是"get"或"post"；等价于 `HTML` 的 `method` 特性。
>   `name`：表单的名称；等价于`HTML` 的 `name `特性。
>   `reset()`：将所有表单域重置为默认值。
>   `submit()`：提交表单。
>   `target`：用于发送请求和接收响应的窗口名称；等价于 `HTML`的`target`特性

取得`<form>`元素有两种方式

```javascript
// 和其他元素一样
let form = document.getElemntBy('myForm');
// 通过form集合访问
let firstForm = document.forms[0];// 通过索引
let form = document.forms['form'];// 通过name
// 不建议使用documnet.form1 也就是直接写name来获取表单，因为这样容易出错，将来的浏览器也可能不支持这个功能
```

### 1.1 提交表单

点击**提交按钮**或者**图像按钮**都会触发提交，且只要下面的任何一种按钮存在，直接按回车也能提交数据(在`textarea`中是换行)

```html
<!-- 通用提交按钮 --> 
<input type="submit" value="Submit Form"> 
<!-- 自定义提交按钮 --> 
<button type="submit">Submit Form</button> 
<!-- 图像按钮 --> 
<input type="image" src="graphic.gif"> 
```

```javascript
// 编程提交
form.submit()
// 阻止提交
form.onsubmit = function() {
    return fasle
    // e.preventDfault()
}
// 利用提交事件，可以阻止重复提交表单
```

### 1.2 重置表单

```html
<!-- 通用重置按钮 --> 
<input type="reset" value="Reset Form"> 
<!-- 自定义重置按钮 --> 
<button type="reset">Reset Form</button> 
```

```javascript
// 编程重置
form.reset()
// 阻止重置
```

### 1.3  表单字段

每个 表单都有`elements`属性，也就是返回这个表单里面的所有控件

```html
<form action="" name="form">
    aaa<input type="text" name="a" id=""> <br>
    bbb<input type="password" name="b" id=""> <br>
    <select name="cc" id="">
        <option value=""></option>
        <option value=""></option>
        <option value=""></option>
    </select>
    <textarea name="dd"></textarea> <br>
    <input type="radio" name="ee" > 
    <input type="radio" name="ee" >
</form>
```

```javascript
document.forms['form'].elements
>>>>>>>
HTMLFormControlsCollection{
    0: <input id="" name="a" type="text">
    1: <input id="" name="b" type="password">
    2: <select id="" name="cc">
    3: <textarea name="dd">
    4: <input name="ee" type="radio">
    5: <input name="ee" type="radio">
    a: <input id="" name="a" type="text">
    b: <input id="" name="b" type="password">
    cc: <select id="" name="cc">
    dd: <textarea name="dd">
    ee: RadioNodeList [ input, input]
    length: 6
}
```

可以观察到如下几个特点：

1.  返回的是一个类数组对象，可以使用两种方法访问元素

    `form.elements[0]`或者`form.elements['cc']`

2.  单选控件在使用`form.elemnts['ee']`获取的时候，会返回一个`RadioNodeList`数组

#### 1.3.1 共有属性

除了`<fieldset>` ，都有共有的一组属性

-   `disabled`：布尔值，表示当前字段是否被禁用。
-   `form`：指向当前字段所属表单的指针；只读。
-   `name`：当前字段的名称。
-   `readOnly`：布尔值，表示当前字段是否只读。
-   `tabIndex`：表示当前字段的切换`（tab）`序号。
-   `type`：当前字段的类型，如`"checkbox"`、`"radio"`，等等。
-   `value`：当前字段将被提交给服务器的值。对文件字段来说，这个属性是只读的，包含着文在计算机中的路径

除了`form`属性外，其他的属性都可以使用`JS`修改

==注意==

​	不能在`click`事件中阻止用户重复点击，只能在`submit`中设置`disabled`。

​	因为不同的浏览器对于`click`和`submit`的触发先后顺序有不同的表现。

#### 1.3.2 共有方法

-   `focus()`
-   `blur()`

上面两个方法会在表单控件被隐藏的时候（`type=hidden\display\visibility`），发生错误

#### 1.3.3 共有事件

-   `blur`：
-   `change`：对于`<input>`和`<textarea>`元素，在它们失去焦点**且** `value` 值改变时触发；对于`<select>`元素，在其选项改变时触发
-   `focus`：
-   ==注意==`blur`和`change`事件没有固定的顺序发生

## 2. 文本框

主要有两种`<input>`和`<textarea>`

```html
<input type="text" size="25" maxlength="50" value="initial value">
<!--size:最大显示字符  maxlength：最大输入字符-->

<textarea rows="25" cols="5">initial value</textarea> 
```

```javascript
// 尽量使用如下方法读取和修改value
text.value;
text.value = 'xxx'
```

### 2.1 选择文本

这两种文本框都有选择文本方法`select()`，此方法不接受任何参数，效果是选中所有文本。==注意==只有在得到焦点的时候这个方法才会生效，在失去`blur`事件中启用这个方法是没有效果的。

这个方法主要是为了，在文本框包含默认值的时候，不必一个个删除文本。

#### 2.1.1 选择事件

选择事件的触发也会因浏览器而异，有的是鼠标还没释放的时候就触发，有的是要等待鼠标释放才触发

#### 2.1.2 取得选择的文本

文本框有两个新的属性，`selectionStart 和 selectionEnd`，无论从后往前选择文本还是从前往后选择文本，这两个值都是一样的。

```javascript
function getSelectedText(textbox){ 
    return textbox.value.substring(textbox.selectionStart, textbox.selectionEnd); 
} 
```

#### 2.1.3 选择部分文本

就是编程选择部分文本

`setSelectionRange()`方法接收两个参数，也是两个索引，和`substring`一样，**左闭右开**

```javascript
textbox.value = "Hello world!" 
//选择所有文本
textbox.setSelectionRange(0, textbox.value.length); //"Hello world!" 
//选择前 3 个字符
textbox.setSelectionRange(0, 3); //"Hel" 
//选择第 4 到第 6 个字符
textbox.setSelectionRange(4, 7); //"o w" 
// 必须在使用这个方法之前或者之后马上设置焦点
```

### 2.2 过滤输入

#### 2.2.1 屏蔽字符

可以通过阻止`keypress`的默认事件来阻止输入某些字符，判断用户输入的这个字符是不是符合规定，不符合就阻止默认事件。

可以屏蔽键码小于10的按键，这样可以避免非字符按键触发。这个方法还会阻止复制粘贴快捷键，所以需要排除`ctrl`键

#### 2.2.2 操作剪贴板

6个剪贴板事件

-   `beforecopy`：在发生复制操作前触发。
-   `copy`：在发生复制操作时触发。
-   `beforecut`：在发生剪切操作前触发。
-   `cut`：在发生剪切操作时触发。
-   `beforepaste`：在发生粘贴操作前触发。
-   `paste`：在发生粘贴操作时触发。

这里面的`before`系列事件，在不同浏览器里面不大一样，这些事件的事件对象有一个剪贴板对象`clipboardData`，这个对象有如下方法

-   `getData()`：
-   `setData()`：
-   `clearData()`：

这三个方法，只有第一个方法能在非`IE`上面使用，其他的只有`IE`上才能用。

`getData()`方法在`Firefox、Safari 和 Chrome`只允许在 `onpaste `事件中使用

### 2.3 自动切换焦点

比如输入电话号码的时候，输入完成区号后就应该自动将焦点切换到下一个输入框内。

```html
<input type="text" name="tel1" id="txtTel1" maxlength="3"> 
<input type="text" name="tel2" id="txtTel2" maxlength="3"> 
<input type="text" name="tel3" id="txtTel3" maxlength="4"> 
<!--
js中通过判断输入内容的长度是否等于maxlength属性值来切换焦点
-->
```

### 2.4 HTML5中的约束验证

除了`JS`验证数据的格式，`HTML5`中也有自带的一些验证约束

```html
<input type="text" name="username" required>
```

```javascript
document.forms[0].elements["username"].required; 
```

```html
<input type="number" min="0" max="100" step="5" name="count"> 
```

```javascript
// 在js中也能编程修改 如range条的数字
input.stepUp(); //加 1 
input.stepUp(5); //加 5 
input.stepDown(); //减 1 
input.stepDown(10); //减 10 
```

```html
<input type="text" pattern="\d+" name="count"> 
<!--一个正则表达式 去掉^和$-->
```

```javascript
// 如果前面的检测方式无效
// 使用checkValidity()就可以查出
input.checkValidity()
// 其中包含一系列属性 告诉你为什么无效
```

```html
<form method="post" action="signup.php" novalidate> 
 <!--这里插入表单元素--> 
</form> 
<!--novalidate 禁用验证-->
```

## 3. 选择框

选择框对象有如下属性和方法

-   `add(newOption, relOption)`：向控件中插入新`<option>`元素，其位置在相关项`（relOption）`之前。
-   `multiple`：布尔值，表示是否允许多项选择；等价于` HTML `中的` multiple `特性。
-   `options`：控件中所有`<option>`元素的 `HTMLCollection`。
-   `remove(index)`：移除给定位置的选项。
-   `selectedIndex`：基于` 0 `的选中项的索引，如果没有选中项，则值为`-1`。对于支持多选的控件，
    只保存选中项中第一项的索引。
-   `size`：选择框中可见的行数；等价于 `HTML `中的 `size` 特性。
-   `value`
    -   如果没有选中的项，则选择框的 `value` 属性保存空字符串。
    -   如果有一个选中项，而且该项的` value` 特性已经在 `HTML `中指定，则选择框的 `value` 属性等
        于选中项的 `value` 特性。即使` value` 特性的值是空字符串，也同样遵循此条规则。
    -   如果有一个选中项，但该项的` value` 特性在 `HTML` 中未指定，则选择框的 `value` 属性等于该
        项的文本。
    -   如果有多个选中项，则选择框的` value` 属性将依据前两条规则取得第一个选中项的值。

```html
<select name="location" id="selLocation"> 
    <option value="Sunnyvale, CA">Sunnyvale</option> 
    <option value="Los Angeles, CA">Los Angeles</option> 
    <option value="Mountain View, CA">Mountain View</option> 
    <option value="">China</option> 
    <option>Australia</option> 
</select> 
<!--
	选择第一项===>value="Sunnyvale, CA"。
	"China"项===>value=""
	最后一项====>value="Australia"
-->
```

而选项`option`对象也有下列属性

-   `index`：当前选项在 `options` 集合中的索引。
-   `label`：当前选项的标签；等价于 `HTML` 中的` label` 特性。
-   `selected`：布尔值，表示当前选项是否被选中。将这个属性设置为 `true` 可以选中当前选项。
-   `text`：选项的文本。
-   `value`：选项的值（等价于 `HTML` 中的 `value` 特性）。

```javascript
var selectbox = document.forms[0].elements["location"]; 
//不推荐
var text = selectbox.options[0].firstChild.nodeValue; //选项的文本
var value = selectbox.options[0].getAttribute("value"); //选项的值

//推荐
var text = selectbox.options[0].text; //选项的文本
var value = selectbox.options[0].value; //选项的值
```

### 3.1 选中选项

对于**只允许选择一项**的选择框

```javascript
var selectedOption = selectbox.options[selectbox.selectedIndex];
// 或者直接设置selected属性
selectbox.options[0].selected = true
```

对于**多选**，只返回第一个项的索引。

### 3.2 添加选项

除了使用`createElement`创建`option`标签，还能使用如下方法

```javascript
var newOption = new Option("Option text", "Option value"); 
selectbox.appendChild(newOption); //在 IE8 及之前版本中有问题

// add()方法
// 必须写两个参数 插入到最后
var newOption = new Option("Option text", "Option value"); 
selectbox.add(newOption, undefined); //最佳方案
```

### 3.3 移除选项

```javascript
// DOM
selectbox.removeChild(selectbox.options[0]); //移除第一个选项

// remove()方法
selectbox.remove(0); //移除第一个选项

// 选项设置为null
selectbox.options[0] = null; //移除第一个选项
```

### 3.4 移动和重排选项

每次重排之后，`index`属性会刷新，因为`NodeList`是动态的

```javascript
// 如果为 appendChild()方法传入一个文档中已有的元素，那么就会先从该元素的父节点中移除它
var selectbox1 = document.getElementById("selLocations1"); 
var selectbox2 = document.getElementById("selLocations2"); 
selectbox2.appendChild(selectbox1.options[0]); 

// 重排使用DOM插入方法
var optionToMove = selectbox.options[1]; 
selectbox.insertBefore(optionToMove, selectbox.options[optionToMove.index-1]); 
```

## 4. 表单序列化

**浏览器是怎样将数据发送给服务器的？**

1.  对表单的`name`和`value`进行`url`编码，并使用`&`分隔
2.  不发送被禁用的表单数据
3.  发送被勾选的复选框和单选，多选框多个选项单独发送
4.  不发送`reset`和`button`按钮
5.  单击提交按钮的时候，会发送提交按钮
6.  `<option>`的`value`或者当它没有`value`时，发送文本值

## 5. 富文本编辑

又称为 `WYSIWYG（What You See Is What You Get，所见即所得）`，本质就是在页面中嵌入一个包含空 `HTML` 页面的 `iframe`。

通过设置 `designMode `属性，这个空白的 `HTML `页面可以被编辑，而编辑对象则是该页面`<body>`元素的 HTML 代码。

`designMode `属性有两个可能的值：`"off"`（默认值）和`"on"`。在设置为`"on"`时，整个文档都会变得可以编辑。

```html
<!--建立一个空白文件blank.html 文档加载完成后设置designMode属性-->
<iframe name="richedit" style="height:600px;width:700px;" src="./blank.html"></iframe> 
<script type="text/javascript"> 
    window.onload = function () {
        frames["richedit"].document.designMode = "on"; 
    }
</script> 
```

### 5.1  `contenteditable`属性

可以给任何元素添加这个属性，就可以实现对它的编辑

### 5.2 编程操作富文本

主要使用`document..execCommand()`方法，这个方法传递 3 个参数：

-   要执行的命令名称
-   表示浏览器是否应该为当前命令提供用户界面的一个布尔值
-   执行命令，必须的一个值（如果不需要值，则传递 null）。
-   为了确保跨浏览器的兼容性，第二个参数应该始终设置为 false，因为 Firefox 会在该参数为 true 时抛出错误

支持得最多的指令

|    命令    | 值（第三个参数） |                   说明                    |
| :--------: | :--------------: | :---------------------------------------: |
| backcolor  |    颜色字符串    |             设置文档背景颜色              |
|    bold    |       null       |          将选择的文本转换为粗体           |
|    copy    |       null       |         将选择的文本复制到剪贴板          |
| createlink |     URL字符      | 将选择的文本转换成一个链接，指向指定的URL |
|    cut     |       null       |         将选择的文本剪切到剪贴板          |
|   delete   |       null       |              删除选择的文本               |
|  fontname  |      字体名      |        将选择的文本修改为指定字体         |
|  fontsize  |       1～7       |      将选择的文本修改为指定字体大小       |
| forecolor  |    颜色字符串    |       将选择的文本修改为指定的颜色        |

|     formatblock      | 要包围当前文本块的
`<br/>`HTML标签；如`<h1>` |            使用指定的HTML标签来格式化选择的文本块            |
|        indent        |                    null                     |                           缩进文本                           |
| inserthorizontalrule |                    null                     |                 在插入字符处插入一个<hr>元素                 |
|     insertimage      |                  图像的URL                  |                   在插入字符处插入一个图像                   |
|  insertorderedlist   |                    null                     |                 在插入字符处插入一个<ol>元素                 |
| insertunorderedlist  |                    null                     |                 在插入字符处插入一个<ul>元素                 |
|   insertparagraph    |                    null                     |                 在插入字符处插入一个<p>元素                  |
|        italic        |                    null                     |                    将选择的文本转换成斜体                    |
|    justifycenter     |                    null                     |                 将插入光标所在文本块居中对齐                 |
|     justifyleft      |                    null                     |                  将插入光标所在文本块左对齐                  |
|       outdent        |                    null                     |                     凸排文本（减少缩进）                     |
|        paste         |                    null                     |               将剪贴板中的文本粘贴到选择的文本               |
|     removeformat     |                    null                     | 移除插入光标所在文本块的块级格式。这是撤销formatblock
<br/>命令的操作 |
|      selectall       |                    null                     |                     选择文档中的所有文本                     |
|      underline       |                    null                     |                    为选择的文本添加下划线                    |
|        unlink        |                    null                     |         移除文本的链接。这是撤销createlink命令的操作         |

==注意==和复制粘贴有关的操作在各种浏览器里面不同，有的浏览器并不支持，但是仍然可以通过快捷键来操作。

```javascript
// 具体使用在框架和 contenteditable 有点区别
// 使用框架 先获取到当前文档对象也就是frames
frames["richedit"].document.execCommand("bold", false, null); 
// 直接设置contenteditable时
document.execCommand("bold", false, null); 
```

==注意==编辑后再页面中的代码呈现，也就是使用的标签，不同的浏览器可能有不同。

#### 5.2.1 相关方法

`queryCommandEnabled()`：判断是否可以执行某个命令

```javascript
var result = frames["richedit"].document.queryCommandEnabled("bold"); 
// 并不实际仍为可以执行相关命令
// 还可以要用来判断 选择的文字是否已经执行了相应的操作
var isBold = frames["richedit"].document.queryCommandState("bold"); 
```

` queryCommandValue()`：获取在使用`excCommand()`传入的值

```javascript
var fontSize = frames["richedit"].document.queryCommandValue("fontsize"); 
// 返回设置的字体大小
```

### 5.3 富文本选区

主要使用`getSelection()`方法，这个方法会返回一个`Selection`对象。

这个对象的属性没有多少有用信息，但是这个对象支持很多有用的方法

`addRange(range)`：将指定的 `DOM `范围添加到选区中。
`collapse(node, offset)`：将选区折叠到指定节点中的相应的文本偏移位置。
`collapseToEnd()`：将选区折叠到终点位置。
`collapseToStart()`：将选区折叠到起点位置。
`containsNode(node)`：确定指定的节点是否包含在选区中
`deleteFromDocument()`：从文档中删除选区中的文本，与`document.execCommand("delete", 
false, null)`命令的结果相同。
`extend(node, offset)`：通过将 `focusNode `和` focusOffset `移动到指定的值来扩展选区。
`getRangeAt(index)`：返回索引对应的选区中的 `DOM `范围。
`removeAllRanges()`：从选区中移除所有 `DOM` 范围。实际上，这样会移除选区，因为选区
至少要有一个范围。
`reomveRange(range)`：从选区中移除指定的 `DOM `范围。
`selectAllChildren(node)`：清除选区并选择指定节点的所有子节点
`toString()`：返回选区所包含的文本内容。

```javascript
// 给选中文本高亮。。直接使用backcolor也能相同的操作
var selection = frames["richedit"].getSelection(); 
//取得选择的文本
var selectedText = selection.toString(); 
//取得代表选区的范围
var range = selection.getRangeAt(0); 
//突出显示选择的文本
var span = frames["richedit"].document.createElement("span"); 
span.style.backgroundColor = "yellow"; 
range.surroundContents(span); 
```

---

# :inbox_tray:第十五章 Canvas绘图

---



---

# :x:第十六章 HTML5脚本编程

---

## 1. 跨文档消息传递

跨文档消息传送`（cross-document messaging）`，有时候简称为` XDM`，指的是在来自不同域的页面间
传递消息。例如，`www.wrox.com `域中的页面与位于一个内嵌框架中的 `p2p.wrox.com `域中的页面通信。

核心方法是` postMessage()`，两个参数信息字符串和开源域的规定

```javascript
//注意：所有支持 XDM 的浏览器也支持 iframe 的 contentWindow 属
var iframeWindow = document.getElementById("myframe").contentWindow; 
iframeWindow.postMessage("A secret", "http://www.wrox.com"); 
// 设置iframeWindow只能接受来自http://www.wrox.com域的消息
// 第二个参数设置为*表示可以接受任意域的消息
```

消息传输完成后会触发`window`的` message `事件，这个事件时==异步==的，这个事件的事件对象包含三个主要属性

-   `data`：作为 `postMessage()`第一个参数传入的字符串数据。
-   `origin`：发送消息的文档所在的域，例如`"http://www.wrox.com"`。
-   `source`：发送消息的文档的 `window `对象的代理。这个代理对象主要用于在发送上一条消息的窗口中调用 `postMessage()`方法。如果发送消息的窗口来自同一个域，那这个对象就是`window`。

```javascript
EventUtil.addHandler(window, "message", function(event){ 
    //确保发送消息的域是已知的域
    if (event.origin == "http://www.wrox.com"){ 
        //处理接收到的数据
        processMessage(event.data); 
        //可选：向来源窗口发送回执
        event.source.postMessage("Received!", "http://p2p.wrox")
 }); 
```

## 2. 原生拖放

并不是移动元素位置

### 2.1 拖放事件

拖放一个元素将依次触发以下事件==注意==：这三个事件都是**被拖动**元素上的

所有的元素都有放置事件，但是不是所有元素都能被当做放置目标

为了让目标元素可以被当做拖动目标，需要在每个事件中阻止默认事件

-   `dragstart` 
-   `drag`  类似`mousemove`
-   `dragend` 

拖放结束时会依次触发以下事件==注意==：这三个事件都是**拖动目标元**素上的

-   `dragenter `
-   `dragover`  类似`mouseover`
-   `dragleave` 或 `drop`

### 2.2 ` dataTransfer`对象

​	这个对象只能在拖放事件中使用，是拖放事件对象的一个属性。

-   `getData()`：一个参数数据格式`text\url`
-   `setData()`：第一个参数数据格式`text\url`第二个参数数据字符串，不手动设置值时，拖动文字默认数据是文字，拖动图片、地址的时候默认是`url`

```javascript
//设置和接收文本数据
event.dataTransfer.setData("text", "some text"); 
var text = event.dataTransfer.getData("text"); 

//设置和接收 URL 
event.dataTransfer.setData("URL", "http://www.wrox.com/"); 
var url = event.dataTransfer.getData("URL"); 
```

-   `dropEffect`：在`dragstart`中设置

    `"none"`：不能把拖动的元素放在这里。这是除文本框之外所有元素的默认值。
    `"move"`：应该把拖动的元素移动到放置目标。
    `"copy"`：应该把拖动的元素复制到放置目标。
    `"link"`：表示放置目标会打开拖动的元素（但拖动的元素必须是一个链接，有 URL）。

-   `effectAllowed`：在`dragenter`中设置

    ` "uninitialized"`：没有给被拖动的元素设置任何放置行为
    ` "none"`：被拖动的元素不能有任何行为。
     `"copy"`：只允许值为"copy"的 dropEffect。
    ` "link"`：只允许值为"link"的 dropEffect。
     `"move"`：只允许值为"move"的 dropEffect。
    ` "copyLink"`：允许值为"copy"和"link"的 dropEffect。
     `"copyMove"`：允许值为"copy"和"move"的 dropEffect。
     `"linkMove"`：允许值为"link"和"move"的 dropEffect。
     `"all"`：允许任意 dropEffect。

-   其他方法属性

    -   `setDragImage`：设置拖动时光标下方的图像

## 3. 媒体标签

## 4.历史状态管理

有的操作不会刷新页面，前进和后退按钮就会无效，解决这个问题可以通过`hashchange`事件，或者`history`对象管理历史状态

```javascript
// hashchange事件是监听#后面的变化
// history是主动更改地址栏，但是页面并不会跳转
// pushState参数 状态数据、新状态标题、【相对url】
history.pushState({name:"Nicholas"}, "Nicholas' page", "nicholas.html"); 

// 第一参数 第二参数随便怎么写
// 第三参数是一个相对路径，也就是会加载之前的url后面
// www.baidu.com/相对url
```

```javascript
// 当点击后退按钮的时候就会触发popstate事件
// popstate事件的事件对象包含一个属性state
// state包含之前push的数据


// 还需注意，state的值是包含的==当前==页面的值
// 也就是说点击后退后---->触发popstate----->回到之前的页面------>state返回当前页面的data（第一个页面为null）
```

```javascript
// 更新状态使用replacestate
// 接收两个参数也就是pushstate前两个参数
```

----

# :boxing_glove:第十七章  错误调试

---

## 1. try-catch语句

```javascript
try {
    // 可能出错的代码
} catch(error) {
    // 发生错误的处理
}
// 这个error错误对象 必写， 这个对象主要包含 message 和 name 属性
```

```javascript
try {
    return 1
} catch(error) {
    
} finally {
    return 2
    // 无论是否有错都会执行
    // try里面的return将不会执行，只会执行这里的
}
```

### 1.1 错误类型

-   `Error` ：其他错误类型都继承于这个
-   `EvalError` ：`eval()`没有被当成函数调用
-   `RangeError` ：超出数值范围
-   `ReferenceError` ：未声明变量
-   `SyntaxError` ：语法错误的字符串传入`eval()`
-   `TypeError` ：传递不合法类型值
-   `URIError` ：使用`encodeURI()`和`decodeURI()`时`URI`格式不正确

```javascript
try { 
    someFunction(); 
} catch (error){ 
    if (error instanceof TypeError){ 
        //处理类型错误
    } else if (error instanceof ReferenceError){ 
        //处理引用错误
    } else { 
        //处理其他类型的错误
    } 
} 
```

### 1.2 抛出错误

`throw`操作符后面可以接任何数据，执行到这里程序就停止了。

```javascript
// 可以这样自定义错误
throw new Error("Something bad happened."); 
throw new SyntaxError("I don’t like your syntax."); 
throw new TypeError("What type of variable do you take me for?"); 
throw new RangeError("Sorry, you just don’t have the range."); 
throw new EvalError("That doesn’t evaluate."); 
throw new URIError("Uri, is that you?"); 
throw new ReferenceError("You didn’t cite your references properly."); 
```

```javascript
// 通过继承error来创建自定义错误
function CustomError(message){  
    this.name = "CustomError";  
    this.message = message; 
} 
CustomError.prototype = new Error(); 
throw new CustomError("My message"); 
```

### 1.3 错误事件

`onerror`事件，不包含事件对象，但是有三个参数

```javascript
window.onerror = function(message, fileURL, lineNumber) {
    //1.错误消息2.文件地址3.错误行号
}

// 图像也能使用
// 加载失败会返回一个事件对象，代表加载失败的图片
var image = new Image(); 
EventUtil.addHandler(image, "load", function(event){ 
    alert("Image loaded!"); 
}); 
EventUtil.addHandler(image, "error", function(event){ 
    alert("Image not loaded!"); 
}); 
image.src = "smilex.gif"; //指定不存在的文件
```

### 1.4 通信错误

为了避免通信错误，应该给查询字符串进行编码，` encodeURIComponent()`

### 1.5 错误记录

```javascript
function logError(sev, msg){ 
    var img = new Image(); 
    img.src = "log.php?sev=" + encodeURIComponent(sev) + "&msg=" + 
        encodeURIComponent(msg); 
} 
// 通过发送一个图片请求，将错误信息当做查询字符串发出
// 使用图片对象可以兼容不支持XMLHttpRequest对象的浏览器

for (var i=0, len=mods.length; i < len; i++){ 
    try { 
        mods[i].init(); 
    } catch (ex){ 
        logError("nonfatal", "Module init failed: " + ex.message); 
    } 
} 
```

## 2. 调试技术

`console`对象

-   `error(message)`：将错误消息记录到控制台
-   `info(message)`：将信息性消息记录到控制台
-   `log(message)`：将一般消息记录到控制台
-   `warn(message)`：将警告消息记录到控制台

---

# :card_file_box: 第十八章  XML

---



---

# :zzz:第十九章 E4X

---



---

# :desert_island: 第二十章 JSON

---

## 1.JSON.stringify()

`JSON.stringify()`会忽略任何函数、原型成员、`undefined`。它接收另外两个参数

-   第二个参数：过滤器，一个数组或者一个函数
-   第三个参数：是否保留缩进

如果**过滤器**是一个数组，序列化的结果只包含数组中的属性

```javascript
var book = { 
    "title": "Professional JavaScript", 
    "authors": [ 
        "Nicholas C. Zakas" 
    ], 
    edition: 3, 
    year: 2011 
}; 
var jsonText = JSON.stringify(book, ["title", "edition"]); 
//-------------->
//{"title":"Professional JavaScript","edition":3} 
```

如果**过滤器**是一个函数

```javascript
var book = { 
    "title": "Professional JavaScript", 
    "authors": [ 
        "Nicholas C. Zakas" 
    ], 
    edition: 3, 
    year: 2011 
}; 
// 这个函数接收两个参数，key和value
var jsonText = JSON.stringify(book, function(key, value){ 
    switch(key){ 
        case "authors": 
            return value.join(",") 
        case "year": 
            return 5000; 
        case "edition": 
            return undefined; // 返回undefined代表忽略这个值
        default: 
            return value; 
    } 
}); 
//--------------------------->
// {"title":"Professional JavaScript","authors":"Nicholas C. Zakas","year":5000} 
```

**缩进**参数，如果写入值就代表缩进的空格数，同时会有换行

```javascript
var jsonText = JSON.stringify(book, null, 4); 
```

**缩进**参数值为字符串时，代表使用这个字符串填充缩进

```javascript
var jsonText = JSON.stringify(book, null, " - -"); 
//---------------->
{ 
--"title": "Professional JavaScript", 
--"authors": [ 
----"Nicholas C. Zakas" 
--], 
--"edition": 3, 
--"year": 2011 
} 
```

可以给对象添加`toJSON()`方法，当这个对象被序列化时，就会调用这个方法

```javascript
var book = { 
    "title": "Professional JavaScript", 
    "authors": [ 
        "Nicholas C. Zakas" 
    ], 
    edition: 3, 
    year: 2011, 
    toJSON: function(){ 
        return this.title; 
    } 
}; 
var jsonText = JSON.stringify(book); 
//--------------------->
// "Professional JavaScript"
```

## 2. JSON.parse()

同样接收另外一个参数，一个函数，参数也是`key`和`value`，表示对每个键值对进行操作，同样如果返回`undefined`表示删除这个键值对

```javascript
var book = { 
    "title": "Professional JavaScript", 
    "authors": [ 
        "Nicholas C. Zakas" 
    ], 
    edition: 3, 
    year: 2011, 
    releaseDate: new Date(2011, 11, 1) 
}; 
var jsonText = JSON.stringify(book); 
var bookCopy = JSON.parse(jsonText, function(key, value){ 
    if (key == "releaseDate"){ 
        // 如果不进行重新返回date对象
        // 那么releaseDate只是一个字符串
        // 下面就不能调用方法了
        return new Date(value); 
    } else { 
        return value; 
    } 
}); 
alert(bookCopy.releaseDate.getFullYear()); 
```

---

# :camping:第二十一章 AJAX与Comet

---

## 1.XMLHttpRequest对象

```javascript
let xhr = new XMLHttpRequest();
xhr.open('get', 'url');
xhr.onreadystatechange = function(){
    if(xhr.readyState === 4) {
        
    }
}
//post
// xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlcoded')
xhr.send()
```

### 1.1 xhr方法

-   `open()`

    接收三个参数，请求方法、请求地址、是否异步

-   `send()`

    接收一个参数，`post`要发送的数据

### 1.2 xhr属性

-   `responseText`：作为响应主体被返回的文本。

-   `responseXML`：如果响应的内容类型是"text/xml"或"application/xml"，这个属性中将保存包含着响应数据的 XML DOM 文档。

-   `status`：响应的 HTTP 状态。

-   `statusText`：HTTP 状态的说明。

-   `readyState `：

    0：未调用`open()` 

    1：未调用`send()` 

    2：未接收到响应

    3：接收到部分响应数据

    4：接收到全部数据

可以使用`xhr.abort()`方法取消异步请求

## 2. HTTP头部信息

-   `Accept`：浏览器能够处理的内容类型。
-   `Accept-Charset`：浏览器能够显示的字符集。
-   `Accept-Encoding`：浏览器能够处理的压缩编码。
-   `Accept-Language`：浏览器当前设置的语言。
-   `Connection`：浏览器与服务器之间连接的类型。
-   `Cookie`：当前页面设置的任何 `Cookie`。
-   `Host`：发出请求的页面所在的域 。
-   `Referer`：发出请求的页面的 `URI`。注意，`HTTP` 规范将这个头部字段拼写错了，而为保证与规范一致，也只能将错就错了。（这个英文单词的正确拼法应该是 `referrer`。）
-   `User-Agent`：浏览器的用户代理字符串。

### 2.1 GET

常用于向服务器查询信息，查询字符串最好都经过编码

```javascript
function addURLParam(url, name, value) { 
    url += (url.indexOf("?") == -1 ? "?" : "&"); 
    url += encodeURIComponent(name) + "=" + encodeURIComponent(value); 
    return url; 
} 
function addURLParam(url, name, value) { 
    url += (url.indexOf("?") == -1 ? "?" : "&"); 
    url += encodeURIComponent(name) + "=" + encodeURIComponent(value); 
    return url; 
} 
```

### 2.2 POST

通常发送应该被保存的数据

```javascript
// 模拟表单提交所以使用
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlcoded')
```

## 3. XMLHttpRequest 2级

下面的新成员有可能有兼容问题

### 3.1 FormData

一个表单序列化对象

```javascript
// 手动添加数据
let data = new FormData();
data.append("name", "value")
// 自动序列化数据
let data = new FormData(document.forms[0]);
```

### 3.2 超时设定

请求等待超时时间过后，就会终止。设置`timeout`属性后会触发`timeout`时间

```javascript
xhr.timeout = 1000; //将超时设置为 1 秒钟（仅适用于 IE8+）
xhr.ontimeout = function(){ 
    alert("Request did not return in a second."); 
}; 
```

### 3.3 进度事件

-   `loadstart`：在接收到响应数据的第一个字节时触发。
-   `progress`：在接收响应期间持续不断地触发。
-   `error`：在请求发生错误时触发。
-   `abort`：在因为调用 `abort()`方法而终止连接时触发。
-   `load`：在接收到完整的响应数据时触发。
-   `loadend`：在通信完成或者触发 `error、abort 或 load` 事件后触发

#### 3.3.1 load事件

接收完毕将触发`load`事件

#### 3.3.2 progress事件

事件对象包含三个属性：

-   `lengthComputable`：表示进度信息是否可用的布尔值
-   `position `：已经接收的字节数
-   ` totalSize`：根据`Content-Length` 响应头部确定的预期字节数

## 4. 跨域资源共享

主流浏览器中传入攫夺地址就行

```javascript
xhr.open("get", "http://www.somewhere-else.com/page/"); 
```

### 4.1其他跨域技术

#### 4.1.1 图像Ping

使用图像的`onload`和`onerror`事件来确定是否接收到了响应

只能`get`请求，不能得到请求数据。

只能用来跟踪用户点击或者广告曝光次数

```javascript
var img = new Image(); 
img.onload = img.onerror = function(){ 
    alert("Done!"); 
}; 
img.src = "http://www.example.com/test?name=Nicholas"; 
```

#### 4.1.2 JSONP

就是`JSON`数据被包含到一个函数中`callback({name:xxx,age:12})`，利用`scrip`标签`src`，动态创建一个标签，获取这个字符串，碰巧调用同一个方法。

```javascript
//JSONP-------------> handleResponse({name=xx, age=11})
//代码中
//同样定义一个这个回调函数
function handleResponse(data) {
    console.log(data)
}
document.creatElement('script').src = "/?call=hadleResponese"
document.body.insertBefore(script, document.body.firstChild); 
```

### 4.2 Comet

一种服务器向页面推送数据的技术，主要使用 **长沦轮询**和流

-   短轮询

![1540611071718](E:\培训\笔记图\1540611071718.png)

-   长轮询

![1540611084422](E:\培训\笔记图\1540611084422.png)

对于**流**就是浏览器向服务器发送一个请求，而服务器保持连接打开，然后周
期性地向浏览器发送数据

```javascript
function createStreamingClient(url, progress, finished){ 
    var xhr = new XMLHttpRequest(), 
        received = 0; 
    xhr.open("get", url, true); 
    xhr.onreadystatechange = function(){ 
        var result; 
        // 检测状态3
        if (xhr.readyState == 3){ 
            //只取得最新数据并调整计数器
            result = xhr.responseText.substring(received); 
            received += result.length; 
            //调用 progress 回调函数
            progress(result); 
        } else if (xhr.readyState == 4){ 
            finished(xhr.responseText); 
        } 
    }; 
    xhr.send(null); 
    return xhr; 
} 
var client = createStreamingClient("streaming.php", function(data) alert("Received: " + data); 
                                   }, function(data){ 
    alert("Done!"); 
}); 
```

### 4.3 Comet两个接口

#### 4.3.1 服务器发送时间

`SSE API`用于创建到服务器的单向连接，服务器通过这个连接可以发送任意数量的数据。

服务器响应的类型必须是`text/event-stream`，响应格式为纯文本，如

```javascript
data: foo
// e.data ----> foo
data: foo
data:bar
// e.data ----> foo\nbar

// 必须换行，才能触发message事件

// 还可以添加id
// 这样，EventSource 就知道上次请求的位置
```



```javascript
var source = new EventSource("myevents.php"); // url必须同源
source.readyState有三个值
0：正在连接服务器
1：打开了连接
2：关闭了连接
// 三个事件
open：建立连接触发
message：从服务器接收到新事件，数据存在e.data中
error：无法建立连接
source.onmessage = function(event){ 
  var data = event.data; 
  //处理数据
}; 

// 关闭连接
source.close()
```

==适合==长轮询和`HTTP`流

#### 4.3.2 Web Sockets

会建立一个自定义的协议，由`http`转为`ws`，`https`转为`wss`。

```javascript
// 不受同源策略限制，必须传入绝对url
var socket = new WebSocket("ws://www.example.com/server.php"); 

// 同样有readyState属性
WebSocket.OPENING (0)：正在建立连接。
WebSocket.OPEN (1)：已经建立连接。
WebSocket.CLOSING (2)：正在关闭连接。
WebSket.CLOSE (3)：已经关闭

// 发送数据 只能发送文本
socket.send("Hello world!");
socket.send(JSON.stringify(data));

// 触发事件，接收数据
// 事件定义只能这样定义 不能使用addEventlistener
socket.onmessage = function(event){ 
 var data = event.data; 
 //处理数据
}; 

// 关闭连接
socket.close()


// 其他事件
open：在成功建立连接时触发。
error：在发生错误时触发，连接不能持续。
close：在连接关闭时触发。
```

#### 4.3.3 如何选择

1.  服务器是否支持`WebStockets`
2.  是否需要全双工通讯

### 4.4 安全

`CSRF`跨站点请求伪造：未经授权访问某个资源

-   使用`SSL`连接来访问资源
-   每次请求都要附带验证码

---

# :black_joker:第二十二章  高级技巧

---

## 1. 函数高级使用

### 1.1 安全的类型检测

传统的类型检测如：

```javascript
// typeof甚至会把正则表达式标记为function
// instanceof在存在多个frame也就是多个全局作用域时
// 会认为不是同一个对象的实例
var isArray = value instanceof Array; 
// 如果value是另外一个frame的数组，这里就会返回false
```

```javascript
// 区分原生对象和自定义对象 可以通过对象的toString()方法返回一个[object NativeConstructorName]
alert(Object.prototype.toString.call(value)); //"[object Array]" 
```

由上面的方法就可以区分原生和自定义的对象，自定义的对象返回`[object Object]`

### 1.2 作用域安全的构造函数

```javascript
function Person(name, age, job){ 
    this.name = name; 
    this.age = age; 
    this.job = job; 
} 
```

上面的构造函数有个问题，当直接调用这个构造函数的时候会意外的给全局对象`window`增加这三个属性。

使用下面的代码判断作用环境，确定新建的对象`this`指向的是`person`

```javascript
function Person(name, age, job){ 
    // 如果this指向的对象是Person的实例
    if (this instanceof Person){ 
        this.name = name; 
        this.age = age; 
        this.job = job; 
    } else { 
        return new Person(name, age, job); 
    } 
} 
var person1 = Person("Nicholas", 29, "Software Engineer"); 
alert(window.name); //"" 
alert(person1.name); //"Nicholas" 

var person2 = new Person("Shelby", 34, "Ergonomist"); 
alert(person2.name); //"Shelby" 
```

要结合`new`的过程在`第六章2.2`中有写，在将属性赋给新对象前，`this`已经指向了新对象。所以`person2`不会进入`else`。

==但是==如果使用某些方法继承的时候就会出现问题，因为继承是想要`this`指向继承元素的，但是因为构造函数的作用域时安全的，所以`this`让然指向的是构造函数，无法实现继承。

```javascript
// 一个作用域安全的构造函数
function Polygon(sides){ 
    if (this instanceof Polygon) { 
        this.sides = sides; 
        this.getArea = function(){ 
            return 0; 
        }; 
    } else { 
        return new Polygon(sides); 
    } 
} 
function Rectangle(width, height){ 
    // 打算将构造函数继承过来
    // 但是由于this不同 进入else返回的仍然是Polygon的实例
    // 所以Rectangle的this没有sides属性
    Polygon.call(this, 2); 
    this.width = width; 
    this.height = height; 
    this.getArea = function(){ 
        return this.width * this.height; 
    }; 
} 
var rect = new Rectangle(5, 10); 
alert(rect.sides); //undefined 
```

解决办法：

```javascript
function Polygon(sides){ 
    if (this instanceof Polygon) { 
        this.sides = sides; 
        this.getArea = function(){ 
            return 0; 
        }; 
    } else { 
        return new Polygon(sides); 
    } 
} 
function Rectangle(width, height){ 
    Polygon.call(this, 2); 
    this.width = width; 
    this.height = height; 
    this.getArea = function(){ 
        return this.width * this.height; 
    }; 
} 
// 增加这句
// 使用原型链继承
Rectangle.prototype = new Polygon(); 
var rect = new Rectangle(5, 10); 
alert(rect.sides); //2 
```

### 1.3 惰性载入函数

函数中会存在很多条件分支，但是执行判断会损失性能，这就需要惰性载入函数。

-   第一种方法，判断成功进入后将方法覆写该函数

    ```javascript
    function isGreatThanTen(num) {
        // 先判断
        if(num > 10) {
            // 覆盖这个函数
            // 不会执行其他判断(这里没写多个判断)
            function isGreatThanTen() {
                console.log('>10')
            }
        }else {
            function isGreatThanTen() {
                console.log('<10')
            }
        }
        // 返回调用
        return isGreatThanTen(num)
    }
    isGreatThanTen(1) //--->"<10"
    isGreatThanTen(90) //--->">10"
    ```

-   方法二，声明函数的时候就覆写好函数，就是执行一个立即执行函数

    ```javascript
    let res = (function() {
        if(){
           	return function(){}
    	}else if() {
        	return function(){}
    	}else {
        	return function(){}
    	}
    })()
    ```

    ### 1.4 函数绑定

    问题：下面代码并不能正确输出`message`，因为`this`变成了这个按钮

    ```javascript
    var handler = {
        message: "Event handled",
        handleClick: function (event) {
            console.log(this)
            alert(this.message);
        }
    };
    var btn = document.getElementById("my-btn"); 
    btn.onclick = handler.handleClick;//------->undefined
    ```

    解决思路：

    1.  主要是`this`指向的问题
    2.  `this`指向==最后==调用这个函数的对象
    3.  如上面代码，最后调用函数（调用函数就是在函数名后面加了()）的是按钮对象。
    4.  所以只要保证最后打上括号的对象是`handler`就可以了，形如 ` handler.handleClick()`
    5.  而直接写`onclick=fn()`网页一加载就会执行，所以在外面包裹一层匿名函数。

    解决方法一：使用闭包

    ```javascript
    btn.onclick = function(e) {
        handler.handleClick()
    }
    // 这样最后调用handleClick
    ```

    解决方法二：使用`bind()`

    ```javascript
    // 使用原生方法bind()
    // 返回一个绑定了指定上下文的函数
    btn.onclick = handler.handleClick.bind(handler)
    
    // 手动创建bind()方法
    // 这个方法和解决方法一原理一样
    function bind(fn, context) {
        return function () {
            return fn.apply(context, arguments);
        };
    } 
    ```

    ==手动创建==：

    1.  思想和解决方法一是一样的，因为不能直接运行，所以使用匿名函数包裹，返回的就只是一个函数声明。
    2.  当点击事件发生，匿名函数执行，匿名函数返回`fn`执行结果
    3.  `fn`已经通过`apply`将执行环境换成了`context`所以能够正常执行

### 1.5 函数柯里化

创建一个已经设置好一个或者多个参数的函数。

```javascript
// 柯里化函数
function curry(fn) {
    // 这里的arguments是配置时产生的
    var args = Array.prototype.slice.call(arguments, 1);
    return function () {
        // 这里的arguments是未来使用时加入的参数
        var innerArgs = Array.prototype.slice.call(arguments);
        var finalArgs = args.concat(innerArgs);
        return fn.apply(null, finalArgs);
        // return fn(...finalArgs)
    };
}
function add(num1, num2) {
    return num1 + num2;
}
// 将add函数柯里化
var curriedAdd = curry(add, 5);
console.log(curriedAdd(3)); //8 
```

上面的代码：

1.  `curry`函数返回一个匿名函数，接收任意多参数。这个匿名函数返回被柯里化的函数调用结果。
2.  特别注意两个`arguments`是使用，因为要固定`fn`的一个或者多个参数，所以第一次使用`slice`分割了参数，将传递进来的`fn`去除只保留参数。第二次使用`slice`将调用柯里化后函数的参数保存，最后将固定的参数和新的参数合并传入`fn`
3.  这里使用`apply`调用函数是因为参数是数组，而这个方法支持传递数组，也可以使用注释的扩展符号，将参数传入

`bind()`方法也能实现柯里化

```javascript
 handler.handleClick.bind(handler, "my-btn");
```

## 2. 防篡改对象

一旦把对象定义为防篡改，就无法撤销了。

### 2.1 不可扩展对象

任何对象默认都是可扩展的，随时可以添加属性和方法，要想阻止扩展，可以手动配置`第六章的对象属性`即可，也可以如下使用新方法。

```javascript
let person = {
    name:"xx",
    age:111
}
Object.preventExtensions(person);// 阻止扩展
person.son = 'oo';
console.log(person.son)//=>undefined

//Object.istExtensible()是否可扩展
```

### 2.2 密封对象

比不可扩展对象还要严格，不仅==不能扩展==而且还==不能删除==自己本来有的==属性和方法==，相当于`[[Configurable]]`被设置为`false`。但是==修改==属性和方法是可以的

```javascript
var person = { 
    name: "Nicholas",
    get() {
        console.log(name)
    }
}; 
Object.seal(person); 

person.age = 29; 
console.log(person.age); //undefined 

delete person.name; 
console.log(person.name); //"Nicholas" 
person.name = 'XXXX';
console.log(person.name);// "XXXX"

delete person.get;
console.log(person.get)
person.get = function () { alert(this.name)}
person.get() // √

// Object.isSealed()
```

### 2.3 冻结对象

最严格的防篡改级别。`[[Writable]]`被设置为`false`。如果定义了`[[Set]]`函数，访问器属性仍然是可写的

```javascript
var person = { name: "Nicholas" }; 
Object.freeze(person); // 冻结对象
person.age = 29; 
alert(person.age); //undefined 
delete person.name; 
alert(person.name); //"Nicholas" 
person.name = "Greg"; 
alert(person.name); //"Nicholas" 
// Object.isFrozen()是否冻结
```

## 3 高级定时器

首先理解`JavaScript`的进程时间线

1.  页面载入时，首先执行`<script>`中的代码（一些简单的函数和变量声明）
2.  进入线程空闲
3.  如果有新任务要执行，就可以立即执行

![1540711658836](E:\培训\笔记图\1540711658836.png)

除了正在执行的任务，还有一个下次空闲时执行代码的队列。

==JS中没有一个代码是立即执行的，都是等待进程空闲尽快执行==

定时器的原理就是，等待设定的时间后将代码插入，并且**只能保证尽快**执行。

### 3.1 重复定时器

使用`setInterval`问题有两个

-   某些间隔会被跳过
-   多个定时器的代码执行之间的间隔可能会比预期的小

![1540712789197](E:\培训\笔记图\1540712789197.png)

如上图，在一个`click`事件中设置定时器，时间为200毫秒

1.  触发`click`事件，需要执行300毫秒，所以现在无法执行其他代码。

    但是因为定时器间隔为200，所以在事件函数还没结束的时候就添加了定时器代码，显然不会执行

2.  因为定时器每个200毫秒就添加了一段代码，所以到了405的时候才得以执行。

解决这个问题可以链式调用`setTimeout()`

```javascript
setTimeout(function () {
    var div = document.getElementById("myDiv");
    left = parseInt(div.style.left) + 5;
    div.style.left = left + "px";
    // 只有上面的代码运行完成了，才会开启另外一个定时器
    if (left < 200) {
        setTimeout(arguments.callee, 50);
    }
}, 50); 
```

### 3.2 Yielding Processes 

由于浏览器对`JS`做了很多限制，比如运行时间限制，不能长时间运行代码。

如果

-   代码运行不需要同步
-   代码运行不考虑顺序

那么可以这样

```javascript
setTimeout(function(){ 
    //取出下一个条目并处理
    var item = array.shift(); 
    process(item); 
    //若还有条目，再设置另一个定时器
    if(array.length > 0){ 
        setTimeout(arguments.callee, 100); 
    } 
}, 100); 
```

### 3.3 函数节流

比如`onresize`事件会在改变浏览器大小的时候持续触发，这样会增加处理器的使用率和负担，所以需要对函数进行**节流**

```javascript
var processor = { 
    timeoutId: null, 
    //实际进行处理的方法
    performProcessing: function(){ 
        //实际执行的代码
    }, 
    //初始处理调用的方法
    process: function(){ 
     	// ****每次都清除前一次的调用
        clearTimeout(this.timeoutId); 
        var that = this; // 因为定时器this为window所以先保存
        // ****保证每次的调用都只会在100ms后才会实施
        this.timeoutId = setTimeout(function(){ 
            that.performProcessing(); 
        }, 100); 
    } 
}; 
//尝试开始执行 
processor.process(); 

// 简单版
function throttle(method, context) { 
    clearTimeout(method.tId); 
    method.tId= setTimeout(function(){ 
        method.call(context); 
    }, 100); 
} 
```

### 3.4 自定义事件（观察者模式）

```javascript
//EventTarget构造函数
function EventTarget(){ 
    this.handlers = {}; 
} 
// EventTarget定义属性方法
EventTarget.prototype = { 
    constructor: EventTarget, 
    // 订阅
    addHandler: function(type, handler){ 
        if (typeof this.handlers[type] == "undefined"){ 
            this.handlers[type] = []; 
        } 
        // 添加属性type，是一个数组存放该类型的执行函数
        this.handlers[type].push(handler); 
    }, 
    // 执行
    fire: function(event){ 
        if (!event.target){ 
            // 保存调用对象
            event.target = this; 
        } 
        if (this.handlers[event.type] instanceof Array){ 
            // 取得该type的方法数组
            var handlers = this.handlers[event.type]; 
            for (var i=0, len=handlers.length; i < len; i++){ 
                // 调用所有方法
                handlers[i](event); 
            } 
        } 
    },
    // 取消
    removeHandler: function(type, handler){ 
        if (this.handlers[type] instanceof Array){ 
            var handlers = this.handlers[type]; 
            for (var i=0, len=handlers.length; i < len; i++){ 
                // 找到查找的函数 因为var没有块级作用域
                // 直接break 在外面删除这个函数
                if (handlers[i] === handler){ 
                    break; 
                } 
            } 
            handlers.splice(i, 1); 
        } 
    } 
}; 
```

使用方法

```javascript
function handleMessage(event){ 
    alert("Message received: " + event.message); 
} 
//创建一个新对象
var target = new EventTarget(); 
//添加一个事件处理程序
target.addHandler("message", handleMessage); 
//触发事件
target.fire({ type: "message", message: "Hello world!"}); 
//删除事件处理程序
target.removeHandler("message", handleMessage); 
//再次，应没有处理程序
target.fire({ type: "message", message: "Hello world!"}); 

// 多个对象都要使用一套观察者的话，可以使用单例模式创建观察者对象，在每一种对象上面都new一个观察者对象，这样他们都使用同一个对象，也就共享同一个事件调度

//在prototype中增加
// EventTarget.prototype.getInstance()
getInstance() {
    // 如果没有实例化过
    if(!this.instance){
        this.instance = new EventTarget(...arguments);
    }
    return this.instance
}
```

常见的使用方法

```javascript
function Person(name, age){ 
    // 寄生组合继承EventTarget
    EventTarget.call(this); 
    this.name = name; 
    this.age = age; 
} 
inheritPrototype(Person,EventTarget); 
Person.prototype.say = function(message){ 
    this.fire({type: "message", message: message}); 
}; 

function handleMessage(event){ 
    alert(event.target.name + " says: " + event.messag
} 
//创建新 person 
var person = new Person("Nicholas", 29); 
//添加一个事件处理程序
person.addHandler("message", handleMessage); 
//在该对象上调用 1 个方法，它触发消息事
person.say("Hi there."); 
```

## 4. 拖放

---

# :beach_umbrella:第二十三章  离线应用与客户端存储

---

## 1. 离线检测

`navigator.onLine`属性返回一个布尔值，判断设备是否离线。

还有两个相关的事件，`online`和`offline`，这两个事件在`window`上触发

## 2. 应用缓存（appcache）

需要在`html`标签的`manifest`属性中设置，与描述文件（`manifest file`）对应

```javascript
CACHE MANIFEST 
#Comment 
file.js  // 写出你想缓存的文件
file.css 
```

在`html`中

```html
<html manifest="/offline.manifest"> 
```

## 3. 数据存储

### 3.1 Cookie

**请求头**包含`Cookie` ，要求服务器的**响应头**应该包含`Set-Cookie`。

```javascript
// 响应头
HTTP/1.1 200 OK 
Content-type: text/html 
Set-Cookie: name=value 
Other-header: other-header-value 
// 请求头
GET /index.html HTTP/1.1 
Cookie: name=value 
Other-header: other-header-value 
```

#### 3.1.1 限制

-   限制访问的域名

    当设定了`Cookie`后，再向创建它的域名发送请求时，都会包含这个`Cookie`。这样就能限制可以访问`Cookie`的域名。

-   限制数量

    每个浏览器的规则不同，20~50个

-   大小限制

    最好在4095B以内，注意是所有的`Cookie`加起来不能超过这个限制

#### 3.1.2 `cookie`的组成

-   名称

    唯一的名称，虽然不区分大小写，但是有的服务器会区分，所以最好是区分大小写。名称必须经过`URL` 编码

-   值

    存储的字符串值。必须被编码

-   域`domain`

    `cookie`对于哪个域是有效的。所有向这个域发送的请求都会包含这个`cookie`。可以是这样`www.baidu.com\baidu.com`，如果是后者，那么所有子域都有效。

-   路径 `path`

    指定域中的哪个路径可以发送`cookie`，`www.baidu.com/books`可以发送而`www.baidu.com`就不能发送。

-   失效时间`expires`

    表示何时会被删除，默认情况网页关闭时就会删除，设定时间格式是`（Wdy, DD-Mon-YYYY HH:MM:SS GMT）`，如果设置的是以前的时间，`cookie`会被立即删除。

-   安全标志 ` secure `

    指定了以后，`cookie`只会在使用`SSL`连接时，才会发送，也就是`https`才会发送

以上信息发送的方式如下，注意是响应头，是浏览器发送给浏览器的。

```java
// 响应头
HTTP/1.1 200 OK 
Content-type: text/html 
Set-Cookie: name=value; name=value; expires=Mon, 22-Jan-07 07:10:24 GMT; domain=.wrox.com // 都放在这里 用分号+空格 分割每个数据
Other-header: other-header-value 
```

真正发送的内容**只有名值对**，其他的只能是给浏览器的配置参数。

#### 3.1.3 JS中的cookie

`document.cookie`：

-   当用于获取属性值时

    ```javascript
    console.log(document.cookie) //=> name1=value1;name2=value2;name3=value3 
    // 返回当前页面，所有可用的cookie键值对
    ```

-   当用于设置新的值时

    ```javascript
    document.cookie = "name=value; expires=expiration_time; path=domain_path; domain=domain_name; secure "
    // 设置的域必须是当前页面的，不然设置不成功
    // 时间可以这样设置 new Date('2018-10-31')
    document.cookie = "name=Nicholas"; // 创建一个路径和域都是当前页面的cookie
    // 最好都编码
    document.cookie = encodeURIComponent("name") + "=" + 
      encodeURIComponent("Nicholas"); 
    ```

封装`cookie`函数

```javascript
var CookieUtil = { 
    // 获取
    get: function (name){ 
        var cookieName = encodeURIComponent(name) + "=", 
            cookieStart = document.cookie.indexOf(cookieName), // 找到第一个匹配的name索引
            cookieValue = null; 
        if (cookieStart > -1){ 
            var cookieEnd = document.cookie.indexOf(";", cookieStart); // 从cookieStart处开始查找；
            if (cookieEnd == -1){ 
                cookieEnd = document.cookie.length; // 没有分号证明只有一个数据，直接返回全部
            } 
            cookieValue = decodeURIComponent(document.cookie.substring(cookieStart 
                                                                       + cookieName.length, cookieEnd)); // 分割出值，就是除去名称部分
        } 
        return cookieValue; 
    }, 
    // 设置
    set: function (name, value, expires, path, domain, secure) { 
        var cookieText = encodeURIComponent(name) + "=" + 
            encodeURIComponent(value); 
        if (expires instanceof Date) { 
            cookieText += "; expires=" + expires.toGMTString(); 
        } 
        if (path) { 
            cookieText += "; path=" + path; 
        } 
        if (domain) { 
            cookieText += "; domain=" + domain; 
        } 
        if (secure) { 
            cookieText += "; secure"; 
        } 
        document.cookie = cookieText; 
    }, 
    // 删除
    unset: function (name, path, domain, secure){ 
        this.set(name, "", new Date(0), path, domain, secure); // 设置成以前的日期
    } 
}; 
```

#### 3.1.4 子cookie

使用`cookie`值来存储多个键值对，按照查询字符串的格式

`name=name1=value1&name2=value2&....`

#### 3.1.5 其他

还有一类`cookie`是`HTTP`才有的，`JS`无法读取。

不要存储敏感信息

体积大的`cookie`对性能有影响

### 3.2  web存储机制

主要目的

-   提供`cookie`之外的存储方法
-   提供可以储存大量数据的机制

#### 3.2.1 Storage类型

这个类型提供最大的存储空间，**存储键值对**，下面的所有类型都是继承于此。

方法（可以直接调用）：

-   `clear()`：删除所有的值
-   `getItem(name)`：根据指定名字获取值
-   `key(index)`：`index`处的名字
-   `removeItem(name)`：移除指定名字的值
-   `setItem(name, value)`：添加新的值

#### 3.2.2 sessionStorage 对象

存储在`session`的数据会在**网页关闭时清除**。

而且，就算页面**刷新**，数据也在（因为刷新，并没有关闭网页会话）。

`sessionStorage `**绑定于某个服务器**的，所以本地运行是不可用的。

```javascript
//使用方法存储数据
sessionStorage.setItem("name", "Nicholas"); 
//使用属性存储数据 (不建议这样，容易意外改变其他对象的值)
sessionStorage.book = "Professional JavaScript"; 

//使用方法读取数据
var name = sessionStorage.getItem("name"); 
//使用属性读取数据
var book = sessionStorage.book; 

//使用方法删除一个值
sessionStorage.removeItem("book"); 
//使用 delete 删除一个值——在 WebKit 中无效
delete sessionStorage.name; 
```

如果需要跨越页面（跨越会话）操作存储，就需要`globalStorage `或者`localStorage `

#### 3.2.3 globalStorage 对象

被遗弃

#### 3.2.4 localStorage 对象

要访问同一个 `localStorage `对象：

-   页面必须来自**同一个域名**（子域名无效）
-   使用**同一种协议**
-   在**同一个端口上**

```javascript
// 操作方法和其他都一样
```

#### 3.2.5 storage 事件

对` Storage `（意思就是包括session、local）做任何修改都会触发这个事件（发生在`doucument`上）。

`event`对象包含如下属性：

-   `domain`：域
-   `key`：键名
-   `newValue`：新值
-   `oldValue`：更改之前的值

没有测试成功、。。。。

#### 3.2.6 限制

2.5MB~5MB之间

### 3.3 indexedDB

取代`WebSQL`，操作是完全**异步**的。

#### 3.3.1 数据库

最大的特点是使用对象来保存数据。

几乎每一个操作都会返回一个对象，用来指定`success\error`时的事件处理。

```javascript
var request, database; 
// 使用open打开或者创建一个数据库，第二个参数设置版本号只能为整数
// 返回一个个IDBRequest 对象
// 这个对象可以注册事件处理程序
request = indexedDB.open("admin", '2.0'); 
request.onerror = function(event){ 
    alert("Something bad happened while trying to open: " + 
          event.target.errorCode); 
    // event.target就是request对象
}; 
request.onsuccess = function(event){ 
    // 在成功事件里面event.target.result
    // 返回的是数据库实例对象
    database = request.result; 
}; 

consloe.log(database)//=>undefined
// 因为操作是异步的所以在这里打印是没有值的
```

`errorCode`有如下：

-   `IDBDatabaseException.UNKNOWN_ERR(1)`：意外错误，无法归类。
-   `IDBDatabaseException.NON_TRANSIENT_ERR(2)`：操作不合法。
-   `IDBDatabaseException.NOT_FOUND_ERR(3)`：未发现要操作的数据库。
-   `IDBDatabaseException.CONSTRAINT_ERR(4)`：违反了数据库约束。
-   `IDBDatabaseException.DATA_ERR(5)`：提供给事务的数据不能满足要求。
-   `IDBDatabaseException.NOT_ALLOWED_ERR(6)`：操作不合法。
-   `IDBDatabaseException.TRANSACTION_INACTIVE_ERR(7)`：试图重用已完成的事务。
-   `IDBDatabaseException.ABORT_ERR(8)`：请求中断，未成功。
-   `IDBDatabaseException.READ_ONLY_ERR(9)`：试图在只读模式下写入或修改数据。
-   `IDBDatabaseException.TIMEOUT_ERR(10)`：在有效时间内未完成操作。
-   `IDBDatabaseException.QUOTA_ERR(11)`：磁盘空间不足。

设置版本号`setVersion()`方法，被移到了`open`上，**只能升级不能降级**

#### 3.3.2 对象存储空间（object storge）

```javascript
var store = db.createObjectStore("users", { keyPath: "username" }); 
// 第一个参数表示“表”名称
// 第二个参数表示用数据里的username来当数据库查询的key
```

==注意==：操作数据库全放入了`onupgradeneeded`事件中，**这个事件只会在版本更新，也就是新创建数据库时触发**

```javascript
var request, database; 
request = indexedDB.open("admin", '2.0'); 
request.onerror = function(event){ 
    alert("Something bad happened while trying to open: " + 
          event.target.error.message); 
}; 
request.onsuccess = function(event){ 
    
}; 
// onupgradeneeded在这个事件里面可以操作数据库
//  event.target同样返回request对象
request.onupgradeneeded = function(e) {
    // createObjectStore返回一个store对象
   let store = request.result.createObjectStore("users", { keyPath: "username" });
   let  i = 0,
        request,
        requests = [],
        len = users.length;
    while (i < len) {
        // 添加数据
        // add()同名会报错 put()更新同名
        request = store.add(users[i++]);
        request.onerror = function () {
            //处理错误
        };
        request.onsuccess = function () {
            //处理成功
        };
        requests.push(request);
    }
}
```

由于火狐不能再本地浏览数据，只能在谷歌浏览器上测试。

![1540801112099](E:\培训\笔记图\1540801112099.png)

![1540801059143](E:\培训\笔记图\1540801059143.png)

第一张图：

​	`createObjectStore`的第一个参数创建了一个`users`表

第二张图：

​	`createObjectStore`的第二个参数，将数据中`username`当做了这里的`key`

#### 3.3.3 事务

当前面创建好初始数据库了后，就可以通过事务来操作数据库了。

```javascript
// 只能在创建数据库的那个onsuccess事件中创建事务对象
request.onsuccess = function () {
    database = request.result;
    let transaction = database.transaction('users2', 'readwrite'); 
};
// 第一个参数是要操作的表名字可以有多个['1', '2'....]  也就是request.result.createObjectStore("users", { keyPath: "username" });创建的这个users
// 第二个参数 readwrite、readonly现在只有这两个参数了
```

操作数据库同样可以使用`add()/put()增加` `delete()删除` `clear()清空` `get()访问`，这几个方法都会返回一个`request`对象，同样可以注册成功失败事件

```javascript
request.onsuccess = function (event) {
    database = request.result;
    // 创建事务对象
    transaction = database.transaction('users', 'readwrite');
    // 获取key为“aaa”的对象数据
    // transaction.objectStore("users")获取对象存储空间
    request = transaction.objectStore("users").get("aaa");
    // 事件
    request.onerror = ()=>{alert('getError')};
    request.onsuccess = (e)=>{console.log(e.target.result)}
};
```

由于一个`transaction`对象可以执行多个事务，所以也有自己的事件`onerror\  oncomplete `

#### 3.3.4 游标查询

前面可以使用`get()`方法查询某一个对象，但是需要查询多个对象的时候，就需要使用游标。

游标就是一**指向结果集的指针**。

与传统数据库查询不同，游标并不提前收集结果。游标指针会先指向结果中的第一项，在接到查找下一项的指令时，才会指向下一项。**也就是说游标只会返回当前指向的结果**。

```javascript
let store = transaction.objectStore("users");// 获取对象存储空间（前面小节已说明
request = store.openCursor();// 创建游标 // 同样返回一个request对象
request.onerror = ()=>{alert('openCursorError')};
request.onsuccess = (e)=>{console.log(e.target.result)}// 默认返回第一个数据
```

==注意==：

>   在此时的成功事件中的`e.target.result`，在有下一项时返回一个游标对象，没有下一项时返回`null`。

游标对象有如下属性：

-   `direction`：默认是`next`，还有`NEXT_NO_DUPLICATE`下一项不重复，`prev`前一项，`PREV_NO_DUPLICATE`前一项不重复
-   `key`：对象的键
-   `primaryKey`：游标使用的键。可能是对象键，也可能是索引键（稍后讨论索引键）
-   `source`：这个对象所属的对象存储空间
-   `value`：游标指向的对象值

```javascript
// 创建游标请求（request）的成功事件中
request.onsuccess = (e)=>{
    let cursor = e.target.result;
    if(cursor) {// 必须检查，以防下一项为空
        let value = cursor.value// 获取当前数据
        value.pwd = 'abc'
        request = cursor.update(value);// 更新数据库数据
        request.onsuccess = e=>console.log(e.target.result)
    }
}
```

==注意==：

当游标更新的对象属性，名称和之前设置的`keypath`一样时，会发生错误

```javascript
request.onsuccess = (e)=>{
    let cursor = e.target.result;
    if(cursor) {// 必须检查，以防下一项为空
        let value = cursor.value
        value.username = 'abc'// 之前设置的keypath也是username
        request = cursor.update(value);
        // request = cursor.delete(); 删除当前数据
        request.onsuccess = e=>console.log(e.target.result)
    }
}
// 会报错
// Failed to execute 'update' on 'IDBCursor': The effective object store of this cursor uses in-line keys and evaluating the key path of the value parameter results in a different value than the cursor's effective key
// 因为如果修改了值里面的username 那么数据库根据键寻找数据的时候就会失败
```

默认情况下，一个游标只能对当前数据发起请求，想要移动目标使用下面方法：

-   `continue`：一个参数`key`，调到指定位置。不传参默认下一个数据
-   `advance`：一个参数`count`，向下面移动`count`个数据

```javascript
// 游标创建请求对象request = store.openCursor()
request.onsuccess = (e)=>{
    let cursor = e.target.result;
    if(cursor) {// 必须检查，以防下一项为空
        let value = cursor.value
        request = cursor.update(value);
        cursor.advance(2) // 向下移动两个数据
        request.onsuccess = e=>console.log(e.target.result)
        // 这两个方法都共用cursor.update/delete
        // 甚至游标创建请求的的请求对象事件
    }
}
```

==注意==：这两个方法都会分别触发`update\delete`以及`openCursor`的请求对象事件，所以上面代码实际上会执行很多次

```javascript
// 上面代码改成cursor.advance(1)或者cursor.continue()时，就会一直查询下去，
// 因为写在了if(cursor)里面会一直执行
// 实际上最后依次执行返回了一个null

// 暂时不知道怎么让游标停下来
// 我认为让它停下来没有意义，如果只想改变某一个数据用事务对象就可以了
// 这里不能停下来就可以一直查找下去
```

#### 3.3.5 键范围

由于游标查询方式很有限，也就挨着查或者跳着查，所以有了键范围`key range`。

键范围相当于给了游标一个范围，一个约束。也就是说键范围定义在游标定义之前

```javascript
IDBKeyRange.only('key'); // 将范围固定到某个key位置
IDBKeyRange.lowerBound("key")// 定义下界，就是从这里开始向下
IDBKeyRange.lowerBound("key", true)// 第二个参数为true表示不从这个key开始，从它下一个开始查询
IDBKeyRange.upperBound("ace")// 定义上界，到ace为止
IDBKeyRange.upperBound("ace", true)// 同样有第二个参数，为true时上界为ace的上一个对象

IDBKeyRange.bound("007", "ace")// 同时指定上下界
IDBKeyRange.bound("007", "ace", true, true)// 分别可以设置包含边界
```

==注意==：

设置上下范围的时候，有可能会出现如上边界小于下边界的情况，这是由于对象是无序的，而且`indexDB`的操作是异步的，所以最终的数据库顺序可能和原始数据不一样。

#### 3.3.6 设定游标方向

`openCurser`有两个参数，第一个参数就是上节的范围，第二个参数就是设定游标的运动方向。

-   `next`或者`IDBCursor.NEXT`：向下
-   `prev`或者`IDBCursor.PREV`：向上
-   `IDBCursor.NEXT_NO_DUPLICATE`：向下不重复（但是本身创建数据库的时候就不允许重复
-   `IDBCursor.PREV_NO_DUPLICATE`：向上重复

#### 3.3.7 索引

有时候需要两种方式来查询某一数据，那就可以设置索引。

`createIndex()`参数：

-   第一个：索引对象的名字
-   第二个：用数据中的哪个值当做索引
-   第三个：设置是否唯一

```javascript
// 注意创建索引和创建对象存储空间很像
// 都需要在onupgradeneeded事件中注册
request.onupgradeneeded = (e)=>{
            // 创建对象存储空间
            let store = e.target.result.createObjectStore("users", { keyPath: "_id" });
            // 创建索引
            let index = store.createIndex("username", "username", { unique: false});
  //...........
 }
```

在索引上创建游标，和`store`上一样，只是结果中`e.target.result`的`key`属性变成了索引的而已，其他的方法属性都一样。

```javascript
request.onsuccess = function (event) {
    database = request.result;// database对象用来创建事务
    // 创建事务对象
    let transaction = database.transaction('users', 'readwrite');
    // 返回这个对象存储空间
    let store = transaction.objectStore("users");
    // 返回索引对象
    let index = store.index('username');// 直接在store上获取index对象
    //在索引上创建游标
    request = index.openCursor();
    
    request.onsuccess = (e)=>{
		//其他的操作都一样
    }
```

索引上还能创建一个特殊的游标，返回的游标对象不再包含对象的值，`key`表示索引`primaryKey`表示主键。

```javascript
//在索引上创建游标
request = index.openCursor();// 参数是一样的
```

直接从索引对象上得到某个值也是可以的

```javascript
 index = store.index("username"), 
 request = index.get("007");
```

也可以从某个索引上得到主键

```javascript
 index = store.index("username"), 
 request = index.getKey("007"); 
```

对象存储空间也能删除某个索引

```javascript
store.deleteIndex("username"); 
```

```javascript
request = index.openKeyCursor();
request.onsuccess = (e)=>{
    let cursor = e.target.result;
    if(cursor) {
        cursor.advance(1);
    }
} 
// e.target.result.source => IDBIndex
// IDBIndex对象中有如下属性
keyPath: "username"
multiEntry: false
name: "username"
objectStore: IDBObjectStore {name: "users", keyPath: "_id", indexNames: DOMStringList,transaction: IDBTransaction, autoIncrement: false}
unique: false
```

#### 3.3.8 并发问题

如果两个页面同时修改数据库就有可能发生冲突，需要合理利用`onblocked、onversionchange`等事件

```javascript
var openReq = mozIndexedDB.open("MyTestDatabase", 2);

openReq.onblocked = function(event) {
    // 如果其他标签页已经加载了这个数据库，那么
    // 在我们可以继续处理之前它需要被关闭。
    alert("Please close all other tabs with this site open!");
};

openReq.onupgradeneeded = function(event) {
    // 所有其它数据库都已经被关掉了。Set everything up.
    db.createObjectStore(/* ... */);
    useDatabase(db);
}  

openReq.onsuccess = function(event) {
    var db = event.target.result;
    useDatabase(db);
    return;
}

function useDatabase(db) {
    // 确保添加一个如果另一个页面请求一个版本变化时来被通知的处理程序。
    // 我们必须关闭这个数据库。这就允许其他页面对数据库进行升级。
    // 如果你不这么做的话，除非用户关闭标签页否则升级就不会发生。
    db.onversionchange = function(event) {
        db.close();
        alert("A new version of this page is ready. Please reload!");
    };

    // 其他针对数据库的处理
}
```

#### 3.3.9 限制

主要是大小限制，谷歌是5MB，火狐是50MB

---

# :clinking_glasses:第二十四章  最佳实践

---

## 1. 可维护性

-   可理解性，无需原有开发人员完整解释
-   直观性，一看就明白
-   可适应性，适应各种数据变化
-   可扩展性，编写时已经考虑未来扩展
-   可调式性，有地方出错时给予了足够的信息来确定问题所在

### 1.1 代码约定

也可以说是一种规范，约定俗成

#### 1.1.1 可读性

合理的缩进和注释都能增加代码的可读性

-   函数和方法：都应该注释，参数代表什么，返回值是什么，用来完成什么任务
-   大段代码：完成一个任务的多段代码，应该注释
-   复杂算法：使用一个独特的方法解决问题的时候应该注释
-   Hack

#### 1.1.2 变量和函数名

-   变量应该有意义的名词
-   函数名应该是动词

#### 1.1.3 变量类型的透明

-   初始化

    ```javascript
    let a = -1 //表示应该是个数值型
    let b = true // 应该是个布尔型
    ```

-   匈牙利标记法

    ```javascript
    // 在变量前面加个字符
    let bFound; //布尔型
    let iCount; //整数
    let sName; //字符串
    let oPerson; //对象
    ```

-   类型注释

    ```javascript
    let found /*:Boolean*/ = false; 
    let count /*:int*/ = 10; 
    let name /*:String*/ = "Nicholas"; 
    let person /*:Object*/ = null; 
    ```

### 1.2 松散耦合

​	耦合过紧：代码的一部分过于依赖另一部分，一个部分修改必须牵扯到另外一个部分修改。

### 1.3  编程实践

-   不要随便给不属于自己的对象添加方法属性

-   避免全局量

-   避免与`null`比较

    ```javascript
    function sortArray(values){ 
        if (values != null){ //避免！
            values.sort(comparator); 
        } 
    } 
    
    function sortArray(values){ 
        if (values instanceof Array){ //推荐
            values.sort(comparator); 
        } 
    } 
    ```

    如果看到了与 `null`比较的代码，尝试使用以下技术替换：

    -   如果值应为一个引用类型，使用 `instanceof `操作符检查其构造函数；
    -   如果值应为一个基本类型，使用` typeof `检查其类型；
    -   如果是希望对象包含某个特定的方法名，则使用 `typeof `操作符确保指定名字的方法存在于对象上。

-   定义常量

## 2. 性能

本来`JS`由于是解释性语言，所以执行效率较低，谷歌浏览器开始本地将`JS`代码编译执行，现代浏览器都有编译执行功能。

### 2.1 注意作用域

**访问全局变量总是要比访问局部变量慢**，因为需要遍历作用域链。只要能减少花费在作用域链上的时间，就能增加脚本的整体性能。

#### 2.1.1 避免全局查找

```javascript
function updateUI(){ 
    var imgs = document.getElementsByTagName("img"); 
    for (var i=0, len=imgs.length; i < len; i++){ 
        imgs[i].title = document.title + " image " + i; 
    } 
    var msg = document.getElementById("msg"); 
    msg.innerHTML = "Update complete."; 
} 
```

上面的代码表面看起来很正常，虽然没有明显的全局变量，但是它仍然使用了一个全局对象`document`，每次获取`DOM`对象的时候，都需要向上沿着作用域找到全局对象。

解决办法很简单，把全局对象存储在一个局部变量中，限制查找

```javascript
function updateUI(){ 
    var doc = document; // 保存在局部变量中
    var imgs = doc.getElementsByTagName("img"); 
    for (var i=0, len=imgs.length; i < len; i++){ 
        imgs[i].title = doc.title + " image " + i; 
    } 
    var msg = doc.getElementById("msg"); 
    msg.innerHTML = "Update complete."; 
} 
```

将在一个函数中会**用到多次的全局对象**存储为局部变量总是没错的

#### 2.1.2 避免with语句

由于`with`会构建自己的作用域，所以也会增加作用域链长度，影响性能

### 2.2 选择正确的方法

#### 2.2.1 避免不必要的属性查找

-   `O(1)`常数，不管多少值执行的时间是恒定的
-   `O(logn)`对数，值越大越接近常数，二分查找
-   `O(n)`线性，值越大耗时越高，遍历
-   `O(n²)`平方，每个值至少获取`n`次，插入排序

检索数组或者变量的值都是`O(1)`，而访问对象是一个`O(n)`操作，因为对象会搜索原型链。

```javascript
var query = window.location.href.substring(window.location.href.indexOf("?")); 
// window.location.href.substring()有 3 次，window.location.href.indexOf()又有 3 次
```

用了两次`window.location.href`，每次都是`O(n)`操作，但是将重复访问的对象放入变量就可以变成`O(1)`操作，大大减少耗时

```javascript
var url = window.location.href; 
var query = url.substring(url.indexOf("?")); 
```

#### 2.2.2 优化循环

-   减值迭代，有时候从最大值开始减少效率更高
-   简化终止条件，每次循环都会判断终止条件，所以终止条件应该避免属性查找之类的耗时操作
-   简化循环体
-   使用后测试循环，`for`和`while`都是前测试循环，`do-while`可以避免开始的时候进行测试

```javascript
for (var i=0; i < values.length; i++){ 
    process(values[i]); 
} 
// 提前存储长度，避免对象查询
```

#### 2.2.3  展开循环

`Duff`装置

```javascript
//credit: Jeff Greenberg for JS implementation of Duff’s Devic//假设 values.length > 0 
var iterations = Math.ceil(values.length / 8); 
var startAt = values.length % 8; 
var i = 0; 
do { 
    switch(startAt){ 
        case 0: process(values[i++]); 
        case 7: process(values[i++]); 
        case 6: process(values[i++]); 
        case 5: process(values[i++]); 
        case 4: process(values[i++]); 
        case 3: process(values[i++]); 
        case 2: process(values[i++]); 
        case 1: process(values[i++]); 
    } 
    startAt = 0; 
} while (--iterations > 0); 


// 更快的duff
//credit: Speed Up Your Site (New Riders, 2003) 
var iterations = Math.floor(values.length / 8); 
var leftover = values.length % 8; 
var i = 0; 
if (leftover > 0){ 
    do { 
        process(values[i++]); 
    } while (--leftover > 0); 
} 
do { 
    process(values[i++]); 
    process(values[i++]); 
    process(values[i++]); 
    process(values[i++]); 
    process(values[i++]); 
    process(values[i++]); 
    process(values[i++]); 
    process(values[i++]); 
} while (--iterations > 0); 
```

#### 2.2.4 避免双重解释

`JS`代码解释`JS`就是双重解释

```javascript
//某些代码求值——避免!! 
eval("alert('Hello world!')"); 
//创建新函数——避免!! 
var sayHi = new Function("alert('Hello world!')"); 
//设置超时——避免!! 
setTimeout("alert('Hello world!')", 500); 
```

#### 2.2.5 其他注意事项

-   原生方法速度较快
-   `Switch`语句速度较快
-   位运算速度较快

### 2.3 最小化语句数

#### 2.3.1 多个变量声明

```javascript
//4 个语句——很浪费 
var count = 5; 
var color = "blue"; 
var values = [1,2,3]; 
var now = new Date(); 

//一个语句 
var count = 5, 
   	color = "blue", 
   	values = [1,2,3], 
   	now = new Date(); 
```

#### 2.3.2 插入迭代值

```javascript
var name = values[i]; 
i++; 
// 修改为
var name = values[i++]; 
```

#### 2.3.3 使用素组和对象字面量

### 2.4 优化DOM交互

#### 2.4.1 最小化现场更新

如果需要更改一个已经呈现出的内容，那么就是现场更新

```javascript
var list = document.getElementById("myList"), 
    item, 
    i; 
for (i=0; i < 10; i++) { 
    item = document.createElement("li"); 
    list.appendChild(item); 
    item.appendChild(document.createTextNode("Item " + i)); 
} 
// 这段代码试图增加10个li
//总共有20个现场更新，每个li都有两个更新，一个添加一个添加文字
```

使用文档片段构建`DOM`结构，再添加到页面中

```javascript
var list = document.getElementById("myList"), 
    // 创建一个空的文档片段，相当于创建了一个空的对象
    fragment = document.createDocumentFragment(), 
    item, 
    i; 
for (i=0; i < 10; i++) { 
    item = document.createElement("li"); 
    // 正常增加元素
    fragment.appendChild(item); 
    item.appendChild(document.createTextNode("Item " + i)); 
} 
// 最后加入
list.appendChild(fragment); 
```

#### 2.4.2 使用innerHTML

对于大的 `DOM `更改，使用 `innerHTML `要比使用标准`DOM `方法创建同样的 `DOM`结构快得多。

但是由于它仍然是`DOM`操作，所以不要滥用

#### 2.4.3 使用事件代理

#### 2.4.4 注意HTMLCollection

多次查询`HTMLCollection`对性能影响很大

```javascript
var images = document.getElementsByTagName("img"), 
    i, len; 
for (i=0, len=images.length; i < len; i++){ 
    //处理 
} 

var images = document.getElementsByTagName("img"), 
    image, 
    i, len; 
for (i=0, len=images.length; i < len; i++){ 
    image = images[i]; //将当前图像引用保存起来，就不会再次寻找到images HTMLCollection
    //处理
} 
```

返回`HTMLCollection`的情况

-   ` getElementsByTagName()`
-   ` childNodes `属性
-   `attributes`属性
-   特殊集合如`document.forms、document.images 等`

----

# :kick_scooter:第二十五章 新兴API

---

## 1. requestAnimationFrame()

早期的循环动画无非就是调用定时器，定时调用动画，但是定时器也没有那么精确。

浏览器的定时器精度各不相同。

CSS 变换和动画的优势在于浏览器知道动画什么时候开始，因此会计算出正确的循环间隔，所以这个方法就是告诉浏览器什么时候执行动画。

## 2. Page Visibility API 

知道页面是否对用户可见

主要有三部分组成：

-   `document.hidden`：属性，页面是否隐藏，浏览器最小化或者不是当前标签， `true\false`
-   `document.visibilityState`：属性，`hidden\visible\prerender`
-   `visibilitychange`事件：`document`注册

## 3. Geolocation API 

实现地理定位

`navigator.geolocation `对象，包含三个方法

-   `getCurrentPosition(success, reject, options)`

    此方法会产生一个对话框，请求用户共享地理位置。

    三个参数：

    -   成功回调函数

        在成功回调中会接收到一个`Position`对象参数，有两个属性`coords\timestamp`。

        `coords`对象包含如下属性：

        1.  `latitude`：十进制表示的维度

        2.  `longitude`：十进制表示的经度

        3.  `accuracy`：经、纬度坐标的精度，以米为单位

            有些浏览器可能还提供下面的属性

        4.  `altitude`：以米为单位的海拔高度，如果没有相关数据则值为 `null`

        5.  `altitudeAccuracy`：海拔高度的精度，以米为单位，数值越大越不精确

        6.  `heading`：指南针的方向，0°表示正北，值为 `NaN` 表示没有检测到数据

        7.  `speed`：速度，即每秒移动多少米，如果没有相关数据则值为 `null`

            ```javascript
            //主要就是使用atitude 和 longitude
            navigator.geolocation.getCurrentPosition(function(position){ 
                drawMapCenteredAt(position.coords.latitude, positions.coords.longitude); 
            }); 
            ```

```
-   可选的失败回调函数

    接收一个对象包含两个属性`message\code `

-   可选的选项对象

    1.  `enableHighAccuracy `：布尔值，表示必须尽可能使用最准确的位置信息
    2.  `timeout `：等待位置信息的最长时间
    3.  `maximumAge `：上一次取得的坐标信息的有效时间，以毫
        秒表示，如果时间到则重新取得新坐标信息

    ```javascript
    navigator.geolocation.getCurrentPosition(function(position){ 
        drawMapCenteredAt(position.coords.latitude, positions.coords.longitude); 
    }, function(error){ 
        console.log("Error code: " + error.code); 
        console.log("Error message: " + error.message); 
    }, { 
        enableHighAccuracy: true, 
        timeout: 5000, 
        maximumAge: 25000 
    }); 
    ```
```

建议保持 enableHighAccuracy 的 false 值（默认值），将这个选项设置为 true 需要更长的时候，而且在移动设备上还会导致消耗更多电量。

如果不需要频繁更新用户的位置信息，那么可以将 maximumAge 设置为 Infinity，从而始终都使用上一次的坐标信息。

-   `watchPosition()`

    如果需要跟踪位置可以使用这个，用法和`getCurrentPosition()`完全相同，而且不会自己去轮询位置，而是等待系统通知位置已经改变。

-   `clearWatch(id)`

    清除监视

    ```javascript
    var watchId = navigator.geolocation.watchPosition(function(position){ 
        drawMapCenteredAt(position.coords.latitude, positions.coords.longitude); 
    }, function(error){ 
        console.log("Error code: " + error.code); 
        console.log("Error message: " + error.message); 
    }); 
    navigator.geolocation.clearWatch(watchId); 
    ```

## 4. File API

这些`API`是在表单原有基础上增加的。

### 4.1 Files集合

使用`change`事件监听文件上传，每个文件上传元素对象包含一个`files`属性，值是一个`FileList`集合对象，这个集合里面，每个文件就是一个`File`对象，有如下属性：

-   `name`：文件名称，包含后缀
-   `size`：文件字节大小
-   `type`：文件类型，`MIME`类型如`image/gif`
-   `lastModifiedDate`：上次文件修改的时间（只有谷歌才有这个属性）
-   `lastModified`：上次文件修改的时间（毫秒），`new Date(lastModified)`

```javascript
let file = document.getElementById('my-file');
file.onchange = (e)=>{
    console.log(e.target.files)
}
//=> FileList[] => 0:File => 属性
```

### 4.2 FileReader类型

`FileReader`一种异步文件读取机制，相当于文件系统的`ajax`。

主要有如下方法：

-   `readAsText(file, encoding)`：以纯文本的方式读取文件，结果保存到`result`属性中，第二个参数规定编码类型。
-   `readAsDataURL(file)`：读取文件，并将文件以数据URI的形式保存在`result`中
-   `readAsBinaryString(file)`：读取文件并将文件以一个二进制字符串保存
-   `readAsArrayBuffer(file)`：读取文件，内容保存在`ArrayBuffer`中，也是在`result`中读取。

一般来说，对图片使用`readAsText`是没有意义的，所以一般图片使用`readAsDataURL`

```javascript
file.onchange = (e)=>{
    let reader = new FileReader();
    let file = e.target.files[0];
    // 按照dataURL读取
    reader.readAsDataURL(file);
    // 记录读取进度
    reader.onprogress = function(event){ 
        if (event.lengthComputable){ 
            progress.innerHTML = event.loaded + "/" + event.total; 
        } 
    }; 
    // 错误事件
    reader.onerror = ()=>alert('!!!')
    // 读取完成
    reader.onload = (e)=>{
        console.log(e.target.result)
        // 直接将结果赋予img的src属性就能显示图片
        img.src = e.target.result
    }
}
```

`onprogress`事件和`ajax`的一样包含如下属性：

-   `lengthComputable`：字节加载完成
-   `loaded`：已加载字节
-   `total`：总共字节

`error`事件对象只包含一个属性`code`，错误码

`loadend`事件：已经读取完成，读取发生错误，读取中断。



取消上传可以使用`FileReader.abort()`方法，这个方法会触发`loadend`事件。

==注意==：

1.  `onprogress`事件有可能并不会完整检测整个读取过程，因为这个事件`50ms`触发一次，很有可能错过了某段读取过程。可以在`onload`事件中将进度设置为完成，需要将文件总大小保存一下。
2.  为什么使用`onload`而不是`loadend`？因为如果发生错误`onload`就不会触发，可以更好的控制读取。

### 4.3 读取部分内容

文件对象`(File)`有这个方法，返回一个`Blob`对象。

`slice()`方法：

-   和数组的一样，接收两个参数，起始字节和要读取的字节数。

`Blob`是`File`的父类型，他有`size`和`type`类型，也支持`slice`方法。

`FileReader`也可以读取`Blob`中的数据。

如果你需要只读取文件头部的某些信息，这个方法非常有用。

### 4.4 对象URL

生成文件的`URL`，就可以不在`JS`中读取，而是根据地址找到文件

注意区分之前的`readAsDataURL`返回的数据`URI`，是不一样的东西。

这个`URL`是一个字符串，保存的是一块**内存地址**

```javascript
// 创建URL
let dataUrl = URL.createObjectURL(File);
// 可以直接给img
imhg.src = dataUrl;
// 使用完后释放内存
URL.revokeOjbectURL(dataUrl)；
```

### 4.5 读取拖放的文件

结合`HTML5`脚本编程中的拖拽。

```javascript
dragBox.addEventListener('dragenter', e=>{
    e.preventDefault();//
})
dragBox.addEventListener('dragover', e=>{
    e.preventDefault();//
})
dragBox.addEventListener('drop', e=>{
    e.preventDefault();//阻止浏览器打开拖放的文件
    let files = e.dataTransfer.files;// 取得拖放的文件
    // 将文件访如页面
    let dataUrl = URL.createObjectURL(files[0]);
    let img = document.getElementById('drag-img');
    img.src = dataUrl;
})
```

### 4.6 结合`ajax`上传文件

方法一：直接将文件内容放入`xhr.send()`中，再通过`POST`请求。但是服务器端还需要新建一个文件保存这个文件。

方法二：将文件伪装成表单数据

使用二十一章的`FormData`类型，通过`append()`方法将文件添加进去，最后将`FormData`放入`xhr.send()`方法。

```javascript
reader.onload = (e)=>{
  	// 实例一个formData
    let formData = new FormData();
	// 直接将file对象加入
    formData.append('file1', fileItem);
    // ajax初始化
    let xhr = new XMLHttpRequest();
    xhr.open('post', 'servece.php');
    xhr.onreadystatechange = (e)=>{
        if(xhr.readyState === 4) {
            alert(xhr.responseText)
        }
    }
    xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded')
    // 直接将formData传入
    xhr.send(formData)
}
}
```

## 5. Web计时

计时的核心是`performance`对象 。主要两个属性

-   `performance.navigation`：与页面导航有关的多个属性
    1.  `redirectCount`：页面加载前的重定向次数
    2.  `type`：刚刚发生的导航类型
        -   `performance.navigation.TYPE_NAVIGATE (0)`：页面第一次加载
        -   `performance.navigation.TYPE_RELOAD (1)`：页面重载过
        -   `performance.navigation.TYPE_BACK_FORWARD (2)`：页面是通过前进或后退打开的
-   `performance.timing`：不同事件产生的时间戳

1.  `navigationStart`：开始导航到当前页面的时间。
2.  `unloadEventStart`：前一个页面的 `unload` 事件开始的时间。但只有在前一个页面与当前页面来自同一个域时这个属性才会有值；否则，值为 `0`。
3.  `unloadEventEnd`：前一个页面的 `unload` 事件结束的时间。但只有在前一个页面与当前页来自同一个域时这个属性才会有值；否则，值为 `0`。
4.  `redirectStart`：到当前页面的重定向开始的时间。但只有在重定向的页面来自同一个域时个属性才会有值；否则，值为 `0`。
5.  `redirectEnd`：到当前页面的重定向结束的时间。但只有在重定向的页面来自同一个域时这属性才会有值；否则，值为 `0`。
6.  `fetchStart`：开始通过 `HTTP GET` 取得页面的时间。
7.  `domainLookupStart`：开始查询当前页面 `DNS` 的时间。
8.  `domainLookupEnd`：查询当前页面 `DNS `结束的时间。
9.  `connectStart`：浏览器尝试连接服务器的时间。
10.  `connectEnd`：浏览器成功连接到服务器的时间。
11.  `secureConnectionStart`：浏览器尝试以 `SSL` 方式连接服务器的时间。不使用 `SSL` 方式连接时，这个属性的值为`0`。
12.  `requestStart`：浏览器开始请求页面的时间。
13.  `responseStart`：浏览器接收到页面第一字节的时间。
14.  `responseEnd`：浏览器接收到页面所有内容的时间。
15.  `domLoading`：`document.readyState` 变为`"loading"`的时间。
16.  `domInteractive`：`document.readyState` 变为`"interactive"`的时间。
17.  `domContentLoadedEventStart`：发生 `DOMContentLoaded` 事件的时间。
18.  `domContentLoadedEventEnd`：`DOMContentLoaded `事件已经发生且执行完所有事件处理程序的时间。
19.  `domComplete`：`document.readyState` 变为`"complete"`的时间。
20.  `loadEventStart`：发生 `load` 事件的时间。
21.  `loadEventEnd`：`load` 事件已经发生且执行完所有事件处理程序的

## 6. Web Workers

### 1. 使用

```javascript
var = new Worker("stufftodo.js"); // 参数为要执行的js文件地址
worker.postMessage('xxx')// 发送信息给worker
// 监听worker的消息
worker.onmessage = (e)=>{
    
}

// stufftodo.js中
let worker = this;
worker.onmessage = (e)=>{
    if(e.data = "xxx") {
        // 确认接收到信息再执行函数
        // 执行完成后再把数据发回去
        worker.postMessage(data)
    }
}
// 如果想停止调用 
worker.close()
```

## 2. Worker 全局作用域

在被引用的`JS`文件中，全局对象指向了`Worker`，也就是说`this\self`指向`Worker`。

相当于一个小`window`，只包含一些主要功能，比如：

-   `navigator`：包含`onLine\appName\appVersion\userAgent\platform`
-   `location`：唯一区别是这是只读的
-   `setTimeout()\setInterval()\clearTimeout\clearInterval()`
-   `XMLHttpRequest`

如果想引用多个脚本

```javascript
//Web Worker 内部的代码
importScripts("file1.js", "file2.js"); 
// 按照顺序执行
```

----

# :atom_symbol: ES6

---

## 1. 生成器

生成器：每次只能生成一系列值中的一个。

在需要一系列值，而每一个值又与前一个值存在某种关系的情况下，可以使用生成器

```javascript
function myNumbers(){ 
    for (var i=0; i < 10; i++){ 
        yield i * 2; //使用yield返回值
    } 
} 
// 使用yield的函数会返回一个 Generator实例
var generator = myNumbers(); 
try { 
    while(true){ 
        // Generator实例使用next()方法获取下一个值
        document.write(generator.next() + "<br />"); 
    } 
} catch(ex){ 
    //有意没有写代码
} finally { 
    // 关闭迭代器
    generator.close(); 
} 
```

## 2. 代理对象

具体可以设置的配置属性，百度

```javascript
// 注意这个构造函数并不是传统意义上的构造函数
var Stack = function () {
    var stack = [],
        // 允许访问的属性
        allowed = ["push", "pop", "length"];
    // 这个函数返回一个Proxy对象
    // Proxy构造函数接收两个参数，代理目标和配置对象
    // 这里的目标就是实例
    return new Proxy(this, {
        // receiver就是代理目标，也就是实例
        // name就是访问的属性名
        get: function (receiver, name) {
            // 如果属性是允许的
            if (allowed.indexOf(name) > -1) {
                // 如果属性是方法就调用
                if (typeof stack[name] == "function"){
                    // 在一个空数组上调用该方法
                    return stack[name].bind(stack);
                } else {
                    // 不是方法
                    return stack[name];
                }
            } else {
                return undefined;
            }
        }    
    })
}
var mystack = new Stack(); 
mystack.push("hi"); 
mystack.push("goodbye"); 
console.log(mystack[0]); //未定义
console.log(mystack.length); //2 
console.log(mystack.pop()); //"goodbye" 
```

==注意==区别`Object.defineProperty`：

-   `defineProperty`只能对一个属性设置`getter\setter`，这里是所有属性甚至不存在的都行。
-   `defineProperty`中的`getter\setter`参数和这里的不一样

## 3. 代理函数

// 待补充

## 4. 映射与集合

映射：

当使用对象保存数据的时候，有可能数据的键名等于对象的属性，所以会出现混淆。使用映射可以避免这个问题

```javascript
var map = new Map(); 
map.set("name", "Nicholas"); 
map.set("book", "Professional JavaScript"); 
console.log(map.has("name")); //true 
console.log(map.get("name")); //"Nicholas" 
map.delete("name"); 
// set get delete
```

集合：

集合就是一组不重复的元素。

```javascript
var set = new Set(); 
set.add("name"); 
console.log(set.has("name")); //true 
set.delete("name"); 
console.log(set.has("name")); //false 
```

## 5 WeakMap

当属性解除引用时就是删除这个元素

```javascript
var key = {},
    map = new WeakMap();
map.set(key, "Hello!");
//解除对键的引用，从而删除该值
key = null 
console.log(map.has(key))// false
```



















