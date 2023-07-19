# 已淘汰语法

## 过滤器filters

```javascript
// 用计算属性/{{方法(参数)}}两种方法替代
// 参数 | 过滤器
// 自动传参

{{book.price | totalPrice}}
```

## Options API

> Options的data,methods,watch逻辑比较分散 可读性差 可维护性差
>
> 对比vue3的Composition逻辑分明 可维护性高

## component

> vue3中的setup语法糖引入组件时只需要import即可使用
>
> vue2中引入组件需要导入，注册



## 使用ref访问子组件

在`Vue2`中，使用`ref`即可访问子组件里的任意数据及方法，但在`Vue3`中则必须使用`defineExpose`暴露子组件内的方法或属性才能被父组件所调用。

```vue
// 父组件
<template>
  <child ref="childRef" />
</template>

<script lang="ts" setup>
import { ref, onMounted } from 'vue'

const childRef = ref()

onMounted(() => {
  childRef.value.getData()
})
</script>

// 子组件
<script lang="ts" setup>
import { defineExpose } from 'vue'

const getData = () => {
  console.log('getData')
}
const name = ref('张三')

defineExpose({
  getData,
  name
})
</script>
```

## 生命周期

> 在我们使用Vue3组合式API是没有 `beforeCreate` 和 `created `这两个生命周期的





# 响应式原理

Vue2使用的是`Object.defineProperty` Vue3 使用的是`Proxy`

**2.0的不足**

对象只能劫持设置好的数据，新增的数据需要Vue.Set(xxx)；数组只能操作七种方法，修改某一项值无法劫持。

```typescript
export const reactive = <T extends object>(target:T) => {
    return new Proxy(target,{
        get (target,key,receiver) {
          const res  = Reflect.get(target,key,receiver) as object
          return res
        },
        set (target,key,value,receiver) {
           const res = Reflect.set(target,key,value,receiver)
           return res
        }
    })
}
```

Vue3的响应式原理依赖了`Proxy`这个核心 API，通过`Proxy`可以劫持对象的某些操作。



# Vue3自动引入插件

## unplugin-auto-import

1. 安装

```
npm i -D unplugin-auto-import
```

2. vite.config.js配置

```ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import VueJsx from '@vitejs/plugin-vue-jsx'
import AutoImport from 'unplugin-auto-import/vite'
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(),VueJsx(),AutoImport({
    imports:['vue','@vueuse/core'],
    dts:"types/auto-import.d.ts",
    eslintrc:{
        enabled:true,
        filepath:'./.eslintrc-auto-import.json',
        globalsPropValue:true
    }
   // 生成的'./.eslintrc-auto-import.json'要在.eslintrc.cjs中继承一下
  })]
})
```

配置完成之后使用ref reactive watch hooks等无须 import 导入可以直接使用 

## unplugin-vue-components

1. 安装

```
npm i -D unplugin-vue-components
```

2. vite.config.js配置

```ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import VueJsx from '@vitejs/plugin-vue-jsx'
import components from 'unplugin-vue-components/vite'
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(),VueJsx(),components({
      dirs:['src/components/'],dts:'types/components.d.ts',deep:true
  })]
})
```

配置完成之后会自动递归src/components下的组件生成声明文件，所有组件都可以全局使用

# 生命周期

| Vue2选项式API   | Vue3 Hook inside setup                                       |
| --------------- | ------------------------------------------------------------ |
| beforeCreate    | 弃用                                                         |
| created         | 弃用                                                         |
| beforeMount     | onBeforeMount （在组件DOM实际渲染安装之前调用。在这一步中，根元素还不存在） |
| mounted         | onMounted（在组件的第一次渲染后调用，该元素现在可用，允许直接DOM访问） |
| beforeUpdate    | onBeforeUpdate（数据更新之前调用）                           |
| updated         | onUpdated（数据更新之后调用）                                |
| beforeUnmount   | onBeforeUnmount（在卸载组件实例，销毁DOM之前调用。在这个阶段，实例仍然是完全正常的） |
| unmounted       | onUnmounted（卸载组件实例后调用。调用此钩子时，组件实例的所有指令都被解除绑定，所有事件侦听器都被移除，所有子组件实例被卸载） |
| errorCaptured   | onErrorCaptured                                              |
| renderTracked   | onRenderTracked                                              |
| renderTriggered | onRenderTriggered                                            |
| activated       | onActivated (keep-alive切换组件出现)                         |
| deactivated     | onDeactivated (keep-alive切换组件离开)                       |

```javascript
import {
  ref,
  onBeforeMount,
  onMounted,
  onBeforeUpdate,
  onUpdated,
  onBeforeUnmount,
  onUnmounted,
} from 'vue';
export default {
  name: 'index',
  setup() {
    let lyb = ref(1);
    function fn() {
      lyb.value++;
    }

    onBeforeMount(() => console.log('---onBeforeMount---'));
    onMounted(() => console.log('---onMounted---'));
    onBeforeUpdate(() => console.log('---onBeforeUpdate---'));
    onUpdated(() => console.log('---onUpdated---'));
    onBeforeUnmount(() => console.log('---onBeforeUnmount---'));
    onUnmounted(() => console.log('---onUnmounted---'));
    return {
      lyb,
      fn,
    };
  },
};
```



# Ref全家桶

## 应用场景

我们这样操作是无法改变message的值，因为message不是响应式的无法被vue跟踪要改成ref(vue2的data改值是响应式的)

```vue
<template>
  <div>
    <button @click="changeMsg">change</button>
    <div>{{ message }}</div>
  </div>
</template>
 
<script setup lang="ts">
let message: string = "我是message"
 
const changeMsg = () => {
   message = "change msg"
}
</script>
 
<style>
</style>
```

## ref

