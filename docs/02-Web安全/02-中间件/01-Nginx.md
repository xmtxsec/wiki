
# Nginx 处理HTTP请求的 11 个过程
Nginx 处理请求过程总共划分为 11 个阶段，按顺序依次是 post-read、server-rewrite、find-config、rewrite、post-rewrite、preaccess、access、post-access、precontent、content、log。

![](../../_img\02-Web安全\02-中间件/1668580238949-f722eadd-7ae2-4f14-a8d7-205082467baa.jpeg)


## 3.1、post-read阶段
在 Nginx 读取并解析完请求头之后,在没有对头部做任何处理之前就立即开始运行post-read 阶段，通过post-read 阶段可以拿到一些原始信息。

HTTP 协议中，有两个头部可以用来获取用户 IP：

- X-Forwardex-For 是用来传递 IP 的，这个头部会把经过的节点 IP 都记录下来
- X-Real-IP：可以记录用户真实的 IP 地址，只能有一个


### 3.1.1、realip模块
realip模块默认不会编译进 Nginx，需要通过 `--with-http_realip_module` 启用功能。如果还想要使用原来的 TCP 连接中的地址和端口，需要通过`realip_remote_addr`、`realip_remote_port`这两个变量保存。realip模块可以修改客户端地址。

**set_real_ip_from**<br />指定可信的地址，只有从该地址建立的连接，获取的 realip 才是可信的
```
Syntax: set_real_ip_from address | CIDR | unix:;
Default: —
Context: http, server, location
```

**real_ip_header**<br />指定从哪个头部取真实的 IP 地址，默认从 X-Real-IP 中取，如果设置从 X-Forwarded-For 中取，会先从最后一个 IP 开始取
```
Syntax: real_ip_header field | X-Real-IP | X-Forwarded-For | proxy_protocol;
Default: real_ip_header X-Real-IP; 
Context: http, server, location
```

**real_ip_recursive**<br />环回地址，默认关闭，打开的时候，如果 X-Forwarded-For 最后一个地址与客户端地址相同，会过滤掉该地址
```
Syntax: real_ip_recursive on | off;
Default: real_ip_recursive off; 
Context: http, server, location
```

**实例**<br />假设有一个编译了带有realip模块的nginx，配置如下：
```
# 在 example 目录下建立 realip.conf，set_real_ip_from 可以设置为自己的本机 IP
server {
    listen 80;
    server_name xmtx.realip.com;
    error_log /Users/mtdp/myproject/nginx/nginx/logs/myerror.log debug;
    set_real_ip_from 192.168.0.108;
    real_ip_recursive off;
    real_ip_header X-Forwarded-For;

    location / {
        return 200 "Client real ip: $remote_addr\n";
    }
}
```
在上面的配置文件中设置了可信地址为：192.168.0.108，`real_ip_recursive`默认为off，`real_ip_header`从X-Forwarded-For中获取。

测试结果：
```
curl -H 'X-Forwarded-For: 1.1.1.1,192.168.0.108' xmtx.realip.com
Client real ip: 192.168.0.108
```

在配置文件中打开`real_ip_recursive`
```
server {
    listen 80;
    server_name ziyang.realip.com;
    error_log /Users/mtdp/myproject/nginx/nginx/logs/myerror.log debug;
    set_real_ip_from 192.168.0.108;
    real_ip_recursive on;
    real_ip_header X-Forwarded-For;

    location / {
        return 200 "Client real ip: $remote_addr\n";
    }
}
```

测试结果
```
curl -H 'X-Forwarded-For: 1.1.1.1,2.2.2.2,192.168.0.108' xmtx.realip.com
Client real ip: 2.2.2.2
```


