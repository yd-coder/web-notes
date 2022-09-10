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



