# :strawberry:Vue实例 

---

## 1. 简单生成  

1. 当引入vue包后

   ```javascript
   // 就可以实例化对象 一般只有一个根对象
   new Vue({配置选项}
   ```

2. 实例的时候需要传入配置选项

   ```xml
   <!--这是index.html-->
     <body>
       <div id="app"> </div>
     </body>
   ```

   

   ```xml
   <!--这是一个单页面组件-->
   <template>
       <div class="a">{{a}}</div>  <!--只能有一个根层级 也就是一个div-->
   </template>
   <script>
       export default {
           data() {  <!--用return返回data 防止其他元素引用这个组件时 改变对象值 产生混乱-->
               return {
                   a:'hello',
               }
           },
       }
   </script>
   <style scoped>  <!--scoped 代表这个样式是局域的-->
       .a {
           width: 200px;
           height: 200px;
           background-color: #4ff198;
       }
   </style>
   ```

   

   ```javascript
   import Vue from 'vue'  // 引入vue
   import app from './App' // 引入组件 实际上就是引入了实例的选项参数
   
   new Vue ({ 		
       el:"#app",  // 在index.html中的哪个元素位置导入组件 覆盖这个元素
       render: (create)=>{return create(app)}  // 渲染组件 create参数制造一个Vue实例
   });
   ```

## 2. data注意  

1. **data(){return {} }**

		如上面所说，因为所有组件如果引用同一个data对象，那么都指向同一个内存，一个改变其他的都改变。

		在模板中要是用的变量，一定要在data中声明了才有效

	2. **data的改变条件**

```javascript
// 我们可以通过全局的变量来取值
let vm = new Vue ({
    data:{// 这里没有使用 函数来取值 为了方便在外面取值
        a:1,
    },
})
// 在外面我们可以
data.a // 输出1
vm.a // 输出1
vm.a = 2 // a被修改为2 Vue对象也会改变
```

==注意==：只有在**实例**vue对象**的时候**，里面的`data`才是响应式的，如果在实例化完成后，增加一个变量`vm.b=2`是**无效的**。

3. **如果要增加属性**    

   ```javascript
   var obj = {
     foo: 'bar'  // 要增加的值 设置为对象
   }
   
   Object.freeze(obj)  // 这个方法是阻止对象属性改变
   
   new Vue({
     el: '#app',
     data: obj  // 将值赋予
   })
   ```

## 3. 生命周期与钩子函数  

​	每个实例被创建的都有一系列的过程（生命周期），在这些过程中的某些阶段，可以通过特定的函数接口（钩子函数）操作。

==生命周期钩子自动绑定了this，所以不能使用箭头函数来声明一个钩子函数==

[生命周期图示](.\lifecycle.png)

:arrow_up_small: 实例对象前beforeCreated：

:arrow_up_small: 实例对象后created：

:star: 模板被渲染前beforeMount:

⭐️ 模板被渲染后mounted:

:boxing_glove:模板数据被更新前beforeUpdate:

:boxing_glove:模板数据被更新后updated:

:arrow_double_down:实例被销毁前(vm.$destroy())beforeDestroy:

:arrow_double_down:实例被销毁后destroyed:

```javascript
new Vue({
  data: {
    a: 1
  },
  created: function () {  // 在实例被创造前
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  }
})
// => "a is: 1"
```

==注意==：在钩子函数里，或者任何**需要指向当前实例**的情况下，都**不能使用箭头函数**，因为箭头函数的this会指向调用者的上下文环境

# :banana:模板语法  

---

## 1 . 输出{{}}  

```xml
<!--使用了v-once指令 只会绑定一次-->
<span v-once>这个将不会改变: {{ msg }}</span>
<!--输出中可以使用js表达式  但是只能有单个表达式-->
<!--下面的不可以被解析-->
<!-- 这是语句，不是表达式 -->
{{ var a = 1 }}
<!-- 流控制也不会生效，请使用三元表达式 -->
{{ if (ok) { return message } }}
<!--这些可以被解析-->
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```

### 

## 2 . v-text  v-html  

**绝不要**对用户提供的内容使用插值。 

```xml
<p>Using mustaches: {{ rawHtml }}</p>
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
<!--相当于innerText和innerHtml-->
```

### 	

## 3 . v-bind：  

因为胡子{{}}语法不能作用在属性上，如`id="{{a}}"`、`vlue={{b}}`都是不可以的，只能通过`v-bind`简写为`：`，而且胡子语法和v-bind**不能混用**  

实际上v-bind通知编译器，它绑定的属性后面的值**不是一个字符串**而是一个**js表达式（变量）**

```xml
<div v-bind:id="dynamicId"></div>
<div v-bind:style="{font-size: size + '1em'}"></div>
```

---

# 🍎 指令  

---

带v-前缀的，指令的职责是，当表达式的值改变时，将其产生的连带影响，响应式地作用于 DOM。

## 1 . 参数 ：  

在指令后面用`:`表示，告知指令与哪一个属性/参数绑定，当这个属性/参数变化时，执行指令动作。

```xml
<a v-bind:href="url">...</a>
<a v-on:click="doSomething">...</a>
```



## 2. 修饰符  .  

告诉指令应该以什么方式绑定属性  

```xml
<form v-on:submit.prevent="onSubmit">...</form>
<!--这里相当于阻止提交的默认事件-->
```

# :orange:计算属性和监听器  

## 1 .  基础例子   

当**computed属性**里面的**方法**所**包含的属性**改变时 就会触发这个方法

实际上就是一个`getter`，访问这个属性的时候就会触发`getter `

```xml
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p> <!--这里就调用计算属性里面的方法-->
</div>
```

```javascript
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {  // computed是 实例Vue的一个配置属性
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('') // 当message变化时触发计算
    }
  }
})
```

## 2 . 和其他方法的对比  

也可以使用methods 属性定义方法，`{{aa()}}`调用，是一样的结果。

==但是==computed是依赖于它所绑定的元素的，如多次访问方法`a(){return Date.now()}`，返回的值肯定都不一样，而多次访问绑定了`data：{a:Date.now}`的计算属性时，只要a没有更新他就不会更新

==而且==computed，只要在他的方法里面写的所有的变量，以改变都能触发，但是watch只能监听一个变量

## 3 . 侦听器 watch  

​	一个更通用的监听数据变化的配置属性，但是**不能滥用**，合适的情况下应该使用计算属性

​	侦听器还适合处理一步操作，如ajax请求

```javascript
export default {
    data() {
        return {
            a:0,
            cc:0,
        }
    },
    watch: {// 有两个参数 新数据和旧数据
        a (newVal, oldVal) {  // 监听这个实例的a属性值 
            this.cc = oldVal 
        }
    },
    // 更好的应该这种写法 更直观
    a:function (newVal, oldVal){}
    // 还可以设置多个函数
    a[fn1, fn2]
}

//但是
//监听的数据时一个 c = [{name:xx, age:oo},{name:xx, age:oo},{name:xx, age:oo}]
//直接监听是无法响应 深层的变化的 比如我改变第三个人的年龄 watch不会有反应
//所以要开启深度侦听
    c: {
      handler: function (val, oldVal) { /* ... */ },
      deep: true
    }
//你也可以监听其中的某一个值
'c[1].name':fn  //记得名称加上引号
```

# :pizza:CSS与style绑定  

## 1. 绑定对象  CSS 

```xml
<p v-bind:css = "{a:isShow, b:isError}"></p>
<!--v-bind绑定的样式 和直接设置css属性的样式不冲突-->
<!--这里引入了两个样式 a和b 且控制他们显示与否 与变量isShow、isError有关-->

<!--这里的a、b 一定要规定好true/false 不然会无法显示-->

<div v-bind:class="classObject"></div>  <!--也可这样-->
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
<!--也可以在计算属性里面操作-->
<div v-bind:class="classObject"></div>
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,  <!--当isActive、error变化时触发-->
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

## 2. 绑定数组  CSS

所以建议无论对象还是数组 都指定到data里

```xml
<div v-bind:class="[activeClass, errorClass]"></div>
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
<!--也可以这样控制显示样式-->
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```



## 3. 为子组件绑定CSS

​	会应用到子组件的根元素上

## 4. 绑定对象style

```xml
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
data: {
  activeColor: 'red',
  fontSize: 30
}
<!--也可以直接绑定完整的对象-->
<div v-bind:style="styleObject"></div>
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
<!--同样和css一样可以结合计算属性-->
```

## 5. 绑定数组style

```xml
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```



---

# :cherries:条件渲染 

---

## 1. v-if 

当`v-if="false"`,相当于**移除了这个元素**，页面中不存在这个元素(dom树中就不在，直接被删除)，**不占位置**。

当初始就是`false`时不会渲染这个部分。

```xml
<!--可以在template标签上加上条件渲染 实现分组渲染-->
<template v-if="true"></template>
<template v-if="false"></template>
<!--可以和else、else-if一起--> <!--一定要挨在一起-->
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```



## 2. v-show

当`v-show="false"`，页面中**隐藏元素**，相当于`display=‘none’`，**不占位置**，无论初始条件如何都会渲染。

`v-show`不支持`template`元素（只能对原生标签生效），也不能和`v-else`搭配

## 3. 其他注意点

**首先**因为vue设计上，为了保证渲染的高速，两个一样的元素**相互切换**，默认只会修改不同的属性，其他的却保留

```xml
<template v-if="loginType === 'username'">
  <input placeholder="Enter your username">
</template>
<template v-else>
  <input placeholder="Enter your email address">
</template>
<!--在两个template中切换 可以看到有相同的input标签 只是placeholder不同-->
<!--所以在切换的时候 输入的内容不会改变 而只改placeholder 也就是只改变不同的地方  而不是重新渲染-->
```

**但是**有时候我们并不需要这种特性，所以用 `key` 管理可复用的元素

```xml
<template v-if="loginType === 'username'">
  <input placeholder="Enter your username" key="xxx">
</template>
<template v-else>
  <input placeholder="Enter your email address" key="ooo">
</template>
<!--给不需要复用的元素 赋予key属性 这样他们就是独一无二的 每次都会重新渲染-->
```

---

# :cheese:列表渲染

---

## 1. 基本用法 

```xml
<li v-for="(item,index) in c">  <!--可以用of代替in-->
    {{item}}+{{index}}
</li>
<li v-for="(value, key, index) in c">  <!--迭代对象的时候 参数为值、键、index-->
    {{value}}+{{key}}+{{index}}
</li>
	    
data() {
        return {
            c:[1, 2, 3, 4, 5], // 可以迭代数组
		   d:{} // 也可以迭代对象
        }
    },
<!--也可以v-for in 10-->
<!--可以在template上使用-->
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider"></li>
  </template>
</ul><!--相当于多次渲染这个组-->
```

`v-if`和`v-for`嵌套，并列写的话， `v-for`优先级更高

```xml
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
<!--相当于-->
for(todo of todos){
	if(!todo.isComplet)....
}
<!--如果想反过来 可以将if嵌套在外面-->
<ul v-if="todos.length">
  <li v-for="todo in todos">
    {{ todo }}
  </li>
</ul>
```



## 2.key

和之前的`v-if`切换的复用规则一样，`v-for`的迭代对象发生了改变，vue也不会重头渲染。

在vue2.x中强制使用key

```xml
        <li v-for="(item,index) in arr" :key="index"> <!--动态绑定key属性的值为index-->
            {{item}}+{{index}}
        </li>
```



## 3.动态修改列值

使用`a[1]='x'`直接赋值或者`a.length = 2`更改长度都不能触发列表更新

如果需要操作可以用如下方法代替

```javascript
// Vue.set 修改值
Vue.set(修改目标, 位置, 新值)
//别名 vm.$set()
// 使用数组对象的方法splice
[].splice(开始, 结束, 新值)
```



和js很像，js中有些数组方法会改变原来数组如:

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

这些方法都会触发列表渲染的改变

对于那些不改变原数组的map，forEach..可以直接返回一个新数组给原数组赋值

```javascript
            this.c = this.c.filter(function (item) {  // 我们需要改变c的值 
                return item === newVal})
```

## 4.对象更改检测

因为js不能对对象的更改进行检测，所以这样做是不行的

```javascript
var vm = new Vue({
  data: {
    a: 1
  }
})
// `vm.a` 现在是响应式的
vm.b = 2
// 因为无法检测增加了新的属性b，所以`vm.b` 不是响应式的
```

为了加入一个响应的属性，可以使用`Vue.set`

```javascript
var vm = new Vue({
  data: {
    userProfile: {
      name: 'Anika'
    }
  }
})
Vue.set(vm.userProfile, 'age', 27) // 这样就新增加了一个属性age
vm.$set(vm.userProfile, 'age', 27) // 等价

// 如果想增加多个属性
// 不要这么做
Object.assign(vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})// 这样会覆盖
// 应该这样做  创建一个新的对象 加入原对象  这里的后两个参数是源对象 全部合并到第一个参数{}
vm.userProfile = Object.assign({}, vm.userProfile,{
  age: 27,
  favoriteColor: 'Vue Green'
})

```

---

# :peach:事件监听

---

## 1.  基础用法 

```xml
<button v-on:click="counter += 1">Add 1</button> <!--可以简写为@click-->
new Vue({
  el: '#example-1',
  data: {
    counter: 0
  }
})
```

或者把事件函数写入配置属性里

```javascript
<div id="example-2">
  <!-- `greet` 是在下面定义的方法名 -->
  <button v-on:click="greet">Greet</button>
</div>

var example2 = new Vue({
  el: '#example-2',
  data: {
    name: 'Vue.js'
  },
  // 在 `methods` 对象中定义方法
  methods: {
    greet: function (event) {// 还能使用原生的事件参数
      // `this` 在方法里指向当前 Vue 实例
      alert('Hello ' + this.name + '!')
      // `event` 是原生 DOM 事件  
      if (event) {
        alert(event.target.tagName)
      }
    }
  }
})
// 也可以用 JavaScript 直接调用方法
example2.greet() // => 'Hello Vue.js!'
```

在原生js里面，你想触发一个含有多个参数的方法，一般这样写

```javascript
element.onClick = function (e) {
    handle(aaa, bbb, ccc, e)  // 通过套一层匿名函数来传递事件参数
} 
```

而在vue可以这样

```xml
<!--warn事件函数就有两个参数，第二个参数使用特殊变量传递事件参数-->
<button v-on:click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>
```

```javascript
// ...
methods: {
  warn: function (message, event) { 
    // 现在我们可以访问原生事件对象
    if (event) event.preventDefault()
    alert(message)
  }
}
```

## 2. 事件修饰符

为`v-on`提供的修饰符

- `.stop`  阻止冒泡

- `.prevent ` 阻止默认事件

- `.capture` 捕获模式

- `.self`

- `.once`

- `.passive`

  ```xml
  <!-- 修饰符可以串联 -->
  <a v-on:click.stop.prevent="doThat"></a>
  
  <!-- 只有修饰符 -->
  <form v-on:submit.prevent></form>
  
  <!-- 添加事件监听器时使用事件捕获模式 -->
  <!-- 即元素自身触发的事件先在此处处理，然后才交由内部元素进行处理 -->
  <div v-on:click.capture="doThis">...</div>
  
  <!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
  <!-- 即事件不是从内部元素触发的 -->
  <div v-on:click.self="doThat">...</div>
  
  <!-- 点击事件将只会触发一次 -->
  <a v-on:click.once="doThis"></a>
  
  <!-- 滚动事件的默认行为 (即滚动行为) 将会立即触发 -->
  <!-- 而不会等待 `onScroll` 完成  -->
  <!-- 这其中包含 `event.preventDefault()` 的情况 -->
  <!--不要把 .passive 和 .prevent 一起使用-->
  <div v-on:scroll.passive="onScroll">...</div>
  ```

## 3.按键修饰符

```xml
<!-- 只有在 `keyCode` 是 13 时调用 `vm.submit()` -->
<input v-on:keyup.13="submit">
<!-- 同上 -->
<input v-on:keyup.enter="submit">
<!-- 缩写语法 -->
<input @keyup.enter="submit">
```

除了vue内置的名称：

- `.enter`
- `.tab`
- `.delete` (捕获“删除”和“退格”键)
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

还可以自定义键名

```javascript
// 可以使用 `v-on:keyup.f1`
// 使用全局对象
Vue.config.keyCodes.f1 = 112
```

你也可直接将事件对象里面的 `e.key`的名字转换为 kebab-case 来作为修饰符： 

```xml
<!--$event.key === 'PageDown'-->
<input @keyup.page-down="onPageDown">
```

## 4.系统修饰键

表示，只有按下这些键之后才能出发事件函数

- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

```xml
<!-- Alt + C -->
<input @keyup.alt.67="clear">

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```



===注意==修饰键与常规按键不同，在和 `keyup` 事件一起用时，事件触发时修饰键必须处于按下状态。换句话说，只有在按住 `ctrl` 的情况下释放（keyup）其它按键，才能触发 `keyup.ctrl`。而单单释放 `ctrl` 也不会触发事件。如果你想要这样的行为，请为 `ctrl` 换用 `keyCode`：`keyup.17`。 

## 5.  .exact

​	控制**==系统==修饰符**触发的

```xml
<!-- 即使 ctrl 和其他按键一同按下时也会触发 -->
<button @click.ctrl="onClick">A</button>

