
## 0X01 什么是File协议

**中文意思**：本地文件传输协议<br />File协议主要用于**访问本地计算机中的文件**，就如同在Windows资源管理器中打开文件一样。


## 0X02 如何使用File

file协议的基本格式
```
file:///文件路径
```

比如需要打开E盘下txt目录中的index.txt，那么在资源管理器或者浏览器地址栏中输入：
```
file:///E:/txt/index.txt
```

用file:///+文件地址，其实等价于文件的地址。即：
```
file:///E:/txt/index.txt
```
等价于
```
E:/txt/index.txt
```


## 0X03URI中为什么本地文件file后面跟三个斜杠？

URI的结构为：
```
scheme:[//[user:password@]host[:port]][/]path[?query][#fragment]
```

如果有host，前面需要加//，因此对于http或https等网络地址来说会写成：
```
https://blog.csdn.net/lishanleilixin/article/category/7191777
```

这样看上去很自然。如果是文件的话，文件没有host，所以中间的host部分就不要了，就变成了:
```
file:///lishanleilixin/article/category/7191777
```

因为如果没有host的话，第一个 [ ] 的内容就不存在了，这种统一的写法有一个标准叫CURIE。


## 0X04 浏览器通过file:///访问文件和http://访问文件的区别

file协议只能在本地访问， file无法实现跨域，file协议对应有一个类似http的远程访问，就是ftp协议， 即文件传输协议。本地搭建http服务器开放端口后他人也可以通过http访问到你电脑中的文件，但是file协议做不到。

http访问本地的html文件， 相当于将本机作为了一台http服务器，然后通过主机访问的是你自己电脑上的本地服务器，再通过http服务器去访问你本机的文件资源。断网就打不开了。