## 3.2、server-rewrite
service-rewrite 阶段是nginx中第一个必须经历的重要阶段，请求进入此阶段时已经找到对应的虚拟主机（server）配置。nginx的rewrite模块在这个阶段注册了一个handler，rewrite模块提供url重写指令rewrite，变量设置指令set，以及逻辑控制指令if、break和return，用户可以在server配置里面，组合这些指令来满足自己的需求，而不需要另外写一个模块，比如将一些前缀满足特定模式的uri重定向到一个固定的url，还可以根据请求的属性来决定是否需要重写或者给用户发送特定的返回码。rewrite提供的逻辑控制指令能够满足一些简单的需求，针对一些较复杂的逻辑可能需要注册handler通过独立实现模块的方式来满足。<br />在service-rewrite 阶段中，请求还未被匹配到一个具体的location中。该阶段执行的结果（比如改写后的uri）会影响后面FIND_CONFIG阶段的执行。另外这个阶段也是内部子请求执行的第一个阶段。


## 3.3、find-config
经过 rewrite 模块，匹配到 URL 之后，就会进入 find_config 阶段，开始寻找 URL 对应的 location 配置。

**location语法**
```
Syntax: location [ = | ~ | ~* | ^~ ] uri { ... }
        location @name { ... }
Default: —
Context: server, location

Syntax: merge_slashes on | off;
Default: merge_slashes on; 
Context: http, server
```

`merge_slashes` 的作用是，加入 URL 中有两个重复的 /，那么会合并为一个，这个指令默认是打开的，只有当对 URL 进行 base64 之类的编码时才需要关闭。

**匹配规则**<br />location 的匹配规则是仅匹配 URI，忽略参数，有下面三种大的情况：

1. 前缀字符串
- 常规匹配
- =：精确匹配
- ^~：匹配上后则不再进行正则表达式匹配
2. 正则表达式
- ~：大小写敏感的正则匹配
- ~*：大小写不敏感
3. 用户内部跳转的命名 location
- @

匹配优先级：

1. 先遍历所有的前缀字符串，选取最长的一个前缀字符串，如果这个字符串是 = 的精确匹配或 ^~ 的前缀匹配，会直接使用
2. 如果第一步中没有匹配上 = 或 ^~，那么会先记住最长匹配的前缀字符串 location
3. 按照 nginx.conf 文件中的配置依次匹配正则表达式
4. 如果所有的正则表达式都没有匹配上，那么会使用最长匹配的前缀字符串


**实例**<br />nginx配置文件
```
server {
    listen 80;
    server_name location.xmtx.com;
    error_log  logs/error.log  debug;
    default_type text/plain;
    merge_slashes off;
    
    location ~ /Test1/$ {
    	return 200 'first regular expressions match!\n';
    }
    location ~* /Test1/(\w+)$ {
    	return 200 'longest regular expressions match!\n';
    }
    location ^~ /Test1/ {
    	return 200 'stop regular expressions match!\n';
    }
    location /Test1/Test2 {
        return 200 'longest prefix string match!\n';
    }
    location /Test1 {
        return 200 'prefix string match!\n';
    }
    location = /Test1 {
    	return 200 'exact match!\n';
    }
}
```

当访问下面的几个URL时的匹配结果
```
/Test1 匹配 location = /Test1
/Test1/ 匹配 location ^~ /Test1/
/Test1/Test2 匹配 location ~* /Test1/(\w+)$
/Test1/Test2/ 匹配 location /Test1/Test2
/Test1/Test3 匹配 location ^~ /Test1/
```


## 3.4、rewrite
在前面的service-rewrite 阶段已经说过rewrite模块提供url重写指令rewrite，变量设置指令set，以及逻辑控制指令if、break和return，用户可以在配置里面，组合这些指令来满足自己的需求，而不需要另外写一个模块，比如将一些前缀满足特定模式的uri重定向到一个固定的url，还可以根据请求的属性来决定是否需要重写或者给用户发送特定的返回码。

**return指令**
```
Syntax: return code [text];
        return code URL;
        return URL;
Default: —
Context: server, location, if
```

返回状态码包括以下几种：

1. Nginx 自定义
- 444：立刻关闭连接，用户收不到响应
2. HTTP 1.0 标准
- 301：永久重定向
- 302：临时重定向，禁止被缓存
3. HTTP 1.1 标准
- 303：临时重定向，允许改变方法，禁止被缓存
- 307：临时重定向，不允许改变方法，禁止被缓存
- 308：永久重定向，不允许改变方法

