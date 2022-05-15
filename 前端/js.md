<!-- TOC -->

- [1. 基础](#1-%E5%9F%BA%E7%A1%80)
    - [1.1. 数据类型](#11-%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B)
    - [1.2. 对象](#12-%E5%AF%B9%E8%B1%A1)
    - [1.3. 判断](#13-%E5%88%A4%E6%96%AD)
    - [1.4. 循环](#14-%E5%BE%AA%E7%8E%AF)
- [2. 函数](#2-%E5%87%BD%E6%95%B0)
    - [2.1. 定义函数](#21-%E5%AE%9A%E4%B9%89%E5%87%BD%E6%95%B0)
    - [2.2. 调用函数](#22-%E8%B0%83%E7%94%A8%E5%87%BD%E6%95%B0)
    - [2.3. 定义函数参数](#23-%E5%AE%9A%E4%B9%89%E5%87%BD%E6%95%B0%E5%8F%82%E6%95%B0)
    - [2.4. 箭头函数](#24-%E7%AE%AD%E5%A4%B4%E5%87%BD%E6%95%B0)
- [3. 解构](#3-%E8%A7%A3%E6%9E%84)
- [4. 面向对象编程](#4-%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E7%BC%96%E7%A8%8B)
    - [4.1. 构造函数](#41-%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0)
    - [4.2. 类](#42-%E7%B1%BB)
    - [4.3. 标准对象](#43-%E6%A0%87%E5%87%86%E5%AF%B9%E8%B1%A1)
        - [4.3.1. Date](#431-date)
        - [4.3.2. JSON](#432-json)
- [5. 操作 DOM](#5-%E6%93%8D%E4%BD%9C-dom)
    - [5.1. 获取](#51-%E8%8E%B7%E5%8F%96)
    - [5.2. 修改](#52-%E4%BF%AE%E6%94%B9)
    - [5.3. 插入新节点](#53-%E6%8F%92%E5%85%A5%E6%96%B0%E8%8A%82%E7%82%B9)
    - [5.4. 删除节点（todo）](#54-%E5%88%A0%E9%99%A4%E8%8A%82%E7%82%B9todo)

<!-- /TOC -->

# 1. 基础



## 1.1. 数据类型

- javascript 有哪些数据类型？如何判断一个变量的数据类型



`js` 提供以下的数据类型：

1. `number`
2. `string`
3. `boolean`
4. `object`
5. `function`



`number`

关于`number` 数据类型，在 `js` 中，无论是整数还是小数，数据类型都是 `number`, 甚至有些特殊的值也是 `number`

```
123; // 整数123
0.456; // 浮点数0.456
1.2345e3; // 科学计数法表示1.2345x1000，等同于1234.5
-99; // 负数
NaN; // NaN表示Not a Number，当无法计算结果时用NaN表示
Infinity; // Infinity表示无限大，当数值超过了JavaScript的Number所能表示的最大值时，就表示为Infinity
```



`string`

`string` 既可以用单引号也可以用双引号

```js
var s1 = 'hello';
var s2 = "world";
```



`object`

数组、对象、`null` 都是 `object` 数据类型

```js
var o1 = {
            name: "ab"
        }
var o2 = [1, 2, 3];
var o3 = null;

typeof o1; // object
typeof o2; // object
typeof o3; // object
```



`function`

在 js 中还有一种数据类型是 `function` 代表函数。

```javascript
var f1 = () => 1;
typeof f1; // function
```



`undifined`

没有初始化的变量，其数据类型是`undifined`

```javascript
var u1;
var u2 = undifined;
typeof u1; // undifined
typeof u2; // undifined
```





## 1.2. 对象

- 如何声明一个对象？
- 如何访问对象属性？
- 如何删除和添加属性？
- `in` 和 `hasOwnProperty()` 判断对象是否有某个属性时，有什么区别？



**声明对象**

- 声明一个对象，可以用字面量写法，也可以用构造函数写法。
- 访问对象属性可以用 `对象.属性`, `对象[属性]`。
- 可以对 js 对象自由的添加、删除属性，`js` 对此没有任何限制

```js
var xiaoming = {
    name: '小明'
};

// 访问属性
xiaoming.age; // undefined
xiaoming[name]; // '小明'

// 新增属性
xiaoming.age = 18; // 新增一个age属性
xiaoming.age; // 18

// 删除属性
delete xiaoming.age; // 删除age属性
xiaoming.age; // undefined
// 这种方式也可以删除
delete xiaoming['name']; // 删除name属性
xiaoming.name; // undefined
```



**判断对象是否有某个属性**

用`in` 判断

```js
var xiaoming = {
    name: '小明',
    birth: 1990,
    school: 'No.1 Middle School',
    height: 1.70,
    weight: 65,
    score: null
};
'name' in xiaoming; // true
'grade' in xiaoming; // false
```

`in`判断一个属性存在，这个属性不一定是`xiaoming`的，它可能是`xiaoming`继承得到的。比如，`toString`定义在`object`对象中，而所有对象最终都会在原型链上指向`object`，所以`xiaoming`也拥有`toString`属性。

```javascript
'toString' in xiaoming; // true
```



`hasOwnProperty()`方法，可以判断一个属性是否是自身的

```js
var xiaoming = {
    name: '小明'
};
xiaoming.hasOwnProperty('name'); // true
xiaoming.hasOwnProperty('toString'); // false
```



## 1.3. 判断

`js` 也是用 `if`, `elseif`, `else` 做判断



什么时候可以省略 `{}` 呢？当 `if`, `else`, `else if` 只有一行语句时，可以省略

```javascript
var age = 3;
if (age >= 18) {
    alert('adult');
} else if (age >= 6) {
    alert('teenager');
} else {
    alert('kid');
}

// 语句块里有多行时，不可以省略 {}
var age = 20;
if (age >= 18)
    alert('adult');
else
    console.log('age < 18'); // 添加一行日志
    alert('teenager'); // <- 这行语句已经不在else的控制范围了
```



## 1.4. 循环



`for` 有两种写法：

1. 通过初始条件、结束条件和递增条件来循环执行语句块
2. 使用 `for ... in ...`



`for` 循环最常用来变量数组

```javascript
var arr = ['Apple', 'Google', 'Microsoft'];
var i, x;
for (i=0; i<arr.length; i++) {
    x = arr[i];
    console.log(x);
}
```

`for` 循环的三个条件省略，表示无限循环，可以用 `break` 退出循环

```javascript
var x = 0;
for (;;) { // 将无限循环下去
    if (x > 100) {
        break; // 通过if判断来退出循环
    }
    x ++;
}
```



`for ... in ...`

- 常用来遍历对象属性
- 遍历数组。遍历的值是索引，*请注意*，`for ... in`对`Array`的循环得到的是`string`而不是`number`。

```javascript
var o = {
    name: 'Jack',
    age: 20,
    city: 'Beijing'
};
for (var key in o) {
    console.log(key); // 'name', 'age', 'city'
}

// 对象有的属性
for (var key in o) {
    if (o.hasOwnProperty(key)) {
        console.log(key); // 'name', 'age', 'city'
    }
}

// -- 遍历数组
var a = ['A', 'B', 'C'];
for (var i in a) {
    console.log(i); // '0', '1', '2'
    console.log(a[i]); // 'A', 'B', 'C'
}
```





# 2. 函数



## 2.1. 定义函数

`function` 关键字定义一个函数



```javascript
function f1(){
    alert(1);
}

f1();
```



JavaScript的函数也是一个对象，上述定义的`f1()`函数实际上是一个函数对象，而函数名`f1`可以视为指向该函数的变量。

可以用下面的方式定义一个函数

```javascript
var f1 = function(){
    alert(1)
}

f1()
```

`function () { ... }`是一个匿名函数，它没有函数名。但是，这个匿名函数赋值给了变量`abs`，所以，通过变量`abs`就可以调用该函数。

上述两种定义*完全等价*，注意第二种方式按照完整语法需要在函数体末尾加一个`;`，表示赋值语句结束。



## 2.2. 调用函数



在 `js` 中调用函数时，传入的参数是多了，还是少了，都没关系



```javascript
function f(x) {
    return x;
}


f(1); // 1
f(1, 2); // 1
f(); // undifined
```





## 2.3. 定义函数参数

从 ES6 开始，`js` 中的函数的参数，可以：

1. 设定默认值。（被调用的时候，这个参数没有被赋值，则使用默认值）
2. 定义可变参数的函数
3. 参数可以是对象、数组。这时候，可以使用解构将对象的属性绑定到变量中



```javascript
<script>
    	// 给参数设定一个默认值
       function f1(a=1){
            console.log(a)
        }
        f1() // 1

		// 参数是可变的
        function f2(...a){
            console.log(a) // a 是一个数组
        }
        f2(1, 2) // [1. 2]
        f2(1, 2, 3) // [1, 2, 3]

		// 参数是对象
        function f3({name, age}){
            console.info(name, age)
        }
        f3({name: 'zs',age: 14}) // zs 14
        f3([1, 2]) // undifined undifined

		// 参数是数组
        function f4([x, y]){
            console.log(x, y);
        }

        f4([1, 2, 3]) // 1 2
        f4(['a', 'b']) // a b
        f4(1) // Uncaught TypeError: number 1 is not iterable (cannot read property Symbol(Symbol.iterator))
    </script>
```



**看下别人的代码**

```javascript
function buildDate({year, month, day, hour=0, minute=0, second=0}) {
    return new Date(year + '-' + month + '-' + day + ' ' + hour + ':' + minute + ':' + second);
}

// 传了 year, month, day
buildDate({ year: 2017, month: 1, day: 1 });
// Sun Jan 01 2017 00:00:00 GMT+0800 (CST)

// 6 个参数都传了
buildDate({ year: 2017, month: 1, day: 1, hour: 20, minute: 15 });
// Sun Jan 01 2017 20:15:00 GMT+0800 (CST)
```





## 2.4. 箭头函数



箭头函数是 ES6 新增的。使用箭头函数定义函数，更加简单



```javascript
let add = (a, b) => a + b // 单行，省略 return
let add2 = (a, b) => {
    let c = a + b
    return c / 2
}

console.log(add(1, 2)) // 3
console.log(add2(1, 2)) // 1.5
```







# 3. 解构



解构是 ES6 新增的特性，可以方便的将对象属性值、数组元素赋值

- 利用解构，不但可以将数组的全部元素赋值，还可以仅将元素的部分元素给变量赋值
- 将对象属性值时，不但可以赋值给跟属性名相同的变量，也可以赋值给跟属性名不同的变量，如果对象中没有跟某一变量相同的属性，可以指定该变量的默认值





ES6 之前将数组元素赋值

```javascript
var a = [1, 2, 3]
var x = a[0];
var y = a[1];
var z = a[2];
```

ES6 之后将数组元素赋值给变量

```javascript
var a = [1, 2, 3];

// 将1，2， 3分别赋值给变量 x, y, z
var [x, y, z] = a;
console.log(x); // 1
console.log(y); // 2
console.log(z); // 3

// 仅将 3 赋值给变量 z1
var [,, z1] = a;
console.log(z1); //

// 数组解构嵌套
let [x, [y, z]] = ['hello', ['JavaScript', 'ES6']];
x; // 'hello'
y; // 'JavaScript'
z; // 'ES6'
```



利用解构，将对象的属性赋值给变量

```javascript
var person = {
    name: '小明',
    age: 20,
    gender: 'male',
};
// 将 gender 属性赋值给 sex
// 如果 person 没有 address 属性，address 变量值等于 'SH'
var {name, age, gender:sex, address='SH'} = person;
name; // '小明'
age; // 20
sex; 'male'
address; // 'SH'

// 对象解构嵌套
var person = {
    name: '小明',
    age: 20,
    gender: 'male',
    passport: 'G-12345678',
    school: 'No.4 middle school',
    address: {
        city: 'Beijing',
        street: 'No.1 Road',
        zipcode: '100001'
    }
};
var {name, address: {city, zip}} = person;
name; // '小明'
city; // 'Beijing'
zip; // undefined, 因为属性名是zipcode而不是zip
// 注意: address不是变量，而是为了让city和zip获得嵌套的address对象的属性:
address; // Uncaught ReferenceError: address is not defined
```



# 4. 面向对象编程





## 4.1. 构造函数



构造函数和普通的函数看起来长的一样。用法却不同，构造函数需要 `new` 关键字



下面就是一个简单的构造函数。函数体中`this` 关键字表示新创建的对象，`this.xxx = xxx` 就是给对象添加属性，并赋值

```javascript
function Student(name) {
    this.name = name;
    this.hello = function () {
        alert('Hello, ' + this.name + '!');
    }
}

let s = new Student('zs');
s.name; // 'zs'
s.hello(); 
```





## 4.2. 类



ES6 时，新增了一个关键字 `class` 定义类，并且类之间也可用 `extends` 继承



```javascript
class Student {
    constructor(name) {
        this.name = name;
    }

    hello() {
        alert('Hello, ' + this.name + '!');
    }
}

// new 一个对象
var s = new Student('zs');
s.hello();

// 继承
class AgeStudent extends Student {
    constructor(name, age) {
        super(name); // 记得用super调用父类的构造方法!
        this.age = age;
    }

    myAge() {
        alert('My age is ' + this.age);
    }
}
var ps = new AgeStudent('zs', 18)
```



## 4.3. 标准对象



`js` 提供了一些对象，比如 `Date`, `JSON`



### 4.3.1. Date

- 三种构造函数
- `Date` 静态方法 
  - `parse()` 将符合 `ISO 8601` 格式的字符解析为一个日期
  - `now()` 返回时间戳
- 什么是时间戳
  - 时间戳是距离 1970年1月1日0点0分0秒 这一时刻的毫秒值
  - 任意地区某一刻的时间戳都是一致
  - 浏览器根据所在时区，可以将时间戳按照当地日区显示日期



```javascript
// 构造1：无参构造，表示当前日期
var d = new Date();
console.log(d);
console.log(d.getFullYear()); // 2022
console.log(d.getMonth()); // 4
console.log(d.getDay()); // 0
console.log(d.getSeconds()); // 42
console.log(d.getMilliseconds()); //
console.log(d.getTime()); // 1652605667203
console.log(d.getTimezoneOffset()); 


// 构造2：传入指定日期，创建一个 date
var d2 = new Date(2022, 5, 15, 20, 15, 30, 123);
console.log(d2) // Wed Jun 15 2022 20:15:30 GMT+0800 (中国标准时间)

// 构造3：传入一个时间戳
var d3 = new Date(1652605667203)
console.log(d3); // Sun May 15 2022 17:07:47 GMT+0800 (中国标准时间)

// 解析字符串为日期的方法
var d = Date.parse('2015-06-24T19:49:22.875+08:00');
d; // 1435146562875

// 获取时间戳
console.log(Date.now()); // 1652605913160
console.log(new Date().getTime()); // 1652605913160
```



### 4.3.2. JSON



知道两个方法就行了

1. `JSON.stringify(object)` 将一个对象字符串化。也就是得到 `json` 字符串
2. `JSON.parse(string)` 将一个字符串解析为一个对象



```javascript
var p = {name: 'zs', age:10};
var s = JSON.stringify(p)
console.log(s) // {"name":"zs","age":10}
console.log(JSON.parse(s));
```





# 5. 操作 DOM

`html` 文档被浏览器解析时，会生成一个 `DOM` 树，可以利用 `javascript` 操作 `DOM` ，改变 html 文档的内容

`DOM` 节点的操作无非就是下面 4 个操作：

- 更新：更新该DOM节点的内容，相当于更新了该DOM节点表示的HTML的内容；
- 遍历：遍历该DOM节点下的子节点
- 添加：在该DOM节点下新增一个子节点，相当于动态增加了一个HTML节点；
- 删除：将该节点从HTML中删除，相当于删掉了该DOM节点的内容以及它包含的所有子节点。



## 5.1. 获取

在操作 `DOM` 节点之前，得先通过 `docment` 提供的一些 API 拿到这个节点（`docment` 获取`DOM` 节点，没有 `jquery` 方便，`jquery` 获取 `DOM` 节点符合 `CSS` 选择器语法）

- 获取 `DOM` 节点

  - |                                     | 说明                              | 返回值                |
    | ----------------------------------- | --------------------------------- | --------------------- |
    | `document.getElementById()`         |                                   | 会返回一个 `DOM` 节点 |
    | `document.getElementsByTagName()`   |                                   | 返回一组节点          |
    | `document.getElementsByClassName()` |                                   | 返回一组节点          |
    | `document.querySelector()`          | 会返回符合 css 选择器的第一个元素 |                       |
    | `document.querySelectorAll()`       | 会返回符合css 选择器的所有元素    |                       |

- 属性

  - | 属性值   | 说明        |
    | -------- | ----------- |
    | children | 子节点      |
    | style    | 节点的style |

    

**获取 DOM 节点**

```javascript
<body>
    
    <span class="s">1</span>
    <span class="s">2</span>
    <span>3</span>

    <script>
        var a = document.getElementsByClassName("s");
        console.log(a) // HTMLCollection(2)

        var b = document.querySelector(".s");
        console.log(b) // span.s

        var c = document.querySelectorAll(".s");
        console.log(c) // NodeList(2)
    </script>
</body>
```



**查看节点属性**

```html
<body>
    
    <div id="list">
        <p>c++</p>
        <p>java</p>
    </div>

    <script>
        var parent = document.getElementById("list")
		
        // 属性
        console.log(parent.children)
        console.log(parent.children[0])
        console.log(parent.style)
    </script>
</body>
```

控制台内容 :arrow_down:

![image-20220515204246463](https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220515204246.png)





## 5.2. 修改



- 修改文本
  - `innerHtml`
  - `innerText`, `textContent`
- 修改样式。`style` 属性对应所有 CSS 样式，可以获取、修改样式。css 中的样式比如 `font-size` 到 `js` 中变成了小驼峰



```HTML
<body>
    
    <span id = 's-id'>s1</span>

    <script>
        var node = document.getElementById("s-id");
        node.innerHTML = '<span>ABC</span>' // <span id = 's-id'><span>ABC</span></span>
        
        console.log(node.innerText); // ABC
		node.innerText = 'DEF' // <span id="s-id" style="color: rgb(255, 0, 221); font-size: 30px;">DEF</span>

        node.style.color = "#ff00dd"
        node.style.fontSize = "30px"
    
    </script>
</body>
```



注意一下，上面已经在 span 中天一个 html 代码，但是`node.innerText` 返回的是 `ABC`, `node.innerText='DEF'` 后，标签变了 



## 5.3. 插入新节点



- 插入节点不能用 `innerHtml` , `innerHtml` 会将所有的子节点替换掉
- 插入节点
  - 插入到父节点的最后一个子节点位置，`parent.appendChild(node)`
  - 插入到指定子节点的前面，`parent.insertBefore(node, child)`





**parent.appendChild(node)**



<img src='https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220515181522.png'>

**parent.insertBefore(node, child)**

```html
 <div id="list">
        <p>c++</p>
        <p>java</p>
    </div>

    <script>
        var parent = document.getElementById("list")

        var python = document.createElement("p")
        python.innerText = 'python'

        var first = node.children[0]
        parent.insertBefore(python, first);
    </script>
```





## 5.4. 删除节点（todo）

