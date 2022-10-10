

# 正式使用

## 创建项目

打开 Visual Studio Code , 打开内置终端并输入下面命令创建一个`nuxt`项目：

```
npx nuxi init nuxt3-app
```

> 踩坑指南：node版本需要高于v14.16.0

## 安装依赖

```
yarn install
```

## 启动

使用 `yarn dev`以 开发模式启动nuxt:

```
yarn dev
```

✨浏览器会自动打开：http://localhost:3000







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

如果我们在文件名或者文件夹名称里面包含了`方括号`，它们将被转换为`动态路由`参数。

比如下面这样的文件结构：

```
-| pages/
---| users-[group]/
-----| [id].vue
```

上面案例我们可以在组件`[id].vue`中访问`group`、`id`这两个参数:

```vue
<template>
  {{ $route.params.group }}
  {{ $route.params.id }}
</template>
```

通过 `/users-admins/123` 导航即可:

```vue
<NuxtLink to="/users-admins/123">管理员123</NuxtLink>
```

## 4.嵌套路由

目录和文件同名，就制造了嵌套路由。

比如下面目录结构：

```
-| pages/
---| parent/
------| child.vue
---| parent.vue
```

child.vue

```vue
<template>
  <div>
    <h1>child page</h1>
  </div>
</template>
```

父组件中使用NuxtChild组件显示嵌套子组件内容，parent.vue：

```vue
<template>
  <div>
    <h1>parent page</h1>
    <!-- 子组件出口  
    <NuxtChild></NuxtChild>
  </div>
</template>
```

试一下，index.vue

```vue
<NuxtLink to="/parent/child">Parent</NuxtLink>
```

产生的路由会像下面这样：

```
{
 path: '/parent',
 children: [
  {
   path: 'child'
  }
 ]
}
```

那如果只使用`/parent`会怎么样？发现内容没有了，显然需要一个`{path: '/parent/'}`子路由

解决方法也很简单，在`parent/`目录下加一个`index.vue`即可。

## 5.跳转路由传递参数并且取值

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

## 6.项目需求url优化

this.$route.query.key的方式参数显示在地址栏上, 但是并不是我们想要的, :id?id=``?

所以建议还是尽量使用router-link来实现跳转来解决地址栏的变化,更方便网站的优化

## 7.路由参数校验

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

## 8.导航守卫

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



# Nuxt3布局模板

> 布局模板的作用就是你先定义好一个布局页面，然后提取一些通用的UI或代码到可重用的模板中，提高代码复用性，从而降低代码的复杂度，让代码重用性提高。
> 简单说就是把一些通用的UI代码代码提出来，然后放在一个模板里，使用这个模板的每个页面都拥有这些代码UI了。

## 创建布局模板和使用模板

比如现在新建一个文件夹`\layouts`然后再里边写编写一个 `defalut.vue`文件，代码如下。

```html
<template>
  <div>
    我是布局模板，default.vue
    <slot />
  </div>
</template>
```

上边这段代码就相当于你创建了一个布局模板。有了这个模板后，可以在任何你想要使用的页面中用`<NuxtLayout>`标签为页面赋予模板中的内容。比如我们想在每个页面中都赋予这个模板中的内容，就可以在 `app.vue` 页面中使用这个标签。

```html
<template>
  <NuxtLayout name="default">
    <div>
      <hello-world />
      <NuxtPage></NuxtPage>
    </div>
  </NuxtLayout>
</template>
```

这样每个页面都会有布局模板中的效果，因为`app.vue`是每个页面的出口。

### 增加多个插槽

修改`default.vue`布局模板，增加第二个插槽，一个叫做one，一个叫做two。

```html
<template>
  <div>
    我是布局模板，default.vue
    <slot name="one" />
    ---------
    <slot name="two" />
  </div>
</template>
```

