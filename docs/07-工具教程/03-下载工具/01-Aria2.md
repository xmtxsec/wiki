
# 简介
	Aria2是一款开源下载工具，支持的协议是HTTP（S）、FTP、BitTorrent、Metalink、它可以从多个来源/协议下载文件，并尝试利用最大下载带宽。可帮助简化不同设备和服务器之间的下载过程。它支持磁力链接、BT种子、http等类型的文件下载，与迅雷及QQ旋风相比，Aria2有着优秀的性能及较低的资源占用,架构本身非常轻巧，通常只需要4兆字节（HTTP下载）到9兆字节（用于BitTorrent交互）之间。最重要的一点是Aria2完全免费！
![image.png](./assets/1655795828995-718f07f8-a91c-456c-9fbf-7408c090f661.png)


# 安装
下载压缩包
![image.png](./assets/1655795820803-1e4079a3-162b-4616-944e-4257d1c97e9a.png)

解压到自定义的文件路径内双击AriaNg启动器
![image.png](./assets/1655795827855-566f2c84-0f0f-4334-96fb-92fce95c38db.png)

在浏览器访问即可
![image.png](./assets/1655795835613-04e673e1-fa96-43be-b0d5-50287abc1380.png)

显示Aria2状态为未连接，需要配置一下，将这两个地方的localhost修改为127.0.0.1即可
![image.png](./assets/1655795841580-ad2bfaa6-075b-4c04-b447-d77ed670232f.png)


# 下载测试
这里以下载Vulnhub靶场为例<br />可以看到这里达到了2M多每秒 （公司限制带宽）在家应该能更快。
![image.png](./assets/1655795852118-9e4d2257-8e4c-4feb-b1cc-ffbcba9d559c.png)
