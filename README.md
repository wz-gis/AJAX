# AJAX

## 1 AJAX简介

AJAX 全称为 Asynchronous JavaScript And XML，就是异步的 JS 和 XML。
 通过 AJAX 可以在浏览器中向服务器发送异步请求，最大的优势：无刷新获取数据。
 AJAX 不是新的编程语言，而是一种将现有的标准组合在一起使用的新方式。

## 2 AJAX的特点

### AJAX 的优点

- 可以无需刷新页面而与服务器端进行通信。

- 允许你根据用户事件来更新部分页面内容。

### AJAX 的缺点

- 没有浏览历史，不能回退

- 存在跨域问题(同源)

- SEO 不友好

## 3 HTTP

HTTP（hypertext transport protocol）协议『超文本传输协议』，协议详细规定了浏览器和万维网服务器之间互相通信的规则。
约定, 规则

### **请求报文**

重点是格式与参数

    请求行      method:POST/get  url:/s?ie=utf-8  版本:HTTP/1.1 
    请求头      Host: atguigu.com
    			Cookie: name=guigu
    			Content-type: application/x-www-form-urlencoded
    			User-Agent: chrome 83
    空白行		(没有任何内容，起隔离作用)
    请求体      username=admin&password=admin

### **响应报文**

```
状态行      版本：HTTP/1.1  http状态码：200  OK
响应头      Content-Type: text/html;charset=utf-8
			Content-length: 2048
			Content-encoding: gzip
空白行    
响应体      <html>
            <head>
            </head>
            <body>
                <h1>哈哈哈</h1>
            </body>
        </html>
```

## 4 Express的简单使用

- 首先在vscode中项目列表空白处右键选择在终端打开

- 然后先输入npm init --yes

- 再输入npm i express

- 然后使用express，在js中编写一下代码

```javascript
//1. 引入express
const express = require('express');

//2. 创建应用对象
const app = express();

//3. 创建路由规则
// request 是对请求报文的封装
// response 是对响应报文的封装
app.get('/', (request, response) => {
    //设置响应
    response.send('HELLO EXPRESS');
});

//4. 监听端口启动服务
app.listen(8000, () => {
    console.log("服务已经启动, 8000 端口监听中....");
});
```

- 然后在js文件夹上右击，点击在终端打开，输入node expressgg.js（注意：expressgg是你js的文件名）
- 然后再网页中输入127.0.0.1：8000

## 5 练习

### 5.1 ajax的ie浏览器缓存问题

![image-20210319182114949](AJAX.assets/image-20210319182114949.png)

### 5.2 跨域脚本错误信息的获取问题

HTML5 新的规定:允许本地获取到跨域脚本的错误信息的，但有两个条件：

一是跨域脚本的服务器必须通过  Access-Control-Allow-Origin 头信息允许当前域名可以获取错误信息

二是网页里的 script 标签也必须指明 src  属性指定的地址是支持跨域的地址，也就是 crossorigin 属性。有了这两个条件，就可以获取跨域脚本的错误信息：

 

```html
<link crossorigin="anonymous" href="https://cdn.bootcss.com/twitter-bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
```

    <script crossorigin="anonymous" src="https://cdn.bootcdn.net/ajax/libs/jquery/3.5.1/jquery.min.js"></script>

## 6 跨域

比如你的网站的页面地址在：http://127.0.0.1:8080，然后你向后台发送了一次POST请求，但是后台的地址为：http://10.0.0.1:5050，此时的IP地址并不一样（主域名/IP不同）就非常明显的违背了同源政策，即为跨域，那么作为一个前端人员，**跨域并不是前端的原因，而是后台的原因**（甩锅），就好比后台做了一个接口，但是接口并不允许跨域的请求访问这个接口，如果是前后端分离式开发，此问题可直接让后台添加跨域请求允许就好了。

　　那么问题来了，后台是自己（比如自己搭的NODE后台，或者请求本地资源，也报了这个跨域的错误），此时怎么解决呢？刚好我也遇到了这个问题，其实很简单，仔细理解一下跨域就知道问题在哪里了，接下来继续往下面看。