**error_page**<br />当访问一个网站出现 404 的时候，一般不会直接出现一个 404 NOT FOUND，而是会有一个比较友好的页面，这就是 error_page 的功能。
```
Syntax: error_page code ... [=[response]] uri;
Default: —
Context: http, server, location, if in location
```

**实例**
```
server {
    server_name xmtx.return.com;
    listen 80;
    root html/;
    error_page 404 /403.html;
    #return 405;
    location / {
        #return 404 "find nothing!";
    }
}
```

当访问一个不存在的页面，此时`error_page` 生效返回403
```
curl  xmtx.return.com/text
<html>
<head><title>403 Forbidden</title></head>
<body>
<center><h1>403 Forbidden</h1></center>
<hr><center>nginx/1.17.8</center>
</body>
</html>
```

修改配置文件
```
server {
    server_name xmtx.return.com;
    listen 80;
    root html/;
    error_page 404 /403.html;
    #return 405;
    location / {
        return 404 "find nothing!";
    }
}
```

再次访问，return 指令得到了执行。
```
curl  xmtx.return.com/text
find nothing!%    
```

修改位置文件
```
server {
    server_name xmtx.return.com;
    listen 80;
    root html/;
    error_page 404 /403.html;
    return 405;
    location / {
        return 404 "find nothing!";
    }
}
```

再次访问，return 指令得到了执行。
```
curl  xmtx.return.com/text
<html>
<head><title>405 Not Allowed</title></head>
<body>
<center><h1>405 Not Allowed</h1></center>
<hr><center>nginx/1.17.8</center>
</body>
</html>
```

**结论**

1. 当 server 下包含 error_page 且 location 下有 return 指令的时候，会执行 location 下的 return 指令。
2. return 指令同时出现在 server 块下和同时出现在 location 块下，先遇到哪个 return 指令就先执行哪一个。

**rewrite指令**<br />rewrite 指令用于修改用户传入 Nginx 的 URL。
```
Syntax: rewrite regex replacement [flag];
Default: —
Context: server, location, if
```
功能：

1. 将 regex 指定的 URL 替换成 replacement 这个新的 URL
- 可以使用正则表达式及变量提取
- 当 replacement 以 http:// 或者 https:// 或者 $schema 开头，则直接返回 302 重定向
2. 替换后的 URL 根据 flag 指定的方式进行处理
- last：用 replacement 这个 URL 进行新的 location 匹配
- break：break 指令停止当前脚本指令的执行，等价于独立的 break 指令
- redirect：返回 302 重定向
- permanent：返回 301 重定向

**实战**<br />假设有目录结构如下
```
html/first/
└── 1.txt
html/second/
└── 2.txt
html/third/
└── 3.txt
```

nginx配置文件如下
```
server {
    listen 80;
    server_name rewrite.xmtx.com;
    rewrite_log on;
    error_log logs/rewrite_error.log notice;

    root html/;
    location /first {
    	rewrite /first(.*) /second$1 last;
    	return 200 'first!\n';
    }

    location /second {
    	rewrite /second(.*) /third$1;
    	return 200 'second!\n';
    }

    location /third {
    	return 200 'third!\n';
    }
} 
```

访问rewrite.xmtx.com/first/3.txt，结果如下：
```
curl rewrite.xmtx.com/first/3.txt
second!
```
匹配过程

1. 由于 rewrite /first(.*) /second$1 last; 这条指令的存在，last 表示使用新的 URL 进行 location 匹配，因此接下来会去匹配 second/3.txt
2. 匹配到 /second 块之后，会依次执行指令，最后返回 200

注意，location 块中虽然也改写了 URL，但是并不会去继续匹配，因为后面没有指定 flag。

修改配置文件，将 rewrite /second(.*) /third$1; 这条指令加上 break flag
```
server {
    listen 80;
    server_name rewrite.xmtx.com;
    rewrite_log on;
    error_log logs/rewrite_error.log notice;

    root html/;
    location /first {
    	rewrite /first(.*) /second$1 last;
    	return 200 'first!\n';
    }

    location /second {
    	rewrite /second(.*) /third$1 break flag;
    	return 200 'second!\n';
    }

    location /third {
    	return 200 'third!\n';
    }
} 
```

