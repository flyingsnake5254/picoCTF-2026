- [1. 常見用法](#1-常見用法)
- [2. 查看自己可以使用哪些 sudo 命令](#2-查看自己可以使用哪些-sudo-命令)
- [3. 以其他使用者身分執行](#3-以其他使用者身分執行)
- [4. 進入 root Shell](#4-進入-root-shell)
  - [登入型 root shell](#登入型-root-shell)
  - [一般 root shell](#一般-root-shell)
- [5. `sudo` 和 `su` 的差異](#5-sudo-和-su-的差異)
- [6. 密碼快取](#6-密碼快取)
- [7. 非互動模式](#7-非互動模式)
- [8. `sudo` 與輸出重新導向](#8-sudo-與輸出重新導向)
- [9. 使用 sudo 編輯系統檔案](#9-使用-sudo-編輯系統檔案)
- [10. 為什麼 `sudo cd` 不工作](#10-為什麼-sudo-cd-不工作)
- [11. 保留環境變數](#11-保留環境變數)
- [12. 執行上一條命令](#12-執行上一條命令)
- [13. Sudoers 設定](#13-sudoers-設定)
- [14. CTF 中的基本檢查](#14-ctf-中的基本檢查)
- [15. 常見錯誤](#15-常見錯誤)
  - [使用者沒有 sudo 權限](#使用者沒有-sudo-權限)
  - [密碼錯誤](#密碼錯誤)
  - [命令不被允許](#命令不被允許)
  - [找不到命令](#找不到命令)
- [常用命令速查](#常用命令速查)



`sudo` 是「以其他使用者身分執行命令」的工具，預設目標使用者是 `root`。

最基本格式：

```bash
sudo 命令
```

例如安裝套件：

```bash
sudo pacman -S nmap
```

或：

```bash
sudo apt install nmap
```

通常輸入的是「目前使用者的密碼」，不是 root 密碼。輸入時終端不會顯示任何字元，屬於正常現象。

---

## 1. 常見用法

以 root 身分執行命令：

```bash
sudo id
```

可能輸出：

```text
uid=0(root) gid=0(root) groups=0(root)
```

建立系統目錄：

```bash
sudo mkdir /opt/myapp
```

複製檔案：

```bash
sudo cp config.ini /etc/myapp/
```

重新啟動服務：

```bash
sudo systemctl restart sshd
```

查看受限檔案：

```bash
sudo cat /etc/shadow
```

能否執行取決於 sudo 權限設定。

---

## 2. 查看自己可以使用哪些 sudo 命令

```bash
sudo -l
```

這是系統管理與 CTF 權限檢查中最重要的命令之一。

可能輸出：

```text
User alice may run the following commands:
    (ALL : ALL) ALL
```

表示 `alice` 可以透過 sudo 執行所有命令。

如果看到：

```text
(root) NOPASSWD: /usr/bin/systemctl restart nginx
```

表示可以不輸入密碼，以 root 身分執行：

```bash
sudo /usr/bin/systemctl restart nginx
```

如果只允許特定命令，通常必須使用完全符合的路徑及參數。

---

## 3. 以其他使用者身分執行

使用 `-u`：

```bash
sudo -u 使用者 命令
```

例如：

```bash
sudo -u postgres whoami
```

輸出：

```text
postgres
```

以 `www-data` 身分執行：

```bash
sudo -u www-data id
```

預設的：

```bash
sudo command
```

相當於：

```bash
sudo -u root command
```

---

## 4. 進入 root Shell

### 登入型 root shell

```bash
sudo -i
```

提示符可能變成：

```text
root@host:~#
```

`-i` 會模擬 root 登入，切換到 root 的家目錄並載入相應環境。

離開：

```bash
exit
```

### 一般 root shell

```bash
sudo -s
```

它會啟動 root shell，但較多保留目前使用者的環境和目錄。

一般建議：

- 只執行一條命令：`sudo command`
    
- 需要完整 root 登入環境：`sudo -i`
    
- 臨時取得 root shell：`sudo -s`
    

不要長時間停留在 root shell，以免誤操作系統檔案。

---

## 5. `sudo` 和 `su` 的差異

|命令|說明|
|---|---|
|`sudo command`|使用自己的 sudo 權限執行命令|
|`sudo -i`|透過 sudo 進入 root 登入 shell|
|`su`|切換使用者，通常需要目標使用者密碼|
|`su -`|切換使用者並載入其登入環境|
|`sudo -u alice command`|以 alice 身分執行命令|

通常：

```bash
sudo -i
```

要求目前使用者的密碼；而：

```bash
su -
```

通常要求 root 的密碼。

---

## 6. 密碼快取

成功驗證後，sudo 通常會在短時間內記住驗證狀態。

提前更新驗證時間：

```bash
sudo -v
```

取消目前快取：

```bash
sudo -k
```

下一次使用 sudo 時會重新要求密碼：

```bash
sudo -k
sudo id
```

在共用或不可信任的電腦上，完成工作後可以執行：

```bash
sudo -k
```

---

## 7. 非互動模式

使用 `-n` 禁止詢問密碼：

```bash
sudo -n id
```

如果需要密碼，會直接失敗：

```text
sudo: a password is required
```

這適合 shell script，避免程式卡在密碼提示。

例如：

```bash
if sudo -n true 2>/dev/null; then
    echo "sudo is currently available"
else
    echo "sudo requires authentication"
fi
```

不要把密碼直接寫入腳本或命令列。

---

## 8. `sudo` 與輸出重新導向

這是一個常見陷阱：

```bash
sudo echo "hello" > /etc/example.conf
```

即使 `echo` 由 root 執行，`>` 仍由目前的普通 shell 處理，因此可能出現：

```text
Permission denied
```

正確方式之一是使用 `tee`：

```bash
printf '%s\n' 'hello' |
sudo tee /etc/example.conf >/dev/null
```

附加內容使用 `tee -a`：

```bash
printf '%s\n' 'another line' |
sudo tee -a /etc/example.conf >/dev/null
```

也可以讓 root shell 處理重新導向：

```bash
sudo sh -c 'printf "%s\n" "hello" > /etc/example.conf'
```

但內容複雜時需要特別留意引號與變數展開。

---

## 9. 使用 sudo 編輯系統檔案

可以直接執行：

```bash
sudo nano /etc/hosts
```

不過更推薦：

```bash
sudoedit /etc/hosts
```

或：

```bash
sudo -e /etc/hosts
```

`sudoedit` 的流程是：

1. 建立受保護檔案的暫存副本
    
2. 使用目前使用者的編輯器修改
    
3. 完成後再以適當權限寫回
    

指定編輯器：

```bash
EDITOR=vim sudoedit /etc/hosts
```

---

## 10. 為什麼 `sudo cd` 不工作

下面通常無效：

```bash
sudo cd /root
```

因為 `cd` 是 shell 內建命令，而且子程序無法改變父 shell 的目前目錄。

若要進入 root 的家目錄：

```bash
sudo -i
```

若只想在指定目錄執行命令：

```bash
sudo sh -c 'cd /root && ls -la'
```

查看 root 目錄而不切換 shell：

```bash
sudo ls -la /root
```

---

## 11. 保留環境變數

sudo 預設會清除或重設部分環境變數。

嘗試保留目前環境：

```bash
sudo -E command
```

例如：

```bash
sudo -E env
```

但是否允許保留環境，仍由 sudoers 設定決定。

也可以明確傳入單一變數：

```bash
sudo MY_SETTING=value command
```

不要隨意保留 `PATH`、動態函式庫或程式執行相關環境變數，否則可能產生安全問題。

---

## 12. 執行上一條命令

如果忘記加 sudo：

```bash
pacman -S nmap
```

可以使用：

```bash
sudo !!
```

`!!` 會由 shell 展開成上一條命令。

執行前最好確認上一條命令內容，尤其是刪除、格式化或修改磁碟的操作。

---

## 13. Sudoers 設定

sudo 權限主要設定於：

```text
/etc/sudoers
/etc/sudoers.d/
```

不要直接用一般編輯器修改 `/etc/sudoers`，應使用：

```bash
sudo visudo
```

`visudo` 會檢查語法，避免錯誤導致 sudo 完全無法使用。

基本規則範例：

```sudoers
alice ALL=(ALL:ALL) ALL
```

表示 `alice` 可以輸入密碼後，以任何使用者與群組執行所有命令。

只允許重新啟動 nginx：

```sudoers
alice ALL=(root) /usr/bin/systemctl restart nginx
```

不需要密碼：

```sudoers
alice ALL=(root) NOPASSWD: /usr/bin/systemctl restart nginx
```

`NOPASSWD` 應只授予必要且精確限定的命令。

---

## 14. CTF 中的基本檢查

取得 shell 後，通常先執行：

```bash
whoami
id
sudo -l
```

特別觀察：

```text
(ALL) ALL
NOPASSWD
SETENV
```

以及允許執行的程式，例如：

```text
/usr/bin/vim
/usr/bin/find
/usr/bin/python
/usr/bin/bash
/usr/bin/systemctl
自製腳本
包含萬用字元的命令
```

有些程式能啟動子命令或 shell，因此錯誤的 sudo 設定可能導致權限提升。CTF 中可以進一步分析；真實系統只能在獲得授權的情況下測試。

---

## 15. 常見錯誤

### 使用者沒有 sudo 權限

```text
alice is not in the sudoers file
```

表示該帳號未被允許使用 sudo。

### 密碼錯誤

```text
Sorry, try again.
```

通常要輸入目前使用者自己的密碼。

### 命令不被允許

```text
Sorry, user alice is not allowed to execute ...
```

使用：

```bash
sudo -l
```

確認允許的完整命令、參數和目標使用者。

### 找不到命令

```text
sudo: command: command not found
```

sudo 可能使用不同的安全 `PATH`。可以查看：

```bash
sudo -l
```

必要時使用完整路徑：

```bash
sudo /usr/bin/systemctl status sshd
```

---

## 常用命令速查

```bash
# 以 root 執行命令
sudo command

# 查看 sudo 權限
sudo -l

# 以其他使用者執行
sudo -u username command

# 進入 root 登入 shell
sudo -i

# 進入一般 root shell
sudo -s

# 更新驗證時間
sudo -v

# 清除驗證快取
sudo -k

# 非互動模式
sudo -n command

# 編輯受保護檔案
sudoedit /etc/example.conf

# 寫入 root 才能修改的檔案
printf '%s\n' 'content' | sudo tee /etc/example.conf >/dev/null

# 查看除錯／版本資訊
sudo --version
```