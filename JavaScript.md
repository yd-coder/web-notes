<p style="font-size:50px;font-weight:bold;width:100%;text-align:center;color:#fff;text-shadow:0 0 15px">屿东</p>
<p style="text-align:center;color:#aaa;position: relative;top:-10px;text-shadow:0 0 10px">QQ:2395183536</p>

# JavaScrip

## 数据类型

`javascript`是一种弱类型的脚本语言，高级语言，变量的数据类型只有在程序运行中才能根据值判断类型变成相应类型

1. 数字型
2. 字符串型
3. 布尔型
4. null（空值）
5. undefined（定义未赋值）
6. Obeject（复杂数据类型）

`基本数据`类型直接`按值存在栈中`，基础数据类型复制时给值

`引用数据`类型的`数据存在堆内存中`，但是数据指针是存在`栈内存中`，访问引用数据时，先从栈内存中获取指针，通过指针在堆内存中找到数据，引用数据类型赋值时给`地址`

## 简单类型传参

函数的形参也可以看做是一个变量，当我们把一个值类型变量作为参数传给函数的形参时，其实是把变量在栈空间里的值复制了一份给形参，那么在方法内部对形参做如何修改，都不会影响外部变量

```javascript
function fn(a){
    a++;
    console.log(a);
}
var x=10;
fn(x);
console.log(x);//10
```



## 基本包装类型

为了方便操作基本数据类型，`Javascript`还提供了三个特殊的引用类型：`String`，`Number`，`Boolean`.

**基本包装类型**就是把简单数据类型包装成复杂数据类型，这样基本数据类型就有了属性和方法。

## 转义字符

| 转义前  | 转义后 |
| ------- | ------ |
| 换行符  | \n     |
| \       | \\\    |
| '       | \\'    |
| "       | \\"    |
| tab缩进 | \t     |
| 空格    | \b     |

## 声明赋值（易错）

```javascript
var a=b=c=9;
//相当于var a=9;b=9;c=9;
//若要集体声明，需按以下格式写
var a=9,b=9,c=9;
//未声明的赋值的变量是全局变量
function fun(){
    b=9;//全局
}
```



## 块级作用域

> es6以前，`javascript`没有块级作用域，之后新增的。
>
> 块级作用域用{ }包含  如：if{ }，for{ }
>
> 块级作用域变量的范围仅限在{ }里
>
> 注：if,for是语句，不是函数，里面的变量不是局部变量

```javascript
if(3<5) {
    var num = 10;
}
console.log(num);//10

if(3<5) {
    let num = 10;
}
console.log(num);//ReferenceError: a is not defined.
//因为let是es6新增的声明方式，具有块级作用域
```

```javascript
function varTest() {
  var x = 1;
  if (true) {
    var x = 2;  // 同样的变量!
    console.log(x);  // 2
  }
  console.log(x);  // 2
}

function letTest() {
  let x = 1;
  if (true) {
    let x = 2;  // 不同的变量
    console.log(x);  // 2
  }
  console.log(x);  // 1
}
```

## 同步和异步

> **同步任务**都在主线程上执行形成一个执行栈，同步就是先执行完一个再执行下一个，按顺序执行
>
> **异步任务**的回调函数会放到任务队列，可以边等长任务边执行短任务
>
> 1. 普通事件，如click，resize等
> 2. 资源加载，如load，error等
> 3. 定时器，包括setInterval，setTimeout等

## 返回数据类型

**typeof**

> 返回的是字符串

```js
let a = 123;
console.log(typeof a);/* number */
```

## 判断数据类型

**isNaN**

> 用来判断一个变量是否为非数字类型，是数字返回false反之返回ture

```javascript
console.log(isNaN(12));//false
console.log(isNaN("hello world"))//ture
```

**instanceof**

```js
let a = [1, 2, 3];
let b = {};
console.log(a instanceof Array);//false
console.log(b instanceof Object);//true
```

## 数据类型转换

### 转字符串类型

