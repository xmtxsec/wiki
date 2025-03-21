# 1、概述

## 1.1、CAPEv2 Sandbox

恶意软件分析沙箱是在隔离的环境中运行未知且不受信任的应用程序或文件，并获取有关文件用途的信息。恶意软件沙箱是动态分析方法的实际应用，它不是静态分析二进制文件，而是实时执行和监控，可以获取有关恶意软件的更多详细信息。

CAPEv2 Sandbox 是一款用于自动分析可疑文件或恶意文件的开源沙箱。用于在隔离环境中执行恶意文件，同时检测其动态行为并收集取证据。

- **项目地址**：https://github.com/kevoreilly/CAPEv2
- **项目文档**：https://capev2.readthedocs.io/en/latest/ （好像是没有维护了）



## 1.2、FOGProject

FOG 是一个免费的开源克隆/成像/救援套件/库存管理系统。FOG 可用于对 Windows XP、Vista、Windows 7、Window 8/8.1 和 Windows 10、Windows 11 PC 进行映像，并使用 PXE、PartClone 和 Web GUI 将其连接在一起。包括内存和磁盘测试、磁盘擦除、av 扫描、任务调度、库存管理、作系统远程部署和软件包远程安装等功能。客户端计算机向 FOG 注册后，可以通过 Web GUI 触发功能。

- **项目地址**：https://github.com/FOGProject/fogproject
- **项目文档（新）**：https://docs.fogproject.org
- **项目文档（旧）**http://fogproject.org/wiki
- **论坛**：http://fogproject.org/forum



## 1.3、环境准备

|    机器用途    | 操作系统  |    IP 地址    |
| :------------: | :-------: | :-----------: |
| CAPEv2 Sandbox |  Ubuntu   | 192.168.10.33 |
|   FOGProject   |  Ubuntu   | 192.168.10.66 |
|   样本分析机   | Windows 7 | 192.168.10.99 |



**注意：**

- CAPE 服务器必须能够访问样本分析机
- FOG 服务器必须可以访问样本分析机
- FOG 服务器必须可以由 CAPE 服务器访问
- 将 FOG 设置为 DHCP 服务器
- 不得允许样本分析计算机访问网络中的其他计算机，CAPE 和 FOG 除外

------



# 2、安装CAPEv2 Sandbox

我在《搭建CAPEv2_Sandbox》中有过详细描述，下面只进行简要阐述。

**1、准备虚拟机环境**

部署环境为 VMWare 虚拟机中 Ubuntu22.04。虚拟机需要打开`Intel VT-x`！

Ubuntu ISO下载链接: https://pan.baidu.com/s/1ldG9TieC4jdF4SysUo0Nsw?pwd=fdb9

系统安装完后执行

```
sudo apt get update
sudp apt-get upgrade -y
sudo reboot
```



**2、卸载Ubuntu的自动更新**

自动更新经常在安装过程中突然占用dpkg导致安装出现报错！

```
sudo apt remove unattended-upgrades
```



**3、将Ubuntu默认的sh设置为bash**

Ubuntu 默认为`dash`，但安装脚本需要`bash`执行。以`bash xx.sh`的方式可能解决，但更稳妥的方式是修改默认`sh`。出现界面后选择No，回车即可。

```
sudo dpkg-reconfigure dash
```



**4、安装依赖**

在安装过程中缺乏下述依赖会报错。

```
sudo apt install mlocate
sudo apt install ninja-build
```



**5、安装CAPEv2**

建议：使用`git clone`来克隆项目到本地，不要使用下载`.zip`的方法。

脚本位置在`/CAPEv2/installer/cape2.sh`。

在执行脚本前建议使用`-h`查看帮助和用法信息，官方建议阅读脚本本身以了解它们的功能。（很重要）

```
sudo chmod a+x cape2.sh
sudo ./cape2.sh -h
```



修改`cape2.sh`中的 `NETWORK_IFACE`，`IFACE_IP` 和 `PASSWD` 变量以确保正确安装。

