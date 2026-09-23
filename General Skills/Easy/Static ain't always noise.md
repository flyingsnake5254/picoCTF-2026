## 題目

https://learn.cylabacademy.org/library/163?page=4&category=5

Can you look at the data in this binary? The bash script might help!

[static](https://challenge-files.picoctf.net/c_wily_courier/34dfb62cf2c94a618c7cdc292ff1c4062b104773695071e9a16ab25ad8cc935c/static), [ltdis.sh](https://challenge-files.picoctf.net/c_wily_courier/34dfb62cf2c94a618c7cdc292ff1c4062b104773695071e9a16ab25ad8cc935c/ltdis.sh)



## 解題

下載附檔 `ltdis.sh` 、`static` 後，放在同一目錄，執行 `ltdis.sh` ，並把 `static` 當成第一參數：

```bash
sh ltdis.sh static
```

輸出：

```bash
Attempting disassembly of static ...
Disassembly successful! Available at: static.ltdis.x86_64.txt
Ripping strings from binary with file offsets...
Any strings found in static have been written to static.ltdis.strings.txt with file offset
```

然後使用 `ls` 查看新增哪些檔案：

```bash
ltdis.sh  static  static.ltdis.strings.txt  static.ltdis.x86_64.txt
```

輸出 `static.ltdis.strings.txt` 內容，並使用 `grep` 過濾：

```bash
grep -rn "picoCTF" static.ltdis.strings.txt
```

輸出：

```bash
14:   3020 picoCTF{d15a5m_t34s3r_20335e41}
```

取得 flag :  

```bash
picoCTF{d15a5m_t34s3r_20335e41}
```