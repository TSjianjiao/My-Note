# :artificial_satellite: JS中的碎片

------

## 1.扩展运算符

```javascript
[...data]====>[item1, item2, item3]  // 把任意一种列表转化为数组
[...$("div")] ===> Array [<div class="css1">, <div class="css2">]
[...document.querySelectorAll('div')] ===> Array [<div class="css1">, <div class="css2">]
// 利用这个可以把一些不能使用Array对象的集合，转为数组，方便操作
[...maxData.toString()]  // 把一个数字转成字符串，最后分割为数组227===>['2', '2', '7']
```

```javascript
// 还有个用法
// 获取数组中的最大值
var a = [1, 2, 3, 4, 5]
Math.max(...a) // 注意不带中括号，这样就把数组打开了 作为参数输入到了max方法
===> 5
```

==相似==ES6中有一个新的Array对象原型方法，`Array.from()`，将一个**可迭代对象**或者是一个**类数组对象**变成真正的数组

```javascript
//类数组对象:
//就是有个length属性
{
    a:1,
    b:2,
    length:2
}

Array.from(转化目标,相当于map的一个回调函数)
```

还有一个用法

```javascript
// 以前为了不改变原来的数组 需要slice或者其他操作
//直接用扩展运算符就能完全 复制 而且不会影响到原数组
let a = [1, 2, 3]
let b = [...a] //将a直接扩展为另一个一模一样的数组
b.pop()
//打印这两个变量
a=[1, 2, 3]
b=[1, 2]//互不影响
```



## 2.数组的every、some、map、forEach、filter

```javascript
Array.every(function (item, index) {
    return item > 18
})  // 所有的数组元素item 都 大于18才返回 1个 true/否则false
Array.some(function (item, index) {
    return item > 18
})  // 只要有元素大于18就返回 1个 true否则false
Array.map(function (item, index) {
     Math.sqrt(item);
})  // 首先map会返回一个新的数组，和slice一样不会改变原数组
   // 每个元素都会调用函数
Array.forEach(function (item, index) {
     Math.sqrt(item);
}) // 每个元素都会调用函数，也是只调用一下，不会返回新的数组，也不会改变原数组
Array.filter(function (item, index) {
    return item > 18
})  // 返回满足条件的元素
```

## 3.exports和module.exports

- 普通对象的一些性质

  ```javascript
  // 这里有两个对象
  var obj1 = {...};
  var obj2 = {...};
  // 这样操作时，两个对象指向了内存的同一块单元
  obj2 = obj1;
  // 但是对obj2进行这样操作时 两个对象就不再指向同一内存
  // obj1并没有aa这个属性，所以这两个对象不是同一个
  obj2.aa = "sss";
  ```

- 结合上面的例子

  1. 首先每个文件的最后其实都有一个隐藏的一句话

     ```javascript
     return module.exports;
     ```

  2. 所以当我们需要导出东西时，就需要给 module.exports对象赋值

     ```javascript
     module.exports = {
         aaa:aaa,
         aa(){
             ....
         },
         vv:ss,
         sss:121
     }
     ```

  3. 然而为了更加的方便，增加了一个新的对象exports<br>于是 exports 与 module.exports变成了这样的关系

     ```javascript
     var exports = module.exports;  // 这就变成了上面例子的关系
     ```

  4. 所以如果这样

     ```javascript
     exports.aa = function () {
         ....
     }
     // 那么exports 和 module.exports就不是同一个对象了
     ```

  5. ==反正最好不要混用，或者干脆就用module.exports==

## 4.数组操作的一些细节

### 4.1 尽量不操作原数组/字符串

```javascript
var a = [...];
var b = a;
b.pop();  // 尽管表面上看起来是操作的b数组，实际上因为对象的性质（看这一章的第三节）,他们都指向同一个内存
         // 所以操作b也会影响a
// 为了避免影响原数组 使用slice()方法切片数组 保存
var b = a.slice(0)  // 不指定end参数时默认切片到末尾 这样操作b就不会影响a
    b = a.concat() // 不给参数，默认就是复制
```

## 5.call()

```javascript
// 比如这种情况
// 有一个构造函数
function aa() {
    this.bb=...;
    ....
}
aa.prototype.cc = function () {
    // 在这里面使用了一个方法，而这个方法会改变this
    // 比如这个foreach，这里面的this指向的是这个数组
    // 所以为了能使用构造函数里面的属性，这里要改变this指向
    [...].forEach(function (item, index) {
 		this.bb.... // 不能直接this.bb
 	});
     // 增加一句
     [...].forEach(function (item, index) {
         aa.call(this); // 这样这里面的this就指向了构造函数
 		this.bb.... 
 	});
}

```

## 6. forEach中看this

上面一个例子在接下来的例子中却不管用

```javascript
// 有一个构造函数
function aa() {
    this.bb=...;
    ....
}
aa.prototype.cc = function () {
    // 在这里面使用了一个方法，而这个方法会该表this
    // 比如这个foreach，这里面的this指向的是这个数组
    // 所以为了能使用构造函数里面的属性，这里要改变this指向
    [...].forEach(function (item, index) {
     	[...].forEach(function (item, index) {
         	 aa.call(this);
 			this.bb.... // 就算是call了一次构造函数，依然无法使用bb属性
 		});
 	});
}
```

==暂时还不知道这是什么原因，但是解决办法是==

```javascript
aa.prototype.cc = function () {
    [...].forEach(function (item, index) {
     	[...].forEach(function (item, index) {
         	 aa.call(this);
 			this.bb....
 		}, this);
 	}, this);
} // 在forEach的第二个参数让回调函数的this，指向调用者
```

- 姑且猜测

  > 第一层的是`数组`调用的forEach函数，所以`this`指向的`这个数组`，`第二层`是第一层数组里的元素调用的forEach所以也是指向的数组。加上this，先使第一层的回调函数指向了这个`原型方法的调用者`，第二层指向了第一层的原型函数的this也就是`间接`指向了原型方法的调用者

- 简单判断this之像谁

  > 如果不确定需不需要重新指定this，可以在编辑器中打出**this.**看看编辑器有没有提示出属性，如果没有提示，就代表this指向已经改变了。

## 7.字符串与数字

==注意==