继续访问rewrite.xmtx.com/first/3.txt，结果如下：
```
curl rewrite.xmtx.com/first/3.txt
test3%
```
匹配过程：

1. 由于 rewrite /first(.*) /second$1 last; 这条指令的存在，last 表示使用新的 URL 进行 location 匹配，因此接下来会去匹配 second/3.txt
2. 匹配到 /second 块之后，由于 break flag 的存在，会继续匹配 rewrite 过后的 URL
3. 匹配 /third location

**rewrite行为记录日志**<br />主要是一个指令 rewrite_log：
```
Syntax: rewrite_log on | off;
Default: rewrite_log off; 
Context: http, server, location, if
```
这个指令打开之后，会把 rewrite 的日志写入 logs/rewrite_error.log 日志文件中。

**if指令**
```
Syntax: if (condition) { ... }
Default: —
Context: server, location
```
当条件 condition 为真，则执行大括号内的指令。

规则如下：

1. 检查变量为空或者值是否为 0
2. 将变量与字符串做匹配，使用 = 或 !=
3. 将变量与正则表达式做匹配
- 大小写敏感，~ 或者 !~
- 大小写不敏感，* 或者 !*
4. 检查文件是否存在，使用 -f 或者 !-f
5. 检查目录是否存在，使用 -d 或者 !-d
6. 检查文件、目录、软链接是否存在，使用 -e 或者 !-e
7. 检查是否为可执行文件，使用 -x 或者 !-x

例
```
if ($http_user_agent ~ MSIE) { # 与变量 http_user_agent 匹配
    rewrite ^(.*)$ /msie/$1 break; 
} 
if ($http_cookie ~* "id=([^;]+)(?:;|$)") { # 与变量 http_cookie 匹配
    set $id $1; 
} 
if ($request_method = POST) { # 与变量 request_method 匹配，获取请求方法
    return 405; 
} 
if ($slow) { # slow 变量在 map 模块中自定义，也可以进行匹配
    limit_rate 10k; 
} 
if ($invalid_referer) { 
    return 403; 
}
```


## 3.5、post-rewrite
该阶段仅仅只是检查上一阶段是否做了URI重写，如果没有重写的话，直接进入下一阶段；如果有重写的话，则利用next跳转域往前跳转到find_config阶段重新执行。Nginx对URI重写次数做了限制，默认是10次。


## 3.6、preaccess
preaccess 阶段处理完成的是限制访问频率、限制每个客户端的并发连接数。

**limit_conn模块**<br />这里面涉及到的模块是`ngx_http_limit_conn_module`它的基本特性如下：

1. 生效阶段：NGX_HTTP_PREACCESS_PHASE 阶段
2. 模块：http_limit_conn_module
3. 默认编译进 Nginx，通过 --without-http_limit_conn_module 禁用
4. 生效范围
- 全部 worker 进程（基于共享内存）
- 进入 preaccess 阶段前不生效
- 限制的有效性取决于 key 的设计：依赖 postread 阶段的 realip 模块取到真实 IP

注意： key 指的就是对哪个变量进行限制，通常我们取的都是用户的真实 IP。

**语法**<br />定义共享内存（包括大小），以及 key 关键字
```
Syntax: limit_conn_zone key zone=name:size;
Default: —
Context: http
```

限制并发连接数
```
Syntax: limit_conn zone number;
Default: —
Context: http, server, location
```

限制发生时的日志级别
```
Syntax: limit_conn_log_level info | notice | warn | error;
Default: limit_conn_log_level error; 
Context: http, server, location
```

限制发生时向客户端返回的错误码
```
Syntax: limit_conn_status code;
Default: limit_conn_status 503; 
Context: http, server, location
```

**实战**<br /> nginx配置文件如下
```
limit_conn_zone $binary_remote_addr zone=addr:10m;

server {
    listen 80;
    server_name limit.ziyang.com;
    root html/;
    error_log logs/myerror.log info;
    location /{
    	limit_conn_status 500;
    	limit_conn_log_level  warn;
    	limit_rate 50;
    	limit_conn addr 1;
    }
}
```
配置文件中，做了两条限制，一个是 limit_rate 限制为 50 个字节，并发连接数 limit_conn 限制为 1。

