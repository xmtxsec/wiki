	**存储型xss**：存储型XSS又称持久型XSS，攻击脚本将被永久地存放在目标服务器的数据库或文件中，具有很高的隐蔽性。当其他用户访问留言板时，就会看到一一个弹窗。可以看到，存储型XSS的攻击方式能够将恶意代码永久地嵌入一个页面中，所有访问这个页面的用户都将成为受害者。如果我们能够谨慎对待不明链接，那么反射型XSS攻击将没有多大作为，而存储型XSS则不同，由于它注入在一些我们信任的页面，因此无论我们多么小心，都难免会受到攻击。


## 1、Low
**查看源码**
```php
<?php

if( isset( $_POST[ 'btnSign' ] ) ) {
    // Get input
    $message = trim( $_POST[ 'mtxMessage' ] );
    $name    = trim( $_POST[ 'txtName' ] );

    // Sanitize message input
    $message = stripslashes( $message );
    $message = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $message ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));

    // Sanitize name input
    $name = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $name ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));

    // Update database
    $query  = "INSERT INTO guestbook ( comment, name ) VALUES ( '$message', '$name' );";
    $result = mysqli_query($GLOBALS["___mysqli_ston"],  $query ) or die( '
<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );

    //mysql_close();
}

?>
```

**函数说明**
```
定义和用法
trim() 函数移除字符串两侧的空白字符或其他预定义字符。

语法
trim(string,charlist)
string	必需。规定要检查的字符串。
charlist	可选。规定从字符串中删除哪些字符。如果省略该参数，则移除下列所有字符：
	"\0" - NULL
	"\t" - 制表符
	"\n" - 换行
	"\x0B" - 垂直制表符
	"\r" - 回车
	" " - 空格
```

```
定义和用法
stripslashes() 函数删除由 addslashes() 函数添加的反斜杠。
提示：该函数可用于清理从数据库中或者从 HTML 表单中取回的数据。

语法
stripslashes(string)
string：必需。规定要检查的字符串

例：
<?php
echo stripslashes("Who\'s Peter Griffin?");
?>
运行结果：Who's Peter Griffin?
```

通过源码可以看到，对输入并没有做XSS方面的过滤与检查，且存储在数据库中，因此这里存在明显的存储型XSS漏洞。

**实战**<br />构造payload：
```
<script>alert("XMTX")</script>
```
![image-20210416153535664.png](./assets/1655880491309-6893ee5f-e06b-4a9e-a1c5-4648a4adf312.png)

![image-20210416153555650.png](./assets/1655880494758-925cffe9-5726-4c37-a687-b668953cf30d.png)


## 2、Medium
**查看源码**
```php
<?php

if( isset( $_POST[ 'btnSign' ] ) ) {
    // Get input
    $message = trim( $_POST[ 'mtxMessage' ] );
    $name    = trim( $_POST[ 'txtName' ] );

    // Sanitize message input
    $message = strip_tags( addslashes( $message ) );
    $message = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $message ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));
    $message = htmlspecialchars( $message );

    // Sanitize name input
    $name = str_replace( '<script>', '', $name );
    $name = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $name ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));

    // Update database
    $query  = "INSERT INTO guestbook ( comment, name ) VALUES ( '$message', '$name' );";
    $result = mysqli_query($GLOBALS["___mysqli_ston"],  $query ) or die( '
<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );

    //mysql_close();
}

?>
```

**函数说明**
```
定义和用法
strip_tags() 函数剥去字符串中的 HTML、XML 以及 PHP 的标签。

语法
strip_tags(string,allow)
string	必需。规定要检查的字符串。
allow	可选。规定允许的标签。这些标签不会被删除。
```

```
定义和用法
addslashes() 函数返回在预定义的字符前添加反斜杠的字符串。
预定义字符是：
	单引号（'）
	双引号（"）
	反斜杠（\）
	NULL

语法
addslashes(string)
string	必需。规定要转义的字符串。
```

通过代码可以看到对于message参数使用addslashes()函数添加一些反斜杠，然后用strip_tags() 函数剥去字符串中的 HTML、XML 以及 PHP 的标签。最后还使用htmlspecialchars()函数将预定义字符转换成html实体，不存在XSS。对于name参数使用str_replace()函数过滤了`<script>`不过我们可以使用大小写来绕过。

**实战**<br />在前端对name参数输入字符长度进行了限制，将其修改<br />![image-20210416160229069.png](./assets/1655880508064-ee3bd53f-dfa6-4023-a668-b1172c1b2f6c.png)

构造payload：
```
<scrIpT>alert("XMTX")</ScRipt>
```
![image-20210416160323660-1618564108537.png](./assets/1655880512817-61a46f57-ee06-46a3-9e8c-ba76ccd5f860.png)

![image-20210416160343291.png](./assets/1655880516137-5ba75ef9-352c-4bac-a43f-b41909bed70a.png)


## 3、High
**查看源码**
```php
<?phpif( isset( $_POST[ 'btnSign' ] ) ) {    // Get input    $message = trim( $_POST[ 'mtxMessage' ] );    $name    = trim( $_POST[ 'txtName' ] );    // Sanitize message input    $message = strip_tags( addslashes( $message ) );    $message = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $message ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));    $message = htmlspecialchars( $message );    // Sanitize name input    $name = preg_replace( '/<(.*)s(.*)c(.*)r(.*)i(.*)p(.*)t/i', '', $name );    $name = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $name ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));    // Update database    $query  = "INSERT INTO guestbook ( comment, name ) VALUES ( '$message', '$name' );";    $result = mysqli_query($GLOBALS["___mysqli_ston"],  $query ) or die( '
<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );    //mysql_close();}?>
```

通过查看源码可以看到在Medium的基础上对name参数使用了preg_replace()函数对`<script>`j进行过滤，我们可以使用其他参数。

**实战**<br />在前端对name参数输入字符长度进行了限制，将其修改<br />![image-20210416160229069.png](./assets/1655880524884-cffedce3-a4e9-4fb1-a137-eb63da58f3f7.png)

构造payload：
```
<img src=1 onerror=alert("XMTX")>
```
![image-20210416160814852.png](./assets/1655880529353-4087b111-6561-49ee-96c0-1de68c2e2743.png)
![image-20210416160823006.png](./assets/1655880533258-19f43b55-46f4-4c7c-936e-780879f797d4.png)


## 4、Impossible
**查看源码**
```php
<?phpif( isset( $_POST[ 'btnSign' ] ) ) {    // Check Anti-CSRF token    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );    // Get input    $message = trim( $_POST[ 'mtxMessage' ] );    $name    = trim( $_POST[ 'txtName' ] );    // Sanitize message input    $message = stripslashes( $message );    $message = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $message ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));    $message = htmlspecialchars( $message );    // Sanitize name input    $name = stripslashes( $name );    $name = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $name ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));    $name = htmlspecialchars( $name );    // Update database    $data = $db->prepare( 'INSERT INTO guestbook ( comment, name ) VALUES ( :message, :name );' );    $data->bindParam( ':message', $message, PDO::PARAM_STR );    $data->bindParam( ':name', $name, PDO::PARAM_STR );    $data->execute();}// Generate Anti-CSRF tokengenerateSessionToken();?>
```

通过代码可以看到对于message参数和name参数都使用stripslashes() 函数删除由 addslashes() 函数添加的反斜杠。然后用htmlspecialchars()函数将预定义字符转换成html实体，不存在XSS。
