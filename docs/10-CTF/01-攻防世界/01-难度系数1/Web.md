
# GFSJ0162-Training-WWW-Robots
**题目：**<br />在这个小小的训练挑战中，您将了解[Robots_exclusion_standard](http://en.wikipedia.org/wiki/Robots_exclusion_standard)。<br />网络爬虫使用 robots.txt 文件来检查是否允许他们对您的网站或仅部分网站进行爬网和索引。<br />有时这些文件会显示目录结构，而不是保护内容不被抓取。<br />享受！
![image.png](./assets/1657530295867-69f6eea5-330a-4094-8f99-3bfee819b6a7.png)

**解答：**<br />在题目中提到了[robots.txt](https://baike.baidu.com/item/robots%E5%8D%8F%E8%AE%AE/2483797?fromtitle=robots.txt&fromid=9518761&fr=aladdin)
robots协议也称爬虫协议、爬虫规则等,是指网站可建立一个robots.txt文件来告诉搜索引擎哪些页面可以抓取,哪些页面不能抓取,而搜索引擎则通过读取robots.txt文件来识别这个页面是否允许被抓取。但是,这个robots协议不是防火墙,也没有强制执行力,搜索引擎完全可以忽视robots.txt文件去抓取网页的快照。如果想单独定义搜索引擎的漫游器访问子目录时的行为，那么可以将自定的设置合并到根目录下的robots.txt，或者使用robots[元数据](https://baike.baidu.com/item/%E5%85%83%E6%95%B0%E6%8D%AE)（Metadata，又称元数据）。

访问robots.txt文件<br />http://61.147.171.105:51401/robots.txt
![image.png](./assets/1657530312306-db8cd6cb-3d49-4aeb-a831-135d443bcbab.png)

在robots.txt文件中看到了fl0g.php，尝试访问。<br />http://61.147.171.105:51401/fl0g.php

得到flag：cyberpeace{5aeed419107c31b3a716cc0d04c85986}


# GFSJ0474-view_soure
**题目**<br />X老师让小宁同学查看一个网页的源代码，但小宁同学发现鼠标右键好像不管用了。

**解题**<br />题目中说鼠标右键用不了，那么我们在什么情况下浏览网页需要用到鼠标右键呢？在需要查看网页源代码的时候，那就想方法查看源代码。

按F12，选择Elements，即得到flag<br />![](https://img-blog.csdnimg.cn/aed046a01f91491191be29eb8b87a044.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bO05ZCM5a24,size_15,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=NPSRo&originHeight=227&originWidth=451&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

# GFSJ0476-robots
不知道什么是robots？（[点这里](https://www.seobti.com/690.html)）

**解答：**题目的名称是robots，那么就不难想到这题和robots有关

打开环境访问robot.txt文件
```
http://111.200.241.244:58894/robots.txt
```

![](https://img-blog.csdnimg.cn/ded206cf60dd40f8bb8b1e13bdf14c11.png#crop=0&crop=0&crop=1&crop=1&id=Vader&originHeight=176&originWidth=508&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

然后访问f1ag_1s_h3re.php，成功得到flag
```
http://111.200.241.244:58894/f1ag_1s_h3re.php
```
![](https://img-blog.csdnimg.cn/b20ffcfa82604ea08e10f2484d2e3ded.png#crop=0&crop=0&crop=1&crop=1&id=Tqun0&originHeight=145&originWidth=733&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# GFSJ0477-Backup
**题目：**<br />X老师忘记删除备份文件，他派小宁同学去把备份文件找出来,一起来帮小宁同学吧！

**解题：**<br />题目提示“你知道index.php的备份文件名吗？“说明我们需要到备份文件

常见的备份文件后缀有：[点击查看](https://blog.csdn.net/bolibei0/article/details/2882921)
经过尝试得知后缀为.bak，成功得到flag<br />![](https://img-blog.csdnimg.cn/bddef10b83904a66bf791c04e1992eca.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bO05ZCM5a24,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=AoKUS&originHeight=564&originWidth=873&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# GFSJ0478-cookie
**题目**<br />X老师告诉小宁他在cookie里放了些东西，小宁疑惑地想：‘这是夹心饼干的意思吗？’

**解题**<br />题目的意思很明显，查看cookie，在cookie中看到了一个cookie.php的文件<br />![](https://img-blog.csdnimg.cn/01f6cd48d2b84b67842400fad7c652f2.png#crop=0&crop=0&crop=1&crop=1&id=MBDeA&originHeight=191&originWidth=926&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

访问该文件
```
http://111.200.241.244:53143/cookie.php
```
![](https://img-blog.csdnimg.cn/29b2f09f485f4b1785ee001e82efa85b.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bO05ZCM5a24,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=uymee&originHeight=227&originWidth=638&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

要我们请参阅http响应，使用burpsuite抓包查看响应得到flag<br />![](https://img-blog.csdnimg.cn/d6d05679703240fcbc9ea1285e442d33.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bO05ZCM5a24,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=Me2Kx&originHeight=295&originWidth=1196&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# GFSJ0479-disabled_button
**题目**<br />X老师今天上课讲了前端知识，然后给了大家一个不能按的按钮，小宁惊奇地发现这个按钮按不下去，到底怎么才能按下去呢？

**解题**<br />这就是一个前端的问题，删除图是部分即可点击查看flag<br />![](https://img-blog.csdnimg.cn/0ae37266460f483a9f8a3bebe5e889ad.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bO05ZCM5a24,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=bL14q&originHeight=431&originWidth=709&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# GFSJ-0482weak_auth
**题目**<br />小宁写了一个登陆验证页面，随手就设了一个密码。

**解题**<br />这一看就想到弱密码了，直接admin  123456 得到flag<br />![](https://img-blog.csdnimg.cn/e06622b62b8b4854ae86531c49e7a660.png#crop=0&crop=0&crop=1&crop=1&id=VKJLz&originHeight=178&originWidth=654&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# GFSJ0485-simple_php
**题目**<br />小宁听说php是最好的语言,于是她简单学习之后写了几行php代码。

**解题**<br />代码审计
```
<?php
show_source(__FILE__);
include("config.php");
$a=@$_GET['a'];
$b=@$_GET['b'];
if($a==0 and $a){
    echo $flag1;
}
if(is_numeric($b)){
    exit();
}
if($b>1234){
    echo $flag2;
}
?>
```

首先使用了include()函数来包含config.php文件，然后定义了两个变量a和b，使用if语句判断a等于0 给出flag1，b不为数字但是b要大于1234，给出flag2。

**函数说明**
```
include （或 require）语句会获取指定文件中存在的所有文本/代码/标记，并复制到使用 include 语句的文件中。
```

```
is_numeric() 函数用于检测变量是否为数字或数字字符串。如果指定的变量是数字和数字字符串则返回 TRUE，否则返回 FALSE，注意浮点型返回 1，即 TRUE。

语法
bool is_numeric ( mixed $var )
$var：要检测的变量。
```

变量b要求其不为数字类型并且还要大于1234，这里有一个知识点[php弱类型比较](https://blog.csdn.net/zz_Caleb/article/details/88068150)

按照以下输入，得到flag
```
http://111.200.241.244:59927/index.php?a=0 and b=1235a
```


# GFSJ0475-get_post
**题目**<br />X老师告诉小宁同学HTTP通常使用两种请求方法，你知道是哪两种吗？

**解题**<br />题目要我们使用GET方式提交一个名为a,值为1的变量。<br />![](https://img-blog.csdnimg.cn/cf17babbcfff4875b0bacc1c206de642.png#crop=0&crop=0&crop=1&crop=1&id=MiUB1&originHeight=86&originWidth=845&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

直接修改URL
```
http://111.200.241.244:51078/?a=1
```

![](https://img-blog.csdnimg.cn/2ffcf611e0df4546a7311efbf40f31af.png#crop=0&crop=0&crop=1&crop=1&id=ssxfe&originHeight=124&originWidth=847&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

然后又要我们再以POST方式随便提交一个名为b,值为2的变量，使用hackbar<br />![](https://img-blog.csdnimg.cn/b377ff2ae3fb42f0ae9cb8c1a74a044d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bO05ZCM5a24,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=vi1Uq&originHeight=228&originWidth=668&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

成功得到flag<br />![](https://img-blog.csdnimg.cn/9f97c6f88ddc49029a73acfc5a9478a3.png#crop=0&crop=0&crop=1&crop=1&id=DGOjo&originHeight=182&originWidth=822&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


