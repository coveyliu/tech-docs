>参考，这个就行了https://www.cnblogs.com/nogodie/p/9853660.html



# axios



## API



### get 请求



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





### post 请求



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





### 其它请求方式

也支持 `delete`, `options`, `put`, `patch` 方式的请求

- `axios.delete(url[, config])`

- `axios.head(url[, config])`

- `axios.options(url[, config])`

- `axios.put(url[, data[, config]])`

- `axios.patch(url[, data[, config]])`



## 响应



### 响应数据格式

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



### 接收响应



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





## 创建实例

可以使用自定义配置新建一个 axios 实例

- api: `axios.create([config])`



```javascript
const instance = axios.create({
  baseURL: 'https://some-domain.com/api/',
  timeout: 1000,
  headers: {'X-Custom-Header': 'foobar'}
});
```







## 配置



### 请求配置

 `axios` 有个 发送请求的 api，都有一个 `config` 参数



- `axios(config)`

- `axios(ur, [config`)

- 请求别名 `api`

  

  <img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220426121932.png" alt="image-20220426121932322" style="zoom:50%;" />

   在使用别名方法时， `url`、`method`、`data` 这些属性都不必在配置中指定。



### 默认配置

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





## 添加拦截器



- 种类
  - 请求拦截器
  - 响应拦截器
- 拦截器执行顺序
  - 请求拦截器，发送请求之前
  - 响应拦截器，`then` 或 `catch` 接收响应之前



### axios 添加拦截器



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





### 实例添加拦截器

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









# 和 vue 一起用



- 安装，`npm i axios`
- 在 `vue` 中使用 `axios` 一般都是在 `/src/api/request.js` 中写



`request.js`

```javascript

```







## 二次封装

- 为什么需要二次封装？
  - 主要是添加请求处理器，响应处理器完成业务需求