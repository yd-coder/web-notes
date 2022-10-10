# SEO方案

> 搜索引擎优化

1. 多页面
2. title，描述，关键词
3. 网站内容怎么来的？

解决方案一：预渲染

- 使用插件：`prerender-spa-plugin`

  1. vue项目中安装`prerender-spa-plugin`

     ```
     npm install prerender-spa-plugin -S
     ```

  2. vue.config.js进行配置

- 使用插件修改title描述关键词：`vue-meta-info`

  1. 下载

     ```
     npm install vue-meta-info -S
     ```

  2. 到页面组件中配置

     ```
     metaInfo: {
     title: '',
     meta: [{
     	name: '',
     	content: ''
     }]
     }
     ```

可以解决：

1. 打包多页面
2. 可以解决每个页面单独生成title描述关键词 [vue-meta-info]
3. 接口数据是在html生成之前就放在页面上，爬虫可以抓取到内容

存在的问题：

1. 预渲染无法配置动态路由
2. 如果title描述关键词来自于接口数据，配置到meta-info也不行

适合做什么项目：一个项目可能某几个页面需要做seo



解决方案二：SSR服务端渲染





# Nuxt安装

```lua
npm i create-nuxt-app -g
create-nuxt-app my-nuxt-demo
cd my-nuxt-demo
npm run dev
复制代码
```

安装向导：

```less
Project name                                //  项目名称
Project description                         //  项目描述
Use a custom server framework               //  选择服务器框架
Choose features to install                  //  选择安装的特性
Use a custom UI framework                   //  选择UI框架
Use a custom test framework                 //  测试框架
Choose rendering mode                       //  渲染模式
Universal                                   //  渲染所有连接页面
Single Page App                             //  只渲染当前页面
复制代码
```



# Nuxt生命周期

一个完整的服务器请求到渲染的流程



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/4/30/16a6db58678e3bbe~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



通过上面的流程图可以看出，当一个客户端请求进入的时候，服务端有通过`nuxtServerInit`这个命令执行在`Store`的`action`中，在这里接收到客户端请求的时候，可以将一些客户端信息存储到`Store`中，也就是说可以把在服务端存储的一些客户端的一些登录信息存储到`Store`中。之后使用了`中间件`机制，中间件其实就是一个函数，会在每个路由执行之前去执行，在这里可以做很多事情，或者说可以理解为是路由器的拦截器的作用。然后再`validate`执行的时候对客户端携带的参数进行校验，在`asyncData`与`fetch`进入正式的渲染周期，`asyncData`向服务端获取数据，把请求到的数据合并到`Vue`中的`data`中，



## 服务端生命周期

1. nuxtServerInit ( store, context ){} 

   参数1：vuex上下文

   参数2：nuxt上下文

2. middleware({store, route, redirect, params, query, req, res}){}

   类似于vue中的导航守卫，可以全局配置也可以页面配置

   - 全局配置（nuxt.config.js配置）

   ```js
   router:{
       middleware:'名称'
   }
   ```

   新建middleware目录 -> 名称.js

   ```js
   export default ({store, route, redirect, params, query, req, res}){
       
   }
   ```

   - 页面配置

   ```vue
   <script>
       export default{
   		middleware: 'auth'
   	}
   </script>
   ```

   新建middleware目录 -> auth.js

3. asyncData

   SSR需要在服务器端获取并渲染数据。Nuxt.js 添加了`asyncData`方法使得你能够在渲染组件之前异步获取数据。

   `asyncData`方法会在组件（**限于页面组件使用**）每次加载之前被调用。它可以在服务端或路由更新之前被调用。在这个方法被调用的时候，第一个参数被设定为当前页面的**上下文对象**，你可以利用 `asyncData`方法来获取数据并返回给当前组件。

   ```js
   export default {
     data() {
       return { 
           list: [] 
       }
     },
     async asyncData({$axios}) {
       let res = await $axios.get('xxxxx')  
       return { list: res }
     }
   }
   ```

   注意：由于`asyncData`方法是在组件 **初始化** 前被调用的，所以在方法内是没有办法通过 `this` 来引用组件的实例对象。

## 服务端和客户端共有

1. beforeCreate
2. created

## 客户端生命周期

1. beforeMount
2. mounted
3. beforeUpdate
4. updated
5. beforeDestroy
6. destroyed



# Nuxt目录结构

## 目录结构介绍

```java
└─my-nuxt-demo
  ├─.nuxt               // Nuxt自动生成，临时的用于编辑的文件，build
  ├─assets              // 用于组织未编译的静态资源如LESS、SASS或JavaScript,对于不需要通过 Webpack 处理的静态资源文件，可以放置在 static 目录中
  ├─components          // 用于自己编写的Vue组件，比如日历组件、分页组件
  ├─layouts             // 布局目录，用于组织应用的布局组件，不可更改⭐
  ├─middleware          // 用于存放中间件
  ├─node_modules
  ├─pages               // 用于组织应用的路由及视图,Nuxt.js根据该目录结构自动生成对应的路由配置，文件名不可更改⭐
  ├─plugins             // 用于组织那些需要在 根vue.js应用 实例化之前需要运行的 Javascript 插件。
  ├─static              // 用于存放应用的静态文件，此类文件不会被 Nuxt.js 调用 Webpack 进行构建编译处理。 服务器启动的时候，该目录下的文件会映射至应用的根路径 / 下。文件夹名不可更改。⭐
  └─store               // 用于组织应用的Vuex 状态管理。文件夹名不可更改。⭐
  ├─.editorconfig       // 开发工具格式配置
  ├─.eslintrc.js        // ESLint的配置文件，用于检查代码格式
  ├─.gitignore          // 配置git忽略文件
  ├─nuxt.config.js      // 用于组织Nuxt.js 应用的个性化配置，以便覆盖默认配置。文件名不可更改。⭐
  ├─package-lock.json   // npm自动生成，用于帮助package的统一设置的，yarn也有相同的操作
  ├─package.json        // npm 包管理配置文件
  ├─README.md
```

