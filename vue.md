<p style="font-size:50px;font-weight:bold;width:100%;text-align:center;color:#fff;text-shadow:0 0 15px">屿东</p>
<p style="text-align:center;color:#aaa;position: relative;top:-10px;text-shadow:0 0 10px">QQ:2395183536</p>

# Vue安装

## 安装方式

### CDN引入

```javascript
<!-- 开发环境版本，源码没有经过压缩，可以直接查看源码，学习阶段使用CDN更慢,包含了有帮助的命令行警告 --> 
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<!-- 生产环境版本，优化了尺寸和速度 -->
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
```

### 下载引入

官网下载两种版本引入

```javascript
<div id='app'>{{message}}</div>
<script src="vue.js"></script>
<script>
const app=new Vue({
    el:'#app',
    data:{
        message:'你好'
    }
})    
</script>
```



### npm安装

```
npm install vue
```



# Vue基础语法

## Vue的MVVM

### Model层(数据层)

> 数据可能是我们固定的死数据，更多的是来自我们服务器，从网络上请求下来的数据。
>
> 在我们计数器的案例中，就是后面抽取出来的obj，当然，里面的数据可能没有这么简单。

### View层(视图层)

> 在我们前端开发中，通常就是DOM层。主要的作用是给用户展示各种信息。

### ViewModel层(视图模型层)

> 视图模型层是View和Model沟通的桥梁。
> 一方面它实现了Data Binding，也就是数据绑定，将Model的改变实时的反应到View中
> 另一方面它实现了DOM Listener，也就是DOM监听，当DOM发生一些事件(点击、滚动、touch等)时，可以监听到，并在需要的情况下改变对应的Data。

### MVVM的实现原理

**MVVM的实现主要是三个核心点：**

响应式：vue如何监听data的属性变化
模板解析：vue的模板是如何被解析的
渲染：vue模板是如何被渲染成HTML的



## 插值操作

### mustache语法

```javascript
//可以通过Mustache语法(也就是双大括号)。
//Mustache: 胡子/胡须.
//我们可以像下面这样来使用，并且数据是响应式的
<div id="app">
    <h1>{{message}}</h1>
    <h1>{{message}},小明</h1>
    <h1>{{firstName + lastName}}</h1>
    <h1>{{firstName + " " + lastName}}</h1>
    <h1>{{firstName}} {{lastName}}</h1>
    <h1>{{count * 2}}</h1>
</div>
```

### v-once

> 在某些情况下，我们可能不希望界面随意的跟随改变
>
> 这个时候，我们就可以使用一个Vue的指令
>
> 该指令后面不需要跟任何表达式(比如之前的v-for后面是由跟表达式的)
>
> 该指令表示元素和组件(组件后面才会学习)只渲染一次，不会随着数据的改变而改变。



### v-html

> 如果我们直接通过{{}}来输出，会将HTML代码也一起输出。
>
> 但是我们可能希望的是按照HTML格式进行解析，并且显示对应的内容。
>
> 可以使用v-html指令
>
> 该指令后面往往会跟上一个string类型
>
> 会将string的html解析出来并且进行渲染

```html
<h1 v-html="url"></h1>
```

### v-text

> v-text作用和Mustache比较相似：都是用于将数据显示在界面中,但是会覆盖原有的内容
>
> v-text通常情况下，接受一个string类型

```html
<h1 v-text="message"></h1>
```

### v-pre

> v-pre用于跳过这个元素和它子元素的编译过程，用于显示原本的Mustache语法

```html
<h1 v-pre>{{message}}</h1> //{{message}}
```

### v-cloak

> 在js还未执行情况下，我们浏览器可能会直接显然出未编译的Mustache标签。
>
> cloak: 斗篷
>
> 作用：在js未执行时遮住dom的内容，执行后删除cloak

```html
<h1 v-cloak>{{message}}</h1>
```

## 绑定属性

### v-bind

```javascript
<!-- 
    v-bind用于绑定一个或多个属性值，或者向另一个组件传递props值
   -->
<div id="app">
  <!-- 错误的做法: 这里不可以使用mustache语法-->
  <!--<img src="{{imgURL}}" alt="">-->
  
  <!-- 正确的做法: 使用v-bind指令 -->
  <img v-bind:src="imgURL" alt="">
  <a v-bind:href="aHref">百度一下</a>
  <!--<h2>{{}}</h2>-->
 
  <!--语法糖的写法-->
  <img :src="imgURL" alt="">
  <a :href="aHref">百度一下</a>
</div>
 
<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '你好啊',
      imgURL: 'https://img11.360buyimg.com/',
      aHref: 'http://www.baidu.com'
    }
  })
</script>
```

### v-bind对象语法

```html
用法一：直接通过{}绑定一个类
<h2 :class="{'active': isActive}">Hello World</h2>
 
用法二：也可以通过判断，传入多个值
<h2 :class="{'active': isActive, 'line': isLine}">Hello World</h2>
 
用法三：和普通的类同时存在，并不冲突
注：如果isActive和isLine都为true，那么会有title/active/line三个类
<h2 class="title" :class="{'active': isActive, 'line': isLine}">Hello World</h2>
 
用法四：如果过于复杂，可以放在一个methods或者computed中
注：classes是一个计算属性
<h2 class="title" :class="classes">Hello World</h2>
```

### v-bind数组语法

```html
用法一：直接通过[]绑定一个类
<h2 :class="['active']">Hello World</h2>
 
用法二：也可以传入多个值
<h2 :class="['active', 'line']"> World</h2>
 
用法三：和普通的类同时存在，并不冲突
注：会有title/active/line三个类
<h2 class="title" :class="[‘active’, 'line']">Hello World</h2>
 
用法四：如果过于复杂，可以放在一个methods或者computed中
注：classes是一个计算属性
<h2 class="title" :class="classes">Hello World</h2>
```

## 计算属性

>   我们知道，在模板中可以直接通过插值语法显示一些data中的数据。
>   但是在某些情况，我们可能需要对数据进行一些转化后再显示，或者需要将多个数据结合起来进行显示
>   比如我们有firstName和lastName两个变量，我们需要显示完整的名称。但是如果多个地方都需要显示完整的名称，我们就需要写多个{{firstName}} {{lastName}}
>   我们可以换成写在实例的computed选项中的计算属性
>
>   注：相比于methods，computed只执行一次，有缓存机制，性能更好

```javascript
  <h2>{{fullName}}</h2>
  <script src="../js/vue.js"></script>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        firstName: 'Lebron',
        lastName: 'James'
      },
      // computed: 计算属性()
      computed: {
         // computed里面定义的也是函数，但是函数名一般不加类似于get的动词 
        //  但是它叫计算属性，所以一般起类似于属性的名字
        fullName: function () { // 这样写其实也是一个语法糖 里面有setter和getter 这个是getter
          return this.firstName + ' ' + this.lastName
        }
      },
      methods: {
        getFullName() {
          return this.firstName + ' ' + this.lastName
        }
      }
    })
  </script>
```

## 侦听器watch

> 监听data或者props数据的变化触发的函数，默认情况下我们的侦听器只会针对监听的数据本身的改变（内部发生的改变是不能侦听）

```javascript
watch：{
    message(newValue, oldValue){
        console.log(newValue)
    }
}

// 深度侦听/立即执行(数据未变化也会执行一次)
watch：{
    message:{
        handler: function(newValue, oldValue){
        	console.log(newValue)
        },
        deep: true, //深度侦听
        immediate: true //立即执行
    }
}

// 只想侦听对象里属性的改变
watch：{
    "message.name": function(newValue, oldValue){
        console.log(newValue)
    }
}

//  用API侦听(可以传三个参数，属性名，函数，配置项)
created(){
    this.$watch("message",function(newValue, oldValue){
        console.log(newValue)
    },{
        deep: true, //深度侦听
        immediate: true //立即执行
    })
}
```



## ES6字面量的增强写法

### 属性的增强写法

```javascript
// ES5的写法
  const obj = {
    name: name,
    age: age,
    height: height
  }
 console.log(obj);
// ES6的写法
  const obj = {
    name,
    age,
    height,
  }
  console.log(obj);
```

### 函数的增强写法

```javascript
 // ES5的写法
  const obj = {
    run: function () {
    },
    eat: function () {
    }
  }
  // ES6
  const obj = {
    run() {
    },
    eat() {
    }
  }
```

## 事件监听v-on

### v-on基础

```javascript
<!-- 
  v-on介绍
作用：绑定事件监听器
缩写：@
 -->
<div id="app">
  <h2>{{counter}}</h2>
  <button @click="increment">+</button>
  <button @click="decrement">-</button>
</div>
<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      counter: 0
    },
    methods: {
      increment() {
        this.counter++
      },
      decrement() {
        this.counter--
      }
    }
  })
</script>
```

### v-on参数

```javascript
 <div id="app">
  <!--1.事件调用的方法没有参数 ()可以不添加-->
  <button @click="btn1Click">按钮1</button>
  <!--2.在事件定义时, 写方法时省略了小括号, 
    但是方法本身是需要一个参数的, 这个时候, Vue会默认将浏览器生产的event事件对象作为参数传入到方法-->
  <button @click="btn2Click">按钮2</button>
  <!--3.方法定义时, 我们需要event对象, 同时又需要其他参数-->
  <!-- 在调用方式, 如何手动的获取到浏览器参数的event对象: $event-->
  <button @click="btn3Click(abc, $event)">按钮3</button>
</div>
  // 如果函数需要参数,但是没有传入, 那么函数的形参为undefined
  // function abc(name) {
  //   console.log(name);
  // }
  //
  // abc()
```

