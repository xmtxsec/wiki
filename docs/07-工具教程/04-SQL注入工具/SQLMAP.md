[SqlmapWiki翻译.pdf](https://www.yuque.com/attachments/yuque/0/2022/pdf/21411821/1664160431530-03299d19-5245-4821-b8be-fbb7d1993344.pdf)
SQLMAP是一个开放源代码渗透测试工具，它可以自动检测和利用SQL注入漏洞并接管数据库服务器的过程。它具有强大的检测引擎，针对最终渗透测试仪的众多细分功能以及从数据库指纹识别，从数据库获取数据到访问基础文件系统以及通过外出在操作系统上执行命令的多种开关。

`http://192.168.136.131/SQLMAP/mysql/get_int.php?id=1`<br />当给SQLMAP这么一个URL的时候，它会：

1. 判断可注入的参数
2. 判断可以用那种SQL注入技术来注入
3. 识别出哪种数据库
4. 根据用户选择，读取哪些数据


# 基础

## SQL注入的五种模式
SQLMAP支持五种不同的注入模式：

1. 基于布尔的盲注，即可以根据返回页面判断条件真假的注入。
2. 基于时间的盲注，即不能根据页面返回内容判断任何信息，用条件语句查看时间延迟语句是否执行（即页面返回时间是否增加）来判断。
3. 基于报错注入，即页面会返回错误信息，或者把注入的语句的结果直接返回在页面中。
4. 联合查询注入，可以使用union的情况下的注入。
5. 堆查询注入，可以同时执行多条语句的执行时的注入。


## SQLMAP支持的数据库：
`MySQL, Oracle, PostgreSQL, Microsoft SQL Server, Microsoft Access, IBM DB2, SQLite, Firebird, Sybase和SAP MaxD`

# 获取目标的方式

## 目标URL
参数：`-u`或者`--url`<br />格式`：http(s)://targeturl[:port]/[…]`<br />例如：`python SQLMAP.py -u "http://www.target.com/vuln.php?id=1" -f --banner --dbs --users`


## 从Burp或者WebScarab代理中获取日志
参数：`-l`<br />可以直接吧Burp proxy或者WebScarab proxy中的日志直接倒出来交给SQLMAP来一个一个检测是否有注入。


## 从文本中获取多个目标扫描
参数：`-m`<br />文件中保存url格式如下，SQLMAP会一个一个检测
```
www.target1.com/vuln1.php?q=foobar
www.target2.com/vuln2.asp?id=1
www.target3.com/vuln3/id/1
```


## 从文件中加载HTTP请求
参数：`-r`<br />SQLMAP可以从一个文本文件中获取HTTP请求，这样就可以跳过设置一些其他参数（比如cookie，POST数据，等等）。<br />比如文本文件内如下：
```
POST /vuln.php HTTP/1.1
Host: www.target.com
User-Agent: Mozilla/4.0
id=1
```

当请求是HTTPS的时候需要配合这个`--force-ssl`参数来使用，或者可以在Host头后门加上:443


## 处理Google的搜索结果
参数：`-g`<br />SQLMAP可以测试注入Google的搜索结果中的GET参数（只获取前100个结果）。<br />例子：`python SQLMAP.py -g "inurl:\".php?id=1\""`<br />此外可以使用`-c`参数加载SQLMAP.conf文件里面的相关配置。


# 请求

## http数据
参数：`--data`<br />此参数是把数据以POST方式提交，SQLMAP会像检测GET参数一样检测POST的参数。<br />例子：`python SQLMAP.py -u "http://www.target.com/vuln.php" --data="id=1" -f --banner --dbs --users`


## 参数拆分字符
参数：`--param-del`<br />当GET或POST的数据需要用其他字符分割测试参数的时候需要用到此参数。<br />例子：`python SQLMAP.py -u "http://www.target.com/vuln.php" --data="query=foobar;id=1" --param-del=";" -f --banner --dbs --users`


## HTTP cookie头
参数：`--cookie`,`--load-cookies`,`--drop-set-cookie`<br />这个参数在以下两个方面很有用：

1. web应用需要登陆的时候。
2. 想要在这些头参数中测试SQL注入时。

可以通过抓包把cookie获取到，复制出来，然后加到--cookie参数里。<br />在HTTP请求中，遇到Set-Cookie的话，SQLMAP会自动获取并且在以后的请求中加入，并且会尝试SQL注入。<br />如果不想接受Set-Cookie可以使用--drop-set-cookie参数来拒接。<br />当使用`--cookie`参数时，当返回一个Set-Cookie头的时候，SQLMAP会询问用哪个cookie来继续接下来的请求。当`--level`的参数设定为2或者2以上的时候，SQLMAP会尝试注入Cookie参数。


## HTTP User-Agent头
参数：`--user-agent`,`--random-agent`<br />默认情况下SQLMAP的HTTP请求头中User-Agent值是：`SQLMAP/1.0-dev-xxxxxxx (http://SQLMAP.org)`<br />可以使用`--user-agent`参数来修改，同时也可以使用`--random-agent`参数来随机的从./txt/user-agents.txt中获取。<br />当`--level`参数设定为3或者3以上的时候，会尝试对User-Angent进行注入。


## HTTP Referer头
参数：`--referer`<br />SQLMAP可以在请求中伪造HTTP中的referer，当`--level`参数设定为3或者3以上的时候会尝试对referer注入。


## 额外的HTTP头
参数：`--headers`<br />可以通过`--headers`参数来增加额外的http头


## HTTP认证保护
参数：`--auth-type`,`--auth-cred`<br />这些参数可以用来登陆HTTP的认证保护支持三种方式：<br />1、Basic<br />2、Digest<br />3、NTLM<br />例子：`python SQLMAP.py -u "http://192.168.136.131/SQLMAP/mysql/basic/get_int.php?id=1" --auth-type Basic --auth-cred "testuser:testpass"`


## HTTP协议的证书认证
参数：`--auth-cert`<br />当Web服务器需要客户端证书进行身份验证时，需要提供两个文件:key_file，cert_file。<br />key_file是格式为PEM文件，包含着你的私钥，cert_file是格式为PEM的连接文件。


## HTTP(S)代理
参数：`--proxy`,`--proxy-cred`和--ignore-proxy<br />使用`--proxy`代理是格式为：http://url:port。<br />当HTTP(S)代理需要认证是可以使用`--proxy-cred`参数：username:password。<br />`--ignore-proxy`拒绝使用本地局域网的HTTP(S)代理。


## HTTP请求延迟
参数：`--delay`<br />可以设定两个HTTP(S)请求间的延迟，设定为0.5的时候是半秒，默认是没有延迟的。<br />**设定超时时间**<br />参数：`--timeout`<br />可以设定一个HTTP(S)请求超过多久判定为超时，10.5表示10.5秒，默认是30秒。

**设定重试超时**<br />参数：`--retries`<br />当HTTP(S)超时时，可以设定重新尝试连接次数，默认是3次。

**设定随机改变的参数值**<br />参数：`--randomize`<br />可以设定某一个参数值在每一次请求中随机的变化，长度和类型会与提供的初始值一样。

**利用正则过滤目标网址**<br />参数：`--scope`<br />例如：`python SQLMAP.py -l burp.log --scope="(www)?\.target\.(com|net|org)"`

**避免过多的错误请求被屏蔽**<br />参数：`--safe-url`,`--safe-freq`<br />有的web应用程序会在你多次访问错误的请求时屏蔽掉你以后的所有请求，这样在SQLMAP进行探测或者注入的时候可能造成错误请求而触发这个策略，导致以后无法进行。

绕过这个策略有两种方式：<br />1、`--safe-url`：提供一个安全不错误的连接，每隔一段时间都会去访问一下。<br />2、`--safe-freq`：提供一个安全不错误的连接，每次测试请求之后都会再访问一边安全连接。


## 关掉URL参数值编码
参数：`--skip-urlencode`<br />根据参数位置，他的值默认将会被URL编码，但是有些时候后端的web服务器不遵守RFC标准，只接受不经过URL编码的值，这时候就需要用`--skip-urlencode`参数。


## 每次请求时候执行自定义的python代码
参数：`--eval`<br />在有些时候，需要根据某个参数的变化，而修改另个一参数，才能形成正常的请求，这时可以用`--eval`参数在每次请求时根据所写python代码做完修改后请求。<br />例子：`python SQLMAP.py -u "http://www.target.com/vuln.php?id=1&hash=c4ca4238a0b923820dcc509a6f75849b" --eval="import hashlib;hash=hashlib.md5(id).hexdigest()"`


# 注入

## 注入参数

参数：`-p`,`--skip`<br />SQLMAP默认测试所有的GET和POST参数，当--level的值大于等于2的时候也会测试HTTP Cookie头的值，当大于等于3的时候也会测试User-Agent和HTTP Referer头的值。但是你可以手动用-p参数设置想要测试的参数。例如： -p "id,user-anget"<br />当你使用`--level`的值很大但是有个别参数不想测试的时候可以使用`--skip`参数。<br />例如：`--skip="user-angent.referer"`<br />在有些时候web服务器使用了URL重写，导致无法直接使用SQLMAP测试参数，可以在想测试的参数后面加*<br />例如：`python SQLMAP.py -u "http://targeturl/param1/value1*/param2/value2/"`<br />SQLMAP将会测试value1的位置是否可注入。


## 指定数据库
参数：`--dbms`<br />默认情况系SQLMAP会自动的探测web应用后端的数据库是什么，SQLMAP支持的数据库有：<br />MySQL、Oracle、PostgreSQL、Microsoft SQL Server、Microsoft Access、SQLite、Firebird、Sybase、SAP MaxDB、DB2


## 指定数据库服务器系统
参数：`--os`<br />默认情况下SQLMAP会自动的探测数据库服务器系统，支持的系统有：Linux、Windows。


## 指定无效的大数字
参数：`--invalid-bignum`<br />当你想指定一个报错的数值时，可以使用这个参数，例如默认情况系id=13，SQLMAP会变成id=-13来报错，你可以指定比如id=9999999来报错。


## 只定无效的逻辑
参数：`--invalid-logical`<br />原因同上，可以指定id=13把原来的id=-13的报错改成id=13 AND 18=19。


## 注入payload
参数：`--prefix`,`--suffix`<br />在有些环境中，需要在注入的payload的前面或者后面加一些字符，来保证payload的正常执行。<br />例如，代码中是这样调用数据库的：`$query = "SELECT * FROM users WHERE id=(’" . $_GET[’id’] . "’) LIMIT 0, 1";`<br />这时你就需要`--prefix`和`--suffix`参数了：<br />`python SQLMAP.py -u "http://192.168.136.131/SQLMAP/mysql/get_str_brackets.php?id=1" -p id --prefix "’)" --suffix "AND (’abc’=’abc"`<br />这样执行的SQL语句变成：`$query = "SELECT * FROM users WHERE id=(’1’) <PAYLOAD> AND (’abc’=’abc’) LIMIT 0, 1";`


## 修改注入的数据
参数：`--tamper`<br />SQLMAP除了使用CHAR()函数来防止出现单引号之外没有对注入的数据修改，你可以使用--tamper参数对数据做修改来绕过WAF等设备。可以查看 tamper/ 目录下的有哪些可用的脚本


# 探测

## 探测等级
参数：`--level`<br />共有五个等级，默认为1，SQLMAP使用的payload可以在xml/payloads.xml中看到，你也可以根据相应的格式添加自己的payload。<br />这个参数不仅影响使用哪些payload同时也会影响测试的注入点，GET和POST的数据都会测试，HTTP Cookie在level为2的时候就会测试，HTTP User-Agent/Referer头在level为3的时候就会测试。<br />总之在你不确定哪个payload或者参数为注入点的时候，为了保证全面性，建议使用高的level值。


## 风险等级
参数：`--risk`<br />共有四个风险等级，默认是1会测试大部分的测试语句，2会增加基于事件的测试语句，3会增加OR语句的SQL注入测试。<br />在有些时候，例如在UPDATE的语句中，注入一个OR的测试语句，可能导致更新的整个表，可能造成很大的风险。<br />测试的语句同样可以在xml/payloads.xml中找到，你也可以自行添加payload。


## 页面比较
参数：`--string`,`--not-string`,`--regexp`,`--code`<br />默认情况下SQLMAP通过判断返回页面的不同来判断真假，但有时候这会产生误差，因为有的页面在每次刷新的时候都会返回不同的代码，比如页面当中包含一个动态的广告或者其他内容，这会导致SQLMAP的误判。此时用户可以提供一个字符串或者一段正则匹配，在原始页面与真条件下的页面都存在的字符串，而错误页面中不存在（使用`--string`参数添加字符串，`--regexp`添加正则），同时用户可以提供一段字符串在原始页面与真条件下的页面都不存在的字符串，而错误页面中存在的字符串（`--not-string`添加）。用户也可以提供真与假条件返回的HTTP状态码不一样来注入，例如，响应200的时候为真，响应401的时候为假，可以添加参数--code=200。<br />参数：`--text-only`,`--titles`<br />有些时候用户知道真条件下的返回页面与假条件下返回页面是不同位置在哪里可以使用`--text-only`（HTTP响应体中不同）`--titles`（HTML的title标签中不同）。


# 注入技术

## 测试是否是注入
参数：`--technique`<br />这个参数可以指定SQLMAP使用的探测技术，默认情况下会测试所有的方式。<br />支持的探测方式如下：<br />B: Boolean-based blind SQL injection（布尔型注入）<br />E: Error-based SQL injection（报错型注入）<br />U: UNION query SQL injection（可联合查询注入）<br />S: Stacked queries SQL injection（可多语句查询注入）<br />T: Time-based blind SQL injection（基于时间延迟注入）


## 设定延迟注入的时间
参数：`--time-sec`<br />当使用继续时间的盲注时，时刻使用`--time-sec`参数设定延时时间，默认是5秒。


## 设定UNION查询字段数
参数：`--union-cols`<br />默认情况下SQLMAP测试UNION查询注入会测试1-10个字段数，当--level为5的时候他会增加测试到50个字段数。设定--union-cols的值应该是一段整数，如：12-16，是测试12-16个字段数。


## 设定UNION查询使用的字符
参数：`--union-char`<br />默认情况下SQLMAP针对UNION查询的注入会使用NULL字符，但是有些情况下会造成页面返回失败，而一个随机整数是成功的，这是你可以用--union-char只定UNION查询的字符。


## 二阶SQ注入
参数：`--second-order`<br />有些时候注入点输入的数据看返回结果的时候并不是当前的页面，而是另外的一个页面，这时候就需要你指定到哪个页面获取响应判断真假。`--second-order`后门跟一个判断页面的URL地址。


# 列数据

## 标志
参数：`-b`,`--banner`<br />大多数的数据库系统都有一个函数可以返回数据库的版本号，通常这个函数是version()或者变量@@version这主要取决与是什么数据库。


## **用户**
参数：`-current-user`<br />在大多数据库中可以获取到管理数据的用户。


## **当前数据库**
参数：`--current-db`<br />返还当前连接的数据库。


## **当前用户是否为管理用**
参数：`--is-dba`<br />判断当前的用户是否为管理，是的话会返回True。


## **列数据库管理用户**
参数：`--users`<br />当前用户有权限读取包含所有用户的表的权限时，就可以列出所有管理用户。


## 列出并破解数据库用户的hash
参数：`--passwords`<br />当前用户有权限读取包含用户密码的彪的权限时，SQLMAP会现列举出用户，然后列出hash，并尝试破解。


## **列出数据库管理员权限**
参数：`--privileges`<br />当前用户有权限读取包含所有用户的表的权限时，很可能列举出每个用户的权限，SQLMAP将会告诉你哪个是数据库的超级管理员。也可以用-U参数指定你想看哪个用户的权限。


## **列出数据库管理员角色**
参数：`--roles`<br />当前用户有权限读取包含所有用户的表的权限时，很可能列举出每个用户的角色，也可以用-U参数指定你想看哪个用户的角色。<br />仅适用于当前数据库是Oracle的时候。


## **列出数据库系统的数据库**
参数：`--dbs`<br />当前用户有权限读取包含所有数据库列表信息的表中的时候，即可列出所有的数据库。


## **列举数据库表**
参数：`--tables`,`--exclude-sysdbs`,`-D`<br />当前用户有权限读取包含所有数据库表信息的表中的时候，即可列出一个特定数据的所有表。<br />如果你不提供`-D`参数来列指定的一个数据的时候，SQLMAP会列出数据库所有库的所有表。<br />`--exclude-sysdbs`参数是指包含了所有的系统数据库。<br />需要注意的是在Oracle中你需要提供的是TABLESPACE_NAME而不是数据库名称。


## **列举数据库表中的字段**
参数：`--columns`,`-C`,`-T`,`-D`<br />当前用户有权限读取包含所有数据库表信息的表中的时候，即可列出指定数据库表中的字段，同时也会列出字段的数据类型。<br />如果没有使用`-D`参数指定数据库时，默认会使用当前数据库。


## **列举数据库系统的架构**
参数：`--schema`,`--exclude-sysdbs`<br />用户可以用此参数获取数据库的架构，包含所有的数据库，表和字段，以及各自的类型。<br />加上`--exclude-sysdbs`参数，将不会获取数据库自带的系统库内容。


## **获取表中数据个数**
参数：`--count`<br />有时候用户只想获取表中的数据个数而不是具体的内容，那么就可以使用这个参数。


## **获取整个表的数据**
参数：`--dump`,`-C`,`-T`,`-D`,`--start`,`--stop`,`--first`,--last<br />如果当前管理员有权限读取数据库其中的一个表的话，那么就能获取真个表的所有内容。<br />使用`-D`,`-T`参数指定想要获取哪个库的哪个表，不适用`-D`参数时，默认使用当前库。<br />可以获取指定库中的所有表的内容，只用`-dump`跟`-D`参数（不使用`-T`与~参数）。<br />也可以用`-dump`跟`-C`获取指定的字段内容。

SQLMAP为每个表生成了一个CSV文件。<br />如果你只想获取一段数据，可以使用`--start`和`--stop`参数，例如，你只想获取第一段数据可以使用`--stop 1`，如果想获取第二段与第三段数据，使用参数 `--start 1 --stop 3`。<br />也可以用`--first`与`--last`参数，获取第几个字符到第几个字符的内容，如果你想获取字段中地三个字符到第五个字符的内容，使用`--first 3 --last 5`，只在盲注的时候使用，因为其他方式可以准确的获取注入内容，不需要一个字符一个字符的猜解。


## **获取所有数据库表的内容**
参数：`--dump-all`,`--exclude-sysdbs`<br />使用`--dump-all`参数获取所有数据库表的内容，可同时加上`--exclude-sysdbs`只获取用户数据库的表，需要注意在Microsoft SQL Server中master数据库没有考虑成为一个系统数据库，因为有的管理员会把他当初用户数据库一样来使用它。


## **搜索字段，表，数据库**
参数：`--search`,`-C`,`-T`,`-D`<br />`--search`可以用来寻找特定的数据库名，所有数据库中的特定表名，所有数据库表中的特定字段。<br />可以在一下三种情况下使用：<br />`-C`后跟着用逗号分割的列名，将会在所有数据库表中搜索指定的列名。<br />`-T`后跟着用逗号分割的表名，将会在所有数据库中搜索指定的表名<br />`-D`后跟着用逗号分割的库名，将会在所有数据库中搜索指定的库名。


## **运行自定义的SQL语句**
参数：`--sql-query`,`--sql-shell`<br />SQLMAP会自动检测确定使用哪种SQL注入技术，如何插入检索语句。<br />如果是SELECT查询语句，sqlap将会输出结果。如果是通过SQL注入执行其他语句，需要测试是否支持多语句执行SQL语句。


# 爆破数据

## **暴力破解表名**
参数：`--common-tables`<br />当使用`--tables`无法获取到数据库的表时，可以使用此参数。<br />通常是如下情况：<br />1、MySQL数据库版本小于5.0，没有information_schema表。<br />2、数据库是Microssoft Access，系统表MSysObjects是不可读的（默认）。<br />3、当前用户没有权限读取系统中保存数据结构的表的权限。<br />暴力破解的表在txt/common-tables.txt文件中，你可以自己添加。


## **暴力破解列名**
参数：`--common-columns`<br />与暴力破解表名一样，暴力跑的列名在txt/common-columns.txt中。
