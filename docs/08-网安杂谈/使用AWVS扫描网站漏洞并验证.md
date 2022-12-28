
# 简介
**AWVS全称Acunetix Web Vulnerability Scanner是一个网站及服务器漏洞扫描软件，它包含有收费和免费两种版本。**<br />**主要功能有：**

1. 自动的客户端脚本分析器，允许对 Ajax 和 Web 2.0 应用程序进行安全性测试。
2. 业内最先进且深入的 SQL 注入和跨站脚本测试
3. 高级渗透测试工具，例如 HTTP Editor 和 HTTP Fuzzer
4. 可视化宏记录器帮助您轻松测试 web 表格和受密码保护的区域
5. 支持含有 CAPTHCA 的页面，单个开始指令和 Two Factor（双因素）验证机制
6. 丰富的报告功能，包括 VISA PCI 依从性报告
7. 高速的多线程扫描器轻松检索成千上万个页面
8. 智能爬行程序检测 web 服务器类型和应用程序语言
9. Acunetix 检索并分析网站，包括 flash 内容、SOAP 和 AJAX
10. 端口扫描 web 服务器并对在服务器上运行的网络服务执行安全检查
11. 可导出网站漏洞文件


# AWVS扫描
**在这里我所扫描的网站，并非是真实运营的网站，而是自己在网上下载源码所搭建的网站。咱还是要遵守法律的。**

