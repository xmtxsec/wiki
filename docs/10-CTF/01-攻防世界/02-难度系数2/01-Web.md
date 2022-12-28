# GFSJ0481-xff_referer

**题目**

X老师告诉小宁其实xff和referer是可以伪造的。



**解题**
![img](../../../_img/01-Web/1657588803856-a9be5da7-5bc4-4bf4-ab8c-5d2c9abe6183.png)



使用burpsuite抓包如图修改
不知道什么是X-Forwarded-For（[点这里](https://blog.csdn.net/qq_44276741/article/details/120909081)）
![img](../../../_img/01-Web/1657588805174-ff3b72b2-1c6b-4fee-b316-f8811ffa82b2.png)



题目要求必须来自http:s://www.google.com



如图修改即可
不知道什么是Referer（[点这里](https://blog.csdn.net/qq_44276741/article/details/120909081)）
![img](../../../_img/01-Web/1657588805147-a06831c3-92d2-426f-8563-abb32e9e5587.png)



# GFSJ0483-webshell

**题目**

小宁百度了php一句话,觉着很有意思,并且把它放在index.php里。



**解题**
![img](../../../_img/01-Web/1657588803853-4ae2cb34-dd08-48e1-a3f2-8f18d595c004.png)



直接尝试使用蚁剑链接
![img](../../../_img/01-Web/1657588803847-6f3047d8-fb2b-407d-ba39-eefbfe5f1b6a.png)



成功得到flag
![img](../../../_img/01-Web/1657588804632-2572e115-5fe9-4c3c-8c8d-58f31abcac0e.png)



# GFSJ0484-command_execution

**题目**

小宁写了个ping功能,但没有写waf,X老师告诉她这是非常危险的，你知道为什么吗。



**解题**

![img](../../../_img/01-Web/1657588804633-e6555093-15a4-4f7c-99ac-0d3ba47b7baf.png)



这一看就是命令执行了，先查找flag的位置

```plain
127.0.0.1 | find / -name "flag.*"
```



![img](../../../_img/01-Web/1657588805235-0b9e5ff8-4d2e-4991-8687-0186e54f82a5.png)



然后查看flag

```plain
127.0.0.1 | cat /home/flag.txt
```



![img](../../../_img/01-Web/1657588805412-33a7cbca-ef11-43ca-9d37-fe55fe2e8bc5.png)