# 1、CAPEv2接口说明

## 1.1、添加文件到待处理任务列表

| **描述接口名称：** | 提交文件任务以供 CAPEv2 分析。返回对象将为 JSON。 |
|--------------------|---------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/create/file/           |
| **接口方式：**     | POST                                              |



**参数说明**

| **类别** | **参数**        | **是否必填** | **数据类型** | **说明**                                                                       |
|----------|-----------------|--------------|--------------|--------------------------------------------------------------------------------|
| 入参     | file            | 是           | 字符串       | 上传文件的实际文件路径。                                                       |
|          | package         | 否           | 字符串       | 用于分析的分析包。                                                             |
|          | timeout         | 否           | 整型         | 分析超时（以秒为单位）。                                                       |
|          | priority        | 否           | 整型         | 分配给任务的优先级 （1-3）                                                     |
|          | options         | 否           | 字符串       | 要传递给分析包的选项                                                           |
|          | machine         | 否           | 字符串       | 用于分析的分析计算机的ID。                                                     |
|          | platform        | 否           | 字符串       | 要从中选择分析计算机的平台名称（例如“Windows”）。                              |
|          | tags            | 否           | 字符串       | 定义要通过tags启动的计算机。必须将 platform 设置为使用该 API。标签以逗号分隔。 |
|          | custom          | 否           | 字符串       | 用于传递Analysis和 Processing/Reporting 模块的自定义字符串                     |
|          | memory          | 否           | 字符串       | 启用创建分析计算机的完整内存转储                                               |
|          | enforce_timeout | 否           | 字符串       | 启用此选项可强制执行完整超时值                                                 |
|          | clock           | 否           | 字符串       | 设置虚拟机时钟（格式 %m-%d-%Y %H:%M:%S）。                                     |
| 出参     | task_id         | 是           | 对象         | 新创建任务的 ID。                                                              |



**响应参数示例**

```
{
    "task_id" : 1
}
```



## 1.2、从VirusTotal下载文件进行分析

| **描述接口名称：** | 从VirusTotal下载文件进行分析。返回对象将为 JSON。 |
|--------------------|---------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/create/vtdl/           |
| **接口方式：**     | POST                                              |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明**                         |
|----------|----------|--------------|--------------|----------------------------------|
| 入参     | vtdl     | 是           | 字符串       | 需要检测VirusTotal上的文件hash。 |
|          | apikey   | 是           | 字符串       | VirusTotal的APIKEY。             |
|          | machine  | 否           | 字符串       | 用于分析的分析计算机的ID。       |
| 出参     | task_id  | 是           | 对象         | 文件分析任务ID                   |



**响应参数示例**

```
{
    "task_id" : 1
}
```



## 1.3、添加URL到待处理任务列表

| **描述接口名称：** | 提交 URL 任务以供 CAPEv2 分析。返回对象将为 JSON。 |
|--------------------|----------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/create/url/             |
| **接口方式：**     | POST                                               |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明**            |
|----------|----------|--------------|--------------|---------------------|
| 入参     | url      | 是           | 字符串       | 需要检测的URL地址。 |
| 出参     | task_id  | 是           | 对象         | 文件分析任务ID      |



**响应参数示例**

```
{
    "task_id" : 1
}
```



## 1.4、提交URL将下载的文件添加到待处理任务列表

| **描述接口名称：** | 提交一个 URL，它将下载并添加为文件任务，以供 CAPEv2 分析。返回对象将为 JSON。 |
|--------------------|-------------------------------------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/create/dlnexec/                                    |
| **接口方式：**     | POST                                                                          |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明**            |
|----------|----------|--------------|--------------|---------------------|
| 入参     | dlnexec  | 是           | 字符串       | 需要检测的URL地址。 |
| 出参     | task_id  | 是           | 对象         | 文件分析任务ID      |



**响应参数示例**

```
{
    "task_id" : 1
}
```



## 1.5、添加文件进行静态分析

| **描述接口名称：** | 提交文件以在其上尝试静态提取器，文件不会提交到虚拟机。返回对象将为 JSON。 |
|--------------------|---------------------------------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/create/static/                                 |
| **接口方式：**     | POST                                                                      |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明**                   |
|----------|----------|--------------|--------------|----------------------------|
| 入参     | file     | 是           | 字符串       | 上传文件的实际文件路径。   |
|          | machine  | 否           | 字符串       | 用于分析的分析计算机的ID。 |
| 出参     | 未知     | 未知         | 未知         | 未知                       |



**响应参数示例**

| 未知 |
|------|



## 1.6、查看文件信息

