# 1、Enhanced BurpGPT 简介

Enhanced BurpGPT 是一个 Burp Suite 插件，它能帮助你使用 AI（人工智能）来分析 Web 应用的安全问题。简单来说，当你测试网站时，你可以在指定的请求响应对，点击send to gpt，交由AI分析，找出潜在的安全漏洞。

**GitHub地址：**https://github.com/yxdm02/EnhancedBurpGPT

**优点：**

- 自动化分析，节省时间
- AI 辅助发现安全问题
- 适合新手学习安全测试
- 提供详细的分析报告
- 支持中文输出
- 支持自定义prompt



# 2、安装插件

## 2.1、安装Jython

1、下载Jython：https://www.jython.org/download.html

![image-20250305114125345](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503051141632.png)



2、打开命令行执行下面命令会弹出安装界面

```
java -jar jython-installer-2.7.4.jar
```

![image-20250305114251202](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503051142244.png)

![image-20250305114344678](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503051143724.png)



3、安装完成后配置环境变量

![image-20250305121019404](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503051210449.png)

![image-20250305121041918](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503051210961.png)

![image-20250305121133076](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503051211143.png)



4、在命令行输入 jython，安装成功

![image-20250305121214440](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503051212484.png)



## 2.2、配置Burp Suite

1、打开 Burp Suite 点击`Extender` 标签，选择 `Options` 子标签，在 `Python Environment` 部分，选择你的 Jython jar 文件路径

![image-20250305121506915](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503051215973.png)



## 2.3、安装插件

1、在 Burp Suite 中，点击 `Extender` 标签，选择 `Extensions` 子标签，点击 `Add` 按钮，选择 `Extension Type` 为 `Python`，选择下载的 `burpGTPv1.py` 文件点击 `Next`，等待加载完成

**注意：**插件存放的目录不要用中文，会导入失败。

![image-20250305122038978](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503051220114.png)



## 2.4、配置插件

### 2.4.1、基础配置

1、点击 `GPT Analysis` 标签`Configuration`标签页中填写相关信息

```
API URL：填写API URL（例如：https://api.openai.com/v1/chat/completions）
API Key：填写你的API Key
Model：选择或输入要使用的Model（例如：gpt-3.5-turbo）
```

**注意：**获取模型默认访问的是/v1/models，对话访问的是/v1/chat/completions

![image-20250305135500698](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503051355779.png)



### 2.4.2、高级配置

1、设置超时和长度限制

```
Timeout：建议设置 60 秒
Max Request Length：建议设置 1000
Max Response Length：建议设置 2000
```

![image-20250305135617376](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503051356428.png)



2、自定义提示模板，可以使用默认模板，也可以根据需要修改模板

```
支持的变量：
	{URL}：目标网址
	{METHOD}：请求方法
	{REQUEST}：请求内容
	{RESPONSE}：响应内容
```

![image-20250305135728818](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503051357876.png)



# 3、使用方法

1、在 BP 的任意位置（如 Proxy、Repeater）右键点击请求选择 `Send to GPT`，进行分析。

![image-20250305141406536](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503051414615.png)



2、在 `GPT Analysis` 标签下的`Analysis Results`子标签页查看结果。

![image-20250305141801062](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503051418183.png)
