# 一、HTTP Basics

## 问题2

在下面的输入字段中输入您的姓名，然后按“Go！” 进行提交。服务器将接受请求，反转输入并将其显示回用户，说明处理HTTP请求的基础知识。



**解答：**

这里直接输入名字提交即可

![img](../../_img/0X02%20General/1666674880448-28218eba-544f-46a9-ae78-2208c3f5aa8f.png)



## 问题3

WebGoat 在本课中使用了哪种类型的 HTTP 命令，神奇的数字是什么。

![img](../../_img/0X02%20General/1666675097464-93db9558-7fdf-48c3-aa0d-27287a7eeefe.png)



**解答：**

在这里我们知道在前面我们提交的是POST数据包，所以第一个框框填入POST，第二个框框不知道答案。先随便填入一个数字。然后使用burpsuit进行抓包。

![img](../../_img/0X02%20General/1666675371000-ea080114-a41c-44ed-a83f-015b1b9bb6dd.png)



将请求体里面的11改成37后放包即可

![img](https://cdn.nlark.com/yuque/0/2022/png/21411821/1666675416380-8356ef24-f818-40c7-b235-b83e853ad195.png)



# 二、HTTP Proxies

## 问题6

如上所述设置拦截，然后单击提交按钮提交下面的表单/请求。当您的请求被截获（命中断点）时，请按如下方式对其进行修改。

- 将方法更改为 GET
- 添加标头“x-request-intercepted:true”
- 删除请求正文，改为发送“changeMe”作为查询字符串参数，并将值设Requests are tampered easy”（不带单引号）

然后让请求继续通过（通过点击播放按钮）



**解答：**

点击提交并使用burpsuite抓包

![img](https://cdn.nlark.com/yuque/0/2022/png/21411821/1666676349821-c0c25fbe-4664-46f6-996e-0da6f101b1b2.png)



按照题目要求修改数据包如图所示

![img](https://cdn.nlark.com/yuque/0/2022/png/21411821/1666676443186-1df78782-7baf-4b65-b1d6-044e656c6795.png)



放包即可通过

![img](https://cdn.nlark.com/yuque/0/2022/png/21411821/1666676470285-f54fc1e4-52dc-49c7-abc7-acf2f13c526c.png)



# 三、Developer Tools

## 问题4

让我们试试吧。在开发工具中使用控制台，并调用 javascript 函数`**webgoat.customjs.phoneHome()**`

您应该会在控制台中获得响应。您的结果应如下所示：将随机数粘贴到下面的文本字段中。（确保您获得最新的数字，因为每次调用该函数时都会随机生成该数字）phone home said {"lessonCompleted:true, … ,"output":"phone home response is…"



**解答：**

打开谷歌浏览器的开发者模式，按F12，找到控制台输入题目中的函数后回车：

![img](https://cdn.nlark.com/yuque/0/2022/png/21411821/1666676752857-c6dbfcd9-5ff1-4cfc-819e-04fd8853677f.png)



将数字提交即可

![img](https://cdn.nlark.com/yuque/0/2022/png/21411821/1666676858334-a1065662-5245-4d25-a168-1ec58fd6b591.png)



## 问题6

在这个作业中，您需要找到一个特定的 HTTP 请求并从中读取一个随机数。开始单击第一个按钮，这将生成一个 HTTP 请求。尝试查找特定的 HTTP 请求。请求应包含一个字段：networkNum：将之后显示的数字复制到下面的输入字段中，然后单击检查按钮。



**解答：**

点击按钮“go”并使用burpsuite抓包，找到包含networkNum关键词的数据包

![img](https://cdn.nlark.com/yuque/0/2022/png/21411821/1666677049394-07011cc2-39df-4266-9bbb-81f93e53a870.png)



防包后，将数值复制提交即可

![img](https://cdn.nlark.com/yuque/0/2022/png/21411821/1666677093091-cb434f85-0bce-4b33-af2f-bb01163f5123.png)



# 四、CIA Triad

## 问题5

这里是四个有关安全知识的选择题，答题即可



**解答：C、A、D、B**



# 五、Crypto Basics

## 问题2

现在假设您已经截获了以下标头：

Basic YWRtaW4xOnNlY3JldA==

用户名是什么，密码是什么？



**解答：**

将Base64进行解码即可

![img](https://cdn.nlark.com/yuque/0/2022/png/21411821/1666677649438-e1692b7a-c113-4c71-94a0-d9174b27fbf6.png)

![img](https://cdn.nlark.com/yuque/0/2022/png/21411821/1666677738188-6fb72c15-34d1-474c-bc98-7b2136560367.png)



## 问题3

假设您发现数据库密码编码为 {xor}Oz4rPj0+LDovPiwsKDAtOw==



解答：

http://www.sysman.nl/wasdecoder/

将密文复制进去解码即可

![img](https://cdn.nlark.com/yuque/0/2022/png/21411821/1666677922571-38678b4e-0e6a-46c4-a90f-d6f11eb11656.png)

![img](https://cdn.nlark.com/yuque/0/2022/png/21411821/1666677945554-3b205054-fbf8-44dd-936c-e182ceb2f882.png)



## 问题4

现在让我们看看您是否可以找到哪些密码与哪些普通（无盐）哈希值匹配。



**解答：**

https://www.cmd5.com/

将密文复制解码即可

![img](https://cdn.nlark.com/yuque/0/2022/png/21411821/1666678250342-f092a7c7-4396-41c2-95d2-a46f0ea7a1b5.png)

![img](https://cdn.nlark.com/yuque/0/2022/png/21411821/1666678277276-0df48003-2d80-4fe0-b862-1e7497e7e677.png)

![img](https://cdn.nlark.com/yuque/0/2022/png/21411821/1666678293611-c6a27367-def2-4f50-af7a-81a7ae071b46.png)



## 问题6

这是一个简单的任务。将向您发送一个私有 RSA 密钥。将 RSA 密钥的模数确定为十六进制字符串，并使用该密钥计算该十六进制字符串的签名。该练习需要一些使用开放SSL的经验。您可以在互联网上搜索有用的命令和/或使用“提示”按钮获取一些提示。



**解答：**

在kali中创建一个文件夹将得到的密钥文件写入其中

![img](https://cdn.nlark.com/yuque/0/2022/png/21411821/1666679569770-2f8048e8-af55-4ef9-bfdc-b2a6992426af.png)



使用Kali自带的openssl工具进行破解

```plain
openssl rsa -in key -modulus
```

![img](https://cdn.nlark.com/yuque/0/2022/png/21411821/1666679718297-5ea31baf-38e6-49f7-b30b-65f12e35338a.png)



然后再将得到的模进行签名

```plain
echo -n "90B80182D3A1C2B42B4E1DBDD0FCDC1C7C361D14FC3917921B5196EC6A0A7B4805BFB175D0C414580540B8B4AB052FD5C16DF2D1327E1038D5C19B150A9E99129800B26410EBA411EE379946D37EDE450B6BB9DDBEA495265FBA294645D5890BB1582D602EC5E7A44818085AEAA608F33AC31B3A639E6E89F823D40888B4C29B01C0E9CCB89995B6ECD990617E682B9FD613BBB69091467ACD5B287CAED51B56F2CEC0ED5FBA08EEF2F85A144D1291E86CCF6CF254D700ABF2BD1EEE04F28B50E8B3F08CA44273063E0EA849A1DA354F07BBA2D3994AE7C5277D1A5D6758679B537866C1402DECEA287817C259B0CF91B357C69F8DABAA07AB1DE52D61EACFAD" | openssl dgst -sign key -sha256 -out key2.txt
base64 key2.txt > key3
```



将得到的模和key3文件里面的签名进行提交即可

![img](https://cdn.nlark.com/yuque/0/2022/png/21411821/1666679801194-9209624f-0b46-48c3-879f-c07e5a38f93c.png)

![img](https://cdn.nlark.com/yuque/0/2022/png/21411821/1666679867150-301c4fe0-bc09-47d5-9690-ef4cb5595f0b.png)



## 问题8

暂未解决



# 六、Writing new lesson

## 问题6

这一题就是给我们讲解如何使用WebGoat来自己创建题目。