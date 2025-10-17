# 1、简介

​	恶意软件分析沙箱是在隔离的环境中运行未知且不受信任的应用程序或文件，并获取有关文件用途的信息。恶意软件沙箱是动态分析方法的实际应用，它不是静态分析二进制文件，而是实时执行和监控，可以获取有关恶意软件的更多详细信息。

​	CAPEv2 Sandbox 是一款用于自动分析可疑文件或恶意文件的开源沙箱。用于在隔离环境中执行恶意文件，同时检测其动态行为并收集取证据。

- **项目地址：**https://github.com/kevoreilly/CAPEv2
- **项目文档：**https://capev2.readthedocs.io/en/latest/ （好像是没有维护了）

------



# 2、准备环境

**1、准备虚拟机环境**

本文部署环境为 VMWare 虚拟机中 Ubuntu22.04。虚拟机需要打开`Intel VT-x`！

Ubuntu ISO下载链接: https://pan.baidu.com/s/1ldG9TieC4jdF4SysUo0Nsw?pwd=fdb9

![image-20250308003732230](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121056938.png)

![image-20250308002324773](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121056791.png)

```
sudo apt update
sudo apt upgrade
sudo apt autoremove
sudo apt clean
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



- **建议：**完成此步骤后建议拍摄虚拟机快照。
- **在安装CAPEv2的过程中一定要保持网络通常！！！**

------



# 3、安装KVM

- 可以安装和使用您喜欢的任何虚拟机管理程序，但官方建议使用 KVM！



## 3.1、使用项目脚本安装

安装与 KVM 相关的所有内容（包括 KVM 本身）的脚本位置：`/CAPEv2/installer/kvm-qemu.sh`

- 建议：使用`git clone`来克隆项目到本地，不要使用下载`.zip`的方法。



**1、修改脚本文件**

在执行脚本之前，将脚本内容里面出现的 `<WOOT>` 替换为你真正的硬件模式。

```
sudo acpidump > acpidump.out
sudo acpixtract -a acpidump.out
sudo iasl -d dsdt.dat
```

最后一个命令的输出生成了一行代码：

ACPI：DSDT 0x0000000000000000 0213AC（v02 HPQOEM **82BF** 00000000 INTL 20121018）

这里的 **82BF** 就是我们想要的，你的系统需要更具你的实际情况处理。（不知道为什么这里我系统显示的是 **Custom**）



**2、执行修改后的脚本**

```
sudo chmod a+x kvm-qemu.sh
sudo ./kvm-qemu.sh all <username> | tee kvm-qemu.log
```



**3、安装virt-manager（可选）**

将 `<username>` 替换为您的实际用户名。

```
sudo ./kvm-qemu.sh virtmanager <username> | tee kvm-qemu-virt-manager.log
```



## 3.2、手动安装KVM

我采用的是手动安装KVM，搞不懂怎样将脚本内容里面出现的 `<WOOT>` 替换为硬件模式。

**1、手动安装KVM**

```
sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils libvirt-dev libvirt-daemon
sudo apt install virt-manager -y
```



**2、启动`libvirtd`**

```
sudo systemctl start libvirtd  #启动libvirtd
sudo systemctl enable libvirtd #开机自启libvirtd
sudo systemctl status libvirtd #查看libvirtd当前的运行状态
```

<img src="https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121056124.png" alt="image-20250306121656926" style="zoom:50%;" />

- **建议：**完成此步骤后建议拍摄虚拟机快照。

------



# 4、安装虚拟机

**1、准备ISO文件**

将下载好的ISO文件上传到`var/lib/libvirt/images`，这个目录是放磁盘镜像的默认路径。

建议使用Win7，用Win10会卡到怀疑人生。

Win7 ISO下载链接：https://pan.baidu.com/s/11zazsIWPe3fR3224DLITrw?pwd=6455

![image-20250308005812229](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121056281.png)



**2、启动KVM**

```
sudo virt-manager
```

![image-20250308005846228](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121056833.png)



**3、依次选择文件->新建虚拟机**

![image-20250308005940546](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121056056.png)



**4、选择上传的镜像文件，创建虚拟机。**

![image-20250308010010189](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121056557.png)



**5、安装虚拟机**

![image-20250308010059349](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121056713.png)



- **建议：**完成此步骤后建议拍摄虚拟机快照。

------



# 5、配置虚拟机

**1、安装Python**

只有 `32位(x86)` 版本的 Python3 是目前支持 Windows，因为分析器与低级 Windows 库交互的方式。使用 `64位`版本的 Python 将使 Windows 中的分析器崩溃。

官方文档建议首选 Python 版本> 3.6。我这里用的是3.8版本，后面碰到一些问题。

![image-20250308011852442](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121056801.png)

我建议使用Python 版本>= 3.9。但是Python 3.9 及以上版本不支持 win7 了。

有大神自行编译了源码并制作了支持 win7 的 Python 3.9 及以上版本的安装包。

GitHub：https://github.com/adang1345/PythonWin7



- 安装Python的时候可能存在无法安装的问题，这是因为系统缺少补丁，打上补丁即可。

  补丁下载链接：https://pan.baidu.com/s/1zCoF8wnNVFRbgYF9g-kl3w?pwd=js4m

![image-20250308011647980](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121056627.png)



**2、安装Pillow**

用于在分析过程中截取 Windows 桌面的屏幕截图。

```
python -m pip install --upgrade pip
python -m pip install Pillow==9.5.0
```

![image-20250308012032827](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121056370.png)



**3、禁用UAC**

进入控制面板，在搜索框中输入UAC，或者从开始菜单中输入。然后拖动滑块到底部。

<img src="https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121056964.png" alt="image-20250308012243362" style="zoom:67%;" />



**4、禁用防火墙**

![image-20250308012341424](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121056021.png)



**5、禁用windows自动更新**

<img src="https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121057415.png" alt="image-20250308012701606" style="zoom:67%;" />



**6、禁用 Teredo**

以 Administrator 身份打开命令提示符，然后运行：

```
netsh interface teredo set state disabled
```

![image-20250308013629770](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121057188.png)



**7、配置链路本地组播名称解析(LLMNR)**

打开组策略编辑器，导航到 计算机配置> 管理模板> 网络> DNS 客户端，然后点击关闭多播名称解析，选择启动。

<img src="https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121057382.png" alt="image-20250308013936385" style="zoom:67%;" />



**8、配置网络连接状态指示灯、错误报告等**

打开组策略编辑器，导航到 计算机配置> 管理模板> 系统> 限制Internet 通信，选择启动。

<img src="https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121057627.png" alt="image-20250308014204491" style="zoom:67%;" />





**9、安装Agent**

在`CAPEv2`项目的`agent/` 目录中，找到`agent.py`文件，将它复制到win7虚拟机中，运行(双击)agent.py将启动侦听连接的HTTP服务器。

- 如果运行时遇到下图报错，这是因为在 Python 3.9 及以上版本中，`dict` 类型支持直接使用方括号（如 `dict[str, str]`）进行类型注解。但在 Python 3.8 或更早版本中，这种语法是不被支持的。

  ![image-20250308014930721](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121057837.png)

  解决方案一：安装 Python 版本>= 3.9，上面已经说过了。
  
  解决方案二：使用官方0.11版本的 agent.py 脚本，下载链接：https://download.csdn.net/download/weixin_72723791/90591648
  
  

启动脚本后在宿主机通过`curl`命令访问win7虚拟机的8000端口，得到一串json字符串，则代表部署成功。

![image-20250308020320612](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121057822.png)

- 如果只启动脚本，则 Python 窗口将 Spawned。如果想要隐藏窗口，可以将文件从`agent.py`重命名为 `agent.pyw`，这将防止窗口在启动脚本时Spawning。
- 如果你想要脚本在Windows启动时启动，只需将文件放在启动文件夹中。用`Win+R`打开应用程序启动器并搜索`shell:common startup`，它将打开您想要的文件夹（通常为 `C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp` ）。不要将代理程序放在用户启动文件夹（通常为 `C:\Users\<Username>\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup` ），因为它将在没有管理员权限的情况下启动代理，因此权限不足，从而导致代理无法按预期工作。



**10、创建隔离网络**

在 Virtual Machine Manager GUI 中，单击 **编辑** -> **连接详情**。

![image-20250308021256256](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121057175.png)

在打开的窗口中，单击图像左下角的 **+** 号。定义新网络的详细信息，为其命名 （例如 hostonly） 并确保选择 Isolated mode（隔离模式）。然后，单击 **IPv4 配置**下拉菜单并定义网络范围。

<img src="https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121057436.png" alt="image-20250308021424782" style="zoom:67%;" />

创建隔离网络后，单击网络适配器并选择隔离网络。

<img src="https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121057149.png" alt="image-20250308021846036" style="zoom:67%;" />



**11、配置win7虚拟机的静态IP**

<img src="https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121057456.png" alt="image-20250308022240703" style="zoom: 80%;" />



**12、测试网络连通性**

<img src="https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121057345.png" alt="image-20250308022622662" style="zoom:67%;" />



**13、拍摄快照**

在开机状态下拍摄快照，在执行此作之前，**确保CAPE 的Agent正在运行并且 Windows 已完全启动**。

快照拍摄后，关机即可。CAPE需要在KVM虚拟机关闭的情况下运行。

```
virsh snapshot-create "<Name of VM>"
virsh snapshot-create-as --domain "<Name of VM>" --name "<Name of snapshot>"
```

![image-20250308023055106](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121057348.png)



- **建议：**完成此步骤后建议拍摄虚拟机快照。

------



# 6、安装CAPEv2

**1、查看脚本说明**

脚本位置在`/CAPEv2/installer/cape2.sh`。

在执行脚本前建议使用`-h`查看帮助和用法信息，官方建议阅读脚本本身以了解它们的功能。（很重要）

```
sudo chmod a+x cape2.sh
sudo ./cape2.sh -h
```

![image-20250314174346577](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503141744773.png)



**2、修改脚本文件**

修改 `NETWORK_IFACE`，`IFACE_IP` 和 `PASSWD` 变量以确保正确安装。

![image-20250314175500330](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503141755551.png)



**3、执行脚本`cape2.sh`**

按照脚本说明正确选择你所需要的方式执行脚本执行（执行时间较长），例如：

```
#安装依赖、CAPEv2、systemd等
./cape2.sh base 192.168.1.1 | tee ./cape2.sh.log 
```

```
#安装所有东西，建议仅在了解其影响的情况下使用
./cape2.sh all 192.168.1.1 | tee ./cape2.sh.log
```

```
#安装CAPEv2
./cape2.sh sandbox 192.168.1.1 | tee ./cape2.sh.log
```

```
#安装所有依赖并进行性能优化
./cape2.sh dependencies 192.168.1.1 | tee ./cape2.sh.log
```

我在这里执行的命令是（可能存在问题）：

```
./cape2.sh base cape | tee cape.log
```

- 运行完后重启计算机。



**4、查看服务状态**

计算器重启后分别执行以下指令，检查是否均有对应服务存在。

```
systemctl status cape.service
systemctl status cape-processor.service
systemctl status cape-web.service
systemctl status cape-rooter.service
```



确认没问题后关闭web服务（避免后面启动出现端口冲突问题）。

```
systemctl stop cape-web.service
```



- 备注：

  ```
  要重新启动任何服务，请使用：
  systemctl restart <service_name>
  要查看服务日志，请使用：
  journalctl -u <service_name>
  ```



- **建议：**完成此步骤后建议拍摄虚拟机快照。

------



# 7、安装依赖

**1、安装主要依赖**

在`/opt/CAPEv2/`目录下执行下面命令

```
poetry install
```

![image-20250308111704698](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121057402.png)

- 执行此步骤时如果没有成功安装依赖，将路径中的CAPEv2命名为capev2后，尝试执行`poetry install`进行安装。安装成功后再将文件夹名复原。Poetry在识别路径大小写时存在问题。



执行以下命令，若得到对应输出，则安装成功。

```
poetry env list
```

![image-20250308111829702](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121057208.png)

- **注意：**从现在起，需要在 Poetry 的虚拟环境中执行 CAPE。为此，只需要 `poetry run <command>` 。例如：

  ```
  sudo -u cape poetry run python3 cuckoo.py
  ```

  

**2、安装可选依赖**

```
sudo -u cape poetry run pip install -r extra/optional_dependencies.txt
```



**注意：**

- 仅需使用 `sudo` 执行安装脚本和一些如 `rooter.py` 之类的工具，其余的配置脚本和程序必须在系统执行 `cape2.sh` 后创建的 `cape` 用户下执行!

- 默认情况下，cape 用户没有登录权限。为了替代它并代表它使用 cmd，可以执行命令

  ```
  sudo su - cape -c /bin/bash
  ```

------



# 8、修改关键配置文件

CAPEv2的六个关键配置文件

```
cuckoo.conf：用于配置通用行为和分析选项。
auxiliary.conf：用于启用和配置辅助模块。
<machinery>.conf：用于定义您的虚拟化软件选项(文件名与您在 cuckoo.conf 中选择的机器模块同名)
memory.conf：内存配置。
processing.conf：用于启用和配置处理模块。
reporting.conf：用于启用或禁用报告格式。
routing.conf：用于定义虚拟机的互联网连接路由。
```



在`/opt/CAPEv2/conf`下需要配置的重要文件:

**1、cuckoo.conf**

```
# Specify the name of the machinery module to use, this module will
# define the interaction between Cuckoo and your virtualization software
# of choice.
machinery = kvm #修改为你使用的虚拟环境
```

![image-20250308034334691](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121058618.png)



```
# The Result Server is used to receive in real time the behavioral logs
# produced by the analyzer.
# Specify the IP address of the host. The analysis machines should be able
# to contact the host through such address, so make sure it's valid.
# NOTE: if you set resultserver IP to 0.0.0.0 you have to set the option
# `resultserver_ip` for all your virtual machines in machinery configuration.
ip = 192.168.55.1 #修改为宿主机的IP
```

![image-20250308034611210](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121058092.png)



```
# Minimum amount of free space (in MB) available before starting a new task.
# This tries to avoid failing an analysis because the reports can't be written
# due out-of-diskspace errors. Setting this value to 0 disables the check.
# (Note: this feature is currently not supported under Windows.)
freespace = 0 #将5000改为0
```

![image-20250308034749407](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121058664.png)



**2、kvm.conf**

```
[kvm]
# Specify a comma-separated list of available machines to be used. For each
# specified ID you have to define a dedicated section containing the details
# on the respective machine. (E.g. cuckoo1,cuckoo2,cuckoo3)
machines = win7 #修改为你虚拟机的名称

