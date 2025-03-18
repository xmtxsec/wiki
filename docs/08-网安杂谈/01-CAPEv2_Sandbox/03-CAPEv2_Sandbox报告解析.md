# 1、简介

​	CAPEv2 Sandbox 是一款用于自动分析可疑文件或恶意文件的开源沙箱。用于在隔离环境中执行恶意文件，同时检测其动态行为并收集取证据。其检测报告的格式有`litereport`、`jsondump`、`reporthtml`、`reporthtmlsummary`、`reportpdf、``maec41`、`maec5`等格式。可以在`/CAPEv2/conf/reporting.conf`配置文件中配置你想要的报告格式。



 下面将以同一个恶意样本为例来对不同格式的报告进行解析说明。

![image-20250317110707907](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503171107038.png)



# 2、litereport

## 2.1、报告基本格式

`litereport`报告信息：

```
报告命名格式为：恶意样本分析任务ID_lite.json
报告文件类型为：.json
报告文件大小为：6482kb
```



## 2.2、报告内容解析

一个完整的`litereport`报告由十个部分组成：`CAPE`、`info`、`signatures`、`target`、`ttps`、`malscore`、`network`、`dropped`、`procdump`、`behavior`。



### 2.2.1、CAPE

```json
{
  "cape": {
    "payloads": [//描述了在分析过程中提取出的有效载荷（payload）。
      {
        "name": "payload 的唯一标识符，通常是一个哈希值（如 SHA256）",
        "path": "payload 在分析环境中的存储位置",
        "guest_paths": "payload 在虚拟机（Guest）中的路径或加载地址",
        "size": "payload 的文件大小",
        "crc32": "哈希值用于唯一标识 payload，便于后续比对和分析",
        "md5": "哈希值用于唯一标识 payload，便于后续比对和分析",
        "sha1": "哈希值用于唯一标识 payload，便于后续比对和分析",
        "sha256": "哈希值用于唯一标识 payload，便于后续比对和分析",
        "sha512": "哈希值用于唯一标识 payload，便于后续比对和分析",
        "rh_hash": "表示快速哈希（Rapid Hash），通常用于快速比对文件。",
        "ssdeep": "表示模糊哈希值，用于检测文件的相似性。",
        "type": "表示有效载荷的类型（如 PE 文件、数据文件等）。",
        "yara": ["匹配到的 Yara 规则"],
        "cape_yara": ["匹配到的 Yara 规则"],
        "clamav": ["匹配到的 ClamAV 规则"],
        "tlsh": "表示 TLSH（Trend Locality Sensitive Hashing）模糊哈希值，用于检测文件的相似性。",
        "sha3_384": "哈希值用于唯一标识 payload，便于后续比对和分析",
        "pe": {
          "guest_signers": {
            "aux_sha1": "表示辅助签名的 SHA1 哈希值。",
            "aux_timestamp": "表示辅助签名的时间戳。",
            "aux_valid": "表示辅助签名是否有效。",
            "aux_error": "表示是否存在辅助签名验证错误。",
            "aux_error_desc": "描述辅助签名验证错误的原因。",
            "aux_signers": ["所有辅助签名的相关信息。"]
          },
          "digital_signers": "文件的数字签名信息",
          "imagebase": "文件加载到内存时的起始地址",
          "entrypoint": "程序开始执行的位置。",
          "ep_bytes": "程序入口点的原始机器语言指令。",
          "peid_signatures": "使用 PEiD 工具检测到的签名（通常用于识别打包器或加密器）。",
          "reported_checksum": "文件头中声明的校验和。",
          "actual_checksum": "实际计算出的校验和。",
          "osversion": "文件的目标操作系统版本。",
          "pdbpath": "调试符号文件（PDB）的路径。",
          "imports": {
            "KERNEL32": {
              "dll": "动态链接库的完整名称",
              "imports": [
                {
                  "address": "表示导入函数在内存中的地址（RVA，Relative Virtual Address）。",
                  "name": "表示导入函数的名称。"
                },
                {
                  "......": "省略，上面内容的重复"
                }
              ],
              "......": "省略，上面内容的重复"
            }
          },
          "exported_dll_name": "如果该文件是一个 DLL，则表示其导出的 DLL 名称。",
          "exports": [
            {
              "address": "表示导出函数在内存中的地址（RVA，Relative Virtual Address）。",
              "name": "表示导出函数的名称。",
              "ordinal": "表示导出函数的序号（Ordinal），用于标识该函数。"
            },
            {
              "......": "省略，上面内容的重复"
            }
          ],
          "dirents": [
            {
              "name": "表示该条目对应的类型或功能。",
              "virtual_address": "表示该数据段在内存中的虚拟地址（RVA，Relative Virtual Address）。",
              "size": "表示该数据段的大小（以字节为单位）。"
            },
            {
              "......": "省略，上面内容的重复"
            }
          ],
          "sections": [
            {
              "name": "节区的名称，通常表示其用途。",
              "raw_address": "节区在文件中的偏移地址（以字节为单位）。",
              "virtual_address": "节区在内存中的虚拟地址（RVA，Relative Virtual Address）。",
              "virtual_size": "节区在内存中的大小（以字节为单位）。",
              "size_of_data": "节区在文件中的实际大小（以字节为单位）。",
              "characteristics": "节区的属性，描述其权限和用途。",
              "characteristics_raw": "节区属性的原始十六进制值。",
              "entropy": "节区的熵值，用于衡量内容的随机性。"
            },
            {
              "......": "省略，上面内容的重复"
            }
          ],
          "overlay": "文件末尾的附加数据（Overlay）。",
          "resources": [
            {
              "name": "表示资源的类型或名称。",
              "offset": "表示资源在文件中的偏移地址。",
              "size": "表示资源的大小。",
              "filetype": "表示资源的文件类型。",
              "language": "表示资源的语言。",
              "sublanguage": "表示资源的子语言。",
              "entropy": "表示资源的熵值，用于衡量内容的随机性。"
            },
            {
              "......": "省略，上面内容的重复"
            }
          ],
          "versioninfo": [
            {
              "name": "表示版本信息的属性名称。",
              "value": "表示该属性的具体值。"
            },
            {
              "......": "省略，上面内容的重复"
            }
          ],
          "imphash": "导入哈希值，用于唯一标识文件的导入表。",
          "timestamp": "文件的编译时间戳。",
          "icon": "图标相关的信息。",
          "icon_hash": "表示图标的哈希值，通常是对图标数据计算出的唯一标识符（如 MD5 或 SHA256 哈希值）。",
          "icon_fuzzy": "表示图标的模糊哈希值（Fuzzy Hash），用于检测图标之间的相似性。",
          "icon_dhash": "表示图标的差分哈希值（Difference Hash，简称 dHash），用于快速比较图标的视觉相似性。",
          "imported_dll_count": "表示该 PE 文件导入的动态链接库（DLL）的数量。"
        },
        "data": "存储与当前对象（如字符串、payload 或行为）相关的原始数据或补充信息。",
        "strings": [],
        "virustotal": {
          "error": "连接virustotal的查询状态",
          "msg": "查询的详细信息或者错误信息"
        },
        "cape_type_code": "分析任务的ID",
        "cape_type": "表示此 payload 的类型",
        "process_path": "表示与此 payload 相关联的进程路径",
        "process_name": "表示与此 payload 相关联的进程名称",
        "module_path": "表示此 payload 加载到的模块路径。",
        "pid": "进程 ID",
        "virtual_address": "表示 payload 在内存中的加载地址。"
      },
      {
        "......": "省略，此处有多个上述内容"
      }
    ],
    "configs": []
  }
}
```