**结果**<br />访问站点的时候会发现速度非常慢，因为每秒钟只有 50 个字节。<br />如果再同时访问这个站点的话，则会返回 500。

limit_req模块<br />这里面生效的模块是 `ngx_http_limit_req_module`，它的基本特性如下：

1. 生效阶段：NGX_HTTP_PREACCESS_PHASE 阶段
2. 模块：http_limit_req_module
3. 默认编译进 Nginx，通过 --without-http_limit_req_module 禁用
4. 生效算法：leaky bucket 算法（漏桶算法）
5. 生效范围
   - 全部 worker 进程（基于共享内存）
   - 进入 preaccess 阶段前不生效

**语法**<br />定义共享内存（包括大小），以及 key 关键字和限制速率
```
Syntax: limit_req_zone key zone=name:size rate=rate ;
rate 单位为 r/s 或者 r/m（每分钟或者每秒处理多少个请求）
Default: —
Context: http
```


限制并发连接数
```
Syntax: limit_req zone=name [burst=number] [nodelay];
burst 默认为 0
nodelay，如果设置了这个参数，那么对于漏桶中的请求也会立刻返回错误
Default: —
Context: http, server, location
```

限制发生时的日志级别
```
Syntax: limit_req_log_level info | notice | warn | error;
Default: limit_req_log_level error; 
Context: http, server, location
```

限制发生时向客户端返回的错误码
```
Syntax: limit_req_status code;
Default: limit_req_status 503; 
Context: http, server, location

```

**实战**<br />nginx配置文件如下
```
limit_conn_zone $binary_remote_addr zone=addr:10m;
limit_req_zone $binary_remote_addr zone=one:10m rate=2r/m;

server {
    listen 80;
    server_name limit.ziyang.com;
    root html/;
    error_log logs/myerror.log info;
    
    location /{
    	limit_conn_status 500;
    	limit_conn_log_level  warn;
        #limit_rate 50;
        #limit_conn addr 1;
        #limit_req zone=one burst=3 nodelay;
    	limit_req zone=one;
    }
}
```

**运行结果**<br />在 limit_req zone=one 指令下，超出每分钟处理的请求数后就会立刻返回 503。

**修改nginx的配置文件如下**
```
limit_conn_zone $binary_remote_addr zone=addr:10m;
limit_req_zone $binary_remote_addr zone=one:10m rate=2r/m;

server {
    listen 80;
    server_name limit.ziyang.com;
    root html/;
    error_log logs/myerror.log info;
    
    location /{
    	limit_conn_status 500;
    	limit_conn_log_level  warn;
        #limit_rate 50;
        #limit_conn addr 1;
      limit_req zone=one burst=3 nodelay;
      	#limit_req zone=one;
    }
}
```
在没有添加 burst 参数时，会立刻返回错误，而加上之后，不会返回错误，而是等待请求限制解除，直到可以处理请求时再返回。

 nodelay 参数：<br />添加了 nodelay 之后，请求在没有达到 burst 限制之前都可以立刻被处理并返回，超出了 burst 限制之后，才会返回 503。

**结论**

1. limit_req 在 limit_conn 处理之前，因此是 limit_req 会生效
2. 不添加 nodelay，请求会等待，直到能够处理请求；添加 nodelay，在不超出 burst 的限制的情况下会立刻处理并返回，超出限制则会返回 503。


## 3.7、access
经过 preaccess 阶段对用户的限流之后，就到了 access 阶段。

**access模块**<br />这里面涉及到的模块是 ngx_http_access_module，它的基本特性如下：

1. 生效阶段：NGX_HTTP_ACCESS_PHASE 阶段
2. 模块：http_access_module
3. 默认编译进 Nginx，通过 --without-http_access_module 禁用
- 生效范围
- 进入 access 阶段前不生效

**语法**<br />access 模块提供了两条指令 allow 和 deny
```
Syntax: allow address | CIDR | unix: | all;
Default: —
Context: http, server, location, limit_except

Syntax: deny address | CIDR | unix: | all;
Default: —
Context: http, server, location, limit_except
```