| **描述接口名称：** | 查看有关特定样本的信息，了解它是 CAPEv2 的数据库。返回对象将为 JSON。 |
| ------------------ | ------------------------------------------------------------ |
| **接口URL：**      | http://ip:port/apiv2/files/view/md5/[md5_hash]               |
|                    | http://ip:port/apiv2/files/view/sha1/[sha1_hash]             |
|                    | http://ip:port/apiv2/files/view/sha256/[sha256_hash]         |
|                    | http://ip:port/apiv2/files/view/id/[sample_id]               |
| **接口方式：**     | GET                                                          |



**参数说明**

| **类别** | **参数**    | **是否必填** | **数据类型** | **说明**             |
|----------|-------------|--------------|--------------|----------------------|
| 入参     | md5_hash    | 是           | 字符串       | 查询文件的md5值。    |
|          | sha1_hash   | 是           | 字符串       | 查询文件的sha1值。   |
|          | sha256_hash | 是           | 字符串       | 查询文件的sha256值。 |
|          | sample_id   | 是           | 整型         | 查询文件的任务ID。   |
| 出参     | error       | 是           | 字符串       | 请求状态信息。       |
|          | data        | 是           | 对象         | 请求返回的详细信息。 |



**响应参数示例**

```
{
    "error": false,
    "data": {
        "id": 22,
        "file_size": 3222528,
        "file_type": "PE32 executable (GUI) Intel 80386, for MS Windows",
        "md5": "78d71ffaa0f59c0ecc989c2ff0d9c04a",
        "crc32": "FBF12052",
        "sha1": "d0528b8777d556809ac64935fa6247164aaabb4f",
        "sha256": "35c37ed14d74a846325af0f003c85bb12e103333290c5bd80012f2bb146c2407",
        "sha512": "cf6cd7e226f845d94c894f5d3c23e9adc5a3a9974611e27fa9f68aea09c9fcf378fd168f5e088a236c8bcedd2c20de1d78d65da23a7a186c80fa112e1a793989",
        "ssdeep": "49152:PXxLl8DFTFVHWgx7KI+F5OXGMLClr+AG1uR6VMPRPZ+0eX0cS6DoaoNZUBaa:PXxLVUWgC9+eR6Qj+HoaoNZUBaa",
        "parent": null,
        "source_url": null
    }
}
```



## 1.7、基本任务搜索

| **描述接口名称：** | 按 Hash查看 CAPEv2 数据库中特定 Task 的相关信息。返回对象将为 JSON。 |
| ------------------ | ------------------------------------------------------------ |
| **接口URL：**      | http://ip:port/apiv2/tasks/search/md5/[md5_hash]             |
|                    | http://ip:port/apiv2/tasks/search/sha1/[sha1_hash]           |
|                    | http://ip:port/apiv2/tasks/search/sha256/[sha256_hash]       |
| **接口方式：**     | GET                                                          |



**参数说明**

| **类别** | **参数**    | **是否必填** | **数据类型** | **说明**             |
|----------|-------------|--------------|--------------|----------------------|
| 入参     | md5_hash    | 是           | 字符串       | 查询文件的md5值。    |
|          | sha1_hash   | 是           | 字符串       | 查询文件的sha1值。   |
|          | sha256_hash | 是           | 字符串       | 查询文件的sha256值。 |
| 出参     | error       | 是           | 字符串       | 请求状态信息。       |
|          | data        | 是           | 对象         | 请求返回的详细信息。 |



**响应参数示例**

```
{
    "error": false,
    "data": [
        {
            "id": 23,
            "target": "wannacry.exe",
            "category": "file",
            "timeout": 200,
            "priority": 3,
            "custom": "",
            "machine": "cuckoo1",
            "package": "Unpacker",
            "options": "route=tor",
            "platform": "windows",
            "memory": false,
            "enforce_timeout": false,
            "clock": "2025-03-03 07:40:48",
            "added_on": "2025-03-03 15:40:39",
            "started_on": "2025-03-03 15:40:40",
            "completed_on": "2025-03-03 15:44:28",
            "status": "reported",
            "dropped_files": null,
            "running_processes": null,
            "api_calls": null,
            "domains": null,
            "signatures_total": null,
            "signatures_alert": null,
            "files_written": null,
            "registry_keys_modified": null,
            "crash_issues": null,
            "anti_issues": null,
            "analysis_started_on": null,
            "analysis_finished_on": null,
            "processing_started_on": null,
            "processing_finished_on": null,
            "signatures_started_on": null,
            "signatures_finished_on": null,
            "reporting_started_on": null,
            "reporting_finished_on": null,
            "timedout": false,
            "sample_id": 22,
            "machine_id": 5,
            "shrike_url": null,
            "shrike_refer": null,
            "shrike_msg": null,
            "shrike_sid": null,
            "parent_id": 22,
            "tlp": null,
            "tags": []
        }
}
```