### 2.2.2、info

```json
{
  "info": {
    "version": "CAPE的版本",
    "started": "表示分析开始的时间。",
    "ended": "表示分析结束的时间。",
    "duration": "表示分析持续的时间（以秒为单位）。",
    "id": "表示分析任务的唯一标识符。",
    "category": "表示分析的类别。",
    "custom": "表示用户自定义的标签或注释。",
    "machine": {
      "id": "表示虚拟机的唯一标识符。",
      "status": "表示虚拟机的当前状态。",
      "name": "表示虚拟机的名称",
      "label": "表示虚拟机的标签",
      "platform": "表示虚拟机的操作系统平台。",
      "manager": "表示虚拟机管理器的类型。",
      "started_on": "表示虚拟机的启动时间。",
      "shutdown_on": "表示虚拟机的关闭时间。"
    },
    "package": "表示分析使用的软件包类型。",
    "timeout": "表示分析是否因超时而终止。",
    "shrike_url": "表示与该样本相关的 URL，通常是从 Shrike 系统中提取的。",
    "shrike_refer": "表示与该样本相关的引用信息（Referer），通常是 HTTP 请求中的 Referer 头部内容。",
    "shrike_msg": "表示与该样本相关的消息或描述信息，通常是从 Shrike 系统中提取的。",
    "shrike_sid": "表示与该样本相关的 Shrike 会话 ID（Session ID），用于唯一标识一次分析会话。",
    "parent_id": "表示父任务的 ID（如果有）。",
    "tlp": "表示 TLP（Traffic Light Protocol）分类，用于标记信息共享的敏感性。",
    "parent_sample": {},
    "options": {},
    "source_url": "表示样本的来源 URL。",
    "route": "表示网络路由配置。",
    "user_id": "表示用户的唯一标识符。",
    "CAPE_current_commit": "表示 CAPE Sandbox 的当前代码提交哈希值。"
  }
}
```