### v-on修饰符

> .stop - 调用 event.stopPropagation()。
> .prevent - 调用 event.preventDefault()。
> .{keyCode | keyAlias} - 只当事件是从特定键触发时才触发回调。
> .native - 监听组件根元素的原生事件，如组件不能直接@click监听点击，得加修饰符才可以
> .once - 只触发一次回调。

```javascript
  <!--1. .stop修饰符的使用  停止冒泡-->
  <div @click="divClick">
    aaaaaaa
    <button @click.stop="btnClick">按钮</button>
  </div>
 
  <!--2. .prevent修饰符的使用 阻止默认行为-->
  <br>
  <form action="baidu">
    <input type="submit" value="提交" @click.prevent="submitClick">
  </form>
 
  <!--3. .监听某个键盘的键帽-->
  <input type="text" @keyup.enter="keyUp">
  <input type="text" @keyup.13="keyUp">    
 
  <!--4. .once修饰符的使用 点击回调只会触发一次-->
  <button @click.once="btn2Click">按钮2</button>
</div>

 <!--5..串联修饰符-->
  <div @click="divClick">
    aaaaaaa
    <button @click.stop.prevent="btnClick">按钮</button>
  </div>
```



## 条件判断v-if

### v-if、v-else-if、v-else

```javascript
<div id="app">
  <h2 v-if="score>=90">优秀</h2>
  <h2 v-else-if="score>=80">良好</h2>
  <h2 v-else-if="score>=60">及格</h2>
  <h2 v-else>不及格</h2>  
  <h1>{{result}}</h1>
</div>
<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      score: 99
    },
    computed: {
      result() {
        let showMessage = '';
        if (this.score >= 90) {
          showMessage = '优秀'
        } else if (this.score >= 80) {
          showMessage = '良好'
        }
        return showMessage
      }
    }
  })
</script>
```

### v-show的使用和v-if的区别

- 区别
  - v-if当条件为false时，压根不会有对应的元素在DOM中。
  - v-show当条件为false时，仅仅是将元素的display属性设置为none而已。
- 开发中如何选择呢？
  - 当需要在显示与隐藏之间切换很频繁时，使用v-show
  - 当只有一次切换时，通过使用v-if



## 遍历循环v-for

### v-for遍历数组

- 如果在遍历的过程中不需要使用索引值v-for="item in items"

- 依次从items中取出item，并且在元素的内容中，我们可以使用Mustache语法，来使用item

- 如果在遍历的过程中，我们需要拿到元素在数组中的索引值呢？

  语法格式：v-for=(item, index) in items

- 其中的index就代表了取出的item在原数组的索引值。

```javascript
<div id="app">
  <!--1.在遍历的过程中,没有使用索引值(下标值)-->
  <ul>
    <li v-for="item in names">{{item}}</li>
  </ul>
   <!--2.在遍历的过程中, 获取索引值-->
  <ul>
    <li v-for="(item, index) in names">
      {{index+1}}.{{item}}
    </li>
  </ul>
</div>
<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      names: ['why', 'kobe', 'james', 'curry']
    }
  })
</script>
```

### v-for遍历对象

```javascript
<div id="app">
  <!--1.在遍历对象的过程中, 如果只是获取一个值, 那么获取到的是value-->
  <ul>
    <li v-for="item in info">{{item}}</li>
  </ul>
  <!--2.获取key和value 格式: (value, key) -->
  <ul>
    <li v-for="(value, key) in info">{{value}}-{{key}}</li>
  </ul>
  <!--3.获取key和value和index 格式: (value, key, index) -->
  <ul>
    <li v-for="(value, key, index) in info">{{value}}-{{key}}-{{index}}</li>
  </ul>
</div>
<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      info: {
        name: 'why',
        age: 18,
        height: 1.88
      }
    }
  })
</script>
```

### v-for的key属性

- 官方推荐我们在使用v-for时，给对应的元素或组件添加上一个:key属性。
- 为什么需要这个key属性呢（了解）？
  - 这个其实和Vue的虚拟DOM的Diff算法有关系。
  - 加之前是线性表，加之后是链表



### 数组中哪些方法是响应式的？

```javascript
          //push,pop,unshift,shift可以数组响应式变化
          // **注意: 通过索引值修改数组中的元素 
          // Vue内部没有监听这种方式 不是响应式 数组有变化，界面无更新
          // this.letters[0] = 'bbbbbb';
          // 用其他方法
          // 法1
          // this.letters.splice(0, 1, 'bbbbbb')
 
          // 法2
          // set(要修改的对象, 索引值, 修改后的值)
          // Vue.set(this.letters, 0, 'bbbbbb
```

## 过滤器filters

```javascript
//Vue3已经停止使用
//参数 | 过滤器
//自动传参
<tr>{{item.price | showPrice}}</tr>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      },
    filters:{
        showPrice(price){
            return "￥" + price.toFixed(2)
        }
    }
    }
  })
</script>
```

## 高阶函数

### for...in

```javascript
//遍历对象
for(let i in this.books){
}
//如果用来遍历数组，i是数组每一项的索引，不是item
```

### for...of

```javascript
//遍历数组
for(let n of this.books){
}
```

### filter

```javascript
// filter中的回调函数有一个要求: 必须返回一个boolean值
// true: 当返回true时, 函数内部会自动将这次回调的n加入到新的数组中
// false: 当返回false时, 函数内部会过滤掉这次的n
const nums = [10, 20, 111, 222, 444, 40, 50]
let numss = nums.filter(function(n){
return n < 100
})
```

### map

```javascript
// 20, 40, 80, 100对数组元素改变映射
let new2Nums = newNums.map(function (n) {
  return n * 2
})
console.log(new2Nums);//40,80,160,200
```

### reduce

```javascript
// reduce 作用对数组中所有的内容进行汇总
let total = new2Nums.reduce(function (preValue, n) {
  return preValue + n
}, 0)
console.log(total);
//上一次的值加遍历的每个值
```

## 双向数据绑定v-model

### 基本语法

```javascript
//Vue中使用v-model指令来实现表单元素和数据的双向绑定。 
<div id="app">
  <input type="text" v-model="message">
  {{message}}
</div>
<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '你好啊'
    }
  })
</script
```

### 原理

```javascript
//v-model其实是一个语法糖，它的背后本质上是包含两个操作：
//1.v-bind绑定一个value属性
<input type="text" v-model="message">
//2.v-on指令给当前元素绑定input事件
<input type="text" v-bind:value="message" v-on:input="message = $event.target.value">
```

### v-model修饰符

```javascript
 <div id="app">
  <!--1.修饰符: lazy 让数据在失去焦点或者回车时才会更新-->
  <input type="text" v-model.lazy="message">
  <h2>{{message}}</h2>
  <!--2.修饰符: number 让在输入框中输入的内容自动转成数字类型-->
  <input type="number" v-model.number="age">
  <h2>{{age}}-{{typeof age}}</h2>
  <!--3.修饰符: trim 过滤内容左右两边的空格-->
  <input type="text" v-model.trim="name">
  <h2>您输入的名字:{{name}}</h2>
</div>
<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '你好啊',
      age: 0,
      name: ''
    }
  })
</script>
```





# 组件化开发

## 注册组件的基本步骤

### 创建组件构造器

```javascript
//所有组件都继承Vue.prototype原型
// 1.创建组件构造器对象  extend() 没有s
    const cpnC = Vue.extend({
      // 自定义组件的模板 使用到组件的地方，要显示的HTML代码
      // *最外需要一个div包裹
      template: `
      <div>
        <h2>我是标题</h2>
        <p>我是内容, 哈哈哈哈</p>
        <p>我是内容, 呵呵呵呵</p>
      </div>`
      })
 //事实上，这种写法在Vue2.x的文档中几乎已经看不到了，它会直接使用下面我们会讲到的语法糖
```

### 注册组件

```javascript
 // 2.注册组件(全局注册) 
    // 需要传递两个参数：
    // 1、注册组件的标签名 (必须加引号)
    // 2、组件构造器
 Vue.component('my-cpn', cpnC)

//(注册局部组件)在vue实例里注册就只能在该实例挂载元素下使用
 const app = new Vue({
    el: '#app',
    data: {
    },
    component:{
        cpn:cpnC
    }
  })
```

### 使用组件

```javascript
//组件必须挂载在某个Vue实例下，否则它不会生效
<div id="app">
    <!--3.在Vue实例范围内使用组件-->
    <my-cpn></my-cpn>
<!-- 如果组件中间没有内容，也可以写成单标签 <my-cpn/> -->
 </div>
 
 <my-cpn></my-cpn> 
 //不起作用
```

### 创建组件语法糖

```javascript
  <div id="app">
    <cpn1></cpn1>
    <cpn2></cpn2>
  </div>
  <script src="../js/vue.js"></script>
  <script>
    // 主要是省去了调用Vue.extend()的步骤，而是可以直接使用一个对象来代替
    // 1.全局组件注册的语法糖
    // * Vue.component标签名必须加引号
    Vue.component('cpn1', {
      template: `
      <div>
        <h2>我是标题1</h2>
        <p>我是内容, 哈哈哈哈</p>
      </div>
    `
    })
    // 2.注册局部组件的语法糖
    const app = new Vue({
      el: '#app',
      data: {
        message: '你好啊'
      },
      components: {
        'cpn2': {
          template: `
          <div>
            <h2>我是标题2</h2>
            <p>我是内容, 呵呵呵</p>
          </div>
         `
        }
      }
    })
  </script>
```