　　首先，同源限制了哪些条件？

| 当前页面url               | 被请求页面url                   | 是否跨域 | 原因                           |
| ------------------------- | ------------------------------- | -------- | ------------------------------ |
| http://www.test.com/      | http://www.test.com/index.html  | 否       | 同源（协议、域名、端口号相同） |
| http://www.test.com/      | https://www.test.com/index.html | 跨域     | 协议不同（http/https）         |
| http://www.test.com/      | http://www.baidu.com/           | 跨域     | 主域名不同（test/baidu）       |
| http://www.test.com/      | http://blog.test.com/           | 跨域     | 子域名不同（www/blog）         |
| http://www.test.com:8080/ | http://www.test.com:7001/       | 跨域     | 端口号不同（8080/7001）        |

　　下面来谈谈解决方案

第一，在我们搭建NODE的后台框架的时候，如果没有配置跨域允许，是会阻挡跨域请求的。

　　解决办法为，增加一个中间件处理（此处默认使用了EXpress或Koa框架的后台），在后台代码中，加入我之前写的那段代码就好了

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```javascript
　　　app.all('*', function(req, res, next) {
　　　res.header("Access-Control-Allow-Origin", "*");
           res.header("Access-Control-Allow-Headers", "X-Requested-With");
           res.header("Access-Control-Allow-Methods","PUT,POST,GET,DELETE,OPTIONS");
           res.header('Access-Control-Allow-Headers', 'Content-Type, Content-Length, Authorization, Accept, X-Requested-With , yourHeaderFeild');
           res.header("X-Powered-By",' 3.2.1')
           res.header("Content-Type", "application/json;charset=utf-8");
           next();
           });
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

　　这段代码，大概就是增加了允许访问后台的一些请求头（header）、方法（PUT,POST,GET,DELETE,OPTIONS）等

　　第一句则是意思为允许所有origin源访问（此处还有一坑，我后面再描述）

　　如果后台这样配置了却依然没有解决的话，就仔细检查一下前后端是否在Origin、Headers、Methods、Content-Type的配置上是不是有不一样的地方，以ajax请求为例，例如他错误提示为文件类型不允许，那么就需要前端在请求中加入"Content-Type"这一项配置，与后台配置相吻合，其他的也是一样。

　　以上，自己搭建的NODE跨域问题就基本解决了，此外node里有一个第三方模块：cors，可以直接npm install 一下，然后引入、使用，只用两句代码就可以简单实现跨域允许，就省去上面一大段代码了。

### cos解决跨域

app.all('/cors-server', (request, response) => {
    //设置响应头
    response.setHeader("Access-Control-Allow-Origin", "*");
    response.setHeader("Access-Control-Allow-Headers", '*');
    response.setHeader("Access-Control-Allow-Method", '*');
    // response.setHeader("Access-Control-Allow-Origin", "http://127.0.0.1:5500");
    response.send('hello CORS');
});*


**1 CORS 是什么？**
CORS（Cross-Origin Resource Sharing），跨域资源共享。CORS 是官方的跨域解决方
案，它的特点是**不需要在客户端做任何特殊的操作，完全在服务器中进行处理**，支持 get 和 post 请求。跨域资源共享标准新增了一组 HTTP 首部字段，允许服务器声明哪些源站通过浏览器有权限访问哪些资源
**2 CORS 怎么工作的？**
CORS 是通过设置一个响应头来告诉浏览器，该请求允许跨域，浏览器收到该响应以后就会对响应放行。
**3 CORS 的使用主要是服务器端的设置：**
router.get("/testAJAX" , function (req , res) {
//通过 res 来设置响应头，来允许跨域请求
//res.set(“Access-Control-Allow-Origin”,“http://127.0.0.1:3000”); res.set(“Access-Control-Allow-Origin”,"*");
res.send(“testAJAX 返回的响应”);
});



