# HTTP 跨域访问

随便打开一个网页，上面的很多资源可能都来自于其他域名、协议或端口，比如图片可能来自于某个静态服务器，所用到的 js 包来自于cdn加速服务。这些都是属于跨域 HTTP 请求。但是出于安全原因，从脚本发出的跨域请求会无情的报错，导致我们在使用 ajax 或 canvas 用到跨域资源时带来不便。这里就简单的从前端角度解决跨域请求的方法。

## 跨域资源共享 (CORS) 

跨域资源共享(CORS) 是一种机制，它使用额外的 HTTP 头来告诉浏览器让运行在一个 origin (domain) 上的Web应用被准许访问来自不同源服务器上的指定的资源。

---

什么情况下需要 CORS：

* 由 XMLHttpRequest 或 Fetch 发起的跨域 HTTP 请求。
* Web 字体 (CSS 中通过 @font-face 使用跨域字体资源)。
* WebGL 贴图。
* 使用 drawImage 将 Images/video 画面绘制到 canvas。
* 样式表（使用 CSSOM）

---

进行跨域访问的请求可以分为两种：`简单请求`和`预检请求`。简单请求浏览器会直接发出请求，预检请求浏览器会先发出一个 OPTIONS 方法的请求，在获得服务器确认允许之后，才发起实际的 HTTP 请求。

如何区分简单请求和预检请求？很简单，简单请求必须满足下面条件

* 使用下列方法之一：
  * GET
  * HEAD
  * POST
* 请求头不得设置除以下集合之外的字段：
  * Accept
  * Accept-Language
  * Content-Language
  * Content-Type  
  （仅允许下面三个值：`text/plain`，`multipart/form-data`，`application/x-www-form-urlencoded`）
* 请求中的任意 XMLHttpRequestUpload 对象均没有注册任何事件监听器；
* 请求中没有使用 ReadableStream 对象。

预检请求？不是简单请求的请求就是预检请求。

### 实现方法
CORS 实现更多的还是服务端对请求响应的首部字段设置。**注意：**未按规定返回请求头，失败结果无法用状态码获知，因为状态码还会是 200，但是能被被 XMLHttpRequest 的 onerror 回调函数捕获。

#### 简单请求
* 浏览器
  * `Origin` 表明该请求来源于的域名。只要是跨域请求，浏览器都会在自动在请求头中加入该字段。
* 服务器
  * `Access-Control-Allow-Origin` 表示服务端允许跨域访问的域名。要么值为 `*` 让所有域名可以访问，要么包含 `Origin` 所指明的域名。

#### 预检请求
* 浏览器  
  首先浏览器会帮你发一个 OPTIONS 方法的预检请求，包含  
  * `Origin`
  * `Access-Control-Request-Headers` 表明正式请求中将携带的首部字段；
  * `Access-Control-Request-Method` 表明正式请求的方法。
* 服务器
  * `Access-Control-Allow-Origin`
  * `Access-Control-Allow-Headers` 表示请求中允许携带的首部字段，多个字段用逗号分隔；
  * `Access-Control-Allow-Methods` 表示允许使用哪些方法请求，多个方法用逗号分隔。

#### 带cookie的请求
CORS 默认不会发送 Cookie 和 HTTP 认证信息，想要发送 Cookie，浏览器和服务器都要做出设置。

* 浏览器

```
var invocation = new XMLHttpRequest();
invocation.withCredentials = true;
```

开发者要在AJAX中开启 withCredentials 属性，否则服务器要求设置 Cookie 浏览器也不会搭理。

* 服务器

  * `Access-Control-Allow-Credentials: true` 如果服务端返回响应未携带该首部字段，浏览器就会报错阻挡响应接收。
  * `Access-Control-Allow-Origin` 请求的首部中携带了 Cookie 信息，如果 Access-Control-Allow-Origin 的值为 “*”，请求将会失败。这里 Access-Control-Allow-Origin 必须是同意发送 Cookie 的域名。

想要看代码演示，可以[点击这里](https://github.com/zcorw/CORS-test)，演示了上面提到的各种情况。




### 参考资料：

* [MDN - HTTP 访问控制](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS)