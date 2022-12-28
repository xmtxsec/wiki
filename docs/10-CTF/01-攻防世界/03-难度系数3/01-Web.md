# GFSJ0408-simple_js

**题目**

小宁发现了一个网页，但却一直输不对密码。(Flag格式为 Cyberpeace{xxxxxxxxx} )



**解题**

随便输入密码，显示FAUX PASSWORD HAHA
![img](../../../_img/01-Web/1657588946045-a08c4621-ac9b-422d-8424-7fde434c6cb9.png)



查看网页源码
![img](../../../_img/01-Web/1657588946106-ff93270a-c8c8-40cf-9d07-65c49dd63559.png)



真正的密码是位于 fromCharCode



先将字符串用python处理一下，得到数组
![img](../../../_img/01-Web/1657588945990-64cf01ca-804d-4d42-8f58-07d034eead44.png)



```plain
55,56,54,79,115,69,114,116,107,49,50
```



将得到的数字分别进行ascii处理，可得到字符串
![img](../../../_img/01-Web/1657588946143-6bd1f65c-e0e1-4a48-92b3-038c9345b7a4.png)



即flag为Cyberpeace{786OsErtk12}