这样编写，一个模板中就有了两个插槽，你可以在页面中通过`<template #xxx>`的形式来指定对应的模板插槽。 在`index.vue`中使用多个 `<template>` 配合模板实现多插槽。

```html
<template>
  <NuxtLayout name="default">
    <template #one>
      <div class="">
        <h1>Index Page</h1>
        <NuxtLink to="/demo1">Demo1.vue</NuxtLink> <br />
      </div>
    </template>
    <template #two> 我是two中的内容 </template>
  </NuxtLayout>
</template>

<script setup>
import {} from "vue";
</script>

<style scoped></style>
```

注意上面的页面就精确的对应了模板的插槽。如果你作的页面都非常相似，可以好好的利用这个模板布局。
这个插槽也可以是多个，只要名字对应正确就可以实现。





# 多层级组件懒加载组件

## 多层级组件的引用

多层级组件看似好像很复杂，也可能是我表述的不对，其实多层级组件就是把一个组件放在一个文件夹里。在实际工作中组件会非常多，所以会把组件分门别类的放置。那这种有层级的组件，我们要如何引用那。
比如在` components`文件夹下面，新建一个 `test`文件夹，然后在test文件夹下面再创建一个 `MyButton.vue`文件。

```html
<template>
  <div class=""><button>MyButton</button></div>
</template>

<script setup>
import {} from "vue";
</script>

<style scoped></style>
```

写完这个组件后，最关键的一步，就是在页面里如何引用到这个组件。方法很简单，只要在这个页面的前面加上文件夹的名称就可以了。我们的目录结构如下：

```html
--|components
----|test
------|MyButton.vue
```

那引用组件的方法就是这样的。

```html
<TestMyButton />
```

如果有很多层级，我们也依照这个规律，加入前缀就可以实现多层级组件的引用了。
这种设计的目的是让框架可以应对复杂项目和多组件的需求，让我们的组件更加有条例。

## 组件的懒加载

如果在组件名前面加上`Lazy`前缀，则可以按需懒加载该组件。懒加载组件的目的是在项目打包的时候包更小。简单理解可以理解为只有在组件显示在页面上时才进行加载。 比如我们现在要做一个文本，这个文本只有在` show`的值为 true的时候才会显示。然后其他时候他不显示。

```html
<lazyText v-if="show" />
```

这时候我们就可以使用懒加载组件。如果不总是需要该组件，这将特别有用。
在`components`文件夹下，新建一个 `LazyText.vue`的文件，然后编写代码如下。

```html
<template>
  <div class="">Lazy Text Content</div>
</template>

<script setup>
import {} from "vue";
</script>

<style scoped></style>
```

有了组件之后，我们在新建一个页面`demo2.vue`。然后用一个按钮来控制这个组件的显示和隐藏。

```html
<template>
  <div class="">
    <lazyText v-if="show" />
    <button @click="handleClick">显示/隐藏</button>
  </div>
</template>

<script setup>
import { ref } from "vue";
const show = ref(false);
const handleClick = () => {
  show.value = show.value ? false : true;
};
</script>

<style scoped></style>
```

然后到浏览器看一下效果，这种就是懒加载组件的使用。这种组件也可以用来优化页面的打开速度，比如你有一个几百行的长列表。直接加载会给服务器造成很大压力，如果在其他内容已经完成后，过1-2秒再加载这个长列表，就会给用户很好的体验。也会减少服务器的压力。





# Composable使用

在开发中我们经常会有一些通用的业务逻辑代码，需要模块化管理，这时候就可以试用`Composable` 这个文件夹来编写。比如我们常用的显示当前时间，这种常用的通用代码，就可以编写成一个单独的代码段，然后在每个页面进行使用。

## Composable中创建time.ts的编写

新建一个文件夹`composables` 然后在文件夹里边，新建一个文件`time.ts` ，然后编写下面的代码。这段代码你一定编写过，所以就不给大家讲解里边的具体含义了。你可以直接复制这段代码。

