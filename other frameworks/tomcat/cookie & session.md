# Cookie

- `cookie` 可以看作一个容器，以 `K -V ` 形式存储。存储的内容并不在服务端，而是在浏览器等客户端
- 用 `cookie` 无非是在访问某些 RUL 的时候，传递一些数据给服务端。
- `cookie` 用的很少，了解即可





# Session

1. `Tomcat` 如何生成 `Session`
2. `Session` 对象的有效期是多少？
3. `Tomcat` 是如何根据 `Session` 判断某个请求的用户的？
4. 服务端和客户端，关于 `Session` 的交互过程是怎样的？

------

关于 `Session` ，先看下 `Tomcat` 文档中描述：

- A **Session** is the Catalina-internal facade for an `HttpSession` that is used to maintain state information between requests for a particular user of a web application.

这句话提供了一个非常重要的信息：通过 `Session` 判断这个请求（`request` ）对应哪一个用户。**这个功能非常重要**



那么 `Spring boot` 中如何生成 `session` 呢？

```java
@Api(tags = "session 操作")
@RestController
@RequestMapping("session")
@Slf4j
public class Session_Controller {


    @GetMapping("get")
    public Result<String> get(HttpServletRequest request, HttpServletResponse response){
        HttpSession session = request.getSession(); // return a new or old session
//        session.setMaxInactiveInterval();

        return Result.ok();
    }

```

可以看到，使用的时候非常简单，只需要调用 `HttpServletRequest#getSession()`，之后`Tomcat` 会在响应头上加上**Set-Cookie**。响应示例：

```
Connection: keep-alive
Content-Type: application/json
Date: Sun, 12 Jun 2022 08:30:59 GMT
Keep-Alive: timeout=60
Set-Cookie: JSESSIONID=DF72E74AD01B3D96160D5A817C0086E3; Path=/; HttpOnly
```

浏览器看到 **Set-Cookie**就会在本地存储一个 **Cookie**，之后访问服务器的每一个新请求都会加上 **Cookie** 请求头

```
Cache-Control: no-cache
Connection: keep-alive
Cookie: JSESSIONID=DF72E74AD01B3D96160D5A817C0086E3
```



**Session 对象有效期**

下面是文档说明。`session` 对象（`httpServletRequest#getSession()`) 默认的有效期是 `1800s` 也就是 `30 min` 

```java
package javax.servlet.http;
public interface HttpSession {
    /**
     * Specifies the time, in seconds, between client requests before the
     * servlet container will invalidate this session. A zero or negative time
     * indicates that the session should never timeout.
     *
     * @param interval
     *            An integer specifying the number of seconds
     */
    public void setMaxInactiveInterval(int interval);
}
```



## domain 和 path

1. `Cookie` 可以设置 `domain` 和 `path`, `Session` 可以设置这两个配置项吗？

----------------------



- `Sevlet`没有给 `session` 提供设置 domain, path 的 API，因此 `session` 是不可以设置这两个属性的。

- `Session` 的 `domain` , `path` 属性是固定的。也就是说所有请求都会携带 `JSESSIONID`, 而 `cookie` 只能被满足 `path` 条件的 URL 携带过去。那么什么样的 URL 满足 `cookie` 的 `path` 设置呢？参考：https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies



**代码示例**

```java
@RequestMapping("session")
public class Session_Controller {


    @GetMapping("get")
    public Result<String> get(HttpServletRequest request, HttpServletResponse response){
        HttpSession session = request.getSession(); // return a new or old session
//        session.setMaxInactiveInterval();

        Cookie cookie = new Cookie("message", "hello");
//        cookie.setDomain("a");
        cookie.setPath("/session/get");
        response.addCookie(cookie);
        return Result.ok();
    }

    @GetMapping("no")
    public Result<String> no(HttpServletRequest request){
        return Result.ok();
    }
}

```



首次访问 `/session/get` 时

<img width='60%' src='https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220612183607.png'>

 

访问 `/session/no`

<img width='60%' src='https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220612183629.png'>

再次 `/session/get` 时

<img width='60%' src='https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220612183620.png'>

看下 `chrome` 浏览器中 `cookie` 信息

![image-20220612183954125](https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220612183954.png)





## 分布式 session（<u>todo</u>）

- 分布式 `session` 的用途？
- 分布式 `session` 的实现方式（主要关注 `spring` 提供的方案）

------