## 为什么data在组件中必须是函数

首先，如果不是一个函数，Vue直接就会报错。

其次，原因是在于Vue让每个组件对象都返回一个新的对象，因为如果是同一个对象的，组件在多次使用后会相互影响.

组件中的data写成一个函数，数据以函数返回值形式定义，这样每复用一次组件，就会返回一份新的data，类似于给每个组件实例创建一个私有的数据空间，让各个组件实例维护各自的数据。

而单纯的写成对象形式，就使得所有组件实例共用了一份data，就会造成一个变了全都会变的结果。

所以说vue组件的data必须是函数。这都是因为js的特性带来的，跟vue本身设计无关

## 父子组件

```javascript
 <div id="app">
    <cpn2></cpn2>
    <!-- 子组件错误用法：未在全局或局部注册以子标签的形式在Vue实例中使用 -->
    <!-- 正确写法：在父组件的components注册,在template中使用子组件标签 -->
  </div>
  <script src="../js/vue.js"></script>
  <script>
    // 1.创建第一个组件构造器(子组件)
    const cpnC1 = Vue.extend({
      template: `
      <div>
        <h2>我是标题1</h2>
        <p>我是内容, 哈哈哈哈</p>
      </div>
    `
    })
    // 2.创建第二个组件构造器(父组件)
    const cpnC2 = Vue.extend({
      // 2.2 在 template 中使用 <cpn1></cpn1>
      template: `
      <div>
        <h2>我是标题2</h2>
        <p>我是内容, 呵呵呵呵</p>
        <cpn1></cpn1>
      </div>
    `,
      // 2.1 在components注册子组件cpn1
      components: {
        cpn1: cpnC1
      }
    })
    
// vue实例是root根组件，也就是其它组件的父组件
    const app = new Vue({
      el: '#app',
      data: {
        message: '你好啊'
      },
      components: {
        cpn2: cpnC2
      }
    })
  </script>
```



## 组件模板抽离

```javascript
  <!--1.script标签, 注意:类型必须是text/x-template 然后给它设置一个id -->
  <script type="text/x-template" id="cpn">
    <div>
      <h2>我是标题</h2>
      <p>我是内容,哈哈哈</p>
    </div>
  </script>
 
  <!--2.template标签-->
  <template id="cpn">
    <div>
      <h2>我是标题</h2>
      <p>我是内容,呵呵呵</p>
    </div>
  </template>
 
  <script src="../js/vue.js"></script>
  <script>
    // 1.注册一个全局组件
    Vue.component('cpn', {
      template: '#cpn' // 需要加上选择器
    })
 
    const app = new Vue({
      el: '#app',
      data: {
        message: '你好啊'
      }
    })
  </script>
```



## Vue组件通信

- 父子组件：
  - 使用`props`和`emit`进行通信
  - 使用使用`attrs`(可以通过`v-bind="$attrs"`向下级传递)
  - `expose/ref`父组件获取子组件的属性或者调用子组件方法
  - `v-model`数据双向绑定
- 爷孙组件：
  - 使用`两次父子组件间`通信来实现
  - 使用`provide+inject`来通信
- 任意组件：
  - vue2使用`eventBus=new Vue()`通信
  - vue3使用如`mitt库`通信
  - vue2 => `Vuex ` vue3 => `Pinia`



## 父子组件通信

### 父级向子级传递props

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>
 
  <div id="app">
    <!--<cpn v-bind:cmovies="movies"></cpn>-->
    
    <!-- 没有v-bind直接这样写是给prop传递一个静态的值，也就是说movies不是一个变量而是一个字符串 -->
    <!--<cpn cmovies="movies" cmessage="message"></cpn>-->
 
    <!-- 步骤2 通过:cmessage="message" 将data中的数据传给子组件props -->
    <cpn :cmessage="message" :cmovies="movies"></cpn>
  </div>
  <!-- 子组件 -->
  <template id="cpn">
    <div>
      <!-- 步骤3 将props中的值显示在子组件中 -->
      <ul>
        <li v-for="item in cmovies">{{item}}</li>
      </ul>
      <h2>{{cmessage}}</h2>
    </div>
  </template>
 
  <script src="../js/vue.js"></script>
  <script>
    // 父传子: props
    // -------子组件------
    const cpn = {
      template: '#cpn',
      /* ***步骤1*** 在 子组件 定义props */
      props: ['cmovies', 'cmessage'], // 不要把元素当成字符串，把它当成数组
      props:{
       // 2.提供一些默认值, 以及必传值
        cmessage: {
          type: String,
          default: 'aaaaaaaa',
          required: true // 必填的字符串
        },
        // 类型是对象或者数组时, 默认值必须是一个工厂函数
        cmovies: {
          type: Array,
          default () {
            return {
              message: 'hello'
            }
          }
      },
     data {
        return {}
      }
    }
    // -----父组件-----
    const app = new Vue({
      el: '#app',
      data: {
        message: '你好啊',
        movies: ['海王', '海贼王', '海尔兄弟']
      },
      components: {
        cpn
      }
    })
  </script>
 // 步骤：
 //1.在子组件里写props
 // 2.在子组件的标签加上v-bind  <cpn v-bind:props里定义的名称="父组件data数据名称"></cpn>
 // 3.将props中的值显示在子组件中
</body>
</html>
```

### props中的驼峰标识

   HTML 中的 attribute 名是大小写不敏感的，所以浏览器会把所有大写字符解释为小写字符。这意味着当你使用 DOM 中的模板时，camelCase (驼峰命名法) 的 prop 名需要使用其等价的 kebab-case (短横线分隔命名) 命名



### 子级向父级传递-自定义事件

```javascript
  <!--父组件模板-->
  <div id="app">
    <!-- 3.在父组件子标签中，通过v-on来监听子组件事件 并添加一个响应该事件的处理方法 -->
    <cpn @item-click="cpnClick"></cpn>
  </div>
  <!--子组件模板-->
  <template id="cpn">
    <div>
      <!-- 1.在子组件中创建一个按钮，给按钮绑定一个点击事件-->
      <button v-for="item in categories" @click="btnClick(item)">
        {{item.name}}
      </button>
    </div>
  </template>
  <script src="../js/vue.js"></script>
  <script>
    // 子传父 自定义事件
    // 子组件 
    const cpn = {
      template: '#cpn',
      data() {
        return {
          categories: [{
              id: 'aaa',
              name: '热门推荐'
            },
            {
              id: 'bbb',
              name: '手机数码'
            },
            {
              id: 'ccc',
              name: '家用家电'
            },
            {
              id: 'ddd',
              name: '电脑办公'
            },
          ]
        }
      },
      methods: {
        btnClick(item) {
          // 发射事件: 自定义事件
          // 2.在子组件中，通过$emit()来触发事件
          this.$emit('item-click', item)
          // 注意！！！！这里的$emit事件名不要写成驼峰！！！脚手架里可以，会先编译成一个组件对象render函数
        }
      }
    }
    // 父组件 
    const app = new Vue({
      el: '#app',
      data: {
        message: '你好啊'
      },
      components: {
        cpn
      },
      methods: {
        cpnClick(item) {//这里的参数是接收子组件传过来的数据
          console.log('cpnClick', item); 
        }
      }
    })
  </script>
```

## 父子组件的访问

### 父访问子$children

```javascript
<!--this.$children是一个数组类型，它包含所有子组件对象--!>
<div id="app">
    <cpn></cpn>
    <cpn></cpn>
    <my-cpn></my-cpn>
    <y-cpn></y-cpn>
    <button @click="btnClick">按钮</button>
</div>
  <template id="cpn">
    <div>我是子组件</div>
  </template>
  <script src="../js/vue.js"></script>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: '你好啊'
      },
      methods: {
        btnClick() {
          // 1.$children
          // console.log(this.$children);
          this.$children[0].showMessage(); // 子组件的方法可以听过这种方式调用
          // this.$children是一个数组类型，它包含所有子组件对象。
        }
      },
      components: {
        cpn: {
          template: '#cpn',
          data() {
            return {
              name: '我是子组件的name'
            }
          },
          methods: {
            showMessage() {
              console.log('showMessage');
            }
          }
        },
      }
    })
  </script>
```

### 父访问子$refs

children的缺陷：

- 通过`$children`访问子组件时，是一个数组类型，访问其中的子组件必须通过索引值。
- 但是当子组件过多，我们需要拿到其中一个时，往往不能确定它的索引值，甚至还可能会发生变化。
- 有时候，我们想明确获取其中一个特定的组件，这个时候就可以使用

$refs的使用：

- `$refs`和`ref`指令通常是一起使用的。
- 首先，我们通过`ref`给某一个子组件绑定一个特定的ID。
- 其次，通过`this.$refs.ID`就可以访问到该组件了。
- 注：如果给一个标签如`div`设置`ref`而不是一个子组件，则是拿到该标签，相当于`document.querySelector('div')`[不用这个是因为vue不建议直接操作DOM，这样会跨页面获取]

```javascript
 <div id="app">
    <my-cpn></my-cpn>
    <cpn ref="aaa"></cpn>
    <button @click="btnClick">按钮</button>
  </div>
  <template id="cpn">
    <div>我是子组件</div>
  </template>
  <script src="../js/vue.js"></script>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: '你好啊'
      },
      methods: {
        btnClick() {
   // 2.$refs reference(引用)  => 对象类型, 默认是一个空的对象 ref='bbb'
          console.log(this.$refs.aaa.name);
        }
      },
      components: {
        cpn: {
          template: '#cpn',
          data() {
            return {
              name: '我是子组件的name'
            }
          },
          methods: {
            showMessage() {
              console.log('showMessage');
            }
          }
        },
      }
    })
  </script>