**例**<br />对于用户访问来说，这些指令是顺序执行的，当满足了一条之后，就不会再向下执行。
```
location / { 
    deny 192.168.1.1; 
    allow 192.168.1.0/24; 
    allow 10.1.1.0/16; 
    allow 2001:0db8::/32; 
    deny all; 
}
```

**auth_basic模块**<br />auth_basic 模块是用作用户认证的，当开启了这个模块之后，我们通过浏览器访问网站时，就会返回一个 401 Unauthorized，当然这个 401 用户不会看见，浏览器会弹出一个对话框要求输入用户名和密码。这个模块使用的是 RFC2617 中的定义。

**语法**

1. 基于 HTTP Basic Authutication 协议进行用户密码的认证
2. 默认编译进 Nginx
- –without-http_auth_basic_module
- disable ngx_http_auth_basic_module
```
Syntax: auth_basic string | off;
Default: auth_basic off; 
Context: http, server, location, limit_except

Syntax: auth_basic_user_file file;
Default: —
Context: http, server, location, limit_except
```

在这里会用到工具htpasswd，这个工具可以用来生成密码文件，而auth_basic_user_file就依赖这个密码文件

生成密码命令
```
htpasswd –c file –b user pass
```

**实战**<br />在 example 目录下生成密码文件 auth.pass
```
htpasswd -bc auth.pass ziyang 123456
```

nginx配置文件如下
```
server {
    server_name access.ziyang.com;
    listen 80;
    error_log  logs/error.log  debug;
    default_type text/plain;
    location /auth_basic {
    	satisfy any;
    	auth_basic "test auth_basic";
    	auth_basic_user_file example/auth.pass;
    	deny all;
    }
}
```

这时候访问站点就会弹出对话框，提示输入密码

**suth_request模块**

1. 功能：向上游的服务转发请求，若上游服务返回的响应码是 2xx，则继续执行，若上游服务返回的响应码是 2xx，则继续执行，若上游服务返回的是 401 或者 403，则将响应返回给客户端
2. 原理：收到请求后，生成子请求，通过反向代理技术把请求传递给上游服务
3. 默认未编译进 Nginx，需要通过 –with-http_auth_request_module 编译进去

**语法**
```
Syntax: auth_request uri | off;
Default: auth_request off; 
Context: http, server, location

Syntax: auth_request_set $variable value;
Default: —
Context: http, server, location
```

**实战**<br />nginx配置文件如下
```
server {
    server_name access.ziyang.com;
    listen 80;
    error_log  logs/error.log  debug;
    #root html/;
    default_type text/plain;
    location /auth_basic {
    	satisfy any;
    	auth_basic "test auth_basic";
    	auth_basic_user_file example/auth.pass;
    	deny all;
    }
    location / {
    	auth_request /test_auth;
    }
    location = /test_auth {
    	proxy_pass http://127.0.0.1:8090/auth_upstream;
    	proxy_pass_request_body off;
    	proxy_set_header Content-Length "";
    	proxy_set_header X-Original-URI $request_uri;
    }
}
```
这个配置文件中，/ 路径下会将请求转发到另外一个服务中去，可以用 nginx 再搭建一个服务<br />如果这个服务返回 2xx，那么鉴权成功，如果返回 401 或 403 则鉴权失败

**satisfy 指令**<br />限制所有 access 阶段模块
```
Syntax: satisfy all | any;
Default: satisfy all; 
Context: http, server, location
```
satisfy 指令有两个值一个是 all，一个是 any，这个模块对 acces 阶段的三个模块都生效：

1. access 模块
2. auth_basic 模块
3. auth_request 模块
4. 其他模块

如果 satisfy 指令的值是 all 的话，就表示必须所有 access 阶段的模块都要执行，都通过了才会放行；值是 any 的话，表示有任意一个模块得到执行即可。


## 3.8、post-access



## 3.9、precontent
**try_files模块**
```
Syntax: try_files file ... uri;
        try_files file ... =code;
Default: —
Context: server, location
```
依次试图访问多个 URL 对应的文件（由 root 或者 alias 指令指定），当文件存在时，直接返回文件内容，如果所有文件都不存在，则按照最后一个 URL 结果或者 code 返回