```javascript
export  const getTime=()=>{
  const timezone = 8;
  const offset_GMT = new Date().getTimezoneOffset();
  const nowDate = new Date().getTime();
  const today = new Date(nowDate + offset_GMT * 60 * 1000 + timezone * 60 * 60 * 1000);
  const date = today.getFullYear() + "-" + twoDigits(today.getMonth() + 1) + "-" + twoDigits(today.getDate());
  const time = twoDigits(today.getHours()) + ":" + twoDigits(today.getMinutes()) + ":" + twoDigits(today.getSeconds());
  const timeString ='当前时间：' + date + '  ' + time;

  return timeString;

}

function twoDigits(val) {
  if (val < 10) return "0" + val;
  return val;
}
```

写完之后，如何在页面中使用呢？在`pages` 文件夹下面，新建一个`\pages\demo3.vue` 的文件，然后你就可以直接在这个页面中使用刚才写的获得时间的方法了。

```html
<template>
  <div class="">{{ time }}</div>
</template>

<script setup>
import { ref } from "vue";
const time = ref(getTime());
</script>

<style scoped></style>
```

打开浏览器就可以获得当前时间了。 你可以把任何你在项目中经常使用的代码，封装到这个文件夹里，实现代码的复用。这个文件夹的功能和组件很相似，只是组件是UI部分的代码复用，而这个是业务逻辑代码的复用。

## composables的引入规则

`composables` 文件夹的引入规则是，**只有顶层文件会被引入**。也就是说我们如果在这个文件下再新建一个文件夹，是不会被引入到 页面中实现代码复用的。 比如下面的文件格式就没办法引入。

```html
--|composables
----|test
------|test.ts
```

但是有一种是例外的，就是我们可以写成下面的这种形式。

```html
--|composables
----|test
------|index.ts
```

我们这里测试一下，新建一个`\test` 文件夹，然后在它的下面再创建一个`index.ts` 文件。写入下面的代码。

```javascript
export const test = ()=>{
  console.log('jspang.com')
}
```

然后回到`Demo3.vue` 页面使用`test( )` 方法，结果是可以使用这个方法的。

```html
<template>
  <div class="">{{ time }}</div>
</template>

<script setup>
import { ref } from "vue";
const time = ref(getTime());
test();
</script>

<style scoped></style>
```

我们在`\test` 文件夹下面，再新建一个`test.ts` 文件，然后编写代码，如下：

```javascript
export const testTwo = ()=>{
  console.log('66666')
}
```

你会发现，这种形式是不能直接引入到页面当中进行使用的，会直接报错`testTwo is not defined`.也就是找不到这个方法。







# 数据请求

Nuxt3中提供了四种方法：`useAsyncData` 、`useFetch` 、`useLazyFetch` 、`useLazyAsyncData` 。提供的四个方法，都是获取后台数据的，但是使用场景和使用方法有所不同。

## useAsyncData的使用

使用`useAsyncData` 异步获取数据，它可以使用在页面中，组件和插件中。我们先通过这个方法来获取一下服务端的数据。 在pages文件夹下，新建一个页面，然后编写下面的代码。

```html
<template>
  <div class=""></div>
</template>

<script setup>
import {} from "vue";

const res = await useAsyncData("getList", () =>
  $fetch("http://121.36.81.61:8000/getTenArticleList")
);
console.log(res);
</script>

<style scoped></style>
```

> $fetch( )方法是nuxt3提供的内置方法，我们直接可以使用。

写完后，可以打开浏览器的调试面包，在`终端`里可以看到返回值是一个对象，对象里有四个属性。

- data: 返回的数据，我们需要的服务器数据就在这个属性里。
- error：是否存在错误，如果存在错误，可以在这个属性中获得，返回的是一个对象。
- pending：这次请求的状态，返回的是布尔值。
- refresh：这个返回的是一个函数，可以用来刷新 handler函数返回的数据。

