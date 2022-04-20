# ES6



## 简介

`ES(ECMAScript)` 是一个脚本语言的标准，`javascript` 就是这个标准的实现之一。这个标准，每年发布一个新的版本，`ES6` 是 2015  年发布的一个版本，基本都是使用 `ES6`, 原因有两点：1. 兼容性好，2. 版本更新较大，相比于 `java8`



## 新特性





### let & const



- const 声明的变量不可以被重新赋值
- 一个变量多次声明？
  - var 可以
  - let 不可以
- 是否能跨过作用域访问？
  - var 可以
  - let 不可以
- 变量是否提升？
  - var 可以
  - let 不可以
- **建议**
  - 使用 `const`, `let` 声明变量



```html
    <script>
        const a = 1
        // a= 2 // Uncaught TypeError: Assignment to constant variable.
        console.log(a)

        //声明多次
        var b = 1;
        var b = 2;
        let c = 1;
        // let c = 2;
        console.log(b, c)

        // 变量作用域
        {
            var d = 1;
            let e = 2;
        }
        // console.log(d, e) //Uncaught ReferenceError: e is not defined


        // 变量提升
        console.log(f)
        var f = 'f';
        console.log(g) // Cannot access 'g' before initialization
        let g = 'g' 
    </script>
```





### 解构



- 分类
  - 数组
  - 对象
- 优势
  - 快速从数组或对象中取值，并赋值给变量



**示例**

```html
<script>
        let arr = [1, 2, 3]
        // 以前取值
        let a = arr[0]
        let b = arr[1]
        let c = arr[2]
        // 解构取值
        let [aa, bb ,cc] = arr
        console.log(a, b, c)
        console.log(aa, bb, cc)

        // 对象
        let student = {
            name: '张三',
            age: 10
        }
        // 普通取值
        let name1 = student.name
        let age1 = student.age
        // 解构取值
        // 1. 名字一样，2，{ }
        let{name, age} = student
        console.log(name, age)
        console.log(name1, age1)
    </script>
```





### 字符串



- 这个好用 模板字符串符号好用
  - 声明多行字符
  - 可以使用 ${变量} 引用变量，或 ${函数} 调用函数





```html
    <script>
        let s1 = 'line1' +
                    'line2'
        let s2 = `lin1
                    lin2
        `

        let name = '张三'
        function f(){
            return 'hello, world'
        }

        let s = `name: ${name}, 函数返回值：${f()}` 
        console.log(s) // name: 张三, 函数返回值：hello, world
    </script>
```





### 函数



- 参数默认值
- 可变参数
- 参数可以是解构表达式
  - 对象解构表达式，`{name, age}`
  - 数组解构表达式，`[a, b]`
- 声明匿名函数



```html

    <script>
        function f1(a=1){
            console.log(a)
        }
        f1()

        function f2(...a){
            console.log(a) // a 是一个数组
        }
        f2(1, 2) // [1. 2]
        f2(1, 2, 3) // [1, 2, 3]

        function f3({name, age}){
            console.info(name, age)
        }
        f3({
            name: 'zs',
            age: 14
        })

        let add = (a, b) => a + b // 单行，省略 return
        let add2 = (a, b) => {
            let c = a + b
            return c / 2
        }

        console.log(add(1, 2)) // 3
        console.log(add2(1, 2)) // 1.5
    </script>
```





### 对象



1. 增加了一些新方法
   1. `Object.keys(obj)`，获取对象所有的 key（保存在数组中）
   2. `Object.values(obj)`， 获取对象所有的 value（保存在数组中）
   3. `Object.entries(obj)`，
   4. `Object.assign(o1, o2, o3 ...)`，将 02, 03, 的属性合并到 o1 中，后面的属性会覆盖掉 o1 中相同的属性
2. 简化了声明对象过程
3. 简化了对象中函数的声明
   1. 对象内可以使用匿名形式
4. 对象拷贝
5. 对象合并



**示例**

```html
    <script>

        // 1. Object 方法
        let o11 = {
            a: 1,
            b: 2,
            c: {
                c1: 1,
                c2: 2
            }
        }
        console.log(Object.keys(o11)) // ['a', 'b', 'c']
        console.log(Object.values(o11)) // [1, 2, {c1:1, c2:2}]
        console.log(Object.entries(o11)) // [Array(2), Array(2), Array(2)]

        let o12 = {a:1}
        let o13 = {b:2}
        let o14 = {a:3, c: 3}
        console.log(o12) // {a: 1}
        Object.assign(o12, o13, o14)
        console.log(o12) // {a: 3, b: 2, c: 3}

        // 2. 声明对象
        let name = 'zs'
        let age = 29
        // es6 之前
        let o21 = {
            name: name,
            age: age
        }
        // es6
        let o22 = {
            name,age // 会自动找同名的变量
        }
        console.log(o21) // {name: 'zs', age: 29}
        console.log(o22) // {name: 'zs', age: 29}

        // 3. 简化函数属性
        let o31 = {
            a: 1,
            // es6 之前
            f1: function(a){
                console.log(this.a)
            },

            // es6 之后
            f2: (a) => console.log(o31.a), // 箭头函数只能用变量访问对象属性
            f3(a){
                console.log(this.a)
            }
        }
        o31.f1(1) // 1
        o31.f2(2) // 2
        o31.f3(3) // 3

        // 4 对象拷贝和合并
        let o41 = {a:1, b:{b1:1, b2:2}}
        let o42 = {...o41}
        console.log(o42)

        let o43 = {a:1}
        let o44 = {a:2, b:2}
        // 这种拷贝方式，生成新的对象，而不想 Object.assign 那样
        let newO = {...o43, ...o44}
        console.log(newO) // {a: 2, b: 2}


    </script>
```





### 模块化



- 优势
  - 复用。（相同功能代码放在同一个模块中，方便复用）
- 关于 js 的模块化
  - 一个 js 文件就是一个模块
- 使用顺序
  - 先导出
  - 再引入





`user.js`

```javascript

// 想让别人用定义好的变量，对象，数组，函数，就导出
export let u1 = {
    id: 1,
    name: 'u1'
}

let u2 = {
    id: 2,
    name: 'u2'
}

let u3 = {
    id: 3,
    name: 'u3'
}

// 导入方式3，默认导出
export default function f1(){
    return 'default 方式导出'
}

// 导出方式2，批量导出
export {u2, u3}

// 导出方式
// 1. export 直接放到要导出的对象上面
// 2. 文件末尾批量导出
// 3. export default 默认导出
```



`1.html`

```html
 // 这里 type 要是 module
    <script type="module">
        // 这是默认
        import u1 from './js/user.js'
        import {u1 as u11} from './js/user.js'
        import {u2, u3} from './js/user.js'
        import defaultF from './js/user.js'

        console.log(u11)
        console.log(u2)
        console.log(u3)
        console.log(defaultF())

        // 引入
        // 1. import xxx from 'xxx.js' // 引入默认的
        // 2. import {x1} from 'x.js' // 引入一个
        // 3. import {x1, x2} from 'x.js' // 引入多个
        // 4. import * as xx from 'xx.js' // 引入全部，此时，必须取别名
 
    </script>
```