**首先我们来看看网站**<br />![](https://img-blog.csdnimg.cn/20201011222122366.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=l8iZt&originHeight=674&originWidth=955&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

打开AWVS，会弹出一个页面，我们就在此页面进行设置<br />![](https://img-blog.csdnimg.cn/20201011222344994.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=w9RU5&originHeight=674&originWidth=869&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在此处直接默认下一步<br />![](https://img-blog.csdnimg.cn/20201011222423174.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=A5XJy&originHeight=677&originWidth=872&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

继续默认下一步<br />![](https://img-blog.csdnimg.cn/20201011222457610.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=Q0FYf&originHeight=669&originWidth=880&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在这里如果网站需要口令登录的话，在此处需要输入口令，我在这里不需要输入<br />![](https://img-blog.csdnimg.cn/20201011222659354.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=y8dwc&originHeight=672&originWidth=874&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

接下来直接点击Finish<br />![](https://img-blog.csdnimg.cn/2020101122274521.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=W9Z4O&originHeight=671&originWidth=874&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

然后我们只需要静静的等待扫描完毕就可以了

大约等待了三四分钟，ok扫描完毕<br />![](https://img-blog.csdnimg.cn/20201011223246995.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=VIPyr&originHeight=714&originWidth=1359&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

通过扫描结果我们可以看到，该网站存在5个高危漏洞，18个中级漏洞，3个低级漏洞。


# 漏洞验证
我们只验证高危漏洞

## 第一个SQL注入漏洞<br />![](https://img-blog.csdnimg.cn/20201011223622226.png#pic_center#crop=0&crop=0&crop=1&crop=1&id=fqbui&originHeight=90&originWidth=324&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
<br />
我们找到所利用的payload<br />![](https://img-blog.csdnimg.cn/2020101122413563.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=J4wkU&originHeight=403&originWidth=1123&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

一个payload的组成是有referer和get组成<br />在这里为：`http://172.168.1.5:8001/owen.asp?n=20&owen1=-1'%20OR%203_2_1=6%20AND%20000656=000656%20or%20'JiATSxBj'='`

然后我们尝试在sqlamp上去扫<br />![](https://img-blog.csdnimg.cn/20201011224325369.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=s80PD&originHeight=369&originWidth=809&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

需要输入y/n/q的直接一路默认回车就行了

接下来就是等待<br />![](https://img-blog.csdnimg.cn/20201011225530373.png#pic_center#crop=0&crop=0&crop=1&crop=1&id=NNdH8&originHeight=50&originWidth=804&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://img-blog.csdnimg.cn/20201011225543335.png#pic_center#crop=0&crop=0&crop=1&crop=1&id=wFe6x&originHeight=37&originWidth=736&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://img-blog.csdnimg.cn/20201011225552385.png#pic_center#crop=0&crop=0&crop=1&crop=1&id=Sz7eV&originHeight=49&originWidth=808&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://img-blog.csdnimg.cn/20201011225607242.png#pic_center#crop=0&crop=0&crop=1&crop=1&id=DhCnc&originHeight=125&originWidth=807&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

通过sqlmap返回的结果显示并不存在注入，我们根据提示在后面加上--level试试<br />![](https://img-blog.csdnimg.cn/20201011231423754.png#pic_center#crop=0&crop=0&crop=1&crop=1&id=c9WsW&originHeight=48&originWidth=814&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在经过漫长的等待之后，结果是令人失望的，还是没有扫描出存在注入的地方，既然如此我们就当它误报吧<br />![](https://img-blog.csdnimg.cn/20201012001435938.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=lqSqU&originHeight=218&originWidth=797&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 第二个XSS漏洞
<br />![](https://img-blog.csdnimg.cn/20201011231703600.png#pic_center#crop=0&crop=0&crop=1&crop=1&id=pOqpH&originHeight=198&originWidth=1056&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

找到所需要利用的payload<br />![](https://img-blog.csdnimg.cn/20201011231754986.png#pic_center#crop=0&crop=0&crop=1&crop=1&id=T5BNd&originHeight=192&originWidth=490&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
payload为：`http://172.168.1.5:8001/Product.asp?BigClassName=%C9%FA%B2%FA%3F%D6%D6'"()%26%25<ScRiPt%20>Abq8(9382)`

在浏览器中进行访问看是否弹框。<br />![](https://img-blog.csdnimg.cn/20201011232027360.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=zJSPF&originHeight=295&originWidth=826&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

发现没有弹框，有可能是AWVS误报或者payload不合适。从下图可以找到插入点，尝试写自己的payload。

![](https://img-blog.csdnimg.cn/20201011232118764.png#pic_center#crop=0&crop=0&crop=1&crop=1&id=ujhjy&originHeight=100&originWidth=464&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

payload：`http://172.168.1.5:8001/Product.asp?BigClassName=<script>alert("123456")</script>`<br />成功弹窗<br />![](https://img-blog.csdnimg.cn/20201011232642255.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=CNzTQ&originHeight=393&originWidth=651&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
说明漏洞存在

## <br /><br />第三个XSS漏洞
![](https://img-blog.csdnimg.cn/20201011233210394.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=aGeHJ&originHeight=275&originWidth=1045&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

找到所需要的payload<br />![](https://img-blog.csdnimg.cn/20201011233400864.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=KcHya&originHeight=305&originWidth=471&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

payload为：`http://172.168.1.5:8001/Search.asp?BigClassName=1'"()%26%25<ScRiPt%20>8J95(9081)&Field=&Keyword=&page=2&SmallClassName=`<br />在浏览器中打开payload<br />哦豁，与前面一样，并没有弹窗<br />![](https://img-blog.csdnimg.cn/20201011233649245.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=XGNGL&originHeight=301&originWidth=787&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

接下来尝试使用自己构造payload<br />payload为：`http://172.168.1.5:8001/Product.asp?BigClassName=<script>alert("1234656789")</script>`<br />![](https://img-blog.csdnimg.cn/20201011233810477.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=Y8lBm&originHeight=391&originWidth=818&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
漏洞存在


## 第四个XSS漏洞
<br />![](https://img-blog.csdnimg.cn/20201011234103632.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=V87ZJ&originHeight=265&originWidth=968&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

从图中可以看出这个依然为xss的漏洞<br />当然还是老方法了

查找payload<br />![](https://img-blog.csdnimg.cn/20201011234224624.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=rwbyZ&originHeight=360&originWidth=505&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

由图可得payload为：`http://172.168.1.5:8001/Search.asp?BigClassName=&Field=1'"()%26%25<ScRiPt%20>8J95(9851)&Keyword=&page=2&SmallClassName=`

将payload复制到浏览器中验证<br />![](https://img-blog.csdnimg.cn/20201011234356497.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=r7kj2&originHeight=451&originWidth=867&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

还是与前面一样，并没有弹窗<br />当然还是尝试自己构造payload咯

payload为：`http://172.168.1.5:8001/Search.asp?BigClassName=&Field=`<br />将payload复制到浏览器中会发现，然并卵，与前面的不同，依然没有弹窗<br />我们来查看一手源码<br />![](https://img-blog.csdnimg.cn/20201011235151867.png#pic_center#crop=0&crop=0&crop=1&crop=1&id=DH5iR&originHeight=44&originWidth=1298&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
由上图源码可知是因为<没有闭合，所以修改payload后就弹框了。

修改后payload为：`http://172.168.1.5:8001/Search.asp?BigClassName=&Field='>`<br />弹窗成功<br />![](https://img-blog.csdnimg.cn/20201011235405732.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=PdUKT&originHeight=388&originWidth=780&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 第五个XSS漏洞
<br />![](https://img-blog.csdnimg.cn/20201011235740152.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=aHrlf&originHeight=212&originWidth=1011&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

依然是xss漏洞

老套路找payload（小声BB：我都弄烦了）<br />![](https://img-blog.csdnimg.cn/20201011235911356.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=tBZtZ&originHeight=310&originWidth=485&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

可以看到payload为：`http://172.168.1.5:8001/Search.asp?BigClassName=&Field=&Keyword=&page=2&SmallClassName=1'"()%26%25<ScRiPt%20>8J95(9755)`

接下来当然是去浏览器中访问payload咯，意料之中，没有弹窗<br />![](https://img-blog.csdnimg.cn/20201012000053670.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=TRwzu&originHeight=434&originWidth=851&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

自己构建payload<br />payload为：`http://172.168.1.5:8001/Search.asp?BigClassName=&Field=&Keyword=&page=2&SmallClassName=`

浏览器中访问，依然没有弹窗<br />![](https://img-blog.csdnimg.cn/2020101200023344.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=dxJPd&originHeight=434&originWidth=852&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

查看源码<br />![](https://img-blog.csdnimg.cn/20201012000330605.png#pic_center#crop=0&crop=0&crop=1&crop=1&id=dGLFz&originHeight=56&originWidth=1341&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

这一看就晓得了，还是因为<没有闭合，修改payload使它闭合就完事了<br />`172.168.1.5:8001/Search.asp?BigClassName=&Field=&Keyword=&page=2&SmallClassName='>`<br />![](https://img-blog.csdnimg.cn/20201012001034306.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=mwlIP&originHeight=383&originWidth=794&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
弹窗成功。
