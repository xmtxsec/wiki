# 一、Tshark简介

**Tshark** 继承了Wireshark强大的协议解析能力，支持分析成百上千种网络协议。更重要的是，它轻量高效，非常适合在服务器端、脚本自动化等场景中使用。

tshark是 Wireshark 的命令行版本，支持几乎所有 Wireshark 的核心功能。它可以用来：

- 抓包并保存为 pcap 文件
- 实时显示数据包信息
- 提取指定字段进行分析
- 配合 shell 脚本完成自动化任务



# 二、安装 Tshark

## 2.1、Windows

1. 下载安装包： 访问Wireshark官网，找到适合你系统的安装包。
2. 勾选Tshark组件： 安装过程中，务必勾选 “Tshark” 选项。
3. 配置环境变量（可选）： 为了在终端中直接使用 Tshark 命令，建议将 Wireshark 的安装目录添加到 PATH 环境变量中。

安装完成后，打开命令行窗口，输入以下命令，验证是否安装成功：

```
tshark -v
```

如果显示 Tshark 的版本信息，那就说明成功安装了！



## 2.2、Linux

1、基于 Debian

```
sudo apt-get install tshark
```



2、基于 Red Hat

```
# 1. 启用EPEL仓库
sudo yum install epel-release -y

# 2. 安装Wireshark（包含tshark）
sudo yum install wireshark -y

# 3. 验证安装
tshark -v  # 输出类似 "TShark 1.10.14"
```



# 三、参数说明

## 3.1、抓包接口类

| 参数 | 说明                                                         |
| ---- | ------------------------------------------------------------ |
| -i   | 设置抓包的网络接口，不设置则默认为第-一个非自环接口。        |
| -D   | 列出当前存在的网络接口。在不了解OS所控制的网络设备时，一-般先用"tshark-D"查看网络接口的编号以供 -i 参数使用。 |
| -f   | 设定抓包过滤表达式( capture filterexpression) 。抓包过滤表达式的写法雷同于tcpdump |
| -s   | 设置每个抓包的大小，默认为65535，多于这个大小的数据将不会被程序记入内存、写入文件。 |
| -p   | 设置网络接口以非混合模式工作，即只关心和本机有关的流量。     |
| -B   | 设置内核缓冲区大小，仅对windows有效。                        |
| -y   | 设置抓包的数据链路层协议，不设置则默认为-L找到的第一个协议。 |
| -L   | 列出本机支持的数据链路层协议，供 -y 参数使用。               |



## 3.2、抓包停止条件

| 参数 | 说明                                                         |
| ---- | ------------------------------------------------------------ |
| -C   | 抓取的packet数，在处理一定数量的packet后，停止抓取，程序退出。 |
| -a   | 设置tshark抓包停止向文件书写的条件，事实上是tshark在正常启动之后停止工作并返回的条件。条件写为`test:value`的形式，如：<br />"-a duration:5"表示tshark启动后在5秒内抓包然后停止; <br />"-a filesize:10"表 示tshark在输出文件达到10kB后停止; <br />"-a files:n"表示tshark在写满n个文件后停止。 |



## 3.3、文件输出控制

| 参数 | 说明                                                         |
| ---- | ------------------------------------------------------------ |
| -b   | 设置ringbuffer文件参数。ringbuffer的文件名由 -w 参数决定。-b参数采用`test:value`的形式书写。<br />"-b duration:5"表示每5秒写下一个ringbuffer文件;<br/>"-b filesize:5"表示每达到5kB写下一个ringbuffer文件;<br />"-b files:7"表示ringbuffer文件最多7个，周而复始地使用，如果这个参数不设定，tshark会 将磁盘写满为止。 |



## 3.4、文件输入

| 参数 | 说明                                                         |
| ---- | ------------------------------------------------------------ |
| -r   | 设置tshark分析的输入文件。tshark既可以抓取分析即时的网络流量，又可以分析dump在文件中的数据。-r不能是命名管道和标准输入。 |



## 3.5、处理类

| 参数 | 说明                                                         |
| ---- | ------------------------------------------------------------ |
| -R   | 设置读取(显示)过滤表达式( read filterexpression) 。不符合此表达式的流量同样不会被写入文件。 |
| -n   | 禁止所有地址名字解析(默认为允许所有)                         |
| -N   | 启用某一层的地址名字解析。"m”代表MAC层，“n"代表网络层，"t"代表传输层，“C"代表当前异步DNS查找。如果-n和-N参数同时存在，-n将被忽略。如果-n和-N参数都不写，则默认打开所有地址名字解析。 |
| -d   | 将指定的数据按有关协议解包输出。如要将tcp 8888端口的流量按http解包，应该写为"-d tcp.port == 8888,http"。 |



## 3.6、输出类