> 绑定基本数据类型，使其响应式，需要加`.value`
>
> PS：虽然`ref`能绑定复杂数据类型，但底层依旧回去调用`reactive`进行绑定，无疑是多此一举，而且还需要追加`.value`，所以`ref`只适合绑定基础数据类型，复杂数据类型交给[reactive](#reactive)处理

注意被`ref`包装之后需要`.value `来进行赋值；`ref`是函数，`Ref`是类型

```vue
import { ref } from 'vue';

let a = ref('小明');
let b = ref([['小明']]);
let c = ref({xm:{name:'小明'}});

function fn() {
  a.value = '小红';
  c.value.xm.name = '小红'; //支持更深的响应
  b.value[0][0] = '小红'; //支持更深的响应
}
<template>
  <div>
    <button @click="changeMsg">change</button>
    <div>{{ message }}</div>
  </div>
</template>
 
<script setup lang="ts">
import {ref,Ref} from 'vue'
let message:Ref<string> = ref("我是message")
 
const changeMsg = () => {
   message.value = "change msg"
}
</script>
 
<style>
</style>

//--------------------------------ts两种方式

<template>
  <div>
    <button @click="changeMsg">change</button>
    <div>{{ message }}</div>
  </div>
</template>
 
<script setup lang="ts">
import { ref } from 'vue'
let message = ref<string | number>("我是message")
 
const changeMsg = () => {
  message.value = "change msg"
}
</script>
 
<style>
</style>
```

## isRef

> 判断是否为`ref`类型

```js
import { ref, Ref,isRef } from 'vue'
let message: Ref<string | number> = ref("我是message")
let notRef:number = 123
const changeMsg = () => {
  message.value = "change msg"
  console.log(isRef(message)); //true
  console.log(isRef(notRef)); //false
  
}
```

## shallowRef

> 创建一个跟踪自身 `.value` 变化的 ref，但不会使其值也变成响应式的

修改其属性是非响应式，值会更改，视图是不会改变的

```vue
<template>
  <div>
    <button @click="changeMsg">change</button>
    <div>{{ message }}</div>
  </div>
</template>
 
<script setup lang="ts">
import { Ref, shallowRef } from 'vue'
type Obj = {
  name: string
}
let message: Ref<Obj> = shallowRef({
  name: "小满"
})
const changeMsg = () => {
  message.value.name = '大满'
}
</script>
 
<style>
</style>
```

这样是可以被监听到的修改value

```js
import { Ref, shallowRef } from 'vue'
type Obj = {
  name: string
}
let message: Ref<Obj> = shallowRef({
  name: "小满"
})
 
const changeMsg = () => {
  message.value = { name: "大满" }
}
```

## triggerRef

> 强制更新页面DOM，这样也是可以改变值的
>
> 配合`shallowRef`使用，通知`DOM`更新
>
> 注：`ref`与`shallowRef`包裹的值如果同时被修改(不区分先后顺序)，`ref`会调动`shallowRef`通知`DOM`修改，因为`ref`相当于隐式调用了`triggerRef`，应当避免同时调用

```vue
<template>
  <div>
    <button @click="changeMsg">change</button>
    <div>{{ message }}</div>
  </div>
</template>
  
<script setup lang="ts">
import { Ref, shallowRef,triggerRef } from 'vue'
type Obj = {
  name: string
}
let message: Ref<Obj> = shallowRef({
  name: "小满"
})
const changeMsg = () => {
  message.value.name = '大满'
 triggerRef(message)
}
</script> 
 
<style>
</style>
```

## customRef

> 自定义ref 
>
> `customRef`是个工厂函数要求我们返回一个对象，并且实现`get`和`set`，在响应前可以做一系列的操作

```js
import { customRef } from 'vue';
function MyRef<T>(value: T) {
  return customRef((trank, trigger) => {
    return {
      get() {
        console.warn('get'); //立即触发一次，当更改值后会再触发一次
        trank();
        return value;
      },
      set(newVal: T) {
        console.warn('set'); //修改值触发，newVal为修改后的值，value为当前要响应的值
        value = newVal;
        trigger();
      },
    };
  });
}
```

# Reactive全家桶

## reactive

> 只接收复杂数据类型，不需要`.value`可直接改变值
>
> 不接收基础数据类型，基础数据类型交给[ref](#ref)处理
>
> 注：不可直接将数据赋值给`reactive`绑定的数组，会破坏其响应式，推荐以下写法

```js
import { reactive } from 'vue';

let data = reactive([]);
function fn(){
  setTimeout(()=>{
    const arr = [1, 2, 3]
    data = arr; //bad
    data.push(...arr); //good
  }, 1000)
}

```

## readonly

> 拷贝一份proxy对象将其设置为只读

```js
import { reactive, readonly } from "vue";
let data = reactive({
  name: "小明",
});
let a = readonly(data);
a.name = "小红"; //禁止修改，控制台警告
```

## shallowReactive

> 只能修改绑定的第一层的值
>
> 注：在`DOM`挂载之前依然可以修改深层次的值，挂载之后就不可修改

```js
import { shallowReactive } from "vue";
let data = shallowReactive({
  lyb: "冷弋白",
  obj: {
    age: 21,
  },
});
data.obj.age = 20; //dom挂在之前可修改
setTimeout(() => {
  data.lyb = "lengyibai"; //可修改
  data.obj.age = 18; //不可修改
});
```

# to系列全家桶

## toRef

> 将一个对象的属性抽出来并转成`ref`对象进行绑定，修改绑定值，原对象和绑定的`ref`也会跟着改变
>
> 让`toRef`绑定的属性值也具有响应式的前提是原对象也具有响应式

```js
import { toRef, reactive } from "vue";
const obj = reactive({
  lyb: "冷弋白",
});
const name = toRef(obj, "lyb");
setTimeout(() => {
  name.value = "lyb"; //不仅name会被修改并更新视图，reactiveb绑定的对象也会被修改，视图也会更新
});
```

## toRefs

> 接收一个对象，功能与`ref`一样
>
> 当`reactive`对象的属性比较多的时候就需要解构简化代码，但是解构会失去响应式。使用`toRefs`解构`reactive`对象，解构的属性仍具有响应式。

```js
import { toRefs, reactive } from "vue";
const obj = reactive({
  a: "张三",
  b: "李四",
});
const { a: A, b: B } = toRefs(obj);
setTimeout(() => {
  A.value = "zhangsan";
  B.value = "lisi";
});
```

## toRaw

> 通过`toRaw`方法拿到响应式数据的原始数据，对原始数据进行修改不会更新视图

```js
import { toRaw, reactive } from "vue";
const arr = reactive(["a", "b", "c"]);
setTimeout(() => {
  const lyb = toRaw(arr).splice(2, 1);
  console.log(lyb); //['c']
  console.log(arr); //toRaw(arr)只是引用地址，实际上 splice 也会改变响应式数据，此时为 ['a', 'b']，但不会更新视图
});
```

# 方法系列

## computed

注：computed计算出来的就是个ref对象，赋值给变量不用加.value

### 函数形式

> 计算属性就是当依赖的属性的值发生变化的时候，才会触发他的更改，如果依赖的值，不发生变化的时候，使用的是缓存中的属性值。

```js
import { computed, reactive, ref } from 'vue'
let price = ref(0)//$0
 
let m = computed<string>(()=>{
   return `$` + price.value
})
 
price.value = 500
```

### 对象形式

> `setter`写法，可修改计算属性

```vue
<template>
   <div>{{ mul }}</div>
   <div @click="mul = 100">click</div>
</template>
 
<script setup lang="ts">
import { computed, ref } from 'vue'
let price = ref<number | string>(1)//$0
let mul = computed({
   get: () => {
      return price.value
   },
   set: (value) => {
      price.value = 'set' + value
   }
})
</script>
 
<style>
</style>
```



## watch

> 监听data或者props数据的变化触发的函数，默认情况下我们的侦听器只会针对监听的数据本身的改变（内部发生的改变是不能侦听）



### 监听单个

```js
import { watch, ref } from "vue";

let a = ref(0);

watch(a, (newVal, oldVal) => {
  console.log("newVal：", newVal, "\n", "oldVal：", oldVal);
});

setTimeout(() => {
  a.value += 1;
}, 1000);
```

### 监听多个

> 使用数组形式监听多值，以数组形式返回

```js
import { watch, ref } from "vue";

let a = ref(0);
let b = ref(0);

watch([a, b], (newVal, oldVal) => {
  console.log("newVal：", newVal, "\n", "oldVal：", oldVal);
});

setTimeout(() => {
  a.value += 1;
}, 1000);
```

### 深度监听对象

> 只有使用`reactive`才会深度监听，不可使用`ref`
>
> PS：如果非要使用`ref`，则需要开启`deep`才能深度监听
>
> 注：目前无法获取旧值，旧值即新值

```js
import { reactive, watch } from "vue";

let p = reactive({
  user: {
    lyb: {
      name: "冷弋白",
    },
  },
});

watch(p, (newVal, oldVal) => {
  console.log("newVal：", newVal.user.lyb.name)
}, {
  immediate: true, //立即执行
});

setTimeout(() => {
  p.user.lyb.name = "lyb";
}, 1000);
```

### 监听对象指定属性

> 使用返回值的方式监听指定对象属性，且能获取到旧值

```js
import { reactive, watch } from "vue";

let user = reactive({
  lyb: {
    name: "冷弋白",
    age: "21",
  },
});

watch(
  () => user.lyb.name,
  (newVal) => {
    console.log("监听对象：", newVal);
  }
);

setTimeout(() => {
  user.lyb.age = "22"; //此时修改age并不会触发监听
  // user.lyb.name = "lyb";
}, 1000);
```

### 监听多个对象属性

> 将多个对象属性存放在数组返回值内进行监听，且能获取到旧值

```js
import { reactive, watch } from "vue";

let user = reactive({
  lyb: {
    name: "小明",
    age: 21,
    birth: 2000,
  },
});

watch(
  () => [user.lyb.age, user.lyb.birth],
  (newVal) => {
    console.log("newVal：", newVal);
  }
);

setTimeout(() => {
  user.lyb.age = 20;
  setTimeout(() => {
    user.lyb.birth = 2001;
  }, 1000);
}, 1000);
```

## watchEffect

> 立即执行传入的一个函数，同时响应式追踪其依赖，并在其依赖变更时重新运行该函数。
>
> 如果用到message 就只会[监听](https://so.csdn.net/so/search?q=监听&spm=1001.2101.3001.7020)message 就是用到几个监听几个 而且是非惰性 会默认调用一次
>
> `before`：无论处于哪个位置，始终第一执行此回调函数

```js
let message = ref<string>('')
let message2 = ref<string>('')
 watchEffect(() => {
    //console.log('message', message.value);
    console.log('message2', message2.value);
})
```

### 清除副作用

> 就是在值变化触发监听之前(默认监听不算)会调用一个函数可以处理你的逻辑例如防抖

```js
import { watchEffect, ref } from 'vue'
let message = ref<string>('')
let message2 = ref<string>('')
 watchEffect((oninvalidate) => {
    console.log('message', message.value);
    oninvalidate(()=>{
        console.log('先执行这里')
    })
    console.log('message2', message2.value);
})
```

### 停止监听

> 调用监听函数即可取消

```js
import { reactive, watchEffect } from "vue";
let user = reactive({
  lyb: {
    name: "冷弋白",
  },
});

const stop = watchEffect((before) => {
  before(() => {
    console.warn("before");
  });
  console.log(user.lyb.name);
});

setTimeout(() => {
  user.lyb.name = "lyb";
  setTimeout(() => {
    console.warn("关闭监听");
    stop(); //关闭监听，并调用一次before，关闭后监听器下一行内容将不会修改
    setTimeout(() => {
      user.lyb.name = "lengyibai";
    }, 1000);
  }, 1000);
}, 1000);
```

### 获取DOM元素

> 组件更新后执行，`flush: "post"`

|          | pre                | sync                 | post               |
| :------- | :----------------- | :------------------- | :----------------- |
| 更新时机 | 组件**更新前**执行 | 强制效果始终同步触发 | 组件**更新后**执行 |

```js
import { watchEffect } from "vue";

watchEffect(
  () => {
    const app = document.querySelector(".app");
    console.log(app);
  },
  { flush: "post" }
);
```



# 父子组件传参

## 父传子

**父组件传数据**

```vue
<template>
    <div class="layout">
        <Menu v-bind:data="data"  title="我是标题"></Menu>
        <div class="layout-right">
            <Header></Header>
            <Content></Content>
        </div>
    </div>
</template>
 
<script setup lang="ts">
import Menu from './Menu/index.vue'
import Header from './Header/index.vue'
import Content from './Content/index.vue'
import { reactive } from 'vue';
 
const data = reactive<number[]>([1, 2, 3])
</script>
```

**子组件接受数据**

注：子组件通过`defineProps`来接受，有setup`defineProps`是无须引入的直接使用即可，如这是TS特有的,可以使用传递字面量类型的纯类型语法做为参数

```vue
<template>
    <div class="menu">
        菜单区域 {{ title }}
        <div>{{ data }}</div>
    </div>
</template>
 
<script setup lang="ts">
type Props = {
    title:string,
    data:number[]
}
defineProps<Props>()
</script>
```

**ts特有的默认值方式**

withDefaults是个函数也是无须引入开箱即用接受一个props函数第二个参数是一个对象设置默认值

```tsx
type Props = {
    title?: string,
    data?: number[]    // ?代表参数可传可不传
}
withDefaults(defineProps<Props>(), {
    title: "张三",
    data: () => [1, 2, 3]  // 复杂数据类型默认值要用函数return出去
})
```

如果你使用的不是TS

```js
defineProps({
    title:{
        default:"",
        type:string
    },
    data:Array
})
```

## 子传父

**子组件通过defineEmits派发事件**

```vue
<template>
    <div class="menu">
        <button @click="clickTap">派发给父组件</button>
    </div>
</template>
 
<script setup lang="ts">
import { reactive } from 'vue'
const list = reactive<number[]>([4, 5, 6])
 
const emit = defineEmits(['on-click'])
const clickTap = () => {
    emit('on-click', list)
}
</script>
```

**父组件接受子组件的事件**

```vue
<template>
    <div class="layout">
        <Menu @on-click="getList"></Menu>
        <div class="layout-right">
            <Header></Header>
            <Content></Content>
        </div>
    </div>
</template>
 
<script setup lang="ts">
import Menu from './Menu/index.vue'
import Header from './Header/index.vue'
import Content from './Content/index.vue'
import { reactive } from 'vue';
 
const data = reactive<number[]>([1, 2, 3])
 
const getList = (list: number[]) => {
    console.log(list,'父组件接受子组件');
}
</script>
```

**子组件通过defineExpose暴露给父组件内部属性**

我们从父组件获取子组件实例通过ref

```vue
<Menu ref="menus"></Menu>
const menus = ref(null)
```

然后打印menus.value 发现没有任何属性

这时候父组件想要读到子组件的属性可以通过 defineExpose暴露

```ts
const list = reactive<number[]>([4, 5, 6])
 
defineExpose({
    list
})
```

这样父组件就可以读到了





# 兄弟组件传参和Bus

## 借助父组件传参

例如父组件为App 子组件为A 和 B他两个是同级的

```vue
<template>
    <div>
        <A @on-click="getFalg"></A>
        <B :flag="Flag"></B>
    </div>
</template>
    
<script setup lang='ts'>
import A from './components/A.vue'
import B from './components/B.vue'
import { ref } from 'vue'
let Flag = ref<boolean>(false)
const getFalg = (flag: boolean) => {
   Flag.value = flag;
}
</script>
    
<style>
</style>
```

A 组件派发事件通过App.vue 接受A组件派发的事件然后在Props 传给B组件 也是可以实现的

缺点就是比较麻烦 ，无法直接通信，只能充当桥梁

## Event Bus

我们在Vue2 可以使用$emit 传递 $on监听 emit传递过来的事件

这个原理其实是运用了JS设计模式之发布订阅模式

我写了一个简易版

```ts
 
type BusClass<T> = {
    emit: (name: T) => void
    on: (name: T, callback: Function) => void
}
type BusParams = string | number | symbol 
type List = {
    [key: BusParams]: Array<Function>
}
class Bus<T extends BusParams> implements BusClass<T> {
    list: List
    constructor() {
        this.list = {}
    }
    emit(name: T, ...args: Array<any>) {
        let eventName: Array<Function> = this.list[name]
        eventName.forEach(ev => {
            ev.apply(this, args)
        })
    }
    on(name: T, callback: Function) {
        let fn: Array<Function> = this.list[name] || [];
        fn.push(callback)
        this.list[name] = fn
    }
}
 
export default new Bus<number>()
```

然后挂载到Vue config 全局就可以使用啦

## Mitt

在vue3中$on，$off 和 $once 实例方法已被移除，组件实例不再实现事件触发接口，因此大家熟悉的EventBus便无法使用了。然而我们习惯了使用EventBus，对于这种情况我们可以使用Mitt库（其实就是我们视频中讲的发布订阅模式的设计）

1. 安装

```
npm install mitt -S
```

2. main.ts 初始化

全局总线，vue 入口文件 main.js 中挂载全局属性

```ts
import { createApp } from 'vue'
import App from './App.vue'
import mitt from 'mitt' 

const Mit = mitt() // 初始化

//TypeScript注册
// 由于必须要拓展ComponentCustomProperties类型才能获得类型提示
declare module "vue" {
    export interface ComponentCustomProperties {
        $Bus: typeof Mit
    }
}

const app = createApp(App)

//Vue3挂载全局API
app.config.globalProperties.$Bus = Mit

app.mount('#app')
```

3. 使用方法通过emit派发，on 方法添加事件，off 方法移除，clear清空所有 

A组件派发（emit）

```vue
<template>
    <div>
        <h1>我是A</h1>
        <button @click="emit1">emit1</button>
        <button @click="emit2">emit2</button>
    </div>
</template>

<script setup lang='ts'>
import { getCurrentInstance } from 'vue'
const instance = getCurrentInstance();
const emit1 = () => {
    instance?.proxy?.$Bus.emit('on-num', 100)
}
const emit2 = () => {
    instance?.proxy?.$Bus.emit('*****', 500)
}
</script>

<style>
</style>
```

B组件监听（on）

```vue
<template>
    <div>
        <h1>我是B</h1>
    </div>
</template>

<script setup lang='ts'>
import { getCurrentInstance } from 'vue'
const instance = getCurrentInstance()
instance?.proxy?.$Bus.on('on-num', (num) => {
    console.log(num,'===========>B')
})
</script>

<style>
</style>
```

监听所有事件（ on("*") ）

```ts
instance?.proxy?.$Bus.on('*',(type,num)=>{
    console.log(type,num,'===========>B')
})
移除监听事件（off）
```

移除监听事件（off）

```ts
const Fn = (num: any) => {
    console.log(num, '===========>B')
}
instance?.proxy?.$Bus.on('on-num',Fn)//listen
instance?.proxy?.$Bus.off('on-num',Fn)//unListen
```

清空所有监听（clear）

```ts
instance?.proxy?.$Bus.all.clear()
```



# 组件

## 全局组件

在main.ts 引入我们的组件跟随在createApp(App) 后面 切记不能放到mount 后面这是一个链式调用用

其次调用 component 第一个参数组件名称 第二个参数组件实例

```ts
import { createApp } from 'vue'
import App from './App.vue'
import './assets/css/reset/index.less'
import Card from './components/Card/index.vue'
 
 
createApp(App).component('Card',Card).mount('#app')
```

使用方法

直接在其他vue页面 立即使用即可 无需引入

```vue
<template>
 <Card></Card>
</template>
```

## 局部组件

> 在一个组件内（A）通过import 去引入别的组件(B) 称之为局部组件

```vue
<template>
  <div class="wraps">
    <layout-menu :flag="flag" @on-click="getMenu" @on-toogle="getMenuItem" :data="menuList" class="wraps-left"></layout-menu>
    <div class="wraps-right">
      <layout-header> </layout-header>
      <layout-main class="wraps-right-main"></layout-main>
    </div>
  </div>
</template>
 
<script setup lang="ts">
import { reactive,ref } from "vue";
import layoutHeader from "./Header.vue";
import layoutMenu from "./Menu.vue";
import layoutMain from "./Content.vue";
```

## 递归组件

原理跟我们写js递归是一样的 自己调用自己 通过一个条件来结束递归 否则导致内存泄漏

案例递归树

**在父组件配置数据结构 数组对象格式 传给子组件**

```ts
type TreeList = {
  name: string;
  icon?: string;
  children?: TreeList[] | [];
};
const data = reactive<TreeList[]>([
  {
    name: "no.1",
    children: [
      {
        name: "no.1-1",
        children: [
          {
            name: "no.1-1-1",
          },
        ],
      },
    ],
  },
  {
    name: "no.2",
    children: [
      {
        name: "no.2-1",
      },
    ],
  },
  {
    name: "no.3",
  },
]);
```

**子组件接收值**

TreeItem 其实就是当前组件 通过import 把自身又引入了一遍 如果他没有children 了就结束

```vue
<template>
<div style="margin-left:10px;" class="tree">
    <div :key="index" v-for="(item,index) in data">
      <div @click='clickItem(item)'>{{item.name}}
    </div>
    <TreeItem @on-click='clickItem' v-if='item?.children?.length' 
:data="item.children"></TreeItem>
  </div>
  </div>
</template> 

<script>
type TreeList = {
  name: string;
  icon?: string;
  children?: TreeList[] | [];
};
 
type Props<T> = {
  data?: T[] | [];
};
 
defineProps<Props<TreeList>>();
 
const clickItem = (item: TreeList) => {
  console.log(item)
}
</script>

// 子组件增加一个script 定义组件名称为了 递归用 
<script lang="ts">
export default {
  name:"TreeItem"
}
</script>
```

## 动态组件

​	什么是动态组件 就是：让多个组件使用同一个挂载点，并动态切换，这就是动态组件。

在挂载点使用component标签，然后使用v-bind:is=”组件”

用法如下

引入组件

```vue
import A from './A.vue'
import B from './B.vue'
<component :is="A"></component>
```

通过is 切换 A B 组件

**使用场景**

tab切换居多

**注意事项** 

1.在Vue2 的时候is 是通过组件名称切换的 在Vue3 setup 是通过组件实例切换的

2.如果你把组件实例放到Reactive Vue会给你一个警告runtime-core.esm-bundler.js:38 [Vue warn]: Vue received a Component which was made a reactive object. This can lead to unnecessary performance overhead, and should be avoided by marking the component with `markRaw` or using `shallowRef` instead of `ref`. 
Component that was made reactive: 

这是因为reactive 会进行proxy 代理 而我们组件代理之后毫无用处 节省性能开销 推荐我们使用shallowRef 或者  markRaw 跳过proxy 代理

修改如下

```ts
<template>
    <div class="content">
        <div class="tab"
         :class="current.comName==item.comName?'bg-red':''"
          @click="selectactive(item)" v-for="item  in data" 
         :key="item.name">{{item.name}}</div>
    </div>
      <component :is="current.comName" ></component>

</template>

<script setup lang="ts">
import  {reactive ,markRaw} from 'vue'
import  A from './A.vue'
import  B from './B.vue'
import  C from './C.vue' 
type Tabs={
    name:string,
    comName:any
}
const  data=reactive<Tabs[]>([
    {
        name:'我是A组件',
        comName:markRaw(A)
    },
       {
        name:'我是B组件', 
        comName:markRaw(B)

    },
       {
        name:'我是C组件',
        comName:markRaw(C) 
    },
])
// 设置默认选中
let  current=reactive({
    comName:data[0].comName
})
// 点击切换事件
const selectactive=(item:Tabs)=>{
current.comName=item.comName;
}
</script>

<style scoped lang="less">
.bg-red{
    background: red;
}
.content{
    flex: 1;
     display: flex;
    margin: 20px;
    border-bottom: 1px solid #ccc;
    overflow: auto;
    &-items{
        padding:20px;border-bottom: 1px solid #ffffff;
    }
     .tab{
         height: 50px;
        border:1px solid red;
     }
     component{
         height: 30px;;
     }
}
</style>

```

## 异步组件

在大型应用中，我们可能需要将应用分割成小一些的代码块 并且减少主包的体积，这时候就可以使用异步组件

### 顶层 await

在setup语法糖里面可以使用顶层 `await`。代码会被编译成 `async setup()`

```ts
<script setup>
const post = await fetch(`/api/post/1`).then(r => r.json())
</script>
```

父组件引用子组件 通过defineAsyncComponent加载异步配合import 函数模式便可以分包

```ts
<script setup lang="ts">
import { reactive, ref, markRaw, toRaw, defineAsyncComponent } from 'vue'
 
const Dialog = defineAsyncComponent(() => import('../../components/Dialog/index.vue'))
```

### suspense

`<suspense> `组件有两个插槽。它们都只接收一个直接子节点。`default`插槽里的节点会尽可能展示出来。如果不能，则展示`fallback`插槽里的节点。

```html
<Suspense>
  	 <template #default>
          <Dialog>
              <template #default>
                  <div>我在哪儿</div>
              </template>
          </Dialog>
     </template>
 
	<template #fallback>
     	<div>loading...</div>
	</template>
</Suspense>
```


# 插槽slot

## 匿名插槽

1.在子组件放置一个插槽

```vue
<template>
    <div>
       <slot></slot>
    </div>
</template>
```

父组件使用插槽

在父组件给这个插槽填充内容

```vue
<Dialog>
	<template v-slot>
     	<div>插入内容</div>
    </template>
</Dialog>
```
## 具名插槽

具名插槽其实就是给插槽取个名字。一个子组件可以放多个插槽，而且可以放在不同的地方，而父组件填充内容时，可以根据这个名字把内容填充到对应插槽中

```vue
<div>
    <slot name="header"></slot>
    <slot></slot>
    <slot name="footer"></slot>
</div>
```
父组件使用需对应名称

```vue
<Dialog>
   <template v-slot:header>
      <div>1</div>
   </template>
   <template v-slot>
      <div>2</div>
   </template>
   <template v-slot:footer>
      <div>3</div>
   </template>
</Dialog>

```
插槽简写

```vue
<Dialog>
   <template #header>
       <div>1</div>
   </template>
   <template #default>
       <div>2</div>
   </template>
   <template #footer>
       <div>3</div>
   </template>
</Dialog>
```
## 作用域插槽

在子组件动态绑定参数 派发给父组件的slot去使用

```vue
<div>
    <slot name="header"></slot>
    <div>
        <div v-for="item in 100">
            <slot :data="item"></slot>
        </div>
    </div>
 
    <slot name="footer"></slot>
</div>
```
通过结构方式取值

```vue
<Dialog>
    <template #header>
        <div>1</div>
    </template>
    <template #default="{ data }">
        <div>{{ data }}</div>
    </template>
    <template #footer>
        <div>3</div>
    </template>
</Dialog>
```
## 动态插槽

插槽可以是一个变量名

```vue
<Dialog>
   <template #[name]>
      <div>
         内容
      </div>
   </template>
</Dialog>
const name = ref('header')
```


# Teleport传送组件

> 1. Teleport Vue 3.0新特性之一。
>
> 2. Teleport 是一种能够将我们的模板渲染至指定DOM节点，不受父级style、v-show等属性影响，但data、prop数据依旧能够共用的技术；类似于 React 的 Portal。
>
> 3. 主要解决的问题 因为Teleport节点挂载在其他指定的DOM节点下，完全不受父级style样式影响

使用方法

通过to 属性 插入指定元素位置 to="body" 便可以将`Teleport` 内容传送到指定位置

```vue
<Teleport to="body">
    <Loading></Loading>
</Teleport>
```

也可以自定义传送位置 支持 class id等 选择器

```vue
---index.html---
<div id="app"></div>
    <div class="modal"></div>
  
---Content.vue---
<Teleport to=".modal">
   <Loading></Loading>
</Teleport>
```

也可以使用多个

```vue
<Teleport to=".modal1">
     <Loading></Loading>
</Teleport>

<Teleport to=".modal2">
     <Loading></Loading>
</Teleport>
```



# keep-alive缓存组件

> 有时候我们不希望组件被重新渲染影响使用体验；或者处于性能考虑，避免多次重复渲染降低性能。而是希望组件可以缓存下来,维持当前的状态。这时候就需要用到`keep-alive`组件。

开启keep-alive 生命周期的变化

- 初次进入时： onMounted> onActivated
- 退出后触发 deactivated
- 再次进入：
- 只会触发 onActivated
- 事件挂载的方法等，只执行一次的放在 onMounted中；组件每次进去执行的方法放在 onActivated中

```vue
<!-- 基本 -->
<keep-alive>
  <component :is="view"></component>
</keep-alive>
 
<!-- 多个条件判断的子组件 -->
<keep-alive>
  <comp-a v-if="a > 1"></comp-a>
  <comp-b v-else></comp-b>
</keep-alive>
 
<!-- 和 `<transition>` 一起使用 -->
<transition>
  <keep-alive>
    <component :is="view"></component>
  </keep-alive>
</transition>
```

 **`include` 和 `exclude`**

> include 和 exclude prop 允许组件有条件地缓存。二者都可以用逗号分隔字符串、正则表达式或一个数组来表示：

```vue
 <keep-alive :include="" :exclude="" :max=""></keep-alive>
```

**`max`**

> 限制前多少个组件缓存

```vue
<keep-alive :max="10">
  <component :is="view"></component>
</keep-alive>
```



# transition动画组件

> Vue 提供了 transition 的封装组件，在下列情形中，可以给任何元素和组件添加进入/离开过渡:
>
> - 条件渲染 (使用 v-if)
> - 条件展示 (使用 v-show)
> - 动态组件
> - 组件根节点
>
> 自定义 transition 过度效果，你需要对transition组件的name属性自定义。并在css中写入对应的样式

## 过渡的类名

在进入/离开的过渡中，会有 6 个 class 切换。

1. v-enter-from：定义进入过渡的开始状态。在元素被插入之前生效，在元素被插入之后的下一帧移除。

2. v-enter-active：定义进入过渡生效时的状态。在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡/动画完成之后移除。这个类可以被用来定义进入过渡的过程时间，延迟和曲线函数。

3. v-enter-to：定义进入过渡的结束状态。在元素被插入之后下一帧生效 (与此同时 v-enter-from 被移除)，在过渡/动画完成之后移除。

4. v-leave-from：定义离开过渡的开始状态。在离开过渡被触发时立刻生效，下一帧被移除。

5. v-leave-active：定义离开过渡生效时的状态。在整个离开过渡的阶段中应用，在离开过渡被触发时立刻生效，在过渡/动画完成之后移除。这个类可以被用来定义离开过渡的过程时间，延迟和曲线函数。

6. v-leave-to：离开过渡的结束状态。在离开过渡被触发之后下一帧生效 (与此同时 v-leave-from 被移除)，在过渡/动画完成之后移除。

```vue
<button @click='flag = !flag'>切换</button>
   <transition name='fade'>
      <div v-if='flag' class="box"></div>
   </transition>
<style>
//开始过度
.fade-enter-from{
   background:red;
   width:0px;
   height:0px;
   transform:rotate(360deg)
}
//开始过度了
.fade-enter-active{
  transition: all 2.5s linear;
}
//过度完成
.fade-enter-to{
   background:yellow;
   width:200px;
   height:200px;
}
//离开的过度
.fade-leave-from{
  width:200px;
  height:200px;
  transform:rotate(360deg)
}
//离开中过度
.fade-leave-active{
  transition: all 1s linear;
}
//离开完成
.fade-leave-to{
  width:0px;
   height:0px;
}
</style>
```

## 自定义过渡 class 类名

trasnsition props

- enter-from-class

- enter-active-class
- enter-to-class
- leave-from-class
- leave-active-class
- leave-to-class

**通过自定义class结合css动画库animate.css**

安装库 npm install animate.css -S

引入 import 'animate.css'

使用方法

官方文档 [Animate.css | A cross-browser library of CSS animations.](https://animate.style/)

```vue
<transition
   leave-active-class="animate__animated animate__bounceInLeft"
   enter-active-class="animate__animated animate__bounceInRight"
>
  <div v-if="flag" class="box"></div>
</transition>
```

**自定义过度时间**/单位毫秒

你也可以分别指定进入和离开的持续时间：

```vue
<transition :duration="1000">...</transition>
 
 
<transition :duration="{ enter: 500, leave: 800 }">...</transition>
```

## transition生命周期

```ts
  @before-enter="EnterForm" 
  @enter="EnterActive"
  @after-enter="EnterTo"
  @enter-cancelled="EnterCancel"
  @before-leave="LeaveFrom"
  @leave="Leave"
  @after-leave="LeaveTo"
  @leave-cancelled="LeaveCancel"
```

当只用 JavaScript 过渡的时候，在 `enter` 和 `leave` 钩子中必须使用 `done` 进行回调

结合gsap 动画库使用 [GreenSock](https://greensock.com/)

安装库 npm install gsap -S

引入 import gsap from 'gsap'

```ts
const EnterForm = (el: Element) => {
    gsap.set(el,{
        width:0,
        height:0
    })
}
const EnterActive = (el: Element,done:gasp.Callback) => {
    gsap.set(el,{
        width:200,
        height:200,
        onComplete: done()
    })
}
const AfterEnter = (el:gasp.Callback) => {
     gsap.set(el,{
        width:0,
        height:0,
        onComplete: done()
    })
}
```

## appear

> 通过这个属性可以设置节点初始过渡，就是页面加载完成就开始动画对应的三个状态

```ts
appear-active-class=""
appear-from-class=""
appear-to-class=""
appear
```



# transition-group过度列表

> 那么怎么同时渲染整个列表，比如使用 v-for？在这种场景下，我们会使用` <transition-group>`组件。在我们深入例子之前，先了解关于这个组件的几个特点：
>
> - 默认情况下，它不会渲染一个包裹元素，但是你可以通过 tag attribute 指定渲染一个元素。
> - 过渡模式不可用，因为我们不再相互切换特有的元素。
> - 内部元素总是需要提供唯一的 key attribute 值。
> - CSS 过渡的类将会应用在内部的元素中，而不是这个组/容器本身。

```vue
<transition-group>
     <div style="margin: 10px;" :key="item" v-for="item in list">{{ item }</div>
</transition-group>

<script>
const list = reactive<number[]>([1, 2, 4, 5, 6, 7, 8, 9])
const Push = () => {
    list.push(123)
}
const Pop = () => {
    list.pop()
}
</script>
```

## 列表的移动过渡

<transition-group> 组件还有一个特殊之处。除了进入和离开，它还可以为定位的改变添加动画。只需了解新增的 v-move 类就可以使用这个新功能，它会应用在元素改变定位的过程中。像之前的类名一样，它的前缀可以通过 name attribute 来自定义，也可以通过 move-class attribute 手动设置

下面代码很酷炫

```vue
<template>
    <div>
        <button @click="shuffle">Shuffle</button>
        <transition-group class="wraps" name="mmm" tag="ul">
            <li class="cell" v-for="item in items" :key="item.id">{{ item.number }}</li>
        </transition-group>
    </div>
</template>
  
<script setup  lang='ts'>
import _ from 'lodash'
import { ref } from 'vue'
let items = ref(Array.apply(null, { length: 81 } as number[]).map((_, index) => {
    return {
        id: index,
        number: (index % 9) + 1
    }
}))
const shuffle = () => {
    items.value = _.shuffle(items.value)
}
</script>
  
<style scoped lang="less">
.wraps {
    display: flex;
    flex-wrap: wrap;
    width: calc(25px * 10 + 9px);
    .cell {
        width: 25px;
        height: 25px;
        border: 1px solid #ccc;
        list-style-type: none;
        display: flex;
        justify-content: center;
        align-items: center;
    }
}
 
.mmm-move {
    transition: transform 0.8s ease;
}
</style>
```

## 状态过渡

Vue 也同样可以给数字 Svg 背景颜色等添加过度动画 今天演示数字变化

```vue
<template>
    <div>
        <input step="20" v-model="num.current" type="number" />
        <div>{{ num.tweenedNumber.toFixed(0) }}</div>
    </div>
</template>
    
<script setup lang='ts'>
import { reactive, watch } from 'vue'
import gsap from 'gsap'
const num = reactive({
    tweenedNumber: 0,
    current:0
})
 
watch(()=>num.current, (newVal) => {
    gsap.to(num, {
        duration: 1,
        tweenedNumber: newVal
    })
})
 
</script>
    
<style>
</style>
```

# 依赖注入Provide/Inject

通常，当我们需要从父组件向子组件传递数据时，我们使用 [props](https://v3.cn.vuejs.org/guide/component-props.html)。想象一下这样的结构：有一些[深度](https://so.csdn.net/so/search?q=深度&spm=1001.2101.3001.7020)嵌套的组件，而深层的子组件只需要父组件的部分内容。在这种情况下，如果仍然将 prop 沿着组件链逐级传递下去，可能会很麻烦。

当父组件有很多数据需要分发给其子代孙代组件的时候， 就可以使用provide和inject。

> `provide`可以在祖先组件中指定我们想要**提供**给后代组件的数据或方法，而在任何后代组件中，我们都可以使用`inject`来接收 `provide`**提供**的数据或方法。 
>
> 注：你如果传递普通的值，是不具有响应式的。需要通过`ref ` `reactive`添加响应式

**父组件传递数据**

```vue
<template>
    <div class="App">
        <button>我是App</button>
        <A></A>
    </div>
</template>
    
<script setup lang='ts'>
import { provide, ref } from 'vue'
import A from './components/A.vue'
let flag = ref<number>(1)
provide('flag', flag)
</script>
    
<style>
.App {
    background: blue;
    color: #fff;
}
</style>
```

**子组件接收**

```vue
<template>
    <div style="background-color: green;">
        我是B
        <button @click="change">change falg</button>
        <div>{{ flag }}</div>
    </div>
</template>
    
<script setup lang='ts'>
import { inject, Ref, ref } from 'vue'
 
const flag = inject<Ref<number>>('flag', ref(1))
 
const change = () => {
    flag.value = 2
}
</script>
    
<style>
</style>
```



# v-model

> 在Vue3 v-model 是破坏性更新的
>
> v-model在组件里面也是很重要的
>
> v-model 其实是一个语法糖 通过props 和 emit组合而成的

- prop：`value` -> `modelValue`；
- 事件：`input` -> `update:modelValue`；
- `v-bind` 的 `.sync` 修饰符和组件的 `model` 选项已移除
- 新增 支持多个v-model
- 新增 支持自定义修饰符

## 绑定单个案例

**子组件**

```vue
<template>
     <div v-if='propData.modelValue ' class="dialog">
         <div class="dialog-header">
             <div>标题</div><div @click="close">x</div>
         </div>
         <div class="dialog-content">
            内容
         </div>
         
     </div>
</template>
 
<script setup lang='ts'>
 
type Props = {
   modelValue:boolean
}
 
const propData = defineProps<Props>()
 
const emit = defineEmits(['update:modelValue'])
 
const close = () => {
     emit('update:modelValue',false)
}
 
</script>
 
<style lang='less'>
.dialog{
    width: 300px;
    height: 300px;
    border: 1px solid #ccc;
    position: fixed;
    left:50%;
    top:50%;
    transform: translate(-50%,-50%);
    &-header{
        border-bottom: 1px solid #ccc;
        display: flex;
        justify-content: space-between;
        padding: 10px;
    }
    &-content{
        padding: 10px;
    }
}
</style>
```

**父组件**

```vue
<template>
  <button @click="show = !show">开关{{show}}</button>
  <Dialog v-model="show"></Dialog>
</template>
 
<script setup lang='ts'>
import Dialog from "./components/Dialog/index.vue";
import {ref} from 'vue'
const show = ref(false)
</script>
 
<style>
</style>
```

## 绑定多个案例

**子组件**

```vue
<template>
     <div v-if='modelValue ' class="dialog">
         <div class="dialog-header">
             <div>标题---{{title}}</div><div @click="close">x</div>
         </div>
         <div class="dialog-content">
            内容
         </div>
         
     </div>
</template>
 
<script setup lang='ts'>
 
type Props = {
   modelValue:boolean,
   title:string
}
 
const propData = defineProps<Props>()
 
const emit = defineEmits(['update:modelValue','update:title'])
 
const close = () => {
     emit('update:modelValue',false)
     emit('update:title','我要改变')
}
 
</script>
 
<style lang='less'>
.dialog{
    width: 300px;
    height: 300px;
    border: 1px solid #ccc;
    position: fixed;
    left:50%;
    top:50%;
    transform: translate(-50%,-50%);
    &-header{
        border-bottom: 1px solid #ccc;
        display: flex;
        justify-content: space-between;
        padding: 10px;
    }
    &-content{
        padding: 10px;
    }
}
</style>
```

**父组件**

```vue
<template>
  <button @click="show = !show">开关{{show}} ----- {{title}}</button>
  <Dialog v-model:title='title' v-model="show"></Dialog>
</template>
 
<script setup lang='ts'>
import Dialog from "./components/Dialog/index.vue";
import {ref} from 'vue'
const show = ref(false)
const title = ref('我是标题')
</script>
 
<style>
</style>
```

## 自定义修饰符

添加到组件 `v-model` 的修饰符将通过 `modelModifiers` prop 提供给组件。在下面的示例中，我们创建了一个组件，其中包含默认为空对象的 `modelModifiers` prop

```vue
<script setup lang='ts'>
 
type Props = {
    modelValue: boolean,
    title?: string,
    modelModifiers?: {
        default: () => {}
    }
    titleModifiers?: {
        default: () => {}
    }
 
}
 
const propData = defineProps<Props>()
 
const emit = defineEmits(['update:modelValue', 'update:title'])
 
const close = () => {
    console.log(propData.modelModifiers);
 
    emit('update:modelValue', false)
    emit('update:title', '我要改变')
}
```



# 自定义指令directive

## Vue3指令的钩子函数

- **created** 元素初始化的时候
- **beforeMount** 指令绑定到元素后调用 只调用一次
- **mounted** 元素插入父级dom调用
- **beforeUpdate** 元素被更新之前调用
- **updated** 这个update周期被移除 改用**updated**
- **beforeUnmount** 在元素被移除前调用
- **unmounted** 指令被移除后调用 只调用一次

Vue2 指令 bind inserted update componentUpdated unbind

## 在setup内定义局部指令

但这里有一个需要注意的限制：必须以 `vName：Directive` 的形式来命名本地自定义指令，以使得它们可以直接在模板中使用。

```vue
<template>
  <button @click="show = !show">开关{{show}} ----- {{title}}</button>
  <Dialog  v-move="{background:'green',flag:show}"></Dialog>
</template>
 
<script setup lang='ts'>
import { Directive } from "vue";
const vMoveDirective: Directive = {
  created: () => {
    console.log("初始化====>");
  },
  beforeMount(...args: Array<any>) {
    // 在元素上做些操作
    console.log("初始化一次=======>");
  },
  mounted(el: any, dir: DirectiveBinding<Value>) {
    el.style.background = dir.value.background;
    console.log("初始化========>");
  },
  beforeUpdate() {
    console.log("更新之前");
  },
  updated() {
    console.log("更新结束");
  },
  beforeUnmount(...args: Array<any>) {
    console.log(args);
    console.log("======>卸载之前");
  },
  unmounted(...args: Array<any>) {
    console.log(args);
    console.log("======>卸载完成");
  },
};
</script>
```

## 生命周期钩子参数详解

第一个 el 当前绑定的DOM 元素

第二个 binding

- `instance`：使用指令的组件实例。

- `value`：传递给指令的值。例如，在 `v-my-directive="1 + 1"` 中，该值为 `2`。

- `oldValue`：先前的值，仅在 `beforeUpdate` 和 `updated` 中可用。无论值是否有更改都可用。

- `arg`：传递给指令的参数(如果有的话)。例如在 `v-my-directive:foo` 中，arg 为 `"foo"`。

- `modifiers`：包含修饰符(如果有的话) 的对象。例如在 `v-my-directive.foo.bar` 中，修饰符对象为 `{foo: true，bar: true}`。

- `dir`：一个对象，在注册指令时作为参数传递。例如，在以下指令中

  第三个 当前元素的虚拟DOM 也就是Vnode

  第四个 prevNode 上一个虚拟节点，仅在 `beforeUpdate` 和 `updated` 钩子中可用

## 函数简写

你可能想在 `mounted` 和 `updated` 时触发相同行为，而不关心其他的钩子函数。那么你可以通过将这个函数模式实现

```vue
<template>
   <div>
      <input v-model="value" type="text" />
      <A v-move="{ background: value }"></A>
   </div>
</template>
   
<script setup lang='ts'>
import A from './components/A.vue'
import { ref, Directive, DirectiveBinding } from 'vue'
let value = ref<string>('')
type Dir = {
   background: string
}
const vMove: Directive = (el, binding: DirectiveBinding<Dir>) => {
   el.style.background = binding.value.background
}
</script>
 
<style>
</style>
```

## 案例按钮权限

```vue
<template>
   <div class="btns">
       <button v-has-show="'shop:create'">创建</button>
 
       <button v-has-show="'shop:edit'">编辑</button>
 
       <button v-has-show="'shop:delete'">删除</button>
   </div>
</template>
 
<script setup lang='ts'>
import { ref, reactive,  } from 'vue'
import type {Directive} from 'vue'
//permission
localStorage.setItem('userId','xiaoman-zs')
 
//mock后台返回的数据
const permission = [
    'xiaoman-zs:shop:edit',
    'xiaoman-zs:shop:create',
    'xiaoman-zs:shop:delete'
]
const userId = localStorage.getItem('userId') as string
const vHasShow:Directive<HTMLElement,string> = (el,bingding) => {
   if(!permission.includes(userId+':'+ bingding.value)){
       el.style.display = 'none'
   }
}
 
</script>
 
<style scoped lang='less'>
.btns{
    button{
        margin: 10px;
    }
}
</style>
```

## 案例自定义拖拽指令 

```vue
<template>
  <div v-move class="box">
    <div class="header"></div>
    <div>
      内容
    </div>
  </div>
</template>
 
<script setup lang='ts'>
import { Directive } from "vue";
const vMove: Directive = {
  mounted(el: HTMLElement) {
    let moveEl = el.firstElementChild as HTMLElement;
    const mouseDown = (e: MouseEvent) => {
      //鼠标点击物体那一刻相对于物体左侧边框的距离=点击时的位置相对于浏览器最左边的距离-物体左边框相对于浏览器最左边的距离
      console.log(e.clientX, e.clientY, "-----起始", el.offsetLeft);
      let X = e.clientX - el.offsetLeft;
      let Y = e.clientY - el.offsetTop;
      const move = (e: MouseEvent) => {
        el.style.left = e.clientX - X + "px";
        el.style.top = e.clientY - Y + "px";
        console.log(e.clientX, e.clientY, "---改变");
      };
      document.addEventListener("mousemove", move);
      document.addEventListener("mouseup", () => {
        document.removeEventListener("mousemove", move);
      });
    };
    moveEl.addEventListener("mousedown", mouseDown);
  },
};
</script>
 
<style lang='less'>
.box {
  position: fixed;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  width: 200px;
  height: 200px;
  border: 1px solid #ccc;
  .header {
    height: 20px;
    background: black;
    cursor: move;
  }
}
</style>
```

## 案例图片懒加载

```vue
<template>
    <div>
        <div v-for="item in arr">
            <img height="500" :data-index="item" v-lazy="item" width="360">
        </div>
    </div>
</template>
 
<script setup lang='ts'>
import { ref, reactive } from 'vue'
import type { Directive } from 'vue'
    
const images: Record<string, { default: string }> = import.meta.glob('./assets/images/*.*',{eager:true})
let arr = Object.values(images).map(v => v.default)
 
let vLazy: Directive<HTMLImageElement, string> = async (el, binding) => {
    let url = await import('./assets/vue.svg')
    el.src = url.default;
    let observer = new IntersectionObserver((entries) => {
        console.log(entries[0], el)
        if (entries[0].intersectionRatio > 0 && entries[0].isIntersecting) {
            setTimeout(() => {
                el.src = binding.value;
                observer.unobserve(el)
            }, 2000)
        }
    })
    observer.observe(el)
}
 
</script>
 
<style scoped lang='less'></style>
```



# 自定义Hooks

> 主要用来处理复用代码逻辑的一些封装
>
> 这个在vue2 就已经有一个东西是Mixins
>
> mixins就是将这些多个相同的逻辑抽离出来，各个组件只需要引入mixins，就能实现一次写代码，多组件受益的效果。
>
> - 弊端就是会涉及到覆盖的问题, 组件的data、methods、filters会覆盖mixins里的同名data、methods、filters。
>
> - 第二点就是 变量来源不明确（隐式传入），不利于阅读，使代码变得难以维护。

**Vue3的官方hook**

```
import { useAttrs} from 'vue' // 可以获得组件所有属性
```

**Vue3 的自定义的hook**

- Vue3 的 hook函数 相当于 vue2 的 mixin, 不同在与 hooks 是函数
- Vue3 的 hook函数 可以帮助我们提高代码的复用性, 让我们能在不同的组件中都利用 hooks 函数
- Vue3 hook 库[Get Started | VueUse](https://vueuse.org/guide/)



**一个图片转base64的hook功能函数**

```ts
import { onMounted } from 'vue'
 
type Options = {
    el: string
}
 
type Return = {
    Baseurl: string | null
}
export default function (option: Options): Promise<Return> {
 
    return new Promise((resolve) => {
        onMounted(() => {
            const file: HTMLImageElement = document.querySelector(option.el) as HTMLImageElement;
            file.onload = ():void => {
                resolve({
                    Baseurl: toBase64(file)
                })
            } 
        })
 
        const toBase64 = (el: HTMLImageElement): string => {
            const canvas: HTMLCanvasElement = document.createElement('canvas')
            const ctx = canvas.getContext('2d') as CanvasRenderingContext2D
            canvas.width = el.width
            canvas.height = el.height
            ctx.drawImage(el, 0, 0, canvas.width,canvas.height)
            console.log(el.width);
            
            return canvas.toDataURL('image/png')
        }
    })
}
```



# 全局函数和变量

> 由于Vue3 没有Prototype 属性 使用 app.config.globalProperties 代替 然后去定义变量和函数

**Vue2**

```ts
// 之前 (Vue 2.x)
Vue.prototype.$http = () => {}
```

**Vue3**

```ts
// 之后 (Vue 3.x)
const app = createApp({})
app.config.globalProperties.$http = () => {}
```

**案例**

```ts
// ---main.ts----

type Filter = {
    format: <T extends any>(str: T) => T
  }
// 声明要扩充@vue/runtime-core包的声明.
// 这里扩充"ComponentCustomProperties"接口, 因为他是vue3中实例的属性的类型.
  declare module '@vue/runtime-core' {
    export interface ComponentCustomProperties {
        $filters: Filter
    }
  }
  
app.config.globalProperties.$filters = {
  format<T extends any>(str: T): string {
    return `$${str}`
  }
}
```



# 插件

> 插件是自包含的代码，通常向 Vue 添加全局级功能。你如果是一个对象需要有install方法Vue会帮你自动注入到install 方法 你如果是function 就直接当install 方法去使用

## 使用插件

在使用 `createApp()` 初始化 Vue 应用程序后，你可以通过调用 `use()` 方法将插件添加到你的应用程序中。

```ts
import { createApp } from 'vue'
import './style.css'
import App from './App.vue'
import Loading from './components/loading'
let app = createApp(App)

app.use(Loading)
app.mount('#app')
```

## 封装插件

**Loading.Vue**

```vue
<template>
    <div v-if="isShow" class="loading">
        <div class="loading-content">Loading...</div>
    </div>
</template>
    
<script setup lang='ts'>
import { ref } from 'vue';
const isShow = ref(false)//定位loading 的开关
 
const show = () => {
    isShow.value = true
}
const hide = () => {
    isShow.value = false
}
//对外暴露 当前组件的属性和方法
defineExpose({
    isShow,
    show,
    hide
})
</script>
     
<style scoped lang="less">
.loading {
    position: fixed;
    inset: 0;
    background: rgba(0, 0, 0, 0.8);
    display: flex;
    justify-content: center;
    align-items: center;
    &-content {
        font-size: 30px;
        color: #fff;
    }
}
</style>
```

**Loading.ts**

```ts
import {  createVNode, render, VNode, App } from 'vue';
import Loading from './index.vue'
 
export default {
    install(app: App) {
        //createVNode vue提供的底层方法 可以给我们组件创建一个虚拟DOM 也就是Vnode
        const vnode: VNode = createVNode(Loading)
        //render 把我们的Vnode 生成真实DOM 并且挂载到指定节点
        render(vnode, document.body)
        // Vue 提供的全局配置 可以自定义
        app.config.globalProperties.$loading = {
            show: () => vnode.component?.exposed?.show(),
            hide: () => vnode.component?.exposed?.hide()
        }
 
    }
}
```

**Main.ts**

```ts
import Loading from './components/loading'
 
let app = createApp(App)
 
app.use(Loading) 
 
type Lod = {
    show: () => void,
    hide: () => void
}
//编写ts loading 声明文件防止报错 和 智能提示
declare module '@vue/runtime-core' {
    export interface ComponentCustomProperties {
        $loading: Lod
    }
}
 
app.mount('#app')
```

**App.vue**

```vue
<script>
import {getCurrentInstance, ComponentInternalInstance} from 'vue'
    
const {appContext} = getCurrentInstance() as ComponentInternalInstance

const showLoading = () => {
    appContext.config.globalProperties.$loading.show()
    this.$loading.show() // vue2 OptionsAPI写法
}
</script>
```



# Scoped和样式穿透

> 主要是用于修改很多vue常用的组件库（element, vant, AntDesigin），虽然配好了样式但是还是需要更改其他的样式
>
> 并且有scoped就需要用到样式穿透

```css
/* vue2写法 */
/deep/ .class {
    background: red;
}

/* vue3写法 */
:deep(.class) {
    background: red;
}
```

**scoped的原理**

vue中的scoped 通过在DOM结构以及css样式上加唯一不重复的标记:data-v-[hash](https://so.csdn.net/so/search?q=hash&spm=1001.2101.3001.7020)的方式，以保证唯一（而这个工作是由过PostCSS转译实现的），达到样式私有化模块化的目的。



**插槽选择器**`:slotted()`

在父组件替换子组件slot的内容在子组件加css类名不会生效，它会默认slot内容在父组件中，因此用插槽选择器

```css
:slotted(.class) {
    background: red;
}
```

**全局选择器**`:global()`

即使是在scoped的style下也能给全局的元素加样式

```css
:global(div) {
	 background: red;
}
```

**动态CSS**

单文件组件的 `<style>` 标签可以通过 `v-bind` 这一 CSS 函数将 CSS 的值关联到动态的组件状态上：

```vue
<template>
    <div class="div">
       动态css
    </div>
</template>
 
<script lang="ts" setup>
import { ref } from 'vue'
const red = ref<string>('red')
</script>
 
<style lang="less" scoped>
.div{
   color:v-bind(red)
}
 
</style>
```



# Event loop

**JS 执行机制**

在我们学js 的时候都知道js 是单线程的如果是多线程的话会引发一个问题在同一时间同时操作DOM 一个增加一个删除JS就不知道到底要干嘛了，所以这个语言是单线程的但是随着HTML5到来js也支持了多线程`webWorker` 但是也是不允许操作DOM

单线程就意味着所有的任务都需要排队，后面的任务需要等前面的任务执行完才能执行，如果前面的任务耗时过长，后面的任务就需要一直等，一些从用户角度上不需要等待的任务就会一直等待，这个从体验角度上来讲是不可接受的，所以`JS`中就出现了异步的概念。

**同步任务**

代码从上到下按顺序执行

**异步任务**

1. 宏任务

   script(整体代码)、setTimeout、setInterval、UI交互事件onClick...、postMessage、Ajax

2. 微任务

   Promise.then catch finally、MutaionObserver、process.nextTick(Node.js 环境)

**运行机制**

所有的同步任务都是在主进程执行的形成一个执行栈，主线程之外，还存在一个"任务队列"，异步任务执行队列中先执行宏任务，然后清空当次宏任务中的所有微任务，然后进行下一个tick如此形成循环。

```javascript
<script>
async function Prom(){
    console.log('Y') // 同步 
    await Promise.reslove() // 微任务
    console.log('X')
}
setTimeout(() => { // 宏任务
	console.log(1)
	Promise.resolve().then(()=>{
        console.log(2)
    })
})
setTimeout(() => { // 宏任务
	console.log(3)
	Promise.resolve().then(()=>{
        console.log(4)
    })
})
Promise.resolve().then(()=>{ //微任务
    console.log(5)
})
Promise.resolve().then(()=>{ //微任务
    console.log(6)
})
Promise.resolve().then(()=>{ //微任务
    console.log(7)
})
Prom()
console.log(0) // 同步
</script>

// Y 0 5 6 7 X 1 2 3 4
// 先执行script这个宏任务，然后找里面的同步其次清空本次宏任务里的微任务，Prom()在4个微任务之后才执行的，所以x要比567后，最后执行下一个宏任务和情况里面微任务
```



# nextTick

当你在 Vue 中更改响应式状态时，最终的 DOM 更新并不是同步生效的，而是由 Vue 将它们缓存在一个队列中，直到下一个“tick”才一起执行。这样是为了确保每个组件无论发生多少状态改变，都仅执行一次更新。

`nextTick()` 可以在状态改变后立即使用，以等待 DOM 更新完成。你可以传递一个回调函数作为参数，或者 await 返回的 Promise。

把改变数据的代码放在`nextTick()`中，把同步代码变异步，这样数据改变和DOM更新就一致了。

```vue
<script>
let chatList = reactive([])
let box = ref<HTMLDivElement>()
let ipt = ref('')
const send = async () => {
    chatList.push({
        name:'',
        message:ipt.value
    })
    // 2.async await写法
    await nextTick()
    box.value!.scrollTop = 9999999
}
// 1.回调函数模式
nextTick(()=>{
    box.value!.scrollTop = 9999999
})
</script>
```



# Tailwind CSS

> Tailwind CSS 是一个由js编写的CSS框架它是基于postCss 去解析的

 官网地址[Tailwind CSS 中文文档 - 无需离开您的HTML，即可快速建立现代网站。](http://www.tailwindcss.cn/)

对于PostCSS的插件使用，我们再使用的过程中一般都需要如下步骤：

1. PostCSS 配置文件 postcss.config.js，新增 tailwindcss 插件。
2. TaiWindCss插件需要一份配置文件，比如:tailwind.config.js。

[PostCSS - 是一个用 JavaScript 工具和插件来转换 CSS 代码的工具 | PostCSS 中文网](https://www.postcss.com.cn/)



1. 安装 Tailwind 以及其它依赖项

```
npm install -D tailwindcss@latest postcss@latest autoprefixer@latest
```

2. 生成配置文件

```
npx tailwindcss init -p
```

3. 修改配置文件 tailwind.config.js

2.6版本 

```js
module.exports = {
  purge: ['./index.html', './src/**/*.{vue,js,ts,jsx,tsx}'],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

3.0版本

```js
module.exports = {
  content: ['./index.html', './src/**/*.{vue,js,ts,jsx,tsx}'],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

4. 创建一个index.css并在main.ts引入

```
@tailwind base;
@tailwind components;
@tailwind utilities;
```



# unocss原子化

**CSS原子化的优缺点**

1.减少了css体积，提高了css复用

2.减少起名的复杂度

3.增加了记忆成本 将css拆分为原子之后，你势必要记住一些class才能书写，哪怕tailwindcss提供了完善的工具链，你写background，也要记住开头是bg

**tips：最好用于vite webpack属于阉割版功能很少**

1. 安装

```
npm i -D unocss
```

2. 配置vite.config.ts 

```ts
import unocss from 'unocss/vite'
 
 plugins: [vue(), vueJsx(),unocss({
      rules:[
        
      ]
  })],
```

3. main.ts 引入 

```ts
import 'uno.css'
```

4. 配置静态css

```ts
rules: [
  ['flex', { display: "flex" }]
]
```

配置动态css（使用正则表达式）

m-参数*10  例如 m-10 就是 margin:100px

```ts
rules: [
  [/^m-(\d+)$/, ([, d]) => ({ margin: `${Number(d) * 10}px` })],
  ['flex', { display: "flex" }]
]
```

unocss 预设

```ts
 presets:[presetIcons(),presetAttributify(),presetUno()]
// presetIcons Icon图标预设
// presetAttributify 属性化模式支持属性语义化 无须class
// presetUno 工具类预设是一系列流行的原子化框架的 通用超集，包括了 Tailwind CSS，Windi CSS，Bootstrap，Tachyons 等。
```

图标集合安装（[icones](https://icones.js.org/)官网）

```
npm i -D @iconify-json/图标库名
```



# Vue开发桌面程序Electron

>  electron 内置了 Chromium 和 nodeJS 其中 Chromium 是渲染进程 主要渲染和解析HTML，Nodejs作为主进程，其中管道用IPC 通信

## 1.使用vite 构建 electron项目

创建一个vite 项目

```
npm init vite@latest
```

![img](https://img-blog.csdnimg.cn/ca896e7502084f3dbb04f36301e3717f.png)

安装electron

```
npm install electron -D
npm install vite-plugin-electron -D
```

 根目录新建 electron / index.ts

![img](https://img-blog.csdnimg.cn/40a06d3347e84cce85c66b1202da81d2.png)

修改vite.config.ts 配置文件

引入electron插件配置main entry对应electron的文件

```typescript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import electron from 'vite-plugin-electron'
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(), electron({
    main: {
      entry: "electron/index.ts"
    }
  })]
})
```

![img](https://img-blog.csdnimg.cn/d66b35a9ab9f472f89332d2c34b61e44.png)

编写代码 electron / index.ts

```typescript
import { app, BrowserWindow } from 'electron'
import path from 'path'
//app 控制应用程序的事件生命周期。
//BrowserWindow 创建并控制浏览器窗口。
 
let win: BrowserWindow | null;
//定义全局变量获取 窗口实例
 
const createWindow = () => {
    win = new BrowserWindow({
        //
        webPreferences: {
            devTools: true,
            contextIsolation: false,
            nodeIntegration: true
            //允许html页面上的javascipt代码访问nodejs 环境api代码的能力（与node集成的意思）
        }
    })
    if (app.isPackaged) {
        win.loadFile(path.join(__dirname, "../index.html"));
    } else {
//VITE_DEV_SERVER_HOST 如果是undefined 换成  VITE_DEV_SERVER_HOSTNAME
        win.loadURL(`http://${process.env['VITE_DEV_SERVER_HOST']}:${process.env['VITE_DEV_SERVER_PORT']}`)
    }
}
//isPackage 不好使换下面的
  //  if(process.env.NODE_ENV != 'development'){
      //  win.loadFile(path.join(__dirname, "../index.html"));
  //  }else{
        //win.loadURL(`http://${process.env['VITE_DEV_SERVER_HOSTNAME']}:${process.env['VITE_DEV_SE//RVER_PORT']}`)
   // }
//在Electron完成初始化时被触发
app.whenReady().then(createWindow)
```

配置package.json增加`main`字段`type 去掉`

```typescript
{
  "name": "electron-vite",
  "private": true,
  "version": "0.0.0",
  "main": "dist/electron/index.js",
  "scripts": {
    "dev": "vite",
    "build": "vue-tsc --noEmit && vite build  &&  electron-builder",
    "preview": "vite preview"
  },
  "dependencies": {
    "vue": "^3.2.37"
  },
  "devDependencies": {
    "@vitejs/plugin-vue": "^3.0.0",
    "electron": "^19.0.10",
    "electron-builder": "^23.1.0",
    "typescript": "^4.6.4",
    "vite": "^3.0.0",
    "vite-plugin-electron": "^0.8.3",
    "vue-tsc": "^0.38.4"
  }
}
```

npm run dev

![img](https://img-blog.csdnimg.cn/b28dd412c1ef45209e88a6face2110b7.png)



## 2.打包Electron

需要安装electron-builder

```coffeescript
npm install electron-builder -D
```

package json 配置 build 修改npm run build命令

```javascript
"build": "vue-tsc --noEmit && vite build  &&  electron-builder",
  "build": {
    "appId": "com.electron.desktop",
    "productName": "electron",
    "asar": true,
    "copyright": "Copyright © 2022 electron",
    "directories": {
      "output": "release/"
    },
    "files": [
      "dist"
    ],
    "mac": {
      "artifactName": "${productName}_${version}.${ext}",
      "target": [
        "dmg"
      ]
    },
    "win": {
      "target": [
        {
          "target": "nsis",
          "arch": [
            "x64"
          ]
        }
      ],
      "artifactName": "${productName}_${version}.${ext}"
    },
    "nsis": {
      "oneClick": false,
      "perMachine": false,
      "allowToChangeInstallationDirectory": true,
      "deleteAppDataOnUninstall": false
    },
    "publish": [
      {
        "provider": "generic",
        "url": "http://127.0.0.1:8080"
      }
    ],
    "releaseInfo": {
      "releaseNotes": "版本更新的具体内容"
    }
  }
```

nsis 配置详解 

```json
{"oneClick": false, // 创建一键安装程序还是辅助安装程序（默认是一键安装）
    "allowElevation": true, // 是否允许请求提升，如果为false，则用户必须使用提升的权限重新启动安装程序 （仅作用于辅助安装程序）
    "allowToChangeInstallationDirectory": true, // 是否允许修改安装目录 （仅作用于辅助安装程序）
    "installerIcon": "public/timg.ico",// 安装程序图标的路径
    "uninstallerIcon": "public/timg.ico",// 卸载程序图标的路径
    "installerHeader": "public/timg.ico", // 安装时头部图片路径（仅作用于辅助安装程序）
    "installerHeaderIcon": "public/timg.ico", // 安装时标题图标（进度条上方）的路径（仅作用于一键安装程序）
    "installerSidebar": "public/installerSiddebar.bmp", // 安装完毕界面图片的路径，图片后缀.bmp，尺寸164*314 （仅作用于辅助安装程序）
    "uninstallerSidebar": "public/uninstallerSiddebar.bmp", // 开始卸载界面图片的路径，图片后缀.bmp，尺寸164*314 （仅作用于辅助安装程序）
    "uninstallDisplayName": "${productName}${version}", // 控制面板中的卸载程序显示名称
    "createDesktopShortcut": true, // 是否创建桌面快捷方式
    "createStartMenuShortcut": true,// 是否创建开始菜单快捷方式
    "shortcutName": "SHom", // 用于快捷方式的名称，默认为应用程序名称
    "include": "script/installer.nsi",  // NSIS包含定制安装程序脚本的路径，安装过程中自行调用  (可用于写入注册表 开机自启动等操作)
    "script": "script/installer.nsi",  // 用于自定义安装程序的NSIS脚本的路径
    "deleteAppDataOnUninstall": false, // 是否在卸载时删除应用程序数据（仅作用于一键安装程序）
    "runAfterFinish": true,  // 完成后是否运行已安装的应用程序（对于辅助安装程序，应删除相应的复选框）
    "menuCategory": false, // 是否为开始菜单快捷方式和程序文件目录创建子菜单，如果为true，则使用公司名称
}
```

npm run build

![img](https://img-blog.csdnimg.cn/16969184f40d45cf8a3043a11e7281d3.png)

 ![img](https://img-blog.csdnimg.cn/7256d03f7d61424f854dfc458ecb16bc.png)

 ![img](https://img-blog.csdnimg.cn/6018356cd0b94f4bb098da0c23ac6ace.png)

##  3.Electron Vscode 输出乱码解决 方案

dev 的时候 加上chcp 65001 输出中文

```
 "dev": "chcp 65001 && vite",
```

![img](https://img-blog.csdnimg.cn/2458dd6785a349f097eca043eb59766c.png)

加上之后

![img](https://img-blog.csdnimg.cn/22d38f77131248da83fb9bbc25b47512.png)

##  4.渲染进程和主进程通信

vite.config.ts 需要修改 不然会报一个错Error: Module "path" has been externalized for browser compatibility. Cannot

只要安装了 vite-plugin-electron 就会带上 vite-plugin-electron-renderer 直接引入用就行

```typescript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import electron from 'vite-plugin-electron'
import electronRender from 'vite-plugin-electron-renderer'

// https://vitejs.dev/config/

export default defineConfig({
    
  plugins: [vue(), electron({
    main: {
      entry: "electron/index.ts"
    }
  }),electronRender()],
  build:{
    emptyOutDir: false,
  }
})
```

渲染进程使用ipcRenderer 发送

```javascript
import { ipcRenderer } from 'electron'

const open = () => {
     ipcRenderer.send('openFlyCar')
}
```

主进程使用 ipcMain 接受

```javascript
ipcMain.on('openFlyCar',()=>{
    console.log('收到')
})
```

主进程通知渲染进程

```javascript
const  win = new BrowserWindow(xxxxx)
win!.webContents.send('load', { message: "electron初始化了" })
```

渲染进程接受

```javascript
ipcRenderer.on('load',(_,data)=>{
  console.log(data)
})
```



# Pinia

Pinia.js 有如下特点：

- 完整的 ts 的支持；
- 足够轻量，压缩后的体积只有1kb左右;
- 去除 mutations，只有 state，getters，actions；
- actions 支持同步和异步；
- 代码扁平化没有模块嵌套，只有 store 的概念，store 之间可以自由使用，每一个store都是独立的
- 无需手动添加 store，store 一旦创建便会自动添加；
- 支持Vue3 和 Vue2

官方文档[Pinia](https://pinia.vuejs.org/)

git 地址 https://github.com/vuejs/pinia

## 安装

1. 安装

```
yarn add pinia
 
npm install pinia
```

2. 引入注册Vue3

```ts
import { createApp } from 'vue'
import App from './App.vue'
import {createPinia} from 'pinia'
 
const store = createPinia()
let app = createApp(App)
 
 
app.use(store)
 
app.mount('#app')
```

Vue2使用

```js
import { createPinia, PiniaVuePlugin } from 'pinia'
 
Vue.use(PiniaVuePlugin)
const pinia = createPinia()
 
new Vue({
  el: '#app',
  // other options...
  // ...
  // note the same `pinia` instance can be used across multiple Vue apps on
  // the same page
  pinia,
})
```

## 初始化仓库Store

1. 新建一个文件夹Store

2. 新建文件index.ts

3. 定义仓库Store

```javascript
import { defineStore } from 'pinia'
```

4. 新建文件store-namespace.ts

   我们需要知道存储是使用定义的`defineStore()`，并且它需要一个唯一的名称，作为第一个参数传递，所以名称抽离出去了

```objectivec
export const enum Names {
    Test = 'TEST'
}
```

5. 初始化Store

```javascript
import { defineStore } from 'pinia'
import { Names } from './store-namespce'
 
export const useTestStore = defineStore(Names.Test, {
    // State箭头函数返回一个对象,在对象里面定义值
     state:()=>{
         return {
             current:1
         }
     },
     //类似于computed 可以帮我们去修饰我们的值
     getters:{
 
     },
     //可以操作异步 和 同步提交state
     actions:{
 
     }
})
```

这个名称，也称为id，是必要的，Pania 使用它来将商店连接到 devtools。将返回的函数命名为use...是可组合项之间的约定，以使其使用习惯。

## State

> Vuex更改state还还需要通过mutation来更改，Pinia可以直接修改，并且方法众多，是双向数据

```ts
// State是允许直接修改值的 例如current++
Test.current++
// 批量修改State的值
Test.$patch({
   current:200,
   age:300
})
// 批量修改函数形式（推荐使用函数形式 可以自定义修改逻辑）
Test.$patch((state)=>{
    state.current++;
    state.age = 40
})
// 通过原始对象修改整个实例，$state您可以通过将store的属性设置为新对象来替换store的整个状态，缺点就是必须修改整个对象的所有属性
Test.$state = {
    current:10,
    age:30
}
// 通过actions修改，定义Actions，在actions中直接使用this就可以指到state里面的值
actions:{
    setCurrent () {
        this.current++
    }
}
Test.setCurrent()
```

## 解构store

在Pinia是不允许直接解构是会失去响应性的，修改Test current 解构完之后的数据不会变

而源数据是会变的

```ts
const Test = useTestStore()
 
const { current, name } = Test
 
console.log(current, name);
```

**解决方案可以使用 storeToRefs**

```ts
import { storeToRefs } from 'pinia'
 
const Test = useTestStore()
 
const { current, name } = storeToRefs(Test)
```

其原理跟toRefs 一样的给里面的数据包裹一层toref

源码 通过toRaw使store变回原始数据防止重复代理

循环store 通过 isRef isReactive 判断 如果是响应式对象直接拷贝一份给refs 对象 将其原始对象包裹toRef 使其变为响应式对象 



## Action

```ts
// 同步
actions: {
        increment() {
            this.counter++
        },
        randomizeCounter() {
            this.counter = Math.round(100 * Math.random())
        },
}
Test.randomizeCounter()

// 异步
actions: {
        async getLoginInfo() {
            const result = await Login()
            this.user = result;
        }
}
Test.getLoginInfo()
```

## getters

1. 使用箭头函数不能使用this this指向已经改变指向undefined，修改值请用state，主要作用类似于computed数据修饰并且有缓存

```ts
getters:{
   newPrice:(state)=>  `$${state.user.price}`
}
```

2. 普通函数形式可以使用this

```ts
getters:{
   newCurrent ():number {
       return ++this.current
   }
}
```

3. getters 互相调用

```ts
    getters:{
       newCurrent ():number | string {
           return ++this.current + this.newName
       },
       newName ():string {
           return `$-${this.name}`
       }
    },
```

## API

### $reset

> 调用$reset();将会把state所有值重置回原始状态

```ts
state: () => ({
     user: <Result>{},
     name: "default",
     current:1
})
const change = () => {
     Test.current++
}
Test.$reset()
```

### $subscribe

> 类似于Vuex 的abscribe 只要有state的变化就会执行这个函数

```ts
Test.$subscribe((args,state)=>{
   console.log(args,state); 
})
```

第二个参数

如果你的组件卸载之后还想继续调用请设置第二个参数

```ts
Test.$subscribe((args,state)=>{
   console.log(args,state);
   
},{
  detached:true
})
```

### $onAction

>  只要有actions被调用就会执行这个函数

```ts
Test.$onAction((args)=>{
   console.log(args); 
},true)
```

## pinia插件

pinia 和 vuex 都有一个通病 页面刷新状态会丢失

我们可以写一个pinia 插件缓存他的值

```ts
const __piniaKey = '__PINIAKEY__'
//定义兜底变量

type Options = {
  key?: string
}
//定义入参类型

//将数据存在本地
const setStorage = (key: string, value: any): void => {
  localStorage.setItem(key, JSON.stringify(value))
}
//存缓存中读取
const getStorage = (key: string) => {
  return (localStorage.getItem(key) ? JSON.parse(localStorage.getItem(key) as string) : {})
}
//利用函数柯里化接受用户入参
const piniaPlugin = (options: Options) => {
  //将函数返回给pinia  让pinia  调用 注入 context
  return (context: PiniaPluginContext) => {
    const { store } = context;
    const data = getStorage(`${options?.key ?? __piniaKey}-${store.$id}`)
    store.$subscribe(() => {
      setStorage(`${options?.key ?? __piniaKey}-${store.$id}`, toRaw(store.$state));
    })
    //返回值覆盖pinia 原始值
    return {
      ...data
    }
  }
}
//初始化pinia
const pinia = createPinia()

//注册pinia 插件
pinia.use(piniaPlugin({
  key: "pinia"
}))
```



# Router

> vue是单页应用不会有那么多html 让我们跳转 所以要使用路由做页面的跳转
>
> Vue 路由允许我们通过不同的 URL 访问不同的内容。通过 Vue 可以实现多视图的单页Web应用

## 安装

使用Vue3 安装对应的router4版本

使用Vue2安装对应的router3版本

```
npm install vue-router@4
```

在src目录下面新建router 文件 然后在router 文件夹下面新建 index.ts

```ts
//引入路由对象
import { createRouter, createWebHistory, createWebHashHistory, createMemoryHistory, RouteRecordRaw } from 'vue-router'
 
//vue2 mode history vue3 createWebHistory
//vue2 mode  hash  vue3  createWebHashHistory
//vue2 mode abstact vue3  createMemoryHistory
 
//路由数组的类型 RouteRecordRaw
// 定义一些路由
// 每个路由都需要映射到一个组件。
const routes: Array<RouteRecordRaw> = [{
    path: '/',
    component: () => import('../components/a.vue')
},{
    path: '/register',
    component: () => import('../components/b.vue')
}]
 
 
 
const router = createRouter({
    history: createWebHistory(),
    routes
})
 
//导出router
export default router
```

## 命名路由

除了 `path` 之外，你还可以为任何路由提供 `name`。这有以下优点：

- 没有硬编码的 URL

- `params` 的自动编码/解码。

- 防止你在 url 中出现打字错误。

- 绕过路径排序（如显示一个）

  ```ts
  const routes:Array<RouteRecordRaw> = [
      {
          path:"/",
          name:"Login",
          component:()=> import('../components/login.vue')
      },
      {
          path:"/reg",
          name:"Reg",
          component:()=> import('../components/reg.vue')
      }
  ]
  ```

  router-link跳转方式需要改变 变为对象并且有对应name

  ```vue
      <div>
        <router-link :to="{name:'Login'}">Login</router-link>
        <router-link style="margin-left:10px" :to="{name:'Reg'}">Reg</router-link>
      </div>
      <hr />
  ```

  ## 编程式导航

  除了使用 `<router-link>` 创建 a 标签来定义导航链接，我们还可以借助 router 的实例方法，通过编写代码来实现。

  1. 字符串模式

  ```ts
  import { useRouter } from 'vue-router'
  const router = useRouter()
   
  const toPage = () => {
    router.push('/reg')
  }
  ```

  2. 对象模式

  ```ts
  import { useRouter } from 'vue-router'
  const router = useRouter()
   
  const toPage = () => {
    router.push({
      path: '/reg'
    })
  }
  ```

  3. 命名式路由模式

  ```ts
  import { useRouter } from 'vue-router'
  const router = useRouter()
   
  const toPage = () => {
    router.push({
      name: 'Reg'
    })
  }
  ```

  **a标签跳转**

  直接通过a href也可以跳转但是会刷新页面

  ```html
   <a href="/reg">rrr</a>
  ```

## 路由传参

### query路由传参

编程式导航 使用router push 或者 replace 的时候 改为对象形式新增query 必须传入一个对象

```ts
const toDetail = (item: Item) => {
    router.push({
        path: '/reg',
        query: item
    })
}
```

接收参数

```vue
<template>
 <div>品牌：{{ route.query?.name }}</div>
 <div>价格：{{ route.query?.price }}</div>
 <div>ID：{{ route.query?.id }}</div>
</template>

<script>
import { useRoute } from 'vue-router';
const route = useRoute()
</script>
```

### params路由传参

编程式导航 使用router push 或者 replace 的时候 改为对象形式并且只能使用name，path无效，然后传入params

```ts
const toDetail = (item: Item) => {
    router.push({
        name: 'Reg',
        params: item
    })
}
```

接收参数

```vue
<template>
<div>品牌：{{ route.params?.name }}</div>
<div>价格：{{ route.params?.price }}</div>
<div>ID：{{ route.params?.id }}</div>
</template>

<script>
import { useRoute } from 'vue-router';
const route = useRoute()
</script>
```

### 动态路由传参

*路径参数* 用冒号 `:` 表示。当一个路由被匹配时，它的 *params* 的值将在每个组件

```ts
const routes:Array<RouteRecordRaw> = [
    {
        path:"/",
        name:"Login",
        component:()=> import('../components/login.vue')
    },
    {
        //动态路由参数
        path:"/reg/:id",
        name:"Reg",
        component:()=> import('../components/reg.vue')
    }
]
```

```ts
const toDetail = (item: Item) => {
    router.push({
        name: 'Reg',
        params: {
            id: item.id
        }
    })
}
```

### 二者的区别

1. query 传参配置的是 path，而 params 传参配置的是name，在 params中配置 path 无效
2. query 在路由配置不需要设置参数，而 params 必须设置
3. query 传递的参数会显示在地址栏中
4. params传参刷新会无效，但是 query 会保存传递过来的值，刷新不变 ;
5. 路由配置



## 重定向-别名

### 重定向 redirect

1. 字符串形式配置

```ts
const routes: Array<RouteRecordRaw> = [
    {
        path:'/',
        component:()=> import('../components/root.vue'),
        redirect:'/user1',
        children:[
            {
                path:'/user1',
                components:{
                    default:()=> import('../components/A.vue')
                }
            },
            {
                path:'/user2',
                components:{
                    bbb:()=> import('../components/B.vue'),
                    ccc:()=> import('../components/C.vue')
                }
            }
        ]
    }
]
```

2. 对象形式配置

```ts
const routes: Array<RouteRecordRaw> = [
    {
        path: '/',
        component: () => import('../components/root.vue'),
        redirect: { path: '/user1' },
        children: [
            {
                path: '/user1',
                components: {
                    default: () => import('../components/A.vue')
                }
            },
            {
                path: '/user2',
                components: {
                    bbb: () => import('../components/B.vue'),
                    ccc: () => import('../components/C.vue')
                }
            }
        ]
    }
]
```

3. 函数模式（可以传参）

```ts
const routes: Array<RouteRecordRaw> = [
    {
        path: '/',
        component: () => import('../components/root.vue'),
        redirect: (to) => {
            return {
                path: '/user1',
                query: to.query
            }
        },
        children: [
            {
                path: '/user1',
                components: {
                    default: () => import('../components/A.vue')
                }
            },
            {
                path: '/user2',
                components: {
                    bbb: () => import('../components/B.vue'),
                    ccc: () => import('../components/C.vue')
                }
            }
        ]
    }
]
```

### 别名 alias

```ts
const routes: Array<RouteRecordRaw> = [
    {
        path: '/',
        component: () => import('../components/root.vue'),
        alias:["/root","/root2","/root3"],
        children: [
            {
                path: 'user1',
                components: {
                    default: () => import('../components/A.vue')
                }
            },
            {
                path: 'user2',
                components: {
                    bbb: () => import('../components/B.vue'),
                    ccc: () => import('../components/C.vue')
                }
            }
        ]
    }
]
```

## 导航守卫

### 全局前置守卫

router.beforeEach

```ts
router.beforeEach((to, form, next) => {
    console.log(to, form);
    next()
})
```

每个守卫方法接收三个参数：

```ts
to: Route， 即将要进入的目标 路由对象；
from: Route，当前导航正要离开的路由；
next(): 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 confirmed (确认的)。
next(false): 中断当前的导航。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 from 路由对应的地址。
next('/') 或者 next({ path: '/' }): 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。
```

案例 权限判断

```ts
const whileList = ['/']
 
router.beforeEach((to, from, next) => {
    let token = localStorage.getItem('token')
    //白名单 有值 或者登陆过存储了token信息可以跳转 否则就去登录页面
    if (whileList.includes(to.path) || token) {
        next()
    } else {
        next({
            path:'/'
        })
    }
})
```

### 全局后置守卫

使用场景一般可以用来做loadingBar

你也可以注册全局后置钩子，然而和守卫不同的是，这些钩子不会接受 `next` 函数也不会改变导航本身：

```
router.afterEach((to,from)=>{
    Vnode.component?.exposed?.endLoading()
})
```

## 路由元信息

通过路由记录的 `meta` 属性可以定义路由的**元信息**。使用路由元信息可以在路由中附加自定义的数据，例如：

- 权限校验标识。
- 路由组件的过渡名称。
- 路由组件持久化缓存 (keep-alive) 的相关配置。
- 标题名称

我们可以在**导航守卫**或者是**路由对象**中访问路由的元信息数据。

```ts
/*
router.beforeEach((to, from, next) => {
	document.title = to.meta.title
})
*/
const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes: [
    {
      path: '/',
      component: () => import('@/views/Login.vue'),
      meta: {
        title: "登录"
      }
    },
    {
      path: '/index',
      component: () => import('@/views/Index.vue'),
      meta: {
        title: "首页",
      }
    }
  ]
})
```

使用TS扩展

如果不使用扩展 将会是unknow 类型

```ts
declare module 'vue-router' {
  interface RouteMeta {
    title?: string
  }
}
```

## 路由过渡动效

想要在你的路径组件上使用转场，并对导航进行动画处理，你需要使用 [v-slot API](https://router.vuejs.org/zh/api/#router-view-s-v-slot)：

```vue
    <router-view #default="{route,Component}">
        <transition  :enter-active-class="`animate__animated ${route.meta.transition}`">
            <component :is="Component"></component>
        </transition>
    </router-view>
```

上面的用法会对所有的路由使用相同的过渡。如果你想让每个路由的组件有不同的过渡，你可以将[元信息](https://router.vuejs.org/zh/guide/advanced/meta.html)和动态的 `name` 结合在一起，放在`<transition>` 上： 

```ts
declare module 'vue-router'{
     interface RouteMeta {
        title:string,
        transition:string,
     }
}
 
const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes: [
    {
      path: '/',
      component: () => import('@/views/Login.vue'),
      meta:{
         title:"登录页面",
         transition:"animate__fadeInUp",
      }
    },
    {
      path: '/index',
      component: () => import('@/views/Index.vue'),
      meta:{
         title:"首页！！！",
         transition:"animate__bounceIn",
      }
    }
  ]
})
```

## 滚动行为

使用前端路由，当切换到新路由时，想要页面滚到顶部，或者是保持原先的滚动位置，就像重新加载页面那样。vue-router 可以自定义路由切换时页面如何滚动。

当创建一个 Router 实例，你可以提供一个 `scrollBehavior` 方法

```ts
const router = createRouter({
  history: createWebHistory(),
  scrollBehavior: (to, from, savePosition) => {
    console.log(to, '==============>', savePosition);
    return new Promise((r) => {
      setTimeout(() => {
        r({
          top: 10000
        })
      }, 2000);
    })
  },
```

scrollBehavior 方法接收 to 和 from 路由对象。第三个参数 savedPosition 当且仅当 popstate 导航 (通过浏览器的 前进/后退 按钮触发) 时才可用。

scrollBehavior 返回滚动位置的对象信息，长这样：

- { left: number, top: number }

```ts
const router = createRouter({
  history: createWebHistory(),
  scrollBehavior: (to, from, savePosition) => {
    return {
       top:200
    }
  },
```

## 动态路由

我们一般使用动态路由都是后台会返回一个路由表前端通过调接口拿到后处理(后端处理路由)

主要使用的方法就是router.addRoute

### 添加路由

动态路由主要通过两个函数实现。`router.addRoute()` 和 `router.removeRoute()`。它们**只**注册一个新的路由，也就是说，如果新增加的路由与当前位置相匹配，就需要你用 `router.push()` 或 `router.replace()` 来**手动导航**，才能显示该新路由

```ts
router.addRoute({ path: '/about', component: About })
```

### 删除路由

有几个不同的方法来删除现有的路由：

- 通过添加一个名称冲突的路由。如果添加与现有途径名称相同的途径，会先删除路由，再添加路由：

  ```ts
  router.addRoute({ path: '/about', name: 'about', component: About })
  // 这将会删除之前已经添加的路由，因为他们具有相同的名字且名字必须是唯一的
  router.addRoute({ path: '/other', name: 'about', component: Other })
  ```

- 通过调用 `router.addRoute()`

   返回的回调：

  ```javascript
  const removeRoute = router.addRoute(routeRecord)
  removeRoute() // 删除路由如果存在的话
  ```

  当路由没有名称时，这很有用。

- 通过使用 `router.removeRoute()`

   按名称删除路由：

  ```javascript
  router.addRoute({ path: '/about', name: 'about', component: About })
  // 删除路由
  router.removeRoute('about')
  ```

  需要注意的是，如果你想使用这个功能，但又想避免名字的冲突，可以在路由中使用 Symbol作为名字。

当路由被删除时，**所有的别名和子路由也会被同时删除**



### 查看现有路由

Vue Router 提供了两个功能来查看现有的路由：

- [router.hasRoute()](https://router.vuejs.org/zh/api/#hasroute)：检查路由是否存在。
- [router.getRoutes()](https://router.vuejs.org/zh/api/#getroutes)：获取一个包含所有路由记录的数组。

### 案例

```ts
const initRouter = async () => {
    const result = await axios.get('http://localhost:9999/login', { params: formInline });
    result.data.route.forEach((v: any) => {
        router.addRoute({
            path: v.path,
            name: v.name,
                                    // vite这儿不能使用@
            component: () => import(`../views/${v.component}`)
        })
        router.push('/index')
    })
    console.log(router.getRoutes());
 
}
```



# TSX



我们之前呢是使用Template去写我们模板。现在可以扩展另一种风格TSX风格

vue2 的时候就已经支持jsx写法，只不过不是很友好，随着vue3对[typescript](https://so.csdn.net/so/search?q=typescript&spm=1001.2101.3001.7020)的支持度，tsx写法越来越被接受

1. 安装插件

```
npm install @vitejs/plugin-vue-jsx -D
```

vite.config.ts 配置

```ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import vueJsx from '@vitejs/plugin-vue-jsx';
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(),vueJsx()]
})
```

2. 修改tsconfig.json 配置文件

```
    "jsx": "preserve",
    "jsxFactory": "h",
    "jsxFragmentFactory": "Fragment",
```

3. 使用tsx

**tsx支持 v-model 的使用**

```tsx
 
import { ref } from 'vue'
 
let v = ref<string>('')
 
const renderDom = () => {
    return (
        <div>
           <input v-model={v.value} type="text" />
           <div>
               {v.value}
           </div>
        <div/>
    )
}
 
export default renderDom
```

**v-show**

```tsx
 
import { ref } from 'vue'
 
let flag = ref(false)
 
const renderDom = () => {
    return (
        <div>
           <div v-show={flag.value}>景天</div>
           <div v-show={!flag.value}>雪见</div>
        <div/>
    )
}
 
export default renderDom
```

**v-if是不支持的**

所以需要改变风格

```tsx
import { ref } from 'vue'
 
let flag = ref(false)
 
const renderDom = () => {
    return (
        <div>
            {
                flag.value ? <div>景天</div> : <div>雪见</div>
            }
        <div/>
    )
}
 
export default renderDom
```

**v-for也是不支持的**

需要使用Map

```tsx
import { ref } from 'vue'
 
let arr = [1,2,3,4,5]
 
const renderDom = () => {
    return (
        <div>
            {
              arr.map(v=>{
                  return <div>${v}</div>
              })
            }
        <div/>
    )
}
 
export default renderDom
```

**v-bind使用**

直接赋值就可以

```tsx
import { ref } from 'vue'
 
let arr = [1, 2, 3, 4, 5]
 
const renderDom = () => {
    return (
        <div>
            <div data-arr={arr}>1</div>
        <div/>
    )
}
 
export default renderDom
```

**v-on绑定事件 所有的事件都按照react风格来**

- 所有事件有on开头
- 所有事件名称首字母大写

```tsx
 
const renderDom = () => {
    return (
        <div>
            <button onClick={clickTap}>点击</button>
        <div/>
    )
}
 
const clickTap = () => {
    console.log('click');
}
 
export default renderDom
```

**Props 接受值**

```tsx
 
import { ref } from 'vue'
 
type Props = {
    title:string
}
 
const renderDom = (props:Props) => {
    return (
        <div>
            <div>{props.title}</div>
            <button onClick={clickTap}>点击</button>
        <div/>
    )
}
 
const clickTap = () => {
    console.log('click');
}
 
export default renderDom
```

**Emit派发**

```tsx
type Props = {
    title: string
}
 
const renderDom = (props: Props,content:any) => {
    return (
        <div>
            <div>{props.title}</div>
            <button onClick={clickTap.bind(this,content)}>点击</button>
        <div/>
    )
}
 
const clickTap = (ctx:any) => {
 
    ctx.emit('on-click',1)
}
```



# Vite

## 安装vite工具

1. 安装node环境
2. 安装vite

```
npm install vite -g  // 全局安装
npm install vite -D// 局部安装
```

3. 局部打包

```
npx vite // 并且会自动搭建网页serve服务
npx vite build // 项目打包
npx vite preview // 预览打包后网页
```

## 构建vite项目

1. 初始化Vite

npm

```kotlin
npm init vite@latest
```

Yarn

```sql
yarn create vite
```

2. 输入项目名称

3. 选择构建的项目模板

4. npm install 安装依赖包

5. npm run dev 启动



## 优势

1. 比vue-cli创建项目更快
2. import的文件可以不加后缀(webpack需要)
3. import的文件可以不写路径(webpack需要)
4. css样式可以直接解析(webpack需要css-loader,style-loader)

4. less,sass预处理器可以安装后直接解析(webpack需要less-loader)
5. postcss、浏览器前缀插件不需要配置安装即用
6. ts语法可以解析(webpack需要bable)



# pnpm

- 支持monorepo 单一仓库, 当前项目只能使用当前项目的包, 不可使用其依赖依赖的包

- ##### 使用pnpm, 依赖包将被存放在一个统一的位置

- **硬链接 (安装包时启用)**: 多个文件夹平等的共享同一个存储单元 (就算删除其中一个,仍可通过其他文件夹访问)

- 软链接: 其他文件或目录的引用

- mklink /H new source

- 跨磁盘会报警告 ==cross-device link not permitted==, 包存储应与安装的位置处于同一驱动器和文件系统上，否则，包将被复制，而不是被链接。 这是由于硬链接的工作方式带来的一个限制，因为一个文件系统上的文件无法寻址另一个文件系统中的位置。

- **项目所在磁盘要和pnpm存储所在磁盘一致！就是说如果您在磁盘 `A` 上执行 `pnpm install`，则 pnpm 存储必须位于磁盘 `A`。 如果 pnpm 存储位于磁盘 `B`，则所有需要的包将被直接复制到项目位置而不是链接。 这个严重的抑制了 pnpm 的存储和性能优势。**

### 安装pnpm

```
npm install -g pnpm

config get registry

pnpm set registry https://registry.npm.taobao.org 

# https://registry.npmjs.org/
pnpm config set store-dir D:/.pnpm-store  # 修改默认仓库地址

pnpm store path  # 获取包仓库地址（pnpm的仓库不能跨磁盘）
pnpm store prune  # 从store中删除当前未被(硬连接)引用的包来释放store的空间
比较
```

| npm命令            | pnpm等价命令              |
| ------------------ | ------------------------- |
| npm install        | pnpm install 安装全部依赖 |
| npm install 包名   | pnpm add (-D) 包名        |
| npm uninstall 包名 | pnpm remove 包名          |
| npm run 脚本       | pnpm 脚本                 |



# Nvm

## 介绍

> NVM全称[node](https://so.csdn.net/so/search?q=node&spm=1001.2101.3001.7020).js version management ，专门针对node版本进行管理的工具，通过它可以安装和切换不同版本的node.js

## 使用场景

> 我目前的公司有很多项目，其中有一些老项目用的是vue2.5左右了[webpack](https://so.csdn.net/so/search?q=webpack&spm=1001.2101.3001.7020)版本也比较低，只能使用10.16.0左右的node版本，但是也有一些新项目需要使用高版本的node例如14.17.3左右的这时候就可以使用nvm切换node 版本

## 安装

### win

```
https://github.com/coreybutler/nvm-windows/releases
```

### mac

1.执行以下脚本

```bash
sudo curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
```

2.重启电脑 或者是刷新bash文件即可生效

```bash
source ~/.bash_profile
```

## 常用命令

```
nvm list  // 查看现在所有安装的node版本
nvm list available  // 查看nodejs 官方的所有版本
nvm install 版本号  // 下载对应的node版本号
nvm use  // 切换node版本
```



# Nrm

## 介绍

> nrm 是一个 npm 源管理器，允许你快速地在 npm源间切换。

## 安装

```
npm install -g nrm
```

## 常用命令

```
nrm ls  // 查看可选源 星号代表当前使用源
nrm use 源 // 切换源
nrm add（名称）（源） // 添加源
nrm test npm  // 测试速度
```