## 1.8、扩展任务搜索

| **描述接口名称：** | 通过各种选项搜索 MongoDB 中的任务 ID。返回对象将为 JSON。 |
|--------------------|-----------------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/extendedsearch                 |
| **接口方式：**     | POST                                                      |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明** |
|----------|----------|--------------|--------------|----------|
| 入参     | options  | 是           | 字符串       | 未知     |
|          | argument | 是           | 字符串       | 未知     |
| 出参     | 未知     | 未知         | 未知         | 未知     |



**可搜索选项列表**

```
id : Task id
name : Name of target file name
type : Name of file type
string : Match a string in the static analysis section
ssdeep : Match an ssdeep hash
crc32 : Match a CRC32 hash
file : Match a file in the behavioral analysis summary
command : Match an executed command
resolvedapi : Match an API that a sample resolved
key : Match a registry key in the behavioral analysis summary
mutex : Match a mutex in the behavioral analysis summary
domain : Match a resolved domain
ip : Match a contacted IP Address
signature : Match a CAPE signature description
signame : Match a CAPE signature name
detections: Match samples associated with malware family
url : Match a URL target task (submitted URL task)
imphash : Match an import hash
iconhash: Match the exact hash of the icon associated with the PE
iconfuzzy: Match a hash designed to match on similar-looking icons
surialert : Match a suricata alert signature
surihttp : Match suricata HTTP data
suritls : Match suricata TLS data
clamav : Match a Clam AV signature
yaraname : Match a Yara signature name
virustotal : Match a virustotal AV Signature
comment : Match a comment posted to a specific task
md5 : Targets with a specific MD5 hash
sha1 : Targets with a specific SHA1 hash
sha256 : Targets with a specific SHA256 hash
sha512 : Targets with a specific SHA512 hash
TTP: TTP number
```



**响应参数示例**

| 未知 |
|------|



## 1.9、获取任务列表

| **描述接口名称：** | 查看有关任务 ID 范围的信息。返回对象将为 JSON         |
|--------------------|-------------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/list/\[limit\]/\[offset\]/ |
| **接口方式：**     | GTE                                                   |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明**             |
|----------|----------|--------------|--------------|----------------------|
| 入参     | limit    | 否           | 整型         | 返回任务的最大数量。 |
|          | offset   | 否           | 整型         | 数据偏移量。         |
| 出参     | data     | 是           | 对象         | 所查询的分析任务。   |



**响应参数示例**

```
{
    "data": [
        {
            "id": 23,
            "target": "/tmp/cuckoo-tmp/upload_o8_dlx55/wannacry.exe",
            "category": "file",
            "timeout": 200,
            "priority": 3,
            "custom": "",
            "machine": "cuckoo1",
            "package": "Unpacker",
            "options": "route=tor",
            "platform": "windows",
            "memory": false,
            "enforce_timeout": false,
            "clock": "2025-03-03 07:40:48",
            "added_on": "2025-03-03 15:40:39",
            "started_on": "2025-03-03 15:40:40",
            "completed_on": "2025-03-03 15:44:28",
            "status": "reported",
            "dropped_files": null,
            "running_processes": null,
            "api_calls": null,
            "domains": null,
            "signatures_total": null,
            "signatures_alert": null,
            "files_written": null,
            "registry_keys_modified": null,
            "crash_issues": null,
            "anti_issues": null,
            "analysis_started_on": null,
            "analysis_finished_on": null,
            "processing_started_on": null,
            "processing_finished_on": null,
            "signatures_started_on": null,
            "signatures_finished_on": null,
            "reporting_started_on": null,
            "reporting_finished_on": null,
            "timedout": false,
            "sample_id": 22,
            "machine_id": 5,
            "shrike_url": null,
            "shrike_refer": null,
            "shrike_msg": null,
            "shrike_sid": null,
            "parent_id": 22,
            "tlp": null,
            "tags": [],
            "guest": {
                "id": 16,
                "status": "stopping",
                "name": "cuckoo1",
                "label": "cuckoo1",
                "manager": "VirtualBox",
                "started_on": "2025-03-03 15:40:40",
                "shutdown_on": "2025-03-03 15:44:28",
                "task_id": 23
            },
            "errors": [],
            "sample": {
                "id": 22,
                "file_size": 3222528,
                "file_type": "PE32 executable (GUI) Intel 80386, for MS Windows",
                "md5": "78d71ffaa0f59c0ecc989c2ff0d9c04a",
                "crc32": "FBF12052",
                "sha1": "d0528b8777d556809ac64935fa6247164aaabb4f",
                "sha256": "35c37ed14d74a846325af0f003c85bb12e103333290c5bd80012f2bb146c2407",
                "sha512": "cf6cd7e226f845d94c894f5d3c23e9adc5a3a9974611e27fa9f68aea09c9fcf378fd168f5e088a236c8bcedd2c20de1d78d65da23a7a186c80fa112e1a793989",
                "ssdeep": "49152:PXxLl8DFTFVHWgx7KI+F5OXGMLClr+AG1uR6VMPRPZ+0eX0cS6DoaoNZUBaa:PXxLVUWgC9+eR6Qj+HoaoNZUBaa",
                "parent": null,
                "source_url": null
            }
        }
    ],
    "config": "Limit: 1, Offset: 1",
    "buf": 1
}
```



