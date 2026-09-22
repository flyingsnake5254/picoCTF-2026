`smbclient` 是 Linux 上操作 SMB/CIFS 分享資料夾的命令列工具，可以把它理解成「終端機版的 Windows 網路芳鄰」。

CTF 常用來：

- 列出 SMB 分享
- 匿名登入
- 瀏覽遠端檔案
- 下載 flag 或設定檔
- 測試帳號密碼
- 上傳檔案（分享具有寫入權限時）

---

## 1. 基本語法

```bash
smbclient [選項] //主機/分享名稱
```

例如連線到 `10.10.10.20` 上的 `public` 分享：

```bash
smbclient //10.10.10.20/public
```

如果需要帳號：

```bash
smbclient //10.10.10.20/public -U alice
```

接著輸入密碼。

SMB 通常使用：

```text
TCP 445
```

舊式 NetBIOS SMB 也可能使用 TCP 139。

---

## 2. 列出主機上的分享

這通常是第一步。

匿名列舉：

```bash
smbclient -L //10.10.10.20 -N
```

參數含義：

- `-L`：列出分享
- `-N`：不詢問密碼，使用空密碼

可能得到：

```text
Sharename       Type      Comment
---------       ----      -------
public          Disk      Public Share
backup          Disk      Backup Files
IPC$            IPC       IPC Service
```

使用指定帳號列舉：

```bash
smbclient -L //10.10.10.20 -U alice
```

指定空密碼的 guest：

```bash
smbclient -L //10.10.10.20 -U guest -N
```

也可能寫成：

```bash
smbclient -L //10.10.10.20 -U 'guest%'
```

`%` 後方是密碼，因此 `guest%` 表示帳號 `guest`、空密碼。

一般不建議直接把真正密碼放進命令，因為可能留在 shell history。

---

## 3. 連線到分享

匿名連線：

```bash
smbclient //10.10.10.20/public -N
```

使用帳號：

```bash
smbclient //10.10.10.20/public -U alice
```

成功後會進入互動介面：

```text
smb: \>
```

它類似 FTP 的命令列介面，不是一般 Linux shell。

---

## 4. 瀏覽檔案

### 列出遠端檔案 - ls

```text
smb: \> ls
```

也可以使用：

```text
smb: \> dir
```

### 查看目前遠端路徑 - pwd

```text
smb: \> pwd
```

### 進入目錄 - cd

```text
smb: \> cd documents
```

### 回到上一層 - cd ..

```text
smb: \documents\> cd ..
```

SMB 路徑通常使用反斜線：

```text
\documents\secret.txt
```

### 查看命令清單 - help

```text
smb: \> help
```

查看特定命令說明：

```text
smb: \> help get
```

---

## 5. 下載檔案

### 下載單一檔案

```text
smb: \> get flag.txt
```

下載後會存到本機目前目錄。

### 指定本機檔名

```text
smb: \> get flag.txt downloaded_flag.txt
```

### 查看本機目前路徑

```text
smb: \> lpwd
```

### 切換本機下載目錄

```text
smb: \> lcd /tmp/smb-files
```

注意：

- `cd`：切換遠端目錄
- `lcd`：切換本機目錄

---

## 6. 批次下載

### 下載符合條件的檔案

```text
smb: \> mget *.txt
```

預設會逐一詢問：

```text
Get file one.txt? y
```

關閉詢問：

```text
smb: \> prompt OFF
smb: \> mget *.txt
```

### 遞迴下載整個目錄

```text
smb: \> recurse ON
smb: \> prompt OFF
smb: \> mget *
```

建議先進入一個空的本機目錄，避免檔案混在一起：

```bash
mkdir smb-download
cd smb-download
smbclient //10.10.10.20/public -N
```

接著：

```text
smb: \> recurse ON
smb: \> prompt OFF
smb: \> mget *
```

---

## 7. 上傳檔案

分享必須具有寫入權限。

### 上傳單一檔案

```text
smb: \> put local.txt
```

指定遠端名稱：

```text
smb: \> put local.txt remote.txt
```

### 上傳多個檔案

```text
smb: \> mput *.txt
```

關閉逐一確認：

```text
smb: \> prompt OFF
smb: \> mput *.txt
```

CTF 中如果找到可寫入的 Web 分享，題目可能要求將檔案上傳到網站目錄；只應在題目或有授權的環境操作。

---

## 8. 非互動模式

### 使用 -c 直接執行 smbclient 命令

```bash
smbclient //10.10.10.20/public -N -c 'ls'
```

### 下載檔案

```bash
smbclient //10.10.10.20/public -N \
  -c 'get flag.txt'
```

### 進入目錄再下載

```bash
smbclient //10.10.10.20/public -N \
  -c 'cd secrets; ls; get flag.txt'
```

### 一次下載多個檔案

```bash
smbclient //10.10.10.20/public -N \
  -c 'recurse ON; prompt OFF; mget *'
```

### 使用者帳號

