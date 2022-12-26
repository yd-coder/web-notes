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



> 原生的uniapp的pages.json文件中配置的tabbar不支持路由拦截，完成不了未登录拦截路由的需求，所以得写自定义封装的tabbar，通过if else判断跳转页面







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