## 1.10、获取任务视图

| **描述接口名称：** | 查看有关特定任务的信息，包括 VM、示例和错误信息。返回对象将为 JSON。 |
|--------------------|----------------------------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/view/\[task_id\]/                         |
| **接口方式：**     | GTE                                                                  |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明**             |
|----------|----------|--------------|--------------|----------------------|
| 入参     | task_id  | 是           | 整型         | 需要查询的任务ID。   |
| 出参     | error    | 是           | 字符串       | 请求状态信息。       |
|          | data     | 是           | 对象         | 请求返回的详细信息。 |



**响应参数示例**

```
{
    "error": false,
    "data": {
        "id": 23,
        "target": "wannacry.exe",
        "category": "file",
        "timeout": 200,
        "priority": 3,
        "custom": "",
        "machine": "cuckoo1",
        "package": "Unpacker",
        "options": "route=tor",
        "platform": "windows",
        "memory": false,
        "enforce_timeout": false,
        "clock": "2025-03-03 07:40:48",
        "added_on": "2025-03-03 15:40:39",
        "started_on": "2025-03-03 15:40:40",
        "completed_on": "2025-03-03 15:44:28",
        "status": "reported",
        "dropped_files": null,
        "running_processes": null,
        "api_calls": null,
        "domains": null,
        "signatures_total": null,
        "signatures_alert": null,
        "files_written": null,
        "registry_keys_modified": null,
        "crash_issues": null,
        "anti_issues": null,
        "analysis_started_on": null,
        "analysis_finished_on": null,
        "processing_started_on": null,
        "processing_finished_on": null,
        "signatures_started_on": null,
        "signatures_finished_on": null,
        "reporting_started_on": null,
        "reporting_finished_on": null,
        "timedout": false,
        "sample_id": 22,
        "machine_id": 5,
        "shrike_url": null,
        "shrike_refer": null,
        "shrike_msg": null,
        "shrike_sid": null,
        "parent_id": 22,
        "tlp": null,
        "tags": [],
        "guest": {
            "id": 16,
            "status": "stopping",
            "name": "cuckoo1",
            "label": "cuckoo1",
            "manager": "VirtualBox",
            "started_on": "2025-03-03 15:40:40",
            "shutdown_on": "2025-03-03 15:44:28",
            "task_id": 23
        },
        "errors": [],
        "sample": {
            "id": 22,
            "file_size": 3222528,
            "file_type": "PE32 executable (GUI) Intel 80386, for MS Windows",
            "md5": "78d71ffaa0f59c0ecc989c2ff0d9c04a",
            "crc32": "FBF12052",
            "sha1": "d0528b8777d556809ac64935fa6247164aaabb4f",
            "sha256": "35c37ed14d74a846325af0f003c85bb12e103333290c5bd80012f2bb146c2407",
            "sha512": "cf6cd7e226f845d94c894f5d3c23e9adc5a3a9974611e27fa9f68aea09c9fcf378fd168f5e088a236c8bcedd2c20de1d78d65da23a7a186c80fa112e1a793989",
            "ssdeep": "49152:PXxLl8DFTFVHWgx7KI+F5OXGMLClr+AG1uR6VMPRPZ+0eX0cS6DoaoNZUBaa:PXxLVUWgC9+eR6Qj+HoaoNZUBaa",
            "parent": null,
            "source_url": null
        }
    }
}
```



## 1.11、重新计划任务

| **描述接口名称：** | 重新计划分析任务以再次运行。返回对象将为 JSON。    |
|--------------------|----------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/reschedule/\[task_id\]/ |
| **接口方式：**     | GTE                                                |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明**             |
|----------|----------|--------------|--------------|----------------------|
| 入参     | task_id  | 是           | 整型         | 需要重置的任务ID。   |
| 出参     | error    | 是           | 字符串       | 请求状态信息。       |
|          | data     | 是           | 对象         | 请求返回的详细信息。 |



**响应参数示例**