```bash
smbclient //10.10.10.20/private -U alice \
  -c 'ls; get notes.txt'
```

執行後會安全地提示輸入密碼。

---

## 9. 特殊分享名稱

Windows 管理分享經常包含 `$`：

```text
C$
ADMIN$
IPC$
```

Shell 可能把 `$` 解讀為變數，因此要加引號：

```bash
smbclient '//10.10.10.20/C$' -U administrator
```

`IPC$` 通常不是一般檔案分享：

```bash
smbclient '//10.10.10.20/IPC$' -N
```

它主要用於 SMB/RPC 通訊，不一定能在裡面找到普通檔案。

---

## 10. 指定網域或工作群組

指定網域：

```bash
smbclient //10.10.10.20/share \
  -U 'DOMAIN\alice'
```

為避免反斜線被 shell 處理，最好使用單引號。

也可以使用：

```bash
smbclient //10.10.10.20/share \
  -W DOMAIN \
  -U alice
```

常見格式：

```text
WORKGROUP\username
DOMAIN\username
```

---

## 11. 指定 SMB 協定版本

現代主機通常使用 SMB2 或 SMB3：

```bash
smbclient //10.10.10.20/share -N -m SMB3
```

或者：

```bash
smbclient //10.10.10.20/share -N -m SMB2
```

非常舊的 CTF 主機可能只支援 SMB1/NT1。可以在隔離的題目環境嘗試：

```bash
smbclient //10.10.10.20/share -N \
  --option='client min protocol=NT1'
```

SMB1 有嚴重安全問題，不應在一般環境啟用。

---

## 12. 主機名稱無法解析

如果題目提供主機名稱，但 DNS 或 `/etc/hosts` 無法解析，可以指定 IP：

```bash
smbclient -L //fileserver -I 10.10.10.20 -N
```

連線分享：

```bash
smbclient //fileserver/public -I 10.10.10.20 -N
```

也可以在授權環境中加入 `/etc/hosts`：

```text
10.10.10.20 fileserver
```

---

## 13. 除錯輸出

增加除錯等級：

```bash
smbclient -L //10.10.10.20 -N -d 3
```

更詳細：

```bash
smbclient //10.10.10.20/public -N -d 5
```

等級越高，輸出越多。一般從 `-d 3` 開始即可。

---

## 14. 常見錯誤

### NT_STATUS_LOGON_FAILURE

```text
session setup failed: NT_STATUS_LOGON_FAILURE
```

代表：

- 帳號或密碼錯誤
- 網域錯誤
- 不允許匿名登入
- 帳號被停用或限制

嘗試：

```bash
smbclient -L //HOST -U username
smbclient -L //HOST -U 'DOMAIN\username'
```

### NT_STATUS_ACCESS_DENIED

```text
NT_STATUS_ACCESS_DENIED
```

已成功登入，但沒有列舉、讀取或寫入權限。

### NT_STATUS_BAD_NETWORK_NAME

```text
tree connect failed: NT_STATUS_BAD_NETWORK_NAME
```

通常表示分享名稱錯誤。先列出分享：

```bash
smbclient -L //HOST -N
```

### Connection refused

可能原因：

- TCP 445 沒有開放
- SMB 服務沒有啟動
- 防火牆阻擋
- IP 或 Port 錯誤

可以先確認：

```bash
nc -zv 10.10.10.20 445
```

### 協定版本錯誤

若目標非常舊，可能出現：

```text
protocol negotiation failed
```

CTF 環境可嘗試指定 SMB2、SMB3，或必要時使用 NT1。

---

## 15. CTF 標準探查流程

假設目標是 `10.10.10.20`。

確認 SMB Port：

```bash
nc -zv 10.10.10.20 445
```

匿名列出分享：

```bash
smbclient -L //10.10.10.20 -N
```

嘗試 guest：

```bash
smbclient -L //10.10.10.20 -U guest -N
```

連線可疑分享：

```bash
smbclient //10.10.10.20/public -N
```

列出檔案：

```text
smb: \> ls
smb: \> cd backup
smb: \backup\> ls
```

下載可疑檔案：

```text
smb: \backup\> get flag.txt
smb: \backup\> get config.ini
smb: \backup\> get passwords.txt
```

大量下載後在本機搜尋：

```bash
rg -i 'flag|password|secret|token|key' smb-download/
```

---

## 16. 安裝方式

Kali／Debian：

```bash
sudo apt update
sudo apt install smbclient
```

Manjaro／Arch：

```bash
sudo pacman -S samba
```

確認版本：

```bash
smbclient --version
```

最常用的幾條可以先記住：

```bash
# 匿名列出分享
smbclient -L //HOST -N

# 使用帳號列出分享
smbclient -L //HOST -U USER

# 匿名連線
smbclient //HOST/SHARE -N

# 使用帳號連線
smbclient //HOST/SHARE -U USER

# 直接列檔案
smbclient //HOST/SHARE -N -c 'ls'

# 直接下載
smbclient //HOST/SHARE -N -c 'get flag.txt'
```