- [1. 基本語法](#1-基本語法)
- [2. 連線到遠端服務](#2-連線到遠端服務)
- [3. 傳送文字](#3-傳送文字)
- [4. 建立監聽端](#4-建立監聽端)
- [5. 傳送檔案](#5-傳送檔案)
- [6. 測試 Port 是否開放](#6-測試-port-是否開放)
- [7. 使用 UDP](#7-使用-udp)
- [8. 手動測試 HTTP](#8-手動測試-http)
- [9. CTF 常見操作](#9-ctf-常見操作)
- [10. 自動重複測試](#10-自動重複測試)
- [常用選項](#常用選項)



`netcat`（常用指令是 `nc`）是一個用來建立 TCP／UDP 連線的工具。CTF 中最常見的用途是連線到題目伺服器並進行文字互動。

## 1. 基本語法

```bash
nc [選項] 主機 Port
```

例如：

```bash
nc example.com 12345
```

等同於建立 TCP 連線：

```text
你的電腦 → example.com:12345
```

picoCTF 常會提供：

```bash
nc saturn.picoctf.net 50000
```

執行後直接輸入答案或依照選單操作。

---

## 2. 連線到遠端服務

```bash
nc 192.168.1.10 8000
```

指定連線逾時秒數：

```bash
nc -w 5 192.168.1.10 8000
```

顯示詳細連線資訊：

```bash
nc -v 192.168.1.10 8000
```

組合使用：

```bash
nc -v -w 5 192.168.1.10 8000
```

結束方式：

- `Ctrl+C`：立即中斷
- `Ctrl+D`：送出 EOF；部分版本會在資料送完後關閉

---

## 3. 傳送文字

互動輸入：

```bash
nc example.com 12345
```

連線後直接輸入：

```text
hello
```

也可以使用 pipe：

```bash
printf 'hello\n' | nc example.com 12345
```

從檔案傳送內容：

```bash
nc example.com 12345 < input.txt
```

把伺服器輸出儲存到檔案：

```bash
nc example.com 12345 > output.txt
```

同時傳送輸入並儲存輸出：

```bash
nc example.com 12345 < input.txt > output.txt
```

如果 nc 傳完資料後沒有自動結束，可以加上：

```bash
nc -N example.com 12345 < input.txt
```

部分版本使用：

```bash
nc -q 1 example.com 12345 < input.txt
```

`-N` 與 `-q` 是否可用取決於安裝的 netcat 版本。

---

## 4. 建立監聽端

讓本機監聽 TCP Port 9000：

```bash
nc -l 9000
```

顯示詳細資訊：

```bash
nc -lv 9000
```

部分舊版 netcat 語法是：

```bash
nc -l -p 9000
```

另一台電腦連線：

```bash
nc 伺服器IP 9000
```

之後兩邊輸入的文字會傳送給對方，可以用來簡單測試網路服務。

---

## 5. 傳送檔案

假設接收端 IP 是 `192.168.1.10`。

接收端先監聽：

```bash
nc -l 9000 > received.bin
```

傳送端再執行：

```bash
nc 192.168.1.10 9000 < original.bin
```

檢查檔案是否一致：

```bash
sha256sum original.bin
sha256sum received.bin
```

Netcat 本身不會加密或驗證資料，只適合可信任網路或實驗環境。

---

## 6. 測試 Port 是否開放

`-z` 表示只檢查連線，不進行資料交換：

```bash
nc -zv example.com 80
```

掃描一小段 Port：

```bash
nc -zv example.com 20-100
```

加上逾時：

```bash
nc -zv -w 2 example.com 20-100
```

常見輸出：

```text
Connection to example.com 80 port [tcp/http] succeeded!
```

表示該 Port 可以建立 TCP 連線。

只應對 CTF 或獲得授權的系統進行掃描。

---

## 7. 使用 UDP

Netcat 預設使用 TCP，`-u` 表示 UDP。

UDP 監聽端：

```bash
nc -u -l 9000
```

UDP 傳送端：

```bash
nc -u 192.168.1.10 9000
```

傳送單一訊息：

```bash
printf 'hello\n' | nc -u -w 2 192.168.1.10 9000
```

UDP 沒有 TCP 的連線建立程序，因此沒有回應不一定代表 Port 關閉。

---

## 8. 手動測試 HTTP

可以使用 netcat 手動送出 HTTP 請求：

```bash
printf 'GET / HTTP/1.1\r\nHost: example.com\r\nConnection: close\r\n\r\n' |
nc example.com 80
```

重點是 HTTP header 使用：

```text
\r\n
```

最後必須有一個空白行，也就是：

```text
\r\n\r\n
```

HTTPS 不能直接用普通 `nc`，因為它不會處理 TLS。可改用：

```bash
openssl s_client -connect example.com:443
```

或使用 Ncat：

```bash
ncat --ssl example.com 443
```

---

## 9. CTF 常見操作

題目給出：

```bash
nc challenge.example 31337
```

連線：

```bash
nc challenge.example 31337
```

如果服務有選單：

```text
1. Encrypt
2. Decrypt
3. Guess
>
```

你可以直接輸入：

```text
1
```

也可以預先準備輸入：

```bash
printf '1\nhello\n' | nc challenge.example 31337
```

記錄完整互動：

```bash
nc challenge.example 31337 | tee session.txt
```

但這只能記錄伺服器輸出。若要完整記錄終端互動，可使用：

```bash
script session.log
nc challenge.example 31337
exit
```

---

## 10. 自動重複測試

例如重複提交不同數值：

```bash
for value in 1 2 3 4 5; do
    printf '%s\n' "$value" |
    nc -w 2 challenge.example 31337
done
```

如果服務需要多輪互動、等待提示或解析輸出，Shell pipe 容易因時序出問題。這時通常改用 Python `pwntools`：

```python
from pwn import *

io = remote("challenge.example", 31337)

io.recvuntil(b"> ")
io.sendline(b"1")

response = io.recvline()
print(response.decode())

io.interactive()
```

---

## 常用選項

|選項|用途|
|---|---|
|`-l`|監聽模式|
|`-v`|顯示詳細資訊|
|`-n`|不進行 DNS 解析|
|`-u`|使用 UDP|
|`-z`|只檢查 Port，不傳送資料|
|`-w N`|設定逾時秒數|
|`-N`|標準輸入結束後關閉連線|
|`-q N`|EOF 後等待 N 秒再離開|
|`-k`|監聽端持續接受連線|

不同版本的 netcat 選項可能不同，可以查看：

```bash
nc -h
man nc
```

CTF 最常用的三種形式是：

```bash
# 互動連線
nc HOST PORT

# 自動傳入資料
printf 'answer\n' | nc HOST PORT

# 保存結果
nc HOST PORT | tee output.txt
```