```
{
    "error": false,
    "data": {
        "new_task_id": 12,
        "message": "Task ID 1 has been rescheduled"
    }
}
```



## 1.12、重新处理任务

| **描述接口名称：** | 将分析任务标记为 ready to be processing。返回对象将为 JSON。 |
|--------------------|--------------------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/reprocess/\[task_id\]/            |
| **接口方式：**     | GTE                                                          |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明**             |
|----------|----------|--------------|--------------|----------------------|
| 入参     | task_id  | 是           | 整型         | 需要重置的任务ID。   |
| 出参     | error    | 是           | 字符串       | 请求状态信息。       |
|          | data     | 是           | 对象         | 请求返回的详细信息。 |



**响应参数示例**

```
{
    "error": false,
    "data": "Task ID 2 with status reported marked for reprocessing"
}
```



## 1.13、删除任务

| **描述接口名称：** | 从数据库中删除任务。返回对象将为 JSON。                      |
| ------------------ | ------------------------------------------------------------ |
| **接口URL：**      | http://ip:port/apiv2/tasks/delete/[task_id]/[status]         |
|                    | http://ip:port/apiv2/tasks/delete/[task_id],[task id]/[status] |
|                    | http://ip:port/apiv2/tasks/delete/[start_task_id]-[end_task_id]/[status] |
| **接口方式：**     | GET                                                          |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明**                   |
|----------|----------|--------------|--------------|----------------------------|
| 入参     | status   | 否           | 字符串       | 指定作业失败时的确切状态。 |
|          | task_id  | 是           | 整型         | 需要删除的任务ID。         |
| 出参     | error    | 是           | 字符串       | 请求状态信息。             |
|          | failed   | 是           | 对象         | 请求返回的详细信息。       |



**响应参数示例**

```
{
    "error": true,
    "failed": "Task(s) ID(s) 23 failed to remove"
}
```



## 1.14、获取任务状态

| **描述接口名称：** | 通过 ID 查询 Task 的状态，返回对象为 JSON。    |
|--------------------|------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/status/\[task_id\]/ |
| **接口方式：**     | GTE                                            |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明**             |
|----------|----------|--------------|--------------|----------------------|
| 入参     | task_id  | 是           | 整型         | 需要查询的任务ID。   |
| 出参     | error    | 是           | 字符串       | 请求状态信息。       |
|          | failed   | 是           | 对象         | 请求返回的详细信息。 |



**响应参数示例**

```
{
    "error": false,
    "data": "reported"
}
```



## 1.15、获取任务报告

| **描述接口名称：** | 下载为特定任务生成的报告。返回对象将为 JSON、XML 或 application/zip （zip）。 |
| ------------------ | ------------------------------------------------------------ |
| **接口URL：**      | http://ip:port/apiv2/tasks/get/report/[task_id]/[format]/    |
|                    | http://ip:port/apiv2/tasks/get/report/[task_id]/[format]/zip/ |
|                    | http://ip:port/apiv2/tasks/get/report/all                    |
| **接口方式：**     | GET                                                          |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明**                                                     |
| -------- | -------- | ------------ | ------------ | ------------------------------------------------------------ |
| 入参     | task_id  | 是           | 整型         | 要获取其报告的任务的ID。                                     |
|          | format   | 否           | 字符串       | 用于检索 [json/maec/maec5/metadata/lite/all] 的报告格式。如果未指定任何内容，则将返回 JSON 报告。all将所有结果文件返回为 tar.bz2。请求返回的详细信息。 |



**响应参数示例**

| 下载报告 |
|----------|



## 1.16、获取任务 IOC

| **描述接口名称：** | 查看任务报告中可能包含潜在 IOC 的对象。返回对象将为 JSON。 |
| ------------------ | ---------------------------------------------------------- |
| **接口URL：**      | http://ip:port/apiv2/tasks/get/iocs/[task_id]              |
|                    | http://ip:port/apiv2/tasks/get/iocs/[task_id]/detailed/    |
| **接口方式：**     | GET                                                        |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明**                 |
|----------|----------|--------------|--------------|--------------------------|
| 入参     | task_id  | 是           | 整型         | 要获取其报告的任务的ID。 |
| 出参     | 未知     | 未知         | 未知         | 未知                     |



**响应参数示例**

| 未知 |
|------|



## 1.17、获取屏幕截图

| **描述接口名称：** | 根据ID查看/下载任务的屏幕截图。返回对象将为 image/jpeg 或 octet-stream。（.jpg 或 .tar.bz2） |
|--------------------|----------------------------------------------------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/get/screenshot/\[task_id\]/\[screenshot_number\]                  |
| **接口方式：**     | GTE                                                                                          |



**参数说明**

