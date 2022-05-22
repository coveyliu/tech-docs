# vue 组件库



- 移动端
  - Vant
  - Cube UI
  - Mint UI
- PC 端
  - Element UI
  - IView UI



==不要记这些组件的 api，看文档就行了==



# 使用 element ui

环境， vue 2

- 按需引入

  1. 安装 `babel-plugin-component`,

     -  **命令**：npm install babel-plugin-component -D

     - 这是一个 vue 插件，每个项目都需要用

  2. 下载 `element-ui`

     - npm i element-ui

  3. 配置 `babel.config.js`（已经存在了， 直接替换为下面的内容即可）

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

  4. 在 `main.js` 中引入部分组件

     比如， `Button`, `Select` 组件，示例如下：

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

     