## 配置文件

```javascript
const pkg = require('./package')
module.exports = {
  mode: 'universal',    //  当前渲染使用模式
  head: {       //  页面head配置信息
    title: pkg.name,        //  title
    meta: [         //  meat
      { charset: 'utf-8' },
      { name: 'viewport', content: 'width=device-width, initial-scale=1' },
      { hid: 'description', name: 'description', content: pkg.description }
    ],
    link: [     //  favicon，若引用css不会进行打包处理
      { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }
    ]
  },
  loading: { color: '#fff' },   //  页面进度条
  css: [    //  全局css（会进行webpack打包处理）
    'element-ui/lib/theme-chalk/index.css'  
  ],
  plugins: [        //  插件
    '@/plugins/element-ui'
  ],
  modules: [        //  模块
    '@nuxtjs/axios',
  ],
  axios: {},
  build: {      //  打包
    transpile: [/^element-ui/],
    extend(config, ctx) {       //  webpack自定义配置
    }
  }
}
```

## Nuxt运行命令

```json
{
  "scripts": {
    //  开发环境
    "dev": "cross-env NODE_ENV=development nodemon server/index.js --watch server",
    //  打包
    "build": "nuxt build",
    //  在服务端运行
    "start": "cross-env NODE_ENV=production node server/index.js",
    //  生成静态页面
    "generate": "nuxt generate"
  }
}
```





# Nuxt常用配置项

## 1.配置IP和端口

开发中经常会遇到端口被占用或者指定IP的情况。我们需要在根目录下的package.json里对config项进行配置。比如现在我们想把IP配置成127.0.0.1，端口设置1000。

/package.json

```json
"config":{
    "nuxt":{
      "host":"127.0.0.1",
      "port":"1000"
    }
  },
```

配置好后，我们在终端中输入npm run dev，然后你会看到服务地址改为了127.0.0.1:1000.

## 2.配置CSS

> 全局css

在开发多页项目时，都会定义一个全局的CSS来初始化我们的页面渲染，比如把padding和margin设置成0，网上也有非常出名的开源css文件normailze.css。要定义这些配置，需要在nuxt.config.js里进行操作。

比如现在我们要把页面字体设置为红色，就可以在assets/css/common.css文件，然后把字体设置为红色。

/assets/css/common.css

```css
html{
    color:red;
}
```

/nuxt.config.js

```arduino
  css:['~assets/css/normailze.css'],
```

设置好后，在终端输入npm run dev 。然后你会发现字体已经变成了红色。

> nuxt的页面css默认是独立的，组件css不是独立的需要加scoped
>
> 注：如果要使用sass，less，需要安装loader

## 3.配置webpack的loader