```

### 子访问父$parent

如果我们想在子组件中直接访问父组件，可以通过$parent

注意事项：

- 尽管在Vue开发中，我们允许通过$parent来访问父组件，但是在真实开发中尽量不要这样做。
- 子组件应该尽量避免直接访问父组件的数据，因为这样耦合度太高了。
- 如果我们将子组件放在另外一个组件之内，很可能该父组件没有对应的属性，往往会引起问题。
- 另外，更不好做的是通过$parent直接修改父组件的状态，那么父组件中的状态将变得飘忽不定，很不利于我的调试和维护。

### 子访问根$root

如果我们想在子组件中直接访问根组件，可以通过$root



## 插槽slot

### 基本使用

```html
插槽
      插槽，也就是slot，是组件的一块HTML模板，这块模板显示不显示、以及怎样显示由父组件来决定,简而言之，插槽就是预留的空间（位置），即占位符
      可以放置在组件的任意位置 一个组件中只能有一个该类插槽
      
      1.插槽的基本使用 <slot></slot>
      2.设置插槽的默认值 <slot><button>按钮</button></slot>
      3.如果有多个值,同时放入到组件进行替换时, 一起作为替换元素
      4.如果不使用插槽，往组件标签里写东西是没有效果的
```

### 具名插槽slot

```html
 <!-- 
  在2.6.0中，vue为具名插槽和作用域插槽引入了一个新的统一的语法 (即 v-slot 指令)。它取代了slot和slot-scope这两个目前已被废弃但未被移除且仍在文档中的attribute。
  注意 v-slot 只能添加在 < template > 上，只有当被提供的内容只有默认插槽时，
  组件的标签才可以被当作插槽的模板来使用。这样我们就可以把 v-slot 直接用在组件
 -->
    <div id="app">
        <!-- 默认 -->
        <cpn></cpn>
        <hr>
        <!-- 使用v-slot替换 -->
        <cpn>
            <!-- vscode 快捷语法 vslot-named -->
            <template v-slot:left>
                <span>返回</span>
            </template>
        </cpn>
        <!-- 
        跟 v-on 和 v-bind 一样，v-slot 也有缩写，即把参数之前的所有内容 (v-slot:) 替换为字符 #。
        例如 v-slot:header 可以被重写为 #header：，前提是必须要有插槽名！！！
        -->
          <cpn>
            <template #right>
                <span>替换后的右边</span>
            </template>
        </cpn>
    </div>
    <template id="cpn">
        <div>
            <slot name="left"><span>左边</span></slot>
            <slot name="center"><span>中间</span></slot>
            <slot name="right"><span>右边</span></slot>
            <slot>默认插槽内容</slot>
        </div>
    </template>
    <!-- 需要重新引入一个vue2.6之后的版本 -->
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
    <script>
        const cpn = {
            template: '#cpn'
        }
        const app = new Vue({
            el: '#app',
            data: {},
            components: {
                cpn
            }
        })
    </script>
```

### 作用域插槽

官方文档



# webpack

## es6的导入导出

```javascript
// 1.导入的{}中定义的变量
let flag = true;
let sum = 1;
export {
  flag, sum
}
import {flag, sum} from "./aaa.js";
 
// 2.直接导入export定义的变量
export let num1 = 1;
export let height=180;
import {num1, height} from "./aaa.js";
 
// 3.导入 export的function/class
export function mul(num1, num2) {
  return num1 * num2
}
import {mul, Person} from "./aaa.js";

// 4.导入 export default中的内容 可以根据需要命名它对应的名字
//export default导出的导入不用import{addr}，直接import addr
// export default在同一个模块中，不允许同时存在多个
export default address;
import addr from "./aaa.js";

// 5.统一全部导入 
import {flag, num, num1, height, Person, mul, sum} from "./aaa.js";
// 通过*可以导入模块中所有的export变量
// 但是通常情况下我们需要给*起一个别名，方便后续的使用
import * as aaa from './aaa.js'
```

## webpack安装

1. 安装webpack首先需要安装Node.js，Node.js自带了软件包管理工具npm
2. 查看自己的node版本

```
node -v
```

3. 全局安装webpack(这里我先指定版本号3.6.0，因为vue cli2依赖该版本)

```
npm install webpack@3.6.0 -g
npm install webpack webpack-cli -g
```

4. 局部安装webpack（后续才需要）

​    --save-dev是开发时依赖，项目打包后不需要继续使用的。 

```
cd 对应目录
npm install webpack@3.6.0 --save-dev  或者-D简写
npm install webpack webpack-cli
```

5. 生成package.json(管理记录项目包依赖)

```
npm init	  // 需要配置	
npm init -y   // 无需配置直接生成
```

- 为什么全局安装后，还需要局部安装呢？
  - 在终端直接执行webpack命令，使用的全局安装的webpack，全局安装的版本可能与不同项目的webpack版本不兼容
  - 当在package.json中定义了scripts时，其中包含了webpack命令，那么使用的是局部webpack

## webpack的基本使用

- 文件和文件夹解析：
- dist文件夹(disdurition发布分布)：用于存放之后打包的文件
- src文件夹(source源码)：用于存放我们写的源文件

​         main.js：项目的入口文件。具体内容查看下面详情。

​         mathUtils.js：定义了一些数学工具函数，可以在其他地方引用   

​         并且使用。具体内容查看下面的详情。

- index.html：浏览器打开展示的首页html
- package.json：通过npm init生成的，npm包管理的文件（暂时没有用上，后面才会用上）

打包命令

注：npx webpack 和 npm run build打包命令默认是找项目目录下的src里的index.js作为入口文件来打包

```
webpack // 全局安装打包

npx webpack // 局部安装打包

"scripts": {
	"build": "webpack"
}
npm run build // 更改package.json的脚本命令，用脚本命令打包

npx webpack --entry ./src/main.js --output-path ./dist 
// 指定打包入口文件出口文件
```

## webpack配置

在项目根目录下新建配置文件：`webpack.config.js`

Webpack 是基于 Node.js 运行的，所以导入导出采用 Common.js 模块化规范

```javascript
// Node.js的核心模块，专门用来处理文件路径
const path = require("path");

module.exports = {
  // 入口
  // 相对路径和绝对路径都行
  entry: "./src/main.js",
  // 输出
  output: {
    // path: 文件输出目录，必须是绝对路径
    // path.resolve()方法返回一个绝对路径
    // __dirname 当前文件的文件夹绝对路径
    path: path.resolve(__dirname, "dist"),
    // filename: 输出文件名
    filename: "main.js",
  },
  // 加载器
  module: {
    rules: [
        {
            test: /\.css$/,	// 正则表达式
        //  loader: "css-loader" // css-loader只负责解析css不会插入html还需要style-loader
            use: [
                "style-loader",
                "css-loader"
            ] // 从下往上执行
        }
    ],
  },
  // 插件
  plugins: [],
  // 模式
  mode: "development", // 开发模式
};

```

此时功能和之前一样，也不能处理样式资源



# Vue cli脚手架

## 什么是Vue CLI

- 如果你只是简单写几个Vue的Demo程序, 那么你不需要Vue CLI.
- 如果你在开发大型项目, 那么你需要, 并且必然需要使用Vue CLI
  - 使用Vue.js开发大型应用时，我们需要考虑代码目录结构、项目结构和部署、热加载、代码单元测试等事情。
  - 如果每个项目都要手动完成这些工作，那无疑效率比较低效，所以通常我们会使用一些脚手架工具来帮助完成这些事情。
- CLI是什么意思?
  - CLI是Command-Line Interface, 翻译为**命令行界面,** 但是**俗称脚手架.**
  - Vue CLI是一个官方发布 vue.js 项目脚手架
  - **使用 vue-cli 可以快速搭建Vue开发环境以及对应的webpack配置.** 



## Vue cli安装

1. ##### 安装NodeJS

2. ##### 安装npm

   ##### cnpm安装

   - 由于国内直接使用 npm 的官方镜像是非常慢的，这里推荐使用淘宝 NPM 镜像。

   - 你可以使用淘宝定制的 cnpm (gzip 压缩支持) 命令行工具代替默认的 npm:

   - ```
     npm install -g cnpm --registry=https://registry.npm.taobao.org
     ```

3. ##### webpack安装

   ```
   npm install webpack -g
   ```

4. ##### 安装Vue脚手架

```
npm install -g @vue/cli
//注意:上面安装的是Vue CLI3的版本，如果需要想按照Vue CLI2的方式初始化项目时不可以的
```

5. ##### 初始化项目

```
vue create 项目名称
// 项目名称不能有大写字母
```

6. ##### 运行项目

```
npm run dev //vuecli2的方法
npm run serve //vuecli3的方法
npm run build //打包编译项目
npm install //安装依赖
```

## 可视化ui配置

```
命令行输入vue ui接着导入项目
或者cd切换到项目路径再命令行vue ui
```

## 更改serve配置

```
----package.json-----

"scripts": {
    "serve": "vue-cli-service serve --open --port 3000",
    "build": "vue-cli-service build"
  },
  
