
# 概述
其形成的原因大都是由于服务端**提供了从其他服务器应用获取数据的功能**,但又没有对目标地址做严格过滤与限制，导致攻击者可以传入任意的地址来让后端服务器对其发起请求,并返回对该目标地址请求的数据

            数据流:攻击者----->服务器---->目标地址

根据后台使用的函数的不同,对应的影响和利用方法又有不一样
```
PHP中下面函数的使用不当会导致SSRF:
file_get_contents()
fsockopen()
curl_exec()
```

如果一定要通过后台服务器远程去对用户指定("或者预埋在前端的请求")的地址进行资源请求,**则请做好目标地址的过滤**。


# SSRF(curl)

## 查看关键代码
```php
if(isset($_GET['url']) && $_GET['url'] != null){

    //接收前端URL没问题,但是要做好过滤,如果不做过滤,就会导致SSRF
    $URL = $_GET['url'];
    $CH = curl_init($URL);
    curl_setopt($CH, CURLOPT_HEADER, FALSE);
    curl_setopt($CH, CURLOPT_SSL_VERIFYPEER, FALSE);
    $RES = curl_exec($CH);
    curl_close($CH) ;
//ssrf的问是:前端传进来的url被后台使用curl_exec()进行了请求,然后将请求的结果又返回给了前端。
//除了http/https外,curl还支持一些其他的协议curl --version 可以查看其支持的协议,telnet
//curl支持很多协议，有FTP, FTPS, HTTP, HTTPS, GOPHER, TELNET, DICT, FILE以及LDAP
    echo $RES;

}
```

额...没什么好讲的。


## 函数说明
```
isset() 函数用于检测变量是否已设置并且非 NULL。如果指定变量存在且不为 NULL，则返回 TRUE，否则返回 FALSE

语法
bool isset ( mixed $var [, mixed $... ] )
$var：要检测的变量。
```

```
curl_init ([ string $url = NULL ] )
初始化一个新的会话，返回一个cURL句柄，供curl_setopt(), curl_exec()和curl_close() 函数使用。
如果成功，返回一个cURL句柄，出错返回 FALSE。
```

```
bool curl_setopt ( resource $ch , int $option , mixed $value )
为给定的cURL会话句柄设置一个选项。

ch        由 curl_init() 返回的 cURL 句柄。
option    需要设置的CURLOPT_XXX选项。
value     将设置在option选项上的值。

成功时返回 TRUE， 或者在失败时返回 FALSE。
```

```
mixed curl_exec ( resource $ch )
执行给定的cURL会话。

ch   由 curl_init() 返回的 cURL 句柄。

成功时返回 TRUE， 或者在失败时返回 FALSE。 然而，如果 CURLOPT_RETURNTRANSFER选项被设置，函数执行成功时会返回执行的结果，失败时返回 FALSE 。
```

```
void curl_close ( resource $ch )
关闭一个cURL会话并且释放所有资源。cURL句柄ch 也会被释放。

ch   由 curl_init() 返回的 cURL 句柄。

没有返回值。
```


## 实战
点击页面的最后一个“ 累了吧,来读一首诗吧”即可看到改变的URL<br />![image-20210715160329917.png](./assets/1655881151767-241d0ecc-69ae-49cd-8d45-242b7615145f.png)

修改URL即可跳转<br />![image-20210715160504426.png](./assets/1655881156431-d1ac8fc3-c789-4e2a-ad2a-8fea2135eddd.png)


# SSRF(file_get_content)

## 查看关键代码
```php
if(isset($_GET['file']) && $_GET['file'] !=null){
    $filename = $_GET['file'];
    $str = file_get_contents($filename);
    echo $str;
}
```

啊这...


## 函数说明
```
file_get_contents() 把整个文件读入一个字符串中。该函数是用于把文件的内容读入到一个字符串中的首选方法。如果服务器操作系统支持，还会使用内存映射技术来增强性能。语法file_get_contents(path,include_path,context,start,max_length)path	必需。规定要读取的文件。include_path	可选。如果您还想在 include_path（在 php.ini 中）中搜索文件的话，请设置该参数为 '1'。context	可选。规定文件句柄的环境。context 是一套可以修改流的行为的选项。若使用 NULL，则忽略。start	可选。规定在文件中开始读取的位置。该参数是 PHP 5.1 中新增的。max_length	可选。规定读取的字节数。该参数是 PHP 5.1 中新增的。
```


## 实战
点击页面的最后一个“ 反正都读了,那就在来一首吧”即可看到改变的URL<br />![image-20210715160903943.png](./assets/1655881177784-effa74c7-7b85-4eda-a657-b29a08bd9d67.png)

修改URL查看passwd文件<br />![image-20210715160958015.png](./assets/1655881181453-8a7c311a-f253-4deb-a164-11a6d176c37d.png)

到此pikachu靶场就到此结束了，emm个人感觉啊这个靶场的前面一部分还不错后面就比较鸡肋了...