在nuxt.config.js里是可以对webpack的基本配置进行覆盖的，比如现在我们要配置一个url-loader来进行小图片的64位打包。就可以在nuxt.config.js的build选项里进行配置,相关可参照此链接[www.cnblogs.com/ssh-007/p/7…](https://link.juejin.cn/?target=https%3A%2F%2Fwww.cnblogs.com%2Fssh-007%2Fp%2F7867954.html)

```js
build: {
    loaders:[
      {
        test:/\.(png|jpe?g|gif|svg)$/,
        loader:"url-loader",
        query:{
          limit:10000,
          name:'img/[name].[hash].[ext]'
        }
      }
    ],
    /*
    ** Run ESLint on save
    */
    extend (config, { isDev, isClient }) {
      if (isDev && isClient) {
        config.module.rules.push({
          enforce: 'pre',
          test: /\.(js|vue)$/,
          loader: 'eslint-loader',
          exclude: /(node_modules)/
        })
      }
    }
  }
```

## 4.配置head

如果页面没有单独配置head，则会使用nuxt.config.js文件中全局的head

页面配置：

```vue
<script>
  export default {
    head() {
      return {
        title: 'title',
        meta: [
          {
            hid: 'description',
            name: 'description',
            content: 'My custom description'
          }
        ]
      }
    }
  }
</script>
```

全局配置：

nuxt.config.js文件中,修改title为wfaceboss：

```js
 head: {
    title: 'wfaceboss',
    meta: [
      { charset: 'utf-8' },
      { name: 'viewport', content: 'width=device-width, initial-scale=1' },
      { hid: 'description', name: 'description', content: 'Nuxt.js project' }
    ],
    link: [
      { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }
    ]
  },
```

修改后重启服务，即运行 npm run dev,效果如下



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/4/30/16a6db583aa9f80d~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



## 5.配置plugins

> 需要全局引入的js文件在nuxt.config.js文件中的plugins配置，js文件放在plugins目录下

## 6.配置代理解决跨域

1. 安装

   ```
   npm install @nuxtjs/axios @nuxtjs/proxy -S
   ```

2. nuxt.config.js进行配置

   ```js
   modules: [
   	'@nuxtjs/axios',
   	'@nuxtjs/proxy'
   ],
   axios: {
   	proxy: true, // 是否可以跨域
   },
   proxy: {
   	'/api':{
   		target: 'http://localhost:4000',
   		pathRewrite:{
   			'^/api':''
   		}
   	}
   }
   ```

   ## 7.配置loading

   1. nuxtjsloading默认是开启的，也可以关闭

      nuxt.config.js中loading:false

      也可以更改样式

      ```
      loading: {
      	color: 'blue',
      	height: '5px'
      }
      ```

   2. 自定义loading组件

      loading: '@/components/loading.vue'







# Nuxt的路由配置和参数传递

Nuxt.js的路由并不复杂，它给我们进行了封装，让我们节省了很多配置环节。

## 1.基本路由

Nuxt.js 依据 `pages` 目录结构自动生成 vue-router 模块的路由配置。

假设 `pages` 的目录结构如下

```markdown
└─pages
    ├─index.vue
    └─user
      ├─index.vue
      ├─one.vue
```

那么，Nuxt.js 自动生成的路由配置如下：

```js
router: {
  routes: [
    {
      name: 'index',
      path: '/',
      component: 'pages/index.vue'
    },
    {
      name: 'user',
      path: '/user',
      component: 'pages/user/index.vue'
    },
    {
      name: 'user-one',
      path: '/user/one',
      component: 'pages/user/one.vue'
    }
  ]
}
```

## 2.页面跳转

1. 不要写成a标签，因为是重新获取一个新的页面，并不是SPA
2. `<nuxt-link to="/users"></nuxt-link>`
3. this.$router.push('/users')

## 3.动态路由

- 在 Nuxt.js 里面定义带参数的动态路由，需要创建对应的**以下划线作为前缀**的 Vue 文件 或 目录。

以下目录结构：

```bash
pages/
--| _slug/
-----| comments.vue
-----| index.vue
--| users/
-----| _id.vue
--| index.vue
```

Nuxt.js 生成对应的路由配置表为：

```js
router: {
  routes: [
    {
      name: 'index',
      path: '/',
      component: 'pages/index.vue'
    },
    {
      name: 'users-id',
      path: '/users/:id?',
      component: 'pages/users/_id.vue'
    },
    {
      name: 'slug',
      path: '/:slug',
      component: 'pages/_slug/index.vue'
    },
    {
      name: 'slug-comments',
      path: '/:slug/comments',
      component: 'pages/_slug/comments.vue'
    }
  ]
}
```

你会发现名称为 `users-id` 的路由路径带有 `:id?` 参数，表示该路由是可选的。如果你想将它设置为必选的路由，需要在 `users/_id` 目录内创建一个 `index.vue` 文件。

- 获取动态参数{{$route.params.id}}

## 4.跳转路由传递参数并且取值

路由经常需要传递参数，我们可以简单的使用params来进行传递参数，我们现在向新闻页面（news）传递个参数，然后在新闻页面进行简单的接收。

（1）使用nuxt传递参数

```vue
<template>
  <div>
    <ul>
      <li><nuxt-link :to="`informa/${item.newsCode}-${item.newsType}`"></li>
    </ul>
  </div>
</template>
复制代码
```

注意：name其实指向的是路由（文件夹或文件名），而路由死活区分大小写的 ， 所以to后面区分大小写！！！建议文件夹都写成小写的。



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/4/30/16a6db5841bd84bb~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



（2）使用nuxt接收参数

```csharp
async asyncData(context) {
    let newsCode = context.route.params.code.split('-')[0]
    let newsType = context.route.params.code.split('-')[1]
},
复制代码
```

（3）使用this.$router.push的params传递参数

```php
传递参数  -- this.$router.push({path: ' 路由 ', query: {key: value}})
参数取值  -- this.$route.query.key

注: 使用这种方式，传递参数会拼接在路由后面，出现在地址栏
复制代码
```

（4）使用this.$router.push的params传递参数

```csharp
传递参数  -- this.$router.push({name: ' 路由的name ', params: {key: value}})
参数取值  -- this.$route.params.key

注: 使用这种方式，参数不会拼接在路由后面，地址栏上看不到参数

注意: 由于动态路由也是传递params的，所以在 this.$router.push() 方法中 path不能和params一起使用，否则params将无效。需要用name来指定页面。
复制代码
```

## 4.项目需求url优化

this.$route.query.key的方式参数显示在地址栏上, 但是并不是我们想要的, :id?id=``?

所以建议还是尽量使用router-link来实现跳转来解决地址栏的变化,更方便网站的优化

## 5.路由参数校验

Nuxt.js 可以让你在动态路由对应的页面组件中配置一个`validate`方法用于校验动态路由参数的有效性。该函数有一个布尔类型的返回值，如果返回true则表示校验通过，如果返回false则表示校验未通过。

```javascript
export default {
  // nuxt中使用validate方法进行路由参数校验，这个方法必须返回一个布尔值，为true表示校验通过，为false表示校验失败。注意validate不能写到methods属性中。
  validate(obj) {
    // console.log(obj);
    // return true
    return /^\d+$/.test(obj.params.id)
  }
}
复制代码
```

## 6.嵌套路由

1. 添加一个Vue文件，作为父组件
2. 添加一个与父组件同名的文件夹来存放子视图组件
3. 在父文件中，添加组件，用于展示匹配到的子视图

## 7.导航守卫

一、`router.js`

```
npm install @nuxtjs/router -S
```

vue-cli中怎么用，nuxt中就怎么用，几乎一样



二、`nuxt.js`

1. 中间件：middlewre

   类似于vue中的导航守卫，可以全局配置也可以页面配置

   - 全局配置（nuxt.config.js配置）

   ```js
   router:{
       middleware:'名称'
   }
   ```

   新建middleware目录 -> 名称.js

   ```js
   export default ({store, route, redirect, params, query, req, res}){
       
   }
   ```

   - 页面配置

   ```vue
   <script>
       export default{
   		middleware: 'auth'
   	}
   </script>
   ```

   新建middleware目录 -> auth.js

2. 插件：plugins

   nuxt.config.js进行配置

   ```js
   plugins: [
       '~/plugins/router.js'
   ]
   ```

   新建plugins/router.js

   ```js
   export default ({app})=>{
       app.router.beforeEach((to,from,next))=>{
           next()
       }
   }
   ```

## 8.重构项目配置路由

1. 下载

   ```
   npm install @nuxtjs/router -S
   ```

2. 在nuxt.config.js中配置

   ```
   modules: [
   	'@nuxtjs/router'
   ]
   ```

3. 把vue-cli中的router文件拷贝到nuxt项目根目录

4. 修改返回

   ```
   export function createRouter({
   	return new Router({
   		...
   	})
   })
   ```

   

# Nuxt的路由动画效果

路由的动画效果，也叫作页面的更换效果。Nuxt.js提供两种方法为路由提供动画效果，一种是全局的，一种是针对单独页面制作。

## 1.全局路由动画

全局动画默认使用page来进行设置，例如现在我们为每个页面都设置一个进入和退出时的渐隐渐现的效果。我们可以先在根目录的assets/css下建立一个normailze.css文件。

（1）添加样式文件

/assets/css/normailze.css(没有请自行建立)

```css
.page-enter-active, .page-leave-active {
    transition: opacity 2s;
}

.page-enter, .page-leave-active {
    opacity: 0;
}
```

（2）文件配置

然后在nuxt.config.js里加入一个全局的css文件就可以了。

```vbnet
css:['assets/css/main.css']
```

这时候在页面切换的时候就会有2秒钟的动画切换效果了，但是你会发现一些页面是没有效果的，这是因为你没有是`<nuxt-link>`组件来制作跳转链接。你需要进行更改。

比如我们上节课作的动态路由新闻页，你就需要改成下面的链接。

```vue
<li><nuxt-link :to="{name:'news-id',params:{id:123}}">News-1</nuxt-link></li>
```

改过之后你就会看到动画效果了。

## 2.单独设置页面动效

想给一个页面单独设置特殊的效果时，我们只要在css里改变默认的page，然后在页面组件的配置中加入transition字段即可。例如，我们想给about页面加入一个字体放大然后缩小的效果，其他页面没有这个效果。

（1）在全局样式assets/main.css 中添加以下内容

```css
.test-enter-active, .test-leave-active {
    transition: all 2s;
    font-size:12px;
}
.test-enter, .test-leave-active {
    opacity: 0;
    font-size:40px;
}
```

（2）然后在about/index.vue组件中设置

```javascript
export default {
  transition:'test'
}
```

这时候就有了页面的切换独特动效了。

总结：在需要使用的页面导入即可。





# Nuxt的默认模版和默认布局

在开发应用时，经常会用到一些公用的元素，比如网页的标题是一样的，每个页面都是一模一样的标题。这时候我们有两种方法，第一种方法是作一个公用的组件出来，第二种方法是修改默认模版。这两种方法各有利弊，比如公用组件更加灵活，但是每次都需要自己手动引入；模版比较方便，但是只能每个页面都引入。

## 1.默认模板

Nuxt为我们提供了超简单的默认模版订制方法，只要在根目录下创建一个app.html就可以实现了。现在我们希望每个页面的最上边都加入“ 学习nuxt.js” 这几个字，我们就可以使用默认模版来完成。

app.html中：

```html
<!DOCTYPE html>
<html lang="en">
<head>
   {{ HEAD }}
</head>
<body>
    <p>学习nuxt.js</p>
    {{ APP }}
</body>
</html>
```

这里的{{ HEAD }}读取的是nuxt.config.js里的信息，{{APP}} 就是我们写的pages文件夹下的主体页面了。需要注意的是HEAD和APP都需要大写，如果小写会报错的。

注意：如果你建立了默认模板后，记得要重启服务器，否则显示不会成功；但是默认布局是不用重启服务器的。

## 2.默认布局

默认模板类似的功能还有默认布局，但是从名字上你就可以看出来，默认布局主要针对于页面的统一布局使用。它在位置根目录下的layouts/default.vue。需要注意的是在默认布局里不要加入头部信息，只是关于`<template>`标签下的内容统一订制。

需求：我们在每个页面的最顶部放入“学习nuxt.js” 这几个字，看一下在默认布局里的实现。

```html
<template>
  <div>
    <p>学习nuxt.js</p>
    <nuxt/>
  </div>
</template>
```

这里的`<nuxt/>`就相当于我们每个页面的内容，你也可以把一些通用样式放入这个默认布局里，但会增加页面的复杂程度。

总结：要区分默认模版和默认布局的区别，模版可以订制很多头部信息，包括IE版本的判断；模版只能定制`<template>`里的内容，跟布局有关系。在工作中修改时要看情况来编写代码。





# Nuxt插件的使用

## 1.ElementUI使用

1. 下载npm i element-ui -S

2. 在plugins文件夹下面，创建ElementUI.js文件

   ```javascript
   import Vue from 'vue'
   import ElementUI from 'element-ui'
   Vue.use(ElementUI)
   ```

3. 在nuxt.config.js中添加配置

   ```js
   css: [
     'element-ui/lib/theme-chalk/index.css'
   ],
   plugins: [
     {src: '~/plugins/ElementUI', ssr: true }
   ],
   build: {
     vendor: ['element-ui']
   }
   ```

## 2.axios的使用

1. 安装`npm install --save axios`
2. 使用

```javascript
import axios from 'axios'

asyncData(context, callback) {
  axios.get('http://localhost:3301/in_theaters')
    .then(res => {
      console.log(res);
      callback(null, {list: res.data})
    })
}
```

1. 为防止重复打包，在nuxt.config.js中配置

```js
module.exports = {
  build: {
    vendor: ['axios']
  }
}
```



# Nuxt的错误页面和个性meta设置

当用户输入路由错误的时候，我们需要给他一个明确的指引，所以说在应用程序开发中404页面是必不可少的。Nuxt.js支持直接在默认布局文件夹里建立错误页面。

## 1.建立错误页面

在根目录下的layouts文件夹下建立一个error.vue文件，它相当于一个显示应用错误的组件。

```vue
<template>
  <div>
      <h2 v-if="error.statusCode==404">404页面不存在</h2>
      <h2 v-else>500服务器错误</h2>
      <ul>
          <li><nuxt-link to="/">HOME</nuxt-link></li>
      </ul>
  </div>
</template>

<script>
export default {
  props:['error'],
}
</script>
```

代码用v-if进行判断错误类型，需要注意的是这个错误是你需要在`<script>`里进行声明的，如果不声明程序是找不到error.statusCode的。

这里我也用了一个`<nuxt-link>`的简单写法直接跟上路径就可以了。

## 2.个性meta设置

页面的Meta对于SEO的设置非常重要，比如你现在要作个新闻页面，那为了搜索引擎对新闻的收录，需要每个页面对新闻都有不同的title和meta设置。直接使用head方法来设置当前页面的头部信息就可以了。我们现在要把New-1这个页面设置成个性的meta和title。

1.我们先把`pages/news/index.vue`页面的链接进行修改一下，传入一个title，目的是为了在新闻具体页面进行接收title，形成文章的标题。

/pages/news/index.vue

```ruby
<li><nuxt-link :to="{name:'news-id',params:{id:123,title:'nuxt.com'}}">News-1</nuxt-link></li>
```

2.第一步完成后，我们修改/pages/news/_id.vue，让它根据传递值变成独特的meta和title标签。

```vue
<template>
  <div>
      <h2>News-Content [{{$route.params.id}}]</h2>
      <ul>
        <li><a href="/">Home</a></li>
      </ul>
  </div>
</template>

<script>
export default {
  validate ({ params }) {
    // Must be a number
    return /^\d+$/.test(params.id)
  },
  data(){
    return{
      title:this.$route.params.title,
    }
  },
//独立设置head信息
  head(){
      return{
        title:this.title,
        meta:[
          {hid:'description',name:'news',content:'This is news page'}
        ]
      }
    }
}
</script>
```

注意：为了避免子组件中的meta标签不能正确覆盖父组件中相同的标签而产生重复的现象，建议利用 hid 键为meta标签配一个唯一的标识编号。



# asyncData方法获取数据

Nuxt.js贴心的为我们扩展了Vue.js的方法，增加了anyncData，异步请求数据。

## 1.造假数据

（1）创建远程数据

在这里制作一些假的远程数据，我选择的网站是myjson.com，它是一个json的简单仓库，学习使用是非常适合的。 我们打开网站，在对话空中输入JSON代码，这个代码可以随意输入，key和value均采用字符串格式创建。

```json
{
  "name": "Nuxt",
  "age": 18,
  "interest": "I love coding!"
}
```

输入后保存，网站会给你一个地址，这就是你这个JSON仓库的地址了。[api.myjson.com/bins/1ctwlm](https://link.juejin.cn/?target=https%3A%2F%2Fapi.myjson.com%2Fbins%2F1ctwlm)

（2）安装Axios

Vue.js官方推荐使用的远程数据获取方式就Axios，所以我们安装官方推荐，来使用Axios。这里我们使用npm 来安装 axios。 直接在终端中输入下面的命令：

```
npm install axios --save
```

## 2.ansycData的promise方法

我们在pages下面新建一个文件，叫做ansyData.vue。然后写入下面的代码：

```vue
<script>
import axios from 'axios'
export default {
  data(){
     return {
         name:'hello World',
     }
  },
  asyncData(){
      return axios.get('https://api.myjson.com/bins/1ctwlm')
      .then((res)=>{
          console.log(res)
          return {info:res.data}
      })
  }
}
</script>
```

这时候我们可以看到，浏览器中已经能输出结果了。asyncData的方法会把值返回到data中。是组件创建（页面渲染）之前的动作，所以不能使用this.info,

## return的重要性

```
一定要return出去获取到的对象，这样就可以在组件中使用，这里返回的数据会和组件中的data合并。这个函数不光在服务端会执行，在客户端同样也会执行。
```

## 3.ansycData的promise并发应用

```js
async asyncData(context) {
  let [newDetailRes, hotInformationRes, correlationRes] = await Promise.all([
    axios.post('http://www.huanjingwuyou.com/eia/news/detail', {
      newsCode: newsCode
    }),
    axios.post('http://www.huanjingwuyou.com/eia/news/select', {
      newsType: newsType, // 资讯类型： 3环评资讯 4环评知识
      start: 0, // 从第0条开始
      pageSize: 10,
      newsRecommend: true
    }),
    axios.post('http://www.huanjingwuyou.com/eia/news/select', {
      newsType: newsType, // 资讯类型： 3环评资讯 4环评知识
      start: 0, // 从第0条开始
      pageSize: 3,
      newsRecommend: false
    })
  ])
  return {
    newDetailList: newDetailRes.data.result,
    hotNewList: hotInformationRes.data.result.data,
    newsList: correlationRes.data.result.data,
    newsCode: newsCode,
    newsType: newsType
  }
}
```

## 4.ansycData的await方法

当然上面的方法稍显过时，现在都在用ansyc…await来解决异步,改写上面的代码。

```vue
<script>
import axios from 'axios'
export default {
  data(){
     return {
         name:'hello World',
     }
  },
  async asyncData(){
      let {data}=await axios.get('https://api.myjson.com/bins/8gdmr')
      return {info: data}
  }
}
</script>
```

## 5.注意事项+生命周期

1. asyncData 方法会在组件(限于页面组件)每次加载之前被调用
2. asyncData 可以在服务端或路由更新之前被调用
3. 第一个参数被设定为当前页面的上下文对象
4. Nuxt会将 asyncData 返回的数据融合到组件的data方法返回的数据一并返回给组件使用
5. 对于 asyncData 方式实在组件初始化前被调用的，所以在方法内饰没办法通过`this`来引用组件的实例对象





# 静态资源和打包

## 1.静态资源

（1）直接引入图片
在网上任意下载一个图片，放到项目中的static文件夹下面，然后可以使用下面的引入方法进行引用

```html
<div><img src="~static/logo.png" /></div>
```

“~”就相当于定位到了项目根目录，这时候图片路径就不会出现错误，就算打包也是正常的。

（2）CSS引入图片
如果在CSS中引入图片，方法和html中直接引入是一样的，也是用“~”符号引入。

```css
<style>
  .diss{
    width: 300px;
    height: 100px;
    background-image: url('~static/logo.png')
  }
</style>
```

这时候在npm run dev 下是完全正常的。

## 2.打包

用Nuxt.js制作完成后，你可以打包成静态文件并放在服务器上，进行运行。

1. 执行打包命令

   ```
   npm run build
   ```

2. 将打包好的以下文件放入服务器文件夹中，并且在服务器安装node环境

   ```
   .nuxt ==> 隐藏文件
   static
   nuxt.config.sj
   package.json
   ```

3. 在服务器中执行npm install

总结：Nuxt.js框架非常简单，因为大部分的事情他都为我们做好了，我们只要安装它的规则来编写代码。





# nuxt的跨域解决+拦截器

安装`axios`

```sql
npm install @nuxtjs/axios --save-dev
```

安装完成后更改配置信息：

**nuxt.config.js**

```js
module.exports = {
    modules: [
        // Doc: https://axios.nuxtjs.org/usage
        '@nuxtjs/axios',
    ],
    axios: {
        proxy: true,
        prefix: '/api', // baseURL
        credentials: true,
    },
    proxy: {
        "/eia/":"http://192.168.0.97:8181/",    //  key(路由前缀)：value(代理地址)
        changeOrigin: true, // 是否跨域
        pathRewrite: {
          '^/api': '' //路径重写
        }
    }
}
```

既然说到了`axios`，就不得不提到的一个东西就是拦截器，很是有用在项目开发过程中必不可少的。

举个例子：

安装

```js
npm install @nuxtjs/axios @nuxtjs/proxy --save
复制代码
module.expores{
  plugins: [
    {
      src: "~/plugins/axios",
      ssr: false
    },
  ],
  modules: [
    // Doc: https://axios.nuxtjs.org/usage
    '@nuxtjs/axios',
  ],
}
```

**plugins/axios.js**

```javascript
export default ({ $axios, redirect }) => {
  $axios.onRequest(config => {
    console.log('Making request to ' + config.url)
  })

  $axios.onError(error => {
    const code = parseInt(error.response && error.response.status)
    if (code === 400) {
      redirect('/400')
    }
  })
}

export default function ({$axios,store}) {
  let axios = $axios; 
 // 基本配置
  axios.defaults.timeout = 10000
  axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded'

  // 请求回调
  axios.onRequest(config => {
      config.headers['Authorization'] = store.state.token
  })

  // 返回回调
  axios.onResponse(res => {
      return res.data
  })

  // 错误回调
  axios.onError(error => {})
}
```



# 项目重构使用Nuxt指南

1. 创建Nuxt项目

   确保安装了 npx（npx 在 NPM 版本 5.2.0 默认安装了）：

   ```
    npx create-nuxt-app <项目名>
   ```

   或者用 yarn ：

   ```
    yarn create nuxt-app <项目名>
   ```

2. 配置路由

   1. 下载

      ```
      npm install @nuxtjs/router -S
      ```

   2. 在nuxt.config.js中配置

      ```js
      modules: [
      	'@nuxtjs/router'
      ]
      ```

   3. 把vue-cli中的router文件拷贝到nuxt项目根目录

   4. 修改返回

      ```js
      export function createRouter({
      	return new Router({
      		...
      	})
      })
      ```

3. 配置请求

   1. 安装axios和proxy

      ```
      npm install @nuxtjs/axios @nuxtjs/proxy -S
      ```

   2. 配置nuxt.config.js代理解决跨域问题

      ```js
      modules: [
      	'@nuxtjs/axios',
      	'@nuxtjs/proxy'
      ],
      axios: {
      	proxy: true, // 是否可以跨域
      },
      proxy: {
      	'/api':{
      		target: 'http://localhost:4000',
      		pathRewrite:{
      			'^/api':''
      		}
      	}
      }
      ```

   3. axios二次封装配置

      根目录新建plugins/axios.js

      ```js
      plugins: [
      	'@/plugins/axios'
      ]
      ```

      axios.js

      ```js
      export default function ({$axios,store}) {
        let axios = $axios; 
       // 基本配置
        axios.defaults.timeout = 10000
        axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded'
      
        // 请求回调
        axios.onRequest(config => {
            config.headers['Authorization'] = store.state.token
        })
      
        // 返回回调
        axios.onResponse(res => {
            return res.data
        })
      
        // 错误回调
        axios.onError(error => {})
      }
      ```

   4. 接口封装

      ```js
      plugins: [
      	'@/plugins/axios',
          '@/api/xxx'
      ]
      ```

      xxx.js

      ```js
      export default ({$axios},inject)=>{
          inject('getUserInfo',()=>$axios({
              url:'/api/user/getUserInfo',
              method:'GET'
          }))
      }
      ```

      页面调用接口

      ```js
      async asyncData(app){
          let res = await app.$getUserInfo()
          return {
              user:res.data
          }
      }
      ```

4. 配置Vuex

   把vue-cli的store引入到nuxtjs项目中，修改store的返回

   ```js
   const store = ()=> new Vuex.Store({
       modules: {
           ...
       }
   })
       
   export default store
   ```






# 爬坑

## 1.NuxtServerError connect ECONNREFUSED 127.0.0.1:80

**原因: asyncData方法异步请求数据时，以为/api/${params.id}这个接口的网址是 127.0.0.1:80, 所以将请求发送给了127.0.0.1:80，而我的接口服务器并没有跑在80端口上，所以报错。**

**解决方法:**

1. **将node服务器端口改成 127.0.0.1:80**
2. **将接口服务器端口改成 127.0.0.1:80**
3. **将asyncData方法使用的请求url加上域名+端口，如下所示**

```javascript
export default {
  asyncData ({ params }) {
    return axios.get(`https://127.0.0.1:3000/api/${params.id}`)
    .then((res) => {
      return { title: res.data.title }
    })
  }
}
```

## 1. 如何在 head 里面引入js文件?

> 背景: 在`<head>`标签中，以inline的形式引入`flexible.js`文件。本项目主要为移动端项目，引入`flexible.js` 实现移动端适配问题。

Nuxt.js 通过 `vue-meta` 实现头部标签管理，通过查看文档发现，可以按照如下方式配置：

```
head: {
  script: [{ innerHTML: require('./assets/js/flexible'), type: 'text/javascript', charset: 'utf-8'}],
  __dangerouslyDisableSanitizers: ['script']
}
```

## 2.nuxt使用less,sass等预处理器

> 背景：在组件中的`<template>`， `<script>` 或 `<style>` 上使用各种预处理器，加上处理器后，控制台报错。

```
npm install --save-dev node-sass sass-loader
```

但是解决过程并不是很顺利的，在阅读中文文档时，忽略版本号，按照上面的提示进行操作，发现不能成功，后来各种debug，最后发现了该解决方案。后知后觉的发现了中文文档的版本号过低，如果需要查看文档，一定要看最新版本的英文文档！

## 3. 如何使用px2rem

> 背景：在css中，写入px，通过`px2rem loader`，将px转换成rem

在以前的项目中，是通过 `px2rem loader`实现的，但是在Nuxt.js项目下，添加 css loader 还是很费力的，因为涉及到`vue-loader`。

想到了一个其他方案，可以使用 `postcss` 处理。可以在 `nuxt.config.js` 文件中添加配置，也可以在`postcss.conf.js`文件中添加。

```javascript
build: {
  postcss: [
    require('postcss-px2rem')({
      remUnit: 75 // 转换基本单位
    })
  ]
}
```

## 4. 如何拓展 webpack 配置

> 背景：给 utils 目录添加别名

刚刚说到，Nuxt.js内置了 `webpack` 配置，如果想要拓展配置，可以在 `nuxt.config.js` 文件中添加。同时也可以在该文件中，将配置信息打印出来。

```javascript
extend (config, ctx) {
  console.log('webpack config:', config)
  if (ctx.isClient) {
    // 添加 alias 配置
    Object.assign(config.resolve.alias, {
      'utils': path.resolve(__dirname, 'utils')
    })
  }
}
```

## 5. 如何添加 vue plugin

> 背景：自己封装了一个 toast vue plugin，由于 vue 实例化的过程没有暴露出来，不知道在哪个时机注入进去。

可以在 `nuxt.config.js` 中添加 plugins 配置，这样插件就会在 Nuxt.js 应用初始化之前被加载导入。

```javascript
module.exports = {
  plugins: ['~plugins/toast']
}
```

~plugins/toast.js 文件：

```javascript
import Vue from 'vue'
import toast from '../utils/toast'
import '../assets/css/toast.css'