<!-- 有且只有 Ctrl 被按下的时候才触发 -->
<button @click.ctrl.exact="onCtrlClick">A</button>

<!-- 没有任何  系统修饰符   被按下的时候才触发 -->
<button @click.exact="onClick">A</button>
```

## 6.鼠标按钮修饰符

- `.left`
- `.right`
- `.middle`

---

#  🍐表单输入与绑定

---

​	可以用 `v-model` 指令在表单 `<input>` 及 `<textarea>` 元素上创建双向数据绑定。

​         `v-model` 会**忽略**所有表单元素的 `value`、`checked`、`selected` 特性的**初始值**而总是将 Vue 实例的数据作为数据来源。应该通过 JavaScript 在组件的 `data` 选项中声明初始值。

​	对于需要使用[输入法](https://zh.wikipedia.org/wiki/%E8%BE%93%E5%85%A5%E6%B3%95) (如中文、日文、韩文等) 的语言，你会发现 `v-model` 不会在输入法组合文字过程中得到更新。如果你也想处理这个过程，请使用 `input` 事件。

可以通过注册`compositionstart`和`compositionend`事件判断有无输入

## 1.基础用法

### 1.1文本

```xml
<!--文本-->
<input v-model="message" placeholder="edit me">
<p>Message is: {{ message }}</p>
    
<!--多行文本-->
<span>Multiline message is:</span>
<p style="white-space: pre-line;">{{ message }}</p>
<br>
<textarea v-model="message" placeholder="add multiple lines"></textarea>
<!--在文本区域内部加入值 (<textarea>这里</textarea>) 并不会生效，应用 v-model 来代替。-->
```

### 1.2 多选

```html
<input type="checkbox" id="checkbox" v-model="checked">
<label for="checkbox">{{ checked }}</label>  <!--绑定的是布尔值-->

<!--多个复选框可以绑定到一个数组-->
<div id='example-3'>
    <input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
    <input type="checkbox" id="john" value="John" v-model="checkedNames">
    <input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
    <br>
    <span>Checked names: {{ checkedNames }}</span>
</div>
new Vue({
  el: '#example-3',
  data: {
    checkedNames: []  <!--这样checkedNames就会有所有被选中的value-->
  }   <!--如果checkedNames设置为true/false 那么只会返回true/false-->
})

<input
  type="checkbox"
  v-model="toggle"
  true-value="yes"  <!--使用true-value 和 false-value 可以修改true/fasle名字-->
  false-value="no"
> <!--所以当选中时 不会是true 而是 yes-->
```

==总结==当checkedNames为**数组**才能接收checkbox的value，否则无论如何都是**布尔值**

### 1.3单选

不可以获取布尔值

```html
<div id="example-4">
  <input type="radio" id="one" value="One" v-model="picked">
  <input type="radio" id="two" value="Two" v-model="picked"> <!--注意这里没有给相同name-->
  <span>Picked: {{ picked }}</span>                          <!--但是仍然可以实现单选-->
</div>                                        <!--如果没有设置picked 就不能单选-->
new Vue({
  el: '#example-4',
  data: {
    picked: ''
  }
})
 <!--因为只有多选才有true和false 所以这里可以绑定value为一个变量a-->
<!--选择的时候就会返回这个变量的值-->
<input type="radio" v-model="pick" v-bind:value="a">
```

### 1.4选择框

```xml
<!--单选时-->
<div id="example-5">
  <select v-model="selected">
    <option disabled value="">请选择</option>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <span>Selected: {{ selected }}</span>
</div>
<!--多选时 和checkbox一样 可以绑定一个数组-->
<!--选项可以用v-for渲染-->
  <option v-for="option in options" v-bind:value="option.value">
    {{ option.text }}
  </option>

<!--和上面一样 也可以绑定变量-->
<!--在被选中后 selected的值包含一个对象-->
<select v-model="selected">
    <!-- 内联对象字面量 -->
  <option v-bind:value="{ number: 123 }">123</option>
</select>
```

```javascript
new Vue({
  el: '...',
  data: {
    selected: ''
  }
})

//如果 v-model 表达式的初始值未能匹配任何选项，<select> 元素将被渲染为“未选中”状态。在 iOS 中，这会使用户无法选择第一个选项。因为这样的情况下，iOS 不会触发 change 事件。
//因此，更推荐像上面这样提供一个值为空的禁用选项。
```

## 2. V-Model 修饰符

也可以说是表单控件的修饰符

`.lazy`

和onchange事件一样，只有在**输入完成后**(输入完了按回车 失去焦点...)，才会更新数据

```html
<!-- 在“change”时而非“input”时更新 -->
<input v-model.lazy="msg" >
```

`.number`

将输入转为数字

```html
<input v-model.number="age" type="number">
```



`.trim`

去除前后空格

```html
<input v-model.trim="msg">
```

---

# :watermelon:组件 

---

[通常组件层级结构](C:\Users\dell\29237\笔记\1527935267236.png)

## 1.组件注册

### 1.1 全局注册

在所有组件都可以使用全局定义的组件，包括他们自己的内部也可以相互使用

```javascript
Vue.component('my-component-name', {
  // ... 选项 ..和其他模板一样
})
//当你引用了一个单文件组件
Vue.component('name', 组件)
// 在html中引用时 直接写标签
<my-component-name></my-component-name>
```

### 1.2 局部注册

就和之前的各个vue文件一样，可以多编写几个vue文件然后

```javascript
// 在想要引入组件的地方
import xxx from './xxx'
export default {
    //...
    components:{
        ooo:xxx //将引入的组件放入这个配置属性中 一般来说不会改名字
    }
}
// 在html中就可以 直接引用了 
<ooo></ooo>
```



### 1.3 组件名规范

因为html中是不区分大小写的，所以一般使用`-`来作为连接符，所以在js中使用驼峰，而在html中对应的使用如`my-component-name`的命名方法

## 2.子父组件传递数据

[子父组件传递数据](C:\Users\dell\29237\笔记\1527936139992.png)

## 2.1 父组件----->子组件传递

传递的方式很简单，就是像普通html标签一样，拥有一个**自定义的属性**，只要在父组件中设置了这个属性的值，那么对应的子组件的值也会改变。

在子组件中

```xml
<template>
  <div class="hello">
    <h1>{{ msgA }}</h1>  
  </div>
</template>
```

```javascript
export default {
  name: 'HelloWorld',
  data () {
    return {
    }
  },
  props:['msgA']  // 使用props配置属性 可设置多个自定义属性
}				// 这里设置了一个msgA属性
```

在父组件中

```javascript
import hello from './components/HelloWorld'
    //....
	components:{
        hello  // 载入组件hello 值和键一样的名字
    }
	//...
```

```xml
<!--假设这个子组件取名hello-->
<hello msg-a="hello!!!!"></hello>  <!--假如设置的属性名称是驼峰命名 在这里必须要转成用-连接-->
<!--最终在子组件的h1位置会出现"hello!!!!"-->
```

**任何类型**的数据都可以传给自定义属性，前提是使用**v-bind绑定**这个属性才能加上变量或者表达式

```html
<!-- 即便 `42` 是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post v-bind:likes="42"></blog-post>

<!-- 用一个变量进行动态赋值。-->
<blog-post v-bind:likes="post.likes"></blog-post>

<!-- 包含该 prop 没有值的情况在内，都意味着 `true`。-->
<blog-post favorited></blog-post>

<!-- 即便 `false` 是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<base-input v-bind:favorited="false">

<!-- 用一个变量进行动态赋值。-->
<base-input v-bind:favorited="post.currentUserFavorited">

<!-- 即便数组是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post v-bind:comment-ids="[234, 266, 273]"></blog-post>

<!-- 用一个变量进行动态赋值。-->
<blog-post v-bind:comment-ids="post.commentIds"></blog-post>

<!-- 即便对象是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post v-bind:comments="{ id: 1, title: 'My Journey with Vue' }"></blog-post>

<!-- 用一个变量进行动态赋值。-->
<blog-post v-bind:post="post"></blog-post>
    

```

如果你想要将一个**对象**的所有属性都作为 prop 传入，你可以使用不带参数的 `v-bind` (取代 `v-bind:prop-name`)。例如，对于一个给定的对象 `post`：

```javascript
post: {
  id: 1,
  title: 'My Journey with Vue'
}
```

下面的模板：

```html
<blog-post v-bind="post"></blog-post>
<!--代替如下写法 注意是不绑定：属性-->
<blog-post v-bind:props = "post"></blog-post>
```

等价于：

```html
<blog-post
  v-bind:id="post.id"
  v-bind:title="post.title"
></blog-post>
```

==注意==因为保证数据流动是可理解的，props传递数据时单向的，所以**应该避免直接在子组件里改变属性值**，这会触发vue发出警告。比如：传入的是数组或者对象，因为js的特性，那么在子组件改变这个数组或者对象时，父组件的对应变量也会改变。

### props验证：

```javascript
Vue.component('my-component', {
  props: {
    // 基础的类型检查 (`null` 匹配任何类型)
    propA: Number,
    // 多个可能的类型
    propB: [String, Number],
    // 必填的字符串
    propC: {
      type: String,
      required: true
    },
    // 带有默认值的数字
    propD: {
      type: Number,
      default: 100
    },
    // 带有默认值的对象
    propE: {
      type: Object,
      // 对象或数组且一定会从一个工厂函数返回默认值
      default: function () {
        return { message: 'hello' }
      }
    },
    // 自定义验证函数
    propF: {
      validator: function (value) { // validator是特定属性
        // 这个值必须匹配下列字符串中的一个
        return ['success', 'warning', 'danger'].indexOf(value) !== -1
      }
    }
  }
})
```

**还可以自定义验证**

```javascript
function Person (firstName, lastName) {  // 实际上是个构造函数
  this.firstName = firstName
  this.lastName = lastName
} // 就像数据库的模板一样 规定这个person对象应该是什么样
```

你可以使用：

```javascript
Vue.component('blog-post', {
  props: {
    author: Person
  }
})
```

### 非props特性

之所以props可以传递任何类型的数据的一个原因就是，它允许父组件在传递属性的时候，子组件并没有设置这个属性

```html
<!--在父组件-->
<com1  aaa="sss"></com1>
<!--在子组件com1中-->
<div aaa="sss">  <!--即使子组件没有设置自定义属性 但是仍会把属性和值作用在根节点上-->
    <input></input>  <!--这样也可以 间接给子节点设置一些属性-->
</div>
----------------------------------------------------------------------------------------------
<!--属性合并-->
<!--在父组件-->
<com1  clsss="sss"></com1><!--父组件定义了class属性-->
<!--在子组件com1中-->
<div class="aaa">  <!--子组件也定义了class属性-->
    <input></input>  <!--只有class和style会合并  最后变成class=“sss aaa”-->
</div>               <!--注意！！！ 默认只会在根节点上合并-->
-----------------------------------------------------------------------------------------
<!--如果不希望继承 **自定义属性**  css之类的不影响-->
<!--在组件配置属性里增加-->
{
//...
inheritAttrs: false,
//....
}
```

### $atrr

唯一`v-bind`可以跟等号的写法，`v-bind=$atrr`

```html
    <label>
      {{ label }}
      <input
        v-bind="$attrs"  // 有了这个就能允许父组件传递 属性
        v-bind:value="value"
        v-on:input="$emit('input', $event.target.value)"
      >
    </label>
```

```javascript
 // ....
inheritAttrs: false,  // 首先这里禁止了父组件直接传递属性至子组件
props: ['label', 'value'],  // 这里规定一些自定义属性
// ....                    // 但是父组件 仍然  不能  传进来属性
    					// 关键在上面的v-bind="$attrs" 它允许接收父组件的属性
```

```html
<!--在父组件中引用-->
<base-input
  v-model="username"
  class="username-input"
  placeholder="Enter your username"  <!--这里给input传入了一个placeholder  在子组件中并没有设置-->
></base-input>						<!--最后子组件的 input 里面 会有placeholder属性-->
```



**经一步解释**：

上面这种方法的作用在于：

**首先**如果没有`v-bind="$attrs" `和`inheritAttrs: false`，父组件也可以随便传属性，**但是**props有一个特性，就是会直接将子组件不存在的属性放在，**子组件的根节点上**，也就是说你无法控制这个属性放在哪里。

**所以**如果使用了``v-bind="$attrs`，如上面，在子组件里面的`input`元素中绑定了一个`$attrs" `，那么父组件中设置的一**切在子组件中没有指示的属性**，都会**应用**在这个**input**上而**不是根节点**上。

**最后**当然可以再设置一个props，比如这里的placeholder。

**所以这个方法相当于稍微简化了自定义属性的流程**

## 2.2子组件------>父组件传递

`$emit`方法从子组件触发 父组件的自定义事件

```html
<!--在一个子组件中 点击这个按钮会触发一个事件 enlarge-->
<button v-on:click="$emit('enlarge-text')">
  Enlarge text
</button>
<!--在父组件中 注册了这个事件enlarge 和普通事件一样-->
<blog-post
  ...
  v-on:enlarge-text="postFontSize += 0.1"
></blog-post>
```

### 使用事件抛出数据

```html
<!--在一个子组件中 点击这个按钮会触发一个事件 enlarge  同时传递了一个值0.1-->
<button v-on:click="$emit('enlarge-text', 0.1)">
  Enlarge text
</button>
<!--在父组件中 注册了这个事件enlarge 使用$event收到这个值-->
<blog-post
  ...
  v-on:enlarge-text="postFontSize += $event"
></blog-post>
<!--当然使用自己定义事件函数 也能获取这个值 传入事件参数就行了-->
<blog-post
  ...
  v-on:enlarge-text="aa($event)"
></blog-post>
```

==这里的$event==只包含传递的数值，不像普通事件一样是一个对象，里面有很多属性

### 在组件上使用`v-model`

回想一下，`v-model`是双向绑定，也就是通过事件监听输入**监听value**，获取输入的值，随后更新变量。

所以：

```html
<input v-model="searchText">
```

*等价于：*

```html
<input
  v-on:input="searchText = $event.target.value"
>
```

**所以整体就是这样**

子组件中：

```html
    <input
      v-bind:value = "value"-------这里把自定义属性value绑定到原生属性value 是为了父组件能够设置原生							value，简单得说使  原生value=自定义value 不需要设置也可以不写这句
      v-on:input="$emit('input', $event.target.value)"
    >  <!--通过$emit 触发父组件中@input 实际上父组件并没有写@input 是v-model展开的结果 参考上面 -->
<!--对于checkbox 可以emit  checked值 相应的设置自定义checked值-->
```

```javascript
//...
props:["value"]
//..
```

父组件中：

```html
<custom-input v-model="searchText"></custom-input>
<!------------相当于---------------->
<custom-input
  v-bind：value = searchText----将值又反馈回子组件的自定义属性 value 这句话不影响双向绑定
  v-on:input="searchText = $event"----被emit触发 将子组件传递的数据给了searchtext
></custom-input>
```

### .sync修饰符

不同于`$emit`或者`v-on`/`$on()`，这两个事件方法，只是触发和传递数据，而.sync是**改变父组件的状态或者说是属性**

