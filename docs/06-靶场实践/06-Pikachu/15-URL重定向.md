
# 概述
不安全的url跳转问题可能发生在一切执行了url地址跳转的地方。如果后端采用了前端传进来的(可能是用户传参,或者之前预埋在前端页面的url地址)参数作为了跳转的目的地,而又没有做判断的话就可能发生"跳错对象"的问题。

url跳转比较直接的危害是:钓鱼,既攻击者使用漏洞方的域名(比如一个比较出名的公司域名往往会让用户放心的点击)做掩盖,而最终跳转的确实钓鱼网站


# 不安全的URL跳转

## 查看关键代码
```php
$html="";
if(isset($_GET['url']) && $_GET['url'] != null){
    $url = $_GET['url'];
    if($url == 'i'){
        $html.="<p>好的,希望你能坚持做你自己!</p>";
    }else {
        header("location:{$url}");
    }
}
```

没啥说的嘛，直接将URL参数代入并没有任何的限制。


## 函数说明
```
isset() 函数用于检测变量是否已设置并且非 NULL。如果指定变量存在且不为 NULL，则返回 TRUE，否则返回 FALSE

语法
bool isset ( mixed $var [, mixed $... ] )
$var：要检测的变量。
```

```
header() 函数向客户端发送原始的 HTTP 报头。

语法
header(string,replace,http_response_code)
string	必需。规定要发送的报头字符串。
replace	可选。指示该报头是否替换之前的报头，或添加第二个报头。默认是 TRUE（替换）。FALSE（允许相同类型的多个报头）。
http_response_code	可选。把 HTTP 响应代码强制为指定的值。（PHP 4.3 以及更高版本可用）
```


## 实战
点击页面的最后一个“ 我就是我,放荡不羁的我”即可看到跳转的URL<br />![image-20210715154001115.png](./assets/1655881284785-8ab426af-1584-4017-bdb8-d26847eba6cd.png)

将URL中的i改为我们想要跳转的网站，即可跳转<br />![image-20210715154110329.png](./assets/1655881296734-fd8dc924-fcf7-452c-ac7b-8d470d1ed144.png)
![image-20210715154125491.png](./assets/1655881289475-cbf9f525-5430-47d5-8db3-2f0fc0dbe8bc.png)


