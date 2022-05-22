



>  ==及其重要，使用 vue ，就是在写路由==



# 路由工作原理



**示例图**

<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220415224624.png" alt="image-20220415224624235" style="zoom: 25%;" />





# 关于 vue-router

- `vue` 的一个插件库，专门用来实现 `SPA` 应用
- `SPA` 应用
  1. 整个应用只有一个完整的页面
  2. 点击页面中的导航连接不会刷新信息，只会做页面的==局部更新==
  3. 数据需要通过 `ajax` 获取



# 基础内容



### 理解路由



<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220415224931.png" alt="image-20220415224931687" style="zoom:33%;" />







### 安装



- 版本要求（和 `vuex` 一样，不同的 版本的`vue` 使用的版本不同 
  -  `vue3` 需要 `vue-router` 的 4 版本
  - `vue2` 需要 `vue-router` 的 3 版本
    - ` npm i vue-router@3`



### 简单使用



1. 安装
2. 创建 `router`
   1. 创建文件`/src/router/index.js` （路由器一般写在这个文件下）
   2. `import VueRouter from "vue-router";`
   3. 引入组件
   4. new VueRouter（配置 k=v, k: path, v:组件）
   5. 暴露
3. 在 `main.js`
   1. 引入插件 `vue-router`
   2. 引入写好的 `router`
   3. Vue.use(vue-router)
   4. 给 `vm` 配置 router



`/src/router/index.js`

```javascript
// VueRouter
import VueRouter from "vue-router";
// 组件
import Product from '@/pages/Product'

// 实例
const router = new VueRouter({
    // 配置 routes, 数组
    routes: [
        {
            name: 'PRODUCT',
            path: '/product', // 路径
            component: Product // 要跳转的组件
        }
    ]
})

export default router
```



`main.js`

```javascript
import Vue from 'vue'
import App from './App.vue'
import VueRouter from 'vue-router'

// import 
import router from '@/router'

// 使用 VueRouter 插件
Vue.use(VueRouter)

Vue.config.productionTip = false



new Vue({
  render: h => h(App),
  router // 配置路由
}).$mount('#app')

```



### 路由组件显示或隐藏



<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220420175056.png" alt="image-20220420175056078" style="zoom:50%;" />





# 配置



### 嵌套路由



`Home.vue` 嵌套了`News.vue`, `Message.vue`

`Home.vue`

```vue
<template>
  <div>
    <div>
      <ul>
        <li><router-link to="/home/news">news</router-link></li>
        <li><router-link to="/home/message">message</router-link></li>
      </ul>    
    </div>

    <router-view></router-view>
  </div>
  
</template>

<script>
export default {
    name:"Home"
}
</script>
```



`/src/router/index.js`

```javascript
import VueRouter from "vue-router";

import Home from '../pages/Home'
import News from '../pages/News'
import Message from '../pages/Message'


// 路由 = 路径：组件
const router = new VueRouter({

    // mode:"hash",
    // mode: "history",

    // routes 配置项是个数组
    routes:[
        {
            path:'/home',
            component: Home, // 访问 /home 是，展示 Home 组件（元素、样式、交互）
            children:[
                {
                    // 子路由不需要 '/'
                    path:'news',
                    component: News,
                },
                {
                    path:'message',
                    component: Message
                }              
            ]
        }
    ]
    
})


export default router
```









### 配置 `name`



- 当路径过长时，可以给路由配置 `name`, 在跳转的地方使用 路由名字
- 注意
  - 调整，比如，to，必须是对象形式





```html
 <!-- path 写法 -->
    <router-link class='a' active-class='active'  to="/About">About</router-link>
    <!-- name 写法 -->
    <router-link class='a' active-class='active'  :to="{name:'about'}">About</router-link>

<router-link :to="{
            <!-- path: '/home/message/detail', -->
            name:'detail',
            query:{
              id: m.id,
              title: m.title
            }
          }">{{m.title}}</router-link>
```



### 配置重定向









# 路由传参

### 路由传参三种方式



- 路由传参，真的是把数据传给另一个路由

- 三种方式（以编程式路由跳转为例）
  - 字符串
  - 模板字符串
  - 对象