Vue.use(toast)
```

## 6.如何修改环境变量 NODE_ENV

> 背景：在项目中，设置 3个 `NODE_ENV` 的值，来对应不同的版本。development，本地开发；release，预发布版本；production，线上版本。其中，预发布版本比production版本，多出vconsole。

```javascript
// package.json
"scripts": {
  "buildDev": "cross-env NODE_ENV=release nuxt build && backpack build",
  "startDev": "cross-env NODE_ENV=release PORT=3000 node build/main.js"
  }
```

打印 `process.env.NODE_ENV` 依旧是，`production`。

在 backpack 的源码中，找到了答案，在 执行 `backpack build` 命令时，会把 `process.env.NODE_ENV` 修改为 `production`，并且是写死的不可配置的......

无奈下，只能在 `process.env` 下，添加 `__ENV` 属性，代表 `NODE_ENV`



![clipboard.png](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/4/30/16a6db58347dfd96~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



这时，在页面中打印出来的信息 `process.env.__ENV undefined`，但是可以打印出 `process.env.NODE_ENV`。

可以通过配置 `nuxt.config.js` 中的，`env`属性，解决该问题。

```javascript
env: {
  __ENV: process.env.__ENV
}
```

## 7. Window 或 Document 对象未定义？

> 背景: 在引入第三方插件，或者直接在代码中写 `window` 时，控制台会给出警告，`window` 未定义。

发生在这个问题的原因时，node服务端并没有`window` 或 `document` 对象。解决方法，通过 `process.browser` 来区分环境。

```javascript
if (process.browser) {
  // 引入第三方插件
  require('***')
  // 或者修改window对象下某一属性
  window.mbk = {}
}
```

## 8.按需引入(UI框架等等)

例如使用UI框架：`element-ui`

我找了很多相关文章，并没有详细说明该如何引入。所以我要拿出来将他说明：

先来看下，如果不按需引入`vendor.js`的体积大小为：



![nuxt.js打包shi'li](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/4/30/16a6db582ee73b0e~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



> 第一步，下载依赖：

```
# 先下载element-ui