--open //浏览器自动打开vue页面
--port 3000 // 更改vue页面端口号
```



# Vue-router路由

## URL的hash和history

- URL的hash
  - URL的hash也就是锚点(#), 本质上是改变window.location的href属性.
  - 我们可以通过直接赋值location.hash来改变href, 但是页面不发生刷新 

HTML5的history模式：pushState

- history接口是HTML5新增的, 它有五种模式改变URL而不刷新页面.
- history.**pushState**() 

HTML5的history模式：**replaceState** 

-  history.**replaceState**()

HTML5的history模式：go

- history.**go**()

```javascript
//vue-cli4在创建项目时选择安装vue-router会让你选择hash或者history模式，如果默认是hash想改为路径不带'/#/'的history，在router的index.js中修改
const router = new VueRouter({
  mode: 'history',
  routes
})
```



## 基本使用

### 安装配置

1. 安装vue-router 

```javascript
npm install vue-router --save
//现在vue-cli4已经可以在创建项目时选择安装router
```

2. 配置vue-router

```javascript
//现在通过vue-cli4安装的router会自动配置好了
import Vue from 'vue'
import VueRouter from 'vue-router'  //导入路由
import Home from '../components/Home' //导入路由组件

Vue.use(VueRouter)  //安装路由

const routes = [    //定义路由，配置映射
  {
    path: '/home',
    name: 'home',
    component: Home
  }
]

const router = new VueRouter({  //创建路由实例
  mode: 'history',
  base: process.env.BASE_URL,
  routes
})

export default router  //导出路由实例
```

3. 在main.js中的Vue实例中挂载导入的路由实例



### 使用66

1. 创建路由组件
2. 导出路由组件
3. router的index.js导入路由组件
4. 配置路由映射: 组件和路径映射关系
5. App.vue使用路由: 通过<router-link>和<router-view>



### 路由的默认路径

- 我们这里还有一个不太好的实现:
  - 默认情况下, 进入网站的首页, 我们希望<router-view>渲染首页的内容.
  - 但是我们的实现中, 默认没有显示首页组件, 必须让用户点击才可以. 
- 如何可以让**路径默认跳到到首页**, 并且<router-view>渲染首页组件呢?
  - 非常简单, 我们只需要多配置一个映射就可以了. 

```javascript
const routes = [    //定义路由，配置映射
  {
    path: '/',
    redirect:'/home'  //重定向
  },{
    path: '/home',
    component: Home
  },{
    path: '/about',
    component: About
  }
]
```

### router-link补充

- **tag**: tag可以指定<router-link>之后渲染成什么组件, 比如代码tag=" li "会被渲染成一个<li>元素, 而不是<a>

- **replace**: replace不会留下history记录, 所以指定replace的情况下, 后退键返回不能返回到上一个页面中

- **active-class**

  : 当<router-link>对应的路由匹配成功时, 会自动给当前元素设置一个router-link-active的class, 可以直接修改该类样式；设置active-class="active"可以修改默认的名称.

  - 在进行高亮显示的导航菜单或者底部tabbar时, 会使用到该类.
  - 但是通常不会修改类的属性, 会直接使用默认的router-link-active即可.
  - 被点击添加class也可以通过router实例的linkActiveClass:'active'属性进行修改

- this.$router.push('/home')相当于用history.pushState，这种路由跳转带有返回方法，适用于跳转可回退页面的详情页。

- this.$router.replace('/home')相当于history.replaceState，这种路由跳转没有返回方法，适用于不需要回退页面的主页面切换。



## 动态路由

当我们想要路由的路径后面跟一些参数时，这种path和Component的匹配关系，我们称之为动态路由(也是路由传递数据的一种方式

```javascript
// 路由：/detail?id=111
this.$router.push({
    path:'/detail',
    query:{
        id,
        ...
    }
}) 

// 路由:/detail/id
this.$router.push('/detail/' + this.goodItem.id)  
```

1. 创建一个User.vue组件

```javascript
<template>
  <div>
    <h2>我是用户界面</h2>
    <p>我是用户的相关信息, 嘿嘿嘿</p>
    <h2>计算属性userId:{{userId}}</h2>
    <h2>$route.params.id:{{$route.params.id}}</h2>
    <button @click="btnClick">按钮</button>
  </div>
</template>
 
<script>
  export default {
    name: "User",
    computed: {
      userId() {
        return this.$route.params.id  //获取路由传来的参数
      }
    },
    created() {
      console.log('User created');
    },
    destroyed() {
      console.log('User destroyed');
    },
    methods: {
      btnClick() {
        // 所有的组件都继承自Vue类的原型
        console.log(this.$router); // 是我们index.js创建的那个大的路由对象router
        console.log(this.$route); // 当前哪个路由处于活跃状态，获取到的就是哪个路由，即当前浏览器url中正在使用的路由
        console.log(this.name);
      }
    }
  }
</script>
<style scoped>
</style>
```

2. 配置router文件夹下index.js

```javascript
const routes = [
    {
        path:'/user/:id',  // 路由传的参数
        component:User
    }
]
const router = new VueRouter({
    routes,
    mode:'history',
    linkActiveClass:'active'
})
export default router
```

3. 配置App.vue

```javascript
<template>
  <div id="app">
    <h2>我是APP组件</h2>
    <router-link to="/home">首页</router-link>
    <router-link to="/about">关于</router-link>
    <!-- 动态路由 -->
    <!--<router-link :to="/user/yyy">用户</router-link>-->
    <!-- 动态拼接 使用v-bind -->
     <router-link :to="'/user/'+userId">用户</router-link>
	// 路由传参数userId
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      userId: "zhangsan"
    };
  },
};
</script>
```

## 路由懒加载

- 官方给出了解释:
  - 当打包构建应用时，Javascript 包会变得非常大，影响页面加载。
  - 如果我们能把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件，这样就更加高效了
- 官方在说什么呢?
  - 首先, 我们知道路由中通常会定义很多不同的页面.
  - 这个页面最后被打包在哪里呢? 一般情况下, 是放在一个js文件中.
  - 但是, 页面这么多放在一个js文件中, 必然会造成这个页面非常的大.
  - 如果我们一次性从服务器请求下来这个页面, 可能需要花费一定的时间, 甚至用户的电脑上还出现了短暂空白的情况.
  - 如何避免这种情况呢? 使用路由懒加载就可以了.
- 路由懒加载做了什么?
  - 路由懒加载的主要作用就是将路由对应的组件打包成一个个的js代码块.
  - 只有在这个路由被访问到的时候, 才加载对应的组件

```javascript
//第一张写法
const Home = () => import('../components/Home.vue')
const routes = [
    {
        path:'/home',
        component:Home
    }
]

//第二种写法
const routes = [
    {
        path:'/home',
        component:() => import('../components/Home.vue')
    }
]
```

## 嵌套路由

- 嵌套路由是一个很常见的功能
  - 比如在home页面中, 我们希望通过/home/news和/home/message访问一些内容.
  - 一个路径映射一个组件, 访问这两个路径也会分别渲染两个组件.

- 实现嵌套路由有两个步骤:

  1. 创建对应的子组件, 并且在路由映射中配置对应的子路由.

  ```javascript
  const Message = () => import('../components/HomeMessage.vue')
  const News = () => import('../components/HomeNews.vue')
  const routes = [
      {
          path:'/home',
          component:Home,
          children:[
              {
                  path:'message',
                  component:Message
              },
              {
                  path:'news',
                  component:News
              }
          ]
      }
  ]
  ```

  2. 在父组件内部使用<router-link><router-view>标签. 

```html
<router-link to="/home/message">消息</router-link>
<router-link to="/home/news">新闻</router-link>
<router-view></router-view
```



## 参数传递

### params

**params的类型:**

- 配置路由格式: /router/:id
- 适用情况：少量参数
- 传递的方式: 在path后面跟上对应的值
- 传递后形成的路径: /router/123, /router/abc



### query

**query的类型:**

- 配置路由格式: /router, 也就是普通配置
- 适用情况：大量参数
- 传递的方式: 对象中使用query的key作为传递方式
- 传递后形成的路径: /router?id=123, /router?id=abc&name=why

```html
<router-link :to="{path:'/profile',query:{name:'why',age:18,height:1.85}}">消息</router-link>

const routes = [
    {
        path:'/profile',
        component:() => import('../components/profile.vue')
    }
]

//获取参数方法
this.$route.query.name
{{$route.query.age}}
```



## 导航守卫|生命周期

> “导航”表示路由正在发生改变。
>
> 正如其名，`vue-router` 提供的导航守卫主要用来通过跳转或取消的方式守卫导航。有多种机会植入路由导航过程中：全局的, 单个路由独享的, 或者组件级的.
>
> `vue-router`提供的导航守卫主要用来监听监听路由的进入和离开的.
>
> `vue-router`提供了`beforeEach`和`afterEach`的钩子函数, 它们会在路由即将改变前和改变后触发. 



### 基本使用

麻烦不利于维护的方法:在每个组件的js里写方法

```javascript
<script>
export default {
  name: "App",
  created(){
      document.title='首页' //生命周期函数
  },
  mounted: (){ 
      document.title='首页' //生命周期函数
  } 
  destroyed(){} //生命周期组件销毁函数
};
</script>
```

### create和mounted区别

**原理解释**：

1. created 在模板渲染成html前调用，即通常初始化某些属性值，然后再渲染成视图
2. mounted在模板渲染成html后调用，通常是初始化页面完成后，再对html的dom节点进行一些需要的操作。

**过程解释**：

可以看到第一个报了错，实际是因为找不到id getElementById(ID) 并没有找到元素，原因如下：

在created的时候，视图中的html并没有渲染出来，所以此时如果直接去操作html的dom节点，一定找不到相关的元素；mounted由于此时html已经渲染出来了，所以可以直接操作dom节点，故输出了结果“Geooo”。

**生命周期补充**：创建前后、载入前后、更新前后、销毁前后



### 全局导航守卫

**导航钩子的三个参数解析:**

- to: 即将要进入的目标的路由对象.
- from: 当前导航即将要离开的路由对象.
- next: 调用该方法后, 才能进入下一个钩子.

补充一：如果是后置钩子, 也就是afterEach, 不需要主动调用next()函数.

补充二：全局守卫（beforeEach、afterEach)

- 路由独享的守卫（beforeEnter）.
- 组件内的守卫（beforeRouterEnter、beforeRouterUpdate、beforeRouterLeave）. 

```javascript
//router下的index.js
const routes = [
  {
    path: '',
    // redirect重定向
    redirect: '/home'
  },
  {
    path: '/home',
    component: Home,
    // meta元数据（描述数据的数据）
    meta: {
      title: '首页'
    }},
    {
        path: 'message',
        component: HomeMessage,
        meta: {
          title: '消息'
      }}
    ]