### 2.2.3、signatures

```json
{
  "signatures": [
    {
      "name": "表示签名的名称。",
      "description": "表示签名的描述信息。",
      "categories": [
        "表示签名所属的类别。"
      ],
      "severity": "表示签名的严重性等级",
      "weight": "表示签名的权重，用于评估其重要性。",
      "confidence": "表示签名的置信度（百分比），表示检测结果的可信程度。",
      "references": [],
      "data": [
        {
          "type": "表示行为类型",
          "pid": "表示进程 ID",
          "cid": "示调用 ID（Call ID），用于唯一标识一次 API 调用。"
        },
        {
          "......": "省略，上面内容的重复"
        }
      ],
      "new_data": [],
      "alert": false,
      "families": []
    },
    {
      "......": "省略，上面内容的重复"
    }
  ]
}
```



### 2.2.4、target

```json
{
  "target": {
    "category": "表示目标的类别",
    "file": {
      "name": "表示目标文件的名称",
      "path": "表示目标文件在分析系统中的存储路径",
      "guest_paths": "表示目标文件在虚拟机（Guest）中的路径",
      "size": "表示目标文件的大小（以字节为单位）",
      "crc32": "表示目标文件的 CRC32 校验值",
      "md5": "表示目标文件的 MD5 哈希值",
      "sha1": "表示目标文件的 SHA1 哈希值",
      "sha256": "表示目标文件的 SHA256 哈希值",
      "sha512": "表示目标文件的 SHA512 哈希值",
      "rh_hash": "表示目标文件的快速哈希值（Rapid Hash）",
      "ssdeep": "表示目标文件的模糊哈希值",
      "type": "表示有效载荷的类型（如 PE 文件、数据文件等）。",
      "yara": [],
      "cape_yara": [],
      "clamav": [],
      "tlsh": "表示 TLSH（Trend Locality Sensitive Hashing）模糊哈希值，用于检测文件的相似性。",
      "sha3_384": "哈希值用于唯一标识 payload，便于后续比对和分析",
      "pe": {
        "guest_signers": {
          "aux_sha1": "表示辅助签名的 SHA1 哈希值。",
          "aux_timestamp": "表示辅助签名的时间戳。",
          "aux_valid": "表示辅助签名是否有效。",
          "aux_error": "表示是否存在辅助签名验证错误。",
          "aux_error_desc": "描述辅助签名验证错误的原因。",
          "aux_signers": []
        },
        "digital_signers": "文件的数字签名信息",
        "imagebase": "文件加载到内存时的起始地址",
        "entrypoint": "程序开始执行的位置。",
        "ep_bytes": "程序入口点的原始机器语言指令。",
        "peid_signatures": "使用 PEiD 工具检测到的签名（通常用于识别打包器或加密器）。",
        "reported_checksum": "文件头中声明的校验和。",
        "actual_checksum": "实际计算出的校验和。",
        "osversion": "文件的目标操作系统版本。",
        "pdbpath": "调试符号文件（PDB）的路径。",
        "imports": {
          "KERNEL32": {
            "dll": "动态链接库的完整名称",
            "imports": [
              {
                "address": "表示导入函数在内存中的地址（RVA，Relative Virtual Address）。",
                "name": "表示导入函数的名称。"
              },
              {
                "......": "省略，上面内容的重复"
              }
            ]
          },
          "......": {"省略，上面内容的重复"}
        },
        "exported_dll_name": "如果该文件是一个 DLL，则表示其导出的 DLL 名称。",
        "exports": [
          {
            "address": "表示导出函数在内存中的地址（RVA，Relative Virtual Address）。",
            "name": "表示导出函数的名称。",
            "ordinal": "表示导出函数的序号（Ordinal），用于标识该函数。"
          },
          {
            "......": "省略，上面内容的重复"
          }
        ],
        "dirents": [
          {
            "name": "表示该条目对应的类型或功能。",
            "virtual_address": "表示该数据段在内存中的虚拟地址（RVA，Relative Virtual Address）。",
            "size": "表示该数据段的大小（以字节为单位）。"
          },
          {
            "......": "省略，上面内容的重复"
          }
        ],
        "sections": [
          {
            "name": "节区的名称，通常表示其用途。",
            "raw_address": "节区在文件中的偏移地址（以字节为单位）。",
            "virtual_address": "节区在内存中的虚拟地址（RVA，Relative Virtual Address）。",
            "virtual_size": "节区在内存中的大小（以字节为单位）。",
            "size_of_data": "节区在文件中的实际大小（以字节为单位）。",
            "characteristics": "节区的属性，描述其权限和用途。",
            "characteristics_raw": "节区属性的原始十六进制值。",
            "entropy": "节区的熵值，用于衡量内容的随机性。"
          },
          {
            "......": "省略，上面内容的重复"
          }
        ],
        "overlay": "文件末尾的附加数据（Overlay）。",
        "resources": [
          {
            "name": "表示资源的类型或名称。",
            "offset": "表示资源在文件中的偏移地址。",
            "size": "表示资源的大小。",
            "filetype": "表示资源的文件类型。",
            "language": "表示资源的语言。",
            "sublanguage": "表示资源的子语言。",
            "entropy": "表示资源的熵值，用于衡量内容的随机性。"
          },
          {
            "......": "省略，上面内容的重复"
          }
        ],
        "versioninfo": [
          {
            "name": "表示版本信息的属性名称。",
            "value": "表示该属性的具体值。"
          },
          {
            "......": "省略，上面内容的重复"
          }
        ],
        "imphash": "导入哈希值，用于唯一标识文件的导入表。",
        "timestamp": "文件的编译时间戳。",
        "icon": "图标相关的信息。",
        "icon_hash": "表示图标的哈希值，通常是对图标数据计算出的唯一标识符（如 MD5 或 SHA256 哈希值）。",
        "icon_fuzzy": "表示图标的模糊哈希值（Fuzzy Hash），用于检测图标之间的相似性。",
        "icon_dhash": "表示图标的差分哈希值（Difference Hash，简称 dHash），用于快速比较图标的视觉相似性。",
        "imported_dll_count": "表示该 PE 文件导入的动态链接库（DLL）的数量。"
      },
      "data": "存储与当前对象（如字符串、payload 或行为）相关的原始数据或补充信息。",
      "strings": [],
      "virustotal": {
        "error": "连接virustotal的查询状态",
        "msg": "查询的详细信息或者错误信息"
      },
      "cape_type_code": "分析任务的ID",
      "cape_type": "表示此 payload 的类型"
    }
  }
}
```