```javascript
// 子组件中写入  一般监听属性
{
//...
    watch:{
        name(newVal){
      			//设置监听，如果改变就更新 p_model
                this.$emit('update:p_model', newVal)
            	// 为什么写update:p_model 因为修饰符展开就是v-on:update:p_model
            	// update:p_model就看成事件
            },
    }
//...
}
```

```html
   <子组件
  	:p_model.sync='pname'></子组件> <!--改变name属性就会触发这里的更新 将属性pname更新-->
<!--反正就记住 .sync是更新父组件属性的-->
```



## 2.3  自定义事件

事件名称使用的时候**必须一样**，不然无法触发

推荐**始终使用 kebab-case 的事件名** 

### `v-model`的一点

像上面的那种，`v-model`默认是利用**input**事件来监听**value**属性，但是当使用checkbox或者radio的时候，value可能代表不同东西，比如cheked.

```javascript
Vue.component('base-checkbox', {
  model: {
    prop: 'checked',  // 属性变为 checked  实际上也只有checkbox需要这样
    event: 'change' // 事件变为change
  },
  props: {
    checked: Boolean  // 限制属性为true或者false
  },
  template: `
    <input
      type="checkbox"
      v-bind:checked="checked"
      v-on:change="$emit('change', $event.target.checked)"
    >
  `
})
```

现在在这个组件上使用 `v-model` 的时候：

```html
<base-checkbox v-model="lovingVue"></base-checkbox>
```

这里的 `lovingVue` 的值将会传入这个名为 `checked` 的 prop。同时当 `<base-checkbox>` 触发一个 `change` 事件并附带一个新的值的时候，这个 `lovingVue` 的属性将会被更新 

## 2.4 给组件绑定原生事件

**直接**给组件`@click`之类的是**无法生效**的，估计是因为vue的普通绑定事件原理就是dom的add监听器，但是自定义组件不是原生的html元素，所以给原生事件加上修饰符`.native`，就会绑定到子组件上

```html
<base-input v-on:focus.native="onFocus"></base-input>
```

值得==注意==的是，这种方法**一定**（就算用了$listenners也一样）会将事件绑定在子组件的**根元素**上，也就是会出现如下情况

```html
<label> <!--如果注册了input事件 就会绑定在label上 显然这个事件不可能生效-->
  {{ label }}
  <input
    v-bind="$attrs"
    v-bind:value="value"
    v-on:input="$emit('input', $event.target.value)"
  >
</label>
```

使用`$listeners`属性来解决这个问题 

`$listeners`是一个对象，是实例属性，内容是父组件传进来的事件。

```javascript
// $listeners属性值可以是这样
{// 比如在父组件中 @focus.native  @input.native 传入了一个原生的事件
  focus: function (event) { /* 这里确定一些事件方法*/ }
  input: function (value) { /* ... */ },
}

// 在父组件中就不要再加 native 修饰符了 原因如上节所说 加了这个修饰符 注册的事件就跑到根节点上了
// @click=“”什么的就够了 因为 已经设置捕获了父组件绑定的事件
```

```javascript
Vue.component('base-input', {
  inheritAttrs: false,
  props: ['label', 'value'],
  computed: {
    inputListeners: function () {
      var vm = this  
      // `Object.assign` 将所有的对象合并为一个新对象
      return Object.assign({}, // 最后会返回一个$listeners对象包含了覆写的行为
        // 我们从父级组件获取 绑定的事件 
        // 因为是计算属性 所以当$listeners发生变化的时候就会有触发！！！！！
        this.$listeners,
        // 然后我们添加自定义监听器，
        // 或覆写一些监听器的行为
        {
          // 这里确保组件配合 `v-model` 的工作
            input: function (event) {
            vm.$emit('input', event.target.value)// 这里的vm是之前保存的this
          } 				//因为原生事件函数的this是指向的是原生的html元素如这里的input
        }					// 而这里需要emit触发父组件绑定的input事件，emit只能是vue实例触发
      )
    }
  },
  template: `
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on="inputListeners" 		// 可以看到这里给这个子组件 绑定的是一个计算属性
      >
    </label>
  `
})
```

---

# :lemon:插槽 

---

如果想在子组件中插入内容

这样做是不行的:

```html
<alert-box>
  Something bad happened.
</alert-box>
```

这样是可行的

```html
    <div class="demo-alert-box">
      <strong>Error!</strong>
      <slot></slot>  <!--这个位置就是上面在父组件中输入的内容  插入的地方-->
    </div>
	
```

## 1.具名插槽

有时候需要多个地方插入，需要在子组件的`slot`标签上设置`name`属性，然后插入的时候，在父组件的`template`标签上设置`slot`属性，属性值就是`name`。普通标签设置`slot`也可以其实

在子组件上

```html
    <div >
      <slot name="a"></slot> 
      <slot name="b"></slot> 
      <slot name="c"></slot> 
    </div>

	<!--可以将slot设置默认值 父组件更改slot的时候就会覆盖-->
<button type="submit">
  <slot>Submit</slot>
</button>
```



在父组件中

```html
<子组件>
    <template slot="a">
        <h1>hello</h1>
    </template>
    <template slot="c">
        <p>hi</p>
    </template>
    <template slot="b">
        <p>hi</p>
        ......
    </template>
    <!--也可以直接用在标签上-->
  <h1 slot="header">Here might be a page title</h1>
  <p slot="footer">Here's some contact info</p>
</子组件>
```

## 2.编译作用域

当你想在插槽内使用数据时，例如：

```html
<navigation-link url="/profile">  <!--这个url属性是子组件的属性-->
  Logged in as {{ user.name }}  <!--这个user.name是父组件的变量-->
</navigation-link>
```

该插槽可以访问跟这个模板的其它地方相同的实例属性 (也就是说“作用域”是相同的)。但这个插槽**不能**访问 `<navigation-link>` 的作用域。例如尝试访问 `url` 是不会工作的。==牢记一条准则==：

> 父组件模板的所有东西都会在父级作用域内编译；子组件模板的所有东西都会在子级作用域内编译。

## 3.作用域插槽

也是一种 **子组件传数据给父组件**的方法

首先作用域插槽和普通插槽一样，接收任何元素插入到这里，替换掉slot，唯一不同的就是可以从子元素得到数据，而且要写名字

插槽一定是写在子组件内部的，**不是套在外面**

```html
       <!--比如子组件中会渲染一个列表-->
		<li
            v-for="todo in todos"
            v-bind:key="todo.id"
        >  <!--要是父组件不使用作用域插槽 这里默认会渲染一堆todo.text -->
      		
		 	<slot>{{todo.text}}</slot>  
        </li>
		<!--在父组件 我们想根据子组件的 数据 传入不同的东西 比如我只想让它显示id而不是text-->
		<!--比如我想要在 子组件的数据的isComplete是true的时候渲染一个✓-->
```

显然，我们既然要在**父组件**中控制子组件的渲染，就需要获取到**子组件的数据**，这也就是作用域插槽的作用

```html
<引用子组件><!--一定是在子组件内部使用作用域插槽 而不是外面-->
  <!-- 使用 slot-scope 属性 可以给任何一个标签设置作用域名-->
  <template slot-scope="name">
    <span v-if="name.xxx.isComplete">✓</span>-------这样就获取到了子组件slot绑定的数据
    {{ name.todo.text }}  <!--将text回退给子组件-->
  </template>
</引用子组件>
```

对应的子组件应该变成这样

```html
<li
    v-for="todo in todos"
    v-bind:key="todo.id"
  >
    <!--可以看到 在这里增加了 作用域插槽-->
    <!-- v-bind的参数随便取名字 只是父组件取数据的时候要对应上 后面的值就是你想传递的内容-->
    <slot v-bind:xxx="todo">
      {{ todo.text }}  <!--这里是从父组件得到的回退的内容->
    </slot>            
  </li>
```

上面的例子里面有一个**回退内容**，原因很简单 既然作用域插槽是为了 父组件控制子组件的渲染 那么作用域插槽内部的内容**都应该**由父组件传入/控制

==总结==首先，我们想把父组件的一些数据传入到`slot`

为了让内容**简洁**

```html
<todo-list>
  <template slot-scope="{ xxx }">  <!--直接将对象作为 名称-->
    <span v-if="xxx.isComplete">✓</span>  <!--省去了name.xxx...-->
    {{ xxx.text }}
  </template>
</todo-list>
```

这里面的数据并不是通过设置属性的方式传递的，所以 `inheritAttrs: false`不能阻止他们之间数据传递



---

# :avocado:动态组件  

---

```html
<!-- 组件会在 `currentTabComponent` 改变时改变 -->
<component v-bind:is="currentTabComponent"></component>
<!--通过给 component(vue内置组件) 绑定is属性 来控制哪个组件应该被显示-->
<!--当然普通标签也能 绑定is属性-->
```

上面的方法，有一个问题，每次切换的时候，vue都会重新渲染一次那个组件，但是有时候我们希望切回来的时候，这个组件应该保持切走时的状态，我们可以使用`<keep-alive>` 

```html
<!-- 失活的组件将会被缓存！-->
<keep-alive>
  <component v-bind:is="currentTabComponent"></component>
</keep-alive>
```

==注意==这个方法要求，每个组件都应该由自己的名字，显然是因为缓存后，需要取出。所以建议组件都应该**有个名字**

---

#  🍇异步组件

---

没怎么看懂，大概就是只在需要的时候加载组件

```javascript
new Vue({
  // ...
  components: {
    'my-component': () => import('./my-async-component')
  }
})
```

这里的异步组件工厂函数也可以返回一个如下格式的对象：

```javascript
const AsyncComponent = () => ({
  // 需要加载的组件 (应该是一个 `Promise` 对象)
  component: import('./MyComponent.vue'),
  // 异步组件加载时使用的组件
  loading: LoadingComponent,
  // 加载失败时使用的组件
  error: ErrorComponent,
  // 展示加载时组件的延时时间。默认值是 200 (毫秒)
  delay: 200,
  // 如果提供了超时时间且组件加载也超时了，
  // 则使用加载失败时使用的组件。默认值是：`Infinity`
  timeout: 3000
})
```

----

# :shallow_pan_of_food:解析 DOM 模板时的注意事项 

---

有些 HTML 元素，诸如 `<ul>`、`<ol>`、`<table>` 和 `<select>`，对于哪些元素可以出现在其内部是有严格限制的。而有些元素，诸如 `<li>`、`<tr>` 和 `<option>`，只能出现在其它某些特定的元素内部。 

```html
<!--在父组件中 我们需要这样嵌套子组件-->
<table>
  <blog-post-row></blog-post-row>
</table>
<!--但是子组件里面是这样的 很明显 table不可能再嵌套一个table-->
<!--所以使用 is 属性 然后就可以在父组件里单独使用这个-->
<table>
  <tr is="blog-post-row"></tr>
</table>
```

在如下情况下 是没有这种限制的:

- 字符串 (例如：`template: '...'`)
- 单文件组件 (`.vue`)
- `<script type="text/x-template">`

---

#  🍸处理边界情况

---

访问某个组件的dom元素，甚至操作组件的元素或者值，一般来说会造成混乱，**推荐使用状态管理工具**如vuex

但是有时候确实需要这么做那么：

## 1.访问根组件实例

在每个 `new Vue` 实例的子组件中，其根实例可以通过 `$root` 属性进行访问。例如，在这个根实例中：

```javascript
// Vue 根实例
new Vue({
  data: {
    foo: 1
  },
  computed: {
    bar: function () { /* ... */ }
  },
  methods: {
    baz: function () { /* ... */ }
  }
})
// 在其他组件中 想要访问根实例的内容 就可以
// 获取根组件的数据
this.$root.foo

// 写入根组件的数据
this.$root.foo = 2

// 访问根组件的计算属性
this.$root.bar

// 调用根组件的方法
this.$root.baz()
```

## 2.访问父组件的内容

`$parent`

这里的父组件是相对于，组件内部的组件说的

```html
<google-map> <!--这就是父组件 父组件里面有个一map数据 -->
  <google-map-region v-bind:shape="cityBoundaries">
    <google-map-markers v-bind:places="iceCreamShops"></google-map-markers>
  </google-map-region>
</google-map>
```

```javascript
// 我们想要取出google-map父组件里面的map
// 可以这样
let map = this.$parent.map || this.$parent.$parent.map
// 注意到越深层的组件 这里就要写更多的parent
```

为了解决以上的问题使用**依赖注入**使用`provide` 和 `inject`

```javascript
// 在父组件中 如这里的google-map
provide: function () {  // 提供下面这些值
  return {
    getMap: this.getMap
  }
}
```

```javascript
// 在子组件中
inject: ['getMap']
```

==但是==仍然建议使用vuex这样的状态管理工具来实现这些功能。

## 3.访问子组件实例或元素

`ref`属性

在原生html标签上使用，获取到的就是dom元素，在vue组件上使用，获取到的就是组件对象

```html
<!--使用组件的时候 赋予ref属性 并设置名称-->
<base-input ref="usernameInput"></base-input>
<!--或者在子组件里面设置 ref 在 父组件 里也能访问到子组件里面的元素-->
<input ref="input">
```

```javascript
// 使用 $refs.name 来获取这个元素
methods: {
  // 用来从父级组件聚焦输入框
  focus: function () {
    this.$refs.input.focus()// 因为是原生元素，所以可以直接使用原生js的一些方法
  }
}
```

## 4.事件监听器其他接口

在js中除了可以使用$emit，下面这些也能手动使用

- 通过 `$on(eventName, eventHandler)` 侦听一个事件
- 通过 `$once(eventName, eventHandler)` 一次性侦听一个事件
- 通过 `$off(eventName, eventHandler)` 停止侦听一个事件

```javascript
// mounted表示生命周期里的 这个组件已经渲染完成
mounted: function () {  // 重要的是 这里创建和摧毁都是在  一个钩子函数  里 所以可以直接用picker
  var picker = new Pikaday({ // 当渲染完成的时候 给this.$refs.input这个组件 加上Pikaday插件
    field: this.$refs.input,
    format: 'YYYY-MM-DD'
  })

  this.$once('hook:beforeDestroy', function () {  // 为这个组件绑定一个一次性事件  在这个组件被摧毁前触					              // 发  hook:beforeDestroy和前面update:xx类似
  	picker.destroy()   // 这里就可以直接写picker.destroy() 而不用在之前保存picker
  })
}
```

## 5.循环引用

### 递归调用

也就是，组件自己调用自己。但是必须设置`name`属性，全局注册组件的时候已经写了名字了，

`Vue.component('unique-name-of-my-component',...)`，但是也要注意无限循环的情况

### [组件之间的循环引用](https://cn.vuejs.org/v2/guide/components-edge-cases.html#组件之间的循环引用)

## 6.模板定义的替代

### [内联模板](https://cn.vuejs.org/v2/guide/components-edge-cases.html#内联模板)

### [X-Templates](https://cn.vuejs.org/v2/guide/components-edge-cases.html#X-Templates)

## 7.控制更新

### [强制更新](https://cn.vuejs.org/v2/guide/components-edge-cases.html#强制更新)`$forceUpdate`

### [通过 `v-once` 创建低开销的静态组件](https://cn.vuejs.org/v2/guide/components-edge-cases.html#通过-v-once-创建低开销的静态组件)

---

# :jack_o_lantern:动画 

---

## 1.进入/离开过渡

### 1.1单组件的过度

Vue 提供了 `transition` 的封装组件，在下列情形中，可以给任何元素和组件添加进入/离开过渡

- 条件渲染 (使用 `v-if`)
- 条件展示 (使用 `v-show`)
- 动态组件(`is`)
- 组件根节点

#### 通过设置CSS实现过渡

大多数情况下，都是css控制

在进入/离开的过渡中，会有 6 个 class 切换。

1. `v-enter`：定义**<u>开始状态</u>**。在元素**被插入之前**生效，元素**插入后**的**下一帧**被**移除**

2. `v-enter-active`：在**整个进入**的阶段中应用，也就是定义**这个过渡过程应该怎么实现**,这个类可以被用来定义进入过渡的**周期**，**延迟**和**缓动函数**。

3. `v-enter-to`: 2.1.8版及以上 定义进入过渡的结束状态。在元**素被插入后**的下一帧生效 (与此同时 `v-enter` 被移除)

   ==也就是==向。。。进入

4. `v-leave`:  定义离开过渡的开始状态。在**离开过渡被触发**时立刻生效，**下一帧**被**移除**。