| 方式                     | 说明                         | 案例                                    |
| :----------------------- | ---------------------------- | --------------------------------------- |
| toString()               | 转换字符串                   | var num=1;console.log(num.toString());  |
| String()强制转换         | 转换字符串                   | var num=1;console.log(String(num));     |
| 加号拼接字符串(隐式转换) | 和字符串拼接的结果都是字符串 | var num=1;console.log(num+"我是字符串"; |



### 转数字类型

|                      |                              |                     |
| -------------------- | ---------------------------- | ------------------- |
| 方式                 | 说明                         | 案例                |
| parseInt()函数       | 将string类型转成整数数值型   | parseInt('3')       |
| parseFloat()函数     | 将string类型转成浮点数数值型 | parseFloat('3.14')  |
| Number()强制转换函数 | 将string类型转换为数值型     | Number('12')        |
| js隐式转换(- * /)    | 利用算术运算隐式转换为数值型 | console.log('12'-0) |

### 转换为布尔类型

> `Boolean()`
>
> 代表空，否定的值会被转为false，如:" ",0,NaN,null,undefined
>
> 其余值都会转换为ture

```javascript
console.log(Boolean(''));//false
console.log(Boolean(0));//false
console.log(Boolean(NaN));//false
console.log(Boolean(null));//false
console.log(Boolean(undefined));//false
console.log(Boolean('hello world'));//ture
console.log(Boolean('你好'));//ture
```



## 保留n位小数

**toFixed(n)**

```js
let a = 0.111111;
console.log(a.toFixed(2));//'0.11'
```

## JSON

> 对象转字符串：`JSON.stringify(obj)`
>
> 字符串转对象：`JSON.parse(key)`

```js
let obj = {
    a:1,
    b:2,
    c:3,
    d:4
}
let a = JSON.stringify(obj);
console.log(a);//{"a":1,"b":2,"c":3,"d":4}
let b = JSON.parse(a)
console.log(b);//{ a: 1, b: 2, c: 3, d: 4 }
```

## try...catch

> `try`：捕获try作用域里面的第一个异常
>
> `catch`：抛出try捕获的异常

```js
try {
    alert(Welcome);
    adddlert("Welcome");
} catch (err) {
    console.log(err);//在控制台显示异常，但不会终止程序
}
alert('a')
```

## 字符串

### 字符串不可变

`String`的值是不可变的，只是操作后在不断开辟内存新地址指向它，原来的值的内存地址还在，很占内存空间。

类似于`Java`的`String`，`StringBuffer`，`StringBuilder`.



### indexOf查找字符/串

> 返回字符串中指定文本首次出现的索引，未找到返回-1
>
> 使用`lastIndexOf`倒着查找
>
> 与[search()](#search())功能一样，但search()可以使用正则表达式
>
> 数组也可以使用：[indexOf查找元素](#indexOf查找元素)

```js
let str = 'abcabc';
console.log(str.indexOf('a')); //0，返回当前索引号，它只返回第一个满足条件的索引号
console.log(str.indexOf('d')); //-1，如果找不到字符，则返回-1
console.log(str.indexOf('a', 1)); //3，从下标为1的位置开始查找
/* 倒着查找 */
console.log(str.lastIndexOf('a')); //3，返回最后一个a索引号
console.log(str.lastIndexOf('a', 2)); //0，从下标为2的位置开始查找
```

### 求某个字符出现的位置和次数

```javascript
var str="abcoefoxyozzopp";
var index=str.indexOf('o');
var num=0;
//确定条件用for，不确定用while
while(index !==-1){
    console.log(index);
    num++;
    index =str.indexOf('o',index + 1);
}
console.log(num);
//3,6,9,12,4
```

### 根据位置返回字符

| 方法名            | 说明                                     | 使用                         |
| ----------------- | ---------------------------------------- | ---------------------------- |
| charAt(index)     | 返回指定位置的字符(inedx 字符串的索引号) | str.charAt(0)                |
| charCodeAt(index) | 获取指定位置处字符的ASCII码(index索引号) | str.charCodeAt(0)            |
| str[index]        | 获取指定位置处字符                       | HTML5,IE8+支持和charAt()等效 |



### 统计出现次数最多的字符

```javascript
//利用charAt()遍历这个字符串
//把每个字符都存储给对象，如果对象没有该属性，就为1，有就++
//遍历对象，得到最大值和该字符
var str='abcoefoxyozzopp';
var o={};
for(i=0;i<str.length;i++){
    var chars=str.charAt(i);
    if(o[chars]){
        o[chars]++;
    }else{
        o[chars]=1;
    }
}
console.log(o);
//遍历对象
var max=0;
var ch='';
for(var k in o){
    //k得到的是属性名
    //o[k]得到是是属性值
    if(o[k]>max){
        max=o[k];
        ch=k;
    }
}
console.log('最多的字符是'+ch);
```



### concat合并字符串

> 数组也可以使用：[concat数组合并](#concat数组合并)
>
> 不会修改原字符串

```js
var str1 = '123';
var str2 = '456';
var str3 = '789';
console.log(str1 + str2); //原始方法
console.log(str1.concat(str2, str3));
```

### slice截取字符串

> slice(start,end)从start位置开始，截止到end位置，end取不到
>
> 不会修改原字符串
>
> 数组也可以使用slice：[数组截取](#slice数组截取)

```js
let str = '123456';
console.log(str.slice(1, 4)); //234，通过索引号截取字符索引为1开始4结束
```

### substr截取字符串

> `substr(3, 2)`：从下标3开始截取，截取2个字符
>
> 不会修改原字符串

```js
let str = 'Hello World';
console.log(str.substr(6, 5)); //'World'
```

### 更多截取方式

```js
let str5 = 'Hello World';
console.log(str5.slice(-3)); //rld，将负数与字符串的长度相加，结果作为参数
console.log(str5.substr(-3)); //rld，与slice一致
console.log(str5.substring(-3)); //hello world，自动把负数转成0，截取后面包含自己的所有

console.log(str5.slice(3, -4)); //lo w，将负数与字符串的长度相加，结果作为参数，再从下标3开始截取（包含3），截取到第7个（不包含7）
console.log(str5.substr(3, -4)); //空字符串
console.log(str5.substring(3, -4)); //hel，先将负数转成0，再将参数从小到大排列，再进行从下标0开始截取（包含4），截取到第七个（不包含7）
```

### toString转字符串

> 不会修改原字符串

```js
let a = 123;
console.log(a.toString());
console.log(String(a));
//隐式转换
let b = 123 + ' ';
```

### replace字符替换

> 返回替换后的字符串，且只能替换第一个
>
> 一般用于[正则替换](#replace())
>
> 不会修改原字符串

```js
let str = '123321';
console.log(str.replace('3', '0')); //'120321'
```

### split字符串转数组

> 括号留空，可以直接转数组，可用于字符串替换
>
> 不会修改原字符串

```js
var str = 'a.b.c.d';
console.log(str.split('.')); //["a", "b", "c", "d"]
```

### trim()自动清除空格

> 自动清除空格，换行符，可用于输入框
>
> 不会修改原字符串

```js
let a = '  123   ';
console.log(a.trim())//123
```

### 大小写转换

> 不会修改原字符串

```js
var str = 'Ab123Cd';
console.log(str.toUpperCase()); //AB123CD
console.log(str.toLowerCase()); //ab123cd
```

## 数组

### 数组对象

```javascript
//1.利用数组字面量
var arr=[1,2,3];
console.log(arr[0]);
//2.利用new Array()
var arr1= new Array();
var arr1=new Array(2);//这个2表示数组的长度 2个空的元素
var arr1=new Array(2,3);//等价于[2,3] 表示2个数组元素2和3
```

### 判断是否为数组

```javascript
//1.instanceof 运算符 
var arr=[];
console.log(arr instanceof Array);//true
//2.Array.isArray(参数) H5新增 ie9以上
console.log(Array.isArray(arr));//true
```





### 添加或删除数组元素

| 方法名           | 说明                                                  | 返回值               |
| ---------------- | ----------------------------------------------------- | -------------------- |
| push(参数1...)   | 末尾添加一个或多个元素，注意修改原数组                | 并返回新的长度       |
| pop()            | 删除数组最后一个元素，把数组长度减1无参数，修改原数组 | 返回它删除元素的值   |
| unshift(参数1..) | 向数组的开头添加一个或更多元素，注意修改原数组        | 并返回新的长度       |
| shift()          | 删除数组的第一个元素，数组长度减1无参数，修改原数组   | 并返回第一个元素的值 |

#### push尾部添加元素

> 返回添加的元素，原数组会被修改

```js
let arr = [1, 2];
console.log(arr.push(3,4)); //4
console.log(arr);// [1,2,3,4]
```

#### pop删除尾部元素

> 返回删除的元素

```js
let arr = [1, 2, 3,4,5,6];
console.log(arr.pop()); //6
console.log(arr); //[1, 2, 3, 4, 5]
```

#### unshift头部添加元素

> 返回添加的元素

```js
let arr = [2, 3];
console.log(arr.unshift(1)); //1
console.log(arr); //[1,2,3]
```

#### shift删除头部元素

> 删除数组最后一个元素，返回删除的元素

```js
let arr = [1, 2, 3, 4, 5, 6];
console.log(arr.pop()); //1
console.log(arr); //[2, 3, 4, 5, 6]
```



### 数组排序

| 方法名    | 说明                         | 是否修改原数组                     |
| --------- | ---------------------------- | ---------------------------------- |
| reverse() | 颠倒数组中元素的顺序，无参数 | 该方法会改变原来的数组，返回新数组 |
| sort()    | 对数组的元素进行排序         | 该方法会改变原来的数组，返回新数组 |

#### reverse数组翻转

> 返回翻转后的数组，原数组会被修改

```js
let arr = [1, 2];
console.log(arr.reverse()); //[2,1]
console.log(arr); //[2,1]
```

#### sort数组排序

> 原数组会被修改（内置方法是按ASCII来比较的，所以只能直接比较个位数，多位数需要更改）

```js
let arr = [13, 4, 77, 1, 7];
console.log(arr.sort());//1,13,4,7,77
arr.sort(function (a, b) {
    return a - b; //升序，如果想倒序，则b - a;
});
console.log(arr); //[1, 4, 7, 13,77 ]
```





### toString数组转字符串

> 转换后的字符串以逗号分隔

```js
let arr = [1, 2, 3, 4];
console.log(arr.toString()); //1，2，3，4
```

### join数组转字符串

> 可自定义分隔符，空默认逗号
>
> 另外还有ES6的[利用扩展运算符字符串转数组](#字符串转数组)

```js
let arr = [1, 2];
console.log(arr.join("-")); //1-2 以-分割
```

### concat数组合并

> 连接两个或多个数组，不影响原数组
>
> 遗憾的是已被ES6的[利用扩展运算符数组合并](#数组合并)取代
>
> 字符串也可以使用：[concat合并字符串](#concat合并字符串)

```js
let arr = [1, 2];
let arrs = [1, 2];
console.log(arr.concat(arrs)); //[1,2,1,2]
```

### slice数组截取

> 数组截取slice(begin,end)，返回被截取的数组
>
> 不会修改原数组
>
> 字符串也可以使用slice：[字符串截取](#slice截取字符串)

```js
let arr = [1, 2, 3, 4, 5];
console.log(arr.slice(1, 4)); //[2,3,4];
```

### splice删除范围元素

> 数组删除splice(开始索引，删除个数)
>
> 返回被删除的数组元素（数组形式），原数组会被修改
>
> 数组插入splice（开始索引，0，"插入元素"）
>
> 数组替换splice(开始索引，1，“替换元素”)

```js
let arr = [1, 2, 3, 4, 5, 6];
console.log(arr.splice(2, 3)); //[3,4,5] 删除第三个元素，共删除3个，
console.log(arr); //[1,2,6]
```

### forEach遍历数组

> 注意：请勿使用`forin`，性能极差，推荐`forEach`

```js
let arr = [1, 2, 3];
arr.forEach((value, index, array)=> {
    //如果只填一个值，则是value
    console.log("数组元素：" + value);
    console.log("索引号：" + index);
    console.log("数组：" + array);
});
/*
数组元素：1
索引号：0
数组：1,2,3
数组元素：2
索引号：1
数组：1,2,3
数组元素：3
索引号：2
数组：1,2,3
 */
```

### map遍历数组

> 拥有`foreach`一模一样的功能，且`map`方法可以先遍历，再整合返回遍历后的数组

```js
let arr1 = [
    { name: "AAA", age: 12 },
    { name: "BBB", age: 13 },
    { name: "CCC", age: 14 },
];

let arr2 = arr1.map(value => {
    return {
        name: value.name,
        age: value.age + 10,
    };
});

console.log(arr2);
/*
[
  { name: 'AAA', age: 22 },
  { name: 'BBB', age: 23 },
  { name: 'CCC', age: 24 }
]
*/
```

### for...of遍历数组

> `keys()`：返回下标
> `values()`：返回数组元素，可以省略不写
> `entries()`：返回下标及元素

```js
let arr = ["a","b","c","d"];
for(k of arr.keys()){
    console.log(k);
}
/*
0
1
2
3
*/
for(v of arr.values()){
    console.log(v);
}
/*
a
b
c
d
*/
for([k,v] of arr.entries()){
    console.log(k,v);
}
/*
0 a
1 b
2 c
3 d
*/
```

### filter筛选数组

> 返回符合条件的数组元素（数组形式），且不会修改原数组
>
> `filter`和`forEach`一样有相同的三个参数
>
> 具有map的特性：可以先遍历，再整合返回遍历后的数组

```js
let arr1 = [
    { name: 'AAA', age: 12 },
    { name: 'BBB', age: 13 },
    { name: 'CCC', age: 14 },
];

let arr2 = arr1.filter((value) => {
    return value.age < 14;
});
console.log(arr2); //[{name: 'AAA', age: 12}, {name: 'BBB', age: 13}]
```

### findIndex查找元素

> 返回第一个符合条件的数组成员的位置，如果所有成员都不符合条件，则返回-1

```js
let arr1 = [1, 2, 3, 4];
let arr2 = arr1.findIndex(value => {
    return value > 3;
});
console.log(arr2); // 3
```

### includes检测数组

> 判断一个数组是否包含一个指定的值，如果是返回 true，否则false

```js
let arr = ['runoob', 'google', 'taobao'];
arr.includes('runoob'); // true 
arr.includes('baidu'); // false
```

### some检测数组

> 用于检测数组中的元素是否存在满足指定条件的元素，若存在返回`true`，否则返回`false`

```js
let arr = [1, -56, 80, -5];
let flag = arr.some(n => n > 0);
console.log(flag); //true
```

### every检测数组

> 用于检测数组中所有元素是否都符合指定条件，若符合返回`true`，否则返回`false`

```js
let arr = [1, 2, 3, 4];
let flag = arr.every(item => {
    return item < 0;
});
console.log(flag); //false
```

### 数组去重

> 原理：遍历数组，然后拿着旧数组去查询新数组，如果该元素没有出现过就添加，反之不添加

```javascript
function unique(arr){
    var newArr = [];
    for (var i=0;i<arr.length;i++){
        if(newArr.indexOf(arr[i]) === -1){
            newArr.push(arr[i]);
        }
    }
    return newArr;
}
var demo=unique(['c','a','z','a','x','a','x','c']);
console.log(demo);//['c','a','z','x']
```

### indexOf查找元素

> 只返回第一个满足条件的索引，找不到则返回-1
>
> 字符串也可以使用：[indexOf查找字符/串](#indexOf查找字符/串)

```js
var arr = ['red', 'orange', 'yellow', 'green', 'red', 'orange'];
console.log(arr.indexOf('orange')); //1
console.log(arr.indexOf('pink')); //-1
console.log(arr.indexOf('orange', 2)); //5，从下标为2的位置开始查找
```

### lastIndexOf查找元素

> 从后开始找，只返回第一个满足条件的索引，找不到则返回-1（索引是不会反的）

```javascript
/* 倒着查找 */
var arr = ['red', 'orange', 'yellow', 'green', 'red', 'orange'];
console.log(arr.lastIndexOf('red')); //4，返回最后一个red数组下标
console.log(arr.lastIndexOf('orange', 4)); //1，从下标为2的位置开始查找
```

### find查找元素

> 找出第一个符合条件的元素

```js
let arr1 = [1, 2, 3, 4];
let arr2 = arr1.find(value => {
    return value > 3;
});
console.log(arr2); //4
```

### flat深度遍历合并

> 将多维数组遍历元素合并成一个数组

```js
let arr1 = [1, [2, [3, [4, [5, [6]]]]]];
let arr2 = arr1.flatmap(Infinity); //此处为无限深度
console.log(arr2); //[1, 2, 3, 4, 5, 6]
```

### flatmap遍历合并

> 用法和`flat`一致，但效率比`flat`高

```js
let arr = [1, 2, 3, 4];
let arr1 = arr.flatMap((x) => [x]);
console.log(arr1); //[1, 2, 3, 4]
//只会将 flatMap 中的函数返回的数组 “压平” 一层

let arr2 = arr.flatMap((x) => [x]);
console.log(arr2); //[1, 2, 3, 4]
```

### Array.from转数组

> 遗憾的是被ES6的[字符串转数组](#字符串转数组)，虽然也可以将对象转为数组，但过程太过复杂

```js
let str = "12345";
console.log(Array.from(str)); //[ '1', '2', '3', '4', '5' ]
```

```js
let obj = {
    0: 1,
    1: 2,
    2: 3,
    length: 3
}
console.log(Array.from(obj));
```

### Object.keys属性转数组

> 对象属性转数组

```js
//Object.keys(obj)  传入的是对象：返回所传对象的key值，
let obj = {
    id: 1,
    name: "lyb",
    age: 20,
    job: "web"
};
let arr = Object.keys(obj);
console.log(arr); //[ 'id', 'name', 'age', 'job' ]
```

### 求最大/小值

> 使用了[扩展运算符](#扩展运算符)

```js
let arr =[1,5,4,6,3,7,2]
console.log(Math.max(...arr)); //6
console.log(Math.min(...arr)); //1
```

### 判断是否为数组

> 来自 [判断数据类型](#判断数据类型)

```js
let arr = [1, 2, 3];
console.log(arr instanceof Array);//false
console.log(Array.isArray(arr)); //第二种方法
```

### reduce高级语法合集

> 返回所有元素相加的和，为了传进来的数组为空而报错，可以在函数后面加默认参数0

#### 元素出现次数统计

```js
let a = '122333444455555';
let b = [1, 2, 2, 3, 3, 3, 4, 4, 4, 4, 5, 5, 5, 5, 5];

function fn(strorarr) {
    if (typeof strorarr == 'string') {
        strorarr = [...strorarr];
    }
    let obj = strorarr.reduce((items, item) => {
        return (items[item] = (items[item] || 0) + 1), items;
    }, {});
    console.log(obj);
}
fn(a); //{'1': 1, '2': 2, '3': 3, '4': 4, '5': 5}
fn(b); //{'1': 1, '2': 2, '3': 3, '4': 4, '5': 5}

```

> 相似方法，但能获取坐标

```js
function find(a, b) {
    let index = a.indexOf(b); //获取第一次出现的索引号
    let arr = [];
    while (index !== -1) {
        arr.push(index); //将索引号添加进数组
        index = a.indexOf(b, index + 1); //从上次查找结束的索引号位置+1继续查找
    }
    console.log('在' + a + '中，' + b + '出现了' + arr.length + '次,' + '坐标为' + arr.join(','));
}
find('122333444455555', 3); //单独使用
console.log('全部使用');

function found(arr) {
    let newArr = [...new Set(arr)];
    newArr.push();
    for (let i = 0; i < newArr.length; i++) {
        find(arr, newArr[i]);
    }
}

found([1, 2, 3, 4, 5, 6]); //查找每一个元素的出现次数及坐标
```

#### 数据分类汇总

> 根据对象属性名进行分类

```js
function Group(arr = [], key) {
    return key ? arr.reduce((t, v) => (!t[v[key]] && (t[v[key]] = []), t[v[key]].push(v), t), {}) : {};
}
const arr = [{
    area: 'GZ',
    name: 'YZW',
    age: 27,
}, {
    area: 'GZ',
    name: 'TYJ',
    age: 25,
}, {
    area: 'SZ',
    name: 'AAA',
    age: 23,
}, {
    area: 'FS',
    name: 'BBB',
    age: 21,
}, {
    area: 'SZ',
    name: 'CCC',
    age: 19,
}, ];
console.log(Group(arr, "area"));
/*
{
  GZ: [
    { area: 'GZ', name: 'YZW', age: 27 },
    { area: 'GZ', name: 'TYJ', age: 25 }
  ],
  SZ: [
    { area: 'SZ', name: 'AAA', age: 23 },
    { area: 'SZ', name: 'CCC', age: 19 }
  ],
  FS: [ { area: 'FS', name: 'BBB', age: 21 } ]
}
*/
```

#### 多维数组按数据类型拆解

```js
function Unzip(arr = []) {
    return arr.reduce(
        (items, item) => (item.forEach((w, i) => items[i].push(w)), items),
        Array.from({ length: Math.max(...arr.map(item => item.length)) }).map(v => [])
    );
}
 
const arr = [["a", 1, true], ["b", 2, false]];
Unzip(arr); 
// [["a", "b"], [1, 2], [true, false]]
```

#### 字符串翻转

```js
function ReverseStr(str = "") {
    return str.split("").reduceRight((items, item) => items + item);
}
 
const str = "reduce最牛逼";
ReverseStr(str); // "逼牛最ecuder"
```

#### 累加累乘

```js
function Accumulation(...vals) {
    return vals.reduce((t, v) => t + v, 0);
}
 
function Multiplication(...vals) {
    return vals.reduce((t, v) => t * v, 1);
}
 
Accumulation(1, 2, 3, 4, 5); // 15
Multiplication(1, 2, 3, 4, 5); // 120
```

#### 返回指定对象的键值

```js
function GetKeys(obj = {}, keys = []) {
    return Object.keys(obj).reduce((items, item) => (
        keys.includes(item) && (items[item] = obj[item]), items
    ), {});
}

const target = {
    a: 1,
    b: 2,
    c: 3,
    d: 4
};
const keyword = ["a", "d"];
console.log(GetKeys(target, keyword)); //{a: 1, d: 4}
```

#### 数组转对象

```js
const people = [
    { area: 'GZ', name: 'YZW', age: 27 },
    { area: 'SZ', name: 'TYJ', age: 25 },
];
const map = people.reduce((t, v) => {
    const { name, ...rest } = v;
    t[name] = rest;
    return t;
}, {}); // {YZW: {area: 'GZ', age: 27 }, TYJ: { area: 'SZ', age: 25}}
```

## 对象

### 对象赋值(易错)

> object对象类型的赋值应该是属于地址传值，所有被赋值变量的对象内部发生改变，原对象也会发生改变



### 创建对象

> 创建对象的三种方式
>
> 1. 利用`字面量`创建对象
> 2. 利用`new Object`创建对象
> 3. 利用`构造函数`创建对象

```javascript
//字面量创建
var obj={
    name="小明"，
    age=18,
    sayHi:function(){
        console.log('hi');
    }
}
//利用new boject创建
var obj=new Object();
obj.name='小明'；
obj.age=18;
obj.sex='男';
obj.sayHi=function(){
    console.log('hi');
}
//利用构造函数创建对象(解决其它方法一次只能创建一个对象的问题)
function Star(uname,age,sex){
    this.name=uname;
    this.age=age;
    this.sex=sex;
    this.sing=function(sang){
        console.log(sang);
    }
}
var ldh=new Star('刘德华',18,'男');
var zxy=new Star('张学友',19,'男');
ldh.sing('冰雨');
//1.构造函数名字首字母要大写
//2.我们构造函数不需要return就可以返回结果
//3.我们调用构造函数必须使用new
//4.new 构造函数可以在内存中创建了一个空的对象，this就会指向这个新对象，接着执行构造函数里面的代码，给新对象添加属性和方法，最后返回这个新对象（不用return）
```



### 调用对象

```javascript
var obj={
    name="小明"，
    age=18,
    sayHi:function(){
        console.log('hi');
    }
}
//调用对象的属性采用对象名.属性名
console.log(obj.name);
//调用属性还有一种方法 对象名['属性名']
console.log(obj['age']);
//调用对象的方法采用对象名.方法名()
obj.sayHi();
```

### 遍历对象

> `for...in`用于对数组或者对象的属性进行循环操作

```javascript
 for(变量 in 对象){
     //demo
 }
var obj={
    name:'小明',
    age=18,
    sex:'男'
}
for(var k in obj){
    console.log(k);//k变量输出得到的是所有属性值
    console.log(obj[k]);//输出得到的是属性值
} 
```

### 内置对象

> `Javascript`中的对象分三种：自定义对象，内置对象，浏览器对象
>
> `内置对象`就是指`js`语言自带的一些对象，这些对象供开发者使用，并提供了一些常用的或是基本而必要的功能(属性和方法)   
>
> 具体`Math()`,`Date()`请跳转至函数章

# Web API

## DOM

### console.dir

> 打印我们返回的元素对象，更好的查看里面的属性和方法



### 获取元素

| 方法                                   | 说明                                                         |
| -------------------------------------- | ------------------------------------------------------------ |
| document.getElementById()              | 通过id获取元素，返回的是一个元素对象                         |
| document.getElementsByTagName()        | 通过标签名获取元素，返回获取对象元素的集合(以伪数组形式)     |
| (父元素)element.getElementsByTagName() | 获取某个元素内部所有指定标签名元素(父元素必须是单个对象不能是伪数组) |
| document.getElementsByClassName()      | ES5+根据类名返回元素对象集合(以伪数组形式)                   |
| document.querySelector()               | ES5+返回指定选择器(id，类等等)的第一个元素对象               |
| document.querySelectorAll()            | ES5+返回指定选择器(id，类等等)的所有元素对象集合(伪数组形式) |
| document.body                          | 获取body元素对象                                             |
| document.documentElement               | 获取html元素对象                                             |



### 注册绑定事件方式

**传统注册方式**

```javascript
//绑定事件唯一行，只能绑定一个事件
<button onclick="alert('hi')"></button>
btn.onclick=function(){}
```

**方法监听注册方式**

```javascript
//IE9+，可绑定多个事件，同一个元素同一个事件可以添加多个监听器
//里面事件类型是字符串加引号，而且不带on
btn.addEventListener('click',function(){
    alert('hi');
})
```

### 删除解绑事件方式

**传统方式解绑**

```javascript
btn.onclick=function(){
    alert('hi');
    btn.onclick=null;
}
```

**监听事件解绑**

```javascript
btn.addEventListener('click',fn);
function fn(){
    alert('hi');
    btn.removeEventListener('click',fn);
}
```



### 事件

以下为addEventListener情况下事件，其它情况需加on

| 事件                         | 说明                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| change                       | 内容发生改变触发，用于事件绑定，`onchange`用于`<select>`下拉菜单 |
| DomContentLoaded(window专用) | 等DOM加载完毕触发(不包含图片、CSS等)IE9+支持                 |
| input                        | 输入时触发                                                   |
| keyup                        | 某个键盘按键松开时触发，不区分输入的大小写                   |
| keydown                      | 某个键盘按键按下时持续触发，不区分输入的大小写               |
| keypress                     | 和`onkeydown`一样，但不识别功能键，Ctrl，shift，方向键，但会区分大小写，并能返回不同的ASCLL码值 |
| keyCode                      | 返回当前按下键的ASCLL码值，只有`onkeypress`能够使用          |
| load(window专用)             | 等DOM加载完毕触发(包含图片、CSS等)就可以执行，传统注册window.onload只生效一个 |
| mousewheel                   | 滚轮事件                                                     |
| mouseover                    | 鼠标移入事件，会冒泡                                         |
| mouseenter                   | 鼠标移入事件，不会冒泡                                       |
| mouseout                     | 鼠标移出事件，会冒泡                                         |
| mouseleave                   | 鼠标移出事件，不会冒泡                                       |
| mousemove                    | 鼠标滑动事件                                                 |
| reset                        | 当重置表单触发                                               |
| resize(window专用)           | 只要浏览器窗口尺寸发生变化，它就会触发，用于响应式布局，`window.innerWidth`获取浏览器宽度，`window.innerHeight`获取浏览器高度，配合此事件使用 |
| submit                       | 当提交表单触发                                               |
| select()                     | 输入框文字选中状态                                           |
| scroll                       | 滚动条滚动                                                   |
| transitionend                | 监听过渡动画是否结束的事件                                   |



### 排他思想tab切换栏

```javascript
var lis=document.querySelector('.tab_list').querySelectorAll('li');
//for循环绑定点击事件
for(var i=0;i<lis.length;i++){
    lis[i].onclick=function(){
        //干掉所有人，清除li的class类
        for(var i=0;i<lis.length;i++){
            lis[i].className='';
        }
        //再留下点击触发的自己
        this.className='current';
    }
}
```

### H5自定义属性

> H5规定自定义属性data-开头做为属性名并且赋值
>
> 比如`<element data-index="1"></element>`



### 内容操作

| 操作              | 说明                                             |
| ----------------- | ------------------------------------------------ |
| element.innerText | 不识别html标签，仅当成文本插入，去除空格和换行   |
| element.innerHTML | 识别html标签，可以插入标签和文本，保留空格和换行 |



### 属性值操作

| 操作                                     | 说明                                                         |
| ---------------------------------------- | ------------------------------------------------------------ |
| element.属性                             | 获取属性设置属性值(主要内置属性)如`element.id`，`element.title`，`img.src`但只能获取内置属性，如`id` |
| element.style                            | 行内样式操作，样式采取驼峰命名法(如`fontSize`,`backgroundColor`)产生的是行内样式，css权重比较高 |
| element.className                        | 更改元素的类，会覆盖原先的类，要想保留原先的加上即可`="agoClass afterClass"` |
| element.classList.add(' ')               | (css新属性，兼容性不好)添加类名，不会覆盖原来的类名          |
| element.classList.remove(' ')            | (css新属性，兼容性不好)删除类名                              |
| element.classList.toggle(' ')            | (css新属性，兼容性不好)切换类名，有变无，无变有              |
| element.getAttribute("属性")             | 可获取任何属性值(包括自定义属性)                             |
| element.setAttribute("属性","值")        | 设置或删除属性值(主要自定义属性)                             |
| element.dataset.index`/`dataset['index'] | 获取自定义属性值，H5新增，ie11+支持，dataset相当于一个集合，对象 |
| element.removeAttribute("属性")          | 移除属性                                                     |



### 选择器操作

> 如果需要更改多个属性，则可使用这个

| 操作                                 | 说明                                   |
| ------------------------------------ | -------------------------------------- |
| element.className = '选择器名'       | 修改样式                               |
| element.classList.add('选择器名')    | 添加样式                               |
| element.classList.remove('选择器名') | 删除样式                               |
| element.classList.toggle('选择器名') | 存在则删除，不存在则添加，用于样式切换 |



### 节点操作

| 操作                                         | 说明                                             |
| -------------------------------------------- | ------------------------------------------------ |
| element.parentNode                           | 获取父节点                                       |
| element.childNodes                           | 获取所有子节点的集合(包括元素节点，文本节点)     |
| element.children                             | 获取子元素节点，IE9+                             |
| element.children[0]                          | 获取第一个子元素节点，兼容性更好                 |
| element.children[element.children.length-1]  | 获取最后一个子元素节点，兼容性更好               |
| element.firstElementChild                    | 获取第一个子元素节点，IE9+                       |
| element.lastElementChild                     | 获取最后一个子元素节点,IE9+                      |
| element.nextElementSibling                   | 兄弟节点，下一个元素节点,IE9+                    |
| element.previousElementSibling               | 兄弟节点，上一个元素节点,IE9+                    |
| document.createElement('element')            | 创建节点                                         |
| element.appendChild(节点名)                  | 追加节点                                         |
| element.insertBefore(element,ul.children[0]) | 添加指定位置元素前面                             |
| element.removeChild(element.children[0])     | 删除子节点                                       |
| element.remove()                             | 删除自身                                         |
| element.cloneNode(）                         | 浅拷贝，只克隆标签不克隆内容                     |
| element.cloneNode(true)                      | 深拷贝，克隆标签及内容，克隆后需要添加节点到页面 |

> `insertAdjacentHTML('position'，'text')`可直接把字符串格式元素添加父元素中，且只能是字符串

| position    | 说明                 |
| ----------- | -------------------- |
| beforebegin | 插入在自身最前面     |
| afterend    | 插入在自身最后面     |
| afterbegin  | 插入在自身内部最前面 |
| beforeend   | 插入在自身内部最后面 |



### 动态创建元素

- document.write()
- element.innerHTML
- document.creatElement()

**区别**

1. document.write是直接写入页面的内容刘，但是文档流执行完毕，则它会导致页面全部重绘
2. innerHTML是将内容写入某个DOM节点，不会导致页面重绘
3. innerHTML创建多个元素效率高(不要拼接字符串，采取数组形式拼接)，结构稍微复杂
4. creatElement()创建多个元素效率稍低一点，但是结构清晰

```js
let array[];
for(let i = 0; i < 1000; i++) {
    array.push("<element>创建元素</element>");
}
document.body.innerHTML = array.join(" ");//join将数组转换成字符串
```

### 捕获冒泡

> 捕获：从父到子依次触发事件
>
> addEventListener()括号内末尾添加true，则为捕获

> 冒泡：从子到父依次触发事件
> addEventListener()括号内末尾添加false，则为冒泡
> 默认为冒泡



### 事件对象

| 属性                | 说明                                                         |
| ------------------- | ------------------------------------------------------------ |
| e.target            | 返回触发事件对象                                             |
| e.srcElement        | 返回触发事件对象，IE6-8                                      |
| e.type              | 返回不带on的事件类型                                         |
| e.preventDefault()  | 阻止默认事件(让链接不跳转)                                   |
| e.stopPropagation() | 阻止冒泡(当父子元素都有事件监听，点击子元素后父元素也会触发事件，将阻止冒泡写给子元素即可阻止父元素触发) |



### target和this的区别

```javascript
//target返回的是绑定事件对象 this返回的是触发事件对象
var ul=document.querySelector('ul');
ul.addEventListener('click',function(e){
    console.log(this);//输出ul
    console.log(e.target);//输出li
})
```



### 禁止鼠标右键

```js
document.addEventListener('contextmenu', (e) => {
    e.preventDefault()
})
```

### 禁止鼠标选中

```js
document.addEventListener("selectstart",function(e){
	e.preventDefault()
})
```

## BOM

### 定时器

#### setTimeout

```javascript
window.setTimeout(调用函数，延时时间);
//只回调一次
//window可以省略
//延时时间为毫秒，省略默认为0
//回调函数
//定时器多的时候可以赋值一个标识符
```

#### setInterval

```javascript
window.setInterval(回调函数,[间隔毫秒数]);
//每隔一段时间回调一次
//window可以省略
//延时时间为毫秒，省略默认为0
//回调函数
//定时器多的时候可以赋值一个标识符
```

#### 清除定时器

```javascript
window.clearTimeout(timeoutID);
//window可以省略
//里面的参数就是定时器的标识符

window.clearInterval(IntervalID);
//window可以省略
//里面的参数就是定时器的标识符
```



### location对象

| 用法                     | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ |
| location.href＝"链接"    | 跳转页面，无法后退                                           |
| location.assign("链接")  | 记录浏览历史，跳转页面，可以后退（重定向页面）               |
| location.replace("链接") | 替换当前页面，因为不记录历史，所以不能后退页面               |
| location.reload()        | 重新加载页面(刷新页面)在括号内加true强制刷新，表示重新从服务器获取资源数据，而不是从本地缓存获取 |
| location.host            | 返回主机(域名)                                               |
| location.port            | 返回端口号，如果未写则返回空字符                             |
| location.pathname        | 返回路径                                                     |
| location.search          | 返回参数(重点记住)                                           |
| location.hash            | 返回片段，#后面内容，常见于链接、锚点                        |
| window.open("链接")      | 跳转到新窗口打开链接                                         |
| window.close()           | 关闭当前标签                                                 |



### navigator对象

> `navigator`对象包含有关浏览器的信息，它有很多属性，我们最常用的是`userAgent`，该属性可以返回客户端的浏览器信息



### history对象

| 用法              | 说明                                                  |
| ----------------- | ----------------------------------------------------- |
| history.back()    | 后退功能                                              |
| history.forward() | 前进功能                                              |
| history.go(参数)  | 前进后退功能，参数是1前进1个页面，参数是-1后退1个页面 |

### 本地存储

#### 临时存储

> 关闭浏览器数据就会丢失

| 用法                                 | 说明     |
| ------------------------------------ | -------- |
| sessionStorage.setItem('key',value); | 存储数据 |
| sessionStorage.getItem('key');       | 获取数据 |
| sessionStorage.removeItem('key');    | 删除数据 |
| sessionStorage.clear();              | 清空数据 |

#### 永久存储

> 数据永久保存

| 用法                                | 说明     |
| ----------------------------------- | -------- |
| localStorage.setItem("命名",value); | 存储数据 |
| localStorage.getItem(''命名'');     | 获取数据 |
| localStorage.removeItem(''命名'');  | 删除数据 |
| localStorage.clear();               | 清空数据 |



## 坐标位置三大系列

### 坐标系列

| 用法      | 说明                                          |
| --------- | --------------------------------------------- |
| e.clientX | 获取鼠标在浏览器可视窗口的X坐标，会忽略滚动条 |
| e.clientY | 获取鼠标在浏览器可视窗口的X坐标，会忽略滚动条 |
| e.pageX   | 获取鼠标在文档页面的X坐标，不会忽略滚动条     |
| e.pageY   | 获取鼠标在文档页面的Y坐标，不会忽略滚动条     |
| e.screenX | 获取鼠标相对电脑屏幕的X坐标                   |
| e.screenY | 获取鼠标相对电脑屏幕的Y坐标                   |



### 轮播图节流阀

> 防止轮播图按钮连续点击造成播放过快。
>
> **作用：当上一个函数动画内容执行完毕再去执行下一个动画，让事件无法连续触发。**

```javascript
var flag = true;  //flag作为节流阀开关
element.addEventListener('click',function() {
        if(flag) {
            flag = false;  //关闭节流阀（锁住函数）
            if (num == ul.children.length - 1 ) { 
                ul.style.left = 0;
                num =  0;  
            }
            num ++;
            animate(ul, - num * focusWidth, function() {
                flag = true;  //动画函数执行后执行回调函数来开启节流阀（解锁函数）
            });
            }
    });
```



### offset系列

> `offest`翻译过来就是偏移量，我们使用offset系列相关属性可以动态得到该元素的位置（偏移），大小。可以获取任意样式表中的样式值，获取数值没有单位，包含padding，border的值，只能获取，不能赋值

| 用法                 | 说明                                                         |
| -------------------- | ------------------------------------------------------------ |
| element.offsetParent | 返回带有定位的父亲，没有定位则返回body                       |
| element.offsetTop    | 返回元素相对有定位的父元素上方的偏移，若父元素没有定位，则以body为准，返回数值不带单位 |
| element.offsetLeft   | 返回元素相对有定位的父元素左边的偏移，若父元素没有定位，则以body为准，返回数值不带单位 |
| element.offsetWidth  | 返回自身包括padding、边框、内容区的宽度，返回数值不带单位    |
| element.offsetHeight | 返回自身包括padding、边框、内容区的高度，返回数值不带单位    |

```js
//获取鼠标在盒子内的坐标
X = e.pageX - this.offsetLeft
Y = e.pageY - this.offsetTop
```

### offset和style的区别

| offset                                    | style                                     |
| ----------------------------------------- | ----------------------------------------- |
| 可以得到任意样式表中的样式值              | 只能得到行内样式有的样式值                |
| 获取的数值是没有单位的                    | 获取的数值是有单位的字符串                |
| offsetWidth包括padding+border+width       | style.width不包括padding和border          |
| offsetWidth等是可读属性，只能获取不能赋值 | style.width是可读写属性，可以获取也能赋值 |



### client系列

> `client`翻译过来就是客户端，我们使用client系列的相关属性来获取元素可视区的相关信息，可以动态的得到元素边框大小，元素大小

| client系列属性       | 作用                                                      |
| -------------------- | --------------------------------------------------------- |
| element.clientTop    | 返回元素上边框大小                                        |
| element.clientLeft   | 返回元素左边框大小                                        |
| element.clientWidth  | 返回自身包括padding，内容宽度，不含边框，返回数值不带单位 |
| element.clientHeight | 返回自身包括padding，内容高度，不含边框，返回数值不带单位 |



### scroll系列

> 配合滚动事件scroll
>
> 注意：元素被卷去的头部是element.scrollTop，页面被卷去的头部是window.pageYOffset

| 用法                 | 说明                                                         |
| -------------------- | ------------------------------------------------------------ |
| element.scrollTop    | 返回滚动条向下拖动，盒子内的内容被滑动至上方所隐藏的高度     |
| element.scrollLeft   | 返回滚动条向左拖动，盒子内的内容被滑动至右方所隐藏的宽度     |
| element.scrollWidth  | 返回元素滚动条内自身实际的宽度，包含里面溢出的内容溢出的宽度，不包含边框 |
| element.scrollHeight | 返回元素滚动条内自身实际的高度，包含里面溢出的内容溢出的高度，不包含边框 |
| window.pageYOffset   | 获取HTML整个页面被卷去隐藏的滚动距离高度                     |
| window.pageXOffset   | 获取HTML整个页面被卷去隐藏的滚动距离长度                     |
| window.scroll(x，y)  | 设置滚动坐标,滚动窗口到文档中特定位置                        |



## 移动端修相关

### 事件

> PS：当触发touchend，不会有前两个，只会有最后一个，当有多个手指的时候，可以采用数组形式获取，如e.targetTouches[0]

| 事件             | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| touchstart       | 手指触摸触发，类似点击事件                                   |
| touchmove        | 手指触摸并滑动触发，类似mousemove事件，所以不需要像电脑版那样删除移动事件 |
| touchend         | 手指离开触发，类似鼠标弹起事件，由于此事件无法使用事件对象，如果想获取坐标，需要使用`e.touches[0]`来获取，如`e.touches[0].pageX`，并且touchend无法获取坐标，所以需要在全局作用域定义event，在touchmove里，将事件对象e赋值给event，最后在touchend里面使用event即可 |
| touchenter       | 移动的手指进入                                               |
| touchleave       | 移动的手指离开                                               |
| touchcancel      | 拖动中断触发                                                 |
| e.altKey         | 如果按住Alt触发的事件，则返回true                            |
| e.touches        | 正在触摸屏幕的所有手指列表及信息，包含手指个数和每个手指的坐标 |
| e.targetTouches  | 正在触摸当前元素的所有手指列表及信息，同上，重点记住这个     |
| e.changedTouches | 手指状态发生了改变的列表                                     |

# 函数

## 函数的返回值

> `return`具有终止函数的作用；
>
> `return`语句之后的代码不被执行；
>
> `return`只能返回一个值，有多个值只返回最后一个的值
>
> （若要返回多个值，以数组的形式返回）；
>
> 函数都有返回值，但若函数没有`return`，返回undefined；

## arguments的使用

> 当我们不确定有多少个参数传递的时候，可以用arguments来获取。在`javascript`中，arguments实际上它是当前函数的一个内置对象。所有函数都内置了一个arguments对象，arguments对象中储存了传递的所有实参。

**arguments展示形式是一个伪数组**，因此可以进行遍历。伪数组具有以下特点：

- 具有length属性
- 按索引方式储存数据
- 不具有数组的push，pop等方法

## 作用域链（就近原则）

> 当函数嵌套，内部函数执行时，内部函数所打印的变量以最近的开始向链外面寻找

## 预解析（变量提升）

> js引擎运行js分为两步：预解析>>代码执行
>
> 预解析js引擎会把js里面所有的`var` 和`function`提
>
> 升到当前作用域最前面

```javascript
//这种形式叫函数表达式，不会像函数声明一样提升
var fun = function fun(){
    comsole.log()
}
```

```javascript

let a = 100;

function b() {
    console.log(a);
    let a = 200;//变量会提升到当前作用域顶层
    console.log(a);
} 
//相当于以下代码
let a;
function b() {
    let a;
    console.log(a); //undefined
    a = 200;
    console.log(a); //200
}
a = 100;
b();
```

## Math方法

> `Math`不是构造函数不用`new`调用，作为内置对象，直接调用即可

| 用法                  | 说明                 |
| --------------------- | -------------------- |
| Math.max( 1, 3, 4, 2) | 最大值4              |
| Math.min(1, 3, 4, 2)  | 最小值1              |
| Math.abs(-10)         | 绝对值10             |
| Math.floor(1.9)       | 向下取整1            |
| Math.ceil(1.1)        | 向上取整2            |
| Math.round(1.5)       | 四舍五入(.5往大的取) |
| Math.random()         | 返回随机数(0=<,<1 )  |

## 封装Math方法

```javascript
封装一个自己的数字对象
var myMath={
    PI=3.141592653,
    max:function(){
        var max=arguments[0];
        for(var i=1;i<arguments.length;i++){
            if(arguments[i]>max){
                max=arguments[i];
            }
        }
        retuen max;
    }
    min:funtion(){
        var min=arguments[0];
        for(var i=1;i<grguments.length;i++){
            if(aeguments[i]<min){
                min=arguments[i];
            }    
        }
        return min;
    }
}
console.log(myMath.PI);//3.141592653
console.log(myMath.max(1,5,9));//9
console.log(myMath.min(1,5,9));//1
```

## Date方法

> `Date`是构造函数，需要`new`来创建对象，与Math不一样

```javascript
var date=new Date();
console.log(date);//返回系统当前时间
//带参数常用的写法：数字型 2019，10，01或者是字符串型'2019-10-1 8:8:8'
var date1=new date(2019，10，01)
console.log(date1);//返回的是11月不是10月
var date1=new date('2019-10-1 8:8:8')
console.log(date2);//返回2019-10-1 8:8:8时间
```

| 方法名        | 说明                    | 代码              |
| ------------- | ----------------------- | ----------------- |
| getFullYear() | 获取当年                | obj.getFullYear() |
| getMonth()    | 获取当月(0-11)          | obj.getMonth()    |
| getDate()     | 获取当天日期            | obj.getDate()     |
| getDay()      | 获取星期几(周日0-周六6) | obj.getDay()      |
| getHours()    | 获取当前小时            | obj.getHours()    |
| getMinutes()  | 获取当前分钟            | obj.getMinutes()  |
| getSeconds()  | 获取当前秒钟            | obj.getSeconds()  |

### 时间戳

```javascript
//距离1970年1月1日总的毫秒数
//1.通过valueOf() getTime()
var date=new Date();
console.log(date.valueOf());
console.log(date.getTime());
//2.简单的写法（最常用的写法）
var date1= +new Date();
console.log(date1);
//3.H5新增的 获取总毫秒数
console.log(Date.now());
```

### 倒计时

> 用目标时间时间戳减去当前时间戳再转换为时间

```javascript
d=parseInt(总秒数/60/60/24);//计算天数
h=parseInt(总秒数/60/60%24);//计算小时
m=parseInt(总秒数/60%60);//计算分数
s=parseInt(总秒数%60);//计算当前秒数
```

### 立即执行函数

> 不需要调用，立马能够自己执行的函数，独立创建了一个作用域，避免了变量命名冲突问题

```javascript
//第一张方法
(function(a,b){
    console.log(a+b);
})(1,2);//3

//第二种方法
(function(a,b){
    console.log(a+b);
}(2,3));//3
```



# 函数进阶

> **[总结]()**：call、apply 和 bind 的作用都是改变 this 的指向，其中 call 和 apply 的区别在于它们传参的方式不同——call可以传多个形参，而apply只能传一个数组形参。而bind和call、apply的区别在于bind不会立即调用，而是返回一个函数对象，因此开发中一般用bind比较多

## call()继承

```js
let o = {
    name: "冷弋白",
};
function fn1(age) {
    console.log("我叫" + this.name + "，我" + age + "岁");
}
fn1.call(o, 20);
  


const steven = {
 name: Steven,
 phoneBattery:70,
 charge: function (level1,
 level2) {
 this. phoneBattery=level
 }
}
const becky = {
 name:'Becky',
 phoneBattery:30,
}
console.log(becky)
steven.charge.call(becky, 95)
console.log(becky)
控制台结果：
{name:Becky, phoneBattery: 30 }
{name: 'Becky ', phoneBattery: 95 }
```

## bind()继承

> bind()不调用函数，需要手动调用

```js
let o = {
    id: 200,
};
function Father() {
    this.id = 100;
}
function Son() {
    console.log(this.id);//200
}
function Son1() {
    console.log(id);//100
}
let fn1 = Son.bind(o); 
let fn2 = Son1.bind(Father());
fn1();
fn2();
//案例
let btns = document.querySelectorAll('button');
for (let i = 0; i < btns.length; i++) {
    btns[i].onclick = function () {
        this.disabled = true;
        setTimeout(function () {
            this.disabled = false;
        }.bind(this), 2000);//使指向this内的定时器指向外层的按钮，从而使按钮解锁
    }
}
```

## apply()继承

> apply与call不同的是它只能接收数组，数组元素代表继承者的参数，但被ES6的[剩余参数](#剩余参数)替代了

```js
function Father(id1, id2) {
    console.log(id1, id2);
}
function Son(id) {
    Father.apply(this, id);
}
Son([101, 102]); //101 102
//apply()求最大值
let max = Math.max.apply(Math, [1, 5, 7, 9, 6, 4, 2, 3]);//apply可以使数组直接可以用于Math判断最大值
console.log(max); 
```

## 递归

自己调用自己

```js
let num = 0;
function DG() {
    console.log("递归");
    num++;
    if (num == 5) {
        return;
    }
    DG();
}
DG();
```

### 阶乘

```js
function JC(n) {
    if (n == 1) {
        return 1;
    }
    return n * JC(n - 1);
}
console.log(JC(3));//3*2*1
```

## 对象浅拷贝

> 浅拷贝只是拷贝一层，更深层次对象级别的拷贝只拷贝地址
>
> 只拷贝了地址，修改obj，q也会被修改，Object.assign只有对象第一层的数值是深拷贝，第一层往下只拷贝了地址

```js
let obj = {
    name: "浅拷贝",
    fn: {
        id: 0
    }
};
let q = {};
console.log(obj);//{ name: '浅拷贝', fn: { id: 0 } }
Object.assign(q, obj);
q.name = '小明';//第一层为深拷贝，所以修改后不会影响obj
q.fn.id = 1;//第二层为浅拷贝，所以修改后会影响obj
console.log(obj);//{ name: '浅拷贝', fn: { id: 1 } }
```

## 对象深拷贝

```js
// 第一种方法
let obj = {
    uname: "深拷贝",
};
let deep = {};

function deepCopy(newObj, oldObj) {
    for (let k in oldObj) {
        let item = oldObj[k];
        if (item instanceof Array) {
            newObj[k] = [];
            deepCopy(newObj[k], item);
        } else if (item instanceof Object) {
            newObj[k] = {};
            deepCopy(newObj[k], item);
        } else {
            newObj[k] = item;
        }
    }
}
deepCopy(deep, obj);
console.log(deep);
obj.uname = "lyb"; //因为是深拷贝，所以修改obj不会修改deep的值
console.log(deep);


// 第二种方法
const info = {name:"why", age: 18}
const obj = JSON.parse(JSON.stringify(info))
```

# 面向对象

## 静态成员与实例化成员

```js
function Human(uname, age) {
    this.uname = uname;
    this.age = age;
}
Human.prototype.sing = function () { //公共方法写进原型对象上，就不会开辟新的内存空间浪费内存，每个方法里都有一个_proto_指向prototype
    console.log('我会唱歌');
}
let lyb = new Human('冷弋白', 20);
console.log(Human.uname); //不可通过构造函数访问成员,，否则undefine
Human.sex = '男'; //添加静态成员
console.log(Human.sex); //静态成员只能通过构造函数访问
lyb.job = 'web'; //添加实例化成员
console.log(lyb.job); //访问实例化成员
```

## 原型对象

```js
/* 原型对象 */
function Star(uname, age) {
    this.uname = uname;
    this.age = age;
}
Star.prototype = {
    constructor: Star, //如果直接给原型对象赋值，则必须手动的利用constructor指回原来的构造函数
    sing() {
        console.log('我会唱歌');
    }
}
Object.prototype.movie = function () {
    console.log('我会拍戏');
}
let ldh = new Star('刘德华', 18);
ldh.sing()
ldh.movie() //如果Star.prototype没有，则会去Object里找，如果还没有，则报错
```

> 释义

```js
//每个函数都有一个 prototype属性， Person.prototype是一个对象
Object.prototype.name = '爷'

function Person() {}
let person = new Person();
Person.prototype.name = '爹';
person.__proto__.name = '儿'
person.name = '孙';
console.log(Person.prototype.__proto__.name); //从Person.prototype.__proto__开始向上找
console.log(Person.prototype.name); //从person.__proto__开始向上找
console.log(person.__proto__.name); //从person.__proto__开始向上找
console.log(person.name);
/**
 * person.__proto__ === Person.prototype——对象
 * Person.prototype：原型对象
 * Person === Person.prototype.constructor——函数
 * person.constructor === Person.prototype.constructor //true
 */
```

### 自定义库

```js
Object.prototype.lyb = function () {
    return this.sort(function (a, b) {
        return a - b;
    });
};
let arr = [5, 7, 7, 9, 2];
console.log(arr.lyb()); //[ 2, 5, 7, 7, 9 ]
```



## Object.defineProperty

> `Object.defineProperty(对象名, '属性名', {`
>     `value:`修改属性值，没有则新增
>     `writable:`是否可以修改，默认为false不允许，独一无二，但只有对象名。属性不能修改，使用Object.defineProperty依旧可以修改
>     `enumerable:`是否被枚举，也就是这个无法被遍历出来，但却能被调用，默认为false，所以新增一个属性需要改为true才能遍历
>     `configurable:`是否可以被删除或者修改特性，也就是修改writable、enumerable、configurable，但value依旧可以修改，默认为false
>
> `})`

```js
let obj = {
    id: 1,
    uname: "lyb",
};
/* Object.defineProperty(对象名, '属性名', {
    value:修改属性值，没有则新增
    writable:是否可以修改，默认为false不允许，独一无二，但只有对象名。属性不能修改，使用Object.defineProperty依旧可以修改
    enumerable:是否被枚举，也就是这个无法被遍历出来，但却能被调用，默认为false，所以新增一个属性需要改为true才能遍历
    configurable:是否可以被删除或者修改特性，也就是修改writable、enumerable、configurable，但value依旧可以修改，默认为false
}) */
Object.defineProperty(obj, "id", {
    writable: false,
});
Object.defineProperty(obj, "uname", {
    configurable: false,
});
obj.id = 2; //此时无法修改
delete obj.uname; //此时无法被删除
console.log(obj);

Object.defineProperty(obj, "uname", {
    //enumerable: false //无法修改，将报错
});
Object.defineProperty(obj, "age", {
    //如果是以这种方式新增属性，想要让它能够修改这三个特性且具备普通属性拥有的特性，则需要全部改成true
    value: 20,
    enumerable: true,
    writable: true,
    configurable: true,
});
```

# ES6

## let和const

### let

1. let 声明的变量具有块作用域的特征。
2. 在同一个块级作用域，不能重复声明变量。
3. let 声明的变量不存在变量提升，换一种说法，就是 let 声明存在**暂时性死区**（TDZ）。

### const

const 声明方式，除了具有 let 的上述特点外，其还具备一个特点，即 const 定义的变量，一旦定义后，就不能修改，即 const 声明的为常量。

## 箭头函数

> 普通函数this指向函数自己，箭头函数没有自己的`this`，向外层作用域一层一层查找，找到最近的定义`this`
>
> 没有`this`、`super`、`arguments`和`new.target`绑定
>
> 不能通过`new`关键字调用
>
> 没有原型
>
> 不支持`arguments`对象
>
> 不支持重复的命名参数

```js
function fn4() {
    return () => {
        console.log(this);
    };
}
let obj3 = {
    name: "冷弋白",
};
fn4.call(obj3)(); //{ name: '冷弋白' }

 // 1.参数问题:
  // 1.1.放入两个参数
  const sum = (num1, num2) => {
    return num1 + num2
  }
 
  // 1.2.放入一个参数
  const power = num => {
    return num * num
  }
  
  // 1.３.无参数
　 const test = () => {
    console.log('Hello World');
  }
  //当函数内只有一句时，可以省略｛｝和return，自动返回
  const mul = (num1, num2) => num1 * num2
 const demo = () => console.log('Hello Demo')
```

## 字符串

### 遍历

```js
for (let s of 'lyb') {
  console.log(s)
}
//"f"
//"o"
//"o"
```

### 模板字符串

> 使用`${变量/返回值函数}`可直接拼接字符串，但字符串必须是被``包裹，模板字符串可保留换行符

```js
let str1 = `冷弋白`;
console.log(`老师好，我叫${str1}`); 
let str2 = `<element>
    <span>${str1}</span>
</element>`;
console.log(str2);
let fn5 = () => "函数";
console.log(`我是${fn5()}`);
```

### startWith()/endsWith()

> 判断字符串中是否包含某些字符串
>
> `startWith()`：如果在字符串的起始部分检测到指定文本则返回`true` 
>
>  `endsWith()`：如果在字符串的结束部分检测到指定文本则返回`true`

```js
const str = 'superman'
const subStr = 'super'
console.log(str.includes(subStr)); //true
```

### 重复字符串

```js
'superman'.repeat(2);////supermansuperman
```

## 默认参数

### 普通参数

```js
function fn(x=0, y=0) {
    return x + y;
}
fn(3, 6); //9
fn(3); //3
fn(); //0
```

### 对象参数

```js
function fn({a = 0, b = 0, c = 0}) {
    console.log(a,b,c);
}
fn({b: 2});//0 2 0
```

## 解构赋值

### 数组解构

```js
let arr = [1, 2, 3];
let [e, f, g, h] = arr;
console.log(e, f, g, h); //1,2,3，undefined
//交换数值
let i = 1;
let j = 2;
[i, j] = [j, i];
console.log(i, j); //2,1
```

### 对象解构

> 用的比较多，因为不使用解构赋值会多出`obj.k`，但如果是函数，它的`this`指向就是`window`了

```js
let obj1 = {
    a: 101,
    b: "lyb",
    c: 20,
};
let {a,b,c} = obj1;
console.log(a, b, c);//101 lyb 20
//第二种方法，能够自定义名
let obj2 = {
    id: 102,
    uname: "lyb",
    age: 20,
};
let {id: a,uname: b,age: c} = obj2;
console.log(n, o, p);//102 lyb 20
```

### 字符串

```js
let [a, b, c, d, e] = 'hello';
console.log(a,b,c,d,e);//h e l l o
```

## 剩余参数

> 替代了[apply()继承](#apply()继承)

```js
let sum = (a, ...args) => {
    return args;
};
console.log(sum(1, 5, 6, 3));//[5,6,3]
```

### 与解构赋值结合

```js
let [first, ...rest] = [1, 2, 3, 4, 5];
console.log(first); //1
console.log(rest);  //[2, 3, 4, 5]
```

## 扩展运算符

> 将数组/字符串/对象展开，每个元素/字符以逗号分隔

> 哪些地方使用了扩展运算符
>
> [set数组去重](#set数组去重)
>
> [求最大/小值](#求最大/小值)

### 数组展开

```js
let stu = ["张三", "李四", "王五", "赵六"];
let [q, ...r] = stu;
console.log(q); //张三
console.log(r); //李四，王五，赵六，以逗号形式分隔
console.log(...stu); //张三 李四 王五 赵六，输出自动去逗号
```

### 数组拷贝

```js
//浅拷贝
let s = [1, 2, 3];
let S = s; 
S[0] = 4;
console.log("s:" + s); //4,2,3
//深拷贝
let t = [3, 2, 1];
let T = [...t]; 
T[0] = 4;
console.log("t:" + t); //3,2,1
```

### 数组合并

> 取代了[通过concat数组合并](#concat数组合并)

```js
let arr1 = ["a", "b"];
let arr2 = ["c"];
let arr3 = ["d", "e"];
let arr = [...arr1, ...arr2, ...arr3];
console.log(arr); //['a', 'b', 'c', 'd', 'e']
//第二种方法
arr2.push(...arr3);
console.log(arr2); //['c', 'd', 'e']
```

### 字符串转数组

> 此方法取代了利用[Array.from](#Array.from转数组)来字符串转数组
>
> 另外还有[join数组转字符串](#join转字符串)

```js
[...'hello']
//[ "h", "e", "l", "l", "o" ]
```

## Symbol

> `Symbol` 特点
>
> ​    1. `Symbol`的值是唯一的,用来解决命名冲突的问题
>
> ​    2. `Symbol`的值不能与其他数据进行运算
>
> ​    3. `Symbol`定义的对象属性不能使用`for..in`循环遍历,但是可以使用
>
> `Reflect.ownKeys`来获取对象的所有键名
>
>    
>
> ES5的对象属性名都是字符串,这容易造成属性名的冲突,比如,你使用了一个他人
>
>    提供的对象,但又想为这个对象添加新的方法`(mixin 模式)`,新方法的名字就有可
>
>    能与现有方法产生冲突,如果有一种机制,保证每个属性的名字都是独一无二的就好
>
>    了,这样就从根本上防止属性名的冲突,这就是ES6引入`Symbol`的原因
>
>    *不能与其他数据进行运算 > == + - \* /都不可用甚至也不能和自己相加*

```js
let a = Symbol();
let b = Symbol();

let game = {
    a: '狼人杀',
    b: '今天天气不错',
    [a]: () => {
        console.log('我可以说话')
    },
    [b]: () => {
        console.log('我可以自爆')
    }
}
game[a]();//我可以说话
game[b]();//我可以自爆
console.log(game.a);//今天天气不错
```

## set数据结构

### set获取长度

> 返回 Set 结构的成员总数，会忽略重复的

```js
const set = new Set([1, 3, 5, 1]);
console.log(set.size); //3
```

### set追加元素

```js
const set = new Set([1]);
console.log(set.add("a").add("lyb"));//Set(3) {1,a, lyb}
```

### set删除元素

```js
let set = new Set([1, 1, 1, 2, 3])
set.delete(1);
console.log(set1);//Set(2) { 2, 3 }
```

### set删除对象

```js
let setObj = new Set();
setObj.add({
    x: 10,
    y: 20
});

setObj.add({
    x: 20,
    y: 30
});

console.log(setObj);//Set(2) { { x: 10, y: 20 }, { x: 20, y: 30 } }
setObj.forEach(function (point) {
    if (point.x > 10) {
        setObj.delete(point)
    }
})
console.log(setObj);//Set(1) { { x: 10, y: 20 }}
```

### set清空所有元素

```js
const set = new Set([1, 2, 3]);
set.clear();
console.log(set);
```

### set查找指定值

```js
const set = new Set(['a','b','c']);
set.has("b");//true
set.has("d");//false
```

### set遍历元素

> `Set.keys()`：返回键名的遍历器
>
> `Set.values()`：返回键值的遍历器
>
> `Set.entries()`：返回键值对的遍历器，但Set的键值对是相等的
>
> `Set.forEach()`：使用回调函数遍历每个成员

```js
let set = new Set(['red', 'green', 'blue']);

//Set的键名和键值是相等的
for (let item of set.keys()) {
  console.log(item);
}
//red
//green
//blue

for (let item of set.values()) {
  console.log(item);
}
//red
//green
//blue

for (let item of set.entries()) {
  console.log(item);
}
//["red", "red"]
//["green", "green"]
//["blue", "blue"]

set.forEach((value) => console.log(value)); 
//red
//green
//blue
```

### set数组去重

> 利用[扩展运算符](#扩展运算符)可以转为数组，但会被去重

```js
const set = new Set([1, 2, 2, 3, 3]);
console.log([...set]); //[1,2,3]
```

### set字符串去重

```js
const set = new Set('abbcccde');
console.log([...set].join('')); //abcd
```

### set转为真数组

> 此方法不会像[set数组去重](#set数组去重)被去掉重复的元素

```js
const set = new Set();
set.add(1).add(2).add(3);
const arr = Array.from(set);
console.log(arr); //[1, 2, 3]
```

## Map数据结构

### Map.size()

> 返回 `Map` 结构的成员总数，会忽略重复的

```js
const map = new Map();
map.set('foo', true);
map.set('bar', false);
map.set('bar', false);

console.log(map.size); //2
```

### Map.set(key, value)

> 设置键名`key`对应的键值为`value`，然后返回整个 `Map` 结构。如果`key`已经有值，则键值会被更新，否则就新生成该键

```js
const m = new Map();

m.set('edition', 6); //键是字符串
m.set(262, 'standard'); //键是数值
m.set(undefined, 'nah'); //键是 undefined
//采用链式写法
let map = new Map()
  .set(1, 'a')
  .set(2, 'b')
  .set(3, 'c');
```

### Map.get(key)

> 任何具有 `Iterator` 接口、且每个成员都是一个双元素的数组的数据结构都可以当作`Map`构造函数的参数。这就是说，`Set`和`Map`都可以用来生成新的 `Map`

```js
const set = new Set([
  ['foo', 1],
  ['bar', 2]
]);
const m1 = new Map(set);
console.log(m1.get('foo')); //1
const m2 = new Map([['baz', 3]]);
const m3 = new Map(m2);
console.log(m3.get('baz')); //3
```

### Map.has(key)

> 返回一个布尔值，表示某个键是否在当前 `Map` 对象之中

```js
const m = new Map();

m.set('edition', 6);

m.has('edition'); //true
m.has('years'); //false
```

### Map.delete(key)

> 删除某个键，返回`true`，如果删除失败，返回`false`

```js
const m = new Map();
m.set(undefined, 'nah');
m.has(undefined); //true

m.delete(undefined)
m.has(undefined); //false
```

### Map.clear()

> 清除所有成员，没有返回值

```js
let map = new Map();
map.set('foo', true);
map.set('bar', false);

map.size //2
map.clear()
map.size //0
```

### Map遍历方法

> `Map.keys()`：返回键名的遍历器
>
> `Map.values()`：返回键值的遍历器
>
> `Map.entries()`：返回所有成员的遍历器
>
> `Map.forEach()`：遍历 Map 的所有成员

```js
const map = new Map([
  ['F', 'no'],
  ['T',  'yes'],
]);

for (let key of map.keys()) {
  console.log(key);
}
//"F"
//"T"

for (let value of map.values()) {
  console.log(value);
}
//"no"
//"yes"

for (let item of map.entries()) {
  console.log(item[0], item[1]);
}
//"F" "no"
//"T" "yes"

//或者
for (let [key, value] of map.entries()) {
  console.log(key, value);
}
//"F" "no"
//"T" "yes"

//等同于使用map.entries()
for (let [key, value] of map) {
  console.log(key, value);
}
//"F" "no"
//"T" "yes"

map.forEach(function (a, b) {
    console.log(a, b);
})
//'no' 'F'
//'yes' 'T'
```

### Map转数组

> 会去重

```js
const map = new Map([
  [1, 'one'],
  [2, 'two'],
  [3, 'three'],
]);

[...map.keys()]
//[1, 2, 3]

[...map.values()]
//['one', 'two', 'three']

[...map.entries()]
//[[1,'one'], [2, 'two'], [3, 'three']]

[...map]
//[[1,'one'], [2, 'two'], [3, 'three']]
```

### Map转对象

> 如果所有 Map 的键都是字符串，它可以无损地转为对象

```js
function MapObj(map) {
    let obj = {};
    for (let [k, v] of map) {
        obj[k] = v;
    }
    return obj;
}

const map = new Map()
    .set('yes', true)
    .set('no', false);

console.log(MapObj(map)); //{ yes: true, no: false }
```

### 对象转Map

```js
let obj = {
    "a": 1,
    "b": 2
};
let map = new Map(Object.entries(obj));
console.log(map); //Map(2) { 'a' => 1, 'b' => 2 }

//实现原理
function objToStrMap(obj) {
  let strMap = new Map();
  for (let k of Object.keys(obj)) {
    strMap.set(k, obj[k]);
  }
  return strMap;
}

objToStrMap({yes: true, no: false})
//Map {"yes" => true, "no" => false}
```

> 如果对同一个键多次赋值，后面的值将覆盖前面的值

```js
const map = new Map();
map.set(1, 'aaa').set(1, 'bbb');
map.get(1); //"bbb"
```

> 下面代码的`set`和`get`方法，表面是针对同一个键，但实际上这是两个不同的数组实例，内存地址是不一样的，因此`get`方法无法读取该键，返回`undefined`。

```js
const map = new Map();
map.set(['a'], 555);
map.get(['a']) //undefined
```

> 同理，同样的值的两个实例，在 Map 结构中被视为两个键。

```js
const map = new Map();
const k1 = ['a'];
const k2 = ['a'];
map.set(k1, 111).set(k2, 222);
console.log(map.get(k1)); //111
console.log(map.get(k2)); //222
```

## Promise

> Promise不是异步程序，是解决异步流程化的一种手段
>
> Promise参数——excutor函数参数——resolve函数 reject函数

```js
let promise = new Promise((resolve, reject) => {
    resolve('遵守约定'); //此处不执行，传递给then方法第一个函数参数的res，如果此函数执行了，将不会执行reject函数
    reject('失约'); //此处不执行，传递给then方法第二个函数参数的err，这个函数要等所有程序执行完毕后才执行————最后执行的函数，如果此函数执行了，将不会执行resolve函数，但两者不会同时出现
})
promise.then((res) => { //Then异步调用，最后执行
    console.log(res);
    console.log('我最后执行');
}, (err) => {
    console.log(err);
})
console.log('我比Then先执行'); //Then异步调用，最后执行
```

> 接收错误的第二种方式——`catch()`

```js
promise.then((res) => {
    console.log(res);
}).catch(err => { //也可用catch接收err，但如果then里面的参数接收了err，catch将不会执行
    console.log(err);
})
```

> 更多接收`err`

```js
promise.then((res) => {
    console.log(res);
}).then((res) = {},
    (err) => {//如果上面的 then没有接收err，下面的then可以接收
        console.log(err);
    })
```

### 避免定时器回调地狱写法

> 封装一个新的定时器

```js
function print(delay, fn) {
    return new Promise(function (resolve) {
        setTimeout(() => {
            fn();
            resolve();
        }, delay);
    });
}
```

> 使用`then`

```js
print(1000, () => {
    console.log(111);
}).then(() => {
    return print(1000, () => {
        console.log(222);
    });
}).then(() => {
    print(1000, () => {
        console.log(333);
    });
});
```

> 使用`async`(推荐)

```js
async function asyncFunc() {
    await print(1000, () => {
        console.log(111);
    });
    await print(1000, () => {
        console.log(222);
    });
    await print(1000, () => {
        console.log(333);
    });
}
asyncFunc();
```

## async和await

> `async/await`是`Promise`的语法糖，使异步代码看起来更像同步。
> `async`返回`Promise.resolve`。`async`函数完全可以看作多个异步操作，包装成的一个 `Promise `对象，而`await`命令就是内部`then`命令的语法糖。
> `await`不能单独使用，必须配合`async`使用，`await`是基于`Promise`一起使用的。

```js
//上面代码是一个获取股票报价的函数，函数前面的async关键字，表明该函数内部有异步操作。调用该函数时，会立即返回一个Promise对象。
async function getStockPriceByName(name) {
  const symbol = await getStockSymbol(name);
  const stockPrice = await getStockPrice(symbol);
  return stockPrice;
}

getStockPriceByName('goog').then(function (result) {
  console.log(result);
});
```



## Class类

> ES6的类直接替代了ES5构造函数的写法，类的数据类型为函数，类本身就指向构造函数
>
> `Point===Point.prototype.constructor`

```js
//ES5
function A(a) {
    this.a = a;
}
A.prototype.fn = function () {
    console.log(this.a);
}
let lyb = new A('冷弋白');
lyb.fn(); //冷弋白

//ES6创建类
class A {
    b = 'lyb'; //可直接使用this.b调用
    static c = 'LYB';
    constructor(a) {
        this.a = a;
        this.c = A.c; //静态属性不能直接使用，需要在constructor里需要这样调用
    }
    fn() {
        console.log(this.b + '=' + this.c + '=' + this.a);
    }
}
let lyb = new A('冷弋白');
lyb.fn(); //冷弋白
```

### 类的继承

```js
class Father {
    constructor() {}; //如果不写，会自动添加
    money() {
        return '100万'
    }
}
class Son extends Father {
    /* money() {
        return '10万'
    } */
}
let son = new Son();
console.log('我继承了' + son.money()); //100万
//如果子类有money(),会根据就近原则输出子类的money()
```

### super关键字

> ，在constructor方法里使用super，super必须置于顶端

```js
class Father {
    constructor(x,y) {
        this.x = x;
        this.y = y;
    }
    a() {
        return this.x +this.y;
    }
}
class Son extends Father {
    constructor(x,y) {
        super(x,y); //调用父类中的constructor，再将子类传递进来的x，y传递给父类，才能使用父类的a()
        this.x = x;
        this.y = y;
    }
    //由于extends，a()被继承，但如果想在子类使用父类方法，则需要使用super
    b() {
        return this.x-this.y;
    }
    c() {
       return super.a()-this.x;
    }
}
let son = new Son(7, 3);
console.log(son.a()); //10
console.log(son.b()); //4
console.log(son.c()); //3
```

### static静态属性

> 在一个方法前，加上`static`关键字，就表示该方法或变量不会被实例继承，而是直接通过类来调用，且实例化对象不能调用静态方法

```js
class A {
    static x;//声明静态变量
    static lyb() { //静态方法可以与非静态方法重名
        console.log(this.x + '冷弋白');
    }
    lyb() {
        console.log('LYB');
    }
}
let b = new A();
A.x = 'lyb:'
A.lyb(); //冷弋白
b.lyb(); //LYB
```

> 父类静态方法也可以直接被子类继承和使用super

```js
class A {
    static fn() {
        console.log('Hello World!');
    }
}
class B extends A {
    static fn1() {
        super.fn();
    }
}
B.fn(); //Hello World!
B.fn1(); //Hello World!
```

### 私有和方法

> 只能在类里使用私有属性/方法

```js
class A {
    #a = '冷弋白';//私有属性
    #fn1() {//私有方法
        return 'lyb:';
    }
    fn2() {
        console.log(this.#fn1() + this.#a);
    }
}
let lyb = new A();
lyb.fn2(); //lyb:冷弋白
console.log(lyb.a); //undefined
```

## 正则表达式

### 创建方式

1. 通过调用RegExp对象的构造函数创建

```javascript
var 变量名 = new RegExp(/正则表达式/);
```

2. 通过字面量创建

```javascript
var 变量名 = /正则表达式/
```

### 使用方式

#### test()

> 返回 `true` 或 `false`

```js
let str = 'abcABC';
console.log(/a/g.test(str)); //true
console.log(/d/g.test(str)); //false
```

#### search()

> 返回索引号，检索不到返回-1
>
> 和[indexOf查找字符/串](#indexOf查找字符/串)相似，但search()可以使用正则表达式

```js
let str = 'abcABC';
console.log(str.search(/c/g)); //2
console.log(str.search(/d/g)); //-1
```

#### match()

> 返回多个匹配到的值，以数组形式存储，可以获取匹配到的数量，查找不到返回null案例参考[全局匹配g](#g修饰符)



#### replace()

> 来自[replace字符替换](#replace字符替换)
>
> 替换与正则表达式相匹配的值，只能替换满足条件的第一个(可以加修饰符全局匹配)
>
> 不会修改原字符串

```js
let str = 'abcABC';
console.log(str.replace(/c/g, 'd')); //abdABC
```

### 直接写

#### /Aa/

> 检索是否存在Aa

#### /^abc/

> 检索是否以abc开头

#### /abc$/

> 检索是否以abc结尾



### 各种符号

#### 边界符

| 边界符 | 说明                         |
| ------ | ---------------------------- |
| ^      | 表示匹配行首的文本(以谁开始) |
| $      | 表示匹配行尾的文本(以谁结束) |
| ^$     | 限定字符                     |

#### 修饰符

> i：执行对大小写不敏感的匹配。

```js
let str = 'aBa';
console.log(/b/.test(str)); //false
console.log(/b/i.test(str)); //true
```

> g：查找所有匹配而非在找到第一个匹配后停止

```js
let str = 'aBa';
console.log(str.match(/a/)); //['a']
console.log(str.match(/a/g)); //['a', 'a']
```

#### 中括号

| 表达式 | 描述                             |
| ------ | -------------------------------- |
| [abc]  | 查找a或b或c，只能包含其一        |
| [0-9]  | 查找任何从 0 至 9 的数字         |
| [a-z]  | 查找任何从小写 a 到小写 z 的字符 |
| [A-Z]  | 查找任何从大写 A 到大写 Z 的字符 |
| [^a-z] | 取反，不能包含a-z                |

#### 量词符

| 量词   | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| n+     | 匹配任何包含至少一个 n 的字符串。个数>=1                     |
| n*     | 匹配任何包含零个或多个 n 的字符串。个数>=0                   |
| n?     | 匹配任何包含零个或一个 n 的字符串。个数 1 \|\| 0             |
| n{X}   | 匹配包含 X 个 n 的序列的字符串。 个数=X                      |
| n{X,}  | 匹配包含 X 个 n 以上的序列的字符串  个数>=X                  |
| n{X,Y} | 匹配包含 X 个到Y个范围内的n    X>= N >=Y，Y前面不能有空格    |
| n$     | 匹配任何结尾为 n 的字符串。                                  |
| ^n     | 匹配任何开头为 n 的字符串。                                  |
| ^n$    | 自私的匹配，匹配任何开头和结尾为 n 的字符串，但字符串内只能存在n |

#### 元字符

##### \b或\B

> 查找位于单词的开头或单独的单词匹配
>
> \blike如下
>
> \d：how `like` youlike youlikeme `like`you
>
> \B：how like you`like` you`like`me likeyou

##### 更多元字符

| 元字符 | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| \w     | [a-zA-Z0-9_],查找数字、字母及下划线                          |
| \W     | [^a-zA-Z0-9_],找非数字、字母及下划线                         |
| \d     | [0-9],查找数字0-9                                            |
| \D     | [^0-9],查找非数字字符                                        |
| \s     | [\t\r\n\v\f],查找空白字符(空格符，制表符，回车符，换行符，垂直换行符，换页符) |
| \S     | [^\t\r\n\v\f],查找非空白字符                                 |
| \n     | 查找换行符                                                   |
| \t     | 查找制表符                                                   |
| \v     | 查找垂直制表符                                               |