const router = new VueRouter({
       // ...
})

router.beforeEach((to,from,next) =>
{ document.title = to.meta.title  //to:要去的路由 from:当前的路由 next:下一步执行的函数
next() //正常跳转
next('/') //回到默认页面
})

export default router
```

### 路由独享的守卫

```javascript
const routes = [
    // ...
      {
    path: '/about',
    component: About,
    meta: {
      title: '关于'
    },
    // 2.路由独享的守卫
    beforeEnter: (to, from, next) => {
      console.log('about beforeEnter');
      next()
    }
  },
    // ...
]
 
const router = new VueRouter({
//...
})
```

### 组件内守卫

```javascript
// Home.vue
<template>
  <div>
    <h2>我是首页</h2>
    <p>我是首页内容, 哈哈哈</p>
 
    <router-link to="/home/news">新闻</router-link>
    <router-link to="/home/message">消息</router-link>
 
    <router-view></router-view>
 
    <h2>{{message}}</h2>
  </div>
</template> 
<script>
export default {
  name: "Home",
  data() {
    return {
      name: "yyy",
    };
  },
  beforeRouteEnter(to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
    // console.log(to);
    // console.log(from);
    console.log(this); // undefined
    next((vm) => { // 通过传一个回调给 next来访问组件实例
      console.log(vm.name);
    });
  },
  beforeRouteUpdate(to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
    console.log("beforeRouteUpdate");
    next();
  },
  beforeRouteLeave(to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
    console.log(this.$route.path);
    next();
  },
};
</script>
<style scoped>
</style>
```



## keep-alive

- keep-alive 是 Vue 内置的一个组件，可以使被包含的组件保留状态，或避免重新渲染。
  - 它们有两个非常重要的属性:
  - **include** - 字符串或正则表达，只有匹配的组件会被缓存
  - **exclude** - 字符串或正则表达式，任何匹配的组件都不会被缓存 
- router-view 也是一个组件，如果直接被包在 keep-alive 里面，所有路径匹配到的视图组件都会被缓存： 



# Vuex状态管理

## vuex详解

- 官方解释：Vuex 是一个专为 Vue.js 应用程序开发的

  状态管理模式

  - 它采用 **集中式存储管理** 应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化，做到响应式。
  - Vuex 也集成到 Vue 的官方调试工具 **devtools extension**，提供了诸如零配置的 time-travel 调试、状态快照导入导出等高级调试功能。 

**状态管理**到底是什么？

- **状态管理模式、集中式存储管理**这些名词听起来就非常高大上，让人捉摸不透。
- 其实，你可以简单的将其看成把需要多个组件共享的变量全部存储在一个对象里面。
- 然后，将这个对象放在顶层的Vue实例中，让其他组件可以使用。
- 那么，多个组件是不是就可以共享这个对象中的所有变量属性了呢？

## State

State**单一状态树**

- 如果你的状态信息是保存到多个Store对象中的，那么之后的管理和维护等等都会变得特别困难。
- 所以Vuex也使用了单一状态树来管理应用层级的全部状态。
- 单一状态树能够让我们最直接的方式找到某个状态的片段，而且在之后的维护和调试过程中，也可以非常方便的管理和维护。 
- 相当于组件中的`data`

```javascript
//Store下的index.js文件
const store = new Vuex.Store({
  state: {
    counter: 1000,
    students: [{
        id: 110,
        name: 'why',
        age: 18
      },
      {
        id: 111,
        name: 'kobe',
        age: 24
      }
    ]
})

//调用方法：在App.vue或其它组件{{$store.state.counter}}
```

## Getters

- 需要从store中获取一些state变异后的状态
- 相当于组件中的`computed`

```javascript
//Store下的index.js文件
const store = new Vuex.Store({
  state: { 
    counter: 1000,
    students: [{
        id: 110,
        name: 'why',
        age: 18
      }
    ]
  },
  getters: { // 可以认为是 store 的计算属性
    // getters里面的方法 也会有state参数
    powerCounter(state) {
      return state.counter * state.counter
    },
    // 找出多于20岁的学生 
    // filter(回调函数(当前元素的值))
    more20stu(state) {
      return state.students.filter(s => s.age > 20)
    },
  }
})

//调用方法：在App.vue或其它组件{{$store.getters.more20stu}}
```

**Getters作为参数**

```javascript
const store = new Vuex.Store({
  state: { 
    counter: 1000,
    students: [{
        id: 110,
        name: 'why',
        age: 18
      }
    ]
  },
  getters: { 
    more20stu(state) {
      return state.students.filter(s => s.age > 20)
    },
    more20stuLength(state, getters) { // Getters 也可以接受其它getters 作为第二个参数
      return getters.more20stu.length
    }
  }
})
```

**Getters传递参数**

```javascript
//store下index.js代码：
getter:{
    // 找出年龄大于参数age的学生
    moreAgeStu(state) { // getters传递参数 只能让getters本身返回另一个函数.
      return age => {
        return state.students.filter(s => s.age > age)
      }
  }
} 

//App.vue
<h2>{{ $store.getters.moreAgeStu(12) }}</h2>
```

## Mutation

-  `Mutations`唯一的目的就是修改`state`中的状态：提交`Mutation`(所以在其它地方如`action`不要直接修改`state`，转移到`Mutation`里来修改`state`)
-  `Mutation`干的事情尽量专一，一个方法只实现一件事情
- `Mutation`主要包括两部分：
  - 字符串的**事件类型（type）**
  - 一个**回调函数（handler）**,该回调函数的第一个参数就是state。
-  只能用于**同步操作**
- 相当于组件的`methods`

```javascript
//Store下的index.js文件
const store = new Vuex.Store({
  state: { 
      count:100
  },
  getters: {
  },
  mutation: {
      increment(state){
          state.count++
      }
  }
})
//调用方法：在App.vue或其它组件methods定义方法
methods: {
    increment:() => {
        this.$store.commit('increment')
    }
}
```

**Mutation传递参数**

```javascript
//在通过mutation更新数据的时候, 有可能我们希望携带一些额外的参数,参数被称为是mutation的载荷(Payload)

//Store下的index.js文件
  mutations: { 
    //参数是变量
     incrementCount(state, count) {
        state.counter +=count
     }, 
    // 参数是对象
    addStudent(state, stu) {
      state.students.push(stu)
    },
     // 特殊的提交封装
    incrementCount(state, payload) {
      state.counter += payload.count
    }
  }

//调用方法：在App.vue或其它组件methods定义方法
<button @click="addCount(5)">+5</button>
 
methods: {
     addCount(count) {
      // 1.普通的提交封装 这样写的 mutations里的 incrementCount(state, count) 的count就是count
         this.$store.commit('incrementCount', count) // 单个参数
    },
     addStudent() {
      //2.提交对象
      const stu = { id: 114, name: "alan", age: 35 };
      this.$store.commit("addStudent", stu);
   },
     addCount(count) {
      this.$store.commit({
        type: "incrementCount",
        count,
      });
}
```

## Action

-  我们强调, 不要再Mutation中进行异步操作.
   - 但是某些情况, 我们确实希望在Vuex中进行一些异步操作, 比如网络请求, 必然是异步的.这个时候怎么处理呢?
   - Action类似于Mutation, 但是是用来代替Mutation进行异步操作的.
-  只能用于**异步操作**
-  相当于组件的`methods`

```javascript
//Store下的index.js文件
const store = new Vuex.Store({
    mutations:{
         updateInfo(state) {
              state.info.name = 'coderwhy'
        }
    },
    actions:{
          // 默认参数 context: 上下文 现在先理解成store
         aUpdateInfo(context, payload) {
              setTimeout(() => {
                context.commit('updateInfo')
                console.log(payload.message);
                payload.success() // 调用回调 告诉外面已经成功
              }, 1000)
        }
    }
})

//调用方法：在App.vue或其它组件methods定义方法
methods: {
    updateInfo() {
      // this.$store.commit('updateInfo')  // 这样没有经过actions
      // 异步修改信息 actions
      // this.$store.dispatch('aUpdateInfo',"我是携带的信息");// 携带一个参数
      // 通知外面已经改成功了 -> commit调用之后就算成功，利用对象的方法回调
      this.$store.dispatch('aUpdateInfo', {
        message: '我是携带的信息',
        success: () => {
          console.log('里面已经完成了');
        }
      })  
    }
}
```

**Action返回的Promise**

```javascript
//Store下的index.js文件
actions:{
     // Action返回的Promise
    aUpdateInfo(context, payload) {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          context.commit('updateInfo');
          console.log(payload);
          resolve('1111111')
        }, 1000)
      }) // then()在App.vue里面写
    }
}
 
