
# 安装Proxyxhains
下载安装包
```
git clone https://github.com/rofl0r/proxychains-ng.git
```
![image.png](_img/assets/1655794984201-a796c6c4-ec52-4edb-ba5e-0db929362e42.png)

进入proxychains目录编译安装
```
./configure --prefix=/usr --sysconfdir=/etc
```

![image.png](_img/assets/1655794998098-ce234fb2-7c96-41e4-82d2-eb93d8c165cc.png)
```
make && make install
```
![image.png](_img/assets/1655795004935-2ab6274c-7b52-41d4-a894-95c3725f505a.png)

```
make install-config
```
![image.png](_img/assets/1655795012007-3aa1888a-3941-4df8-b8f8-3eee86134cb7.png)

完成


# 配置代理
编辑`proxychains.conf`文件
```
vim /etc/proxychains.conf
```

在使用的时候修改最后一行的代码即可
![image.png](_img/assets/1655795024901-39359bc5-50a9-4b3e-b800-3ad6b5e00470.png)


# 使用实例

## 使用Nmap扫描
使用proxychains代理扫描内网主机，socks4的代理，只能使用tcp协议，所以nmap使用的时候要使用-sT选择使用tcp协议，要使用-Pn不使用ICMP的ping确认主机存活
![image.png](_img/assets/1655795041168-ce74a40e-d620-46d6-aecd-15984a5e4fcb.png)