5. `v-leave-active`：定义离开过渡生效时的状态。在**整个离开过渡**的阶段中应用，在离开过渡被触发时立刻生效，在过渡/动画完成之后移除。这个类可以被用来定义离开过渡的过程时间，延迟和曲线函数。

6. `v-leave-to`: 2.1.8版及以上 定义离开过渡的结束状态。在**离开过渡被触发之后**下一帧生效 (与此同时 `v-leave` 被删除)，在过渡/动画完成之后移除。

   ==也就是==向。。。离开

7. ![1528081857613](C:\Users\dell\29237\笔记\1528081857613.png)

如果**没有**给`trasition`标签配置`name`属性，那么过渡css名称就为`v-transition-enter`

如果给`trasition`标签**配置了**`name`属性，那么过渡css名称就为`name-transition-enter`

==注意==上面这些样式，在过渡完成后**都会移除**，比如在过渡样式里面写了改变字体，当过渡完成后，过渡样式被移除，显然你设置的字体大小也会移除，最终目标恢复到默认样式。

==注意==单独设置v-enter/v-leave没有设置v-enter-to/v-leave-to有可能会导致动画不正常。

你可以只设置两头，`v-enter`和`v-leave-to`，其他的搭配都会出现不正常。

**进入**你总得给个**起点**吧，**离开**总得给个**目标**吧。大概就是这个意思。

默认是从你设置的属性值过渡到默认的属性值，但是你可以把这个默认的属性值给改了就相当于`v-enter-to v-leave-to`，比如没有只设置了`v-enter 30px的字体`那么经过`v-enter-active`最后会过渡到默认字体大小，但是设置了`v-enter-to 20px`，会从30px过渡到20px，然后一直到过渡时间到，最后**移除**（为什么会移除，第一条注意写了）了这些样式，变成（跳变）默认的字体大小。

==注意==多个`transition`嵌套会导致失效

```html
<div id="demo">
  <button v-on:click="show = !show"> <!--按钮切换show的值-->
    Toggle
  </button>
  <transition name="fade"><!--使用transition内置标签 包裹需要过渡的元素 名字-->
    <p v-if="show">hello</p>  <!--这样p标签在显示和消失时都能 有过渡-->
  </transition>
</div>
new Vue({
  el: '#demo',
  data: {
    show: true
  }
})
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;  <!--这里就是css过渡的几个中间样式-->
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}

<!--可以设置不同的进入和离开动画 -->
<!--设置持续时间和动画函数 -->
.slide-fade-enter-active {
  transition: all .3s ease;
}
.slide-fade-leave-active {
  transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-fade-enter, .slide-fade-leave-to {
  transform: translateX(10px);
  opacity: 0;
}
<!--也可以设置 动画-->
.bounce-enter-active {
  animation: bounce-in .5s;
}
.bounce-leave-active {
  animation: bounce-in .5s reverse;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
```

在这里vue有一些特性，总结起来就是如果在`transition`里面插入/移除元素，都会**自动增加**过渡

##### 自定义类名

我们可以通过以下特性来自定义过渡类名：（就是在原生的类名后面加个`-class`）

- `enter-class`
- `enter-active-class`
- `enter-to-class` (2.1.8+)
- `leave-class`
- `leave-active-class`
- `leave-to-class` (2.1.8+)

```html
<!--在使用第三方动画库的时候很有用-->
<link href="https://cdn.jsdelivr.net/npm/animate.css@3.5.1" rel="stylesheet" type="text/css">

<div id="example-3">
  <button @click="show = !show">
    Toggle render
  </button>
  <transition
    name="custom-classes-transition"
    enter-active-class="animated tada"
    leave-active-class="animated bounceOutRight"
  >
    <p v-if="show">hello</p>
  </transition>
</div>
```

##### 同时使用animation和transition

如果既想使用animation又想用transition，需要在`transition`标签上增加`type`属性，其值为`transition`或者`animation`这样来设置对应的监听器

#####  控制过渡的时间

在这种情况下你可以用 `<transition>` 组件上的 `duration` 属性定制一个显性的过渡持续时间 (以毫秒计)：

```html
<!--注意这里即使是一个数字1000 也应该使用bind来绑定 告诉vue这是个js表达式 不是字符串-->
<transition :duration="1000">...</transition>
```

你也可以定制进入和移出的持续时间：

```html
<transition :duration="{ enter: 500, leave: 800 }">...</transition>
```

#### 通过js实现过渡

可以在属性中声明 JavaScript 钩子

```html
<transition
  v-on:before-enter="beforeEnter"
  v-on:enter="enter"
  v-on:after-enter="afterEnter"
  v-on:enter-cancelled="enterCancelled"

  v-on:before-leave="beforeLeave"
  v-on:leave="leave"
  v-on:after-leave="afterLeave"
  v-on:leave-cancelled="leaveCancelled"
  v-bind:css="false" ---------------------------------->这一句最好加上 避免去检查css
>
  <!-- ... -->
</transition>
```

```javascript
// ...
methods: {
  // --------
  // 进入中
  // --------

  beforeEnter: function (el) {
    // ...
  },
  // 此回调函数是可选项的设置
  // 与 CSS 结合时使用
  enter: function (el, done) {
    // ...
    done()  //  调用done函数是必须的 只有enter和leave有这个需求
  },
  afterEnter: function (el) {
    // ...
  },
  enterCancelled: function (el) {
    // ...
  },

  // --------
  // 离开时
  // --------

  beforeLeave: function (el) {
    // ...
  },
  // 此回调函数是可选项的设置
  // 与 CSS 结合时使用
  leave: function (el, done) {
    // ...
    done()
  },
  afterLeave: function (el) {
    // ...
  },
  // leaveCancelled 只用于 v-show 中
  leaveCancelled: function (el) {
    // ...
  }
}
```

```javascript
new Vue({
  el: '#example-4',
  data: {
    show: false
  },
  methods: {
    beforeEnter: function (el) {
      el.style.opacity = 0
      el.style.transformOrigin = 'left'
    },
    enter: function (el, done) {
      Velocity(el, { opacity: 1, fontSize: '1.4em' }, { duration: 300 })
      Velocity(el, { fontSize: '1em' }, { complete: done })// 注意这里 complete时候调用done
    },
    leave: function (el, done) {
      Velocity(el, { translateX: '15px', rotateZ: '50deg' }, { duration: 600 })
      Velocity(el, { rotateZ: '100deg' }, { loop: 2 })
      Velocity(el, {
        rotateZ: '45deg',
        translateY: '30px',
        translateX: '30px',
        opacity: 0
      }, { complete: done })
    }
  }
})
```

#### 初始过渡

一个元素在第一次渲染的时候，也可以增加一个过渡`appear`

```html
    <transition
    appear --------首先要开启appear
    appear-class="custom-appear" -------好像只有自定义class才有效
    appear-to-class="custom-appear-to"------ 意思和进入离开一样
    appear-active-class="custom-appear-active"
    >
    <p>sssss</p>
    </transition>
<!--也提供了js钩子-->
<transition
  appear
  v-on:before-appear="customBeforeAppearHook"
  v-on:appear="customAppearHook"
  v-on:after-appear="customAfterAppearHook"
  v-on:appear-cancelled="customAppearCancelledHook"
>
  <!-- ... -->
</transition>
```

```css
 .custom-appear-active{
  color: #080808;
  background: #b6b6b6;
  transition: all 1s ease;
 }
 .custom-appear{
  font-size: 40px;
  color: #e069e2;
  background: #7798e2;
 }
 .custom-appear-to{
  color: #e29138;
  background: #1c8942;
 }
```

### 1.2 多个元素（标签）的过渡

对于多个标签，注意**不是组件**，可以使用`v-if v-else-if v-else`来实现选择进入/离开

而且，在`transition`中只能实现以`v-if v-else-if v-else`为操作的，也就是一个时刻只可能存在一个标签的情况，你想渲染一整个列表什么的就需要后面的`transition-group`

```html
<transition>
  <table v-if="items.length > 0">
    <!-- ... -->
  </table>
  <p v-else>Sorry, no items found.</p>
</transition>
```

 ==注意==以下几种情况会有一些问题

```html
<!--元素（标签）是不同的时候  使用if-else是正常可以实现 过度效果的-->
<transition>
  <table v-if="items.length > 0">
    <!-- ... -->
  </table>
  <p v-else>Sorry, no items found.</p>
</transition>
<!--元素（标签）相同时候 不会有过度效果--> 
<!--这是因为vue为了渲染速度 相同标签默认只会渲染不同的内容 不会真的让它消失了重新渲染-->
<transition>
  <p v-if="items.length > 0">
    <!-- ... -->
  </p>
  <p v-else>Sorry, no items found.</p>
</transition>
```

解决办法很简单，和前面的（条件渲染章节第三小节）情况一样，为了让vue知道这些标签是不同的，要在这些标签上增加`key`属性，在`transition-group`中key是强制加的

```html
<transition>
  <button v-if="isEditing" key="save">
    Save
  </button>
  <button v-else key="edit">
    Edit
  </button>
</transition>
```

在一些场景中，也可以通过给同一个元素的 `key` 特性设置不同的状态来代替 `v-if` 和 `v-else`，上面的例子可以重写为：

```html
<!--因为这里只是为了1.更改不同的button文字 2.而文字和key的值一样 3.我们也需要他们都有过渡效果-->
<!--对于3.我们需要给每个button一个key 对于1.2.我们可以直接绑定一个变量 同时设定在内容和key上-->
<transition>
  <button v-bind:key="isEditing">
    {{ isEditing ? 'Save' : 'Edit' }}
  </button>
</transition>
```

#### [过渡模式](https://cn.vuejs.org/v2/guide/transitions.html#过渡模式)

`<transition>` 的默认行为 - 进入和离开同时发生 ，你会看到一个元素正在消失，另外一个也同时正在显示

可以设置`mode`属性

- `in-out`：新元素先进行过渡，完成之后当前元素过渡离开。

- `out-in`：当前元素先进行过渡，完成之后新元素过渡进入。

  一般选择out-in就能实现在同一个位置，两个元素切换

### 1.3多个组件的过渡

我们不需要使用 `key` 特性。相反，我们只需要使用动态组件`is`：

```javascript
<checkbox....>
<transition name="component-fade" mode="out-in"> // mode设置 
  <component v-bind:is="view"></component>  // 使用内置标签component 确定是哪个组件在这里显示
// 绑定is属性 告诉vue“view”不是字符串 而是js表达式
</transition>
// 导入组件
new Vue({
  el: '#transition-components-demo',
  data: {
    view: 'v-a'
  },
  components: {
    'v-a': {
      template: '<div>Component A</div>'
    },
    'v-b': {
      template: '<div>Component B</div>'
    }
  }
})
// 过度样式
.component-fade-enter-active, .component-fade-leave-active {
  transition: opacity .3s ease;
}
.component-fade-enter, .component-fade-leave-to
{
  opacity: 0;
}
```

### 1.4 列表过渡

`<transition-group>` 组件，它有几个特点：

- 不同于 `<transition>`，它会以一个真实元素呈现：默认为一个 `<span>`。你也可以通过 `tag` 属性更换为其他元素。
- 内部元素 **总是需要** 提供唯一的 `key` 属性值
- 与`transition`的不同参考（1.2）

#### 列表进入/离开过渡

```html
<div id="list-demo" class="demo">
  <button v-on:click="add">Add</button>
  <button v-on:click="remove">Remove</button>
  <transition-group name="list" tag="p">
    <span v-for="item in items" v-bind:key="item" class="list-item">
      {{ item }}
    </span>  <!--其他的操作和一般的离开进入一样 只是这里是一个列表渲染 一个时刻可能有多个元素存在-->
  </transition-group>
</div>
```

#### 列表排序过渡

这个实现在改变列表顺序的时候，各个元素会的交换位置会有一个过渡效果，而不是一下子就变化到新位置。

使用`v-move`这个样式，使用方法和之前的通过css实现过渡一样，也可以`name-move`

```css
.name-item {  /*关注item和move这两个样式 一个是对于每一项的 一个是对于整体排序或者打乱。。的*/
  transition: all 1s;
  display: inline-block;
  margin-right: 10px;
}
.name-move {
  transition: transform 1s;
}
.name-enter, .name-leave-to {
  opacity: 0;
  transform: translateY(30px);
}
.name-leave-active {
  position: absolute;
}
```

==注意==过渡的元素不能设置为 `display: inline` 。作为替代方案，可以设置为 `display: inline-block` 或者放置于 `flex` 

#### [列表的交错过渡](https://cn.vuejs.org/v2/guide/transitions.html#列表的交错过渡)

实际上就是用钩子函数来控制动画

### 1.5[可复用过渡组件](https://cn.vuejs.org/v2/guide/transitions.html#%E5%8F%AF%E5%A4%8D%E7%94%A8%E7%9A%84%E8%BF%87%E6%B8%A1)

你需要做的就是将 `<transition>` 或者 `<transition-group>` 作为根组件，然后将任何子组件放置在其中就可以了 

就是将过渡变成一个模块，配置好过渡样式，就可以套在任何地方

### 1.6[动态过渡](https://cn.vuejs.org/v2/guide/transitions.html#%E5%8A%A8%E6%80%81%E8%BF%87%E6%B8%A1)

实际上就是使用钩子函数，在一些过渡的配置，比如周期什么的用变量代替

## 2.[状态过渡](https://cn.vuejs.org/v2/guide/transitioning-state.html#%E7%8A%B6%E6%80%81%E5%8A%A8%E7%94%BB%E4%B8%8E%E4%BE%A6%E5%90%AC%E5%99%A8)

那么对于数据元素本身的动效呢，比如：

- 数字和运算 （比如从1变到20，不是直接改变数值，而是一个动画有个过渡）
- 颜色的显示
- SVG 节点的位置
- 元素的大小和其他的属性

说白了就是用js和第三方插件实现这些动画

---

# :fire: 混入mixins 

---

就是一个配置对象，和组件不一样的是，这个配置可以任意放在任何一个组件里，那么这个组件的配置属性里面就有这个混入对象的设置属性

```javascript
// 定义一个混入对象
var myMixin = {
  created: function () {  //生命周期钩子函数 created 实例化之后执行
    this.hello()
  },
  methods: {
    hello: function () {
      console.log('hello from mixin!')
    }
  }
}

// 将上面的设置混入这个组件
var Component = Vue.extend({
  mixins: [myMixin]
})

var component = new Component() // 这个对象呗实例化 所以触发钩子函数输出 => "hello from mixin!"
```

## 1.选项合并

如果混合选项和组件里面的**属性名称一样**，会**优先使用组件里面**的。

```javascript
var mixin = {
  data: function () {
    return {
      message: 'hello',  // 和组件有同样的一个message
      foo: 'abc'
    }
  }
}

new Vue({
  mixins: [mixin],
  data: function () {
    return {
      message: 'goodbye',
      bar: 'def'
    }
  },
  created: function () {
    console.log(this.$data) // 那么组件的这个message会优先，也就是覆盖混入选项的
    // => { message: "goodbye", foo: "abc", bar: "def" }
  }
})
```

**同名钩子函数**将混合为一个**数组**，因此**都将被调用**。另外，混入对象的钩子将在**组件自身钩子之前**调用。

```javascript
var mixin = {
  created: function () {
    console.log('混入对象的钩子被调用')
  }
}

new Vue({
  mixins: [mixin],
  created: function () {
    console.log('组件钩子被调用')
  }
})
// 输出
// => "混入对象的钩子被调用"
// => "组件钩子被调用"
```

**值**为**对象**的选项，例如 `methods`, `components` 和 `directives`，将被混合为同一个对象。两个对象**键名冲突**时，取**组件对象**的键值对。 

## 2.全局混入

```javascript
// 为自定义的选项 'myOption'
// 用全局对象的方法Vue.mixin  和注册全局对象一样
Vue.mixin({
  created: function () {
    var myOption = this.$options.myOption
    if (myOption) {
      console.log(myOption)
    }
  }
})

new Vue({
  myOption: 'hello!'
})
// => "hello!"
```

## 3.[自定义选项合并策略](https://cn.vuejs.org/v2/guide/mixins.html#%E8%87%AA%E5%AE%9A%E4%B9%89%E9%80%89%E9%A1%B9%E5%90%88%E5%B9%B6%E7%AD%96%E7%95%A5)