修改完后执行脚本`cape2.sh`，按照脚本说明正确选择你所需要的方式执行脚本执行（执行时间较长）。

我在这里执行的命令是（可能存在问题）：

```
./cape2.sh base cape | tee cape.log
```

- 脚本运行完后重启计算机。



计算器重启后分别执行以下指令，检查是否均有对应服务存在。

```
systemctl status cape.service
systemctl status cape-processor.service
systemctl status cape-web.service
systemctl status cape-rooter.service
```



安装依赖，在`/opt/CAPEv2/`目录下执行命令

```
poetry install

#执行以下命令，若得到对应输出，则安装成功。
poetry env list 
```



在`/opt/CAPEv2/`目录下切换到cape用户安装可选依赖

```
poetry run pip install -r extra/optional_dependencies.txt
```



在`/opt/CAPEv2/`目录下切换到cape用户执行`extra/libvirt_installer.sh`脚本

```
poetry run extra/libvirt_installer.sh
```



在`/opt/CAPEv2/`目录下切换到cape用户执行下面命令进行迁移操作

```
poetry run python3 web/manage.py migrate
```

------



# 3、安装FOG

**1、准备虚拟机环境**

部署环境为 VMWare 虚拟机中 Ubuntu22.04。

Ubuntu ISO下载链接: https://pan.baidu.com/s/1ldG9TieC4jdF4SysUo0Nsw?pwd=fdb9

系统安装完后执行

```
sudo apt get update
sudp apt-get upgrade -y
sudo reboot
```



**2、克隆FOG仓库并安装**

下载后，进入`fogproject/bin`目录并运行`installfog.sh`

```
git clone https://github.com/FOGProject/fogproject.git
```



脚本将询问几个问题。答案在很大程度上取决于你自己网络布局。

```
What version of Linux would you like to run the installation for?
您想为哪个版本的Linux运行安装？
这里选择你自己的系统版本对应的序号。
```

![image-20250320103746849](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211721691.png)



```
What type of installation would you like to do? [N/s (Normal/Storage)]
#您想进行哪种类型的安装？[N/s（正常/存储）]
这里选择Normal即可。
```

![image-20250320103902311](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211722080.png)



```
Would you like to change the default network interface from ens33?
#您想更改ens33的默认网络接口吗？
If you are not sure, select No. [y/N]
#如果您不确定，请选择“否”[y/N]
If you are not sure, select No. [y/N]
我这里是有两张网卡，我选择ens37
```

![image-20250320104405346](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211722937.png)



```
Would you like to setup a router address for the DHCP server? [Y/n]
#您想为DHCP服务器设置路由器地址吗？[是/否]
这里我们选择是

What is the IP address to be used for the router on the DHCP server? [192.168.20.254]
#DHCP服务器 路由使用的IP地址是什么？[192.168.20.254]
这里我设置为192.168.10.254
```

![image-20250320111233181](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211722283.png)



```
Would you like DHCP to handle DNS? [Y/n] 
#您希望DHCP处理DNS吗？

Would you like to use the FOG server for DHCP service? [y/N]
#您想使用FOG服务器进行DHCP服务吗？[y/N]

This version of FOG has internationalization support,
#此版本的FOG具有国际化支持
would you like to install the additional language packs? [y/N]
#您想安装其他语言包吗？[y/N]

Would you like to enable secure HTTPS on your FOG server? [y/N]
#您想在FOG服务器上启用安全的HTTPS吗？[y/N]

Which hostname would you like to use? Currently is: aa
#您想使用哪个主机名？目前是：aa 
Would you like to change it? If you are not sure, select No. [y/N]
#您想更改它吗？如果您不确定，请选择“否”[y/N]

Are you ok with sending this information? [Y/n]
#你同意发送这些信息吗？[是/否]
```

![image-20250320113651240](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211722374.png)



最后会有一个总结界面提供检查设置

![image-20250320115340851](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211722611.png)



FOG 安装几乎准备就绪，脚本将提示：

