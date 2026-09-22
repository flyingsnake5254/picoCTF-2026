- [1. 安裝 CUPP](#1-安裝-cupp)
  - [Kali Linux](#kali-linux)
  - [Manjaro／其他 Linux](#manjaro其他-linux)
- [2. 主要參數](#2-主要參數)
- [3. 互動式產生字典](#3-互動式產生字典)
  - [虛構範例](#虛構範例)
- [4. 各問題的意義](#4-各問題的意義)
  - [額外關鍵字](#額外關鍵字)
  - [特殊符號](#特殊符號)
  - [隨機數字](#隨機數字)
  - [Leet mode](#leet-mode)
- [5. 檢查產生的字典](#5-檢查產生的字典)
- [6. 擴充現有字典](#6-擴充現有字典)
- [7. 修改 `cupp.cfg`](#7-修改-cuppcfg)
- [8. 下載分類字典](#8-下載分類字典)
- [9. 將字典用於 CTF](#9-將字典用於-ctf)
- [10. 常見問題](#10-常見問題)
  - [找不到 `cupp.cfg`](#找不到-cuppcfg)
  - [`python: command not found`](#python-command-not-found)
  - [輸出檔太大](#輸出檔太大)
  - [中文資料結果不理想](#中文資料結果不理想)



CUPP（Common User Passwords Profiler）是一個 Python 3 字典產生器。它把姓名、生日、暱稱、寵物、公司等資料組合成候選密碼，並可加入數字、特殊符號與 leet 變形。

它本身不會破解密碼，只負責產生 wordlist。官方目前的程式碼標示版本為 3.3.1。([GitHub](https://github.com/Mebus/cupp?utm_source=chatgpt.com "GitHub - Mebus/cupp: Common User Passwords Profiler (CUPP)"))

僅應用於 CTF、自有帳號、密碼強度稽核或已獲授權的測試。

## 1. 安裝 CUPP

### Kali Linux

先嘗試套件管理器：

```bash
sudo apt update
sudo apt install cupp
```

確認：

```bash
cupp -h
```

如果套件沒有提供，或想使用官方原始碼：

```bash
git clone https://github.com/Mebus/cupp.git
cd cupp
python3 cupp.py -h
```

### Manjaro／其他 Linux

直接取得官方原始碼：

```bash
git clone https://github.com/Mebus/cupp.git
cd cupp
python3 cupp.py -h
```

CUPP 官方版本只要求 Python 3，主要程式與設定檔分別是 `cupp.py` 和 `cupp.cfg`。([GitHub](https://github.com/Mebus/cupp?utm_source=chatgpt.com "GitHub - Mebus/cupp: Common User Passwords Profiler (CUPP)"))

---

## 2. 主要參數

```bash
python3 cupp.py -h
```

主要選項：

|選項|用途|
|---|---|
|`-i`|互動式輸入資料並產生字典|
|`-w FILE`|擴充現有字典|
|`-l`|下載大型分類字典|
|`-a`|處理舊式 Alecto 預設帳密資料|
|`-v`|顯示版本|
|`-h`|顯示說明|

官方專案列出的核心功能即為 `-i`、`-w`、`-l`、`-a` 和 `-v`。([GitHub](https://github.com/Mebus/cupp?utm_source=chatgpt.com "GitHub - Mebus/cupp: Common User Passwords Profiler (CUPP)"))

---

## 3. 互動式產生字典

最常用模式：

```bash
python3 cupp.py -i
```

如果是 Kali 套件版，也可能直接執行：

```bash
cupp -i
```

它會依序詢問：

```text
First Name
Surname
Nickname
Birthdate
Partner's name
Partner's nickname
Partner's birthdate
Child's name
Child's nickname
Child's birthdate
Pet's name
Company name
```

不知道的欄位直接按 Enter 即可；第一個名字通常不能留空。生日格式為：

```text
DDMMYYYY
```

例如：

```text
23081998
```

官方程式還會詢問額外關鍵字、特殊符號、隨機數字與 leet mode。([GitHub](https://github.com/Mebus/cupp/blob/master/cupp.py?utm_source=chatgpt.com "cupp/cupp.py at master · Mebus/cupp"))

### 虛構範例

```text
> First Name: alice
> Surname: chen
> Nickname: ali
> Birthdate (DDMMYYYY): 23081998

> Partners) name:
> Partners) nickname:
> Partners) birthdate (DDMMYYYY):

> Child's name:
> Child's nickname:
> Child's birthdate (DDMMYYYY):

> Pet's name: milo
> Company name: acme

> Do you want to add some key words about the victim? Y/[N]: y
> Please enter the words, separated by comma: linux,coffee,dragon

> Do you want to add special chars at the end of words? Y/[N]: y
> Do you want to add some random numbers at the end of words? Y/[N]: y
> Leet mode? (i.e. leet = 1337) Y/[N]: y
```

完成後通常產生：

```text
alice.txt
```

內容可能包含：

```text
alice
Alice
alice1998
alice23
alice!
alice123
alicemilo
milo1998
4l1c3
coffee23
```

實際結果取決於 `cupp.cfg`。

---

## 4. 各問題的意義

### 額外關鍵字

```text
Do you want to add some key words about the victim?
```

選擇 `y` 後，可以輸入：

```text
linux,coffee,dragon
```

以逗號分隔。官方程式會移除這個輸入中的空格，因此多字詞若需要保留特定格式，最好自行確認輸出。([GitHub](https://github.com/Mebus/cupp/blob/master/cupp.py?utm_source=chatgpt.com "cupp/cupp.py at master · Mebus/cupp"))

### 特殊符號

```text
Do you want to add special chars at the end of words?
```

會產生類似：

```text
alice!
alice@
alice!!
alice!@
```

特殊符號由 `cupp.cfg` 決定。

### 隨機數字

```text
Do you want to add some random numbers at the end of words?
```

名稱叫 random numbers，但實際上通常是依設定檔中的數字範圍依序產生，例如：

```text
alice0
alice1
alice2
alice10
alice99
```

啟用後字典數量可能快速增加。

### Leet mode

```text
Leet mode? (i.e. leet = 1337)
```

會依 `cupp.cfg` 進行字元替換，例如：

```text
a → 4
i → 1
e → 3
t → 7
o → 0
s → 5
```

例如：

```text
alice → 4l1c3
password → p455w0rd
```

官方程式目前針對 `a i e t o s g z` 讀取 leet 替換設定。([GitHub](https://github.com/Mebus/cupp/blob/master/cupp.py?utm_source=chatgpt.com "cupp/cupp.py at master · Mebus/cupp"))

---

## 5. 檢查產生的字典

查看行數：

```bash
wc -l alice.txt
```

查看前 20 筆：

```bash
head -n 20 alice.txt
```

查看最後 20 筆：

```bash
tail -n 20 alice.txt
```

搜尋特定組合：

```bash
rg -i 'alice.*1998' alice.txt
```

確認是否有重複：

```bash
sort alice.txt | uniq -d | head
```

重新排序並去除重複：

```bash
sort -u alice.txt > alice-unique.txt
```

依長度篩選，例如只保留 8～16 字元：

```bash
awk 'length($0) >= 8 && length($0) <= 16' alice.txt \
  > alice-8-16.txt
```

檢查檔案大小：

```bash
du -h alice.txt
```

---

## 6. 擴充現有字典

假設已有：

```text
base.txt
```

內容為：

```text
alice
milo
dragon
coffee
```

執行：

```bash
python3 cupp.py -w base.txt
```

CUPP 會詢問是否：

- 組合不同單字
    
- 加入年份
    
- 加入特殊符號
    
- 加入數字
    
- 啟用 leet mode
    

產生的檔案通常為：

```text
base.txt.cupp.txt
```

官方程式的 `-w` 會讀取既有字典、建立組合、移除重複並輸出為原檔名加 `.cupp.txt`。([GitHub](https://github.com/Mebus/cupp/blob/master/cupp.py?utm_source=chatgpt.com "cupp/cupp.py at master · Mebus/cupp"))

注意不要對很大的字典啟用「組合所有單字」：

```text
word1 + word2
```

若原本有 nn 個單字，兩兩組合可能接近 n2n^2，檔案大小和記憶體用量會快速上升。

---

## 7. 修改 `cupp.cfg`

設定檔位於 CUPP 目錄：

```bash
less cupp.cfg
```

先備份：

```bash
cp cupp.cfg cupp.cfg.bak
```

編輯：

```bash
nano cupp.cfg
```

主要設定通常包括：

```ini
[years]
years = ...

[specialchars]
chars = ...

[nums]
from = ...
to = ...
wcfrom = ...
wcto = ...

[leet]
a = 4
i = 1
e = 3
...
```

含義：

|設定|用途|
|---|---|
|`years`|附加到單字的年份|
|`chars`|使用的特殊符號|
|`from`／`to`|數字變形範圍|
|`wcfrom`／`wcto`|保留候選字的長度範圍|
|`leet`|leet 字元替換規則|
|`threshold`|防止大型字典組合爆炸的門檻|

程式確實從設定檔載入年份、特殊字元、數字範圍、字長限制、組合門檻和 leet 對照。([GitHub](https://github.com/Mebus/cupp/blob/master/cupp.py?utm_source=chatgpt.com "cupp/cupp.py at master · Mebus/cupp"))

修改後從 CUPP 專案目錄執行，確保程式找得到：

```text
cupp.cfg
```

---

## 8. 下載分類字典

```bash
python3 cupp.py -l
```

它會顯示分類，例如：

```text
computer
names
music
places
science
dictionaries
```

選擇編號後會下載到：

```text
dictionaries/
```

這是較舊的下載功能，來源伺服器可能失效，而且部分字典很大。官方程式目前仍保留這個選項及分類清單。([GitHub](https://github.com/Mebus/cupp/blob/master/cupp.py?utm_source=chatgpt.com "cupp/cupp.py at master · Mebus/cupp"))

通常在 CTF 中，`-i` 或 `-w` 比 `-l` 更實用。

---

## 9. 將字典用於 CTF

產生結果後，可提供給離線密碼稽核工具：

```bash
john --wordlist=alice.txt hashes.txt
```

或：

```bash
hashcat -m HASH_MODE hashes.txt alice.txt
```

`HASH_MODE` 必須依 hash 類型選擇，不能隨便填。例如先辨識：

```bash
hashid 'HASH內容'
```

查看 Hashcat 模式：

```bash
hashcat --example-hashes | less
```

更合理的流程是：

```text
收集題目線索
    ↓
CUPP 產生針對性字典
    ↓
篩選長度、去除重複
    ↓
對 CTF 提供的離線 hash 測試
```

CUPP 的優勢不是字典特別大，而是候選內容與已知線索相關。

---

## 10. 常見問題

### 找不到 `cupp.cfg`

```text
Configuration file cupp.cfg not found!
```

代表你可能不在 CUPP 目錄：

```bash
cd cupp
python3 cupp.py -i
```

確認檔案：

```bash
ls -l cupp.py cupp.cfg
```

### `python: command not found`

使用 Python 3：

```bash
python3 cupp.py -i
```

### 輸出檔太大

可能啟用了：

- 特殊符號
    
- 數字範圍
    
- 所有單字互相組合
    
- 過多額外關鍵字
    

重新執行時縮小條件，或調整 `cupp.cfg`。

### 中文資料結果不理想

CUPP 的組合邏輯主要針對英文字母、數字和常見 leet 變形。中文姓名可以改用：

- 英文名
    
- 羅馬拼音
    
- 暱稱
    
- 帳號名稱
    
- 縮寫
    

例如：

```text
王小明 → wangxiaoming,xiaoming,wangxm,xmwang
```

再將這些放入額外關鍵字。

---

最常用的三條命令是：

```bash
# 查看說明
python3 cupp.py -h

# 根據資料互動產生字典
python3 cupp.py -i

# 擴充現有字典
python3 cupp.py -w base.txt
```