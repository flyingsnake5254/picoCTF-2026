- [1. 腳本基本結構](#1-腳本基本結構)
- [2. 腳本參數](#2-腳本參數)
- [3. 變數](#3-變數)
- [4. 讀取指令結果](#4-讀取指令結果)
- [5. 整數運算](#5-整數運算)
- [6. 讀取使用者輸入](#6-讀取使用者輸入)
- [7. if 條件判斷](#7-if-條件判斷)
- [8. 數字比較](#8-數字比較)
- [9. 字串比較](#9-字串比較)
- [10. 檔案判斷](#10-檔案判斷)
- [11. case 多條件判斷](#11-case-多條件判斷)
- [12. for 迴圈](#12-for-迴圈)
  - [處理多個檔案](#處理多個檔案)
  - [只處理一般檔案](#只處理一般檔案)
  - [處理所有腳本參數](#處理所有腳本參數)
  - [數字範圍](#數字範圍)
- [13. while 迴圈](#13-while-迴圈)
- [14. 函式](#14-函式)
- [15. 輸入、輸出重新導向](#15-輸入輸出重新導向)
  - [覆蓋寫入](#覆蓋寫入)
  - [附加到檔案末尾](#附加到檔案末尾)
  - [將檔案做為標準輸入](#將檔案做為標準輸入)
  - [將錯誤訊息寫入檔案](#將錯誤訊息寫入檔案)
  - [同時儲存標準輸出和錯誤](#同時儲存標準輸出和錯誤)
  - [忽略所有輸出](#忽略所有輸出)
- [16. 管線](#16-管線)
  - [將上一條指令的輸出交給下一條指令](#將上一條指令的輸出交給下一條指令)
- [17. 指令成功與失敗](#17-指令成功與失敗)
  - [每個指令執行後之結束狀態](#每個指令執行後之結束狀態)
  - [只有前一條成功才執行下一條](#只有前一條成功才執行下一條)
  - [只有前一條失敗才執行下一條](#只有前一條失敗才執行下一條)
- [18. 陣列](#18-陣列)


## 1. 腳本基本結構

```bash
#!/usr/bin/env bash

# 這是註解
echo "Hello, Shell!"
```

執行方式：

```bash
chmod +x script.sh
./script.sh
```

也可以不修改權限，直接使用：

```bash
bash script.sh
```

## 2. 腳本參數

```bash
./script.sh file1.txt file2.txt
```

腳本可以使用以下取得參數：

|語法|意義|
|---|---|
|`$0`|腳本名稱|
|`$1`|第一個參數|
|`$2`|第二個參數|
|`$#`|參數數量|
|`"$@"`|所有參數，個別保留|
|`$?`|上一條指令的結束狀態|
|`$$`|目前 Shell 的 PID|

範例：

```bash
#!/usr/bin/env bash

echo "腳本名稱：$0"
echo "第一個參數：$1"
echo "參數數量：$#"

for argument in "$@"; do
    echo "參數：$argument"
done
```

## 3. 變數

**變數賦值時，等號兩側不能有空白**

```bash
name="Sherloxk"
age=20

echo "$name"
echo "Name: $name, Age: $age"
```

單引號：

```bash
echo 'Hello, $name'
```

輸出：`Hello, $name`

## 4. 讀取指令結果

```bash
current_directory=$(pwd)
current_date=$(date)

echo "$current_directory"
```

## 5. 整數運算

```bash
a=10
b=20
result=$((a + b))

echo "$result"
```

其他運算

```bash
result=$((a * b))
result=$((a / b))
result=$((a % b))
```

## 6. 讀取使用者輸入

```bash
read -r -p "請輸入名字：" name
echo "Hello, $name"
```

隱藏輸入內容，例如密碼：

```bash
read -r -s -p "Password: " password
echo
```


## 7. if 條件判斷

基本格式：

```bash
if [[ 條件 ]]; then
    指令
elif [[ 其他條件 ]]; then
    指令
else
    指令
fi
```


範例：

```bash
age=20

if [[ $age -ge 18 ]]; then
    echo "成年"
else
    echo "未成年"
fi
```


## 8. 數字比較

|運算|意義|
|---|---|
|`-eq`|等於|
|`-ne`|不等於|
|`-gt`|大於|
|`-ge`|大於等於|
|`-lt`|小於|
|`-le`|小於等於|

例子：

```bash
if [[ $a -eq $b ]]; then
    echo "相等"
fi
```

## 9. 字串比較

```bash
if [[ "$name" == "admin" ]]; then
    echo "Administrator"
fi

if [[ -z "$name" ]]; then
    echo "字串為空"
fi

if [[ -n "$name" ]]; then
    echo "字串不為空"
fi
```


## 10. 檔案判斷

|條件|意義|
|---|---|
|`-e "$path"`|路徑存在|
|`-f "$path"`|是一般檔案|
|`-d "$path"`|是資料夾|
|`-r "$path"`|可以讀取|
|`-w "$path"`|可以寫入|
|`-x "$path"`|可以執行|
|`-s "$path"`|檔案非空|

範例：

```bash
file="test.txt"

if [[ -f "$file" ]]; then
    echo "$file 是一般檔案"
else
    echo "檔案不存在"
fi
```


## 11. case 多條件判斷

```bash
read -r -p "請輸入操作：" action

case "$action" in
    start)
        echo "啟動"
        ;;
    stop)
        echo "停止"
        ;;
    restart)
        echo "重新啟動"
        ;;
    *)
        echo "未知操作"
        ;;
esac
```


## 12. for 迴圈

### 處理多個檔案

```bash
for file in files/*; do
    echo "$file"
done
```

### 只處理一般檔案

```bash
for file in files/*; do
    if [[ -f "$file" ]]; then
        echo "Processing: $file"
    fi
done
```

### 處理所有腳本參數

```bash
for file in "$@"; do
    echo "Processing: $file"
done
```

### 數字範圍

```bash
for number in {1..5}; do
    echo "$number"
done
```

或使用 C 語言型式：

```bash
for ((i = 0; i < 5; i++)); do
    echo "$i"
done
```


## 13. while 迴圈

```bash
count=1

while [[ $count -le 5 ]]; do
    echo "$count"
    count=$((count + 1))
done
```

逐行讀取檔案：

```bash
while IFS= read -r line; do
    echo "$line"
done < input.txt
```

這種寫法可以保留每行的空白與反斜線


## 14. 函式

```bash
greet() {
    local name="$1"
    echo "Hello, $name"
}

greet "Alice"
greet "Bob"
```

回傳成功或失敗狀態：

```bash
check_file() {
    local file="$1"

    if [[ -f "$file" ]]; then
        return 0
    else
        return 1
    fi
}

if check_file "test.txt"; then
    echo "檔案存在"
else
    echo "檔案不存在"
fi
```


## 15. 輸入、輸出重新導向

### 覆蓋寫入

```bash
echo "Hello" > output.txt
```


### 附加到檔案末尾

```bash
echo "World" >> output.txt
```

### 將檔案做為標準輸入

```bash
./decrypt.sh < encrypted.txt
```


### 將錯誤訊息寫入檔案

```bash
command 2> error.log
```

### 同時儲存標準輸出和錯誤

```bash
command > output.log 2>&1
```

### 忽略所有輸出

```bash
command > /dev/null 2>&1
```


## 16. 管線

### 將上一條指令的輸出交給下一條指令

```bash
cat file.txt | grep "flag"
```

```bash
cat encoded.txt | base64 -d
```


## 17. 指令成功與失敗

### 每個指令執行後之結束狀態

```bash
ls file.txt
echo "$?"
```

成功通常輸出：`0`


### 只有前一條成功才執行下一條

```bash
mkdir output && echo "建立成功"
```


### 只有前一條失敗才執行下一條

```bash
cat missing.txt || echo "讀取失敗"
```


## 18. 陣列

```bash
files=("a.txt" "b.txt" "file name.txt")

echo "${files[0]}"
echo "${files[1]}"
```

處理所有元素：

```bash
for file in "${files[@]}"; do
    echo "$file"
done
```

取得元素數量：

```bash
echo "${#files[@]}"
```