自定义选项将使用默认策略，即简单地覆盖已有值。如果想让自定义选项以自定义逻辑合并，可以向 `Vue.config.optionMergeStrategies` 添加一个函数：

```javascript
Vue.config.optionMergeStrategies.myOption = function (toVal, fromVal) {
  // return mergedVal
}
```

对于大多数对象选项，可以使用 `methods` 的合并策略：

```javascript
var strategies = Vue.config.optionMergeStrategies
strategies.myOption = strategies.methods
```

---

# :circus_tent:自定义指令

---

就和一个普通的配置属性一样：

全局注册：

```javascript
// 注册一个全局自定义指令 `v-focus`  // 注意无论取了什么名字 使用的时候都要加上 v-
Vue.directive('focus', {
  // 当被绑定的元素插入到 DOM 中时……
  inserted: function (el) {
    // 聚焦元素
    el.focus()
  }
})
// 使用
<input v-focus>
```

局部注册：

```javascript
// 增加一个配置属性 directives
directives: {
  focus: {
    // 指令的定义 这里使用了一个钩子函数 在元素插入前执行
    inserted: function (el) {
      el.focus()
    }
  }
}
```

## 1.指令[钩子函数](https://cn.vuejs.org/v2/guide/custom-directive.html#钩子函数)

一个**指令定义对象**可以提供如下几个钩子函数 (均为可选)：

- `bind`：只调用一次，指令**第一次绑定到元素**时调用。在这里可以进行一次性的初始化设置。
- `inserted`：被绑**定元素插入父节点**时调用 (仅保证父节点存在，但不一定已被插入文档中)。
- `update`：所在组件的 VNode 更新时调用，但是可能发生在其子 VNode 更新之前。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新 (详细的钩子函数参数见下)。
- `componentUpdated`：指令所在组件的 VNode 及其子 VNode全部更新后调用。
- `unbind`：只调用一次，指令与元素解绑时调用。

接下来我们来看一下**钩子函数的参数** (即 `el`、`binding`、`vnode` 和 `oldVnode`)。

指令钩子函数会被传入以下参数：

- `el`：指令所绑定的元素，可以用来直接操作 DOM 。
- `binding`：一个对象，包含以下属性：
  - `name`：**指令名**，不包括 `v-` 前缀。
  - `value`：指令的**绑定值**，例如：`v-my-directive="1 + 1"` 中，绑定值为 `2`。
  - `oldValue`：指令绑定的**前一个值**，仅在 `update` 和 `componentUpdated` 钩子中可用。无论值是否改变都可用。
  - `expression`：**字符串**形式的指令**表达式**。例如 `v-my-directive="1 + 1"` 中，表达式为 `"1 + 1"`。
  - `arg`：传给指令的**参数**（就像v-bind绑定属性），可选。例如 `v-my-directive:foo` 中，参数为 `"foo"`。
  - `modifiers`：一个包含**修饰符**的**对象**。例如：`v-my-directive.foo.bar` 中，修饰符对象为 `{ foo: true, bar: true }`。
