
# 0X00 HTTP协议与HTTPS协议

## HTTP协议
HTTP协议是面向事务的(transaction-oriented)应用层协议，它是万维网上能够可靠地交换文件(包括文本、声音、图像等各种多媒体文件)的重要基础。HTTP不仅传送完成超文本跳转所必需的信息，而且也传送任何可从互联网上得到的信息，如文本、超文本、声音和图像等。

HTTP协议目前最新版本是1.1，HTTP是一种无状态的应用层协议。无状态是指Web浏览器与Web服务器之间不需要建立持久的连接。HTTP请求只能由客户端发起，而服务器不能主动向客户端发送数据。


## HTTPS协议
HTTPS（全称：HyperText Transfer Protocol over SecureSocket Layer），是以安全为目标的HTTP通道，在HTTP的基础上通过传输加密和身份认证保证了传输过程中的安全性。


## HTTP和HTTPS的区别
HTTPS的安全基础是SSL，即在HTTP下加入SSL层。HTTPS通过安全传输机制进行转送数据，这种机制可以保护网络传送的所有数据的隐秘性和完整性，可以降低非侵入性拦截攻击的可能性。

两者的主要区别：

- HTTP是超文传输协议，信息是明文传输，HTTPS是具有安全性的SSL加密传输协议。
- HTTP和HTTPS的连接方式不同，HTTP采用80端口连接，而HTTPS采用443端口。
- HTTPS协议需要用到ca申请证书。HTTP不需要。
- HTTP连接相对简单是无状态的，而HTTPS协议是由HTTP+SSL协议构建的可进行加密传输、身份认证的网络协议，相对来说比HTTP更加安全。


