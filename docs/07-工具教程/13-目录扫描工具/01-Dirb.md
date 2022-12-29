Kali Linux提供一款基于字典的Web目录扫描工具DIRB。该工具根据用户提供的字典，对目标网站目录进行暴力猜测。它会尝试以递归方式进行爆破，以发现更多的路径。同时，该工具支持代理、HTTP认证扫描限制访问的网站。该工具还提供两个命令，帮助用户从网页生成字典，或者生成定制字典。
```
-----------------
DIRB v2.22
By The Dark Raver
-----------------

dirb <url_base> [<wordlist_file(s)>] [options]

========================= NOTES =========================
 <url_base> : Base URL to scan. (Use -resume for session resuming)
 <wordlist_file(s)> : List of wordfiles. (wordfile1,wordfile2,wordfile3...)

======================== HOTKEYS ========================
 'n' -> Go to next directory.
 'q' -> Stop scan. (Saving state for resume)
 'r' -> Remaining scan stats.

======================== OPTIONS ========================
 -a <agent_string> : Specify your custom USER_AGENT.
 -b : Use path as is.
 -c <cookie_string> : Set a cookie for the HTTP request.
 -E <certificate> : path to the client certificate.
 -f : Fine tunning of NOT_FOUND (404) detection.
 -H <header_string> : Add a custom header to the HTTP request.
 -i : Use case-insensitive search.
 -l : Print "Location" header when found.
 -N <nf_code>: Ignore responses with this HTTP code.
 -o <output_file> : Save output to disk.
 -p <proxy[:port]> : Use this proxy. (Default port is 1080)
 -P <proxy_username:proxy_password> : Proxy Authentication.
 -r : Don't search recursively.
 -R : Interactive recursion. (Asks for each directory)
 -S : Silent Mode. Don't show tested words. (For dumb terminals)
 -t : Don't force an ending '/' on URLs.
 -u <username:password> : HTTP Authentication.
 -v : Show also NOT_FOUND pages.
 -w : Don't stop on WARNING messages.
 -X <extensions> / -x <exts_file> : Append each word with this extensions.
 -z <millisecs> : Add a milliseconds delay to not cause excessive Flood.

======================== EXAMPLES =======================
 dirb http://url/directory/ (Simple Test)
 dirb http://url/ -X .html (Test files with '.html' extension)
 dirb http://url/ /usr/share/dirb/wordlists/vulns/apache.txt (Test with apache.txt wordlist)
 dirb https://secure_url/ (Simple Test with SSL)
```


# 扫描

## 使用默认字典扫描
```
dirb http://192.168.1.15
```
![image.png](_img/assets/1655877733526-66e995a1-8d16-4840-9cb8-f4309d0b97f0.png)

## 自定义字典扫描
```
dirb http://192.168.1.15 /root/zidian.txt
```
![image.png](_img/assets/1655877746406-2ce1c82c-4ea4-4602-b4c7-4a6ba975b920.png)


## 设置UA
```
dirb http://192.168.1.15 -a "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:75.0) Gecko/20100101 Firefox/75.0"
```
![image.png](_img/assets/1655877755225-56316d9c-ae44-44d3-a02b-eb133c64ecf0.png)

## 设置cookie
```
dirb http://192.168.1.15 -c "BAIDUID=D5C6351DAC89EF8811A51DF3A9A9C0C4:FG=1; HMACCOUNT=2906306413846532; BIDUPSID=D5C6351DAC89EF8811A51DF3A9A9C0C4; PSTM=1585744543; BDORZ=FFFB88E999055A3F8A630C64834BD6D0; H_PS_PSSID=30974_1438_31124_21098; HMVT=6bcd52f51e9b3dce32bec4a3997715ac|1587436663|; delPer=0; PSINO=6; BDRCVFR[gltLrB7qNCt]=mk3SLVN4HKm"
```
![image.png](_img/assets/1655877776447-3917d179-4828-4db9-974a-b75078c5f483.png)


## 扫描指定后缀文件
```
dirb http://192.168.1.15 -X .txt
```
![image.png](_img/assets/1655877776481-efffd45c-c861-4b7b-b45d-869a0d19df91.png)


## 延时扫描
```
dirb http://192.168.1.15 -z 100
```
![image.png](_img/assets/1655877784950-1e29cd6d-bae3-4f2f-acc9-089d2e73d5f4.png)


## 使用代理扫描
```
dirb http://192.168.1.15 -p 127.0.0.1:54321
```
![image.png](_img/assets/1655877800939-8a174381-eeca-4989-8ccc-00e09814462c.png)


## 导出扫描结果
```
dirb http://192.168.1.15 -o /root/aaa.txt
```
![image.png](_img/assets/1655877808027-dc854415-69b2-4e16-b063-5d88531f4ae6.png)