| **类别** | **参数**          | **是否必填** | **数据类型** | **说明**                                      |
|----------|-------------------|--------------|--------------|-----------------------------------------------|
| 入参     | task_id           | 是           | 整型         | 要获取其报告的任务的 ID。                     |
|          | screenshot_number | 否           | 整型         | 单个屏幕截图的数字标识符（例如 0001、0002）。 |
| 出参     | data              | 是           | 字符串       | 详情信息。                                    |



**响应参数示例**

| 未知 |
|------|



## 1.18、获取任务 PCAP

| **描述接口名称：** | 根据ID下载任务的PCAP。返回对象将为application/vnd.tcpdump.pcap。（.pcap） |
|--------------------|---------------------------------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/get/pcap/\[task_id\]/                          |
| **接口方式：**     | GTE                                                                       |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明**                  |
|----------|----------|--------------|--------------|---------------------------|
| 入参     | task_id  | 是           | 整型         | 要获取其报告的任务的 ID。 |



**响应参数示例**

| 下载pcap包 |
|------------|



## 1.19、获取任务放置的文件

| **描述接口名称：** | 根据ID下载与Task关联的已删除文件。返回对象将为 octet-stream。（.tar.bz2） |
|--------------------|---------------------------------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/get/dropped/\[task_id\]/                       |
| **接口方式：**     | GTE                                                                       |



**参数说明**

| **类别** | **参数**    | **是否必填** | **数据类型** | **说明**                  |
|----------|-------------|--------------|--------------|---------------------------|
| 入参     | task_id     | 是           | 整型         | 要获取其报告的任务的 ID。 |
| 出参     | error       | 是           | 字符串       | 详情信息。                |
|          | error_value | 是           | 字符串       | 详情信息。                |



**响应参数示例**

```
响应1
{
    "error": true,
    "error_value": "No files dropped for task 22"
}

响应2
下载文件
```



## 1.20、获取任务SuriFiles

| **描述接口名称：** | 根据ID下载与任务关联的 Suricata 捕获的文件。返回对象将为八位字节流。（.zip） |
|--------------------|------------------------------------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/get/surifile/\[task_id\]/                         |
| **接口方式：**     | GTE                                                                          |



**参数说明**

| **类别** | **参数**    | **是否必填** | **数据类型** | **说明**                  |
|----------|-------------|--------------|--------------|---------------------------|
| 入参     | task_id     | 是           | 整型         | 要获取其报告的任务的 ID。 |
| 出参     | error       | 是           | 字符串       | 详情信息。                |
|          | error_value | 是           | 字符串       | 详情信息。                |



**响应参数示例**

```
响应1
{
    "error": true,
    "error_value": "No suricata files captured for task 2"
}

响应2
下载文件
```



## 1.21、任务进程转储

| **描述接口名称：** | 根据ID和 PID 从 Task 下载进程内存转储。返回对象将是 octet-stream。（.dmp 或 .tar.bz2） |
|--------------------|----------------------------------------------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/get/procmemory/\[task_id\]/\[pid\]/                         |
| **接口方式：**     | GTE                                                                                    |



**参数说明**

| **类别** | **参数**    | **是否必填** | **数据类型** | **说明**                  |
|----------|-------------|--------------|--------------|---------------------------|
| 入参     | task_id     | 是           | 整型         | 要获取其报告的任务的 ID。 |
|          | pid         | 否           | 整型         | 任务进程。                |
| 出参     | error       | 是           | 字符串       | 详情信息。                |
|          | error_value | 是           | 字符串       | 详情信息。                |



**响应参数示例**

```
响应1
{
    "error": true,
    "error_value": "Task is still being analyzed"
}

响应2
下载文件
```



## 1.22、任务已满内存转储

| **描述接口名称：** | 根据ID下载 Task 的完整内存转储，返回对象将为 octet-stream。（.dmp） |
|--------------------|---------------------------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/get/fullmemory/\[task_id\]/              |
| **接口方式：**     | GTE                                                                 |



**参数说明**

| **类别** | **参数**    | **是否必填** | **数据类型** | **说明**                  |
|----------|-------------|--------------|--------------|---------------------------|
| 入参     | task_id     | 是           | 整型         | 要获取其报告的任务的 ID。 |
| 出参     | error       | 是           | 字符串       | 详情信息。                |
|          | error_value | 是           | 字符串       | 详情信息。                |



**响应参数示例**

```
响应1
{
    "error": true,
    "error_value": "Memory dump not found for task 21"
}

响应2
下载文件
```



## 1.23、样本下载

