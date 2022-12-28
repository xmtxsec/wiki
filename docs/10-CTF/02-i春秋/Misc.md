
# 1、你好，i春秋
进入i春秋公众号发送“不愿意”即可得到flag。


# 2、Hello World!
flag就在题目中，按照flag的格式找出提交即可。


# 3、Vape Nation
将得到的图片下载，使用StegSolve工具打开依次翻阅即可看到flag
![image.png](./assets/1655864730728-48a60e8e-c75c-40cb-b74d-b212d7f71569.png)


# 4、签到题
进入i春秋公众号发送“百度杯么么哒”即可得到flag。


# 5、那些年我追过的贝丝
通过题目我们就可以知道这是一个base编码，而且题目中说明2的6次方，那么就是2^6=64，可以确定是base64加密了。解密即可得到flag。


# 6、我要变成一只程序猿
下载运行提供的代码，将题目给出的参数输入即可得到flag。


# 7、剧情大反转
通过给出的题目名字和题目内容，就可以想到将给出的内容倒过来即是flag。


# 8、challenge
得到题目没有思路，猜想是进制转换，经过尝试为十六进制转换.
![image.png](./assets/1655864744063-d87abae8-dd59-4ada-b664-d2c0a5eb2b37.png)


# 9、传说中的签到题
进入i春秋公众号发送“答案在哪里”，会回复“我存在，你深深的脑海中~ ~ 就是不在这”，没错答案就是在你的脑海中，就是那两个字，什么？就是什么啊！。


# 10、听说是rc4算法（暂定）
```python
import random, base64

import hashlib

def crypt(data,key) :

    s = [0] * 256

    for i in range(256) :

        s[i] = i

    print(s)

    j = 0

    for i in range(256) :

        j = (j + s[i] + key[i % len(key)]) % 256

        print(j)

        s[i], s[j] = s[j], s[i]



    i = 0

    j = 0

    res = ""

    for c in data :

        i = (i + 1) % 256

        j = (j + s[i]) % 256

        s[i], s[j] = s[j], s[i]

        res = res + chr(c ^ s[(s[i] + s[j]) % 256])



    return res



def tdecode(data ,key) :

    data = base64.b64decode(data)

    salt = data[:16]

    return crypt(data[16:] ,hashlib.sha1(bytes(key,encoding = "utf8") + salt).digest())



if __name__ =='__main__':

    key = "welcometoicqedu"

    data = "UUyFTj8PCzF6geFn6xgBOYSvVTrbpNU4OF9db9wMcPD1yDbaJw=="

    print(tdecode(data,key))
```


# 11、try to find the flag
将文件下在使用文档编辑器打开搜索“flag”。即可得到flag。


# 12、表姐家的签到题
答案就已经在题目中了，加上flag的格式即可。


# 13、泄露的数据
题目中说“某个数据库的泄漏的重要数据”，数据库，那么数据库的加密就是MD5了，MD5解密即可。


# 14、考眼力
题目意思很明显了“考眼力”，而且根据前四个字母“gmbh”可以发现和“flag”每个祖母的位数都相差一位，那么将题目给gmbh{4d850d5c3c2756f67b91cbe8f046eebd}字母倒退一位即可的到flag。


# 15、flag格式
答案就在题目中提交即可。


# 16、小可爱
看到题目的提示“txt文件内容即为密码”，密码？什么东西需要密码呢？压缩包解压需要密码，那么该文件可能实际上是一个压缩包，将后缀改为zip进行解密发现需要密码，使用Ziperello破解得到密码
![image.png](./assets/1655864765357-c3bc06bb-5069-4378-b707-d9cab8890021.png)

解压后得到一张破碎的二维码图片。将二维码拼接扫码即得到flag。
![image.png](./assets/1655864773551-2f1fa90d-e678-48b4-aa3b-7d3e955f5037.png)


# 17、签到题
flag就在题目中，提交即可。