npm install element-ui --save

# 如果使用按需引入，必须安装babel-plugin-component(官网有需要下载说明，此插件根据官网规则不同，安装插件不同)

npm install babel-plugin-component --save-dev
```

安装好以后，按照`nuxt.js`中的规则，你需要在 `plugins/` 目录下创建相应的插件文件

在**文件根目录**创建(或已经存在)`plugins/`目录，创建名为：`element-ui.js`的文件，内容如下：

```javascript
import Vue from 'vue'

import { Button } from 'element-ui'    //引入Button按钮

export default ()=>{
    Vue.use(Button)
}
```

> 第二步，引入插件

在`nuxt.config.js`中，添加配置为：`plugins`

```javascript
css:[
'element-ui/lib/theme-chalk/index.css'
],
plugins:[
'~/plugins/element-ui'
]
```

默认为：开启SSR,采用服务端渲染，也可以**手动配置关闭SSR**，配置为：

```javascript
css:[
'element-ui/lib/theme-chalk/index.css'
],
plugins:[
    {
        src:'~/plugins/element-ui',
        ssr:false    //关闭ssr
    }
]
```

> 第三步，配置`babel`选项

在`nuxt.config.js`中，配置在`build`选项中，规则为官网规则：

```javascript
build: {
      babel:{        //配置按需引入规则
          "plugins":[
              [
                  "component",
                  {
                      "libraryName":"element-ui",
                      "styleLibraryName":"theme-chalk"
                  }
              ]
          ]
      },
    /*
     ** Run ESLINT on save
     */
    extend (config, ctx) {
      if (ctx.isClient) {
        config.module.rules.push({
           enforce: 'pre',
           test: /\.(js|vue)$/,
           loader: 'eslint-loader',
           exclude: /(node_modules)/
        })
      }
    }
 }
```

此时，我们在观察**打包以后文件体积大小**，如图：



![nuxt.js打包示例](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/4/30/16a6db58847b05ed~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



**此时**，我们成功完成了按需引入配置。

## 9.不想服务端渲染的地方

```javascript
(1)
<no-ssr>
	插件或者组件
</no-ssr>

(2)
nuxt.config.js里ssr改为false
plugins: [
    {src: '~/plugins/ElementUI', ssr: false }
]
```

## 10.nuxt必须在接口地址前加上访问域名

解决: 可以使用axios的baseURL来代理

```javascript
import Vue from 'vue'
import axios from 'axios'

// axios.defaults.baseURL = "http://www.huanjingwuyou.com/"

axios.defaults.baseURL = "http://localhost:3000/"

// axios.defaults.baseURL = "http://test.huanjingwuyou.com/"

export default axios
```

## 11. 服务端不能使用localStorage和cookie

1. 安装下载

   ```
   npm i --save cookie-universal-nuxt
   ```

2. nuxt.config.js进行配置

   ```js
   modules: [
       'cookie-universal-nuxt'
   ]
   ```

3. 正常使用api

   ```js
   this.$cookies.get()
   this.$cookies.set()
   this.$cookies.remove()
   ```
   
   