| **描述接口名称：** | 按任务 ID/MD5/SHA1/SHA256 从任务中下载示例。支持使用默认密码进行 zip 加密。返回对象将是 octet-stream。（.bin） 或 application/zip。（.zip） 如果已加密 |
| ------------------ | ------------------------------------------------------------ |
| **接口URL：**      | http://ip:port/apiv2/files/get/task/[task_id]/?encrypted=1   |
|                    | http://ip:port/apiv2/files/get/md5/[md5_hash]/?encrypted=1   |
|                    | http://ip:port/apiv2/files/get/sha1/[sha1_hash] /?encrypted=1 |
|                    | http://ip:port/apiv2/files/get/sha256/[sha256_hash] /?encrypted=1 |
| **接口方式：**     | GET                                                          |



**参数说明**

| **类别** | **参数**     | **是否必填** | **数据类型** | **说明**             |
|----------|--------------|--------------|--------------|----------------------|
| 入参     | task_id      | 是           | 整型         | 查询文件的任务ID。   |
|          | md5_hash     | 是           | 字符串       | 查询文件的md5值。    |
|          | sha1_hash    | 是           | 字符串       | 查询文件的sha1值。   |
|          | sha256_hash  | 是           | 字符串       | 查询文件的sha256值。 |
| 备注     | ?encrypted=1 | 否           | 字符串       | 未知                 |
| 出参     | error        | 是           | 字符串       | 请求状态信息。       |
|          | data         | 是           | 对象         | 请求返回的详细信息。 |



**响应参数示例**

| 下载样本文件 |
|--------------|



## 1.24、获取虚拟机列表

| **描述接口名称：** | 查看可用虚拟机/虚拟机总数的基本统计信息。返回对象将为 JSON。 |
|--------------------|--------------------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/machines/list/                          |
| **接口方式：**     | GTE                                                          |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明**             |
|----------|----------|--------------|--------------|----------------------|
| 出参     | error    | 是           | 字符串       | 请求状态信息。       |
|          | data     | 是           | 对象         | 请求返回的详细信息。 |



**响应参数示例**

```
{
    "data": [
        {
            "id": 7,
            "name": "win7",
            "label": "win7",
            "arch": "x64",
            "ip": "192.168.55.150",
            "platform": "windows",
            "interface": "virbr1",
            "snapshot": null,
            "locked": false,
            "locked_changed_on": "2025-03-10 14:00:05",
            "status": "running",
            "status_changed_on": "2025-03-10 13:55:58",
            "resultserver_ip": "192.168.55.1",
            "resultserver_port": "2042",
            "reserved": false,
            "tags": []
        }
    ],
    "error": false
}
```



## 1.25、获取虚拟机视图

| **描述接口名称：** | 查看有关特定虚拟机的信息。返回对象将为 JSON。   |
|--------------------|-------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/machines/view/\[vm-name\]/ |
| **接口方式：**     | GTE                                             |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明**             |
|----------|----------|--------------|--------------|----------------------|
| 入参     | vm-name  | 是           | 字符串       | 虚拟机的名称。       |
| 出参     | error    | 是           | 字符串       | 请求状态信息。       |
|          | data     | 是           | 对象         | 请求返回的详细信息。 |



**响应参数示例**

```
{
    "data": {
        "id": 7,
        "name": "win7",
        "label": "win7",
        "arch": "x64",
        "ip": "192.168.55.150",
        "platform": "windows",
        "interface": "virbr1",
        "snapshot": null,
        "locked": false,
        "locked_changed_on": "2025-03-10 14:00:05",
        "status": "running",
        "status_changed_on": "2025-03-10 13:55:58",
        "resultserver_ip": "192.168.55.1",
        "resultserver_port": "2042",
        "reserved": false,
        "tags": []
    },
    "error": false
}
```



## 1.26、获取最近 X 小时内完成的任务 ID

| **描述接口名称：** | 根据任务ID/MD5/SHA1/SHA256 从任务中下载示例。返回对象将是 octet-stream。（.bin） |
|--------------------|----------------------------------------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/get/latests/\[hours\]                                 |
| **接口方式：**     | GTE                                                                              |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明**             |
|----------|----------|--------------|--------------|----------------------|
| 入参     | hours    | 是           | 整型         | 查询的时间范围。     |
| 出参     | error    | 是           | 字符串       | 请求状态信息。       |
|          | ids      | 是           | 对象         | 请求返回的详细信息。 |



**响应参数示例**

