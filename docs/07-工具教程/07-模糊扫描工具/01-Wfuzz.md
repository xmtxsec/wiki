
# 参考链接
- [史上最详[ZI]细[DUO]的wfuzz中文教程（一）——初识wfuzz](https://www.freebuf.com/column/163553.html)
- [史上最详[ZI]细[DUO]的wfuzz中文教程（二）——wfuzz 基本用法](https://www.freebuf.com/column/163632.html)
- [史上最详[ZI]细[DUO]的wfuzz中文教程（三）——wfuzz 高级用法](https://www.freebuf.com/column/163787.html)
- [史上最详[ZI]细[DUO]的wfuzz中文教程（四）—— wfuzz 库](https://www.freebuf.com/column/164079.html)


# Wfuzz
[Wfuzz](https://github.com/xmendez/wfuzz) 是为了促进 Web 应用程序评估中的任务，它基于一个简单的概念：它用给定有效负载的值替换对 FUZZ 关键字的任何引用。

Wfuzz 中的有效载荷是数据源。<br />这个简单的概念允许在 HTTP 请求的任何字段中注入任何输入，从而允许在不同的 Web 应用程序组件中执行复杂的 Web 安全攻击，例如：参数、身份验证、表单、目录/文件、标头等。

Wfuzz 不仅仅是一个 Web 内容扫描器：

- Wfuzz 可以通过查找和利用 Web 应用程序漏洞来帮助您保护 Web 应用程序。Wfuzz 的 Web 应用程序漏洞扫描器由插件支持。
- Wfuzz 是一个完全模块化的框架，即使是最新的 Python 开发人员也可以轻松做出贡献。构建插件很简单，只需几分钟。
- Wfuzz 为之前使用 Wfuzz 或其他工具（例如 Burp）执行的 HTTP 请求/响应公开了一个简单的语言接口。这使您可以在完整的上下文和对操作的理解的情况下执行手动和半自动测试，而无需依赖 Web 应用程序扫描程序的底层实现。

它的创建是为了促进 Web 应用程序评估中的任务，它是渗透测试人员为渗透测试人员提供的工具。


# 入门

## 基本测试
单个有效载荷
```
 wfuzz  -w /usr/share/wfuzz/wordlist/general/common.txt http://192.168.1.15/FUZZ
```
![](https://img-blog.csdnimg.cn/024a18a2905d4c73b2b665281ce29263.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2m4oSh,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=s29Oa&originHeight=517&originWidth=906&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

- ID：按执行顺序的请求编号。
- Response：显示 HTTP 响应代码。
- Lines：显示 HTTP 响应中的行数。
- Word：显示 HTTP 响应中的字数。
- Chars：显示 HTTP 响应中的字符数。
- Payload：显示使用的有效载荷。

多个有效载荷
```
 wfuzz  -w /usr/share/wfuzz/wordlist/general/common.txt -w /usr/share/wfuzz/wordlist/general/common.txt http://192.168.1.15/FUZZ/FUZ2Z
```
![](https://img-blog.csdnimg.cn/0ae42b210f4c43119da6dab607d41919.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2m4oSh,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=bNE02&originHeight=347&originWidth=1187&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 筛选
通过HTTP 响应代码过滤
```
wfuzz --hc 404 -w /usr/share/wfuzz/wordlist/general/common.txt http://192.168.1.15/FUZZ
```
![](https://img-blog.csdnimg.cn/377d89be4ccd45cbb14f5ff2b6465aa9.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2m4oSh,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=HYZ0q&originHeight=506&originWidth=904&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

指定多个值
```
wfuzz --hc 404,301 -w /usr/share/wfuzz/wordlist/general/common.txt http://192.168.1.15/FUZZ
```
![](https://img-blog.csdnimg.cn/2b4650e0fd1e4e9da53046100ce19e68.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2m4oSh,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=qqijE&originHeight=446&originWidth=1064&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

通过 HTTP 响应中的行数过滤。
```
wfuzz --hl 31 -w /usr/share/wfuzz/wordlist/general/common.txt http://192.168.1.15/FUZZ
```
![](https://img-blog.csdnimg.cn/76e504f2a64a45008cda31df986b194c.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2m4oSh,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=Pg5nr&originHeight=503&originWidth=893&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

通过 HTTP 响应中的字数过滤。
```
wfuzz --hw 80 -w /usr/share/wfuzz/wordlist/general/common.txt http://192.168.1.15/FUZZ
```
![](https://img-blog.csdnimg.cn/1c3379c267e94f02b8f98422ce99f57b.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2m4oSh,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=rE6fN&originHeight=506&originWidth=892&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

通过 HTTP 响应中的字符数过滤。
```
wfuzz --hh 1308,147 -w /usr/share/wfuzz/wordlist/general/common.txt http://192.168.1.15/FUZZ
```
![](https://img-blog.csdnimg.cn/cc597b240d4c433fb8eb8a2c44115902.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2m4oSh,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=Ovnzq&originHeight=512&originWidth=997&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# 基本用法
1、目录扫描
```
wfuzz -w /usr/share/wfuzz/wordlist/general/common.txt http://192.168.1.15/FUZZ
```

2、查找常见文件
```
wfuzz -w /usr/share/wfuzz/wordlist/general/common.txt http://192.168.1.15/FUZZ.txt
```

3、对 URL 中的参数进行测试
```
wfuzz -z range,0-10  http://192.168.1.15/include.php?cat=FUZZ
wfuzz -w /usr/share/wfuzz/wordlist/general/common.txt http://192.168.1.15/include.php?cat=FUZZ
```

4、对 POST 请求进行测试
```
wfuzz -w wordlist/others/common_pass.txt -d "uname=admin&pass=FUZZ" http://192.168.1.15/userinfo.php
```

5、Cookie
```
#添加自己的cookie
wfuzz -w wordlist/general/common.txt -b cookie=value1 http://192.168.1.15/FUZZ

#得到的HTTP请求为
GET /attach HTTP/1.1
Host: 192.168.1.15
Accept: */*
Content-Type:  application/x-www-form-urlencoded
Cookie:  cookie=value1
User-Agent:  Wfuzz/2.2
Connection: close
```
```
#cookie测试
wfuzz -w wordlist/general/common.txt -b cookie=FUZZ http://192.168.1.15
```

6、HTTP标头
```
#添加自定义HTTP表标头
wfuzz -w wordlist/general/common.txt -H "myheader: headervalue" http://192.168.1.15/FUZZ

#得到的HTTP请求为
GET /agent HTTP/1.1
Host: 192.168.1.15
Accept: */*
Myheader:  headervalue
Content-Type:  application/x-www-form-urlencoded
User-Agent:  Wfuzz/2.2
Connection: close
```
```
#修改现有标头
wfuzz -w wordlist/general/common.txt -H "myheader: headervalue" -H "User-Agent: Googlebot-News" http://192.168.1.15/FUZZ

#得到的HTTP请求为
GET /asp HTTP/1.1
Host: 192.168.1.15
Accept: */*
Myheader:  headervalue
Content-Type:  application/x-www-form-urlencoded
User-Agent:  Googlebot-News
Connection: close
```

7、HTTP参数
```
#测试HTTP参数
wfuzz -z GET-HEAD-POST-TRACE-OPTIONS -X FUZZ http://192.168.1.10/
```
```
#指定HTTP参数
wfuzz -w wordlist/general/common.txt -X POST http://192.168.1.10/FUZZ
```

8、代理
```
#基本代理
wfuzz -w wordlist/general/common.txt -p localhost:8080 http://192.168.1.15/FUZZ
```
```
#SOCKS4 和 SOCKS5 协议的代理
wfuzz -w wordlist/general/common.txt -p localhost:2222:SOCKS5 http://192.168.1.15/FUZZ
```
```
#同时使用多个代理
wfuzz -w wordlist/general/common.txt -p localhost:8080 -p localhost:9090 http://192.168.1.15/FUZZ
```

9、爆破
```
#爆破密码
wfuzz -w wordlist/general/common.txt -w wordlist/general/common.txt --basic FUZZ:FUZ2Z https://192.168.1.15/login.php
```
```
#对受保护网站的资源进行模糊测试
wfuzz -w wordlist/general/common.txt  --basic admin:password https://192.168.1.15/FUZZ
```

10、深度目录扫描
```
wfuzz -w wordlist/general/common.txt -R1 http://192.168.1.15/FUZZ
```

11、输入结果
```
#以 JSON 格式输出
wfuzz -o json -w wordlist/general/common.txt http://192.168.1.15/FUZZ
```