//调用方法：在App.vue或其它组件methods定义方法
// ...
 updateInfo() {
      // 之前写的不够优雅 回调消息与携带信息混在一起，现在用Promise封装再用resolve调用
      this.$store.dispatch("aUpdateInfo", "我是携带的信息").then((res) => {
        console.log("里面完成了提交");
        console.log(res);
      });
    }
```

## Module

- Module是模块,单元的意思, 为什么在Vuex中我们要使用模块呢?
  - Vue使用单一状态树,那么也意味着很多状态都会交给Vuex来管理.
  - 当应用变得非常复杂时,store对象就有可能变得相当臃肿.
  - 为了解决这个问题, Vuex允许我们将store分割成模块(Module), 而每个模块拥有自己的state、mutations、actions、getters等

```javascript
const moduleA = {
    state:{...},
    mutations:{...},
    actions:{...},
    getters:{...}
}
const moduleB = {
    state:{...},
    mutations:{...},
    actions:{...},
    getters:{...}
}

const store = new Vuex.Store({
    state:{...},
    mutations:{...},
    actions:{...},
    getters:{...},
    modules:{
        a:moduleA,
        b:moduleB
    }
})
```

**获取方式**

```javascript
const moduleA = {
  // 组件里面通过$store.state.a.name获取属性
  state: {
    name: 'zhangsan'
  },
    
 // 使用 this.$store.commit()直接提交，不用区分模块，模块和实例里共用，名字不能一样
  mutations: {
    updateName(state, payload) {
      state.name = payload
    }
  },
    
  // $store.getters.fullname直接调用，不用区分模块
  getters: {
    fullname(state) {
      return state.name + '11111'
    },
    fullname2(state, getters) { // 使用其他getters
      return getters.fullname + '2222'
    },
    // rootState 可以使用大的state里面的值
    fullname3(state, getters, rootState) {
      return getters.fullname2 + rootState.counter
    }
  },
    
  // this.$store.dispatch() 提交
  actions: {
    // 这里的 context 不是store对象了 而是这个module对应的mutations
    aUpdateName(context) {
      console.log(context);
      setTimeout(() => {
        context.commit('updateName', 'wangwu') //只能提交这个模块的mutation
      }, 1000)
    }
  }
}
```



# 网络封装axios

## 跨域

> 1. 前端写代码常用DevServer正向代理，代理浏览器，仅限开发阶段(开发环境)，vue cli自带可以通过配置vue.config.js的代理服务器来解决跨域
> 2. 项目上线后，使用Nginx反向代理，代理服务器，后端人员编写（生产环境）

## axios详解

- 功能特点:
  - 在浏览器中发送 XMLHttpRequests 请求
  - 在 node.js 中发送 http请求
  - 支持 Promise API
  - 拦截请求和响应
  - 转换请求和响应数据
  - 比xhr(new XMLHttpRequests() xhr.open() xhr.send())方便，jQuery($.get $.post)体积小
  - 等等 

## axios基本使用

### 安装axios

```javascript
npm install axios --save
```

### axios发送请求

```javascript
import axios from 'axios'
 
// 1.axios的基本使用 axios(config)
axios({
  url: 'http://123.207.32.32:8000/home/multidata', // 项目接口
  // 默认是get请求 可以用method指定
  // method: 'post'
}).then(res => { // 获取返回的数据
  console.log(res);
})
 
 
axios({
  //  url:'http://123.207.32.32:8000/home/data?type=sell&page=1'
  // 参数除了可以直接拼接在url，也可以用params
  url: 'http://123.207.32.32:8000/home/data',
  // 专门针对get请求的参数拼接
  params: {
    type: 'pop',
    page: 1
  }
}).then(res => {
  console.log(res);
})
```

### axios.all发送并发请求

```javascript
 
// 2.axios发送并发请求(同时发多个请求,同时到达之后才能往下写代码)
// axios.all([axios(),axios()]).then(reslut=>{})
axios.all([axios({
  url: 'http://123.207.32.32:8000/home/multidata'
}), axios({
  url: 'http://123.207.32.32:8000/home/data',
  params: {
    type: 'sell',
    page: 5
  }
})]).then(results => {
  console.log(results);
  console.log(results[0]);
  console.log(results[1]);
})
 
// .then()也可以写成
axios.all([axios({
  url: 'http://123.207.32.32:8000/home/multidata'
}), axios({
  url: 'http://123.207.32.32:8000/home/data',
  params: {
    type: 'sell',
    page: 5
  }
})]).then(axios.spread((res1, res2) => {
  console.log(res1);
  console.log(res2);
}))
```

### 全局配置

```javascript
 // 使用全局的axios和对应的配置在进行网络请求
axios.defaults.baseURL = 'http://123.207.32.32:8000'
axios.defaults.timeout = 5000 // 超时
 
axios.all([axios({
  url: '/home/multidata'
}), axios({
  url: '/home/data',
  params: {
    type: 'sell',
    page: 5
  }
})]).then(axios.spread((res1, res2) => {
  // 使用 axios.spread 可将数组 [res1,res2] 展开为 res1, res2
  console.log(res1);
  console.log(res2);
}))
```

## axios封装

- 这种开发思路不好，每个组件对第三方框架依赖太强了，假如这个框架不再维护，或者有bug，更换框架得一个个文件查找修改
- 要有这种开发意识：**只要引用第三方的东西，千万不要在多个组件对它有依赖**
- 那要怎么做？
  - 单独建一个文件，对它进行**封装**

```javascript
// request.js
 
// request封装v4.0 直接return instance(config) 因为它这个本身就是一个Promise
export function request(config) {
    // 1.创建axios的实例
    const instance = axios.create({
        baseURL: 'http://123.207.32.32:8000',
        timeout: 5000
    })
    // 2.发送真正的网络请求
    return instance(config) // instance本身的返回值就是个promise
 
}
```

```javascript
// main.js
 
//  调用request()，里面的{}就是参数，return返回什么相当于外面调用的方法拿到什么，即request({...})=instance({...})=promise({...})
request({
  url: '/home/multidata'
}).then(res => {
  console.log(res);
}).catch(err => {
  // console.log(err);
})
```

## axios拦截器

```javascript
// request.js
 
export function request(config) {
  // 1.创建axios的实例
  const instance = axios.create({
    baseURL: 'http://123.207.32.32:8000',
    timeout: 5000
  })
 
  // 2.axios的拦截器
  // 2.1.请求拦截的作用 成功/失败
  instance.interceptors.request.use(config => {
    // console.log(config);
    // 1.比如config中的一些信息不符合服务器的要求
 
    // 2.比如每次发送网络请求时, 都希望在界面中显示一个请求的图标
 
    // 3.某些网络请求(比如登录(token)), 必须携带一些特殊的信息
    // 得把config再返回
    return config
  }, err => {
    // console.log(err);
  })
 
  // 2.2.响应拦截 成功/失败
  instance.interceptors.response.use(res => {
    // console.log(res);
    return res.data
  }, err => {
    console.log(err);
  })
 
  // 3.发送真正的网络请求
  return instance(config) // 本身的返回值就是个promise
}
```



# Git管理



#### 第一次使用配置git

1. 第一次使用git的时候我们需要给git配置用户名和邮箱，用户和邮箱可以使用github的，也可以使用自己公司的git lab仓库的账号

```
# 配置用户名
git config --global user.name "用户名"
# 配置邮箱
git config --global user.email "邮箱地址"
```

2. 配置好这个以后我们输入便可以看到我们所有的配置信息了，然后可以看到user.name和user.email配置得对不对

```
git config -l
```

3. 另外，当我们配置好邮箱和用户名之外，还需要配置一下ssh密钥（当然不配置也行，就是每次pull、push代码的时候都要输入密码，比较麻烦，所以还是一劳永逸配置一下ssh密钥方便以后开发）

4. ```
   查看当前所处的状态
   git status
   ```

5. ```
   查看所有git记录
   git log
   ```




#### 在远端拉取项目本地提交

1. 在GitHub创建好仓库
2. 复制仓库链接在本地终端克隆仓库

```
git clone 链接 
//clone是将已经创建好的git仓库克隆到本地，所以不用git init
```

3. 创建分支：首先拉取项目下来之后，我们就要创建自己的开发分支，以后提交代码都主要在自己的开发分支上进行提交

```
//首先我们可以先查看一下目前所有的分支
git branch -a

//然后可以创建自己的分支
git branch 分支名称

//切换到自己创建的分支
git checkout 需要切换到的分支名称

//上述两个指令可以合并成下面的这一个指令，创建并切换到分支
git checkout -b 分支名称

//创建完分支并切换之后，查看自己当前分支
git branch
```

4. 将本地原有的项目文件移动到克隆的仓库里(node_moudles依赖，dist最好不要移进来)

5. 把本地未同步远端仓库的文件都加入暂存区

```
git add .
```

6. 将暂存区文件提交到本地仓库

```
git commit -m "提交备注"
//一般提交代码的时候我们都要写上注释，而且写注释也有一定的规范，然后这个规范跟自己的公司有关，按照自己公司的规范来提交就行了，另外一般我们使用的提交规范有比如说像下面这样：
feat：增加新功能

fix：修复bug

docs：只改动文档

style：格式（不影响代码运行的改动）

refactor：重构

test：增加测试

git commit -m "feat: 完成了订单下载模块"
```

7. 将本地仓库推送到远端仓库

```
git push origin main //上传本地当前分支代码到远程main分支。
git push //上传本地所有分支代码到远程所有的分支上。
```



#### 在远端创建好仓库本地提交

1. 将本地项目文件初始化，建立一个git代码仓库

```
git init
```

2. 拉取远程仓库代码并与本地分支合并

```
//一般可以看作是git fetch和git merge的结合体
git pull

