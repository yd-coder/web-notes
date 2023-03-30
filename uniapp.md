# 生命周期函数

## 应用的生命周期函数

| 函数名   | 说明                                      |
| -------- | ----------------------------------------- |
| onLaunch | 当uni-app初始化完成时触发(全局只触发一次) |
| onShow   | 当uni-app启动，或从后台进入前台显示       |
| onHide   | 当uni-app从前台进入后台                   |
| onError  | 当uni-app报错时触发                       |



## 页面的生命周期函数

| 函数名            | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| onLoad            | 监听页面加载，其参数为上个页面传递的数据，参数类型为Object(用于页面传参) |
| onShow            | 监听页面显示，页面每次出现在屏幕上都触发，包括从下级页面点返回露出当前页面 |
| onReady           | 监听页面初次渲染完成                                         |
| onHide            | 监听页面隐藏                                                 |
| onUnload          | 监听页面卸载                                                 |
| onResize          | 监听窗口尺寸变化(App，微信小程序使用)                        |
| onPullDownRefresh | 监听用户下拉刷新动作                                         |
| onReachBottom     | 页面滚动到底部事件，常用于下拉下一页数据                     |





# 网络请求



### uni.request(OBJECT)

```js
uni.request({
    url:'',
    timeout: ,
    method:'',
    header:{},
    success(res){
    },
    fail(){
    }
})
```

## 封装请求

```js
//util/api.js
const BASE_URL = 'http://localhost:8082'
export const myRequest = (options)=>{
	return new Promise((resolve,reject)=>{
		uni.request({
			url:BASE_URL+options.url,
			method: options.method || 'GET',
			data: options.data || {},
			success: (res)=>{
				if(res.data.status !== 0) {
					return uni.showToast({
						title: '获取数据失败'
					})
				}
				resolve(res)
			},
			fail: (err)=>{
				uni.showToast({
					title: '请求接口失败'
				})
				reject(err)
			}
		})
	})
}
```

```js
//main.js挂载到对象原型全局使用
import { myRequest } from './util/api.js'
Vue.prototype.$myRuquest = myRequest
```



# 数据缓存

### uni.setStorage(OBJECT)

```js
uni.setStorage({
	key: 'storage_key',
	data: 'hello',
	success: function () {
		console.log('success');
	}
});
```

### uni.setStorageSync(KEY,DATA)

```js
uni.setStorageSync('storage_key', 'hello');
```

### uni.getStorage(OBJECT)

```js
uni.getStorage({
	key: 'storage_key',
	success: function (res) {
		console.log(res.data);
	}
});
```

### uni.getStorageSync(KEY)

```js
uni.getStorageSync('storage_key');
```

### uni.removeStorage(OBJECT)

```js
uni.removeStorage({
	key: 'storage_key',
	success: function (res) {
		console.log('success');
	}
});
```

### uni.removeStorageSync(KEY)

```js
uni.removeStorageSync('storage_key');
```

### uni.clearStorage()

```js
uni.clearStorage();
```

### uni.clearStorageSync()

```js
uni.clearStorageSync();
```

## 各平台缓存区别

**uni-app的Storage在不同端的实现不同：**

- H5端为localStorage，浏览器限制5M大小，是缓存概念，可能会被清理
- App端为原生的plus.storage，无大小限制，不是缓存，是持久化的
- 各个小程序端为其自带的storage api，数据存储生命周期跟小程序本身一致，即除用户主动删除或超过一定时间被自动清理，否则数据都一直可用。
- 微信小程序单个 key 允许存储的最大数据长度为 1MB，所有数据存储上限为 10MB。
- 支付宝小程序单条数据转换成字符串后，字符串长度最大200*1024。同一个支付宝用户，同一个小程序缓存总上限为10MB。
- 百度、字节跳动小程序文档未说明大小限制
- 非App平台清空Storage会导致uni.getSystemInfo获取到的deviceId改变



# 条件编译

以注释平台类型包裹代码，实现代码兼容不同平台

```
// #ifdef APP-PLUS
需条件编译的代码
// #endif
仅出现在 App 平台下的代码

// #ifdef H5
代码
// #endif 

// #ifdef H5 || MP-WEIXIN
需条件编译的代码
// #endif
在 H5 平台或微信小程序平台存在的代码（这里只有||，不可能出现&&，因为没有交集）
```

**注意：**

