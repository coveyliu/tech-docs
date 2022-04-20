# vue 脚手架 Vue CLI



- 写的 `xxx.vue` 文件，就是脚手架帮忙转换为 `html` 文件的

- 最新版本 `4.x`
- 官网 `http://cli.vuejs.org/zh/



## 安装



- 打开 cmd 命令行查看，npm 配置淘宝镜像：npm config set registry https://registry.npm.taobao.org
- 在命令行窗口，执行下面的步骤就行了
  - 👇👇👇

<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220415155452.png" alt="image-20220415155452674" style="zoom:33%;" />







## 分析脚手架结构



使用脚手架，创建一个项目，并分析结构

创建 `hello-world`, `vue create vue-hello-world`



<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220415161525.png" alt="image-20220415161525309" style="zoom:50%;" />



**分析 index.html**

![image-20220415162245272](https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220415162245.png)





## 修改脚手架配置



1. 建立 `vue.config.js` 文件
2. 配置官网 https://cli.vuejs.org/zh/config/



**示例**

<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220415163222.png" alt="image-20220415163222107" style="zoom:67%;" />







# vuex

- `vuex` 是一个插件

- 多组件共享数据用 `vuex`
- 多组件共享的数据，就把数据放在 `vuex` 中



- 什么使用 `vuex`

  - 多组件共享（读/写）某一数据

    



<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220415164155.png" alt="image-20220415164155105" style="zoom:25%;" />







## 原理





<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220415212231.png" alt="image-20220415212230801" style="zoom: 33%;" />





## 搭建 vuex

- 步骤	
  1. `npm i vuex@3`，安装，并指定版本，因为，默认的 vue 版本是3，而 vue3 安装的是 vuex4
  2. Vue.use(vuex)
  3. store
  4. 组件看见 store



- 注意:warning:
  - vue2 中只能用 vuex3
  - vue3 中用 vuex4





## 开发者工具



- [ ] todo







## 模块化（待补充）

- [ ] 待补充