![image-20250320120227958](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211722790.png)



启动浏览器并点击蓝色按钮

![image-20250320120355706](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211722369.png)



然后返回控制台回车，安装完成

![image-20250320120643992](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211722545.png)

------



# 4、安装样本分析机

```
注意：
- 计算机必须能够联系 FOG 服务器
- 计算机必须能够联系 CAPE 服务器
- 计算机必须无法访问网络中的任何其他内容
```

我在《搭建CAPEv2_Sandbox》中有过详细描述，下面只进行简要阐述。



**1、准备虚拟机环境**

部署环境为 VMWare 虚拟机中 Windows 7。

Win7 ISO下载链接：https://pan.baidu.com/s/11zazsIWPe3fR3224DLITrw?pwd=6455



**2、安装Python**

只有 `32位(x86)` 版本的 Python3 是目前支持 Windows，因为分析器与低级 Windows 库交互的方式。使用 `64位`版本的 Python 将使 Windows 中的分析器崩溃。

- 安装Python的时候可能才能在无法安装的问题，这是因为系统缺少补丁，打上补丁即可。

  补丁下载链接：https://pan.baidu.com/s/1zCoF8wnNVFRbgYF9g-kl3w?pwd=js4m



**3、安装Pillow**

用于在分析过程中截取 Windows 桌面的屏幕截图。

```
python -m pip install --upgrade pip
python -m pip install Pillow==9.5.0
```



**4、禁用UAC**

进入控制面板，在搜索框中输入UAC，或者从开始菜单中输入。然后拖动滑块到底部。

<img src="https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121056964.png" alt="image-20250308012243362" style="zoom:67%;" />



**5、禁用防火墙**

![image-20250308012341424](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121056021.png)

**6、禁用windows自动更新**

<img src="https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121057415.png" alt="image-20250308012701606" style="zoom:67%;" />



**7、禁用 Teredo**

以 Administrator 身份打开命令提示符，然后运行：

```
netsh interface teredo set state disabled
```



**8、配置链路本地组播名称解析(LLMNR)**

打开组策略编辑器，导航到 计算机配置> 管理模板> 网络> DNS 客户端，然后点击关闭多播名称解析，选择启动。

<img src="https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121057382.png" alt="image-20250308013936385" style="zoom:67%;" />



**9、配置网络连接状态指示灯、错误报告等**

打开组策略编辑器，导航到 计算机配置> 管理模板> 系统> 限制Internet 通信，选择启动。

<img src="https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121057627.png" alt="image-20250308014204491" style="zoom:67%;" />



**10、安装Agent**

在`CAPEv2`项目的`agent/` 目录中，找到`agent.py`文件，将它复制到样本分析机中，运行(双击)agent.py将启动侦听连接的HTTP服务器。

- 如果运行时遇到下图报错，只需要将`agent.py`文件的内容在Windows机器上复制粘贴到`.txt`文件中然后重命名为`agent.py`即可。

  ![image-20250308014930721](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121057837.png)

  

启动脚本后在宿主机通过`curl`命令访问样本分析机的8000端口，得到一串json字符串，则代表部署成功。

- 如果只启动脚本，则 Python 窗口将 Spawned。如果想要隐藏窗口，可以将文件从`agent.py`重命名为 `agent.pyw`，这将防止窗口在启动脚本时Spawning。
- 如果你想要脚本在Windows启动时启动，只需将文件放在启动文件夹中。用`Win+R`打开应用程序启动器并搜索`shell:common startup`，它将打开您想要的文件夹（通常为 `C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp` ）。
- 不要将代理程序放在用户启动文件夹（通常为 `C:\Users\<Username>\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup` ），因为它将在没有管理员权限的情况下启动代理，因此权限不足，从而导致代理无法按预期工作。

------



# 5、配置FOG和样本分析机

**1、设置样本分析机启动项**

![image-20250321103318993](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211723371.png)

改此设置并正确配置 DHCP 服务器后，应该在启动时看到以下屏幕，然后选择 **Quick Registration and Inventory** 选项。

