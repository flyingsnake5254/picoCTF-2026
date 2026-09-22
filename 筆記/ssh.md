- [1. 基本連線](#1-基本連線)
- [2. 離開 SSH](#2-離開-ssh)
- [3. 在遠端執行單一命令](#3-在遠端執行單一命令)
- [4. 使用 SSH 金鑰登入](#4-使用-ssh-金鑰登入)
  - [建立金鑰](#建立金鑰)
  - [將公鑰安裝到遠端](#將公鑰安裝到遠端)
- [5. 使用指定私鑰](#5-使用指定私鑰)
- [6. SSH 設定檔](#6-ssh-設定檔)
- [7. 使用 SCP 傳輸檔案](#7-使用-scp-傳輸檔案)
  - [本機傳到遠端](#本機傳到遠端)
  - [遠端下載到本機](#遠端下載到本機)
  - [傳輸整個目錄](#傳輸整個目錄)
- [8. 使用 SFTP](#8-使用-sftp)
- [9. 本機 Port Forwarding](#9-本機-port-forwarding)
- [10. Dynamic Forwarding](#10-dynamic-forwarding)
- [11. 保持連線](#11-保持連線)
- [12. 除錯模式](#12-除錯模式)
- [13. 常見錯誤](#13-常見錯誤)
  - [Connection refused](#connection-refused)
  - [Connection timed out](#connection-timed-out)
  - [Permission denied](#permission-denied)
  - [Host key changed](#host-key-changed)
- [14. CTF 常用流程](#14-ctf-常用流程)
- [常用指令速查](#常用指令速查)



SSH（Secure Shell）用來透過加密連線登入遠端 Linux／Unix 主機，也能執行遠端命令、傳輸檔案和建立連接埠轉送。

CTF 最常見格式：

```bash
ssh 使用者名稱@主機 -p Port
```

例如：

```bash
ssh ctf-player@saturn.picoctf.net -p 54321
```

---

## 1. 基本連線

使用預設 TCP 22：

```bash
ssh alice@192.168.1.10
```

指定非預設 Port：

```bash
ssh alice@192.168.1.10 -p 2222
```

也可以使用網域：

```bash
ssh alice@example.com
```

連線流程通常是：

```text
The authenticity of host ... can't be established.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

確認主機指紋正確後輸入：

```text
yes
```

接著輸入密碼：

```text
alice@192.168.1.10's password:
```

輸入密碼時畫面不會顯示星號或字元，這是正常現象。

成功後會得到遠端 shell：

```text
alice@server:~$
```

---

## 2. 離開 SSH

在遠端輸入：

```bash
exit
```

也可以按：

```text
Ctrl+D
```

如果連線卡住，可在新的一行輸入 SSH escape sequence：

```text
~.
```

也就是依序輸入波浪符號和句點，會強制中斷 SSH。

---

## 3. 在遠端執行單一命令

不進入互動 shell，直接執行命令：

```bash
ssh alice@192.168.1.10 'whoami'
```

執行多個命令：

```bash
ssh alice@192.168.1.10 'hostname; id; uname -a'
```

查看遠端檔案：

```bash
ssh alice@192.168.1.10 'cat flag.txt'
```

指定 Port：

```bash
ssh -p 2222 alice@192.168.1.10 'cat flag.txt'
```

注意引號差異。單引號中的內容由遠端 shell 解讀：

```bash
ssh alice@host 'echo "$HOME"'
```

這會顯示遠端使用者的家目錄。

---

## 4. 使用 SSH 金鑰登入

SSH 金鑰通常比密碼更安全，由兩部分組成：

```text
私鑰：只能由自己保管
公鑰：放到遠端伺服器
```

### 建立金鑰

推薦使用 Ed25519：

```bash
ssh-keygen -t ed25519
```

過程中會詢問：

```text
Enter file in which to save the key
Enter passphrase
```

預設產生：

```text
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
```

其中：

- `id_ed25519`：私鑰，不能交給別人
    
- `id_ed25519.pub`：公鑰，可以放到伺服器
    

### 將公鑰安裝到遠端

```bash
ssh-copy-id alice@192.168.1.10
```

指定 Port：

```bash
ssh-copy-id -p 2222 alice@192.168.1.10
```

安裝後即可登入：

```bash
ssh alice@192.168.1.10
```

---

## 5. 使用指定私鑰

CTF 常會提供一個私鑰，例如：

```text
id_rsa
private_key
key.pem
```

先限制權限：

```bash
chmod 600 private_key
```

使用它連線：

```bash
ssh -i private_key alice@192.168.1.10
```

指定 Port：

```bash
ssh -i private_key -p 2222 alice@192.168.1.10
```

如果權限太寬，SSH 可能拒絕：

```text
WARNING: UNPROTECTED PRIVATE KEY FILE!
Permissions 0644 for 'private_key' are too open.
```

修正：

```bash
chmod 600 private_key
```

---

## 6. SSH 設定檔

經常連線相同主機，可以編輯：

```text
~/.ssh/config
```

範例：

```sshconfig
Host myserver
    HostName 192.168.1.10
    User alice
    Port 2222
    IdentityFile ~/.ssh/id_ed25519
```

設定檔權限：

```bash
chmod 600 ~/.ssh/config
```

之後只需要：

```bash
ssh myserver
```

CTF 也可以設定：

```sshconfig
Host pico
    HostName saturn.picoctf.net
    User ctf-player
    Port 54321
```

之後：

```bash
ssh pico
```

---

## 7. 使用 SCP 傳輸檔案

`scp` 使用 SSH 連線傳輸檔案。

### 本機傳到遠端

```bash
scp local.txt alice@192.168.1.10:/tmp/
```

指定遠端檔名：

```bash
scp local.txt alice@192.168.1.10:/tmp/remote.txt
```

指定 SSH Port時，`scp` 使用大寫 `-P`：

```bash
scp -P 2222 local.txt alice@192.168.1.10:/tmp/
```

這裡容易混淆：

|命令|指定 Port|
|---|---|
|`ssh`|小寫 `-p`|
|`scp`|大寫 `-P`|

### 遠端下載到本機

```bash
scp alice@192.168.1.10:/tmp/flag.txt .
```

`.` 表示目前目錄。

指定 Port：

```bash
scp -P 2222 alice@192.168.1.10:/tmp/flag.txt .
```

使用私鑰：

```bash
scp -i private_key alice@192.168.1.10:/tmp/flag.txt .
```

### 傳輸整個目錄

```bash
scp -r project/ alice@192.168.1.10:/tmp/
```

下載整個目錄：

```bash
scp -r alice@192.168.1.10:/tmp/project .
```

---

## 8. 使用 SFTP

SFTP 提供類似 FTP 的互動介面，但通訊透過 SSH 加密。

連線：

```bash
sftp alice@192.168.1.10
```

指定 Port：

```bash
sftp -P 2222 alice@192.168.1.10
```

成功後：

```text
sftp>
```

常用命令：

```text
ls                  列出遠端檔案
pwd                 顯示遠端路徑
cd directory        切換遠端目錄
lpwd                顯示本機路徑
lcd directory       切換本機目錄
get file.txt        下載檔案
put file.txt        上傳檔案
get -r directory    下載目錄
put -r directory    上傳目錄
exit                離開
```

---

## 9. 本機 Port Forwarding

假設只能透過 SSH 主機存取它本機的 Web 服務：

```text
遠端主機的 127.0.0.1:8080
```

建立本機轉送：

```bash
ssh -L 9000:127.0.0.1:8080 alice@192.168.1.10
```

之後在本機開啟：

```text
http://127.0.0.1:9000
```

資料流向：

```text
本機 127.0.0.1:9000
    → SSH 加密連線
    → 遠端看到的 127.0.0.1:8080
```

不需要開啟遠端 shell：

```bash
ssh -N -L 9000:127.0.0.1:8080 alice@192.168.1.10
```

其中 `-N` 表示只建立轉送，不執行遠端命令。

---

## 10. Dynamic Forwarding

建立 SOCKS Proxy：

```bash
ssh -N -D 1080 alice@192.168.1.10
```

本機 SOCKS5 Proxy 位址：

```text
127.0.0.1:1080
```

例如透過 SOCKS proxy 使用 curl：

```bash
curl --socks5-hostname 127.0.0.1:1080 http://internal.example
```

這在合法的內部網路測試或 CTF pivot 題目中很常見。

---

## 11. 保持連線

如果 SSH 經常因閒置斷線：

```bash
ssh \
  -o ServerAliveInterval=30 \
  -o ServerAliveCountMax=3 \
  alice@192.168.1.10
```

含義：

- 每 30 秒傳送一次確認
    
- 連續 3 次沒有回應就中斷
    

也可以放進 `~/.ssh/config`：

```sshconfig
Host myserver
    HostName 192.168.1.10
    User alice
    ServerAliveInterval 30
    ServerAliveCountMax 3
```

---

## 12. 除錯模式

連線失敗時使用：

```bash
ssh -v alice@192.168.1.10
```

更詳細：

```bash
ssh -vv alice@192.168.1.10
ssh -vvv alice@192.168.1.10
```

常用於判斷：

- 是否連到正確 Port
    
- 使用了哪個私鑰
    
- 金鑰是否被伺服器接受
    
- 密碼驗證是否開放
    
- 協定協商在哪裡失敗
    

---

## 13. 常見錯誤

### Connection refused

```text
ssh: connect to host 192.168.1.10 port 22: Connection refused
```

表示主機可以到達，但該 Port 沒有 SSH 服務監聽。

檢查題目是否使用其他 Port：

```bash
nc -zv 192.168.1.10 22
nc -zv 192.168.1.10 2222
```

### Connection timed out

```text
ssh: connect to host ... Connection timed out
```

可能是：

- IP 錯誤
    
- 防火牆阻擋
    
- 路由或 VPN 問題
    
- 主機無法到達
    

### Permission denied

```text
Permission denied (publickey,password)
```

代表網路連線成功，但身分驗證失敗：

- 帳號錯誤
    
- 密碼錯誤
    
- 私鑰錯誤
    
- 私鑰權限錯誤
    
- 伺服器不允許該驗證方式
    

使用除錯輸出：

```bash
ssh -vvv -i private_key alice@192.168.1.10
```

### Host key changed

```text
WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!
```

可能是：

- 伺服器重新安裝
    
- IP 被分配給另一台機器
    
- CTF instance 重建
    
- 中間人攻擊
    

確認主機確實已更換後，才移除舊紀錄：

```bash
ssh-keygen -R 192.168.1.10
```

非預設 Port：

```bash
ssh-keygen -R '[192.168.1.10]:2222'
```

不要在未確認原因時直接忽略主機指紋警告。

---

## 14. CTF 常用流程

題目通常提供：

```text
Host: saturn.picoctf.net
Port: 54321
Username: ctf-player
Password: example-password
```

連線：

```bash
ssh ctf-player@saturn.picoctf.net -p 54321
```

登入後先查看：

```bash
whoami
id
hostname
pwd
ls -la
```

搜尋 flag：

```bash
find . -type f 2>/dev/null
rg -i 'picoCTF|flag' . 2>/dev/null
```

離開：

```bash
exit
```

若題目提供私鑰：

```bash
chmod 600 id_rsa
ssh -i id_rsa ctf-player@saturn.picoctf.net -p 54321
```

---

## 常用指令速查

```bash
# 密碼登入
ssh user@host

# 指定 Port
ssh user@host -p 2222

# 使用私鑰
ssh -i private_key user@host

# 執行遠端命令
ssh user@host 'cat flag.txt'

# 上傳檔案
scp file.txt user@host:/tmp/

# 下載檔案
scp user@host:/tmp/file.txt .

# SCP 指定 Port
scp -P 2222 file.txt user@host:/tmp/

# SFTP
sftp user@host

# 建立本機 Port Forwarding
ssh -N -L 9000:127.0.0.1:8080 user@host

# 顯示除錯資訊
ssh -vvv user@host
```