**示例**

```vue
<template>
  <div>
      <input type="text" v-model="info">
      <button @click="submitInfo">提交信息</button>
  </div>
</template>

<script>
export default {
    name:"Demo",
    data(){
        return {
            info:""
        }
    },
    methods:{
        submitInfo(){
            // 方式1
            this.$router.push("/info/"+this.info)
            // 方式2
            this.$router.push(`/info/${this.info}`)
            // 方式3
            this.$router.push("/info", {
                name:"info",
                params:{
                    info: this.info
                }
            })
        }
    }
}
</script>
```





### query 形式传递参数







### params 形式传递参数





todo







### props 配置



- 优势
  - 路由组件接收参数时变得简单，以前：`{{$route.params|query.id}}`, 使用 `props` `{{id}}`
- 三种形式
  - 布尔形式，只能传递 `params` 参数
  - 对象形式
  - 函数形式



**布尔形式**

`/src/router/index.js`

```javascript
{
    path: "/chuancan/:id",
    props: ture    
}
```

`chuancan.vue`

```html
<template>
	<span>{{id}}</span>
</template>
<script>
	export default{
        name: "Chuancan",
        props: [id]
    }
</script>
```



**对象形式**

```javascript
{
    path: "/chuancan/:id",
    props: {id: 1}  
}
```



**函数形式**

```javascript
{
    path: "/chuancan",
    props($route){
        return: {id: $route.params|query.id}
    }  
}
```



# 标签



### `router-link`



- 作用，路由之间跳转
- 原理 `vue` 将 `router-link` 标签最终转为 `a` 标签实现跳转



```html
<!-- 测试路由 -->
    <router-link class='a' active-class='active'  to="/About">About</router-link>
    <router-link class='b' active-class='active' to='/Home'>Home</router-link>
```



**对象写法**

==对象写法 to 得加 `:`==

```vue
 <router-link :to="
      {
          path: '/chuancan1',
          params: {
              id: 1,
              name: 'zs'
          }
      }">传参1</router-link>
```





#### router-link replace 属性



控制==路由==的前进和后退



![image-20220416143042659](https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220416143043.png)





### `router-view`



- [ ] todo





# $router, \$router



- `App.vue` 中注册路由后，路由组件和非路由组件都有 `$route` 和 `$router` 实例

- `$route`
  - 用作获取路由信息，`path`,`name`,`params` ,`query` 等
- `$router`
  - 编程式跳转路由 (`replace`, `push`)





# 编程式路由跳转





### 使用

- 什么时候声明式路由，什么时候编程式跳转路由？
  - 简单跳转 -> 声明式（比如，到登录页）
  - 复杂 -> 编程式（在登录页输入账号密码后，跳转到首页）
- api
  - `$router.push()`
  - `$router.replace()`
  - `$router.forward()`
  - `$router.go(n)`
  - `$router.back()`





**示例**

```js
// push 示例
this.$router.push({
    name: 'xxx' // 路由组件名字,
    path: '/a/b' // 路径
    params:{ // 路由传参（params 形式）
    	id: "xxx", // 
    	name: 'xxx'
	},
    query:{
                  
    }                  
})

this.$router.replace({
    name: 'xxx' // 路由组件名字,
    path: '/a/b' // 路径
    params:{ // 路由传参（params 形式）
    	id: "xxx", // 
    	name: 'xxx'
	},
    query:{
                  
    }                  
})

this.$router.forward()
this.$router.back()
this.$router.go(3)
```



### 重写 push & replace 方法



- 问题
  - 多次调用 `this.$router.push(...)` 会报错 `NavigationDuplicated`
- 解决方式
  - 重写 `push`, `replace`
  - 参考：https://www.bilibili.com/video/BV1Vf4y1T7bw?p=10&spm_id_from=pageDriver



`router/index.js`

```vue
let originPush = VueRouter.prototype.push
let originReplace = VueRouter.prototype.replace

// 重新设置 push,replace 属性
VueRouter.prototype.push=function(location){ // 不好的地方：以后push 只能传一个参数
	originPush(this, localtion, ()=>, ()=>)
}
VueRouter.prototype.replace=function(location){
	originReplace(this, localtion, ()=>, ()=>)
}
```