这个方法的一个特点是，它可以进行很多选项的配置，但是在真实开发中，其实我们用的不多。最常用的就是`lazy` 选项，比如我们设置成`true` 就是需要数据都返回后，才会显示出来 ，简单说就是会阻塞页面。默认是false。 比如要设置lazy为true，就可以这样写。因为我们的数据太少，所以基本看不出来效果。

```javascript
const res = await useAsyncData(
  "getList",
  () => $fetch("http://121.36.81.61:8000/getTenArticleList"),
  {
    lazy: true,
  }
);
```

这个可配置的选项`option` 其实还是挺多的，有七项。如果想详细了解的，可以到官方去看一下，我这里给出地址。

> https://v3.nuxtjs.org/api/composables/use-async-data

但这些选项在开发中很少被配置，一般都使用默认值。所以Nuxt3又提供了一个简单的方法`useFetch` 。

## useFetch的使用

`useFetch` 可以理解为所有的都选择默认配置的`useAsyncData` 方法。比如还是上面的请求，我们就可以写成下面的形式。

```javascript
const res = await useFetch("http://121.36.81.61:8000/getTenArticleList");
```

这样我们依然可以获取数据，当然也是可以传递参数和配置请求方法的。比如我们要设置请求方法是get，传递id是1, 就可以写成下面的形式。

```javascript
const res = await useFetch("http://121.36.81.61:8000/getTenArticleList", {
  method: "get",
  id: 1,
});
```

现在我们要把获取到的数据，显示在页面上。修改一下程序，定义变量，然后用ref来赋值就可以了。

```html
<template>
  <div class="">{{ list }}</div>
</template>

<script setup>
import { ref } from "vue";

const res = await useFetch("http://121.36.81.61:8000/getTenArticleList");
const list = ref(res.data._rawValue.data);
console.log(res);
</script>

<style scoped></style>
```

这样就可以在页面上看到这些从后端得到的数据了。在实际开发中，我们会把这个数组循环输出，并制作一个精美的列表。
当我们会使用了`useAsyncData` 和 `useFetch` 这两个方法后，`useLazyAsyncData` 和`useLazyFetch` 也自然会使用了。他们只是把配置选项中的`Lazy` 设置成了true， 也就是会阻塞页面。 我就不给小伙伴们重复演示这两个方法的使用了。





# middleware路由中间件

Nuxt3提供了路由中间件的概念，你可以在整个应用使用它，目的是在导航到某一个页面之前，执行一些代码。最常见的路由守卫就可以用这个实现。

## 中间件的基本格式

我们先写一个最简单的中间件，就是在控制台打印`来的页面`，和`要去的页面`。目的是通过最简单的实例来了解中间件的基本格式。 在项目根目录，新建一个`middleware`的文件夹，然后在文件下边新建一个文件`default.global.ts` 的文件。其中的`.global`代表这个中间件是全局的，也就是在每次跳转都会执行下面的代码。

```javascript
export default defineNuxtRouteMiddleware((to, from) => {
  console.log("要去那个页面:"+to.path)
  console.log("来自那个页面:"+from.path)
})
```

写完之后，我们可以到浏览器看一下效果。如果一切正常，你可以看到，这时候你在每次跳转时，都会在终端中打出结果。
当然我们可以继续编写代码，看看`to` 和`from`里到底都有什么属性。

```javascript
export default defineNuxtRouteMiddleware((to, from) => {
  console.log("要去那个页面:"+to.path)
  console.log(to)
  console.log("来自那个页面:"+from.path)
  console.log(from)
})
```

可以看到里边的内容是非常多的，特别是`to`的时候，你可以根据这些来进行编程。

## 全局路由守卫

当我们了解路由中间件的基本写法后，在增加一些难度，来模仿一下路由守卫。比如我们要访问的页面是`http://localhost:3000/demo1`，现在设置路由守卫，不允许访问，而是跳回到首页。那代码就可以写成下面的样子。