实战
```
server {
    server_name tryfiles.ziyang.com;
    listen 80;
    error_log  logs/myerror.log  info;
    root html/;
    default_type text/plain;
    location /first {
    	try_files /system/maintenance.html
            $uri $uri/index.html $uri.html
            @lasturl;
    }
    location @lasturl {
    	return 200 'lasturl!\n';
    }
    location /second {
    	try_files $uri $uri/index.html $uri.html =404;
    }
}
```

结果<br />访问 /first 实际上到了 lasturl，然后返回 200<br />访问 /second 则返回了 404

**mirror模块**<br />可以实时拷贝流量，这对于需要同时访问多个环境的请求是非常有用的。

- ngx_http_mirror_module 模块，默认编译进 Nginx
   - 通过 –without-http_mirror_module 移除模块
- 功能：处理请求时，生成子请求访问其他服务，对子请求的返回值不做处理
```
Syntax: mirror uri | off;
Default: mirror off; 
Context: http, server, location

Syntax: mirror_request_body on | off;
Default: mirror_request_body on; 
Context: http, server, location
```

实战<br />nginx配置文件如下，需要再开启另外一个 Nginx 来接收请求
```
server {
    server_name mirror.ziyang.com;
    listen 8001;
    error_log logs/error_log debug;
    location / {
        mirror /mirror;
        mirror_request_body off;
    }
    location = /mirror {
        internal;
        proxy_pass http://127.0.0.1:10020$request_uri;
        proxy_pass_request_body off;
        proxy_set_header Content-Length "";
        proxy_set_header X-Original-URI $request_uri;
    }
}
```
在 access.log 文件中可以看到有请求记录日志


## 




## 3.10、content阶段
content模块<br />content模块主要用来提升小文件的性能，合并多个小文件请求，可以明显提升 HTTP 请求的性能。<br />指令
```
#在 URI 后面加上 ??，通过 ”,“ 分割文件，如果还有参数，则在最后通过 ? 添加参数
concat on | off
default concat off
Context http, server, location

concat_types MIME types
Default concat_types: text/css application/x-javascript
Context http, server, location

concat_unique on | off
Default concat_unique on
Context http, server, location

concat_max_files numberp
Default concat_max_files 10
Context http, server, location

concat_delimiter string
Default NONE
Context http, server, locatione
concat_ignore_file_error on | off
Default off
Context http, server, location
```

index模块<br />index模块用于在指定 / 结尾的目录访问时，返回 index 文件内容，当我们访问以 / 结尾的目录时，会去找 root 或 alias 指令的文件夹下的 index.html，如果有这个文件，就会把文件内容返回，也可以指定其他文件。先于 autoindex 模块执行

语法
```
Syntax: index file ...;
Default: index index.html; 
Context: http, server, location
```

audit_index模块<br />audit_index模块默认编译进 Nginx，使用 --without-http_autoindex_module 取消<br />当 URL 以 / 结尾时，尝试以 html/xml/json/jsonp 等格式返回 root/alias 中指向目录的目录结构

语法
```
# 开启或关闭
Syntax: autoindex on | off;
Default: autoindex off; 
Context: http, server, location
# 当以 HTML 格式输出时，控制是否转换为 KB/MB/GB
Syntax: autoindex_exact_size on | off;
Default: autoindex_exact_size on; 
Context: http, server, location
# 控制以哪种格式输出
Syntax: autoindex_format html | xml | json | jsonp;
Default: autoindex_format html; 
Context: http, server, location
# 控制是否以本地时间格式显示还是 UTC 格式
Syntax: autoindex_localtime on | off;
Default: autoindex_localtime off; 
Context: http, server, location
```

实战<br />nginx的配置文件如下
```
server {
    server_name autoindex.ziyang.com;
    listen 8080;
    location / {
        alias html/;
        autoindex on;
        #index b.html;
        autoindex_exact_size on;
        autoindex_format html;
        autoindex_localtime on;
    }
}
```
这里我把 index b.html 这条指令给注释掉了，而 index 模块是默认编译进 Nginx 的，且默认指令是 index index.html，因此，会去找是否有 index.html 这个文件。

