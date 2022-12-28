
# 一、介绍
ffuf是一款 [Go](https://www.codercto.com/topics/6127.html) 语言编写的高速Web Fuzzer工具，该项目深受大型项目 [gobuster](https://github.com/OJ/gobuster) 和 [wfuzz](https://github.com/xmendez/wfuzz) 的启发。特点：一个字，快！允许fuzz HTTP header值，POST数据和URL的不同部分，包括GET数名称和值；支持静默模式（-s）；模块化架构；易于添加的过滤器和匹配器。


# 二、安装

1. 通过下载二进制兼容版本进行安装[下载地址](https://github.com/ffuf/ffuf/releases)
2. 也可以使用go get命令安装。
```
安装go环境
sudo apt install golang-go
sudo apt install gccgo-go

安装FUFF
go get -u github.com/ffuf/ffuf
apt install ffuf
```

3. 在Kali Linux中使用apt-get install ffuf安装，目前自带。


# 三、命令使用
**查看帮助：-h**
![image.png](./assets/1671676789400-df0f8b59-4c60-40d5-abe0-bd148840d9c5.png)


## 3.1、基本攻击
-u设置目标 ,-w进行设置字典
```
./ffuf -u host/FUZZ -w dict.txt
```
![image.png](./assets/1671677216151-cc5f3a75-39b1-452b-8fd8-fd4e33d3985d.png)


## 3.2、多字典攻击
字典后面跟一个冒号后面再跟一个标识
```
./ffuf --u host/FUZZ1/FUZZ2 -w dict.txt:FUZZ1 -w dict.txt:FUZZ2
```
![image.png](./assets/1671677285982-71d4c8f7-9925-46c5-9a25-0b34f3e09a19.png)


## 3.3、添加COOKIE
```
-b COOKIE_VALUE
```


## 3.4、静默模式
-s 其实就是不打印附加信息 只输出结果
![image.png](./assets/1671677424406-f7feab8f-6db4-4337-803e-3a86265f464d.png)


## 3.5、指定拓展名
-e 主要就是拓展FUZZ关键字每个加个x
![image.png](./assets/1671677481709-a9187719-6000-4375-ad81-09183a249e92.png)


## 3.6、爆破POST请求 (多参数)
主要用到的几个参数

1. -request 原始的http请求文件
2. -request-proto 派生http请求 默认为https
3. -mode 设置爆破模式 默认为clusterbomb

如若不懂 看下./ffuf -h就可以了

先用BURP抓取原始数据
![image.png](./assets/1671677555176-349aa298-0f55-4b9d-8ebf-e984d5f488f5.png)

然后进行测试
```
./ffuf -request test.txt -request-proto http -mode clusterbomb -w user.txt:FUZZ1 -w pass.txt:FUZZ2
```

![image.png](./assets/1671677556079-1a849426-6db2-44a1-9914-9eb9d50a5ee5.png)
可以看出来size完全不一样，后期我们可以过滤这种没用的size


## 3.7、匹配

### 匹配http状态码
```
-mc status-code
```

![image.png](./assets/1671677555535-9176b6fa-b83c-4ab0-b455-234fe4c9a7c6.png)

### 匹配lines
```
-ml lines
```

![image.png](./assets/1671677555590-a7d5157d-33b7-4bd9-9d7e-28ece982d352.png)

### 匹配字数
```
-mw 字数
```

![image.png](./assets/1671677555644-dd6afe99-3166-4c91-b04b-5cc0fb351f6b.png)

### 匹配大小
```
-ms size
```

![image.png](./assets/1671677556233-4e69b3cb-fd89-491e-83df-80769d42d2ce.png)

### 匹配正则
假如我们挖到了一处任意文件读取，可以使用来fuzz
```
-mr value
```

![image.png](./assets/1671677557114-8b2a3752-a317-4be2-9b81-4dfe656aa60f.png)

## 3.8、筛选

### 过滤http状态码
```
-fc status-code
```

![image.png](./assets/1671677557095-d1d23770-903a-4f62-b659-5bcfd7db85b3.png)

### 过滤lines
```
-fl lines
```

![image.png](./assets/1671677557542-abe2d529-a91f-4fa8-97c4-df12576c6b12.png)

### 过滤长度
```
-fs size
```

![image.png](./assets/1671677557691-059dd141-51d6-4d98-9639-c47874ef9c70.png)

### 过滤字数
```
-fw words
```


### 过滤正则
```
-fr value
```



## 3.9、常用

### 颜色
-c 增加颜色

![image.png](./assets/1671677558133-9a5afdf4-8374-4b18-a74a-70a4120e202c.png)
可以明显看见status加了颜色

### 任务的最大时间
max-time 在有限的时间内完成测试

### 延迟
-p 延迟多长时间

### 详细模式
-v

![image.png](./assets/1671677558751-81b36c48-5ea7-4e75-b325-0456999f283c.png)

### 线程
-t

### 重播代理
-replay-proxy http://127.0.0.1:8080

## 3.10、输出
```
HTML: -o file.html -of html
CSV: -o file.csv -of csv
ALL: -o output/file -of all
```


## 3.11、实战例子
这是一个SRC的子域 查询信息的一个接口 没有认证 可以遍历

![image.png](./assets/1671677559125-87eb0b0a-7b49-4a88-9665-bf7551be3441.png)