### 2.2.5、ttps

```json
{
  "ttps": [//恶意样本分析中的 TTPs（Tactics, Techniques, and Procedures） 信息。
    {
      "signature": "表示与该 TTP 关联的签名名称。",
      "ttps": [
        "表示与该行为关联的 MITRE ATT&CK 技术 ID。"
      ],
      "mbcs": [
        "MBC 的标识符，用于进一步细化和分类恶意行为",
        "省略，上面内容的重复"
      ]
    },
    {
      "......": "省略，上面内容的重复"
    }
  ]
}
```



### 2.2.6、malscore

```json
{
  "malscore": 9 //表示恶意软件的恶意程度或可疑程度。
}
```



### 2.2.7、network

```json
{
  "network": {//描述了样本在运行过程中产生的网络活动，包括主机通信、域名解析、TCP/UDP 流量等
    "pcap_sha256": "表示捕获的网络流量（PCAP 文件）的 SHA256 哈希值。",
    "hosts": [//主机通信信息
      {
        "ip": "表示目标 IP 地址",
        "country_name": "表示目标 IP 的国家信息",
        "asn": "表示目标 IP 的自治系统编号（ASN）",
        "asn_name": "表示目标 IP 的自治系统名称",
        "hostname": "表示目标 IP 的主机名",
        "inaddrarpa": "表示目标 IP 的反向 DNS 解析结果",
        "ports": []//表示与该 IP 通信的端口号
      }
    ],
    "domains": [],//域名信息
    "tcp": [],//TCP 流量
    "udp": [//列出了样本产生的所有 UDP 流量。
      {
        "src": "表示源 IP 地址",
        "sport": 49152,//表示源端口号
        "dst": "表示目标 IP 地址",
        "dport": 3702,//表示目标端口号
        "offset": 94,//表示该数据包在捕获文件中的偏移位置。
        "time": 0.01079106330871582//表示该数据包的时间戳，单位为秒。
      },
      {
        "......": "省略，上面内容的重复"
      }
    ],
    "icmp": [],//ICMP 流量
    "http": [],//HTTP 请求
    "dns": [],//DNS 查询
    "smtp": [],//SMTP 邮件发送
    "irc": [],//IRC 聊天等网络行为
    "dead_hosts": []//表示尝试连接但失败的目标主机列表。
  }
}
```



### 2.2.8、dropped

