网站被植入webshell，意味着网站存在可利用的高危漏洞，攻击者通过利用漏洞入侵网站，写入webshell接管网站的控制权。为了得到权限 ，常规的手段如：前后台任意文件上传，远程命令执行，Sql注入写入文件等。


# 0X00 现象描述
网站管理员在站点目录下发现存在webshell，于是开始了对入侵过程展开了分析。<br />![1-1.png](../../_img\05-应急响应/1656922981740-5fb00f9a-670f-4eb5-9648-02bc3b682fd5.png)

Webshell查杀工具：<br />D盾_Web查杀<br />Window下webshell查杀：[http://www.d99net.net/index.asp](http://www.d99net.net/index.asp)

河马：支持多平台，但是需要联网环境。<br />使用方法:<br />wget http://down.shellpub.com/hm/latest/hm-linux-amd64.tgz<br />tar xvf hm-linux-amd64.tgz<br />hm scan  /www


# 0X01 事件分析

## 1、定位时间范围
通过发现的webshell文件创建时间点，去翻看相关日期的访问日志。<br />![1-2.png](../../_img\05-应急响应/1656923008641-141fd0ae-7821-4ab7-896f-fd26d7050d2c.png)


## 2、Web 日志分析
经过日志分析，在文件创建的时间节点并未发现可疑的上传，但发现存在可疑的webservice接口<br />![1-3.png](../../_img\05-应急响应/1656923014828-d15bdff4-4666-4a83-ac5d-4995c3e30a54.png)


## 3、漏洞分析
访问webservice接口，发现变量：buffer、distinctpach、newfilename可以在客户端自定义<br />![1-4.png](../../_img\05-应急响应/1656923020518-66a015a4-cd61-40da-bdee-82ddcde6d463.png)


## 4、漏洞复现
尝试对漏洞进行复现，可成功上传webshell，控制网站服务器<br />![1-5.png](../../_img\05-应急响应/1656923025131-677fb1bd-ba3e-4a3b-a268-5662130aca3b.png)

![1-6.png](../../_img\05-应急响应/1656923030314-446929aa-38f2-4c06-8327-4feafe3510db.png)


## 5、漏洞修复
清除webshell并对webservice接口进行代码修复。

从发现webshell到日志分析，再到漏洞复现和修复，本文暂不涉及溯源取证方面。