interface = virbr1 #修改为你自己配置的隔离网络的接口名称
```

![image-20250308125922673](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121058027.png)



```
[cape1] #这部分为宿主机配置
label = cape1
platform = linux
ip = 192.168.55.1
arch = x64 
```

![image-20250308045327918](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121058831.png)



```
[win7] #修改为你虚拟机的名称
# Specify the label name of the current machine as specified in your
# libvirt configuration.
label = win7 #修改为你虚拟机的名称

# Specify the operating system platform used by current machine
# [windows/darwin/linux].
platform = windows #修改为win7虚拟机的操作系统

# Specify the IP address of the current virtual machine. Make sure that the
# IP address is valid and that the host machine is able to reach it. If not,
# the analysis will fail. You may want to configure your network settings in
# /etc/libvirt/<hypervisor>/networks/
ip = 192.168.55.150 #修改为win7虚拟机的IP
```

![image-20250308130120577](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121058996.png)



```
# Set the machine architecture
# Required to auto select proper machine architecture for sample
# x64 or x86
arch = x64
```

![image-20250308045411242](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121058439.png)



**3、routing.conf**

```
[routing]
# Enable pcap generation for non live connections?
# If you have huge number of VMs, pcap generation can be a bottleneck
enable_pcap = yes #如果需要捕获pcap包，把enable_pcap设置为yes
```

![image-20250308041303426](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121058206.png)

------



# 9、启动CAPE

**1、启动CAPEv2**

在/opt/CAPEv2目录下切换到cape用户执行以下命令：

```
poetry run python3 cuckoo.py
```

![image-20250309134835189](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121058368.png)

- 如果遇到任何依赖项相关错误，请执行`extra/libvirt_installer.sh`脚本

  ![image-20250309133928116](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121058719.png)
  
  ```
  poetry run extra/libvirt_installer.sh
  ```
  
  ![image-20250308123445624](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121058279.png)

- 如果在执行`extra/libvirt_installer.sh`脚本时遇到网络问题，可以先访问https://github.com/libvirt/libvirt-python/archive/v11.0.0.zip下载`libvirt-python-11.0.0.zip`存放到`/tmp/`目录，然后用下面脚本替换`libvirt_installer.sh`（注意权限）并执行

  ```bash
  #!/bin/bash
  set -ex
  
  # run this via...
  # cd /opt/CAPEv2/ ; sudo -u cape /etc/poetry/bin/poetry run extra/libvirt_installer.sh
  
  LIB_VERSION=11.0.0
  cd /tmp || return
  
  
  unzip "libvirt-python-11.0.0.zip"
  
  
  cd "libvirt-python-${LIB_VERSION}"
  
  temp_libvirt_so_path=$(locate libvirt-qemu.so | head -n1 | awk '{print $1;}')
  temp_export_path=$(locate libvirt.pc | head -n1 | awk '{print $1;}')
  libvirt_so_path="${temp_libvirt_so_path%/*}/"
  if [[ $libvirt_so_path == "/usr/lib/x86_64-linux-gnu/" ]]; then
      temp_libvirt_so_path=$(locate libvirt-qemu.so | tail -1 | awk '{print $1;}')
      libvirt_so_path="${temp_libvirt_so_path%/*}/"
  fi
  
  export_path="${temp_export_path%/*}/"
  export PKG_CONFIG_PATH=$export_path
  
  python3 setup.py build
  pip install .
  ```

  

**2、开始处理虚拟机生成数据**

新建一个窗口切换到cape用户执行

```
poetry run python3 utils/process.py -p7 auto
```

![image-20250309135004451](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121058653.png)



**3、启动WEB**

新建一个窗口切换到cape用户执行

```
poetry run python3 web/manage.py runserver 0.0.0.0:8000
```

![image-20250309142902256](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121058143.png)

- 如果在启动 cape-web 服务时遇到与迁移相关的警告

  ![image-20250309135144087](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121059336.png)
  
  执行下面命令后重新启动WEB服务
  
  ```
  poetry run python3 web/manage.py migrate
  ```
  
  ![image-20250309135226615](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121059447.png)



4、查看关键服务启动状态

```
systemctl status cape.service
systemctl status cape-processor.service
systemctl status cape-web.service
systemctl status cape-rooter.service
```

![image-20250309144336650](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121059920.png)

![image-20250309144351708](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121059126.png)

![image-20250309144406838](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121059302.png)

![image-20250309144428226](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121059651.png)



5、在浏览器中访问`http://YOUIP:8000`

![image-20250309143100906](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121059184.png)

- 若在使用中，浏览器报CSRF相关错误，则注释掉`./web/web/setting.py` 的`MIDDLEWARE`的`django.middleware.csrf.CsrfViewMiddleware`重启即可。
- 后续CAPE会开机自启，开机后直接访问本机8000端口即可访问web。



6、上传样本测试

![image-20250309143811072](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503121059252.png)

------



# 10、鸣谢

- [CAPE SANDBOX安装从0到英雄](https://www.jianshu.com/p/c5e80b9458d9)
- [CAPEv2沙箱安装部署](https://blog.csdn.net/qq_40379977/article/details/139375474)
- [恶意软件动态分析经典沙箱Cape的安装和基础用法详解](https://blog.csdn.net/Eastmount/article/details/129507664)
- [Linux虚拟化在KVM上安装Windows](https://blog.csdn.net/wjrandwsy/article/details/136943781)
- [Python无法安装pillow](https://blog.csdn.net/m0_68503106/article/details/131943259)
- [解决python3.8版本在windows 7下无法安装的问题](https://blog.csdn.net/z09364517158/article/details/135182618)