```json
{
  "dropped": [//描述了恶意样本分析中 dropped files（释放文件） 的信息。
    {
      "name": [
        "表示释放文件的名称。"
      ],
      "path": "表示释放文件在分析系统中的存储路径。",
      "guest_paths": [
        "表示释放文件在虚拟机（Guest）中的路径。"
      ],
      "size": "表示文件的大小（以字节为单位）",
      "crc32": "表示文件哈希值，用于唯一标识文件",
      "md5": "表示文件哈希值，用于唯一标识文件",
      "sha1": "表示文件哈希值，用于唯一标识文件",
      "sha256": "表示文件哈希值，用于唯一标识文件",
      "sha512": "表示文件哈希值，用于唯一标识文件",
      "rh_hash": "表示快速哈希值（Rapid Hash）",
      "ssdeep": "表示文件的模糊哈希值（SSDEEP），用于检测文件的相似性",
      "type": "表示文件的类型和内容",
      "yara": [],//匹配到的任何 Yara 规则
      "cape_yara": [],//匹配到的任何CAPE 自定义规则
      "clamav": [],//ClamAV 检测到的匹配结果
      "tlsh": "表示 TLSH（Trend Locality Sensitive Hashing）模糊哈希值，用于检测文件的相似性。",
      "sha3_384": "哈希值用于唯一标识 payload，便于后续比对和分析",
      "pe": {
        "guest_signers": {//虚拟机环境中对文件签名的验证结果。
          "aux_sha1": "表示辅助签名的 SHA1 哈希值。",
          "aux_timestamp": "表示辅助签名的时间戳。",
          "aux_valid": "表示辅助签名是否有效。",
          "aux_error": "表示是否存在辅助签名验证错误。",
          "aux_error_desc": "描述辅助签名验证错误的原因。",
          "aux_signers": []//列出所有辅助签名的相关信息
        },
        "digital_signers": "文件的数字签名信息",
        "imagebase": "文件加载到内存时的起始地址",
        "entrypoint": "程序开始执行的位置。",
        "ep_bytes": "程序入口点的原始机器语言指令。",
        "peid_signatures": "使用 PEiD 工具检测到的签名（通常用于识别打包器或加密器）。",
        "reported_checksum": "文件头中声明的校验和。",
        "actual_checksum": "实际计算出的校验和。",
        "osversion": "文件的目标操作系统版本。",
        "pdbpath": "调试符号文件（PDB）的路径。",
        "imports": {//文件导入的函数及其所属的动态链接库（DLL）
          "KERNEL32": {//表示动态链接库（DLL）的名称
            "dll": "动态链接库的完整名称",
            "imports": [
              {
                "address": "表示导入函数在内存中的地址（RVA，Relative Virtual Address）。",
                "name": "表示导入函数的名称。"
              },
              {
                "......": "省略，上面内容的重复"
              }
            ]
          },
          "......": {}//省略，上面内容的重复
        },
        "exported_dll_name": "如果该文件是一个 DLL，则表示其导出的 DLL 名称。",
        "exports": [//文件导出的函数列表。
          {
            "address": "表示导出函数在内存中的地址（RVA，Relative Virtual Address）。",
            "name": "表示导出函数的名称。",
            "ordinal": "表示导出函数的序号（Ordinal），用于标识该函数。"
          },
          {
            "......": "省略，上面内容的重复"
          }
        ],
        "dirents": [//PE 文件的数据目录表，包含各种数据段的信息。
          {
            "name": "表示该条目对应的类型或功能。",
            "virtual_address": "表示该数据段在内存中的虚拟地址（RVA，Relative Virtual Address）。",
            "size": "表示该数据段的大小（以字节为单位）。"
          },
          {
            "......": "省略，上面内容的重复"
          }
        ],
        "sections": [//PE 文件的节区（Section）信息。
          {
            "name": "节区的名称，通常表示其用途。",
            "raw_address": "节区在文件中的偏移地址（以字节为单位）。",
            "virtual_address": "节区在内存中的虚拟地址（RVA，Relative Virtual Address）。",
            "virtual_size": "节区在内存中的大小（以字节为单位）。",
            "size_of_data": "节区在文件中的实际大小（以字节为单位）。",
            "characteristics": "节区的属性，描述其权限和用途。",
            "characteristics_raw": "节区属性的原始十六进制值。",
            "entropy": "节区的熵值，用于衡量内容的随机性。"
          },
          {
            "......": "省略，上面内容的重复"
          }
        ],
        "overlay": "文件末尾的附加数据（Overlay）。",
        "resources": [//PE 文件中的资源信息（如图标、位图等）。
          {
            "name": "表示资源的类型或名称。",
            "offset": "表示资源在文件中的偏移地址。",
            "size": "表示资源的大小。",
            "filetype": "表示资源的文件类型。",
            "language": "表示资源的语言。",
            "sublanguage": "表示资源的子语言。",
            "entropy": "表示资源的熵值，用于衡量内容的随机性。"
          },
          {
            "......": "省略，上面内容的重复"
          }
        ],
        "versioninfo": [//文件的版本信息（如产品名称、版本号等）。
          {
            "name": "表示版本信息的属性名称。",
            "value": "表示该属性的具体值。"
          },
          {
            "......": "省略，上面内容的重复"
          }
        ],
        "imphash": "导入哈希值，用于唯一标识文件的导入表。",
        "timestamp": "文件的编译时间戳。",
        "icon": "图标相关的信息。",
        "icon_hash": "表示图标的哈希值，通常是对图标数据计算出的唯一标识符（如 MD5 或 SHA256 哈希值）。",
        "icon_fuzzy": "表示图标的模糊哈希值（Fuzzy Hash），用于检测图标之间的相似性。",
        "icon_dhash": "表示图标的差分哈希值（Difference Hash，简称 dHash），用于快速比较图标的视觉相似性。",
        "imported_dll_count": "表示该 PE 文件导入的动态链接库（DLL）的数量。"
      },
      "data": "存储与当前对象（如字符串、payload 或行为）相关的原始数据或补充信息。",
      "strings": [],//从文件或内存中提取的可打印字符串。这些字符串通常包含文件路径、函数名称、错误消息、URL、加密算法标识符等
      "virustotal": {
        "error": "连接virustotal的查询状态",
        "msg": "查询的详细信息或者错误信息"
      },
      "cape_type_code": "分析任务的ID",
      "cape_type": "表示此 payload 的类型",
      "pid": "进程 ID"
    }
  ]
}
```