```javascript
export default defineNuxtRouteMiddleware((to, from) => {
  if (to.path === '/demo1') {
     console.log('禁止访问这个页面')
     abortNavigation()  //停止当前导航，可以使用error进行报错
     return  navigateTo('/')
  }
})
```

这时候再到浏览器访问`demo1` 页面，已经不能访问了，但其他页面是可以访问的。

## 局部导航守卫

上面都是对所有路由起作用的，如果只想中间件对一个特殊页面起作用，也是可以的。只要去掉`.global`的后缀就是可以的。 在`middleware` 文件夹下，新建一个页面，`default.ts`，并编写下面的代码。

```javascript
export default defineNuxtRouteMiddleware((to, from) => {
  console.log("Hello JSPang.com")

})
```

这时候它对任何页面都是不起作用的，你需要再去对应的页面里注册一下。去`pages`文件夹，新建一个文件`demo7.vue`。然后需要注册这个页面使用这个中间件，代码如下。

```javascript
<template>
  <div class="">Demo7 Page</div>
</template>

<script setup>
definePageMeta({
  middleware: ["default"],
  // or middleware: 'auth'
});
</script>

<style scoped></style>
```



# SEO相关的配置

## title 和 meta 标签的作用

**title标签**：主要是为了告诉搜索引擎我们的网站标题是什么，然后搜索引擎才会根据你提供的的title给你打上tag，用户在搜索的时候才会搜索到你。 **meta标签**：这个标签根据name的不同有很多中，和SEO相关的主要是`name=description` 和`name=keywords` 这两种，如果不设置这两个标签，对SEO的效果就会有所影响。 所以我们在开发需要SEO的网站时，对这两个标签一定要进行设置。当然你可以用两种方法对meta标签进行设置，这节我们就讲两个方法。 1.使用useHead( )方法 2.直接在模板中使用标签

## Nuxt3中的useHead 和useMeta

Nuxt3中提供了 `useHead`方法来设置SEO需要的内容，用它可以设置HTML中Head的全部内容，所以这也包括meta标签的内容，基本的使用方法也是很简单。 上节课我们新建了一个Nuxt3的项目，这节我们就在上节课的项目中继续。在练习的根目录中下的 page文件夹下，新建一个文件`demo1.vue`，然后使用 `useHead( )`方法来设置头部信息。

```html
<template>
  <div class="">Demo8 Page</div>
</template>

<script setup>
useHead({
  title: " JSPang.com 技术胖的博客",
  viewport: "width=device-width,initial-scale=1,maximum-scale=1 ",
  charset: "utf-8",
  meta: [
    { name: "description", content: "技术胖的前端免费视频博客" },
    { name: "keywords", content: "技术胖" },
  ],
});
</script>

<style scoped></style>
```

如果你这时候报错，说明你按照的不是最新的Nuxt版本，可以直接安装最新的版本，我这里就在最新的版本上使用了`useHead( )` 方法。

## 使用template中的标签定义Head

除了使用`useHead( )` 方法外，你还可以直接使用`<template>` 中的的`<head>`来定义SEO相关的属性。
我们在`/pages` 文件夹下面，新建一个`demo1.vue` 的文件，然后编写下面的代码。

```html
<template>
  <div class="">
    <Head>
      <Title>{{ title }}</Title>
      <Meta name="description" :content="title" />
    </Head>
    <div>技术胖的博客</div>
  </div>
</template>

<script setup>
import { ref } from "vue";
const title = ref("技术胖的博客");
</script>

<style scoped></style>
```

从代码中可以看到，我们直接使用了`<Head>`标签，然后在里边还可以使用`<Title>`标签和`<Meta>`标签，可以设置这两个标签后，关于SEO的设置就都可以作了。 我们使用Nuxt的意义就在于可以有很好的SEO效果，所以在你开发的时候，一定要对页面进行标题、描述和关键词的设置和编写。 好了，这节的内容就是这些了，希望对你的学习有所帮助，下面你可以自己试试这个文章中的内容了。