其它组件中正常用 `push`, `replace` 跳转即可

`Home.vue`

```vue
export default function({

	methods:{
		sendInfo(){
			this.$router.push("/home")
		}
	}
})
```





# 路由生命周期



### 路由组件特有的生命周期钩子



- `activated()`，组件被激活时触发
- `deactivated()`，组件失活时触发



<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220416192534.png" alt="image-20220416192534532" style="zoom:50%;" />



### 缓存路由组件



切换路由时，让该路由不被销毁



<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220416192000.png" alt="image-20220416192000572" style="zoom:50%;" />





# 路由守卫:star:

- 分类
  - 全局
  - 独享
  - 组件内守卫



### 全局

- 全局

  - `afterEach()`, `beforeEach()`

  ```html
  // 配置全局、前置路由守卫
  // 什么时候执行？ 1. 初始化时， 2. 切换路由之前调用（不一定切换成功）
  router.beforeEach((to, from, next) => { // 箭头函数形式
      // to: 上一个路由配置
      // from: 下一个路由配置
      console.log(to, from)
      if(to.path == '/news/home' || to.name == 'a'){
          if(localStorage.getItem("school") == '北京'){
              next()
          } else {
              alert("无权限查看")
          }
      }
  
  })
  
  // 后置路由守卫
  // 什么时候执行？ 1. 初始化时， 2. 切换路由之后调用（一定切换过了）
  router.afterEach((to, from) => {
  
  })
  ```

  



### 独享



- `beforeEnter()`
- 没有后置（不跟全局守卫一样）



```html
 				{
                    // 子路由不需要 '/'
                    path:'news',
                    component: News,
                    beforeEnter: (to, from, next) => {
                        console.log("news 路由独享")
                        // 判断逻辑
                    }
                },
```





### 组件内路由守卫



- 写到组件内，而非路由内
- `beforeRouteEnter (to, from, next)`, `beforeRouteLeave (to, from, next)`



```vue
<template>
    <div class="news">
        <h2>Home 下的 news</h2>
    </div>
  
</template>

<script>
    export default {
        name:'News',

        // 通过路由规则，进入该组件时被调用
        beforeRouteEnter (to, from, next) {
            // 控制逻辑

            // 放行
            next()
        },

        // 通过路由规则，离开该组件时被调用
        beforeRouteLeave (to, from, next) {
            // 控制逻辑

            // 放行
            next()
        }
    }
</script>

<style>
    .news{
        color: red;
    }
</style>
```





#  router 两种工作模式



- `hash`
  - url 会有 `#`
  - `#` 后面的字符串不会发送给服务器。比如，`/news/#/home` ，则真正请求的路径是 `/news/`
- `history`
  - url 中不会有 `#`



| 对比     | hash     | history |
| -------- | -------- | ------- |
| 兼容性   | 好       | 差      |
| 打包     |          |         |
| 发送资源 | 不会发送 | 会      |







# 注意事项



1. 一个应用所有的路由器都写在一个文件下 `router/index.js`

2. 路由组件分类

   - 普通组件，（放在 `components` 目录下）
   - 路由组件，（放在 `pages` 目录下）

3. 在页面中切换路由时，一个组件被创建，另一个组件被销毁

   <img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220416093606.png" alt="image-20220416093606431" style="zoom:33%;" />

4. 每个组件都有自己的 `$route` 属性，里面存储着自己的路由信息
5. 整个应用只有一个 router，可以通过组件的 `$router` 属性获取到







# 相关面试题



1. 路由传递参数（对象写法）path 是否可以可以 params 参数一起使用？
2. 如何指定 params 参数可传可不传？
   1. 路径发生变化
      - 传递时： `#/chuancan2/1/zs`
      - 不传递：`#/` (路径都不正常了)
3. params 参数可以传递也可以不传递，但是如果传递的是空串，如何解决？
4. 路由传参能用 props 吗？
   - 可以，而且路由组件接收参数，会变的很简单







一般都是直接取，`$route.params.keyword`,`$route.query.id`