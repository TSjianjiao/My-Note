# :zap:

## 1. 操作dom

操作dom肯定要等渲染完成了在操作，所以要在生命周期的`mounted` 钩子函数里面操作

```javascript
//...
mounted(){
    ///....
}
//...
```

## 2.fullpage.js

```javascript
//因为fullpage.js只能初始化一次 所以要在组件销毁之前清除一次
beforeDestroy() {
    $.fn.fullpage.destroy('all');
}
```

## 3.让编译器识别ES6

```html
<!--把type改一下-->
<script type="text/ecmascript-6">  
</script>
<!--还要记得安装babel-polyfill  不然promise什么的无法支持-->
<!--反正我用async/await都支持  这个就只是记录一下-->
```

## 4.vue-router激活样式

```html
.router-link-active  <!--这个样式是默认 连接激活时 可以自行设置-->
```

## 5.vue事件函数的参数

```javascript
<li v-for="(item, index) in singerList" 
:key="index" 
@click="getSingerDetail($event, item.ting_uid)">
    // 比如这里的li 直接给事件函数传递参数，到点击的时候 就会传递对于的参数
    // 不会像原生js那样 会直接调用函数而不是等到点击的时候再调用
```

## 6. 子组件使用v-model

```javascript
// 父组件中
...
<p>{{total}}</p>
<child v-model="total"></child>
....
// 子组件中
...
methods:{
    computeTotal() {
        this.$emit('input', this.total='1+1')
    }
}
...
```

子组件和父组件传递参数不再是使用`props`而是直接双向绑定。

1.  `v-model`的实质是通过`input`事件监听
2.  `$emit`不仅可以触发自定义事件，也可以触发原生事件

## 7. 多级之间传递数据

```javascript
// 全局引用这个空的Vue实例
const bus = new Vue();
// 父组件
..
<child></child>
...
export default {
    create() {
		const _this = this;//注意保存this，bus是和当前组件不同的实例
        bus.$on('msg', (msg)=>{
            _this.msg = msg
        })
    }
}
// 子组件
bus.$emit('msg', '来自子组件的数据')
```

1.  建立一个空`Vue`实例
2.  通过在这个空实例上注册和监听事件来传递数据