| 参数 | 说明                                                         |
| ---- | ------------------------------------------------------------ |
| -w   | 设置raw数据的输出文件。这个参数不设置，tshark将会把解码结果输出到stdout。"-w-"表示把raw输出到stdout。如果要把解码结果输出到文件，使用重定向"> "而不要-w参数。 |
| -F   | 设置输出raw数据的格式，默认为libpcap。 "tshark -F"会列出所有支持的raw格式。 |
| -V   | 设置将解码结果的细节输出，否则解码结果仅显示一个packet一行的summary。 |
| -X   | 设置在解码输出结果中，每个packet后面以HEXdump的方式显示具体数据。 |
| -T   | 设置解码结果输出的格式，包括text、ps、psml和pdml， 默认为text。 |
| -t   | 设置解码结果的时间格式。"ad"表示带日期的绝对时间，"a"表示不带日期的绝对时间，"r”表示从第一个包到现在的相对时间，“d"表示两个相邻包之间的增量时间(delta ) |
| -S   | 在向raw文件输出的同时，将解码结果打印到控制台。              |
| -l   | 在处理每个包时即时刷新输出。                                 |
| -X   | 扩展项。                                                     |
| -q   | 设置安静的stdout输出(例如做统计时)。                         |
| -Z   | 设置统计参数。                                               |



# 四、使用示例

## 4.1、基本操作

1、查询当前系统可以捕获的借口

```
tshark -D
```



2、指定网卡抓包，且指定抓包数目及类型

```
tshark -i 1 -C 2000 -f "tcp dst port 443"
说明:捕获tcp，目的端口为443的流量，捕获数量为2000个。
```



3、将抓到的包写入文件，且指定文件的大小和数量

```
tshark -i 1 -W /var/tshark/tshark.log -b filesize:8096 -b files:8
说明:运行tshark来捕获数据包到文件，指定文件大小为8M左右，只保留8个最新文件，一边对采集的文件作进一步分析处理。
```



4、转换包文件格式

```
tshark -r http.pcap -W http.cap
说明:将pcap格式的包文件另存为cap格式的包文件。
```



5、过滤某包文件中的数据包

```
tshark -r http_ _bak.pcap -R "tcp.dstport= = 80" -W http.cap
说明:过滤包文件中所有TCP协议目的端口为80的数据包，并将过滤的数据包另存为其他包文件。
```





## 4.2、将PCAP包转换为文本格式的几种方法



**1、输出完整协议详情（适合深度分析）**

```
tshark -r input.pcap -V > output.txt
```

- 功能：生成包含数据包各层协议解析的详细文本（如 Ethernet、IP、TCP 头部及负载）。
- 适用场景：需要完整协议树分析（如调试协议交互问题）。
- 注意：输出内容较长，建议配合 `grep` 过滤关键字段（如 `tshark -r input.pcap -V | grep "HTTP"`）

![image-20250625163600391](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202506251636775.png)



**2、十六进制转储格式（适合二进制分析）**

```
tshark -r input.pcap -x > hex_output.txt
```

- 功能：输出十六进制字节 + ASCII 解码格式，保留原始数据包字节级信息。
- 适用场景：修改数据包内容后需重新导入（如 Wireshark 的 File > Import from Hex Dump）

![image-20250625163807674](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202506251638771.png)



**3、表格字段提取（适合结构化处理）**

```
tshark -r input.pcap -T fields -e frame.time -e ip.src -e ip.dst -e http.request.uri -E separator=, -E header=y > output.csv
```

- 功能：提取指定字段生成 CSV/TSV 文件，适合导入 Excel 或数据库。
- 关键参数：
  - `-T fields`：指定字段输出模式。
  - `-e`：选择字段（如 `tcp.port`、`http.host`）。
  - `-E separator=`：设置分隔符（如 `,` 或 `\t`）。
  - `-E header=y`：包含字段名标题行。

![image-20250625164056886](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202506251640990.png)



**4、 简洁摘要格式（类似 Wireshark 主界面）**

```
tshark -r input.pcap > summary.txt
```

- 功能：输出类似 Wireshark 主界面的摘要信息（时间、源/目标地址、协议、长度）
- 适用场景：快速浏览数据包概况。

![image-20250625164350124](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202506251643247.png)



**5、JSON 格式（适合编程处理）**

```
tshark -r input.pcap -T json > output.json
```

- 功能：生成结构化 JSON 数据，包含每个包的协议字段及原始十六进制数据。
- 优势：支持程序化解析（如 Python 脚本处理）。
- 定制字段：指定输出特定字段（减少文件体积）`tshark -r input.pcap -T json -e ip.src -e http.request.method`

![image-20250625173223941](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202506251732248.png)



⚠️ **操作注意事项**：

1. **字段兼容性**
   - 旧版 `tshark`（如 1.10）使用 `col.Protocol`，新版需用 `_ws.col.Protocol`。
   - 验证字段存在性：通过 Wireshark 打开 PCAP 文件，右键字段 > **Copy > Field Name** 获取准确字段名。

2. **大文件处理**
   - 使用 `-Y` 过滤减少输出量（如 `-Y "http"` 仅输出 HTTP 包）。
   - 分块处理：`tshark -r large.pcap -c 1000 > partial.txt`（仅处理前 1000 个包）。

3. **编码问题**
   - 非 ASCII 字符（如中文）可能乱码，添加 `-E bom=y` 生成 UTF-8 BOM 头（仅限 `-T fields`）。



💎 **最佳实践建议**

**自动化脚本**：使用 Python 调用 `tshark` 批量处理 PCAP 并解析文本（示例）:

```python
import subprocess
command = "tshark -r traffic.pcap -T fields -e frame.time -e ip.src -e ip.dst -E separator=;"
output = subprocess.check_output(command, shell=True, text=True)
with open("result.csv", "w") as f:
    f.write(output)
```

























