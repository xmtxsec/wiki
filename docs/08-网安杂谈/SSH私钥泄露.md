首先打开靶机我并不知道靶机的密码也不知道靶机的ip<br />![](https://img-blog.csdnimg.cn/20210309115152880.png#crop=0&crop=0&crop=1&crop=1&id=t0LgQ&originHeight=163&originWidth=507&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

要想进行渗透，首先我们就得先知道目标机器得ip，在这里我想到了可以利用msfconsole中的扫描模块进行扫描

查看扫描模块<br />![](https://img-blog.csdnimg.cn/20210309115157544.png#crop=0&crop=0&crop=1&crop=1&id=ATrDU&originHeight=145&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

利用模块auxiliary/scanner/portscan/tcp并查看需要配置得参数，设置ip段以及扫描的端口并开始扫描<br />![](https://img-blog.csdnimg.cn/20210309115204186.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=g2Ahg&originHeight=317&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

通过扫描我们可以得知目标机的IP为192.168.1.14<br />![](https://img-blog.csdnimg.cn/20210309115209570.png#crop=0&crop=0&crop=1&crop=1&id=Jgffa&originHeight=66&originWidth=490&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

既然知道了目标IP那么我们紧接着就进行端口扫描
```bash
nmap -sS -nP -sV 192.168.1.14
-sS：执行一次隐秘的TCP扫描。
-Pn：不使用ping扫描，默认所有主机都是存活状态。
-sV：探测打开的端口以确定服务/版本信息
```
![](https://img-blog.csdnimg.cn/20210309115215926.png#crop=0&crop=0&crop=1&crop=1&id=VgeGC&originHeight=176&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

可以看到开放了三个端口分别是：22、80、31337

看到了大端口31337而且是HTTP服务，那当然先探测大端口信息，在浏览器中输入：
```bash
http://192.168.1.14:31337/
```
![](https://img-blog.csdnimg.cn/20210309115220653.png#crop=0&crop=0&crop=1&crop=1&id=xtJNk&originHeight=114&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

啥也没有

尝试查看源代码获取信息,通过源代码我们可以看到获取不到任何信息<br />![](https://img-blog.csdnimg.cn/20210309115224765.png#crop=0&crop=0&crop=1&crop=1&id=jVCwf&originHeight=90&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

依然没有什么有用的东西

使用dirb对服务隐藏文件进行探测<br />dirb是一个基于字典的web目录扫描工具，会用递归的方式来获取更多的目录，它还支持代理和http认证限制访问的网站
```bash
格式：dirb <url_base> [<wordlist_file(s)>] [options]
-a 设置user-agent
-p <proxy[:port]>设置代理
-c 设置cookie
-z 添加毫秒延迟，避免洪水攻击
-o 输出结果
-X 在每个字典的后面添加一个后缀
-H 添加请求头
-i 不区分大小写搜索
```

在终端输入
```bash
dirb http://192.168.54.132:31337/
```
![](https://img-blog.csdnimg.cn/20210309115230906.png#crop=0&crop=0&crop=1&crop=1&id=oUoRs&originHeight=199&originWidth=501&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

结果显示出了一些文件

依次去访问这些文件发现大部分并没有什么卵用。不过在访问http://192.168.1.14:31337/.ssh时我们发现<br />![](https://img-blog.csdnimg.cn/20210309115236713.png#crop=0&crop=0&crop=1&crop=1&id=aBJJB&originHeight=102&originWidth=428&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

有一个id_rsa的私钥和一个服务器上的id_rsa.pub公钥去对比，对比成功则成功登录。

尝试去访问http://192.168.1.14:31337/robots.txt<br />![](https://img-blog.csdnimg.cn/20210309115242886.png#crop=0&crop=0&crop=1&crop=1&id=ytctV&originHeight=142&originWidth=470&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

我们可以看到发现了一些隐藏文件，下面去尝试访问这些文件<br />当访问http://192.168.54.132:31337/taxes/的时候，可以看到第个flag<br />![](https://img-blog.csdnimg.cn/20210309115247754.png#crop=0&crop=0&crop=1&crop=1&id=SycPx&originHeight=99&originWidth=402&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在前面我们看到有一个id_rsa的私钥和一个服务器上的id_rsa.pub公钥去对比，对比成功则成功登录。<br />我们来尝试一下是否存在一个id_rsa的文件
```bash
http://192.168.1.14:31337/.ssh/id_rsa
```
![](https://img-blog.csdnimg.cn/20210309115251901.png#crop=0&crop=0&crop=1&crop=1&id=z6jbB&originHeight=185&originWidth=433&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

我们将该文件下载下来

同样去访问authorized_keys，并也将该文件下载下来
```bash
http://192.168.1.14:31337/.ssh/authorized_keys
```
![](https://img-blog.csdnimg.cn/20210309115255811.png#crop=0&crop=0&crop=1&crop=1&id=nyvve&originHeight=170&originWidth=414&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

将下载下来的文件查看一下<br />Id_rsa文件需要密码才能打开，在authorized_keys文件中疑似看到了一个账户名称<br />![](https://img-blog.csdnimg.cn/20210309115300357.png#crop=0&crop=0&crop=1&crop=1&id=nt53J&originHeight=68&originWidth=351&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

既然疑似那我们就当就是他，尝试使用该账户去进行链接
```bash
ssh -i id_rsa simon@192.168.1.14 //-i 私钥文件
```
![](https://img-blog.csdnimg.cn/20210309115304920.png#crop=0&crop=0&crop=1&crop=1&id=uW1jb&originHeight=185&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

结果显示我们无法建立连接，权限太开放，那我们就去降低权限
```bash
chmod 600 id_rsa
```
![](https://img-blog.csdnimg.cn/20210309115309780.png#crop=0&crop=0&crop=1&crop=1&id=GPg2S&originHeight=86&originWidth=473&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

再次尝试登录
```bash
ssh -i id_rsa simon@192.168.1.14
```
![](https://img-blog.csdnimg.cn/20210309115313626.png#crop=0&crop=0&crop=1&crop=1&id=ylZgy&originHeight=42&originWidth=466&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

提示要我们是输入密码

尝试使用simon无法登录<br />![](https://img-blog.csdnimg.cn/20210309115318207.png#crop=0&crop=0&crop=1&crop=1&id=EAhTy&originHeight=96&originWidth=456&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

好家伙密码不对，不过问题不大

使用ssh2john将id_isa密钥信息转换为john可以识别的信息
```bash
python /usr/share/john/ssh2john.py id_rsa > 111
```
![](https://img-blog.csdnimg.cn/20210309115325988.png#crop=0&crop=0&crop=1&crop=1&id=SN6MN&originHeight=50&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

查看一下111<br />![](https://img-blog.csdnimg.cn/20210309115331440.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=AdqSS&originHeight=237&originWidth=458&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

看不懂全是密文

利用字典解密isacrack信息
```bash
zcat /usr/share/wordlists/rockyou.txt.gz |john --pipe --rules 111
```

使用zcat指令加载john自带的rockyou.txt字典（zcat可以不真正解压缩文件，就能显示压缩包中文件的内容），<br />并将结果用管道传递给john工具（“ | ”符号叫做管道符号，可以把前一个命令原本要输出到屏幕的数据当作是后一个命令的标准输入），

最后使用john工具在已知密文的情况下尝试破解出明文。<br />![](https://img-blog.csdnimg.cn/20210309115336469.png#crop=0&crop=0&crop=1&crop=1&id=odWqE&originHeight=154&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

得到密码starwars

使用密码登录，登录成功。
```bash
ssh -i id_rsa simon@192.168.1.14
```
![](https://img-blog.csdnimg.cn/20210309115340326.png#crop=0&crop=0&crop=1&crop=1&id=cJzQH&originHeight=181&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

看一下目录下的文件
```bash
Ls
```

![](https://img-blog.csdnimg.cn/20210309115344673.png#crop=0&crop=0&crop=1&crop=1&id=Mlt7P&originHeight=38&originWidth=231&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

并没有我们flag，尝试切换到root下，并查看文件<br />![](https://img-blog.csdnimg.cn/20210309115348166.png#crop=0&crop=0&crop=1&crop=1&id=iMSiE&originHeight=37&originWidth=222&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

看到了flag.txt文件，访问该文件结果报错，我们的权限不够，那就要提权了<br />![](https://img-blog.csdnimg.cn/20210309115351609.png#crop=0&crop=0&crop=1&crop=1&id=Ugoa2&originHeight=42&originWidth=318&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

权限提升，查看一下那些文件具有root的权限
```bash
find / -perm -4000 2>/dev/null
```
![](https://img-blog.csdnimg.cn/20210309115356323.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=UPYwI&originHeight=229&originWidth=416&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
可以看到/usr/local/bin/read_message和我们当前的read_message.c是一样的，我们先来看一下read_message.c

查看read_message.c
```bash
cat read_message.c
```
![](https://img-blog.csdnimg.cn/20210309115408138.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=aFX70&originHeight=251&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在这里我们可以看到第二个flag

通过审计代码我们可以看到是对输入做了一个判断，判断输入的前五个字符是否为simon。<br />我们先尝试一下正确输入，结果显示：<br />![](https://img-blog.csdnimg.cn/20210309115414777.png#crop=0&crop=0&crop=1&crop=1&id=WJlUD&originHeight=140&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

通过代码还可以看到定义了一个数组buf[20],尝试一下溢出提权<br />![](https://img-blog.csdnimg.cn/20210309115419652.png#crop=0&crop=0&crop=1&crop=1&id=Fjet5&originHeight=84&originWidth=277&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://img-blog.csdnimg.cn/202103091154258.png#crop=0&crop=0&crop=1&crop=1&id=bVFky&originHeight=110&originWidth=461&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

成功

访问一下flag.txt文件，成功。<br />![](https://img-blog.csdnimg.cn/2021030911542978.png#crop=0&crop=0&crop=1&crop=1&id=cEopc&originHeight=70&originWidth=433&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
