
# 简介
Rsync 是一款开源的，快速的，多功能的，可实现全量及增量（差异化备份）的本地或远程数据同步备份的优秀工具。

Rsync软件适用于Unix、Linux、Windows等多种操作系统。它可以在本地计算机与远程计算机之间，或者两个本地目录之间同步文件（但不支持两台远程计算机之间的同步）。它也可以当作文件复制工具，替代`cp`和`mv`命令

官网：[https://rsync.samba.org/](https://rsync.samba.org/)

文件实时同步推荐使用Syncthing，Syncthing与rsync相比安装、使用都很方便，且具有web界面无需使用inotify监控即可实现文件实时同步。

# 安装
```
# Debian
$ sudo apt-get install rsync

# Red Hat
$ sudo yum install rsync

# Arch Linux
$ sudo pacman -S rsync
```

传输的双方都必须安装 rsync。


# 远程同步基本用法
列出本地/aaa/目录下的文件列表
```
rsync /aaa/
```

列出远程主机上/bbb/目录下的文件列表
```
rsync remote_host:/bbb/
```

将本地内容同步到远程服务器。
```
rsync -av source remote_host:destination
```

将远程内容同步到本地。
```
rsync -av username@remote_host:source destination
```
```
-a参数表示存档模式，保存所有的元数据，比如修改时间、权限、所有者等，并且软链接也会同步过去。
-v参数表示输出细节
-vv表示输出更详细的信息
-vvv表示输出最详细的信息
```

**源路径如果是一个目录的话，带上尾随斜线和不带尾随斜线是不一样的，不带尾随斜线表示的是整个目录包括目录本身，带上尾随斜线表示的是目录中的文件，不包括目录本身。**


# 实现实时传输
**两台服务器都如下配置**<br />安装rsync
```
yum -y install rsync
```

配置/etc/rsyncd.conf文件
```
vi /etc/rsyncd.conf

# rsync 守护进程的用户
uid = root
# 运行 rsync 守护进程的组
gid = root
# 允许 chroot，提升安全性，客户端连接模块，首先 chroot 到模块 path 参数指定的目录下，chroot 为 yes 时必须使用 root 权限，且不能备份 path 路径外的链接文件
use chroot = yes
# 只读
read only = no
# 只写
write only = no
# 设定白名单，可以指定IP段（172.18.50.1/255.255.255.0）,各个Ip段用空格分开
hosts allow = 192.168.1.27
hosts deny = *
# 允许的客户端最大连接数
max connections = 4
# 欢迎文件的路径，非必须
#motd file = /etc/rsyncd.motd
# pid文件路径
pid file = /var/run/rsyncd.pid
# 记录传输文件日志
transfer logging = yes
# 日志文件格式
log format = %t %a %m %f %b
# 指定日志文件
log file = /var/log/rsync.log
# 剔除某些文件或目录，不同步
exclude = lost+found/
# 设置超时时间
timeout = 900
ignore nonreadable = yes
# 设置不需要压缩的文件
dont compress   = *.gz *.tgz *.zip *.z *.Z *.rpm *.deb *.bz2

# 模块，可以配置多个
[test]
# 模块的根目录，同步目录，要注意权限
path = /srv/docker
# 是否允许列出模块内容
list = no
# 忽略错误
ignore errors
# 添加注释
comment = ftp export area
# 模块验证的用户名称，可使用空格或者逗号隔开多个用户名
auth users = root
# 模块验证密码文件 可放在全局配置里
secrets file = /etc/rsyncd.secrets
```

编辑 `/etc/rsyncd.secrets` 文件
```
vi /etc/rsyncd.secrets
root:123456
chmod 600 /etc/rsyncd.secrets
```

启动
```
rsync --daemon --config=/etc/rsyncd.conf
```

加入开机自启
```
echo 'rsync --daemon --config=/etc/rsyncd.conf' >> /etc/rc.d/rc.local
```

创建密码文件 /etc/rsyncd.pass
```
vi  /etc/rsyncd.pass
123456
chmod 600 /etc/rsyncd.pass
```

远程传输命令
```
#远程到本地
rsync -avz --delete root@192.168.1.27::test /srv/docker/ --password-file=/etc/rsyncd.pass
#本地到远程
rsync -avz --delete /srv/docker/ root@192.168.1.27::test --password-file=/etc/rsyncd.pass
```

安装**inotify**
```
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
yum install -y inotify-tools
```

**inotify常用命令**<br />1、创建事件
```latex
inotifywait -mrq  /data --timefmt "%d-%m-%y %H:%M" --format "%T %w%f 事件信息: %e" -e create
```

2、删除事件
```latex
inotifywait -mrq  /data --timefmt "%d-%m-%y %H:%M" --format "%T %w%f 事件信息: %e" -e delete
```

3、修改事件
```latex
inotifywait -mrq  /data --timefmt "%d-%m-%y %H:%M" --format "%T %w%f 事件信息: %e" -e close_wr
```

实时传输脚本<br />脚本一（建议使用）
```bash
#!/bin/bash

path=/srv/docker
Server=192.168.1.27 
User=root
module=test

monitor() {
  /usr/bin/inotifywait -mrq /srv/docker/ --format '%w%f' -e create,modify,delete,attrib,close_write,move $1 | while read line; do
        rsync -avz --delete /srv/docker/ ${User}@${Server}::${module} --password-file=/etc/rsyncd.pass
  done
}

monitor $Path;
```

脚本二
```bash
#!/bin/bash

path=/srv/docker
Server=192.168.1.27
User=root
module=test

monitor() {
  /usr/bin/inotifywait -mrq /srv/docker/ --format '%w%f' -e create,modify,delete,attrib,close_write,move $1 | while read line; do
    if [ -f $line ]; then
      rsync -avz --delete ${User}@${Server}::${module} /srv/docker/ --password-file=/etc/rsyncd.pass
    else
      cd $1 &&
        rsync -avz --delete /srv/docker/ ${User}@${Server}::${module} --password-file=/etc/rsyncd.pass
    fi
  done
}

monitor $Path;
```
