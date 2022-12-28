
# Sqli-labs-Less-19
使用admin进行登录<br />1.登录成功显示的是Referer信息<br />2.登录失败是没有回显信息的<br />![](https://img-blog.csdnimg.cn/20200720111451472.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=jXs2B&originHeight=252&originWidth=628&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**我们可以利用这个回显和报错注入来进行注入，**修改Referer来进行注入，使用burp抓包<br />![](https://img-blog.csdnimg.cn/20200720111653181.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=ULW18&originHeight=372&originWidth=558&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看当前数据库**<br />' or updatexml(1,concat(0x7e,(select database()),0x7e),1) and '1'='1<br />![](https://img-blog.csdnimg.cn/20200720112056727.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=MPviE&originHeight=553&originWidth=1660&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
以下步骤与Sqli-labs-Less-18相同不进行详细叙述<br />注意：<br />Sqli-labs-Less-19中是修改Referer来进行注入<br />Sqli-labs-Less-18中是修改User-Agent来进行注入
