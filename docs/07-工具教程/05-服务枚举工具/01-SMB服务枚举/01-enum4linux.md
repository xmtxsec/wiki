
# 0X00 简介
Enum4linux是用于枚举windows和Linux系统上的SMB服务的工具。可以轻松的从与**SMB服务**有关的目标中快速提取信息。<br />SMB是一个协议名，全称是**Server Message Block**（服务器消息快协议），用于在计算机间共享文件、打印机、串口等，电脑上的网上邻居由它实现。一般**共享文件夹**和**打印机**使用较多，它是应用层（和表示层）协议，使用C/S架构。


# 0X01 安装



# 0X02 指令说明
![image.png](./assets/1671780002937-6b5a0ca7-d9ed-4a70-8f74-962a416fe5ab.png)

枚举命令说明

| **选项** | **说明** |
| --- | --- |
| -U |      获取用户列表 |
| -M |       获取机器列表 |
| -S |        获取共享列表 |
| -P |           获取密码策略信息 |
| -G |        获取组和成员列表 |
| -d |    详述，适用于-U和-S |
| -u |  user 用户指定要使用的用户名（默认为空） |
| -p |      指定要使用的密码（默认为空） |


附加命令

| **选项** | **说明** |
| --- | --- |
| -a  | 做所有简单枚举（-U -S -G -P -r -o -n -i），如果您没有提供任何其他选项，则启用此选项 |
| -h  |                      显示此帮助消息并退出 |
| -r  |                        通过RID循环枚举用户 |
| -R  | RID范围要枚举（默认值：500-550,1000-1050，隐含-r） |
| -K  | 指定不对应次数n。继续搜索RID，直到n个连续的RID与用户名不对应，Impies RID范围结束于999999.对DC有用 |
| -l  | 通过LDAP 389 / TCP获取一些（有限的）信息（仅适用于DN） |
| -s  | 文件暴力猜测共享名称 |
| -k  | user 远程系统上存在的用户（默认值：administrator，guest，krbtgt，domain admins，root，bin，none）用于获取sid与“lookupsid known_username”使用逗号尝试几个用户：“-k admin，user1，user2” |
| -o  | 获取操作系统信息 |
| -i  | 获取打印机信息 |
| -w  | 手动指定工作组（通常自动找到） |
| -n | 做一个nmblookup（类似于nbtstat） |
|  -v | 详细输出，显示正在运行的完整命令（net，rpcclient等） |


常用命令
```
enum4linux -a -o ip
```

LDAP信息探测
```
enum4linux -l ip   
```

获取操作系统信息
```
enum4linux -o ip  
```

通过RID循环枚举用户
```
enum4linux -r ip  
```