# 18、所以这是13点吗
题目已经提示了“13”，而且观察可以发现f“flag”每个字母往后数13个即是“synt”。可以采用[Rot13密码](http://ctf.ssleye.com/rot13.html)来解密。


# 19、嘀嘀嘀
很明显这是摩尔斯密码了。使用[摩尔斯电码解密解密](http://ctf.ssleye.com/morse.html)即可得到flag。


# 20、山岚
题目就给了一段密文，很明显解密即是flag。题目名字“山岚”谐音“栅栏”，使用[栅栏密码](http://ctf.ssleye.com/railfence.html)解密或者使用CTFcrackTools.jar工具解密即可得到flag。
![image.png](./assets/1655864785631-09d0e219-2128-443f-bdb5-99c927ede43e.png)


# 21、签到题2
看题目大致可知为unicode加密，使用CTFcrackTools.jar工具解密即可。
![image.png](./assets/1655864791692-7efec6b7-5036-42c3-80fd-6ad179493610.png)


# 22、xx
通过题目和提供的密文可以知道是XXencode加密，使用[XXencode加密](http://ctf.ssleye.com/xxencode.html)即可得到flag。


# 23、贝丝家族
通过题目就可以看出是与base加密有关，贝丝是base64，那么贝丝的表妹那就是base32了。


# 24、敲击
敲击，那就是和键盘有关了，通过题目给的字母段，在键盘上敲击可以发现可以描绘成一个个字母，即使flag。


# 25、签到题3
仔细观察可以发现长度为32为，猜测为MD5，加密即可得到flag。


# 26、小常识
按照题目要求转换即可。


# 27、回归原始
这一看就知道是二进制转换了，转换为字符串即是flag。


# 28、一个16岁的少年
贝丝远房的表亲，一个16岁的少年，那当然是base16了。


# 29、藏在邮件头里的秘密
可打印字符编码(Quoted_Printable),[在线解码](http://ctf.ssleye.com/quoted.html)即得到flag


# 30、吃货
根据题目“吃货”和题目给出的密文特征可以得知这是培根密码。解密即可得到flag。


# 31、misc1
将图片下载下来，使用文档编辑器打开查找flag即可得到flag。


# 32、misc2
啊这，熟悉的人一看就可以知道这是猪圈密码。


# 33、misc3
题目已经说了“键盘敲击密码”在键盘上找到对应字母即可得到flag。


# 34、福尔摩斯
通过题目名称我们就可以得知是福尔摩斯密码，解密即可得到flag。


# 35、+——+
通过题目和密文形式，可以看判断出是非常规加密。使用[Brainfuck](http://ctf.ssleye.com/brain.html)解密即可得到flag。


# 36、misc1-纵横四海
将下载的附件解压，打开可以发现里面的每一个文档里面都有一个字母，而且前四个组合起来刚好是flag，所以将每个文件里面的字母按顺序拼凑起来即可得到flag。
```
# 在终端输入下面命令即可将每个文件中的字母合并到一个文件中
type dabiaojie* >>flag.txt
```

然后使用notepad++操作即可。


# 37、misc2-对错
打开附件看到二进制数字，首先想到的就是二进制转换为字符串，转换后即使flag。


# 38、misc3-枯竭
下载附件发现是一个压缩包，解压需要密码使用Ziperello.exe工具爆破即可得到密码，解压得到flag。


# 39、misc 1 祸起北荒
flag{百度杯之让红包飞}


# 40、misc 2 上古神器  上古神器
查看题目可以得到关键信息“波利比奥斯棋盘”，在百度搜索波利比奥斯棋盘密码表，对照解密即可得到flag。先横后竖。


# 41、misc 3 此去经年
将题目中给的base64解密进行提交发现是失败，百度一下这首诗
![image.png](./assets/1655864823222-cc197604-0fc6-4569-9483-970ace701ad4.png)
“十月蟋蟀入我床下”即是flag。


# 42、
```php
include "flag.php";
$a = @$_REQUEST['hello'];
if(!preg_match('/^\w*$/',$a )){
  die('ERROR');
}
eval("var_dump($$a);");
show_source(__FILE__);
?>
```
