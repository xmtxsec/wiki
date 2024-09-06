<span style='font-size:17px'>

# 一、安装Burp Suite

## 1.1、安装Java运行环境

1、JAVA官网下载地址：https://www.oracle.com/cn/java/technologies/

下载Java SE 17.0.8(LTS) 2023版Burp Suite 完美的运行脚本的环境是Java17

![image-20240907001843833](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070018922.png)



2、安装程序一直下一步，如果需要更改程序安装地址，存储程序路径都应该是英文目录下(中文目录/路径:会报错无法安装)

![image-20240907002120069](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070021116.png)



3、CMD命令行，查看是否安装Java17脚本环境成功

![image-20240907002247548](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070022612.png)



## 1.2、下载Burp Suite

Burp Suite官方下载地址：https://portswigger.net/burp/releases#professional

```
Stable：稳定版本
Early Adopter：实验版本

Professional：专业版本
Community Edition：社区版本

建议选择：Stable(稳定版本) + Burp Suite Professional(专业版本) + JAR(文件格式)
```

![image-20240907011551159](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070115204.png)



## 1.3、下载Burp Suite注册机破解脚本

Burp Suite注册机破解脚本地址：https://github.com/Leon406/BurpSuiteCN-Release

![image-20240907011643062](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070116117.png)



## 1.4、破解Burp Suite

1、下载的Burp Suite的jar文件和 Burp Suite注册机破解脚本的jar文件 (放在同一目录下)

![image-20240907011816564](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070118616.png)



2、点击 burpsuitloader-3.7.17-all.jar

![image-20240907012006819](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070120884.png)



3、点击Run，自动打开Burp Suite Professional

![image-20240907020643308](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070206343.png)



4、将破解机的License对应 Burp Suite Professional输入，点击Next(下一步)

![image-20240907020848490](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070208554.png)



5、选择手动激活

![image-20240907020907103](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070209152.png)



6、按图所示操作

![image-20240907021118987](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070211061.png)



7、页面提示：Success(成功) -> 点击Finish(完成)关闭激活注册页面

![image-20240907021130541](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070211581.png)



8、再次打开burpsuitloader-3.7.17-all.jar，勾选 auto start，点击 run。这样下次直接双击 burpsuitloader-3.7.17-all.jar 就可以打开Burp Suite。

![image-20240907021401647](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070214702.png)



9、若不想直接进入Burp Suite ，可以在命令行运行以下命令来取消步骤8中的设置。

```
java -jar burpsuitloader-3.7.17-all.jar -r
```

![image-20240907021803229](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070218294.png)



# 二、取消注册 Burp Suite的方法

1、在 Burp Suite 的 Help 界面下最后一行有个叫 Clean Burp from computer 点击后选择箭头指向的 Remove Burp license key，然后点击 Next。

![image-20240907015600729](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070156791.png)



2、删除license key删除成功

![image-20240907015635945](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070156006.png)



# 三、安装证书

**抓取 Web 服务器 Https 协议需要安装证书才能抓取数据包**

1、第一步Settings(设置) -> 点击：第二步Proxy(代理) -> 点击：第三步import / export CA certificate(导出CA证书)

![image-20240907022640255](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070226310.png)



2、点击Certificate in DER format(DER格式的证书) -> Next(下一步)

![image-20240907022721487](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070227544.png)



3、保存的目录最好是和Burp Suite其他所有文件在一起

![image-20240907022956082](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070229150.png)



4、浏览器安装Burp Suite证书，浏览器设置 -> 搜索：证书 -> 点击：安全

![image-20240907023137349](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070231403.png)



5、点击：管理设备的证书

![image-20240907023241865](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070232906.png)



6、为受信任的根证书颁布机构(安装证书)

![image-20240907023346145](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070233186.png)



![image-20240907023448432](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070234486.png)



![image-20240907023732944](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070237514.png)



7、为中间证书颁发机构(安装证书)

![image-20240907023705801](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070237856.png)



![image-20240907023732944](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070237017.png)



**注意：为什么要给两个都安装证书,是因为有的web服务器,需要奇奇怪怪的机构,才能抓取https协议,一次性给它搞定。**



# 四、浏览器安装代理插件

1、浏览器代理插件名：SwitchyOmega

![image-20240907024000526](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070240591.png)



2、设置代理规则

![image-20240907024206714](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070242793.png)



3、设置代理端口：8080 设置代理ip：127.0.0.1(本地地址)

![image-20240907024319424](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202409070243477.png)















</span>