//获取远程仓库的变动
//一般是将远程代码获取下来，然后去和git merge做合并处理
git fetch origin 分支名称(比如说主分支origin master)

//将代码进行合并
git merge 分支名
```

3. 把本地未同步远端仓库的文件都加入暂存区

```
git add .
```

4. 将暂存区文件提交到本地仓库

```
git commit -m "提交备注"
```

5. 将本地仓库添加到远端仓库

```
git remote add origin 链接
```

6. 将本地库存推送到远端仓库

```
git push -u origin main
```

#### 提PR(Pull Request)

1. 首先把你要提交 PR 的项目`Fork`（拷贝当时的项目文件到自己仓库，如果后续原项目文件发生改变，你必须通过其他的方式去同步）到自己的仓库。

2. 然后到自己的仓库中，将`Fork`的项目 clone 到本地。

3. 添加远程分支

   ```
   # git remote add 远程仓库别名 远程仓库地址
   git remote add upstream https://github.com/开源项目.git
   ```

4. 拉取合并远程分支到master分支（这步需要随时做，保持你的master分支永远是官方最新成果，也可以在GitHub仓库页手动拉取同步代替命令行更新）

   ```
   # 拉取远程仓库的master分支/git fetch 远程仓库别名 分支名
   git fetch upstream master
   # 切换分支
   git checkout master
   # 合并远程分支/git merge 远程仓库别名/分支名
   git merge upstream/master
   ```

5. 创建并切换到自己的分支，分支名最好紧贴这次更新的内容

   ```
   git checkout -b feat/xxxx
   ```

6. 修改代码

7. 暂存修改的代码

   ```
   git add .
   ```

8. 提交你的修改：

   ```
   git commit -m 'feat(function): add xxxxx'
   ```

9. 将新的分支推送到远程上游

   ```
   git push --set-upstream origin feat/xxxx
   ```

10. 在自己的 GitHub 仓库中找到`Fork`的对应项目，打开 `Pull requests` Tab，点击 `New pull request` 按钮，新建一个 PR。

11. 然后，在下面的界面中，选择刚刚提交的分支，最后点击 `Create pull request` 即可。



#### 如何在新分支开发时保持主分支更新同步

当你在开发功能分支时，如果主分支更新了，你可以采取以下步骤来保持你的功能分支与最新的主分支同步：

1. 提交当前工作：在你切换到主分支之前，确保你已经提交或者存储了当前功能分支上的所有修改。

2. 切换到主分支：通过运行git checkout main命令，切换到主分支。

3. 拉取最新代码：运行git pull origin main命令，从远程仓库的主分支拉取最新的代码。

4. 切换回功能分支：运行git checkout <你的功能分支>命令，切换回你的功能分支。

5. 合并主分支变更：运行git merge main命令，将主分支的更新合并到你的功能分支中。这将把主分支的最新代码合并到你的功能分支上。

6. 解决冲突（如果有）：如果在合并主分支时出现冲突，你需要手动解决这些冲突。Git会提示你哪些文件发生了冲突，你需要打开这些文件并手动编辑以解决冲突。

7. 提交合并结果：在解决完所有冲突后，使用git add <文件>命令将解决冲突的文件标记为已解决。然后运行git commit命令提交合并的结果。

8. 现在你的功能分支应该包含主分支的最新更新。请注意，这只是一种处理主分支更新的方法，具体步骤可能因为你所使用的开发流程而有所不同。





# Nginx部署

### windows本地部署

1. npm run build打包项目文件(前提vue.config.js配置了生产环境路径)

   ```js
   const BASE_URL = process.env.NODE_ENV === "production" ? "./" : "/";
   module.exports = {
     publicPath: BASE_URL,
   }
   ```

2. 下载安装稳定版Nginx

3. 将打包后的文件放入html文件夹中，要删除原有index.html，放入打包后的index.html

4. 解压文件夹双击exe文件启动Nginx

5. 浏览器访问localhost访问页面

6. 可以在Nginx.config里更改文件位置，不一定要在html文件夹(更改配置文件必须在nginx目录cmd执行重启命令)

```nginx
nginx -s reload
```

### 遇到的坑

如果 vue 项目设置的是 history 路由模式，由于只有一个 `index.html` 主页面，所以每次访问非首页地址都会造成 404 。

比如我们访问 `http://domain.com/path` ，默认他就会去找 `path` 这个目录下的 `index.html` 文件，但是找不到这个文件，就会报 404 。

于是我们在 [nginx](https://so.csdn.net/so/search?q=nginx&spm=1001.2101.3001.7020) 下采用路径重写回 `index.html` ：

```nginx
# vue redirect
location / {
   try_files $uri $uri/ /index.html;
}
```



# Gitee Pages部署



### 遇到的坑

1. **修改 `vue.config.js` 配置中的`publicPath` 配置**：

```js
const BASE_URL = process.env.NODE_ENV === "production" ? "/yourProjName/" : "/";
module.exports = {
  publicPath: BASE_URL,
}
```

解释：当变量 `process.env.NODE_ENV` 为生产环境时，将 `BASE_URL` 设置为项目名称，这是因为使用 gitee pages 部署的应用，默认根路径为 `http://xxx.gitee.io/`，如果不修改 `publicPath` ，则你的应用中的资源请求都将会从根路径去找，结果就是出现 **404**，找不到你的资源，因为你的资源都存放在 `http://xxx.gitee.io/` 下面的项目目录中，比如你在 `gitee` 上的项目名称为 `myblog`，那么上面代码中的 `"/yourProjName/"` 就应为 `"/myblog/"`，如下：

```js
const BASE_URL = process.env.NODE_ENV === "production" ? "/supermall/" : "/";
```

2. 如果 vue 项目设置的是 history 路由模式，由于只有一个 `index.html` 主页面，所以每次访问非首页地址都会造成 404 。而在 [Github](https://so.csdn.net/so/search?q=Github&spm=1001.2101.3001.7020) pages 里不能这样做，只能借助 `404.html` ，当 Github pages 找不到页面时会自动寻找根目录下的 `404.html` 文件，那我们多拷贝一份与 `index.html` 相同的文件命名为 `404.html` 即可解决这个问题。





# Github Pages部署

## github pages基础用法

### 1. URL 规则

假设你的 github 帐号为 mygithub，需要发布的仓库名为 myrepo，那么 pages 的 URL 为：

https://mygithub.github.io/myrepo

### 2. 添加内容

用任意编辑器写好（或者生成）标准的网页内容，push 到 myrepo 即可。和大部分 web 服务一样，pages 会到指定目录里面寻找 index.html 作为网页入口。

### 3. 通过选择分支激活 pages

在仓库的设置界面，选择需要作为内容发布的分支，如下图：

![img](https://img-blog.csdnimg.cn/e93bffb172b2407ba6b214145becde47.png)

这里的分支选择、目录选择都是很巧妙的设计。主分支一般是放代码的，不会放 index.html 这样的网页内容。所以一个方案就是通过目录，把网页放到 docs 目录下。另外一个方案就是单独拉出一个分支来放网页内容，在这个分支下，可以把内容放到根目录。分支设计对下面要讲的通过 action 发布至关重要。

那为啥不用两个仓库？原因在于：

1. 因为你的酷毙了的开源项目已经有很多 star 了

2. 你得想两个名字

3. 增加了维护成本



## 通过action发布

这里以`vitepress`为例，你也可以选择任何工具。

在项目的根目录下创建`.github/workflows/deploy.yml`

其中`.github/workflows` 这两个目录的名字不能修改，`deploy.yml`文件则可以为任意的名字（后缀只能为 yml）。`deploy.yml` 的内容如下：

```yaml
name: Deploy

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v3
        with:
          node-version: 16
          cache: npm
      - run: npm install

      - name: Build
        run: npm run docs:build

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: docs/.vitepress/dist
```

这段代码的意思为：每当 main branch 有 push 的时候，在 ubuntu 机器上，通过搭建 node 环境，执行 vitepress 打包命令，并将 dist 下面的产出**提交**到 **gh-pages** 分支。若没有 gh-pages 分支，则**自动创建**该分支。

具体操作步骤如下：

1. 先不要管仓库里面的 pages 设置，也就是先不需要去激活 pages

2. 编写好 yml 文件，并提交到主分支。（由于这里的例子为 vitepress，所以仓库的内容需要为一个 vitepress 项目）

3. 等待 action 执行完成。如果成功，会发现仓库自动增加了一个新的分支，如下图：

![img](https://img-blog.csdnimg.cn/34e657a04a9844b3af03fcca0f2e084c.png)

其内容正是一个网页。

4. 去仓库设置里面，激活 pages。注意，这时分支要选 gh-pages，而不是 main。目录为 root。

![img](https://img-blog.csdnimg.cn/6a7a3fbc1ddf47b98c6318e94263d930.png)



5. pages 发布的时候，会花费点时间，请耐心等待约 1-5 分钟。

6. 通过打开 https://mygithub.github.io/myrepo 检查内容是否发布成功



## vitepress注意事项

1. 在 `docs/.vitepress/config.js`设置正确的`base`。

   如果你想部署到 `https://<USERNAME>.github.io/`, 你可以省略这一步，因为 `base` 默认为 `'/'`。

   如果你想部署到 `https://<USERNAME>.github.io/<REPO>/`, 比如你的仓库是 `https://github.com/<USERNAME>/<REPO>`, 那么需要设置`base` 为 `'/<REPO>/'`。