- 一个字符串类型的数据，如'123','56'....在做计算操作时一定要先**转换**为数字类型，不然如加法，会变成**字符串连接**！！！

## 8. onmousemove 、mouseleave 、onmouseout 

onmousemove 事件在鼠标移动到 **div** 元素上时触发。

 mouseleave 事件只在鼠标指针移出 **div** 元素时触发。 

 onmouseout 事件在鼠标指针移出 **div** 元素及离开**子元素**时触发。

## 9. async 和 await

[主要参考文献1](https://www.cnblogs.com/vipzhou/p/6519552.html)

[主要参考文献2](https://segmentfault.com/a/1190000007535316)

1. **首先**每个函数都可以返回一个`Promise `类

   ```javascript
   function A() {
       return new Promise((resolve, rejects)=>{// 回调函数两个参数resolve和rejects
           .......
           resovle(....)  // 这里的resovle后面解释
       })
   }
   ```

2. **然后**`async` 修饰的函数本身返回值就是Promise 类

   其实最重要的是==**只有它修饰过**==的函数才能用`await`

   ```javascript
   // async修饰的函数
   async function A() {
       var a = "aaa";  // 随便什么值都一样
       return a // 直接返回没有意义  只是一个Promise 类 里面包含这个异步函数是否执行完成
   }			// 所以想要直接获取async修饰的函数的返回值是很困难的 反正我不知道怎么获取
   
   // 只有在async修饰过的函数中才能用await关键词
   ```

3. `await`就是等待，等待函数的返回值，但是等待的函数返回的如果是Promise 类

   那么他等的就是**Promise .resolve,** 而且还要**等待这个函数执行完了才进行下一步**

   也就是**会阻塞**后面

   ```javascript
   // 在b中可以放个异步函数，输出由resovle传递
   function B() {
       return new  Promise((resolve, rejects)=>{
         var a = '!!!'
         resovle(a) 
       };
   }
   // 在 async中
   async function A() {
       var a = await B();  // 有了await 没有等到resovle传递的数据就不会下一步
       console.log(a)  // 这里不能直接return a 和上面说的一样 只会返回一个Promise 类
   }
   //输出>>>>'!!!!' 
   ```

4. 示例

   ```javascript
   function asyncFunc (arr, i) { // 普通函数
       return new Promise(function(resolve, reject) {  // 返回一个Promise 类
           setTimeout(function() {  // 模拟一个异步函数
               arr.push(i);
               console.log("index is : ", i);
               resolve();  // 这里没有传递实际参数 但是也必须写 不然await一直等
           }, 1000);
       });
   }
    
   var box5 = async function() {  // async修饰的异步函数
       var arr = [];
       for (var i = 0; i < 5; i++) {  // 这是一个循环 循环是同步操作 正常情况下 
           await asyncFunc(arr, i);  // 如果循环里面有异步函数 循环不会等异步函数
       }                             // 也就是说 异步函数还没有执行完 有可能循环已经完成了
       console.log(arr);     // 这里可以看到使用了 await 也就是说每次循环执行到这里就会等待
   }                         // 直到异步函数完成 相当于阻塞了后面程序
    
   box5();
   
   ```

   - 我自己的一个例子

   ```javascript
   // 现在有三个函数
   function update(){}
   function ajax(){}
   function main(){}
   // 首先 ajax() 这里面是ajax的一些函数 肯定是异步的
   // 所以使用 return 一个Pormise类来让 update函数等待 之后才能更新
   function ajax() {
       return new Pormise((resolve, reject)=>{
           ....ajax操作...
           resolve(ajax响应结果)
       })
   }
   // 然后 update() 接收
   // 这里也解释了为什么要使用async修饰 因为需要循环这个一步操作
   async function update() {
       for (x of range) {
          var a = [];
         a.push (await ajax()）; // 这些操作就和示例一一样
       }						// 每一次循环都等待 ajax()的返回值
   }
   // 最后问题来了 我要怎么把update()的数据读出来呢
   // 前面说过不能直接return 因为async修饰的函数只会返回Pormise类
   // 最后我发现 可以让宁外一个函数再等待update()的Pormise
   // update()就可以这样写
   async function update() {
       for (x of range) {
          var a = [];
         a.push (await ajax()）; 
       }	
    	return new Promise((resolve, reject)=>{
             resolve(a)  // 这样就可以输出了
         })
   }
   // 使用另外一个函数接收 
   // 注意加上async 不然用不了await
   async function main() { 
   	var b = await update();
       //  然后接下来就可以在这个函数里面任意使用这个值了
   }
   ```



## 10. 进制转换

```javascript
let a = 10;
console.log(a.toString(2)); // 转二进制
console.log(parseInt('1011', 2)); // 转十进制
```

## 11. Object.defineProperty(obj, prop, descriptor)

### 参数

- `obj`

  要在其上定义属性的对象。

- `prop`

  要定义或修改的属性的名称。

- `descriptor`

  将被定义或修改的属性描述符。

### 返回值

```
被传递给函数的对象。

```

第三个参数可选的设置

{`configurable` /`enumerable` /`value` /`writable` }

可以被删除或者重新定义/可以被枚举/设置的值/可以做赋值运算

## 12.箭头函数的一些问题

```javascript
// 如果使用function定义函数
var a = {
    a:1,
    function bb(){
    console.log(this.a)  // 在这个对象里 this就指向的对象
}
}
a.bb()
// 输出就会是1
// 如果用箭头函数
var a = {
    a:1,
    bb：()=>{
    console.log(this.a)  // this将不指向对象a  而是父级作用域(a的作用域)的上下文 window
}
}
a.bb()
// 输出将是undefined
```

## 13.对象继承

第一种方法：`call()`

```javascript
// 在构造函数里 call另一个构造函数
function aa(A, B, C) {
    // 这是另一个对象的构造函数
    bb.call(this,A, B)
    this.C = xxx
}
```

==但是==这样做只能继承属性，要继承方法可以这样：

```javascript
//在上面的基础上加上
Teacher.prototype = Object.create(Person.prototype);
Teacher.prototype = Person.prototype；//相当于
// 这样Teacher的原型继承了所有Person的属性和方法
```

==还需改进==：使用` Object.create`方法创造的对象`this`仍然指向Person，如上面的第二句代码，两个对象是一样的，都指向同一块内存，所以`this`指向不变

```javascript
// 所以改变this指向Teacher的构造函数
Teacher.prototype.constructor = Teacher;
```

```javascript
//  据说是最佳的方法
//继承原型
  function extend(subType, superType) {
    function F(){};
    F.prototype = superType.prototype;  // 现在这个F类完全指向了超类
 									// 等于超类
    var prototype = new F;
    prototype.constructor = subType;// 将这个F类的构造属性指向子类
    subType.prototype = prototype;// 再把超类 给子类
   // 优点就是只需要调用一次就继承 不会像写在构造函数里面一样 new一个继承一次
  }
 
  //超类方法
  function SuperType(name) {
    this.name = name;
    this.colors = ["red", "blue", "green"];
  }
  SuperType.prototype.sayName = function() {
    return this.name;
  }
 
  //子类方法
  function SubType(name, age) {
    SuperType.call(this, name);
    this.age = age;
  }
 
  //继承超类的原型
  extend(SubType, SuperType);
 
  //子类方法
  SubType.prototype.sayAge = function() {
    return this.age;
  }
 
  var instance1 = new SubType("Shelby");
  var instance2 = new SubType("Court", 28);
 
  instance1.colors.push('black');
 
  alert(instance1.colors); //red,blue,green,black
  alert(instance2.colors); //red,blue,green
 
  alert(instance1 instanceof SubType); //true
  alert(instance1 instanceof SuperType); //true
```

[几种继承方法](http://www.jb51.net/article/88295.htm)

## 14. 解构赋值

```javascript
let [a, b] = [10, 20]; // 同时给多个变量赋值  顺序是有关系的
let [a, b, ...rest] = [10, 20, 30, 40, 50];// =>10,20，[30, 40, 50] //多余的合并到加了...的变量里
let ({ a, b } = { a: 10, b: 20 });//=>a=10，b=20//注意键，一定要对应
let ({a, b, ...rest} = {a: 10, b: 20, c: 30, d: 40});// 和数组一样，a=10，b=20，rest={c:30，d:40}

let x = [1, 2, 3, 4, 5];
let [y, z] = x;//可以直接取出一维数组的值 y=1，z=2

let [a=5, b=7] = [1];// 可以给一个默认值 防止右边有undefined =>a=1，b=7

let a = 1;
let b = 3;
[a, b] = [b, a];//可以交换了两个变量的值 => a=3, b=1  //不用再来个中间temp变量

function f() {
  return [1, 2, 3];
}// 一个函数返回一个数组
let [a, b] = f(); //运用解构 =>a=1，b=2
let [a, , b] = f();// 甚至还能忽略某些值 =>a=1，b=3


/*****************解构对象****************************/
let o = {p: 42, q: true};
let {p, q} = o; // 解构对象 =>p=42，q=true//键名要对应上//解构的结果不是对象了，是一个个单独的变量

let ({a, b} = {a: 1, b: 2});//字面量解构 // 要用（）包围
let {a = 10, b = 5} = {a: 3};//同样可以有默认值
let {a:aa = 10, b:bb = 5} = {a: 3};//还能给新的键名换个名字=>aa=3,bb=5


/*********************解构函数参数  会有点难理解************************************/
//注意左边虽然是{}包围的 但是里面不是key:value而是key=value
function drawES2015Chart({size = 'big', cords = { x: 0, y: 0 }, radius = 25} = {}) 
{
  console.log(size, cords, radius);//=>size='big',cords={ x: 18, y: 30 },radius=30
  // do some chart drawing
}

drawES2015Chart({//我们调用了drawES2015Chart函数 传入参数{cords: { x: 18, y: 30 },radius: 30}
  cords: { x: 18, y: 30 },
  radius: 30
});
// 观察到 声明drawES2015Chart的时候 形参是{size = 'big', cords = { x: 0, y: 0 }, radius = 25} = {}
// 是一个解构对象，但是右边是空的，这是用来传入参数的
// 当传入参数的时候，如这里：
//{size = 'big', cords = { x: 0, y: 0 }, radius = 25}={cords: { x: 18, y: 30 },radius: 30}
//最后就被解构成了单独的变量


/**************************嵌套对象和数组****************************************/
var metadata = {
    title: "Scratchpad",
    translations: [
       {
        locale: "de",
        localization_tags: [ ],
        last_edit: "2014-04-14T08:43:37",
        url: "/de/docs/Tools/Scratchpad",
        title: "JavaScript-Umgebung"
       }
    ],// translations是一个数组对象
    url: "/en-US/docs/Tools/Scratchpad"
};
//首先这里重命名的title变为了englishTitle
// englishTitle获取了第一个值title的Scratchpad
//localeTitle获取了第二个值 translations里面的title 
//并把title重命名为localeTitle
var { title: englishTitle, translations: [{ title: localeTitle }] } = metadata;

console.log(englishTitle); // "Scratchpad"
console.log(localeTitle);  // "JavaScript-Umgebung"


/***********************For of 迭代*************************************/
var people = [
  {
    name: "Mike Smith",
    family: {
      mother: "Jane Smith",
      father: "Harry Smith",
      sister: "Samantha Smith"
    },
    age: 35
  },
  {
    name: "Tom Jones",
    family: {
      mother: "Norah Jones",
      father: "Richard Jones",
      brother: "Howard Jones"
    },
    age: 25
  }
];
// 接收people值的变量 变成了解构变量 这里依旧重命名了变量
for (var {name: n, family: { father: f } } of people) 
{
  console.log("Name: " + n + ", Father: " + f);
}

// "Name: Mike Smith, Father: Harry Smith"
// "Name: Tom Jones, Father: Richard Jones"


/*******也可以直接获取参数*******/
function userId({id}) {
  return id;//直接获取了id//注意这里{id}意思是取实参的id属性
}

function whois({displayName: displayName, fullName: {firstName: name}}){
  console.log(displayName + " is " + name);
}

var user = { 
  id: 42, 
  displayName: "jdoe",
  fullName: { 
      firstName: "John",
      lastName: "Doe"
  }
};

console.log("userId: " + userId(user)); // "userId: 42"
whois(user); // "jdoe is John"
```

## 15.对象取值

```javascript
//我们都知道 对象有两种取值方式[]和.
// 有时会会忘记可以直接在字面量上面取
{a:111,b:222}["a"];
let x = "a"
{a:111,b:222}[x];
```

## 16.闭包

https://www.cnblogs.com/q460021417/p/4154340.html

https://www.cnblogs.com/giggle/p/5452271.html

一个函数内部的变量，在外部是无法被访问到的，但是**闭包**可以说是**一个连接的桥梁**，比如例子中，**return了一个对象**，当然也可以只返回一个函数，这个对象里面的方法在函数内部使用了函数内部的变量，但是我们在外面`.`这个方法的时候仍然能得出结果。

闭包有个特性，不会在没使用里面函数的时候，加载里面函数和方法，因为你只是声明了没有使用

```javascript
        var Counter = (function() {
        var privateCounter = 0;
        function changeBy(val) {
            privateCounter += val;
        }
        return {
            increment: function() {
            changeBy(1);
            },
            decrement: function() {
            changeBy(-1);
            },
            value: function() {
            return privateCounter;
            }
        }   
        })();// 使用匿名函数包裹 立即执行了{}（）匿名函数

	    var makeCounter = function() {
      	var privateCounter = 0;
        function changeBy(val) {
            privateCounter += val;
        }
        return {
            increment: function() {
            changeBy(1);
            },
            decrement: function() {
            changeBy(-1);
            },
            value: function() {
            return privateCounter;
            }
        }  
        };// 没有立即执行

```

上面两种方法都能使我们间接访问到函数内部的变量，但是两种方法只有一点区别，**是否**在定义时**执行**了这个**匿名函数**。

这样做的作用很简单：

```javascript
//如果是第一种 创建的时候立即执行 只有一个实例
var a = {..}()//那么之后你只能使用这个一个 a变量 然后a.xxx a.ooo来访问函数内部的变量

// 如果是第二种
var b = function(){}
//因为没有立即执行 所以不会返回一个对象
//使用前再执行 这样可以执行多次 创建多个实例
var cc = bb()
var dd = bb()
//每个变量互不影响
console.log(a.value()); /* logs 0 */
a.increment();
a.increment();
console.log(a.value()); /* logs 2 */
a.decrement();
console.log(a.value()); /* logs 1 */
console.log(b.value()); /* logs 0 *///无论a怎么加减 b仍然是0
```

在创建类的时候，可以这样

```javascript
//原始创建 这样每次new的时候都会调用这个构造函数里面那些函数
function MyObject(name, message) {
  this.name = name.toString();
  this.message = message.toString();
  this.getName = function() {
    return this.name;
  };
  this.getMessage = function() {
    return this.message;
  };
}

// 利用闭包 只有在用的时候才会调用
function MyObject(name, message) {
  this.name = name.toString();
  this.message = message.toString();
}
MyObject.prototype.getName = function() {
  return this.name;
};
MyObject.prototype.getMessage = function() {
  return this.message;
};

/// 但是闭包会在程序一打开就执行 必须关闭浏览器才会销毁 容易内存泄露
```

## 17.单例

和**20小节工厂模式**一样，属于一种设计模式

单例就是一个类只有一个实例，无论立new多少次，this都指向一个

```javascript
//第一个方法 就是利用上面的闭包 创建一个变量 
var Counter = (function() {
    var privateCounter = 0;
    function changeBy(val) {
        privateCounter += val;
    }
    return {
        increment: function() {
            changeBy(1);
        },
        decrement: function() {
            changeBy(-1);
        },
        value: function() {
            return privateCounter;
        }
    }   
})();// 现在就只能通过Counter.xxx来访问

// 第二种方法
function singletonAccepter( args ){
    //判断Universe.instance是否已存在实例
    if(typeof singletonAccepter.instance === 'object'){// 如果已经创建 就直接返回这个this
        return singletonAccepter.instance;// 主要就是这两个部分
    }			
    this.name = args.name || 'Monkey';
    this.age = args.age || 24;
    singletonAccepter.instance = this;// 主要 如果instance属性没有被赋值this 也就是创建了一个对象
};									//  就在这里赋值

//还有利用new的原理
function singletonAccepter( args ){
    var instance = null;
    var args = args || {};
    this.name = args.name || 'Monkey';
    this.age = args.age || 24;
    //将instance引用创建的实例this
    instance = this;
    //重写构造函数
    singletonAccepter = function(){ // 注意了 这里就是闭包 下面解释
        return instance;		
    }
};
/***********解释一下*********/
比如我们要创建两个实例 当然实际上只会有一个
var a = new singletonAccepter({name:'xx', age:12})
var b = new singletonAccepter({name:'oo', age:15})
//正常情况下 输出应该都是 {name:'xx', age:12}
//第一次创建，也就是a的时候
instance = this
然后构造函数被覆写了，变成了一个闭包 返回上面的instance
//所以第二次创建 b的时候
实际上singletonAccepter变成了 这个覆写的函数也就是直接返回上面的this
而不是再走一遍 instance = this 什么的


// es6
class Restaurant {
    constructor({cash = 0, seats = 0, staffList = []} = {}) {
        this.cash = cash;
        this.seats = seats;
        this.staffList =staffList;
    }
    // 单例接口
    static getInstance(param) {
        if(!this.instance) {
            this.instance = new Restaurant(param);
        }
        return this.instance;
    }
}
//并且继承这个类的时候 子类的实例也是单例 但是子类的单例又和其他的单例相区别
```

## 18.ES6类

和上面有点关联

```javascript
let id = 1;//这是一个父类
class Staff {
    constructor ({name = '', wages = 0} = {}) {
        this.id =  id++;
        this.name = name;
        this.wages = wages;
    }
    // 单例接口
    static getInstance(param) {
        if(!this.instance) {
            this.instance = new Staff(param);
        }
        return this.instance;
    }
    // 完成工作
    doneWork() {
        console.log("staff完成工作")
    }
}
//这个子类继承这个父类
/**
 * 服务员类，继承自职员
 */
class Waiter extends Staff {
    constructor ({name = '', wages = 0} = {}) {
        super(name, wages); // 使用super代替call(this) 
    }                                     // 创造了一个这个子类的this 不然使用的是父类的this
    // 服务员完成工作
    waiterWork(order) {
        //如果参数是个数组，则记录客人点菜，如果参数不是数组则是上菜行为
        if(typeof order === 'array') {
            console.log("记录点菜")
        }
        else {
            console.log("进行上菜")
        }
        Waiter.prototype.doneWork();
    }
}
Waiter.getinstance()
xxx.getinstance()//两个创建出来的单例 是不同的


//动态给this设置属性名称
// list是一个数组对象
    constructor (list) {
        this[list[2].name] = list[2].name;
    }
```

## 19.判断数组对象

`[{xx},{xxx},{xxxx}]`这种形式为数组对象，但是如果你用`typeof`判断是不是数组就很尴尬了，因为无论如何都返回的类型时`object`，正确的做法可以是，判断长度`length`因为对象是没有这个属性的，而数组有。

```javascript
order.length !== undefined && typeof order !== "string" // 别忘了字符串也有长度属性
xxx instanceof Array // 这种方法更简单
```

## 20.工厂模式

[参考](https://segmentfault.com/a/1190000014196851#articleHeader0)

和1**7小节的单例**一样也属于设计模式的一种

工厂模式定义一个用于创建对象的接口，这个接口由子类决定实例化哪一个类 。

工厂模式是用来创建对象的一种最常用的设计模式。我们**不暴露**创建对象的**具体**逻辑，而是将**将逻辑封装在一个**函数中，那么这个函数就可以被视为一个工厂。工厂模式根据抽象程度的不同可以分为：简单工厂，工厂方法和抽象工厂。 

**我的理解**：有一个函数（工厂），你传入需求，返回一个需求的对象，本质上就两种模式`工厂`+`抽象工厂`

### 20.1简单工厂模式(常用)

在实际的项目中，我们常常需要根据用户的权限来渲染不同的页面，高级权限的用户所拥有的页面有些是无法被低级权限的用户所查看。所以我们可以在不同权限等级用户的构造函数中，保存该用户能够看到的页面。在根据权限实例化用户。代码如下： 

```javascript
let UserFactory = function (role) {
  // 角色不同 对应的构造函数不同
  function SuperAdmin() {
    this.name = "超级管理员",
    this.viewPage = ['首页', '通讯录', '发现页', '应用数据', '权限管理']
  }
  function Admin() {
    this.name = "管理员",
    this.viewPage = ['首页', '通讯录', '发现页', '应用数据']
  }
  function NormalUser() {
    this.name = '普通用户',
    this.viewPage = ['首页', '通讯录', '发现页']
  }
// 根据传入的role参数 选择构造函数
  switch (role) {
    case 'superAdmin':
      return new SuperAdmin();
      break;
    case 'admin':
      return new Admin();
      break;
    case 'user':
      return new NormalUser();
      break;
    default:
      throw new Error('参数错误, 可选参数:superAdmin、admin、user');
  }
}

//调用
let superAdmin = UserFactory('superAdmin');
let admin = UserFactory('admin') 
let normalUser = UserFactory('user')
```

==小结==：可以看到对于`UserFactory`这个工厂函数，我们可以传递不同`role`去创建我们想要的实例。

简单工厂的优点在于，你只需要一个正确的参数，就可以获取到你所需要的对象，而无需知道其创建的具体细节。

**但是**在函数内包含了所有对象的创建逻辑（构造函数）和判断逻辑的代码，每增加新的构造函数还需要修改判断逻辑代码。当我们的对象不是上面的3个而是30个或更多时，这个函数会成为一个庞大的超级函数，便得难以维护。所以，简单工厂**只能作用于**创建的**对象数量较少**，**对象的创建逻辑不复杂**时使用。 

### 20.2工厂方法模式

工厂方法模式的本意是**将实际创建对象的工作推迟到子类中**，这样核心类就变成了抽象类。但是在JavaScript中很难像传统面向对象那样去实现创建抽象类。所以在JavaScript中我们只需要参考它的核心思想即可。我们可以将工厂方法看作是一个**实例化对象**的工厂**类**。 （把上面的工厂函数变为工厂类）

------

在简单工厂模式中，我们每添加一个构造函数需要修改两处代码。

现在我们使用工厂方法模式改造上面的代码，刚才提到，工厂方法我们只把它看作是一个实例化对象的工厂，它只**做实例化对象**这一件事情！ 我们采用安全模式创建对象。 

```javascript
//安全模式创建的工厂方法函数
let UserFactory = function(role) {
  if(this instanceof UserFactory) {
    var s = new this[role]();
    return s;
  } else {
    return new UserFactory(role);
  }
}

//工厂方法函数的原型中设置所有对象的构造函数
// 实例化的时候就UserFactory.SuperAdmin 这一步实际上是在构造函数里面的
UserFactory.prototype = {
  SuperAdmin: function() {
    this.name = "超级管理员",
    this.viewPage = ['首页', '通讯录', '发现页', '应用数据', '权限管理']
  },
  Admin: function() {
    this.name = "管理员",
    this.viewPage = ['首页', '通讯录', '发现页', '应用数据']
  },
  NormalUser: function() {
    this.name = '普通用户',
    this.viewPage = ['首页', '通讯录', '发现页']
  }
}

//调用
let superAdmin = UserFactory('SuperAdmin');
let admin = UserFactory('Admin') 
let normalUser = UserFactory('NormalUser')
```

==解释==一下这段代码

```javascript
// 主要的这部分
let UserFactory = function(role) {
  if(this instanceof UserFactory) {
    var s = new this[role]();
    return s;
  } else {
    return new UserFactory(role);
  }
}
//调用
let superAdmin = UserFactory('SuperAdmin');
// 先分析一下实例的过程
调用工厂函数（构造函数）=>this指向全局(调用者是一个普通变量)
    =>进入return new UserFactory(role)这句
        =>上面这句 有点像递归 因为又调用了UserFactory构造函数
        	=>这次调用 因为已经new了 所以this是指向的一个UserFactory实例 
            	=>执行 var s = new this[role]() 返回一个用户
 // 那么安全模式作用在哪
 // 如果是普通的方法 每一次实例用户都要手动new 一个UserFactory，而安全模式省去了这一步
```

上面的这段代码就很好的解决了每添加一个构造函数就需要修改两处代码的问题，如果我们需要添加新的角色，只需要在`UserFactory.prototype`中添加。例如，我们需要添加一个`VipUser`: 

```javascript
UserFactory.prototype = {
  //....
  VipUser: function() {
    this.name = '付费用户',    
    this.viewPage = ['首页', '通讯录', '发现页', 'VIP页']
  }
}

//调用
let vipUser = UserFactory('VipUser');
```

### 20.3抽象工厂模式

```javascript
// 这个函数就是个继承extend函数
let AccountAbstractFactory = function(subType, superType) {
  //判断抽象工厂中是否有该抽象类
  if(typeof AccountAbstractFactory[superType] === 'function') {
    //缓存类
    function F() {};
    //继承父类属性和方法
    F.prototype = new AccountAbstractFactory[superType] ();
    //将子类的constructor指向子类
    subType.constructor = subType;
    //子类原型继承父类
    subType.prototype = new F();

  } else {
    throw new Error('抽象类不存在!')
  }
}
// 以一类为例
//微信用户抽象类
AccountAbstractFactory.WechatUser = function() {
  this.type = 'wechat';// 注意这里不是写在原型上的 而是增加了一个属性WechatUser()构造函数
}
AccountAbstractFactory.WechatUser.prototype = {// 给WechatUser增加一个方法
  getName: function() {// 这就是抽象方法 如果没有覆写这个方法 就会报错
    return new Error('抽象方法不能调用');
  }
}
//普通微信用户子类
function UserOfWechat(name) {
  this.name = name;
  this.viewPage = ['首页', '通讯录', '发现页']
}
//抽象工厂实现WechatUser类的继承
AccountAbstractFactory(UserOfWechat, 'WechatUser');
//子类中重写抽象方法
UserOfWechat.prototype.getName = function() {
  return this.name;
}
//实例化微信用户
let wechatUserA = new UserOfWechat('微信小李');
console.log(wechatUserA.getName(), wechatUserA.type); //微信小李 wechat
let wechatUserB = new UserOfWechat('微信小王');
console.log(wechatUserB.getName(), wechatUserB.type); //微信小王 wechat
```

==小结==：**首先**构造了一个继承函数，**然后**在继承函数的原型上，创造了一个抽象类`WechatUser`的构造函数(抽象类的方法需要覆写)，**随后**将实例对象的任务**交给了**这个抽象类，对于接下来的操作都是基于这个抽象类的，比如上面代码，想创建一个普通微信用户子类，只需要创建好`UserOfWechat`后从`WechatUser`继承，然后覆写抽象类(**为什么覆写**，因为每个子类需要的这个抽象方法功能不同)，最后实例的时候直接`new UserOfWechat`

==有什么用呢==：如果用户结构是这样的

```javascript
普通   vip    普通   vip    普通   vip    
 \      /     \      /      \      /
  \    /       \    /        \    /
    QQ         wechat          weibo
     \            |             /
       \          |            /
         \        |          /
               一个用户
               
// 如果使用普通的工厂模式
// 其实也可以 就是继承关系没有这么明确
// 普通工厂模式 要想加一个vip用户 就得在原型上 增加一个构造函数 
// 所以 构造出来的vip用户 只属于vip类 而不是像抽象工厂一样 直属与vip类 继承与qq (最后其实就是属于qq类)
```



### 20.4使用ES6的方式改写

```javascript
// 简单工厂模式
//User类
class User {
  //构造器
  constructor(opt) {
    this.name = opt.name;
    this.viewPage = opt.viewPage;
  }
  //静态方法
  static getInstance(role) {
    switch (role) {
      case 'superAdmin':
        return new User({ name: '超级管理员', viewPage: ['首页', '通讯录', '发现页', '应用数据', '权限管理'] });
        break;
	// .....为了简短 省去一部分
      default:
        throw new Error('参数错误, 可选参数:superAdmin、admin、user')
    }
  }
}
//调用
let superAdmin = User.getInstance('superAdmin');
```

虽然ES6也没有实现`abstract`，但是我们可以使用`new.target`来模拟出抽象类。

`new.target`指向直接被`new`执行的构造函数，

我们对`new.target`进行判断，如果指向了该类则抛出错误来使得该类成为抽象类。下面我们来改造代码。 

```javascript
class User {
  constructor(name = '', viewPage = []) {
    if(new.target === User) {
      throw new Error('抽象类不能实例化!');
    }
    this.name = name;
    this.viewPage = viewPage;
  }
}

class UserFactory extends User {
  constructor(name, viewPage) {
    super(name, viewPage)
  }
  create(role) {
    switch (role) {
      case 'superAdmin': 
        return new UserFactory( '超级管理员', ['首页', '通讯录', '发现页', '应用数据', '权限管理'] );
        break;
//.........................
      default:
        throw new Error('参数错误, 可选参数:superAdmin、admin、user')
    }
  }
}

let userFactory = new UserFactory();
let superAdmin = userFactory.create('superAdmin');
```

抽象工厂模式并不直接生成实例， 而是用于对产品类簇的创建。我们同样使用`new.target`语法来模拟抽象类，并通过继承的方式创建出`UserOfWechat`, `UserOfQq`, `UserOfWeibo`这一系列子类类簇。使用`getAbstractUserFactor`来返回指定的类簇。 

```javascript
class User {
  constructor(type) {
    if (new.target === User) {
      throw new Error('抽象类不能实例化!')
    }
    this.type = type;
  }
}

class UserOfWechat extends User {
  constructor(name) {
    super('wechat');
    this.name = name;
    this.viewPage = ['首页', '通讯录', '发现页']
  }
}
//....
function getAbstractUserFactory(type) {
  switch (type) {
    case 'wechat':
      return UserOfWechat;
      break;
//..................
    default:
      throw new Error('参数错误, 可选参数:superAdmin、admin、user')
  }
}
let WechatUserClass = getAbstractUserFactory('wechat');// 返回了一个没有new的微信类
let wechatUser = new WechatUserClass('微信小李');// 输入参数 想创建什么微信类
```

## 21.命令模式

[参考](https://www.jianshu.com/p/500219498569)

因此命令模式把请求封装成command对象，这个对象可以在程序中四处传递 ，传递信息。

使得请求**发送者**和请求**接收者**能够**消除**彼此的**耦合**关系 

```javascript
<body> 
    <button></button> 
	<button></button> 
	<button></button> 
	<button></button> 
	<button></button> 
</body>
// 我们想要给这些按钮绑定事件，相当于JS和html分别作为 发送者和请求者
// 假设我们并不知道 应该在哪个按钮绑定什么事件 都是临时决定

// 设置命令函数 （哪个按钮，事件函数）
var setCommand = function (button,func) { 
    button.onclick = function () { 
        func() 
    } 
} 
// 菜单的所有方法
var MenuBar = { 
    refresh : function () { 
        console.log('refresh menu pages') 
    } 
} 
// 刷新菜单的方法
var RefreshMenuBarCommand = function (receiver) { 
    //这里的receiver没体现出用途
    // 加入我们有个movecommand， 我们想向一个方向前进，那么receiver可以是left right..
    return function () { 
        receiver.refresh() 
    } 
} 

var RefreshMenuBarCommand = RefreshMenuBarCommand(MenuBar) setCommand(button1,RefreshMenuBarCommand)
// 撤销很简单
就是再定义一个方法 把初始状态恢复
// 宏命令 //一口气添加很多命令 然后再一起执行
var closeDoorCommand = { 
    execute:function () { 
        console.log('closedoor') 
    } 
}; 
var openPcCommand = { 
    execute : function () { 
        console.log('open PC') 
    } 
}; 
var openQQCommand = { 
    execute : function () { 
        console.log('login qq') 
    } 
}; 
var MacroCommand = function () { 
    return { 
            commandsList : [],

            add: function (command) { // 把很多命令存到列表里
                this.commandsList.push(command) 
            },

            execute:function () { // 调用execute方法就依次执行
                let command = this.commandsList;
                for(var i = 0; command[i]; i++){ 
                    command[i].execute();
                } 
            } 
        } 
} 
var macroCommand = MacroCommand(); 
macroCommand.add(closeDoorCommand) 
macroCommand.add(openPcCommand) 
macroCommand.add(openQQCommand) 
macroCommand.execute()
```

## 22.责任链模式

[参考](https://www.cnblogs.com/editor/p/5679552.html)

它是一种**链式结构**，每个节点都有可能两种操作，**要么**处理该请求停止该请求操作 **要么**把请求转发到下一个节点，让下一个节点来处理请求 。

```javascript
// 处理对象构造函数 
// 给不同的节点使用 让节点创造自己的处理对象
function Handler() {
    this.next = null;
    this.setNext = function(_handler) {
        this.next = _handler;
    };
    this.handleRequest = function(money) {
    }
};

//采购部
function CGBHandler = function() {}
CGBHandler.prototype = new Handler();
// 覆写handleRequest
CGBHandler.prototype.handleRequest = function(money) {
     //处理权限最多10000
    if (money < 10000){
        console.log('同意');
    } else {
        console.log('金额太大，只能处理一万以内的采购');
        if (this.next) {// setnext在末尾 调用的适合有设置
            this.next.handleRequest(money);
        }
    }
};

//总经理
function ZJLHandler = function() {}
ZJLHandler .prototype = new Handler();
ZJLHandler .prototype.handleRequest = function(money) {
     //处理权限最多100000
    if (money < 100000){
        console.log('10万以内的同意');
    } else {
        console.log('金额太大，只能处理十万以内的采购');
        if (this.next) {
            this.next.handleRequest(money);
        }
    }
};

//董事长
function DSZHandler = function() {}
DSZHandler .prototype = new Handler();
DSZHandler .prototype.handleRequest = function(money) {
     //处理权限至少100000
    if (money >= 100000){
        console.log('10万以上的我来处理');
        //处理其他逻辑
    } 
};

//调用
function Client() {
    var cgb = new CGBHandler();
    var zjl = new ZJLHandler();
    var dsz = new DSZHandler();
    cgb.setNext(zjl);
    zjl.setNext(dsz);
    
    cgb.handleRequest(800000);
}
```

## 23.适配器模式

```javascript
function interfaceMethod(str1, str2, str3) {//本来需要三个单独的字符串做输入
     //...
 }
 var clientObject = {// 但是输入是一个对象
     string1: 'foo',
     string2: 'bar',
     string3: 'baz'
 };
 // 所以用一个适配器包裹 使得能够适配
 function clientToInterfaceAdapter(o) {
     interfaceMethod(o.string1, o.string2, o.string3); 
}
clientToInterfaceAdapter(clientObject);
```

==如果==更新了代码后，发现需要更改另一处的代码，为了避免更改，可以使用适配器，也就是在外面包裹一层，使得输入符合新的代码

## 24.桥接模式

[参考1](https://www.cnblogs.com/TomXu/archive/2012/04/19/2437321.html)

`抽象`与`具体实现`分离。

```javascript
// 以forEach为例 
var forEach = function (arr, fn) { // 这个forEach函数就是一个  抽象
    for (var i = 0; i < arr.length; i++) {
        var val = arr[i];
        if (fn.call(this, i, val, arr)) {
            return false;
        }
    }
}
var arr = [1, 2, 3, 4];
forEach(arr, function (i, v) { // 这里的function就是具体实现
    arr[i] = v * 2;
})
// 所以我们不需要给各种forEach方法单独设置，我们就设置一个抽象的
```

## 25.观察者模式

```javascript
var pubsub = {};   // 定义发布者

(function (q) {

    var list = [],  //回调函数存放的数组，也就是记录有多少人订阅了我们东西
        subUid = -1;

    // 发布消息,遍历订阅者
    q.publish = function (type, content) {
        // type 为文章类型，content为文章内容
        
        // 如果没有人订阅，直接返回
        if (!list[type]) {

            return false;
        }

        setTimeout(function () {
            var subscribers = list[type],
                len = subscribers.length;

            while (len--) {
                // 将内容注入到订阅者那里
                subscribers[len].func(type, content);
            }
        }, 0);

        return true;

    };
    //订阅方法，由订阅者来执行
    q.subscribe = function (type, func) {
        // 如果之前没有订阅过
        if (!list[type]) {
            list[type] = [];
        }

        // token相当于订阅者的id，这样的话如果退订，我们就可以针对它来知道是谁退订了。
        var token = (++subUid).toString();
        // 每订阅一个，就把它存入到我们的数组中去
        list[type].push({
            token: token,
            func: func
        });
        return token;
    };
    //退订方法
    q.unsubscribe = function (token) {
        for (var m in list) {
            if (list[m]) {
                for (var i = 0, j = list[m].length; i < j; i++) {
                    if (list[m][i].token === token) {
                        list[m].splice(i, 1);
                        return token;
                    }
                }
            }
        }
        return false;
    };

} (pubsub));

//将订阅赋值给一个变量，以便退订
var girlA = pubsub.subscribe('js类的文章', function (type, content) {
    console.log('girlA订阅的'+type + ": 内容内容为：" + content);
});
var girlB = pubsub.subscribe('js类的文章', function (type, content) {
    console.log('girlB订阅的'+type + ": 内容内容为：" + content);
});
var girlC = pubsub.subscribe('js类的文章', function (type, content) {
    console.log('girlC订阅的'+type + ": 内容内容为：" + content);
});

//发布通知
pubsub.publish('js类的文章', '关于js的内容');  
// 输出：
// girlC订阅的js类的文章: 内容内容为：关于js的内容
// test3.html:78 girlB订阅的js类的文章: 内容内容为：关于js的内容
// test3.html:75 girlA订阅的js类的文章: 内容内容为：关于js的内容


//girlA退订了关于js类的文章 
setTimeout(function () {
    pubsub.unsubscribe(girlA);
}, 0);

//再发布一次，验证一下是否还能够输出信息
pubsub.publish('js类的文章', "关于js的第二篇文章");
// 输出：
// girlB订阅的js类的文章: 内容内容为：关于js的第二篇文章
// girlC订阅的js类的文章: 内容内容为：关于js的第二篇文章
```

## 26.EventBus

观察者：一个发，其他都被动接收

事件总线：一个发，其他的主动接收

```javascript
(function(){
    //创建EventBus对象
    EventBus = function () {
        console.log("maps init...");
    };
    //准备数组容器
    var objBus = [],arrbus = [];
    //添加方法
    EventBus.prototype = {
        obj : {
            set : function(key,action){
                if(key && action){
                    var map = {};
                    map.k = key;
                    map.v = action;
                    //如果存在，则删除之前添加的事件
                    for(var i = 0,busLength = objBus.length;i < busLength;i ++){
                        var tempMap = objBus[i];
                        if(tempMap.k == key){
                            objBus.splice(i,1);
                        }
                    }
                    objBus.push(map);
                }
            },
            get : function(key){
                if(key){
                    for(var i = 0,busLength = objBus.length;i < busLength;i ++){
                        var map = objBus[i];
                        if(map.k == key){
                            return map.v();
                        }
                    }
                }
            }
        },
        arr : {
            push : function (key,action) {
                if(key && action){
                    var map = {};
                    map.k = key;
                    map.v = action;
                    arrbus.push(map);
                }
            },
            pop : function (key) {
                if(key){
                    for(var i = 0,busLength = arrbus.length;i < busLength;i ++){
                        var map = arrbus[i];
                        if(map.k == key){
                            map.v();
                        }
                    }
                }
            }
        }
    }
})();
function testObj(){
        eventBus.obj.set('event1',function(){
            console.log('event1');
        });
        eventBus.obj.set('event1',function(){
            console.log('event2');
        });
        eventBus.obj.set('event3',function(){
            console.log('event3');
        });

        eventBus.obj.get('event1');
    }

    //测试obj
    testObj();
event2

function testArr() {
        eventBus.arr.push('event1',function(){
            console.log('event1');
        });
        eventBus.arr.push('event1',function(){
            console.log('event2');
        });
        eventBus.arr.push('event3',function(){
            console.log('event3');
        });

        eventBus.arr.pop('event1');
    }

    //测试arr
    testArr();

event1
event2
```

## 27.set对象

去除数组多余元素

```javascript
let arr = [1, 1, 2, 3, 4];
new Set(arr) => [1, 2, 3, 4];
//注意 输出是一个set对象
// 转换为数组可以使用扩展符[...new Set(arr)]
```

## 28. 异步操作

```javascript
//想要封装一个延迟函数
function delay(ms) {
    return new Promise(resolve=>{
        setTimeout(()=>{resolve()}, ms)
    })
}
async function aler() {
    await delay(5000);
    console.log('1')
}----->  5秒后输出1
// 但是这样写就是错误的
function delay(ms) {
    setTimeout(()=>{
        return new Promise(resolve=>{
            console.log(2)
            resolve();
        })
    },ms)--------> 马上输出1,5秒后输出2
}

/*错误的原因是 返回promise对象被放在了setTimeout里面 
  而定时器是异步函数，当程序运行到await delay(5000)这一行时，跳过了这句直接运行了下一句
  所以使用promise尽量直接就return promise 不要嵌套其他异步函数*/
```

## 29. `null` 与`undefined`区别

- `null`实际上指的是空指针，`undefined`指的是没有值
- `typeof` `null` 是`object` 而`undefined`是`undefined`
- `Number()`方法转换，`null`返回0，`undefined`返回`NaN`

## 30. 循环遍历中的`let`和`var`

```javascript
// 循环注册事件的时候会有如下问题
let eleList = document.querySeletorAll('#ele');
for(var i = 0; i < eleList.length; i++) {
    eleList[i].onclick = function() {
        alert(i) //这里使用了var声明循环变量 然而点击元素时永远都只显示eleList.length这个数值
    }
}
// 首先，var没有块级作用域，所以这里的i是一个全局变量
// 然后，定义的事件函数是一个全局函数
// 所以，当调用事件函数的时候，该函数会使用全局变量i，也就是eleList.length
// 因为let有块级作用域，所以能避免这种情况

// 或者在外部在包裹一个函数,让事件函数使用局部变量而不是全局的
for(var i = 0; i < eleList.length; i++) {
    (function () {// 立即执行的匿名函数
        var index = i;// 保存一个局部变量
        eleList[i].onclick = function() {
            alert(index)
        }
    })()
}
```

## 31. `offsetWidth`和`offsetHeight`

```javascript

```



