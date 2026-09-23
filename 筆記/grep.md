- [正則表達式](#正則表達式)
  - [12. 基本符號](#12-基本符號)
    - [行首](#行首)
    - [行尾](#行尾)
    - [空白行](#空白行)
    - [只包含數字的行](#只包含數字的行)
  - [13. 擴充正則表達式：`-E`](#13-擴充正則表達式-e)
    - [OR 條件](#or-條件)
    - [一個以上數字](#一個以上數字)
    - [指定重複次數](#指定重複次數)
    - [群組](#群組)
  - [14. 固定字串搜尋：`-F`](#14-固定字串搜尋-f)
  - [15. 顯示上下文](#15-顯示上下文)
  - [16. 多個搜尋條件](#16-多個搜尋條件)
  - [17. 安靜模式：`-q`](#17-安靜模式-q)
  - [18. Grep 的結束狀態](#18-grep-的結束狀態)
  - [19. 顯示顏色](#19-顯示顏色)
  - [20. 搜尋以 `-` 開頭的內容](#20-搜尋以---開頭的內容)
  - [21. 二進位檔案](#21-二進位檔案)
  - [22. 檔名搜尋與內容搜尋的差異](#22-檔名搜尋與內容搜尋的差異)
  - [23. 常見錯誤](#23-常見錯誤)
    - [忘記加引號](#忘記加引號)
    - [把 `*` 理解成任意字串](#把--理解成任意字串)
    - [搜尋特殊符號](#搜尋特殊符號)
    - [Grep 主要是逐行處理](#grep-主要是逐行處理)
- [CTF 常用指令](#ctf-常用指令)
  - [常用指令速查](#常用指令速查)



`grep` 是 Linux 中用來搜尋文字內容的工具。它會逐行檢查輸入，並輸出符合指定模式的行。

基本語法：

```bash
grep [選項] '搜尋模式' 檔案
```

例如：

```bash
grep 'flag' output.txt
```

這會輸出 `output.txt` 中所有包含 `flag` 的完整行。

## 1. 基本搜尋

搜尋單一檔案：

```bash
grep 'hello' file.txt
```

搜尋多個檔案：

```bash
grep 'hello' file1.txt file2.txt
```

搜尋萬用字元指定的檔案：

```bash
grep 'hello' *.txt
```

從其他命令的輸出搜尋：

```bash
some_command | grep 'flag'
```

例如：

```bash
ps aux | grep 'python'
```

```bash
ip addr | grep 'inet'
```

---

## 2. 不區分大小寫：`-i`

```bash
grep -i 'error' log.txt
```

可以匹配：

```text
error
ERROR
Error
eRrOr
```

同時顯示行號：

```bash
grep -in 'error' log.txt
```

---

## 3. 顯示行號：`-n`

```bash
grep -n 'password' config.txt
```

輸出：

```text
12:password=secret
```

表示匹配內容位於第 12 行。

---

## 4. 只輸出匹配部分：`-o`

一般 `grep` 會輸出整行：

```bash
grep 'picoCTF' data.txt
```

若一整行包含大量亂碼，使用 `-o` 只輸出符合的部分：

```bash
grep -oE 'picoCTF\{[^}]*\}' data.txt
```

輸出：

```text
picoCTF{grep_is_good_to_find_things_29f42460}
```

其中：

- `-o`：只顯示匹配部分
    
- `-E`：使用擴充正則表達式
    
- `picoCTF\{`：匹配 `picoCTF{`
    
- `[^}]*`：匹配任意數量的非 `}` 字元
    
- `\}`：匹配結尾 `}`
    

---

## 5. 反向匹配：`-v`

顯示不包含指定文字的行：

```bash
grep -v 'DEBUG' application.log
```

排除空白行：

```bash
grep -v '^$' file.txt
```

排除空白或只有空格的行：

```bash
grep -vE '^[[:space:]]*$' file.txt
```

---

## 6. 完整單字匹配：`-w`

```bash
grep -w 'cat' file.txt
```

會匹配：

```text
the cat is here
cat
```

不會匹配：

```text
category
concatenate
```

不分大小寫：

```bash
grep -iw 'admin' users.txt
```

---

## 7. 完整一行匹配：`-x`

```bash
grep -x 'admin' users.txt
```

只匹配內容完全等於：

```text
admin
```

不會匹配：

```text
admin123
user admin
```

---

## 8. 計算匹配行數：`-c`

```bash
grep -c 'error' log.txt
```

輸出匹配的行數。

注意：`-c` 計算「符合的行」，不是字串出現總次數。

如果一行中有三個 `error`，仍只算一行。

計算實際出現次數：

```bash
grep -o 'error' log.txt | wc -l
```

不分大小寫：

```bash
grep -oi 'error' log.txt | wc -l
```

---

## 9. 只顯示檔名：`-l`

列出包含指定內容的檔案：

```bash
grep -l 'password' *.txt
```

遞迴搜尋：

```bash
grep -rl 'password' .
```

列出不包含指定內容的檔案：

```bash
grep -rL 'password' .
```

---

## 10. 遞迴搜尋：`-r`

搜尋目前目錄及所有子目錄：

```bash
grep -r 'flag' .
```

常用組合：

```bash
grep -rin 'flag' .
```

意思是：

- `-r`：遞迴
    
- `-i`：不分大小寫
    
- `-n`：顯示行號
    

避免處理二進位檔案：

```bash
grep -rIn 'flag' .
```

其中大寫 `-I` 表示忽略二進位檔案。

`-R` 也會遞迴，但會跟隨符號連結：

```bash
grep -R 'flag' .
```

一般使用 `-r` 較安全。

---

## 11. 限制搜尋的檔案類型

只搜尋 `.rs`：

```bash
grep -rIn --include='*.rs' 'XORCryptor' .
```

搜尋多種檔案：

```bash
grep -rIn \
  --include='*.py' \
  --include='*.rs' \
  --include='*.c' \
  'password' .
```

排除 `.log`：

```bash
grep -rIn \
  --exclude='*.log' \
  'password' .
```

排除目錄：

```bash
grep -rIn \
  --exclude-dir='.git' \
  --exclude-dir='target' \
  --exclude-dir='node_modules' \
  'password' .
```

CTF 原始碼搜尋範例：

```bash
grep -rIn \
  --exclude-dir='.git' \
  --exclude-dir='target' \
  -E 'flag|secret|password|token|key' .
```

---

# 正則表達式

## 12. 基本符號

|符號|意義|
|---|---|
|`.`|任意單一字元|
|`*`|前一項出現 0 次以上|
|`^`|行首|
|`$`|行尾|
|`[abc]`|`a`、`b` 或 `c`|
|`[^abc]`|不是 `a`、`b`、`c`|
|`[0-9]`|任意數字|
|`[a-z]`|小寫英文字母|

### 行首

```bash
grep '^root' /etc/passwd
```

匹配以 `root` 開頭的行。

### 行尾

```bash
grep 'bash$' /etc/passwd
```

匹配以 `bash` 結尾的行。

### 空白行

```bash
grep '^$' file.txt
```

### 只包含數字的行

```bash
grep -E '^[0-9]+$' file.txt
```

---

## 13. 擴充正則表達式：`-E`

`-E` 啟用 ERE，方便使用：

```text
+
?
|
()
{m,n}
```

### OR 條件

```bash
grep -E 'error|warning|critical' log.txt
```

不分大小寫：

```bash
grep -Ei 'error|warning|critical' log.txt
```

### 一個以上數字

```bash
grep -E '[0-9]+' file.txt
```

### 指定重複次數

尋找 4 位數：

```bash
grep -E '\b[0-9]{4}\b' file.txt
```

### 群組

```bash
grep -E '(user|admin)_[0-9]+' file.txt
```

---

## 14. 固定字串搜尋：`-F`

如果搜尋內容不是正則表達式，使用 `-F`：

```bash
grep -F 'a.b[0]*' file.txt
```

這會把：

```text
a.b[0]*
```

視為普通文字，而不是正則符號。

搜尋 flag 也可以使用：

```bash
grep -F 'picoCTF{' file.txt
```

搜尋程式碼中的特殊符號：

```bash
grep -Fn 'array[index]' source.c
```

常用模式：

|模式|用途|
|---|---|
|`grep`|基本正則 BRE|
|`grep -E`|擴充正則 ERE|
|`grep -F`|固定字串，不解析正則|
|`grep -P`|Perl-compatible regex，GNU grep 才常見|

---

## 15. 顯示上下文

顯示匹配行及後面 3 行：

```bash
grep -A 3 'error' log.txt
```

`A` 表示 After。

顯示前面 3 行：

```bash
grep -B 3 'error' log.txt
```

`B` 表示 Before。

前後各 3 行：

```bash
grep -C 3 'error' log.txt
```

`C` 表示 Context。

例如搜尋程式中的函式：

```bash
grep -nC 5 'fn decrypt' main.rs
```

---

## 16. 多個搜尋條件

使用多個 `-e`：

```bash
grep \
  -e 'password' \
  -e 'secret' \
  -e 'token' \
  config.txt
```

等同於：

```bash
grep -E 'password|secret|token' config.txt
```

從檔案讀取搜尋條件：

```bash
grep -f patterns.txt data.txt
```

`patterns.txt`：

```text
password
secret
token
picoCTF
```

固定字串模式：

```bash
grep -Ff patterns.txt data.txt
```

---

## 17. 安靜模式：`-q`

只判斷是否找到，不顯示內容：

```bash
grep -q 'flag' file.txt
```

常用於 Shell 判斷：

```bash
if grep -qF 'picoCTF{' output.txt; then
    echo '找到 flag'
else
    echo '沒有找到'
fi
```

---

## 18. Grep 的結束狀態

執行後查看：

```bash
echo $?
```

|狀態碼|意義|
|--:|---|
|`0`|有找到|
|`1`|沒有找到|
|`2`|發生錯誤|

例如：

```bash
grep -q 'root' /etc/passwd
echo $?
```

適合在 Shell script 中做條件判斷。

---

## 19. 顯示顏色

```bash
grep --color=auto 'error' log.txt
```

常用別名：

```bash
alias grep='grep --color=auto'
```

如果要強制在 pipe 後保留顏色：

```bash
grep --color=always 'error' log.txt | less -R
```

---

## 20. 搜尋以 `-` 開頭的內容

假設要搜尋：

```text
-danger
```

需要使用 `--` 表示選項結束：

```bash
grep -- '-danger' file.txt
```

否則 grep 可能把它誤認成選項。

固定字串：

```bash
grep -F -- '-danger' file.txt
```

---

## 21. 二進位檔案

一般情況下，grep 可能顯示：

```text
binary file matches
```

強制把二進位檔當成文字：

```bash
grep -a 'flag' file.bin
```

只擷取 flag：

```bash
grep -aoE 'picoCTF\{[^}]*\}' file.bin
```

常見做法也包括：

```bash
strings file.bin |
grep -E 'picoCTF\{[^}]*\}'
```

顯示匹配內容的 byte offset：

```bash
grep -aob 'picoCTF{' file.bin
```

輸出可能是：

```text
4096:picoCTF{
```

表示字串大約從 offset 4096 開始。

---

## 22. 檔名搜尋與內容搜尋的差異

搜尋檔名使用 `find`：

```bash
find . -type f -iname '*flag*'
```

搜尋檔案內容使用 `grep`：

```bash
grep -rIn 'flag' .
```

兩者搭配：

```bash
find . -type f -name '*.txt' \
  -exec grep -nH 'flag' {} +
```

其中 `-H` 強制顯示檔名。

---

## 23. 常見錯誤

### 忘記加引號

不建議：

```bash
grep *.txt file
```

Shell 可能先展開 `*.txt`。

正確：

```bash
grep '*.txt' file
```

如果是搜尋固定文字：

```bash
grep -F '*.txt' file
```

### 把 `*` 理解成任意字串

在正則中：

```text
*
```

表示「前一個項目重複零次以上」，不是獨立的任意字串。

匹配任意字串應使用：

```text
.*
```

例如：

```bash
grep -E 'user.*admin' file.txt
```

### 搜尋特殊符號

若要搜尋：

```text
a+b
```

可以使用固定字串：

```bash
grep -F 'a+b' file.txt
```

比手動跳脫更簡單。

### Grep 主要是逐行處理

一般 `grep` 不適合匹配跨越多行的結構，例如：

```text
picoCTF{
multiline_flag
}
```

這種情況可使用 Perl、Python 或支援 multiline 的工具。

---

# CTF 常用指令

搜尋 flag：

```bash
grep -rIn 'picoCTF' .
```

只輸出 flag：

```bash
grep -rhoE 'picoCTF\{[^}]*\}' .
```

搜尋常見敏感字串：

```bash
grep -rIniE \
  'flag|secret|password|passwd|token|api[_-]?key' .
```

搜尋十六進位字串：

```bash
grep -oE '[0-9a-fA-F]{32,}' output.txt
```

搜尋 Base64 形式的長字串：

```bash
grep -oE '[A-Za-z0-9+/]{20,}={0,2}' output.txt
```

搜尋 IPv4：

```bash
grep -oE \
'([0-9]{1,3}\.){3}[0-9]{1,3}' \
file.txt
```

這只檢查格式，不保證每段都小於或等於 255。

搜尋 Email：

```bash
grep -oE \
'[[:alnum:]._%+-]+@[[:alnum:].-]+\.[[:alpha:]]{2,}' \
file.txt
```

---

## 常用指令速查

```bash
# 基本搜尋
grep 'text' file.txt

# 不分大小寫
grep -i 'text' file.txt

# 顯示行號
grep -n 'text' file.txt

# 只輸出匹配內容
grep -o 'text' file.txt

# 使用擴充正則
grep -E 'error|warning' file.txt

# 搜尋固定字串
grep -F 'a.b[0]' file.txt

# 排除匹配行
grep -v 'text' file.txt

# 遞迴搜尋
grep -r 'text' .

# 遞迴並顯示行號
grep -rIn 'text' .

# 只列出匹配的檔名
grep -rl 'text' .

# 顯示上下文
grep -nC 3 'text' file.txt

# 只輸出 picoCTF flag
grep -oE 'picoCTF\{[^}]*\}' file.txt
```

最實用的組合通常是：

```bash
grep -rInE 'pattern1|pattern2' 路徑
```

若只需要匹配部分：

```bash
grep -rhoE '指定格式' 路徑
```