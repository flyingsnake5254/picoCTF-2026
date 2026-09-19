## 題目

https://learn.cylabacademy.org/library/459?page=1&category=4

A digital ghost has breached my defenses, and my sensitive data has been stolen! 😱💻 Your mission is to uncover how this phantom intruder infiltrated my system and retrieve the hidden flag.

To solve this challenge, you'll need to analyze the provided PCAP file and track down the attack method. The attacker has cleverly concealed his moves in well timely manner. Dive into the network traffic, apply the right filters and show off your forensic prowess and unmask the digital intruder!

Find the PCAP file here [Network Traffic PCAP file](https://challenge-files.picoctf.net/c_verbal_sleep/a16868557f2510da0f9614e00e69950868489749884fd7db5a3247937eabe7bc/myNetworkTraffic.pcap) and try to get the flag.


## 解題

>[tshark 提取 TCP Payload](../../筆記/tshark.md#10-提取-tcp-payload)  

使用 `tshark` 提取 TCP Payload：

本題重點再於：「**要使用 timestamp 排序，而非 sequence number !**」

```bash
tshark -r myNetworkTraffic.pcap \
  -Y 'tcp.len > 0' \
  -T fields \
  -e frame.time_epoch \
  -e tcp.payload
```

輸出：

```bash
1741231902.550317000    657a46305833633063773d3d
1741231902.551541000    66513d3d
1741231902.551325000    4e4749314e7a6b774f513d3d
1741231902.550867000    587a4d3063336c6664413d3d
1741231902.547494000    3633335a4c66593d
1741231902.546634000    6b375a647a4c4d3d
1741231902.546388000    386c5537597a6f3d
1741231902.549082000    57614862484b383d
1741231902.548751000    562b72326277413d
1741231902.550547000    626e52666447673064413d3d
1741231902.548521000    65375155496b6b3d
1741231902.549309000    2f636159646f773d
1741231902.545909000    5052483963734d3d
1741231902.547234000    6756726e4a72633d
1741231902.549540000    3351794f3478303d
1741231902.546870000    48664b682f62493d
1741231902.548294000    533134664838343d
1741231902.551103000    596d68664e484a665a413d3d
1741231902.549769000    43746c306252453d
1741231902.547721000    365743634469593d
1741231902.548054000    72526f303272733d
1741231902.550085000    63476c6a62304e5552673d3d
```

看到 playload 有藏訊息，將訊息存成 `enc.txt` 檔案後，撰寫 python 程式，讀取 `enc.txt` 後，先依照 timestamp 排序，再進行 base64 解碼。

並且本題先將 enc.txt 的 playload 部份，轉成 bytes 並 decode 後，輸出內容如下：

```bash
PRH9csM=
8lU7Yzo=
k7ZdzLM=
HfKh/bI=
gVrnJrc=
633ZLfY=
6WCcDiY=
rRo02rs=
S14fH84=
e7QUIkk=
V+r2bwA=
WaHbHK8=
/caYdow=
3QyO4x0=
Ctl0bRE=
cGljb0NURg==
ezF0X3c0cw==
bnRfdGg0dA==
XzM0c3lfdA==
YmhfNHJfZA==
NGI1NzkwOQ==
fQ==
```

發現其還包含非 base64 編碼的部份，故僅須對 base64 可解碼的部份進行解碼即可得到 flag ：

```python
import base64

data = {}
timestamps = []
with open("enc.txt", "r", encoding="utf-8") as f:
    for line in f:
        timestamp, msg = (line.strip()).split()
        data[timestamp] = msg
        timestamps.append(timestamp)
timestamps.sort()

for t in timestamps:
    decode_data = bytes.fromhex(data[t]).decode('utf-8')
    if '==' in decode_data:
        print(base64.b64decode(decode_data).decode('utf-8'), end='')
```


輸出：  
picoCTF{1t_w4snt_th4t_34sy_tbh_4r_d4b57909}