```
{
    "error": false,
    "ids": [
        {
            "id": 25,
            "target": "/tmp/cuckoo-tmp/upload_o8_dlx55/wannacry.exe",
            "category": "file",
            "timeout": 200,
            "priority": 3,
            "custom": "",
            "machine": "cuckoo1",
            "package": "Unpacker",
            "options": "route=tor",
            "platform": "windows",
            "memory": false,
            "enforce_timeout": false,
            "clock": "2025-03-03 07:40:48",
            "added_on": "2025-03-04 08:20:16",
            "started_on": "2025-03-04 16:20:17",
            "completed_on": "2025-03-04 16:24:54",
            "status": "reported",
            "dropped_files": null,
            "running_processes": null,
            "api_calls": null,
            "domains": null,
            "signatures_total": null,
            "signatures_alert": null,
            "files_written": null,
            "registry_keys_modified": null,
            "crash_issues": null,
            "anti_issues": null,
            "analysis_started_on": null,
            "analysis_finished_on": null,
            "processing_started_on": null,
            "processing_finished_on": null,
            "signatures_started_on": null,
            "signatures_finished_on": null,
            "reporting_started_on": null,
            "reporting_finished_on": null,
            "timedout": false,
            "sample_id": 22,
            "machine_id": 7,
            "shrike_url": null,
            "shrike_refer": null,
            "shrike_msg": null,
            "shrike_sid": null,
            "parent_id": null,
            "tlp": null,
            "tags": []
        }
    ]
}
```



## 1.27、获取CAPEv2 状态

| **描述接口名称：** | 查看 CAPEv2 主机的当前状态。返回对象将为 JSON。 |
|--------------------|-------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/cuckoo/status/             |
| **接口方式：**     | GTE                                             |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明**             |
|----------|----------|--------------|--------------|----------------------|
| 出参     | error    | 是           | 字符串       | 请求状态信息。       |
|          | data     | 是           | 对象         | 请求返回的详细信息。 |



**响应参数示例**

```
{
    "error": false,
    "data": {
        "version": "2.4-CAPE",
        "hostname": "AA",
        "machines": {
            "total": 1,
            "available": 1
        },
        "tasks": {
            "total": 10,
            "pending": 0,
            "running": 0,
            "completed": 0,
            "reported": 9
        },
        "server": {
            "storage": {
                "free": 110,
                "total": 146,
                "used": 27,
                "used_by": "20.1%"
            },
            "ram": {
                "free": 10,
                "total": 15,
                "used": 2
            }
        }
    }
}
```



## 1.28、提取示例配置

| **描述接口名称：** | 查看 CAPEv2 提取的示例配置。返回对象将为 JSON。    |
|--------------------|----------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/get/config/\[task_id\]/ |
| **接口方式：**     | GTE                                                |



**参数说明**

| **类别** | **参数**    | **是否必填** | **数据类型** | **说明**             |
|----------|-------------|--------------|--------------|----------------------|
| 入参     | task_id     | 是           | 整型         | 查询文件的任务ID。   |
| 出参     | error       | 是           | 字符串       | 请求状态信息。       |
|          | error_value | 是           | 对象         | 请求返回的详细信息。 |



**响应参数示例**

```
{
    "error": true,
    "error_value": "Unable to retrieve results for task 25."
}
```



## 1.29、获取CAPE 负载文件

| **描述接口名称：** | 查看 CAPEv2 提取的示例配置。返回对象将为 JSON。          |
|--------------------|----------------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/get/payloadfiles/\[task_id\]/ |
| **接口方式：**     | GTE                                                      |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明**           |
|----------|----------|--------------|--------------|--------------------|
| 入参     | task_id  | 是           | 整型         | 查询文件的任务ID。 |
| 出参     | 未知     | 未知         | 未知         | 未知               |



**响应参数示例**

| 未知 |
|------|



## 1.30、获取CAPE Procdump 文件

| **描述接口名称：** | 查看 CAPEv2 提取的示例配置。返回对象将为 JSON。           |
|--------------------|-----------------------------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/get/procdumpfiles/\[task_id\]/ |
| **接口方式：**     | GTE                                                       |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明**           |
|----------|----------|--------------|--------------|--------------------|
| 入参     | task_id  | 是           | 整型         | 查询文件的任务ID。 |
| 出参     | 未知     | 未知         | 未知         | 未知               |



**响应参数示例**

| 下载对应文件 |
|--------------|



## 1.31、统计

| **描述接口名称：** | 不同模块的任务和时间详情（TOP 30）   |
|--------------------|--------------------------------------|
| **接口URL：**      | http://ip:port/apiv2/tasks/\[days\]/ |
| **接口方式：**     | GTE                                  |



**参数说明**

| **类别** | **参数** | **是否必填** | **数据类型** | **说明**     |
|----------|----------|--------------|--------------|--------------|
| 入参     | days     | 是           | 整型         | 查询的天数。 |
| 出参     | 未知     | 未知         | 未知         | 未知         |



**响应参数示例**

| 未知 |
|------|



# 2、附录

参考链接：https://capev2.readthedocs.io/en/latest/index.html