- `vnode`：Vue 编译生成的虚拟节点。移步 [VNode API](https://cn.vuejs.org/v2/api/#VNode-接口) 来了解更多详情。
- `oldVnode`：上一个虚拟节点，仅在 `update` 和 `componentUpdated` 钩子中可用。

==注意==

除了 `el` 之外，其它参数都应该是只读的，切勿进行修改。如果需要在钩子之间共享数据，建议通过`HTMLElement`(el)的 [`dataset`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement/dataset) 来进行**读取**。 

```html
<div id="hook-arguments-example" v-demo:foo.a.b="message"></div>
<!--也可以直接传入字面量-->
<div v-demo="{ color: 'white', text: 'hello!' }"></div>
```

```javascript
// 这里定义了一个全局的自定义指令 v-demo
Vue.directive('demo', {
  bind: function (el, binding, vnode) {// 使用了bind钩子函数 绑定的时候就会触发
    var s = JSON.stringify
    el.innerHTML = // 按照上面的使用 下面会依次输出
      'name: '       + s(binding.name) + '<br>' + // demo
      'value: '      + s(binding.value) + '<br>' + // hello 是下面实例vue的data里面的message变量
      'expression: ' + s(binding.expression) + '<br>' + // message
      'argument: '   + s(binding.arg) + '<br>' +// foo
      'modifiers: '  + s(binding.modifiers) + '<br>' + // 修饰符对象{"a":true, "b":true}  可以看到修饰符只有布尔值
      'vnode keys: ' + Object.keys(vnode).join(', ')  // 参考vnode的定义
  }
})
// 也可以简写 这样默认 设置了bind钩子函数
Vue.directive('color-swatch', function (el, binding) {
  el.style.backgroundColor = binding.value
})
new Vue({
  el: '#hook-arguments-example',
  data: {
    message: 'hello!'
  }
})vuex
```

----

# :yellow_heart:渲染函数render

## 1.基础

如果我们想满足这样一个要求：

```html
<!--这是一个子组件 有一个自定义属性level-->
<!--我想在父组件中应用它 然后给他一个level值 最后显示不同如h1\h2\..等大小的标签-->
<anchored-heading :level="1">Hello world!</anchored-heading>
```

所以在这个子组件里可以这样写

```html
  <!--个努努从父组件传来的属性值 用if来控制哪一个h标签显示 并将内容插入到slot-->
<script type="text/x-template" id="anchored-heading-template">
<h1 v-if="level === 1">
    <slot></slot>
  </h1>
  <h2 v-else-if="level === 2">
    <slot></slot>
  </h2>
  <h3 v-else-if="level === 3">
    <slot></slot>
  </h3>
  <h4 v-else-if="level === 4">
    <slot></slot>
  </h4>
  <h5 v-else-if="level === 5">
    <slot></slot>
  </h5>
  <h6 v-else-if="level === 6">
    <slot></slot>
  </h6>
  </script>
<!--然后配置自定义属性-->
Vue.component('anchored-heading', {
  template: '#anchored-heading-template',
  props: {
    level: {
      type: Number,
      required: true
    }
  }
})
```

虽然上面的方法可行，但是代码很冗长，且有很多重复的逻辑，所以我们可以这样做：

```javascript
// 这里是全局注册了一个组件<anchored-heading>
Vue.component('anchored-heading', {
  render: function (createElement) {  // 使用render函数
    return createElement(  // 他有个参数是createElement  之后有详细讲解
      'h' + this.level,   //第一个参数 tag name 标签名称
      this.$slots.default // 这里的$slots是，实例属性，$slots.default代表访问默认的slot也就是没有给名字的slot，给了名字就$slots.name
    )
  },
  props: {
    level: {
      type: Number,
      required: true
    }
  }
})
// 这样调用这个子组件的时候，输入level属性的值，就能对应渲染出对应等级的元素，有点像HTMLElement.createElement()方法，可以动态创建元素
```

## 2.render函数的原理

### 虚拟dom

Vue 通过建立一个**虚拟 DOM** 对真实 DOM 发生的变化保持追踪。请仔细看这行代码：

```javascript
return createElement('h1', this.blogTitle)
```

`createElement` 到底会返回什么呢？其实不是一个*实际的* DOM 元素。它更准确的名字可能是 `createNodeDescription`，因为它所包含的信息会告诉 Vue 页面上需要渲染什么样的节点，及其子节点。我们把这样的节点描述为“虚拟节点 (Virtual Node)”，也常简写它为“VNode”。“虚拟 DOM”是我们对由 Vue 组件树建立起来的整个 VNode 树的称呼。

## 3.`createElement` 参数

```javascript
// @returns {VNode}
createElement(
   // 第一个参数（标签名 组件名/选项对象（也就是创建一个组件）
  // {String | Object | Function}
  // 一个 HTML 标签字符串，
  // 组件选项对象，或者解析上述任何一种的一个 async 异步函数，必要参数
  'div',
 
   // 第二个参数（配置这个元素
  // {Object}
  // 一个包含模板相关属性的 数据对象 定义一些自定义属性、绑定事件、其实就是实例的配置属性
  // 这样，您可以在 template 中使用这些属性。可选参数。
  {
    // (详情见下一节)
  },
	
   // 第三个参数 生成子节点 值得注意的是 只有这里才能用数组 也就是可以生成复数的子节点！！！！！
  // {String | Array}  创建子组件的时候 记得import组件 组件名不要加引号
  // 子节点 (VNodes)，由 `createElement()` 构建而成，
  // 或使用字符串来生成“文本节点”。可选参数。
  [
    '先写一些文字',//一个text子节点
    createElement('h1', '一则头条'),// 创造的一个h1子节点
    createElement(MyComponent, { // 创造的一个组件子节点 有自定义属性props 
      props: {
        someProp: 'foobar'
      }
    })
  ]
)
```

### 第二个参数data

就是对这个新创建的组件/元素 设定一些例如 属性 自定义属性 指令 自定义属性之类的 

还有就是，**每个组件都是vue实例**，所以data里面可以**设置实例属性**，也就是`vm.$xxx`的这些属性，直接`xxx:{}`

```javascript
{
  // 和`v-bind:class`一样的 API  
  // 给创建的这个元素绑定class 注意一定要写清楚true/false 不然无法应用样式
  // 接收一个字符串、对象或字符串和对象组成的数组
  'class': {
    foo: true,
    bar: false
  },
  // 和`v-bind:style`一样的 API
  // 给创建的这个元素绑定内联样式
  // 接收一个字符串、对象或对象组成的数组
  style: {
    color: 'red',
    fontSize: '14px'
  },
  // 正常的 HTML 属性
  // 里面可以写任何正常的html属性 注意不是dom属性 如innnerHTML
  // 简单得说就是  写标签里面的那些属性
  attrs: {
    id: 'foo'
  },
  // 组件 props
  // 父组件可以通过自定义属性 传递数据
  props: {
    myProp: 'bar'
  },
  // DOM 属性
  // innerText什么的
  domProps: {
    innerHTML: 'baz'
  },
  // 事件监听器基于 `on`
  // 所以不再支持如 `v-on:keyup.enter`修饰符
  // 需要手动匹配 keyCode 也就是自己用js判断
  on: {
    click: this.clickHandler
  },
  // 仅对于组件，用于监听原生事件，而不是组件内部使用
  // `vm.$emit` 触发的事件。
  nativeOn: {
    click: this.nativeClickHandler// 
  },
  // 对这个元素 设定 自定义指令。注意，你无法对 `binding` 中的 `oldValue`
  // 赋值，因为 Vue 已经自动为你进行了同步。
  directives: [
    {
      name: 'my-custom-directive',
      value: '2',
      expression: '1 + 1',
      arg: 'foo',
      modifiers: {
      bar: true
      }
    }
  ],
  // 作用域插槽格式
  // { name: props => VNode | Array<VNode> }
  scopedSlots: {
    default: props => createElement('span', props.text)
      // 这句意思是 创建一个  <span slot-scop="default">props.text</span>  的作用域插槽
  },
  // 如果组件是其他组件的子组件，需为插槽指定名称
  slot: 'name-of-slot',
  // 其他特殊顶层属性
  key: 'myKey',
  ref: 'myRef'
}
```

一个示例

```javascript
//注意 render里面不能重复createElement同一个节点 如这里的<p>hi</p>
render: function (createElement) {
  return createElement('div',
    Array.apply(null, { length: 20 }).map(function () {
      return createElement('p', 'hi')
    })
  )
}// Array.apply是创建了一个长长度为20的数组，然后map，返回一个节点数组，每个元素都是p标签，最后创建了20个p标签，包含在一个div里面
// 这里使用了第一和第三个参数，那么为什么不谢第二个参数也能正常呢？上面的代码已经解释了，其实只有第三个参数才能输入一个数组，所以这里不需要指定第二个参数
```

## 4.在render中写入js代码

### 实现v-if

```html
<!--在模板中我们可以使用 v-if 之类的来控制元素/组件的显示-->
<ul v-if="items.length">
  <li v-for="item in items">{{ item.name }}</li>
</ul>
<p v-else>No items found.</p>
```

```javascript
// 在render函数里面 我们可以直接使用js代码
//......
props: ['items'],
render: function (createElement) {
  if (this.items.length) {// 很直观的 我们可以控制创建什么元素 而且可以很复杂
    return createElement('ul', this.items.map(function (item) {
      return createElement('li', item.name) 
    }))
  } else {
    return createElement('p', 'No items found.')
  }
}
//.....
```

### 实现v-model

```javascript
props: ['value'],
render: function (createElement) {
  var self = this
  return createElement('input', {
    domProps: {
      value: self.value// 将原生的dom属性value 改成了自定义的属性 value
    },
    on: {
      input: function (event) {
        self.$emit('input', event.target.value)// 和上面的 绑定原生事件张杰的$listeners部分 一样
      }							// 这里绑定了一个input事件 注意不是原生的input事件 原生应该使用
    }                    	//nativeOn。当父组件@input 同样这个不是原生事件，这个组件的input被触发 
  })					// 就会emit父组件的input 顺便把子组件的event.target.value传给了父组件
}						// 这样父组件就能使用子组件的数据 实现双向绑定
```

### 部署修饰符

对于 `.passive`、`.capture` 和 `.once`事件修饰符, Vue 提供了相应的前缀可以用于 `on`：

| Modifier(s)                        | Prefix |
| ---------------------------------- | ------ |
| `.passive`                         | `&`    |
| `.capture`                         | `!`    |
| `.once`                            | `~`    |
| `.capture.once` or `.once.capture` | `~!`   |

```javascript
on: {
  '!click': this.doThisInCapturingMode,
  '~keyup': this.doThisOnce,
  '~!mouseover': this.doThisOnceInCapturingMode
}
```

对于其他的修饰符，前缀不是很重要，因为你可以在事件处理函数中使用事件方法：

| Modifier(s)                                        | Equivalent in Handler                                        |
| -------------------------------------------------- | ------------------------------------------------------------ |
| `.stop`                                            | `event.stopPropagation()`                                    |
| `.prevent`                                         | `event.preventDefault()`                                     |
| `.self`  currentTarget代表注册事件的那个元素       | `if (event.target !== event.currentTarget) return`           |
| Keys: `.enter`, `.13`                              | `if (event.keyCode !== 13) return` (change `13` to [another key code](http://keycode.info/) for other key modifiers) |
| Modifiers Keys: `.ctrl`, `.alt`, `.shift`, `.meta` | `if (!event.ctrlKey) return` (change `ctrlKey` to `altKey`, `shiftKey`, o |

```javascript
on: {
  keyup: function (event) {
    // 如果触发事件的元素不是事件绑定的元素
    // 则返回
    if (event.target !== event.currentTarget) return
    // 如果按下去的不是 enter 键或者
    // 没有同时按下 shift 键
    // 则返回
    if (!event.shiftKey || event.keyCode !== 13) return
    // 阻止 事件冒泡
    event.stopPropagation()
    // 阻止该元素默认的 keyup 事件
    event.preventDefault()
    // ...
  }
}
```

### 创建插槽

```javascript
// 创建一个普通slot
render: function (createElement) {
  //这样将会创建 `<div><slot></slot></div>`
  return createElement('div', this.$slots.default) //为什么是slots.default在这一章的基础这一小节的最后代码的注释上有写
}


// 创建一个 作用域插槽 供父组件使用 （传递作用域插槽）
data(){
    return {
        message:"!!!"
    }
},
render: function (createElement) {// 这里使用了第一和第三个参数 而上面只使用了第一和第二个参数
    					// 为什么上面的slot不写成第三个参数呢 这样【this.$slots.default】
    				   // 因为没有必要 因为不需要给普通slot配置什么东西 
  // `<div><slot :text="message"></slot></div>`
  return createElement('div', [
    this.$scopedSlots.default({  // 在父组件中就可以  <span slot-scop="aa">{{aa.text}}=>"!!!"
      text: this.message
    })
  ])
}

// 创一个作用域插槽 用于获取子组件(比如这里的child)数据（获取作用域插槽）
render: function (createElement) {
  return createElement('div', [
    createElement('child', {
      // 传送作用域插槽  利用实例属性scopedSlots  在第二个参数data对象
      // in the form of { name: props => VNode | Array<VNode> }
      scopedSlots: {//请对比作用域插槽那一章
        default: function (name) { // 这里default就行了 因为不可能有多个获取作用域插槽
          return createElement('span', name.text)
        }		
      }            //相当于<div><child> <span slot-scoped="name">name.text</span>  </child></div>
    })
  ])
}
```

## 5.JSX

通过更简单的方法创建组件

这一章的第三小节的第二个参数data的示例相当于这里的Vue 2.0 JSX:

```javascript
render (h) {//必须写h来代替createElement 不然会报错
  return (
    <div
      // 原生的属性或者组件属性 直接这样写.
      id="foo"
      // 设置dom属性 前面加上`domProps`
      domPropsInnerHTML="bar"
      // 注册vue事件和原生事件
      onClick={this.clickHandler}
      nativeOnClick={this.nativeClickHandler} //在组件上注册原生事件
      // 其他顶级属性 也就是原生属性和vue内置属性
      class={{ foo: true, bar: false }}
      style={{ color: 'red', fontSize: '14px' }}
      key="key"
      ref="ref"
      // 使用v-for给ref赋值
      refInFor
      // slot取名字
      slot="slot">
    </div>
  )
}
```

---

# :japanese_goblin:插件

---

## 1.使用插件

```javascript
// 用 Browserify 或 webpack 提供的 CommonJS 模块环境时
var Vue = require('vue')
var VueRouter = require('vue-router')

// 不要忘了调用此方法
Vue.use(VueRouter)
```

---

# :1st_place_medal:过滤器

---

对文本格式化

过滤器可以用在两个地方：**双花括号插值**和 **v-bind 表达式** 

使用`|`跟在需要过滤的js表达式后面

```html
<!-- 在双花括号中 -->
{{ message | capitalize }}
<!--可以串联-->
{{ message | filterA | filterB }}
<!-- 在 `v-bind` 中 -->
<div v-bind:id="rawId | formatId"></div>
```

```javascript
filters: {// 配置属性filters
  capitalize: function (value) { // 定义一个过滤器
    if (!value) return ''
    value = value.toString()
    return value.charAt(0).toUpperCase() + value.slice(1)
  }
}

// 或者在创建Vue实例之前 注意是之前 也就是new Vue之前
// 创建全局过滤器
Vue.filter('capitalize', function (value) {
  if (!value) return ''
  value = value.toString()
  return value.charAt(0).toUpperCase() + value.slice(1)
})
```

过滤器**默认**会接收`|`前面的那个js表达式的值，即使**调用的时候**没有明确传入，仍然会传入

```html
{{ message | filterA('arg1', arg2) }}
```

这里，`filterA` 被定义为接收三个参数的过滤器函数。其中 `message` 的值作为第一个参数，普通字符串 `'arg1'` 作为第二个参数，表达式 `arg2` 的值作为第三个参数。

---

# :baby_chick:[生产环境部署](https://cn.vuejs.org/v2/guide/deployment.html)

---

# :palm_tree:路由

---

## 1.简单使用

需要在`html`和`js`中都配置

在`HTML`中使用`<router-link to="/foo">`和`<router-view>`这样的标签

`router-link `在页面中是一个`a`标签的形式

当 `<router-link>` 对应的路由匹配成功，将自动设置 class 属性值  `.router-link-active` ，所以可以在css里面给这个样式自定义属性

```html
<script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>
<div id="app">
  <h1>Hello App!</h1>
  <p>
    <!-- 使用 router-link 组件来导航. -->
    <!-- 通过传入 `to` 属性指定链接. -->
    <!-- <router-link> 默认会被渲染成一个 `<a>` 标签 -->
    <router-link to="/foo">Go to Foo</router-link>
    <router-link to="/bar">Go to Bar</router-link>
  </p>
  <!-- 路由出口 -->
  <!-- 路由匹配到的组件将渲染在这里 -->
  <router-view name="HelloWorld"></router-view>
</div>
```

在`JS`中

```javascript
import Vue from 'vue'
import Router from 'vue-router'//导入包
import HelloWorld from '@/components/HelloWorld'

Vue.use(Router)// use插件
// 导出router实例 直接在全局注册
export default new Router({
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld // 注意import组件
    },
    //.....
  ]
})

// 在全局Vue的文件 main.js中
import router from './router/index'
new Vue ({
    el:"#app",
    render: (h)=>{return h(app)},
    router：router  // 注意 这里其实可以有多个路由属性名变为routers 但是routers [里面必须是配置对象]
});				//这句话就是讲router注入进了根实例 这样所有子实例就可以this.来配置
```

## 2.动态路由匹配

使用动态参数以`:`开头 就像使用`v-bind`绑定一个变量一样

```javascript
const router = new VueRouter({
  routes: [
    // 动态路径参数 以冒号开头
    { path: '/user/:id', component: User }
  ]
})
// 设置了动态路由后
$route.params.id ===》params就有了值 // 注意这里使用的$route不是router//$route是路由对象//在router被注入后产生
在组件里{{$route.params.id}}就能得到 比如这里/user/xxx的xxx 也就是动态的那一部分
```

比如这样：

| 模式                          | 匹配路径            | $route.params                        |
| ----------------------------- | ------------------- | ------------------------------------ |
| /user/:username               | /user/evan          | `{ username: 'evan' }`               |
| /user/:username/post/:post_id | /user/evan/post/123 | `{ username: 'evan', post_id: 123 }` |

只有动态路由才需要在path属性上加东西，其他的都不需要

**$route.path**

- 类型: `string`

字符串，对应当前路由的路径，总是解析为绝对路径，如 `"/foo/bar"`。

**$route.params**

- 类型: `Object`

配置`router：path：'/xx/'`，在html中`router-link to="/xx?aa=xx&bb=oo"`，最后`$route.params={aa:xx, bb:oo}`

一个 key/value 对象，包含了动态片段和全匹配片段，如果没有路由参数，就是一个空对象。

**$route.query**

- 类型: `Object`

  一个 key/value 对象，表示 URL 查询参数。例如，对于路径 `/foo?user=1`，则有 `$route.query.user == 1`，如果没有查询参数，则是个空对象。

**$route.hash**

- 类型: `string`

  当前路由的 hash 值 (带 `#`) ，如果没有 hash 值，则为空字符串。

**$route.fullPath**

- 类型: `string`

  完成解析后的 URL，包含查询参数和 hash 的完整路径。

**$route.matched**

- 类型: `Array<RouteRecord>`

一个数组，包含当前路由的所有嵌套路径片段的**路由记录** 。路由记录就是 `routes` 配置数组中的对象副本 (还有在 `children` 数组)。

```javascript
const router = new VueRouter({
  routes: [
    // 下面的对象就是路由记录
    { path: '/foo', component: Foo,
      children: [
        // 这也是个路由记录
        { path: 'bar', component: Bar }
      ]
    }
  ]
})
```

当 URL 为 `/foo/bar`，`$route.matched` 将会是一个包含从上到下的所有对象 (副本)。

**$route.name**

当前路由的名称，如果有的话。(查看[命名路由](https://router.vuejs.org/zh/guide/essentials/named-routes.html))

==还需注意==和很多重复渲染的地方一样，vue不会完全重新渲染同样的元素，也就意味着，**生命周期钩子不会起作用**，为了监测变化可以使用`watch`$rout对象或者`路由卫士`（就是路由的钩子函数）

```javascript
// watch 监听$route对象
const User = {
  template: '...',
  watch: {
    '$route' (to, from) { // 这里的 to from next也是路由对象
      // 对路由变化作出响应...
    }
  }
}
// 路由卫士
const User = {
  template: '...',
  beforeRouteUpdate (to, from, next) {
    // 响应路由变化
    // 不要忘记调用next()
  }
}

```

还有许多[高级匹配](https://github.com/pillarjs/path-to-regexp#parameters)，甚至还能使用正则表达式匹配

## 3.嵌套路由

```html
<!--现在我们想有个请求地址/index/User/detaile-->
<!--而且如下面所写 User页面（这个h2标签）嵌套了一个view 意思是我想在User页面下面再一次请求detaile-->
<!--将请求的东西放在User页面里-->
<!--  请求——>再请求 不是之前的一次 注意区别动态路由-->
<div class="user">
    <h2>User {{ $route.params.id }}</h2>
    <router-view></router-view>
</div>
```

```javascript
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User,
      children: [
        {
          // 当 /user/:id/profile 匹配成功，
          // UserProfile 会被渲染在 User 的 <router-view> 中
          path: 'profile',
          component: UserProfile
        },
        {
          // 当 /user/:id/posts 匹配成功
          // UserPosts 会被渲染在 User 的 <router-view> 中
          path: 'posts',
          component: UserPosts
        }
      ]
    }
  ]
})

```

==注意==当路径是`/user/:id/`没有后续子路由的时候，是没有任何东西会渲染的，所以可以给一个空的路径的子路由

```javascript
      children: [
        //....
        {
          // 当 /user/:id/ 匹配成功，
          // main组件 会被渲染在 User 的 <router-view> 中
          path: '',// 空路径
          component: main
        },
		//....
      ]
```

## 4.编程触发导航

`router.push(location, onComplete?, onAbort?)`

相对于`router-link to`，你也可以使用方法，在js里面触发跳转

==注意==：在 Vue 实例内部(子组件或者说是组件配置内)，你可以通过 `$router` 访问路由实例。因此你可以调用 `this.$router.push`

注意这里的`push`方法，相当于原生的`history.pushState()`，也就是说会保存在历史记录里。

```javascript
// 字符串 请求 '/home'
router.push('home')//注意这里使用的是router实例方法 不是$route
// 在一个组件里面
export default {
//....
    methods:{
        aa() {// 用this.$router//注意是router不是route//访问router实例
            this.$router.push({name:'HelloWorld',params:{xx:123}})
        }
    }
//....
}
// 对象 请求 '/home'
router.push({ path: 'home' })

// 命名的路由 注意这里使用的name 不是导航到'/name/123' 而是 '一个name为user的路由/123'
//这个路由有可能这么配置
const router = new VueRouter({
  routes: [
    {
      path: '/xxx/ooo/:userId',
      name: 'user',
      component: User
    }
  ]
})
// 那么这里表示 请求 '/xxx/ooo/123'  命名路由在后面会讲到
router.push({ name: 'user', params: { userId: 123 }})

// 带查询参数，变成 /register?plan=private
router.push({ path: 'register', query: { plan: 'private' }})

// 还需要  注意  如果提供了path，那么设置param是无效的
const userId = 123
router.push({ name: 'user', params: { userId }}) // -> /user/123
router.push({ path: `/user/${userId}` }) // -> /user/123
// 这里的 params 不生效
router.push({ path: '/user', params: { userId }}) // -> /user

```

==注意：==如果目的地和当前路由相同，只有参数发生了改变 (比如从一个用户资料到另一个 `/users/1` -> `/users/2`)，你需要使用 [`beforeRouteUpdate`](https://router.vuejs.org/zh/guide/essentials/dynamic-matching.html#响应路由参数的变化) 来响应这个变化 (比如抓取用户信息) ，原因就是动态路由渲染的特性，在动态路由小节有讲

==还有==可以看到这个方法还有两个参数onComplete和onAbort，这是两个回调函数，分别在`导航完成`和`导航中断`时调用，

这里解释一下`导航中断`，**导航到相同路由**或者在**导航到一个路由前导航到另外一个路由**，怎么中断导航呢，参考这一章的第十一小节导航守卫的第一小节全局守卫，里面的next（）方法有讲。

`router.replace(location, onComplete?, onAbort?)`

**不会**向 history **添加**新记录，而是跟它的方法名一样 —— 替换掉当前的 history 记录。

| 声明式                            | 编程式                |
| --------------------------------- | --------------------- |
| `<router-link :to="..." replace>` | `router.replace(...)` |

`router.go(n)`

这个方法的参数是一个整数，意思是在 history 记录中向前或者后退多少步，类似 `window.history.go(n)`

```javascript
// 在浏览器记录中前进一步，等同于 history.forward()
router.go(1)

// 后退一步记录，等同于 history.back()
router.go(-1)

// 前进 3 步记录
router.go(3)

// 如果 history 记录不够用，那就默默地失败呗
router.go(-100)
router.go(100)
```

## 5.命名路由

可以给路由配置取名字，代替使用一长串路径

```javascript
export default new Router({
  routes: [
    {
      path: '/hello/aaa/bbb',
      name: 'HelloWorld',// 给一个长的路由 取了个名字
      component: HelloWorld
    }
  ]
})
// 在代码中可以这样触发
router.push({ name: 'HelloWorld'})
```

在html中就可以这样

```html
<router-link :to="{name:'HelloWorld'}">????</router-link>
```

==注意==很容易遗漏的是，`to`前面有一个`:`，**要记住**，想要在标签里边使用变量，**一定要配合指令**，不然就只是一个字符串

## 6.命名视图

```html
<router-view class="view one"></router-view>
<router-view class="view two" name="a"></router-view>
<router-view class="view three" name="b"></router-view>
```

```javascript
const router = new VueRouter({
  routes: [
    {
      path: '/',
      components: {// 在component后面加上s
        default: Foo,  // default对应的是没有给name属性view标签
        a: Bar,// bar组件对应的name是a
        b: Baz// baz组件对应的name是b
      }
    }
  ]
})

```

## 7.重定向

重定向也是通过 `routes` 配置来完成，下面例子是从 `/a` 重定向到 `/b`：

```javascript
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: '/b' }
  ]
})
```

重定向的目标也可以是一个命名的路由：

```javascript
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: { name: 'foo' }}
  ]
})
```

甚至是一个方法，动态返回重定向目标：

```javascript
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: to => {
      // 方法接收 目标路由to路由对象 作为参数
      // return 重定向的 字符串路径/路径对象
    }}
  ]
})
```

## 8.别名

“重定向”的意思是，当用户访问 `/a`时，URL 将会被替换成 `/b`，然后匹配路由为 `/b`，那么“别名”又是什么呢？

就是请求`/a`和`/b`都是一样的，但是地址栏不会像重定向一样请求`/a`跳转到`/b`，而是请求的是什么地址栏就是什么。

上面对应的路由配置为：

```javascript
const router = new VueRouter({
  routes: [
    { path: '/a', component: A, alias: '/b' }
  ]
})
```

“别名”的功能让你可以自由地将 UI 结构映射到任意的 URL，而不是受限于配置的嵌套路由结构。

## 9.参数（'：'后面的）传递

在之前我们通过`$route`来传递参数

```javascript
const User = {
  template: '<div>User {{ $route.params.id }}</div>'// 在html中仍然使用了js代码 没有得到解耦
}												
const router = new VueRouter({					
  routes: [
    { path: '/user/:id', component: User }
  ]
})
```

通过`props`解耦

```javascript
const User = {
  props: ['id'],// 组件里面设置自定义属性 
  template: '<div>User {{ id }}</div>'// 这样html中没有了js代码  
    								// 注意这里的props定义仍然要和路由里面的定义一样的名称
}
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User, props: true },// 开启自定义属性设置

    // 对于包含命名视图的路由，你必须分别为每个命名视图添加 `props` 选项：
    {
      path: '/user/:id',
      components: { default: User, sidebar: Sidebar },
      props: { default: true, sidebar: false }// 给每个视图name开启props
    }
  ]
})
{ path: '/', component: Hello }, // 没有开启自定义属性
{ path: '/hello/:name', component: Hello, props: true }, // 通过自定义属性传递参数
{ path: '/static', component: Hello, props: { name: 'world' }}, // 传递对象 是静态值 无需：
{ path: '/dynamic/:years', component: Hello, props: dynamicPropsFn }, // 自定义函数 将输入的参数转化
```

布尔模式

如果 `props` 被设置为 `true`，`route.params` 将会被设置为组件属性。 其实是本来是把`route`对象传递给prorps，但是布尔模式传递的是参数

对象模式

如果 `props` 是一个对象，它会被按原样设置为组件属性。当 `props` 是静态的时候有用。也就是说只能**传递静态**值

```javascript
const router = new VueRouter({
  routes: [
    { path: '/promotion/from-newsletter', component: Promotion, props: { newsletterPopup: false } }
  ]
})
```

函数模式

你可以创建一个函数返回 `props`。这样你便可以将参数转换成另一种类型，将静态值与基于路由的值结合等等。

```javascript
const router = new VueRouter({
  routes: [
    { path: '/search', component: SearchUser, props: (route) => ({ query: route.query.q }) }
  ]												// 传递一个参数 route对象
})
```

如果请求`/search?q=vue` 首先会将 `{query: 'vue'}` 作为属性传递给 `SearchUser` 组件。

请尽可能保持 `props` 函数为无状态的，因为它只会在路由发生变化时起作用。如果你需要状态来定义 `props`，请使用包装组件，这样 Vue 才可以对状态变化做出反应。

## 10.使用history模式

可以观察到，默认的情况下，vue的跳转其实是模拟hash，也就是`#/.../...`之类的，因为浏览器并不会将`#`后面的内容，发送给服务端请求。

也许你觉得有个`#`太丑了，想就让路径显示为正常的请求，可以这样

```javascript
const router = new VueRouter({
  mode: 'history',// 配置模式
  routes: [...]
})
```

==但是==还有一个问题，这种模式下你的请求路径会变为`xx/oo/pp`也就是正常的请求，而服务端并不知道这是vue的跳转，结果就是，`404`。为了解决这个问题，服务端需要配置：

```javascript
//在原生node.js
// 原理是对于  任何没有设置的请求  都返回一个index.html 防止出现404
const http = require('http')
const fs = require('fs')
const httpPort = 80

http.createServer((req, res) => {
  fs.readFile('index.htm', 'utf-8', (err, content) => {
    if (err) {
      console.log('We cannot open "index.htm" file.')
    }

    res.writeHead(200, {
      'Content-Type': 'text/html; charset=utf-8'
    })

    res.end(content)
  })
}).listen(httpPort, () => {
  console.log('Server listening on: http://localhost:%s', httpPort)
})

```

## 11.导航守卫

正如其名，`vue-router` 提供的导航守卫主要用来通过**跳转或取消的方式**守卫导航。有多种机会植入路由导航过程中：全局的, 单个路由独享的, 或者组件级的。

记住**参数**(`:`)或**查询**(`?`)的改变并**不会触发**进入/离开的**导航守卫**。你可以通过`watch` `$route` 对象来应对这些变化，或使用 `beforeRouteUpdate` 的组件内守卫

### 1. 全局守卫

你可以使用 `router.beforeEach` 注册一个全局前置守卫：

总之就是使**用router实例**对象来**创建**一个**守卫**

```javascript
const router = new VueRouter({ ... })

router.beforeEach((to, from, next) => {
  // ...
})
// 当一个导航触发时，全局前置守卫按照创建顺序调用。
// 守卫是异步解析执行，此时导航在所有守卫 resolve 完之前一直处于 等待中(await)
// 参考笔记文档的js碎片章节中的 async的使用
```

每种守卫都有三个参数：

- **to: Route**: 即将要进入的目标 [路由对象](https://router.vuejs.org/zh/api/#路由对象)
- **from: Route**: 当前导航正要离开的路由
- **next: Function**: 一定要调用该方法来 **resolve** 这个钩子。执行效果依赖 `next` 方法的调用参数。
  - **next()**: 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 **confirmed** (确认的)。
  - **next(false)**: 中断当前的导航。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 `from` 路由对应的地址。
  - **next('/') 或者 next({ path: '/' })**: 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。你可以向 `next` 传递任意位置对象，且允许设置诸如 `replace: true`、`name: 'home'` 之类的选项以及任何用在 [`router-link` 的 `to` prop](https://router.vuejs.org/zh/api/#to) 或 [`router.push`](https://router.vuejs.org/zh/api/#router-push) 中的选项。
  - **next(error)**: (2.4.0+) 如果传入 `next` 的参数是一个 `Error` 实例，则导航会被终止且该错误会被传递给 [`router.onError()`](https://router.vuejs.org/zh/api/#router-onerror) 注册过的回调。

==一定要调用 next 方法==，否则钩子就不会被 resolved，下一个钩子就不会有反应。

### 2.全局解析守卫

在 2.5.0+ 你可以用 `router.beforeResolve` 注册一个全局守卫。这和 `router.beforeEach` 类似，区别是在导航被确认之前，同时在所有**组件内守卫**和**异步路由组件**被解析**之后**，解析守卫就被调用。

**总之**就是比`beforeEach` 还要靠后一点 具体可以看最后一小节`完整导航顺序`

### 3.全局后置钩子

你也可以注册全局后置钩子，然而**和守卫不同**的是，这些钩子**不会接受** `next` 函数也**不会改变导航本**身：

只有`next()`可以改变导航，既然钩子不支持`next`自然也就不能改变

```javascript
router.afterEach((to, from) => {
  // ...
})
```

### 4.路由独享守卫

你可以在路由配置上直接定义 `beforeEnter` 守卫：

```javascript
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      }
    }
  ]
})
```

这些守卫与全局前置守卫的方法参数是一样的

### 5.组件内守卫

- `beforeRouteEnter`

- `beforeRouteUpdate` (2.2 新增)

- `beforeRouteLeave`

  ```javascript
  const Foo = {
    template: `...`,
    beforeRouteEnter (to, from, next) {
      // 在 ...渲染该组件 ...的对应路由被 confirm 前调用 // confirm就是完全完成了跳转的状态
      // 不！能！获取组件实例 `this`
      // 因为当守卫执行前，组件实例还没被创建
    },
    beforeRouteUpdate (to, from, next) {
      // 在当前路由改变，但是该组件被...复用... 时调用
      // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
      // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
      // 可以访问组件实例 `this`
    },
    beforeRouteLeave (to, from, next) {
      // 导航....离开...该组件的对应路由时调用
      // 可以访问组件实例 `this`
    }
  }
  ```

  这个**离开守卫**通常用来禁止用户在还未保存修改前突然离开。该导航可以通过 `next(false)` 来取消。

  ```javascript
  beforeRouteLeave (to, from , next) {
    const answer = window.confirm('Do you really want to leave? you have unsaved changes!')
    if (answer) {
      next()
    } else {
      next(false)
    }
  }
  ```

### 6.完整导航顺序

1. 导航被触发。
2. 在失活的组件里调用离开守卫。
3. 调用全局的 `beforeEach` 守卫。
4. 在重用的组件里调用 `beforeRouteUpdate` 守卫 (2.2+)。
5. 在路由配置里调用 `beforeEnter`。
6. 解析异步路由组件。
7. 在被激活的组件里调用 `beforeRouteEnter`。
8. 调用全局的 `beforeResolve` 守卫 (2.5+)。
9. 导航被确认。
10. 调用全局的 `afterEach` 钩子。
11. 触发 DOM 更新。
12. 用创建好的实例调用 `beforeRouteEnter` 守卫中传给 `next` 的回调函数。

## 12.过渡效果

别忘了，`route-view`也是像`v-if`那样按需求渲染的，也就是说我们可以控制过渡效果。

```html
<transition>
  <router-view></router-view>
</transition>
<!--transition效果全都适用-->
```

如果想要给不同的路由设置不同的效果，可以给transition加上name

还可以基于当前路由与目标路由的变化关系，动态设置过渡效果：

```javascript
<!-- 使用动态的 transition name -->
<transition :name="transitionName">// 原理就是 给name绑定变量
  <router-view></router-view>
</transition>
// 接着在父组件内
// watch $route 决定使用哪种过渡
watch: {
  '$route' (to, from) {
    const toDepth = to.path.split('/').length
    const fromDepth = from.path.split('/').length
    this.transitionName = toDepth < fromDepth ? 'slide-right' : 'slide-left'
  }
}
```

## 13.数据获取

- **导航完成之后获取**：先完成导航，然后在接下来的组件**生命周期钩子中**获取数据。在数据获取期间显示“加载中”之类的指示。
- **导航完成之前获取**：导航完成前，在**路由进入的守卫中**获取数据，在数据获取成功后执行导航。

**导航完成后获取数据**，我们会马上导航和渲染组件，然后在组件的 `created` 钩子中获取数据。这让我们有机会在数据获取期间展示一个 loading 状态，还可以在不同视图间展示不同的 loading 状态。

假设我们有一个 `Post` 组件，需要基于 `$route.params.id` 获取文章数据：

```html
<template>
  <div class="post">
    <div class="loading" v-if="loading">
      Loading...
    </div>

    <div v-if="error" class="error">
      {{ error }}
    </div>

    <div v-if="post" class="content">
      <h2>{{ post.title }}</h2>
      <p>{{ post.body }}</p>
    </div>
  </div>
</template>
```



```javascript

export default {
  data () {
    return {
      loading: false,
      post: null,
      error: null
    }
  },
  created () {
    // 组件创建完后获取数据，
    // 此时 data 已经被 observed 了
    this.fetchData()
  },
  watch: {
    // 如果路由有变化，会再次执行该方法
    '$route': 'fetchData'
  },
  methods: {
    fetchData () {
      this.error = this.post = null
      this.loading = true
      // replace getPost with your data fetching util / API wrapper
      getPost(this.$route.params.id, (err, post) => {
        this.loading = false
        if (err) {
          this.error = err.toString()
        } else {
          this.post = post
        }
      })
    }
  }
}
```

**导航完成之前获取数据**，我们在导航转入新的路由前获取数据。我们可以在接下来的组件的  `beforeRouteEnter` 守卫中获取数据，当数据获取成功后只调用 `next` 方法。

```javascript
export default {
  data () {
    return {
      post: null,
      error: null
    }
  },
  beforeRouteEnter (to, from, next) {
    getPost(to.params.id, (err, post) => {
      next(vm => vm.setData(err, post))
    })
  },
  // 另一个位置获取 路由改变前，组件就已经渲染完了
  // 逻辑稍稍不同
  beforeRouteUpdate (to, from, next) {
    this.post = null
    getPost(to.params.id, (err, post) => {
      this.setData(err, post)
      next()
    })
  },
  methods: {
    setData (err, post) {
      if (err) {
        this.error = err.toString()
      } else {
        this.post = post
      }
    }
  }
}
```

## 14.滚动行为

使用前端路由，当切换到新路由时，想要页面滚到顶部，或者是保持原先的滚动位置，就像重新加载页面那样。

**注意: 这个功能只在支持 history.pushState 的浏览器中可用。**

当创建一个 Router 实例，你可以提供一个 `scrollBehavior` 方法：

```javascript
const router = new VueRouter({
  routes: [...],
  scrollBehavior (to, from, savedPosition) {
    // return 期望滚动到哪个的位置
  }
})
```

`scrollBehavior` 方法接收 `to` 和 `from` 路由对象。第三个参数 `savedPosition` 当且**仅当** `popstate` 导航 (**通过浏览器的 前进/后退 按钮触发**) 时才可用。

这个方法返回滚动位置的对象信息，长这样：

- `{ x: number, y: number }`
- `{ selector: string, offset? : { x: number, y: number }}` (offset 只在 2.6.0+ 支持)

如果返回一个 falsy (alsy 不是 `false`，就是null、undefined什么的可以被当做false用的值)，或者是一个空对象，那么不会发生滚动。

举例：

```javascript
scrollBehavior (to, from, savedPosition) {
  return { x: 0, y: 0 }// 滚动至顶
}
```

返回 `savedPosition`，在按下 后退/前进 按钮时，就会像浏览器的原生表现那样：

```javascript
scrollBehavior (to, from, savedPosition) {
  if (savedPosition) {
    return savedPosition // 按下后退/前进 该是什么样就是什么样
  } else {
    return { x: 0, y: 0 } // 新网页 至顶
  }
}
```

如果你要模拟“滚动到锚点”的行为：

```javascript
scrollBehavior (to, from, savedPosition) {
  if (to.hash) {// 目标路由的hash
    return {
      selector: to.hash
    }
  }
}
```

---

# :ear_of_rice:状态管理

---

## 1.简单起步

如下面这个图，我们可以用一个`Store`存储所有的变量，数据，然后每个组件需要的时候分发，更新，注意整个是单向的

![1528175932757](C:\Users\dell\29237\笔记\1528175932757.png)

## 2.Vuex

下面就是vuex的思想，将所有组件视图与数据的交互，看成一个巨大的单向流程图，一个动作只能操作自己的下一个目标。

![1528176006928](C:\Users\dell\29237\笔记\1528176006928.png)

### 1.开始

每一个 Vuex 应用的核心就是 store（仓库）。“store”基本上就是一个容器，它包含着你的应用中大部分的**状态 (state)**。Vuex 和单纯的全局对象有以下两点不同：

1. Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。
2. 你**不能**直接**改变** store 中的**状态（steate）**。改变 store 中的状态的唯一途径就是显式地**提交 (commit) mutation**。这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。

简单的创建一个`store`

```javascript
// 如果在模块化构建系统中，请确保在开头调用了 Vue.use(Vuex)
import Vuex from 'vuex'
vue.use('Vuex')
const store = new Vuex.Store({
  state: {// 如上面图中的三个节点 state mutations action
    count: 0// 实际上只能操作mutations和action
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
    action: {
        
    }
})
```

现在，你可以通过 `store.state` 来获取状态对象，以及通过 `store.commit` 方法触发状态变更：

```javascript
store.commit('increment') // 每个节点只能以规定的方法触发

console.log(store.state.count) // -> 1
```

### 2.state

用一个对象就包含了全部的应用层级状态 ，也同时表示每个应用将仅仅包含一个 store 实例 

**获取state**：由于store是响应式的，所以可以通过`computed`来获取变化`state`：

```javascript
// 以上面的计数为例
// 创建一个Counter组件
const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return store.state.count// 每当store.state.count改变时 触发
    }
  }
}
```

==但是==这个模式有个缺点，你需要在每个组件里频繁导入vuex

Vuex 通过 `store` 选项，提供了一种机制将状态**从根组件“注入”**到每一个子组件中（需调用 `Vue.use(Vuex)`）：

```javascript
const app = new Vue({
  el: '#app',
  // 把 store 对象提供给 “store” 选项，这可以把 store 的实例注入所有的子组件
  store,// store:store
  components: { Counter },
  template: `
    <div class="app">
      <counter></counter>
    </div>
  `
})
//在其他组件中
const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return this.$store.state.count// 可以使用  this.$store  来访问
    }
  }
}
```

#### `mapState` 辅助函数

当一个组件需要获取多个状态时候，将这些状态都声明为计算属性会有些重复和冗余。为了解决这个问题，我们可以使用 `mapState` 辅助函数帮助我们生成计算属性，让你少按几次键：

```javascript
// 在单独构建的版本中辅助函数为 Vuex.mapState
import { mapState } from 'vuex'

export default {
  // ...
  computed: mapState({// 映射state
    // 箭头函数可使代码更简练
    count: state => state.count,

    // 传字符串参数 'count' 等同于 `state => state.count`
    // 那还要上面的写法干嘛
    countAlias: 'count',

    // 为了能够使用 `this` 获取局部状态，必须使用常规函数
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
  })
}
```

当映射的计算属性的名称与 state 的子节点名称相同时，我们也可以给 `mapState` 传一个字符串数组。

```javascript
computed: mapState([
  // 映射 this.count 为 store.state.count
  'count'
])
```

#### 对象展开运算符

`mapState` 函数返回的是一个合并的对象，我们使用`...`展开：

```javascript
computed: {
  localComputed () { /* ... */ },
  // 使用对象展开运算符将此对象混入到外部对象中
  ...mapState({
    // ...
  })
}
```

#### Gtter

有时候我们需要从 store 中的 state 中派生出一些状态，例如对列表进行过滤并计数：

```javascript
computed: {
  doneTodosCount () {
    return this.$store.state.todos.filter(todo => todo.done).length
  }
}// 但是多个组件都要过滤 那么每个组件都有一个一模一样的函数 太冗余了！ 
```

 getter就像计算属性一样，且只有当它的依赖值发生了改变才会被重新计算 ,相当于js对象里面的get，只有在读取对象属性的时候才触发

Getter 接受 state 作为其**第一个参数**：

```javascript
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {// 注意是写getters不是getter
    doneTodos: state => {// 这样每次访问Store对象里面的属性 都会触发getters里面的方法
      return state.todos.filter(todo => todo.done)
    }
  }
})
```

Getter 会暴露为 `store.getters` 对象，你可以以属性的形式访问这些值：

```javascript
store.getters.doneTodos // -> [{ id: 1, text: '...', done: true }]
```

Getter 也可以接受其他 getter 作为**第二个参数**：

```javascript
getters: {
  // ...
  doneTodosCount: (state, getters) => {
    return getters.doneTodos.length// 调用另外一个getter的方法
  }
}
```

```javascript
store.getters.doneTodosCount // -> 1
```

我们可以很容易地在任何组件中使用它：

```javascript
computed: {
  doneTodosCount () {
    return this.$store.getters.doneTodosCount// 调用它 // 前提是在跟组件设置了store选项
  }
}
```

你也可以通过让 getter 返回一个函数，来实现给 getter 传参。在你对 store 里的数组进行查询时非常有用。

```javascript
getters: {
  // ...
  getTodoById: (state) => (id) => {
    return state.todos.find(todo => todo.id === id)// 返回store里面的state
  }
}
store.getters.getTodoById(2) // -> { id: 2, text: '...', done: false }
```

##### `mapGetters` 

仅仅是将 store 中的 getter 映射到局部计算属性： 

```javascript
// 先
mapGetters({
  // 把 `this.doneCount` 映射为 `this.$store.getters.doneTodosCount`
  doneCount: 'doneTodosCount'
})


//  然后
import { mapGetters } from 'vuex'

export default {
  // ...
  computed: {
  // 使用对象展开运算符将 getter 混入 computed 对象中
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter',
      // ...
    ])
  }
}
```

### 3.Mutation

更改 Vuex 的 store 中的state的**唯一方法**是提交（commit） mutation 

每个 mutation 都有一个字符串的 **事件类型 (type)** 和 一个 **回调函数 (handler)**。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数： 

```javascript
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {// 这就是回调函数
      // 变更状态      // 但是我们不能直接调用这个函数 实际上需要commit触发
      state.count++		// 这更像是注册了一个increment事件 但是触发条件是commit
    }
  }
})

//应该这样触发
store.commit('increment')
```

你可以向 `store.commit` 传入额外的参数，即 mutation 的 **装载值（payload）**也就是额外传递的数据：

```javascript
// 这里传递了一个10
store.commit('increment', 10)
// ...
mutations: {
  increment (state, n) {// 这里就可以接收到10
    state.count += n
  }
}


// 或者这样提交装载值
// 对象的方法
store.commit('increment', {
  amount: 10
})

// 这样也行
store.commit({
  type: 'increment',
  amount: 10
})
// 对应的
mutations: {
  increment (state, payload) {
    state.count += payload.amount// 这里取值方式也要变
  }
}


```

#### 最遵守一些规则

i. **Vuex 中的 mutation 也需要与使用 Vue 一样遵守一些注意事项**：

1. 最好提前在你的 store 中**初始化**好**所有**所需属性。
2. 当需要在对象上添加新属性时，你应该

- 使用 `Vue.set(obj, 'newProp', 123)`, 或者

- 以新对象替换老对象。例如，利用 stage-3 的[对象展开运算符](https://github.com/sebmarkbage/ecmascript-rest-spread)我们可以这样写：

  ```javascript
  state.obj = { ...state.obj, newProp: 123 }
  ```

ii. **建议用常量指定mutation 事件类型**

使用常量替代 mutation 事件类型在各种 Flux 实现中是很常见的模式。这样可以使 linter 之类的工具发挥作用，同时把这些常量放在单独的文件中可以让你的代码合作者对整个 app 包含的 mutation 一目了然：

```javascript
// mutation-types.js
export const SOME_MUTATION = 'SOME_MUTATION'
// store.js
import Vuex from 'vuex'
import { SOME_MUTATION } from './mutation-types'

const store = new Vuex.Store({
  state: { ... },
  mutations: {
    // 我们可以使用 ES2015 风格的计算属性命名功能来使用一个常量作为函数名
    [SOME_MUTATION] (state) {
      // mutate state
    }
  }
})
```

iii.  **mutation 必须是同步函数** 

在组件中映射commit

```javascript
import { mapMutations } from 'vuex'

export default {
  // ...
  methods: {
    ...mapMutations([//使用扩展运算符
      'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`

      // `mapMutations` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
    ]),
    ...mapMutations({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
    })
  }
}
```

### 4.Action

Action 类似于 mutation，不同在于：

- Action 提交的是 mutation，而不是直接变更state。
- Action 可以包含任意异步操作。

一个简单例子

```javascript
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})
```

Action 函数接受一个与 store 实例具有**相同方法和属性**的 context 对象，因此你可以调用 `context.commit` 提交一个 mutation，或者通过 `context.state` 和 `context.getters` 来获取 state 和 getters。当我们在之后介绍到 [Modules](https://vuex.vuejs.org/zh/guide/modules.html) 时，你就知道 context 对象为什么不是 store 实例本身了。 

```javascript

actions: {
    increment (context) {//本来是这样 我们要用context.commit来触发mutation
        context.commit('increment')
    }
}
actions: {// 用参数解构 直接获取实参的commit属性 绑定到变量commit上
  increment ({ commit }) {
    commit('increment')
  }
}
```

**重点来了**

Action 通过 `store.dispatch` 方法触发：

```javascript
store.dispatch('increment')
```

乍一眼看上去感觉多此一举，我们直接分发 mutation 岂不更方便？实际上并非如此，还记得 **mutation 必须同步执行**这个限制么？Action 就不受约束！我们可以在 action 内部执行**异步**操作：

```javascript
actions: {
  incrementAsync ({ commit }) {
    setTimeout(() => {
      commit('increment')//一个异步操作 commit mutation 
    }, 1000)
  }
}
// 同样支持 装载数据
store.dispatch('incrementAsync', {
  amount: 10
})
// 以对象形式
store.dispatch({
  type: 'incrementAsync',
  amount: 10
})

```

另外一个例子

```javascript
actions: {
  checkout ({ commit, state }, products) {
    // 把当前购物车的物品备份起来
    const savedCartItems = [...state.cart.added]
    // 发出结账请求，然后乐观地清空购物车
    commit(types.CHECKOUT_REQUEST)
    // 购物 API 接受一个成功回调和一个失败回调
    shop.buyProducts(
      products,
      // 成功操作
      () => commit(types.CHECKOUT_SUCCESS),
      // 失败操作
      () => commit(types.CHECKOUT_FAILURE, savedCartItems)
    )
  }
}
```

在组件里面使用

```javascript
import { mapActions } from 'vuex'

export default {
  // ...
  methods: {
    ...mapActions([
      'increment', // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`

      // `mapActions` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.dispatch('incrementBy', amount)`
    ]),
    ...mapActions({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
    })
  }
}

```

**多重嵌套异步操作**，你需要明白 `store.dispatch` 可以处理被触发的 action 的处理函数**返回的 Promise**，并且 `store.dispatch` 仍旧返回 Promise：

```javascript
actions: {
  actionA ({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        commit('someMutation')
        resolve()
      }, 1000)
    })
  }
}
```

现在你可以：

```javascript
store.dispatch('actionA').then(() => {
  // ...
})
```

在另外一个 action 中也可以：

```javascript
actions: {
  // ...
  actionB ({ dispatch, commit }) {
    return dispatch('actionA').then(() => {
      commit('someOtherMutation')
    })
  }
}
```

我还是喜欢这样，如果我们利用 [async / await](https://tc39.github.io/ecmascript-asyncawait/)，我们可以如下组合 action：

```javascript
// 假设 getData() 和 getOtherData() 返回的是 Promise

actions: {
  async actionA ({ commit }) {
    commit('gotData', await getData())//等待getData完成
  },
  async actionB ({ dispatch, commit }) {
    await dispatch('actionA') // 等待 actionA 完成
    commit('gotOtherData', await getOtherData())
  }
}
```

一个 `store.dispatch` 在不同模块中可以触发多个 action 函数。在这种情况下，只有当所有触发函数完成后，返回的 Promise 才会执行。 

## 5.Module

由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。

为了解决以上问题，Vuex 允许我们将 store 分割成**模块（module）**。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割：

```javascript
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态 先.state再.module
store.state.b // -> moduleB 的状态
```

对于模块内部的 mutation 和 getter，接收的**第一个参数**是**模块的局部状态对象**。

```javascript
const moduleA = {
  state: { count: 0 },
  mutations: {
    increment (state) {
      // 这里的 `state` 对象是模块的局部状态
      state.count++
    }
  },

  getters: {
    doubleCount (state) {
      return state.count * 2
    }
  }
}
```

同样，对于模块内部的 action，**局部状态**通过 `context.state` 暴露出来，**根节点**状态则为 `context.rootState`：

```javascript
const moduleA = {
  // ...
  actions: {
    incrementIfOddOnRootSum ({ state, commit, rootState }) {
      if ((state.count + rootState.count) % 2 === 1) {
        commit('increment')
      }
    }
  }
}
```

对于模块内部的 getter，根节点状态会作为**第三个参数**暴露出来：

```javascript
const moduleA = {
  // ...
  getters: {// 第一个是局部state 第二个是局部getter
    sumWithRootCount (state, getters, rootState) {
      return state.count + rootState.count
    }
  }
}
```

### 命名空间  

`namespaced: true`后，自动的模块内的action、mutation 和 getter  变成了局部的，不是全局的

```java
const store = new Vuex.Store({
  modules: {
    account: {
      namespaced: true,

      // 模块内容（module assets）
      state: { ... }, // 模块内的状态已经是嵌套的了，使用 `namespaced` 属性不会对其产生影响
      getters: {
        isAdmin () { ... } // -> getters['account/isAdmin']
      },
      actions: {
        login () { ... } // -> dispatch('account/login')
      },
      mutations: {
        login () { ... } // -> commit('account/login')
      },

      // 嵌套模块
      modules: {
        // 继承父模块的命名空间
        myPage: {
          state: { ... },
          getters: {
            profile () { ... } // -> getters['account/profile']
          }
        },

        // 进一步嵌套命名空间
        posts: {
          namespaced: true,

          state: { ... },
          getters: {
            popular () { ... } // -> getters['account/posts/popular']
          }
        }
      }
    }
  }
})
```

设置了命名空间后，仍然想访问全局的state 和 getter：`rootState` 和 `rootGetter` 会作为第三和第四参数传入 getter，也会通过 `context` 对象的属性传入 action 

 action 或提交 mutation ：将 `{ root: true }` 作为**第三参数**传给 `dispatch` 或 `commit` 即可 

```javascript
modules: {
  foo: {
    namespaced: true,

    getters: {
      // 在这个模块的 getter 中，`getters` 被局部化了
      // 你可以使用 getter 的第四个参数来调用 `rootGetters`
      someGetter (state, getters, rootState, rootGetters) {
        getters.someOtherGetter // -> 'foo/someOtherGetter'
        rootGetters.someOtherGetter // -> 'someOtherGetter'
      },
      someOtherGetter: state => { ... }
    },

    actions: {
      // 在这个模块中， dispatch 和 commit 也被局部化了
      // 他们可以接受 `root` 属性以访问根 dispatch 或 commit
      someAction ({ dispatch, commit, getters, rootGetters }) {
        getters.someGetter // -> 'foo/someGetter'
        rootGetters.someGetter // -> 'someGetter'

        dispatch('someOtherAction') // -> 'foo/someOtherAction'
        dispatch('someOtherAction', null, { root: true }) // -> 'someOtherAction'

        commit('someMutation') // -> 'foo/someMutation'
        commit('someMutation', null, { root: true }) // -> 'someMutation'
      },
      someOtherAction (ctx, payload) { ... }
    }
  }
}
```

​	不仅仅想**在命名空间**访问全局的，还想**注册：**

若需要在带命名空间的模块注册全局 action，你可添加 `root: true`，并将这个 action 的定义放在函数 `handler` 中。例如：

```javascript
{
  actions: {
    someOtherAction ({dispatch}) {
      dispatch('someAction')
    }
  },
  modules: {
    foo: {
      namespaced: true,

      actions: {
        someAction: {
          root: true,
          handler (namespacedContext, payload) { ... } // -> 'someAction'
        }
      }
    }
  }
}
```

在**组件中使用**，命名空间的各种

你可以通过使用 `createNamespacedHelpers` 创建基于某个命名空间辅助函数。它返回一个对象，对象里有新的绑定在给定命名空间值上的组件绑定辅助函数：

```javascript
import { createNamespacedHelpers } from 'vuex'
//记得导入
const { mapState, mapActions } = createNamespacedHelpers('some/nested/module')
// 使用createNamespacedHelpers 转化命名空间'some/nested/module' 返回对应的mapState, mapActions
export default {
  computed: {
    // 在 `some/nested/module` 中查找
    ...mapState({
      a: state => state.a,
      b: state => state.b
    })
  },
  methods: {
    // 在 `some/nested/module` 中查找
    ...mapActions([
      'foo',
      'bar'
    ])
  }
}
```

### 多个模块实例

有时我们可能需要创建一个模块的多个实例，例如：

- 创建多个 store，他们公用同一个模块 (例如当 `runInNewContext` 选项是 `false` 或 `'once'` 时，为了[在服务端渲染中避免有状态的单例](https://ssr.vuejs.org/en/structure.html#avoid-stateful-singletons))
- 在一个 store 中多次注册同一个模块

如果我们使用一个纯对象来声明模块的状态，那么这个状态对象会通过引用被共享，导致状态对象被修改时 store 或模块间数据互相污染的问题。

实际上这和 Vue 组件内的 `data` 是同样的问题。因此解决办法也是相同的——使用一个函数来声明模块状态（仅 2.3.0+ 支持）：

```javascript
const MyReusableModule = {
  state () {
    return {
      foo: 'bar'
    }
  },
  // mutation, action 和 getter 等等...
}
```

### 双向绑定处理

方法是使用带有 setter 的双向绑定计算属性：

```javascript
<input v-model="message">
// ...
computed: {
  message: {
    get () {//注意computed的get/set写法和vuex的不一样//而且vuex没有set 因为不能直接改变state
      return this.$store.state.obj.message
    },
    set (value) {//当改变属性值时触发
      this.$store.commit('updateMessage', value)
    }
  }
}
```

# :bride_with_veil:axios

## 1.简单使用

```javascript
import axios from 'axios'
Vue.prototype.$axios = axios;//将axios对象的所有方法 属性 给 $axios
// 既然我们给了Vue对象赋予了axios 那么所有Vue实例都有了这个属性和方法
this.$axios.get('././', 配置对象).then(res=>..).catch(err=>...).finnaly(()=>...)
									/// 在option预检错误的时候 配置成这样
this.$axios.post('././', 数据,请求配置对象{headers:"content-type":'applocation/x-www-form-urlencoded'})
// 不设置也行 但是数据不能直接传对象 而是传字符串“key1=val1&key2=val2”
// 请求配置还能写 parma //get配置对象在第二个//post配置对象在第3个

//可以设置默认的请求地址
//全局设置 //一般都是全局设置
axios.defaults.baseURL = '....'
//按需设置
就是上面的配置对象里面写
// 你请求'xx'就相当于请求'.../xx'
```

## 2.合并请求

```javascript
// 多个请求，有一个失败就是失败，全部成功才算成功
this.$axios.all([this.$axios.post('.../..', '内容'), 
                 this.$axios.get('../...')])// 分发响应，有几个请求就有几个响应
				.then(this.$axios.spread((res1, res2)=>{...}))
				.catch(err=>consloe.log(err))
```

## 3.拦截器

实际上不是指拦截请求，有点像`express`的中间件，在请求和响应里加点东西什么的，比如`express`的`body-parse`，在请求里加了一个body对象。

```javascript
//全局设置
axios.interceptors.request.use(callback)//这是在  请求   之前
//其中callback 有一个参数数config 就等于 配置对象
// 往其中加入上面请求头、请求默认地址、。。。
function （config）{config.headers={....};return config}
//不return或者return false 就真的拦截了请求 

// config 的一些用法
// 追加请求头//因为本例子设置的是请求拦截
config.header.acept = 'xxx'  //在请求头的acept位置增加了xxx
config.header = {acept：xxx}
```

## 4.拦截器应用

拦截器操作loading

```javascript
//我们要请求和响应的时候 都来控制loading
// 在请求的时候
axios.interceptors.request.use(request)
//为了清晰 单独写一下callback
function request(config) {
    开启loading
}
// 在接收响应的时候
axios.interceptors.response.use(response)
function response(data) {//注意这里不是config对象了 是data对象 本质上是一样 内容也差不多 为了区分
   关闭loading
}
```

