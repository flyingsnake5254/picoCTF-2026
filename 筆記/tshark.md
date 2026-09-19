- [install](#install)
- [1. 查看網路介面](#1-查看網路介面)
- [2. 即時顯示封包](#2-即時顯示封包)
- [3. 讀取 PCAP](#3-讀取-pcap)
- [4. Capture Filter 與 Display Filter](#4-capture-filter-與-display-filter)
- [5. 提取指定欄位](#5-提取指定欄位)
  - [基本格式](#基本格式)
  - [加上標題和 CSV 格式](#加上標題和-csv-格式)
  - [搜尋可使用的欄位名稱](#搜尋可使用的欄位名稱)
  - [常用 Frame 欄位](#常用-frame-欄位)
  - [Ethernet 與 IP 欄位](#ethernet-與-ip-欄位)
  - [TCP 欄位](#tcp-欄位)
  - [UDP 欄位](#udp-欄位)
  - [DNS 欄位](#dns-欄位)
  - [HTTP 欄位](#http-欄位)
  - [TLS 欄位](#tls-欄位)
  - [ICMP、ARP 欄位](#icmparp-欄位)
  - [FTP、SMTP、SMB 欄位](#ftpsmtpsmb-欄位)
  - [USB 封包](#usb-封包)
  - [控制輸出格式](#控制輸出格式)
- [6. DNS 分析](#6-dns-分析)
  - [列出 DNS 查詢](#列出-dns-查詢)
  - [統計查詢次數](#統計查詢次數)
  - [列出 DNS 回應](#列出-dns-回應)
- [7. HTTP 分析](#7-http-分析)
  - [列出請求](#列出請求)
  - [列出 HTTP Response](#列出-http-response)
- [8. 查看端點、協定和連線](#8-查看端點協定和連線)
  - [協定分佈](#協定分佈)
  - [IP、TCP、UDP conversations](#iptcpudp-conversations)
  - [端點統計](#端點統計)
  - [每秒流量](#每秒流量)
- [9. Follow TCP Stream](#9-follow-tcp-stream)
  - [列出 stream 編號](#列出-stream-編號)
  - [查看第 0 個 TCP stream](#查看第-0-個-tcp-stream)
- [10. 提取 TCP payload](#10-提取-tcp-payload)
    - [列出封包編號和 payload](#列出封包編號和-payload)
    - [將指定 stream 的 payload 拼成二進位檔](#將指定-stream-的-payload-拼成二進位檔)
- [11. 匯出 HTTP 等物件](#11-匯出-http-等物件)
  - [查看支援的協定](#查看支援的協定)
  - [匯出 HTTP 檔案](#匯出-http-檔案)
- [12. 將過濾結果另存成 PCAP](#12-將過濾結果另存成-pcap)


<hr>

## install

```bash
sudo apt update
sudo apt install tshark

tshark --version
```


## 1. 查看網路介面

```bash
tshark -D
```

```bash
1. eth0
2. any
3. lo (Loopback)
4. bluetooth-monitor
5. nflog
6. nfqueue
7. dbus-system
8. dbus-session
9. ciscodump (Cisco remote capture)
10. dpauxmon (DisplayPort AUX channel monitor capture)
11. randpkt (Random packet generator)
12. sdjournal (systemd Journal Export)
13. sshdig (SSH remote syscall capture)
14. sshdump (SSH remote capture)
15. udpdump (UDP Listener remote capture)
16. wifidump (Wi-Fi remote capture)
```


## 2. 即時顯示封包

```bash
sudo tshark -i eth0
```

只抓取前 20 個：

```bash
sudo tshark -i eth0 -c 20
```

儲存為 **PCAPNG** ：

```bash
sudo tshark -i eth0 -w capture.pcapng
```

按 `Ctrl+C` 結束


## 3. 讀取 PCAP

**基本使用**

```bash
tshark -r capture.pcapng
```

**只看前 10 個封包**

```bash
tshark -r capture.pcapng -c 10
```

**顯示完整協定欄位**

```bash
tshark -r capture.pcapng -c 1 -V
```

**顯示十六進位資料**

```bash
tshark -r capture.pcapng -c 1 -x
```

**只展開指定協定**

```bash
tshark -r capture.pcapng -O tcp,http
```


## 4. Capture Filter 與 Display Filter

兩者不可混用：

|選項|用途|語法|
|---|---|---|
|`-f`|即時抓包前過濾|BPF capture filter|
|`-Y`|顯示或讀取後過濾|Wireshark display filter|

**即時只抓 TCP 80**

```bash
sudo tshark -i eth0 -f 'tcp port 80'
```

**讀取 PCAP 時只顯示 HTTP**

```bash
tshark -r capture.pcapng -Y 'http'
```

**常用 Display Filter**

```bash
tshark -r capture.pcapng -Y 'dns'
tshark -r capture.pcapng -Y 'http.request'
tshark -r capture.pcapng -Y 'ip.addr == 192.168.1.10'
tshark -r capture.pcapng -Y 'tcp.port == 80'
tshark -r capture.pcapng -Y 'tcp.flags.syn == 1 && tcp.flags.ack == 0'
tshark -r capture.pcapng -Y 'tcp.len > 0'
tshark -r capture.pcapng -Y 'frame contains "flag"'
```


## 5. 提取指定欄位

### 基本格式

```bash
tshark -r capture.pcapng \
  -T fields \
  -e frame.number \
  -e ip.src \
  -e ip.dst \
  -e _ws.col.protocol \
  -e frame.len
```

### 加上標題和 CSV 格式

```bash
tshark -r capture.pcapng \
  -T fields \
  -e frame.number \
  -e ip.src \
  -e ip.dst \
  -e tcp.srcport \
  -e tcp.dstport \
  -E header=y \
  -E separator=, \
  -E quote=d > packets.csv
```

### 搜尋可使用的欄位名稱

```bash
tshark -G fields | rg 'dns.qry.name'
tshark -G fields | rg 'tcp.payload'
```

### 常用 Frame 欄位

| 欄位                    | 說明                   |
| --------------------- | -------------------- |
| `frame.number`        | 封包編號                 |
| `frame.time`          | 完整時間                 |
| `frame.time_epoch`    | Unix timestamp       |
| `frame.time_relative` | 相對第一個封包的時間           |
| `frame.time_delta`    | 與上一個封包的時間差           |
| `frame.len`           | 線路上的封包長度             |
| `frame.cap_len`       | 實際擷取長度               |
| `frame.interface_id`  | PCAPNG 介面編號          |
| `frame.comment`       | PCAPNG 封包註解          |
| `_ws.col.protocol`    | Wireshark Protocol 欄 |
| `_ws.col.info`        | Wireshark Info 欄     |

例如：

```bash
tshark -r capture.pcapng \
  -T fields \
  -e frame.number \
  -e frame.time_relative \
  -e frame.len \
  -e _ws.col.protocol \
  -e _ws.col.info
```


### Ethernet 與 IP 欄位

|欄位|說明|
|---|---|
|`eth.src`|來源 MAC|
|`eth.dst`|目的 MAC|
|`eth.type`|EtherType|
|`ip.src`|來源 IPv4|
|`ip.dst`|目的 IPv4|
|`ip.addr`|來源或目的 IPv4|
|`ip.proto`|上層協定編號|
|`ip.ttl`|TTL|
|`ip.id`|IP Identification|
|`ip.len`|IP 封包長度|
|`ip.flags.df`|Don't Fragment|
|`ip.flags.mf`|More Fragments|
|`ip.frag_offset`|Fragment offset|
|`ipv6.src`|來源 IPv6|
|`ipv6.dst`|目的 IPv6|
|`ipv6.hlim`|IPv6 Hop Limit|

### TCP 欄位

|欄位|說明|
|---|---|
|`tcp.srcport`|來源 port|
|`tcp.dstport`|目的 port|
|`tcp.port`|來源或目的 port|
|`tcp.stream`|TCP stream 編號|
|`tcp.seq`|Sequence number|
|`tcp.ack`|Acknowledgment number|
|`tcp.len`|TCP payload 長度|
|`tcp.hdr_len`|TCP header 長度|
|`tcp.flags`|完整 flags|
|`tcp.flags.syn`|SYN|
|`tcp.flags.ack`|ACK|
|`tcp.flags.fin`|FIN|
|`tcp.flags.reset`|RST|
|`tcp.flags.push`|PSH|
|`tcp.window_size_value`|Window size|
|`tcp.payload`|TCP payload bytes|
|`tcp.analysis.retransmission`|是否判斷為重傳|
|`tcp.analysis.out_of_order`|是否順序異常|

### UDP 欄位

| 欄位             | 說明                |
| -------------- | ----------------- |
| `udp.srcport`  | 來源 port           |
| `udp.dstport`  | 目的 port           |
| `udp.port`     | 來源或目的 port        |
| `udp.stream`   | UDP stream 編號     |
| `udp.length`   | UDP 長度            |
| `udp.checksum` | Checksum          |
| `udp.payload`  | UDP payload，視版本而定 |
| `data.data`    | 未被上層協定解析的資料       |

### DNS 欄位

|欄位|說明|
|---|---|
|`dns.id`|Transaction ID|
|`dns.flags.response`|查詢或回應|
|`dns.qry.name`|查詢網域|
|`dns.qry.type`|Query type|
|`dns.a`|IPv4 回應|
|`dns.aaaa`|IPv6 回應|
|`dns.cname`|CNAME|
|`dns.txt`|TXT record|
|`dns.resp.name`|回應名稱|
|`dns.flags.rcode`|Response code|

例如：

```bash
tshark -r capture.pcapng \
  -Y 'dns.qry.name' \
  -T fields \
  -e frame.number \
  -e ip.src \
  -e dns.qry.type \
  -e dns.qry.name \
  -e dns.a
```


### HTTP 欄位

|欄位|說明|
|---|---|
|`http.request`|HTTP request|
|`http.request.method`|GET、POST 等|
|`http.host`|Host header|
|`http.request.uri`|URI|
|`http.request.full_uri`|完整 URI|
|`http.user_agent`|User-Agent|
|`http.referer`|Referer|
|`http.cookie`|Cookie|
|`http.authorization`|Authorization header|
|`http.response.code`|HTTP status code|
|`http.content_type`|Content-Type|
|`http.content_length`|Content-Length|
|`http.file_data`|HTTP 傳輸內容|

例如：

```bash
tshark -r capture.pcapng \
  -Y 'http.request' \
  -T fields \
  -e frame.number \
  -e ip.src \
  -e http.request.method \
  -e http.host \
  -e http.request.uri \
  -e http.user_agent
```


### TLS 欄位

|欄位|說明|
|---|---|
|`tls.record.version`|TLS record 版本|
|`tls.handshake.type`|Handshake 類型|
|`tls.handshake.version`|ClientHello 版本|
|`tls.handshake.extensions_server_name`|SNI 網域|
|`tls.handshake.ciphersuite`|Cipher suite|
|`tls.record.content_type`|Record 類型|
|`tls.app_data`|加密 application data|

提取 SNI：

```bash
tshark -r capture.pcapng \
  -Y 'tls.handshake.type == 1' \
  -T fields \
  -e frame.number \
  -e ip.src \
  -e ip.dst \
  -e tls.handshake.extensions_server_name
```


### ICMP、ARP 欄位

```bash
icmp.type
icmp.code
icmp.ident
icmp.seq
icmp.resp_in
icmp.resp_to

arp.opcode
arp.src.hw_mac
arp.src.proto_ipv4
arp.dst.hw_mac
arp.dst.proto_ipv4
```


### FTP、SMTP、SMB 欄位

```bash
ftp.request.command
ftp.request.arg
ftp.response.code
ftp.response.arg

smtp.req.command
smtp.req.parameter
smtp.response.code

smb2.cmd
smb2.filename
smb2.tree
smb2.nt_status
```

實際名稱可能因 TShark 版本與協定版本不同，使用以下方式確認：

```bash
tshark -G fields | rg '\tftp\.'
tshark -G fields | rg '\tsmtp\.'
tshark -G fields | rg '\tsmb2\.'
```


### USB 封包

```bash
usb.src
usb.dst
usb.device_address
usb.endpoint_address
usb.transfer_type
usb.data_len
usb.capdata
```

### 控制輸出格式

```bash
tshark -r capture.pcapng \
  -T fields \
  -e frame.number \
  -e ip.src \
  -e ip.dst \
  -E header=y \
  -E separator=, \
  -E quote=d \
  -E occurrence=a
```

|選項|作用|
|---|---|
|`header=y`|顯示欄位名稱|
|`separator=,`|使用逗號分隔|
|`quote=d`|使用雙引號|
|`occurrence=f`|只取第一個值|
|`occurrence=l`|只取最後一個值|
|`occurrence=a`|輸出全部值|

## 6. DNS 分析

### 列出 DNS 查詢

```bash
tshark -r capture.pcapng \
  -Y 'dns.qry.name' \
  -T fields \
  -e frame.number \
  -e ip.src \
  -e dns.qry.type \
  -e dns.qry.name
```

### 統計查詢次數

```bash
tshark -r capture.pcapng \
  -Y 'dns.qry.name' \
  -T fields -e dns.qry.name |
sort | uniq -c | sort -nr
```


### 列出 DNS 回應

```bash
tshark -r capture.pcapng \
  -Y 'dns.flags.response == 1' \
  -T fields \
  -e dns.qry.name \
  -e dns.a \
  -e dns.aaaa
```


## 7. HTTP 分析

### 列出請求

```bash
tshark -r capture.pcapng \
  -Y 'http.request' \
  -T fields \
  -e frame.number \
  -e ip.src \
  -e http.request.method \
  -e http.host \
  -e http.request.uri
```


### 列出 HTTP Response

```bash
tshark -r capture.pcapng \
  -Y 'http.response' \
  -T fields \
  -e frame.number \
  -e http.response.code \
  -e http.content_type
```


## 8. 查看端點、協定和連線

### 協定分佈

```bash
tshark -r capture.pcapng -q -z io,phs
```


### IP、TCP、UDP conversations

```bash
tshark -r capture.pcapng -q -z conv,ip
tshark -r capture.pcapng -q -z conv,tcp
tshark -r capture.pcapng -q -z conv,udp
```


### 端點統計

```bash
tshark -r capture.pcapng -q -z endpoints,ip
tshark -r capture.pcapng -q -z endpoints,tcp
```


### 每秒流量

```bash
tshark -r capture.pcapng -q -z io,stat,1
```


## 9. Follow TCP Stream

### 列出 stream 編號

```bash
tshark -r capture.pcapng \
  -T fields -e tcp.stream |
awk 'NF' | sort -n | uniq
```


### 查看第 0 個 TCP stream

```bash
tshark -r capture.pcapng \
  -q -z 'follow,tcp,ascii,0'
```

以 16 進位查看：

```bash
tshark -r capture.pcapng \
  -q -z 'follow,tcp,hex,0'
```

第一個 stream 是 `0`，第二個是 `1`

## 10. 提取 TCP payload

#### 列出封包編號和 payload

```bash
tshark -r capture.pcapng \
  -Y 'tcp.len > 0' \
  -T fields \
  -e frame.number \
  -e tcp.stream \
  -e tcp.len \
  -e tcp.payload
```

#### 將指定 stream 的 payload 拼成二進位檔

```bash
tshark -r capture.pcapng \
  -Y 'tcp.stream == 0 && tcp.len > 0' \
  -T fields -e tcp.payload |
tr -d ':\r\n' |
xxd -r -p > payload.bin
```


## 11. 匯出 HTTP 等物件

### 查看支援的協定

```bash
tshark --export-objects help
```

### 匯出 HTTP 檔案

```bash
mkdir -p http_objects

tshark -r capture.pcapng \
  --export-objects http,http_objects
```


## 12. 將過濾結果另存成 PCAP

**只保留 DNS**

```bash
tshark -r capture.pcapng \
  -Y 'dns' \
  -w dns-only.pcapng
```

**保留指定主機**

```bash
tshark -r capture.pcapng \
  -Y 'ip.addr == 192.168.1.10' \
  -w host-only.pcapng
```


