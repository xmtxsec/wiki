攻击者在入侵网站时，通常要通过各种方式写入Webshell，从而获得服务器的控制权限，比如执行系统命令、读取配置文件、窃取用户数据，篡改网站页面等操作。

本文介绍十款常用的Webshell管理工具，以供你选择，你会选择哪一个？

---

**1、中国菜刀(Chopper)**<br />中国菜刀是一款专业的网站管理软件，用途广泛，使用方便，小巧实用。只要支持动态脚本的网站，都可以用中国菜刀来进行管理！在非简体中文环境下使用，自动切换到英文界面。UNICODE方式编译，支持多国语言输入显示。<br />![privilege-6-1.png](/_img\05-应急响应/1656914560265-4ea4efad-e886-4135-bfda-212e100b716f.png)

**2、蚁剑(AntSword)**<br />AntSword是一个开放源代码，跨平台的网站管理工具，旨在满足渗透测试人员以及具有权限和/或授权的安全研究人员以及网站管理员的需求。

github项目地址：[https://github.com/AntSwordProject/antSword](https://github.com/AntSwordProject/antSword)
![privilege-6-2.png](/_img\05-应急响应/1656914567627-cf37823b-e284-4111-a791-0df9134cc8ee.png)

**3、C刀(Cknife)**<br />这是一款跨平台的基于配置文件的中国菜刀，把所有操作给予用户来定义。<br />github项目地址：[https://github.com/Chora10/Cknife](https://github.com/Chora10/Cknife)
![privilege-6-3.png](/_img\05-应急响应/1656914574684-2354894a-eff8-43d8-bc7a-601fd3f23389.png)

**4、冰蝎(Behinder)**<br />冰蝎”是一款动态二进制加密网站管理客户端。<br />github地址：[https://github.com/rebeyond/Behinder](https://github.com/rebeyond/Behinder)
![privilege-6-4.png](/_img\05-应急响应/1656914581335-609c4b74-3eed-4047-8b44-bdf7fb5062ea.png)

**5、Xise**<br />XISE WebShell管理工具。<br />![privilege-6-5.png](/_img\05-应急响应/1656914589244-3f0e55bd-9eed-4b30-b6f2-b9436b94ff83.png)

**6、Altman**<br />Altman3是一款渗透测试软件，基于.Net4.0开发，依托Eto.Form可以完美运行在Windows、Linux、Mac等多个平台。<br />github项目地址：[https://github.com/keepwn/Altman](https://github.com/keepwn/Altman)
![privilege-6-6.png](/_img\05-应急响应/1656914592902-aaf8cd32-68a4-4349-ba7f-03bf618a4a4a.png)

**7、Weevely**<br />Weevely是一种Python编写的webshell管理工具，跨平台，只支持PHP。<br />github项目地址：[https://github.com/epinna/weevely3](https://github.com/epinna/weevely3)
用法示例：
```
weevely generate <password> <path>
weevely <URL> <password> [cmd]
```

同时，在Kali 2.0 版本下，集成了三款Web后门工具：WebaCoo、weevely、PHP Meterpreter。<br />![privilege-6-7.png](/_img\05-应急响应/1656914602840-164259c8-94c1-48e8-ae32-2dd90b2be781.png)

**8、QuasiBot**<br />QuasiBot是一款php编写的webshell管理工具，可以对webshell进行远程批量管理。<br />github项目地址：[https://github.com/Smaash/quasibot](https://github.com/Smaash/quasibot)
![privilege-6-8.png](/_img\05-应急响应/1656914608415-a8f90d27-b709-438a-8bc8-432b568f0c39.png)

**9、Webshell-Sniper**<br />这是一款基于终端的webshell管理工具，仅支持在类Unix系统上运行。<br />github项目地址：[https://github.com/WangYihang/Webshell-Sniper](https://github.com/WangYihang/Webshell-Sniper)
用法示例：
```
Usage : 
        python webshell-sniper.py [URL] [METHOD] [AUTH]
Example : 
        python webshell-sniper.py http://127.0.0.1/c.php POST c
```
![privilege-6-9.png](/_img\05-应急响应/1656914708512-577d774f-ec99-4e2e-a65b-530215c28bec.png)

**10、WebshellManager**<br />一款用PHP+Mysql写的一句话WEB端管理工具，目前仅支持对PHP的一句话进行操作。<br />github项目地址：[https://github.com/boy-hack/WebshellManager](https://github.com/boy-hack/WebshellManager)
![privilege-6-10.jpg](/_img\05-应急响应/1656914626148-6226ee52-93b2-4778-8a05-d1594197b7e8.jpeg)