### 2.2.9、procdump

```json
{
  "procdump": {
    "name": "进程转储文件的名称",
    "path": "释放文件在分析系统中的存储路径",
    "guest_paths": "释放文件在虚拟机（Guest）中的路径",
    "size": "文件的大小（以字节为单位）",
    "crc32": "文件的不同哈希值，用于唯一标识文件",
    "md5": "文件的不同哈希值，用于唯一标识文件",
    "sha1": "文件的不同哈希值，用于唯一标识文件",
    "sha256": "文件的不同哈希值，用于唯一标识文件",
    "sha512": "文件的不同哈希值，用于唯一标识文件",
    "rh_hash": "快速哈希值（Rapid Hash）",
    "ssdeep": "文件的模糊哈希值（SSDEEP），用于检测文件的相似性",
    "type": "文件的类型和内容",
    "yara": ["文件是否匹配特定的 YARA 规则"],
    "cape_yara": ["文件是否匹配特定的 YARA 规则"],
    "clamav": ["文件是否匹配特定的 ClamAV 签名"],
    "tlsh": "表示 TLSH（Trend Locality Sensitive Hashing）模糊哈希值，用于检测文件的相似性",
    "sha3_384": "文件的 SHA3-384 哈希值",
    "pe": {
      "guest_signers": {
        "aux_sha1": "表示辅助签名的 SHA1 哈希值。",
        "aux_timestamp": "表示辅助签名的时间戳。",
        "aux_valid": "表示辅助签名是否有效。",
        "aux_error": "表示是否存在辅助签名验证错误。",
        "aux_error_desc": "描述辅助签名验证错误的原因。",
        "aux_signers": []
      },
      "digital_signers": "文件的数字签名信息",
      "imagebase": "文件加载到内存时的起始地址",
      "entrypoint": "程序开始执行的位置。",
      "ep_bytes": "程序入口点的原始机器语言指令。",
      "peid_signatures": "使用 PEiD 工具检测到的签名（通常用于识别打包器或加密器）。",
      "reported_checksum": "文件头中声明的校验和。",
      "actual_checksum": "实际计算出的校验和。",
      "osversion": "文件的目标操作系统版本。",
      "pdbpath": "调试符号文件（PDB）的路径。",
      "imports": {
        "KERNEL32": {
          "dll": "动态链接库的完整名称",
          "imports": [
            {
              "address": "表示导入函数在内存中的地址（RVA，Relative Virtual Address）。",
              "name": "表示导入函数的名称。"
            },
            {
              "......": "省略，上面内容的重复"
            }
          ],
          "......": "省略，上面内容的重复"
        }
      },
      "exported_dll_name": "如果该文件是一个 DLL，则表示其导出的 DLL 名称。",
      "exports": [
        {
          "address": "表示导出函数在内存中的地址（RVA，Relative Virtual Address）。",
          "name": "表示导出函数的名称。",
          "ordinal": "表示导出函数的序号（Ordinal），用于标识该函数。"
        },
        {
          "......": "省略，上面内容的重复"
        }
      ],
      "dirents": [
        {
          "name": "表示该条目对应的类型或功能。",
          "virtual_address": "表示该数据段在内存中的虚拟地址（RVA，Relative Virtual Address）。",
          "size": "表示该数据段的大小（以字节为单位）。"
        },
        {
          "......": "省略，上面内容的重复"
        }
      ],
      "sections": [
        {
          "name": "节区的名称，通常表示其用途。",
          "raw_address": "节区在文件中的偏移地址（以字节为单位）。",
          "virtual_address": "节区在内存中的虚拟地址（RVA，Relative Virtual Address）。",
          "virtual_size": "节区在内存中的大小（以字节为单位）。",
          "size_of_data": "节区在文件中的实际大小（以字节为单位）。",
          "characteristics": "节区的属性，描述其权限和用途。",
          "characteristics_raw": "节区属性的原始十六进制值。",
          "entropy": "节区的熵值，用于衡量内容的随机性。"
        },
        {
          "......": "省略，上面内容的重复"
        }
      ],
      "overlay": "文件末尾的附加数据（Overlay）。",
      "resources": [
        {
          "name": "表示资源的类型或名称。",
          "offset": "表示资源在文件中的偏移地址。",
          "size": "表示资源的大小。",
          "filetype": "表示资源的文件类型。",
          "language": "表示资源的语言。",
          "sublanguage": "表示资源的子语言。",
          "entropy": "表示资源的熵值，用于衡量内容的随机性。"
        },
        {
          "......": "省略，上面内容的重复"
        }
      ],
      "versioninfo": [
        {
          "name": "表示版本信息的属性名称。",
          "value": "表示该属性的具体值。"
        },
        {
          "......": "省略，上面内容的重复"
        }
      ],
      "imphash": "导入哈希值，用于唯一标识文件的导入表。",
      "timestamp": "文件的编译时间戳。",
      "icon": "图标相关的信息。",
      "icon_hash": "表示图标的哈希值，通常是对图标数据计算出的唯一标识符（如 MD5 或 SHA256 哈希值）。",
      "icon_fuzzy": "表示图标的模糊哈希值（Fuzzy Hash），用于检测图标之间的相似性。",
      "icon_dhash": "表示图标的差分哈希值（Difference Hash，简称 dHash），用于快速比较图标的视觉相似性。"
    },
    "data": "表示文件的内容",
    "strings": ["文件中提取的字符串"],
    "virustotal": {
      "error": "连接virustotal的查询状态",
      "msg": "查询的详细信息或者错误信息"
    },
    "cape_type_code": "分析任务的ID",
    "cape_type": "文件的类型",
    "process_path": "表示与此 payload 相关联的进程路径",
    "process_name": "表示与此 payload 相关联的进程名称",
    "module_path": "表示此 payload 加载到的模块路径。",
    "pid": "释放文件的进程 ID"
  },
  {
    "......": "省略，此处有多个上述内容"

  }
}
```



