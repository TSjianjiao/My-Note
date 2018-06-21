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