# 0X01 HTTP协议工作原理
在用户访问 [http://www.baidu.com/index.html](http://www.baidu.com/index.html) 的链接后，浏览器和Web服务器执行以下动作:

1.  浏览器分析超链接中的URL 
2.  浏览器向DNS请求解析www.baidu.com的IP地址 
3.  DNS将解析出的IP地址220.181.38.148返回浏览器 
4.  浏览器与服务器建立连接(80端口) 
5.  浏览器请求文档: GET /index.html 
6.  服务器给出响应，将文档index.html发送给浏览器 
7.  释放TCP连接 
8.  浏览器显示index.htmI中的内容 

![](https://img-blog.csdnimg.cn/ae7f063672a54d2f909f79e96545612b.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bO05ZCM5a24,size_17,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=XeBVs&originHeight=511&originWidth=516&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**持久性链接与非持久性链接**

1.  **持久性链接**<br />即在一个连接中，可以进行多次文档的请求和响应。服务器在发送完响应后，并不立即释放连接，浏览器可以使用该连接继续请求其他文档。连接保持的时间可以由双方进行协商。 

2. ** 非持久性链接**<br />即浏览器每请求一个Web文档， 就创建一个新的连接，当文档传输完毕后，连接就立刻被释放。 
   - HTTP 0.9、HTTP 1.0采用此连接方式。
   - 对于请求的Web页中包含多个其他文档对象(如图像、声音、视频等)的链接的情况，由于请求每个链接对应的文档都要创建新连接,所以效率低下。


# 0X02 HTTP报文分析

## HTTP报文结构
**1. 请求报文**<br />HTTP请求包含三个部分，分别是请求行（请求方法），请求头（消息报头）和请求正文。<br />![](https://img-blog.csdnimg.cn/7eb09b05993e495193522715ec084964.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bO05ZCM5a24,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=Y7Yay&originHeight=500&originWidth=1267&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**2. 响应报文**<br />HTTP响应也由三部分组成，分别是响应行、响应头（消息报头）和响应正文（消息主题）。<br />![](https://img-blog.csdnimg.cn/5c2b9e953cc44ec39ea6c9a0ba081810.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bO05ZCM5a24,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=PP80Z&originHeight=502&originWidth=1178&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## HTTP常见请求方法

1.  **GET：**用于获取请求页面的指定消息（以实体的形式）。 
2.  **HEAD：**除了服务器不能在响应里返回消息主题外。其他的都与GET方法相同。 
3.  **POST：**POST方法也与GET方法相似，但是GET方法没有请求内容，而POST是有请求内容的。POST多用于向服务器发送大量数据。 
4.  **PUT：**请求服务器把请求中的实体存储在请求资源下。如果请求资源已经在服务器中存在。那么将会用此请求中的数据替换原先的数据。 
5.  **DELETE:**请求源服务器删除请求的指定资源。（一般会关闭此方法，因为客户端可以进行文件删除操作） 
6.  **TRACE：**激发一个远程的应用层的请求消息回路，也就是回显服务器收到的请求。 
7.  **CONNECT：**动态切换的隧道的代理。 
8.  **OPTIONS：**请求获得有由URL标识的资源在请求/响应的通信过程中可以使用的功能选项。 


## HTTP状态码
HTTP协议中的状态码是由三位数字组成，第一位数字代表响应类别：

- **1xx：**表示请求已被成功接收。（100~101）
- **2xx：**服务器成功处理请求。（200~206）
- **3xx：**重定向，代表访问资源已移动，并提供新资源地址位置。（300~305）
- **4xx：**客户端错误代码。（400~415）
- **5xx：**服务器内部错误。（500~505）

**常见的状态码描述：**<br />**200:**客户端请求成功，是最常见的状态。<br />**204：**请求已经成功处理，但是没有内容返回。<br />**206：**服务器已经完成了部分GET请求。<br />**301:**永久重定向，资源已永久分配新URL。<br />**302:**临时重定向，资源已临时分配新URL。<br />**303：**临时重定向，期望使用GET定向获取。<br />**304：**发送的附带条件请求未满足。<br />**307：**临时重定向，POST不会变成GET。<br />**404:**请求资源不存在，是最常见的状态。<br />**400:**客户端请求有语法错误，不能被服务器所理解。<br />**401:**请求未经授权。<br />**403:**服务器收到请求，但是拒绝提供服务。<br />**500:**服务器内部错误，是最常见的状态。<br />**503:**服务器当前不能处理客户端的请求，一段时间后可能恢复正常。<br />详情访问：[https://www.cnblogs.com/xflonga/p/9368993.html](https://www.cnblogs.com/xflonga/p/9368993.html)


## HTTP消息
HTTP消息又称为HTTP头，由四部分组成，分别是请求头、响应头、普通头和实体头。

### 请求头
请求头只出现在HTTP请求中

1.  **Host**<br />主要用于指定被请求资源的主机和端口号 
2.  **User-Agent**<br />允许客户端将他的操作系统、浏览器、和其他属性告诉服务器。 
3.  **Referer**<br />代表当前访问的URl的上一个URL。即用户是从什么地方来到本页面。 
4.  **Cookie**<br />常用来表示请求者身份。 
5.  **data**<br />请求被发送时的日期和时间。 
6.  **Range**<br />请求实体的部分内容，多线程下载一定会用到此请求头。 
7.  **x-forwar-for**<br />XXF头，代表请求端的IP，可以有多个。 
8.  **Accept**<br />指定客户端接受哪些MIME类型的消息。 
9.  **Accept-Charset**<br />指定客户端接受的字符集。 
10.  **Accept-Encoding**<br />客户端能处理的页面编码方法，如gzip。 
11.  **Accept-Language**<br />客户端能处理的自然语言。 


### 响应头
响应头是服务器根据请求向客户端发送的HTTP头

1.  **Server**<br />服务器所使用的Web服务器名称。 
2.  **Set-Cokie**<br />向客户端设置Cookie。 
3.  **Last-Modified**<br />资源最后修改时间 
4.  **Location**<br />告诉浏览器区访问哪个页面，通常和302状态码一起使用。 
5.  **Refresh**<br />告诉浏览器定时刷新浏览器。 


### 普通头
在普通报头中，有少数报头用于所有的请求和响应消息，但不用于被传输的实体，只用于传输的消息。


### 实体头
请求和响应消息都可以传递一个实体头。实体头定义了关于实体正文和请求所标识的资源的元信息。

1.  **Content-Type**<br />向接收方指示实体的介质类型。 
2.  **Content-Encoding**<br />媒体类型的修饰符，它的值指示了已经被应用到实体正文的附加内容的编码。 
3.  **Content-Length**<br />指明实体正文的长度，以字节方式存储的十进制数字来表示。 
4.  **Last-Modified**<br />指示资源最后修改日期和时间。 


# 0X03 HTTP协议中的URL

## 什么是URL
**定义**<br />	URL是Uniform Resource Location的缩写，译为“统一资源定位符”。URL是从互 联网上得到的资源的位置和访问方法的一种简洁的表示，是互联网上标准资源的地址。互联网上的每个文件都有一个唯一的URL。

**URL格式**<br />![](https://img-blog.csdnimg.cn/628ef075b962445192fb159ad7f5fe2b.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bO05ZCM5a24,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=cGTAC&originHeight=206&originWidth=1257&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
```
相对URL: /目录/文件名.文件后缀?参数=值#标志
```

**URL中的协议**

1.  ldap-轻型目录访问协议搜索 
2.  http-超文本传输协议 
3.  file-当地电脑或网上分享的文件 
4.  https-用安全套接字层传送的超文本传输协议 
5.  news-Usenet新闻组 
6.  ftp-文件传输协议 
7.  gopher-Gopher协议 
8.  mailto-电子邮件地址 
9.  telnet-Telnet协议 


## URL编码

1.  URL只能使用ASCI字符集来通过因特网进行发送。 
2.  URL编码使用"%"其后跟随两位的十六进制数来替换非ASCII字符。 
3.  URL不能包含空格。URL编码通常使用+来替换空格。 
4.  只有字母和数字[0-9a-zA-Z]、一些特殊符号“$-_ .+!*(),"[不包括双引号]、 以及某些保留字，才可以不经过编码直接用于URL。 


## 同源策略
同源策略（SOP）是一种约定，是浏览器的核心也是最基本的安全功能，若缺少了同源策略那么浏览器的正常功能会受到影响。同源策略是浏览器的行为，是为了保护本地的数据不被Javascript代码获取回来的数据污染。如果没有同源设计策略，那么在A.com网站加载过的js脚本，就能够在没有加载过这个脚本的B.com.上的页面随意执行并被读取，这就可能造成页面混乱、被破坏以及数据窃取等不安全的行为，比如前段跨域安全。

所谓同源是指:域名，协议，端口号三者相同。

下表给出了与 URL `http://xmtxsec.top/111/index.html` 的源进行对比的示例:

| **URL** | **结果** | **原因** |
| --- | --- | --- |
| [http://xmtxsec.top/bbb/index.html](http://xmtxsec.top/bbb/index.html) | 同源 | 只有路径不同 |
| [http://xmtxsec.top/aaa/bbb/index.html](http://xmtxsec.top/aaa/bbb/index.html) | 同源 | 只有路径不同 |
| [https://xmtxsec.top/index.html](https://xmtxsec.top/index.html) | 失败 | 协议不同 |
| [http://xmtxsec.top:81/aaa/index.html](http://xmtxsec.top:81/aaa/index.html) | 失败 | 端口不同 ( `http://`<br /> 默认端口是80) |
| [http://xmtx.top/aaa/index.html](http://xmtx.top/aaa/index.html) | 失败 | 主机不同 |

