
<!-- TOC -->

- [1. axios](#1-axios)
    - [1.1. API](#11-api)
        - [1.1.1. get 请求](#111-get-%E8%AF%B7%E6%B1%82)
        - [1.1.2. post 请求](#112-post-%E8%AF%B7%E6%B1%82)
        - [1.1.3. 其它请求方式](#113-%E5%85%B6%E5%AE%83%E8%AF%B7%E6%B1%82%E6%96%B9%E5%BC%8F)
    - [1.2. 响应](#12-%E5%93%8D%E5%BA%94)
        - [1.2.1. 响应数据格式](#121-%E5%93%8D%E5%BA%94%E6%95%B0%E6%8D%AE%E6%A0%BC%E5%BC%8F)
        - [1.2.2. 接收响应](#122-%E6%8E%A5%E6%94%B6%E5%93%8D%E5%BA%94)
    - [1.3. 创建实例](#13-%E5%88%9B%E5%BB%BA%E5%AE%9E%E4%BE%8B)
    - [1.4. 配置](#14-%E9%85%8D%E7%BD%AE)
        - [1.4.1. 请求配置](#141-%E8%AF%B7%E6%B1%82%E9%85%8D%E7%BD%AE)
        - [1.4.2. 默认配置](#142-%E9%BB%98%E8%AE%A4%E9%85%8D%E7%BD%AE)
    - [1.5. 添加拦截器](#15-%E6%B7%BB%E5%8A%A0%E6%8B%A6%E6%88%AA%E5%99%A8)
        - [1.5.1. axios 添加拦截器](#151-axios-%E6%B7%BB%E5%8A%A0%E6%8B%A6%E6%88%AA%E5%99%A8)
        - [1.5.2. 实例添加拦截器](#152-%E5%AE%9E%E4%BE%8B%E6%B7%BB%E5%8A%A0%E6%8B%A6%E6%88%AA%E5%99%A8)
- [2. 和 vue 一起用](#2-%E5%92%8C-vue-%E4%B8%80%E8%B5%B7%E7%94%A8)
    - [2.1. 二次封装](#21-%E4%BA%8C%E6%AC%A1%E5%B0%81%E8%A3%85)

<!-- /TOC -->


>参考，这个就行了 <a href = 'https://www.cnblogs.com/nogodie/p/9853660.html'>参考</a>



# 1. axios



## 1.1. API



### 1.1.1. get 请求



- get 请求通过 `url` 传参有两种形式
  1. 拼接
  2. 对象配置



**url 传参形式1、拼接形式**

url: `/user/ID=12345`, `/user/12345`

```javascript
// 为给定 ID 的 user 创建请求
axios.get('/user?ID=12345')
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });

// 路径变量形式
axios.get('/user/'+12345)
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });

```





**url 传参形式2、对象形式**

uri: `/user/ID=xxx`

```javascript
axios.get('/user', {
    params: {
      ID: 12345
    }
  })
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```





### 1.1.2. post 请求



`post` 请求传递 `request body` 只有一种对象配置形式



url 一看就能看出来吧

```javascript
axios.post('/user', {
    firstName: 'Fred',
    lastName: 'Flintstone'
  })
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```





### 1.1.3. 其它请求方式

也支持 `delete`, `options`, `put`, `patch` 方式的请求

- `axios.delete(url[, config])`

- `axios.head(url[, config])`

- `axios.options(url[, config])`

- `axios.put(url[, data[, config]])`

- `axios.patch(url[, data[, config]])`



## 1.2. 响应



### 1.2.1. 响应数据格式

某个请求的响应包含以下信息

```
{
  // `data` 由服务器提供的响应
  data: {},

  // `status` 来自服务器响应的 HTTP 状态码
  status: 200,

  // `statusText` 来自服务器响应的 HTTP 状态信息
  statusText: 'OK',

  // `headers` 服务器响应的头
  headers: {},

   // `config` 是为请求提供的配置信息
  config: {},
 // 'request'
  // `request` is the request that generated this response
  // It is the last ClientRequest instance in node.js (in redirects)
  // and an XMLHttpRequest instance the browser
  request: {}
}
```



### 1.2.2. 接收响应



`then` 接收响应

```javascript
axios.get('/user/12345')
  .then(function(response) {
    console.log(response.data);
    console.log(response.status);
    console.log(response.statusText);
    console.log(response.headers);
    console.log(response.config);
  });
```





## 1.3. 创建实例

可以使用自定义配置新建一个 axios 实例

- api: `axios.create([config])`



```javascript
const instance = axios.create({
  baseURL: 'https://some-domain.com/api/',
  timeout: 1000,
  headers: {'X-Custom-Header': 'foobar'}
});
```







## 1.4. 配置



### 1.4.1. 请求配置

 `axios` 有个 发送请求的 api，都有一个 `config` 参数



- `axios(config)`

- `axios(ur, [config`)

- 请求别名 `api`

  

  <img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220426121932.png" alt="image-20220426121932322" style="zoom:50%;" />

   在使用别名方法时， `url`、`method`、`data` 这些属性都不必在配置中指定。



### 1.4.2. 默认配置

所有请求都会使用的配置

- 全局 `axios` 配置
- 通过 实例配置



**全局的 axios 默认值**

```javascript
axios.defaults.baseURL = 'https://api.example.com';
axios.defaults.headers.common['Authorization'] = AUTH_TOKEN;
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
```



**自定义实例默认值**

```javascript
// Set config defaults when creating the instance
const instance = axios.create({
  baseURL: 'https://api.example.com'
});

// Alter defaults after instance has been created
instance.defaults.headers.common['Authorization'] = AUTH_TOKEN;
```





## 1.5. 添加拦截器



- 种类
  - 请求拦截器
  - 响应拦截器
- 拦截器执行顺序
  - 请求拦截器，发送请求之前
  - 响应拦截器，`then` 或 `catch` 接收响应之前



### 1.5.1. axios 添加拦截器



```javascript
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
  }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
  });

// 添加响应拦截器
axios.interceptors.response.use(function (response) {
    // 对响应数据做点什么
    return response;
  }, function (error) {
    // 对响应错误做点什么
    return Promise.reject(error);
  });
```





### 1.5.2. 实例添加拦截器

```javascript
const instance = axios.create();
instance.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
  }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
  });

// 添加响应拦截器
instance.interceptors.response.use(function (response) {
    // 对响应数据做点什么
    return response;
  }, function (error) {
    // 对响应错误做点什么
    return Promise.reject(error);
  });
```









# 2. 和 vue 一起用



- 安装，`npm i axios`
- 在 `vue` 中使用 `axios` 一般都是在 `/src/api/request.js` 中写



`request.js`

```javascript

```







## 2.1. 二次封装

- 为什么需要二次封装？
  - 主要是添加请求处理器，响应处理器完成业务需求