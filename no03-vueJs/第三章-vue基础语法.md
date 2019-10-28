## 声明：
vueAPI：  
https://vuejs.bootcss.com/v2/api/

vue中文文档：  
https://vuejs.bootcss.com/v2/guide/instance.html

vue菜鸟教程：  
https://www.runoob.com/vue2/vue-tutorial.html

vue组件：element  
https://element.eleme.cn/#/zh-CN/component/installation

## 第一节 

1、{{ }} 用于输出对象属性和函数返回值。
```
<div id="vue_det">
    <h1>site : {{site}}</h1>
    <h1>url : {{url}}</h1>
    <h1>{{details()}}</h1>
    //---可以进行计算---
    {{5+5}}<br>
    {{ ok ? 'YES' : 'NO' }}<br>
    {{ message.split('').reverse().join('') }}
</div>
<script type="text/javascript">
    var vm = new Vue({
        el: '#vue_det',
        data: {
            site: "菜鸟教程",
            url: "www.runoob.com",
            alexa: "10000"
        },
        methods: {
            details: function() {
                return  this.site + " - 学的不仅是技术，更是梦想！";
            }
        }
    })
</script>
```

2、html
  使用 v-html 指令用于输出 html 代码：
```
<div id="app">
    <div v-html="message"></div>
</div>
    
<script>
new Vue({
  el: '#app',
  data: {
    message: '<h1>菜鸟教程</h1>'
  }
})
</script>
```

3、text
  使用 v-text 指令用于输出文本：
```
<div id="app">
    <div v-html="message"></div>
</div>
    
<script>
new Vue({
  el: '#app',
  data: {
    message: '<h1>菜鸟教程</h1>'
  }
})
</script>
```
**说明`v-text`和`v-html`区别：**  
* v-text  输出都是文本  
* v-html  可以输出带HTML标签的文本

4、 v-bind 指令 随着数据变化的属性，要用该指令完成
```
<div id="app-2">
  <span v-bind:title="message">
    鼠标悬停几秒钟查看此处动态绑定的提示信息！
  </span>
  //---可以完全的 JavaScript 表达式支持---
  <div v-bind:id="'list-' + id">菜鸟教程</div>
</div>
var app2 = new Vue({
  el: '#app-2',
  data: {
    message: '页面加载于 ' + new Date().toLocaleString(),
    id : 1
  }
})
```

5、v-if 指令  根据表达式 seen 的值(true 或 false )来决定是否插入 p 元素
```
<div id="app">
    <p v-if="seen">现在你看到我了</p>
</div>
    
<script>
new Vue({
  el: '#app',
  data: {
    seen: true
  }
})
</script>
```

6、v-show 根据表达式 seen 的值(true 或 false )来决定是否隐藏 p 元素
```
<div id="app">
    <p v-show="seen">现在你看到我了</p>
</div>
    
<script>
new Vue({
  el: '#app',
  data: {
    seen: true
  }
})
</script>
```
说明:`v-show `和`v-if `区别：
* v-if   控制是否动态插入元素进行显示和隐藏。用于非频繁进行显示隐藏切换场景。
* v-show 页面中加载，控制display元素进行显示和隐藏。用于频繁进行显示隐藏切换场景。


7、v-on 指令，它用于监听 DOM 事件
```
<div id="app-5">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">逆转消息</button>
</div>

var app5 = new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
```

8、 v-model 指令用来在 input、select、textarea、checkbox、radio 等表单控件元素上创建双向数据绑定，根据表单上的值，自动更新绑定的元素的值。
```

```


```

```


```

```