### 2.2.10、behavior

```json
{
  "behavior": {//恶意样本分析中的 行为（Behavior） 部分。该部分记录了样本在运行过程中产生的进程树、文件操作、注册表操作、API 调用等行为。
    "processtree": [//进程树
      {
        "name": "主进程的名称。",
        "pid": 2924,//主进程的 PID
        "parent_id": 2220,//父进程的 ID。
        "module_path": "表明主进程的可执行文件位于用户的临时目录中。",
        "children": [//子进程列表。
          {
            "name": "子进程的名称",
            "pid": 1608,//子进程的 PID
            "parent_id": 2924,//子进程的 ID
            "module_path": "子进程的可执行文件位于用户的临时目录中。",
            "children": [],//子进程列表。
            "threads": [
              "子进程中包含线程 ID 列表"
            ],
            "environ": {
              "UserName": "运行该子进程的用户名",
              "ComputerName": "运行该子进程的计算机名称",
              "WindowsPath": "系统目录的路径。",
              "TempPath": "临时文件夹的路径",
              "CommandLine": "启动该子进程的命令行参数。",
              "RegisteredOwner": "系统的注册所有者",
              "RegisteredOrganization": "系统的注册组织。",
              "ProductName": "操作系统的名称或产品名称。",
              "SystemVolumeSerialNumber": "系统卷的序列号。",
              "SystemVolumeGUID": "系统卷的全局唯一标识符（GUID）",
              "MachineGUID": "计算机的全局唯一标识符（GUID）",
              "MainExeBase": "可执行文件的基地址",
              "MainExeSize": "可执行文件的大小。",
              "Bitness": "进程的位数",
              "DllBase": "动态链接库（DLL）的基地址。"
            }
          }
        ],
        "threads": [
          "主进程中包含线程 ID 列表"
        ],
        "environ": {
          "UserName": "运行该主进程的用户名",
          "ComputerName": "运行该主进程的计算机名称",
          "WindowsPath": "系统目录的路径",
          "TempPath": "临时文件夹的路径",
          "CommandLine": "启动该主进程的命令行参数。",
          "RegisteredOwner": "系统的注册所有者",
          "RegisteredOrganization": "系统的注册组织。",
          "ProductName": "操作系统的名称或产品名称。",
          "SystemVolumeSerialNumber": "系统卷的序列号。",
          "SystemVolumeGUID": "系统卷的全局唯一标识符（GUID）",
          "MachineGUID": "计算机的全局唯一标识符（GUID）",
          "MainExeBase": "可执行文件的基地址",
          "MainExeSize": "可执行文件的大小",
          "Bitness": "进程的位数"
        }
      }
    ],
    "summary": {
      "files": [
        "样本访问的文件列表。"
      ],
      "delete_files": [
        "样本删除的文件列表。"
      ],
      "keys": [
        "样本访问的注册表键"
      ],
      "read_keys": [
        "样本读取的注册表键"
      ],
      "write_keys": [
        "样本写入的注册表键"
      ],
      "resolved_apis": [
        "样本调用的 API 函数。"
      ],
      "mutexes": [
        "样本创建或使用的互斥体（Mutex）。"
      ],
      "created_services": [
        "样本创建的服务"
      ],
      "started_services": [
        "样本启动的服务"
      ]
    }
  }
}
```