- 打开浏览器，访问 autoindex.ziyang.com:8080，html 目录下默认是有 index.html 文件的，因此显示结果为：

打开 index b.html 指令注释。由于 html 文件夹下并不存在 b.html 这个文件，所以请求会走到 autoindex 模块，显示目录：


**static 模块**<br />root 和 alias 指令，这两个指令都是用来映射文件路径的。

- 功能：将 URL 映射为文件路径，以返回静态文件内容
- 差别：root 会将完整 URL 映射进文件路径中，alias 只会将 location 后的 URL 映射到文件路径
```
Syntax: alias path;
Default: —
Context: location
```

```
Syntax: root path;
Default: root html; 
Context: http, server, location, if in location
```

**实战**<br />现在有一个文件路径：
```
html/first/
└── 1.txt
```

nginx配置文件如下
```
server {
    server_name static.ziyang.com;
    listen 80;
    error_log  logs/myerror.log  info;
    location /root {
    	root html;
    }
    location /alias {
        alias html;
    }
    location ~ /root/(\w+\.txt) {
    	root html/first/$1;
    }
    location ~ /alias/(\w+\.txt) {
    	alias html/first/$1;
    }
    location  /RealPath/ {
    	alias html/realpath/;
        return 200 '$request_filename:$document_root:$realpath_root\n';
    }
}
```

访问这四个路径分别得到的结果是：

- /root：404
- /alias：200
- /root/1.txt：404
- /alias/1.txt：200

root 在映射 URL 时，会把 location 中的路径也加进去，也就是：

- static.ziyang.com/root/ 实际访问的是 html/root/
- static.ziyang.com/root/1.txt 实际是 html/first/1.txt/root/1.txt
- static.ziyang.com/alias/ 实际上是正确访问到了 html 文件夹，由于后面有 / 的存在，因此实际访问的是 html/index.html
- static.ziyang.com/alias/1.txt 实际访问的是 html/first/1.txt，文件存在












## 3.11、log阶段
log阶段的作用是将HTTP请求的相关信息记录到日志

access日志格式
```
Syntax: log_format name [escape=default|json|none] string ...;
Default: log_format combined "..."; 
Context: http
```

默认的combined日志格式
```
log_format combined '$remote_addr - $remote_user [$time_local] ' 
'"$request" $status $body_bytes_sent ' '"$http_referer" 
"$http_user_agent"';
```

配置日志文件路径
```
Syntax: access_log path [format [buffer=size] [gzip[=level]] [flush=time] [if=condition]];
        access_log off;
Default: access_log logs/access.log combined; 
Context: http, server, location, if in location, limit_except
```

- path 路径可以包含变量：不打开 cache 时每记录一条日志都需要打开、关闭日志文件
- if 通过变量值控制请求日志是否记录
- 日志缓存
   - 功能：批量将内存中的日志写入磁盘
   - 写入磁盘的条件：所有待写入磁盘的日志大小超出缓存大小；达到 flush 指定的过期时间；worker 进程执行 reopen 命令，或者正在关闭。
- 日志压缩
   - 功能：批量压缩内存中的日志，再写入磁盘
   - buffer 大小默认为 64KB
   - 压缩级别默认为 1（1最快压缩率最低，9最慢压缩率最高）
   - 打开日志压缩时，默认打开日志缓存功能

对日志文件包含变量时的优化
```
Syntax: open_log_file_cache max=N [inactive=time] [min_uses=N] [valid=time];
        open_log_file_cache off;
Default: open_log_file_cache off; 
Context: http, server, location
```

- max：缓存内的最大文件句柄数，超出后用 LRU 算法淘汰
- inactive：文件访问完后在这段时间内不会被关闭。默认 10 秒
- min_uses：在 inactive 时间内使用次数超过 min_uses 才会继续存在内存中。默认 1
- valid：超出 valid 时间后，将对缓存的日志文件检查是否存在。默认 60 秒
- off：关闭缓存功能