![image-20250321103457070](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211723828.png)



**2、配置FOG**

打开 FOG 服务器并导航到 **Hosts > List all Hosts**会看到样本分析机

![image-20250321104247299](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211723497.png)



制作图像，导航到 **Images > Create New Image** 并添加一些样本分析机的信息

![image-20250321104504745](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211723199.png)



完成后，返回 **Hosts**，然后单击 host 名称将其重命名为更具可读性的名称，并将图像添加到其中。

![image-20250321104714303](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211723853.png)



**3、安装FOG 客户端**

在样本分析计算机中打开浏览器并打开 FOG 管理网站。点击左下角名为`FOG Client`的按钮，然后选择`Smart Installer`

![image-20250321105006039](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211724190.png)



安装下载`.exe`文件，安装过程中只需更改服务器地址为FOG服务的地址即可。

![image-20250321105248821](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211724146.png)

- 如果安装失败并显示`若要运行此应用程序,您必须首先安装.NET Framework的以下版本之一：v4.0.30319`，解决方案：

  https://vip.kingdee.com/knowledge/579368669239413760



**4、修改样本分析机的网络配置**

将 DNS 和网关指向 CAPE 服务器，这意味着除非进行分析，否则将无法从这台机器连接到互联网。

![image-20250321105753166](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211724222.png)



**5、创建镜像**

返回 FOG 服务器的 Web 界面，进入 hosts 选项卡，然后单击**那个橙色的小图标**。进去后点击`Task`。

这将创建一个任务，一旦样本分析计算机重新启动，该任务将被我们的分析计算机选取。

![image-20250321110628716](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211724860.png)



重新启动样本分析计算机，会自动开始录制镜像。

![image-20250321111319031](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211724411.png)



录制完成后，机器会自动重启回 Windows 并准备好接收恶意软件样本！

------



# 6、配置 CAPEv2

在`/opt/CAPEv2/conf`下需要配置的重要文件:

**1、cuckoo.conf**

```
# Specify the name of the machinery module to use, this module will
# define the interaction between Cuckoo and your virtualization software
# of choice.
machinery = kvm #修改为 physical
```

![image-20250321112859729](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211724955.png)



```
# The Result Server is used to receive in real time the behavioral logs
# produced by the analyzer.
# Specify the IP address of the host. The analysis machines should be able
# to contact the host through such address, so make sure it's valid.
# NOTE: if you set resultserver IP to 0.0.0.0 you have to set the option
# `resultserver_ip` for all your virtual machines in machinery configuration.
ip = 192.168.1.1 #修改为 0.0.0.0
```

![image-20250321113013255](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211724067.png)



**2、physical.conf**

```
# Specify a comma-separated list of available machines to be used. For each
# specified ID you have to define a dedicated section containing the details
# on the respective machine. (E.g. physical1,physical2,physical3)
machines = physical01 #修改为在FOG中配置的机器名称
interface = ens33 #修改为你配置的网卡名称
```

![image-20250321113259731](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211724205.png)



```
[fog]
hostname = <FOG server IP>  #修改为FOG服务的IP
apikey = <API Key> #修改为你自己的API Key
user_apikey = <User API Key> #修改为你自己的User API Key
```

![image-20250321115117240](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211724251.png)

- `apikey`获取方法：**FOG Configuration > FOG Settings > API System** 记住要勾选上`API ENABLED`

  ![image-20250321113906618](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211723554.png)



- `user_apikey`获取方法：**Users > List All Users > FOG >  API System**

  ![image-20250321114446004](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211723100.png)

------



# 7、启动CAPEv2

**1、重启CAPEv2服务器**

CAPEv2服务会开机自启



**2、检查CAPEv2服务状态**

```
systemctl status cape.service
systemctl status cape-processor.service
systemctl status cape-web.service
systemctl status cape-rooter.service
```



**3、浏览器访问 CAPEv2 上传样本分析**

![image-20250321170235044](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503211723434.png)

