
# 前言
当网站服务器被入侵时，我们需要一款Webshell检测工具，来帮助我们发现webshell，进一步排查系统可能存在的安全漏洞。

本文推荐了10款 WebShll 检测工具，用于网站入侵排查。当然，目前市场上的很多主机安全产品也都提供这种 WebShell 检测能力，比如阿里云、青藤云、safedog 等，本文暂不讨论。

---


# 1、D盾_Web查杀
阿D出品，使用自行研发不分扩展名的代码分析引擎，能分析更为隐藏的 WebShell 后门行为。

兼容性：只提供 Windows 版本。

工具下载地址：[http://www.d99net.net](http://www.d99net.net)
![20200407-1.png](_img\05-应急响应/1656915966065-f87c5e78-fba1-4fb6-adcb-808277cd8b17.png)


# 2、百度 WEBDIR+
下一代 WebShell 检测引擎，采用先进的动态监测技术，结合多种引擎零规则查杀。

兼容性：提供在线查杀木马，免费开放 API 支持批量检测。

在线查杀地址：[https://scanner.baidu.com](https://scanner.baidu.com)
![20200407-2.png](_img\05-应急响应/1656915967069-5130127c-7fa2-43ac-a1bb-e15034406f6e.png)


# 3、河马
专注 WebShell 查杀研究，拥有海量 WebShell 样本和自主查杀技术，采用传统特征+云端大数据双引擎的查杀技术。查杀速度快、精度高、误报低。

兼容性：支持 Windows、Linux，支持在线查杀。

官方网站：[https://www.shellpub.com](https://www.shellpub.com)
![20200407-3.png](_img\05-应急响应/1656915901706-59139208-758b-4e98-860b-15418015223f.png)


# 4、Web Shell Detector

Web Shell Detector 具有 WebShell 签名数据库，可帮助识别高达 99％ 的 WebShell。

兼容性：提供 PHP、Python 脚本，可跨平台，在线检测。

官方网站：[http://www.shelldetector.com](http://www.shelldetector.com)

github项目地址：[https://github.com/emposha/PHP-Shell-Detector](https://github.com/emposha/PHP-Shell-Detector)
![20200407-4.png](_img\05-应急响应/1656915950072-ea9c45af-05ce-4cb8-80f7-4cc16d906cab.png)


# 5、CloudWalker（牧云）

一个可执行的命令行版本 Webshell 检测工具。目前，项目已停止更新。

兼容性：提供 Linux版本，Windows 暂不支持。

在线查杀 demo：[https://webshellchop.chaitin.cn](https://webshellchop.chaitin.cn)

GitHub 项目地址：[https://github.com/chaitin/cloudwalker](https://github.com/chaitin/cloudwalker)
![20200407-5.png](_img\05-应急响应/1656916060238-e4f532c2-6972-4a28-9abe-a78871ee4d7d.png)


# 6、Sangfor WebShellKill
Sangfor WebShellKill (网站后门检测工具)是一款 Web 后门专杀工具，不仅支持 WebShell 的扫描，同时还支持暗链的扫描。是一款融合了多重检测引擎的查杀工具。能更精准地检测出WEB网站已知和未知的后门文件。

兼容性：支持 Windows、linux

工具下载地址：http://edr.sangfor.com.cn/backdoor_detection.html（已停止访问）<br />![20200407-7.png](_img\05-应急响应/1656915955936-5bae809e-7cb7-4805-8310-23ae9173d834.png)


# 7、深度学习模型检测 PHP Webshell
一个深度学习 PHP WebShell 查杀引擎 demo，提供在线样本检测。

在线查杀地址：[http://webshell.cdxy.me](http://webshell.cdxy.me)
![20200407-7.png](_img\05-应急响应/1656915969402-d30cd6fa-5570-4f6a-8ca3-8d9ac1df4348.png)


# 8、PHP Malware Finder

PHP-malware-finder 是一款优秀的检测webshell和恶意软件混淆代码的工具

兼容性：提供Linux 版本，Windows 暂不支持。

GitHub 项目地址：[https://github.com/jvoisin/php-malware-finder](https://github.com/jvoisin/php-malware-finder)
![20200407-8.png](_img\05-应急响应/1656915978355-61c5def7-018c-4b45-9e65-bcd2c2671392.png)

**9、findWebshell**

这个项目是一款基于 Python 开发的 WebShell 检查工具，可以根据特征码匹配检查任意类型的 WebShell 后门。

GitHub 项目地址：[https://github.com/he1m4n6a/findWebshell](https://github.com/he1m4n6a/findWebshell)
![20200407-9.png](_img\05-应急响应/1656915978674-c84e6476-b9cb-40c8-bf7a-2cd3fcd32b57.png)

**10、在线 WebShell 查杀工具**<br />在线查杀地址：[http://tools.bugscaner.com/killwebshell](http://tools.bugscaner.com/killwebshell)
![20200407-10.png](_img\05-应急响应/1656915978539-2b0b20fc-c1b3-4e3c-acfd-47f536fc7ff0.png)
