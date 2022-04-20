# hvue-router:star::star:



==及其重要，使用 vue ，就是在写路由==



## 路由工作原理



**示例图**

<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220415224624.png" alt="image-20220415224624235" style="zoom: 25%;" />





## 关于 vue-router





<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220415224738.png" alt="image-20220415224738416" style="zoom:33%;" />







## 基础内容



### 理解路由



<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220415224931.png" alt="image-20220415224931687" style="zoom:33%;" />







### 安装



和 `vuex` 一样，`vue2` 需要 `vue-router` 的 3 版本， `vue3` 需要 `vue-router` 的 4 版本

我使用的是 `vue2`，下载` npm i vue-router@3`



### 简单使用



1. 安装
2. 创建 `router`
   1. 创建文件`router/index.js` （路由器一般写在这个文件下）
   2. `import VueRouter from "vue-router";`
   3. 引入组件
   4. new VueRouter（配置 k=v, k: path, v:组件）
   5. 暴露
3. 在 `main.js`
   1. 引入插件 `vue-router`
   2. 引入写好的 `router`
   3. Vue.use(vue-router)
   4. 给 `vm` 配置 router





### 嵌套路由

- [ ] todo





### 路由 query 参数



- [ ] todo



### 给路由命名



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







### params 参数





todo







### props 配置



todo







### router-link replace 属性



控制==路由==的前进和后退



![image-20220416143042659](https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220416143043.png)







### 编程式路由跳转



<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220416191458.png" alt="image-20220416191458644" style="zoom:50%;" />



### 缓存路由组件



切换路由时，让该路由不被销毁



<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220416192000.png" alt="image-20220416192000572" style="zoom:50%;" />



### 路由组件特有的生命周期钩子



- `activated()`，组件被激活时触发
- `deactivated()`，组件失活时触发



<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220416192534.png" alt="image-20220416192534532" style="zoom:50%;" />





## 路由守卫:star:

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





##  router 两种工作模式



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







## 注意事项



1. 一个应用所有的路由器都写在一个文件下 `router/index.js`

2. 路由组件分类

   - 普通组件，（放在 `components` 目录下）
   - 路由组件，（放在 `pages` 目录下）

3. 在页面中切换路由时，一个组件被创建，另一个组件被销毁

   <img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220416093606.png" alt="image-20220416093606431" style="zoom:33%;" />

4. 每个组件都有自己的 `$route` 属性，里面存储着自己的路由信息
5. 整个应用只有一个 router，可以通过组件的 `$router` 属性获取到















## 提供的标签





### vue-link

- 作用：路由跳转

- 原理：vue-link 最终会转为 a 标签



```html
<!-- 测试路由 -->
    <router-link class='a' active-class='active'  to="/About">About</router-link>
    <router-link class='b' active-class='active' to='/Home'>Home</router-link>
```





### vue-view



指定组件呈现的位置





# vue 组件库



- 移动端
  - Vant
  - Cube UI
  - Mint UI
- PC 端
  - Element UI
  - IView UI



==不要记这些组件的 api，看文档就行了==



## 使用 element ui



环境， vue 2

- 按需引入

  1. 安装 babel-plugin-component, npm install babel-plugin-component -D
     - 这是一个 vue 插件，每个项目都需要用

  2. 配置 `babel.config.js`

     ```js
     module.exports = {
       presets: [
         '@vue/cli-plugin-babel/preset',
         ["@babel/preset-env", { "modules": false }]
       ],
       "plugins": [
         [
           "component",
           {
             "libraryName": "element-ui",
             "styleLibraryName": "theme-chalk"
           }
         ]
       ]
     }
     
     ```

  3. 接下来，如果你只希望引入部分组件，比如 `Button` 和 `Select`，那么需要在 `main.js` 中写入以下内容

     ```js
     import Vue from 'vue';
     import { Button, Select } from 'element-ui';
     import App from './App.vue';
     
     Vue.component(Button.name, Button);
     Vue.component(Select.name, Select);
     
     new Vue({
       el: '#app',
       render: h => h(App)
     });
     ```

     