- 条件编译是利用注释实现的，在不同语法里注释写法不一样，js使用 `// 注释`、css 使用 `/* 注释 */`、vue/nvue 模板里使用 `<!-- 注释 -->`；
- 条件编译APP-PLUS包含APP-NVUE和APP-VUE，APP-PLUS-NVUE和APP-NVUE没什么区别，为了简写后面出了APP-NVUE ；
- 使用条件编译请保证`编译前`和`编译后`文件的正确性，比如json文件中不能有多余的逗号；
- `VUE3` 需要在项目的 `manifest.json` 文件根节点配置 `"vueVersion" : "3"`

# 组件通信

```js
//子传父
this.$emit('updateNum',param)

//发送全局注册事件(相当于vue的事件总线$bus)
uni.$emit('updateNum',param) 

//监听发送的全局事件和参数
uni.$on('updateNum',param=>{
})
```





# 路由跳转

### uni.navigateTo(OBJECT)

> 保留当前页面，跳转到应用内的某个页面(带返回)使用`uni.navigateBack`可以返回到原页面。

```js
//在起始页面跳转到test.vue页面并传递参数
uni.navigateTo({
	url: '/pages/test/test?id=1&name=uniapp'
});
```

**注意：**

- 页面跳转路径有层级限制，不能无限制跳转新页面
- 跳转到 tabBar 页面只能使用 switchTab 跳转
- 路由API的目标页面必须是在pages.json里注册的vue页面。如果想打开web url，在App平台可以使用 [plus.runtime.openURL (opens new window)](http://www.html5plus.org/doc/zh_cn/runtime.html#plus.runtime.openURL)或web-view组件；H5平台使用 window.open；小程序平台使用web-view组件（url需在小程序的联网白名单中）。在hello uni-app中有个组件ulink.vue已对多端进行封装，可参考。



### uni.redirectTo(OBJECT)

> 关闭当前页面，跳转到应用内的某个页面。(不带返回)

```js
uni.redirectTo({
	url: 'pages/test/test?id=1'
});
```

**注意：**

- 跳转到 tabBar 页面只能使用 switchTab 跳转



# 图片预览

### uni.previewImage(OBJECT)

```js
uni.previewImage({
			urls:[],
    		current:''
})
```



# 导航守卫路由拦截

> 前提：uni-app不像在vue中的router一样，可以配置导航守卫的功能，所以内置没有的话就需要我们自己想办法，可以写自定义封装的tabbar，通过if else判断跳转页面，但是一般我们会借助第三方插件去用。
>
> 注：微信小程序原生tabbar也不能使用uniapp的路由拦截，得换成自定义tabbar
>
> 参考链接： https://hhyang.cn/v2/start/quickstart.html 

下面详细说一下：uni-simple-router的使用（这里的版本采用2.x了）

### 一、执行安装命令

```perl
npm install uni-simple-router
npm install uni-read-pages
```

**千万看一下是否安装成功了，如果没安装成功建议采用cnpm**

### 二、在项目根目录新建vue.config.js文件

```javascript
//vue.config.js
const TransformPages = require('uni-read-pages')
const {webpack} = new TransformPages()
module.exports = {
	configureWebpack: {
		plugins: [
			new webpack.DefinePlugin({
				ROUTES: webpack.DefinePlugin.runtimeValue(() => {
					const tfPages = new TransformPages({
						includes: ['path', 'name', 'aliasPath']
					});
					return JSON.stringify(tfPages.routes)
				}, true )
			})
		]
	}
}
```

### 三、在项目根目录新建router.js文件

```javascript
// router.js
import {RouterMount,createRouter} from 'uni-simple-router';

const router = createRouter({
	platform: process.env.VUE_APP_PLATFORM,  
	routes: [...ROUTES]
});
//全局路由前置守卫
router.beforeEach((to, from, next) => {
	next();
});
// 全局路由后置守卫
router.afterEach((to, from) => {
    console.log('跳转结束')
})

export {
	router,
	RouterMount
}
```

### 四、在main.js文件中导入插件

```javascript
// main.js
import Vue from 'vue'
import App from './App'
import {router,RouterMount} from './router.js'  //路径换成自己的
Vue.use(router)

App.mpType = 'app'
const app = new Vue({
    ...App
})

//v1.3.5起 H5端 你应该去除原有的app.$mount();使用路由自带的渲染方式
// #ifdef H5
	RouterMount(app,router,'#app')
// #endif

// #ifndef H5
	app.$mount(); //为了兼容小程序及app端必须这样写才有效果
// #endif
```

**然后去运行项目，运行中可能会出现报错，比如：ROUTES找不到的问题，解决办法就是重启一下项目就好了**



### 五、使用插件跳转页面和导航守卫

```kotlin
this.$Router.push('/pages/news/news') //这个和vue基本类似了
```

全局和路由导航守卫都可以在：router.js文件中进行配置

```javascript
// router.js
import {RouterMount,createRouter} from 'uni-simple-router';


const router = createRouter({
	platform: process.env.VUE_APP_PLATFORM,  
	routes: [
		{
			path: "/",
			beforeEnter:(to,from,next)=>{
				console.log( 11 )
				next({name:'router3',params:{msg:'我是从router2路由拦截过来的'}});
			}
		}, 
		{
			path: "/pages/news/news",
			beforeEnter:(to,from,next)=>{
				if( true ){
					next('/')
				}
				next();
			}
		}
		
	]
});

export {
	router,
	RouterMount
}
```





# 搭建node.js后端环境

1. 全局命令安装express：

```
npm install express-generator -g
```

2. 进入项目目录express命令生成serve文件夹：

```
express --view=ejs server
```

3. 进入serve目录安装依赖

```
npm install
```

4. 重启node环境

```
npm start
```

5. 默认端口3000
6. 前端请求node接口

手机电脑同一个wifi

不能使用localhost，用ip地址

注：遇到缺少moudles的保错，即引用的第三方库未安装，npm install安装即可



# 字体图标引入

1. iconfont创建项目，将需要的图标加入购物车
2. 下载项目
3. 只保留iconfont.css和它需要的iconfont.ttf，iconfont.woff，iconfont.woff2文件
4. 修改iconfont.css的src路径

```
//字体文件的引用路径推荐使用以 ~@ 开头的绝对路径。
@font-face {
  font-family: "iconfont"; 
  src: url('~@/static/icon/iconfont.woff2?t=1654063229656') format('woff2'),
       url('~@/static/icon/iconfont.woff?t=1654063229656') format('woff'),
       url('~@/static/icon/iconfont.ttf?t=1654063229656') format('truetype');
}
```

5. 在App.vue的style引入iconfont.css

```
@import url(@/static/icon/iconfont.css);
```

6. 在需要的页面使用字体图标(以Font class为例)

```
<span class="iconfont icon-xxx"></span>
```



# svg引入



最近在做一个需求，logo等png小图片在部分设备下会变糊，又不想做大图来缩放解决，于是考虑换成svg
uni-app目前不支持svg标签，只能通过别的方式委婉的使用SVG。

1. 通过image组件src引入一个svg文件
2. svg转换成base64再通过image组件src引入
3. svg转换成行间形式如："data:image/svg+xml;utf8,svg文件内容"，再通过image组件src引入
4. 通过background-image设置svg文件，但是uni-app背景只支持远程，需要把svg文件放到远程服务器上
5. svg转base64再通过background-image设置来实现
6. svg转换成行间形式如："data:image/svg+xml;utf8,svg文件内容"，再通过background-image设置来实现
   理想状态下以上方式都是可以实现，而且开发调试也都是OK的，但是在打包成ios后，发现第1种方式引入的SVG文件全部丢失。
   **此处推荐用3和6方案，因为文件转base64体积会变大**。

**svg文件转行间svg/base64**

网上有好多在线的转换工具。
推荐使用鑫大神制作的转换工具：svg在线在线压缩合并
界面如下：

```html
// 行间svg
<image src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 200 54'%3E%3Cpath d='M39.5 11.6l19.4 24.6c.2.2.2.6 0 .8l-9.7 12.4c-.2.3-.6.3-.9.1l-.1-.1L34 31.5 19.9 49.4c-.2.3-.6.3-.9.1l-.1-.1-4.8-6c-.2-.2-.2-.6 0-.9l24.3-30.9c.2-.3.6-.3.9-.1.1 0 .2.1.2.1zM9.1 36.2L33.5 5.3c.2-.3.6-.3.9-.1l.1.1 2.4 3c.2.2.2.6 0 .8L12.5 40c-.2.3-.6.3-.9.1 0 0-.1 0-.1-.1l-2.4-3c-.1-.2-.1-.6 0-.8z' fill-rule='evenodd' clip-rule='evenodd' fill='%230e8cf2'/%3E%3Ctext transform='translate(68 38)' font-size='30' font-family='MicrosoftYaHei' fill='%2325292c'%3E远光家园%3C/text%3E%3Cpath fill='none' d='M0 0h200v54H0z'/%3E%3C/svg%3E" />
// svg转换成base64
<image src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyMDAgNTQiPjxwYXRoIGQ9Ik0zOS41IDExLjZsMTkuNCAyNC42Yy4yLjIuMi42IDAgLjhsLTkuNyAxMi40Yy0uMi4zLS42LjMtLjkuMWwtLjEtLjFMMzQgMzEuNSAxOS45IDQ5LjRjLS4yLjMtLjYuMy0uOS4xbC0uMS0uMS00LjgtNmMtLjItLjItLjItLjYgMC0uOWwyNC4zLTMwLjljLjItLjMuNi0uMy45LS4xLjEgMCAuMi4xLjIuMXpNOS4xIDM2LjJMMzMuNSA1LjNjLjItLjMuNi0uMy45LS4xbC4xLjEgMi40IDNjLjIuMi4yLjYgMCAuOEwxMi41IDQwYy0uMi4zLS42LjMtLjkuMSAwIDAtLjEgMC0uMS0uMWwtMi40LTNjLS4xLS4yLS4xLS42IDAtLjh6IiBmaWxsLXJ1bGU9ImV2ZW5vZGQiIGNsaXAtcnVsZT0iZXZlbm9kZCIgZmlsbD0iIzBlOGNmMiIvPjx0ZXh0IHRyYW5zZm9ybT0idHJhbnNsYXRlKDY4IDM4KSIgZm9udC1zaXplPSIzMCIgZm9udC1mYW1pbHk9Ik1pY3Jvc29mdFlhSGVpIiBmaWxsPSIjMjUyOTJjIj7ov5zlhYnlrrblm608L3RleHQ+PHBhdGggZmlsbD0ibm9uZSIgZD0iTTAgMGgyMDB2NTRIMHoiLz48L3N2Zz4=" />
```

# 分包

## 分包配置

因小程序有体积和资源加载限制，各家小程序平台提供了分包方式，优化小程序的下载和启动速度。

所谓的主包，即放置默认启动页面/TabBar 页面，以及一些所有分包都需用到公共资源/JS 脚本；而分包则是根据pages.json的配置进行划分。

在小程序启动时，默认会下载主包并启动主包内页面，当用户进入分包内某个页面时，会把对应分包自动下载下来，下载完成后再进行展示。此时终端界面会有等待提示。

subPackages 节点接收一个数组，数组每一项都是应用的子包，其属性值如下：

| 属性  | 类型   | 是否必填 | 描述                                                         |
| :---- | :----- | :------- | :----------------------------------------------------------- |
| root  | String | 是       | 子包的根目录                                                 |
| pages | Array  | 是       | 子包由哪些页面组成，参数同 [pages](https://uniapp.dcloud.net.cn/collocation/pages.html#pages) |

**注意：**

- `subPackages` 里的pages的路径是 `root` 下的相对路径，不是全路径。
- 微信小程序每个分包的大小是2M，总体积一共不能超过20M。
- 百度小程序每个分包的大小是2M，总体积一共不能超过8M。
- 支付宝小程序每个分包的大小是2M，总体积一共不能超过8M。
- QQ小程序每个分包的大小是2M，总体积一共不能超过24M。
- 字节小程序每个分包的大小是2M，总体积一共不能超过16M（字节小程序基础库 1.88.0 及以上版本开始支持，字节小程序开发者工具请使用大于等于 2.0.6 且小于 3.0.0 的版本）。
- 快手小程序每个分包的大小是2M，总体积一共不能超过24M。
- 分包下支持独立的 `static` 目录，用来对静态资源进行分包。
- `uni-app`内支持对`微信小程序`、`QQ小程序`、`百度小程序`、`支付宝小程序`、`字节小程序(HBuilderX 3.0.3+)`、`快手小程序`分包优化，即将静态资源或者js文件放入分包内不占用主包大小。详情请参考：[关于分包优化的说明](https://uniapp.dcloud.net.cn/collocation/manifest#关于分包优化的说明)
- 针对vendor.js过大的情况可以使用运行时压缩代码
  - `HBuilderX`创建的项目勾选`运行-->运行到小程序模拟器-->运行时是否压缩代码`
  - `cli`创建的项目可以在`package.json`中添加参数`--minimize`，示例：`"dev:mp-weixin": "cross-env NODE_ENV=development UNI_PLATFORM=mp-weixin vue-cli-service uni-build --watch --minimize"`

**使用方法：**

假设支持分包的 `uni-app` 目录结构如下：

```
┌─pages
│  ├─index
│  │  └─index.vue
│  └─login
│     └─login.vue
├─pagesA
│  ├─static
│  └─list
│     └─list.vue
├─pagesB
│  ├─static
│  └─detail
│     └─detail.vue
├─static
├─main.js
├─App.vue
├─manifest.json
└─pages.json
```

则需要在 pages.json 中填写

```javascript
{
	"pages": [{
		"path": "pages/index/index",
		"style": { ...}
	}, {
		"path": "pages/login/login",
		"style": { ...}
	}],
	"subPackages": [{
		"root": "pagesA",
		"pages": [{
			"path": "list/list",
			"style": { ...}
		}]
	}, {
		"root": "pagesB",
		"pages": [{
			"path": "detail/detail",
			"style": { ...}
		}]
	}],
	"preloadRule": {
		"pagesA/list/list": {
			"network": "all",
			"packages": ["__APP__"]
		},
		"pagesB/detail/detail": {
			"network": "all",
			"packages": ["pagesA"]
		}
	}
}
```

## preloadRule

分包预载配置。

配置preloadRule后，在进入小程序某个页面时，由框架自动预下载可能需要的分包，提升进入后续分包页面时的启动速度

`preloadRule` 中，`key` 是页面路径，`value` 是进入此页面的预下载配置，每个配置有以下几项：

| 字段     | 类型        | 必填 | 默认值 | 说明                                                         |
| -------- | ----------- | ---- | ------ | ------------------------------------------------------------ |
| packages | StringArray | 是   | 无     | 进入页面后预下载分包的 `root` 或 `name`。`__APP__` 表示主包。 |
| network  | String      | 否   | wifi   | 在指定网络下预下载，可选值为：all（不限网络）、wifi（仅wifi下预下载） |

app的分包，同样支持preloadRule，但网络规则无效。

## 分包优化

- 在`manifest.json`的源码视图中找对应平台的配置下添加`"optimization":{"subPackages":true}`开启分包优化
- 目前只支持`mp-weixin`、`mp-qq`、`mp-baidu`、`mp-toutiao`、`mp-kuaishou`的分包优化
- 分包优化具体逻辑：
  - 静态文件：分包下支持 static 等静态资源拷贝，即分包目录内放置的静态资源不会被打包到主包中，也不可在主包中使用
  - js文件：当某个 js 仅被一个分包引用时，该 js 会被打包到该分包内，否则仍打到主包（即被主包引用，或被超过 1 个分包引用）
  - 自定义组件：若某个自定义组件仅被一个分包引用时，且未放入到分包内，编译时会输出提示信息

**分包内静态文件示例**

pages.json

```json
"subPackages": [{
	"root": "pages/sub",
	"pages": [{
		"path": "index/index"
	}]
}]
```

以上面的分包为例，放在每个分包root对应目录下的静态文件会被打包到此分包内。



# 微信登录

1. 调uni.login

   ```js
   uni.login({
   	provider: 'weixin',
   	success: (res) => {
   	// 获取 code
   		if (res.code) {
              // 请求后端登录接口
           }
       }
   })
   ```

2. 然后需要把uni.login返回的code码传递给后端

   ​	(一般后端接收code后会调微信官方的code2Session接口获取用户信息，再查询数据库user表判断用户是登录还是未注册去注册)

3. 根据请求后端登录接口返回的状态码来判断用户是登录成功还是未注册去注册

4. 如果返回状态码200表示用户登录成功，需要把后端返回的token存起来，再判断用户是否绑定手机号；

   如果返回状态码60003表示未注册，需要再请求后端注册接口；





# uniapp配置服务协议和隐私政策

> 国家对需要上架应用商店的APP有强制要求，必须启动时有服务协议和隐私政策弹窗。

第一步

![img](https://upload-images.jianshu.io/upload_images/17596677-7fadb6d4d505f90f.png?imageMogr2/auto-orient/strip|imageView2/2/w/1045/format/webp)

image.png


勾选原生隐私政策提示框后会自动生成androidPrivacy.json文件[具体官网链接](https://links.jianshu.com/go?to=https%3A%2F%2Fask.dcloud.net.cn%2Farticle%2F36937)
androidPrivacy.json:

```json
{
    "version" : "1.0.0",
    "prompt" : "template",
    "title" : "服务协议和隐私政策",
    "message" : "　尊敬的用户，欢迎使用本应用，请务必审慎阅读、充分理解“服务协议”和“隐私政策”各条款，包括但不限于：为了更好的向您提供服务，我们需要收集您的设备标识、操作日志等信息用于分析、优化应用性能。<br/>　　您可阅读<a href=\"https://www.xxx.cn/textTwo.html\">《服务协议》</a>和<a href=\"https://www.xxx.cn/textOne.html\">《隐私政策》</a>了解详细信息。如果您同意，请点击下面按钮开始接受我们的服务。",
    "buttonAccept" : "同意并接受",
    "buttonRefuse" : "暂不同意",
    "hrefLoader" : "system|default",
    "disagreeMode" : {
        "support" : false,
        "loadNativePlugins" : false,
        "visitorEntry" : true
    },
    "styles" : {
        "backgroundColor" : "#F7F7F7",
        "borderRadius" : "5px",
        "title" : {
            "color" : "#EB292F"
        },
        "buttonAccept" : {
            "color" : "#EB292F"
        },
        "buttonRefuse" : {
            "color" : "#999999"
        },
        "buttonVisitor" : {
            "color" : "#999999"
        }
    }
}
```

这个文件不能写注释,会影响提示框显示,里面a标签的跳转链接指向对应的服务协议html,放在服务器上的



![img](https://upload-images.jianshu.io/upload_images/17596677-ad0196b8a564022f.png?imageMogr2/auto-orient/strip|imageView2/2/w/1080/format/webp)

- message 模板提示框上的内容，richtext类型字符串，支持a/font/br等节点，点击a链接会调用内置页面打开其href属性中链接地址。
  - HBuilderX3.2.5以下版本a链接的href属性仅支持网络地址，以http:或https:开头，如“https://www.dcloud.io/privacy.html”
  - HBuilder3.2.5及以上版本a链接的href属性支持本地地址，相对于应用根目录，如“static/privacy.html” **注意：务必配置此提示内容，参考上面示例内容并修改《服务协议》和《隐私政策》链接地址**

### 隐私协议内容需要注意的问题

需要在《隐私政策》中必告知用户您的应用基于DCloud uni-app(5+ App/Wap2App)开发，添加如下参考条款：

```
我们的产品基于DCloud uni-app(5+ App/Wap2App)开发，应用运行期间需要收集您的设备唯一识别码（IMEI/android ID/DEVICE_ID/IDFA、SIM 卡 IMSI 信息、OAID）以提供统计分析服务，并通过应用启动数据及异常错误日志分析改进性能和用户体验，为用户提供更好的服务。
```

另外隐私政策中需要补充使用到的三方SDK，参考：

#### [#](https://uniapp.dcloud.net.cn/tutorial/app-privacy-android.html#uni-app默认集成三方sdk)uni-app默认集成三方SDK

请参考文档[Android平台各功能模块隐私合规条款](https://ask.dcloud.net.cn/article/39484)

#### [#](https://uniapp.dcloud.net.cn/tutorial/app-privacy-android.html#uni原生插件)uni原生插件

如果应用使用了uni原生插件，需要注意一下几点：

- 使用插件时请查看插件详情页面中的 `隐私、权限声明` 。（插件使用什么sdk？获取了什么用户信息？都应由插件作者提供并填写在 `隐私、权限声明`中）
- 将插件中用到的三方SDK信息添加到用户隐私协议中。例如集成了`百度定位`。就需要在隐私协议中说明集成了百度定位SDK。获取了xxx用户信息!用于xxx.
- 如果发现插件有获取用户信息而插件详情页并没有提供`隐私、权限声明`，请与插件开发者或与我们反馈共同督促进行补充。

#### [#](https://uniapp.dcloud.net.cn/tutorial/app-privacy-android.html#其它)其它

《隐私政策》必须非常清楚、全面地说明（不要用可能收集、了解用户信息这种模糊不清晰的词语）收集用户个人信息的目的、方式和范围。 如果应用使用“通讯录”、“短信”等相关功能，请根据应用业务场景进行描述。