# Cookie的设置

> 在网页制作时，经常需要临时保存一些信息到Cookie中，而不是全部都保存到数据库中，这样做能减轻服务器的压力。这节就学习一下Nuxt3中的Cookie操作。

## cookie的作用

先来了解一下Cookie的作用，Cookie最常见的开发作用就是临时记录用户个人信息，比如我们登录了一个网站，然后提醒下次记住信息，下次再浏览这个网站时，就不用登录了。
这就是cookie起的作用，当我们登录一次后，把登录信息记录在了cookie里，但是这个记录是有时效性的，通过属性可以进行设置。比如你连续7天没登录，那cookie就过期了，再浏览这个网站就需要重新登录了。

## useCookie( )方法的使用

```javascript
const cookie = useCookie(name, options)
```

制作登录太复杂，我们这属于是入门的教程，所以就用Cookie制作一个计数器，让你了解Cookie的使用方法。这里要使用的函数就是`useCookie` ,代码如下。
在pages 文件夹下，新建一个页面`demo3.vue`

```html
<template>
  <h1>Counter:{{ counter }}</h1>
  <button @click="reset">Reset</button>
  <button @click="add">Add</button>
</template>

<script setup>
const counter = useCookie("counter");
counter.value = counter.value || 0;
const reset = () => {
  counter.value = 0;
};
const add = () => {
  counter.value = counter.value + 1;
};
</script>

<style scoped></style>
```

这段代码的意思，我创建了一个叫做counter的Cookie值，然后取得Cookie值，放到页面上，如果没有Cookie值的时候，就初始化Counter的Cookie值为0。然后我又作了两个按钮，一个是直接将Cookie值设置为0，一个是每点击一次Cookie加1。
代码编写完成后，可以到浏览器中查看一下效果，你也可以按F12打开浏览器的调试模式，找到`Application` 标签，再找到`Cookie` 选项，就可以看到里边的Cookie值了，这也很好的证明我们的Cookie值设置成功了。

## 常用的相关属性

`useCookie( )`函数，第一个参数是设置Cookie值的名字，第二个参数为选项`option`,我们接着来看有那些可选择配置的Cookie参数。（注意：我这里只说两个常用的）

1. maxAge/expires

这两个参数都是设置Cookie的有效时长的，如果两个参数你都不设置，那Cookie的值在关闭浏览器的时候将会被清空。两个参数的不同是，maxAge的值是一个数字`Number`,而expires的值是一个日期对象`Date object`.
比如我们希望设置Cookie的过气时间是一个小时，也就是3600秒，那我们的配置就需要这样写。

```javascript
const counter = useCookie("counter",{
  maxAge:3600,
});
```

2. httpOnly

这算是一个安全设置，如果把httpOnly设置为true，可以对最常见的XSS攻击起到防范作用。

> 什么是HttpOnly？ HttpOnly是包含在http返回头Set-Cookiew里面的一个附件的flag，所以它是后端服务器对cookie设置的一个附件属性，在生成cookie时使用HttpOnly标志有助于减轻客户端脚本访问收保护cookie的风险。

```javascript
const counter = useCookie("counter",{
  htttpOnly:true,
});
```

3. secure
   这也是一个安全设置，如果你的网址不是`HTTPS`的，并且把`secure`的值设置为true，那Cookie的值就不会传递给服务端。总的来说还是一个为了服务器安全的设置。

```javascript
const counter = useCookie("counter",{
  secure:true,
});
```

这个需要配置HTTPS 所以不太好演示，这里也就不演示了。
其余的还有`domain` ,`path` ,`sameSite` ,`encode`,`decode` 这些属性设置，其实都跟安全有关，因为Cookie的设置确实需要考虑安全性，所以根据服务端和app的需求，尽量设置多的安全性参数。