# 3、htmlsummary和html

## 3.1、报告基本格式

`reporthtmlsummary`报告信息：

```
报告命名格式为：恶意样本分析任务ID_summary-report.html
报告文件类型为：.html
报告文件大小为：5832kb
```



`reporthtml`报告信息：

```
报告命名格式为：恶意样本分析任务ID_report.html
报告文件类型为：.html
报告文件大小为：7031kb
```



## 3.2、报告内容解析

### 3.2.1、Info

这部分内容包括恶意样本的文件类型、分析的开始时间、结束时间、持续时间、分析引擎的版本，以及分析机器的名称、标签、管理程序、开机时间、关机时间等信息。

![image-20250318132707488](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503181327654.png)



### 3.2.2、Signatures

这部分内容为恶意文件在执行过程中进行的一系列操作以及进程信息。

- `reporthtmlsummary报告`对详情信息进行了展开。

![image-20250318133337689](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503181333927.png)



- `reporthtml报告`没有展开详情，点击相关模块也没有展开。

![image-20250318135630288](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503181356457.png)



### 3.2.3、Screenshots

这部分内容为CAPEv2在恶意文件在执行过程中对机器屏幕的截图。

- 在`reporthtmlsummary报告`和`reporthtml报告`中这部分都没有显示出来，实际是有的。

![image-20250318133652762](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503181336880.png)

![image-20250318133721634](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503181337826.png)



### 3.2.4、Network Analysis

这部分内容为恶意文件在执行过程中的一系列网络行为。

![image-20250318133955844](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503181339003.png)



### 3.2.5、Dropped Files

这部分内容为恶意文件在执行过程中删除的文件信息。

- `reporthtmlsummary报告`仅仅展示出来相关的文件名称。

  ![image-20250318140259348](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503181405829.png)



- `reporthtml报告`除了展示文件名称还展示出来文件的详细信息（需要点击查看）

  ![image-20250318140507855](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503181405062.png)



### 3.2.6、Payloads

这部分内容为恶意文件在执行过程中提取出的有效载荷（payload）。

- `reporthtmlsummary报告`仅仅展示出来有效载荷的哈希值。

  ![image-20250318140832274](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503181408438.png)



- `reporthtml报告`除了展示出有效载荷的哈希值，还展示出详细信息（需要点击查看）。

  ![image-20250318140931031](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503181409212.png)



### 3.2.7、Behavior Summary

这部分内容对恶意文件在执行过程中的行为进行了总结分析。

包括`Mutexes（互斥量）`、`Executed Commands（执行的命令）`、`Created Services（创建的服务）`、`Started Services（启动的服务）`等信息。

![image-20250318141418446](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503181414653.png)



### 3.2.8、Processes

这部分内容对恶意文件在执行过程中的进程信息、对文件的操作信息、注册表信息以及API信息。

包括`Accessed Files（访问的文件）`、`Read Files（读取文件）`、`Modified Files（修改文件）`、`Deleted Files（删除文件）`、`Registry Keys（注册表键值）`、`Read Registry Keys（读取注册表项）`、`Modified Registry Keys（修改注册表项）`、`Deleted Registry Keys（删除注册表项）`、`Resolved APIs（解析的API）`、



- 进程信息`reporthtmlsummary报告`仅仅展示出程序名称、进程ID和父进程ID。

  ![image-20250318144127150](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503181441902.png)



- 进程信息`reporthtml报告`展示出程序名称、进程ID、父进程ID、以及命令信息。

  ![image-20250318144423422](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503181444603.png)



# 4、reportpdf

## 4.1、报告基本格式

`reportpdf`报告信息：

```
报告命名格式为：恶意样本分析任务ID_report.pdf
报告文件类型为：.pdf
报告文件大小为：358kb
```



## 4.2、报告内容解析

`reportpdf`格式的报告其内容与`htmlsummary`相同。

但是我在观察报告的过程中发现`reportpdf`格式的报告中`Processes`部分缺少一些内容。