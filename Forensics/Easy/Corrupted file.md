## 題目

https://learn.cylabacademy.org/library/519?page=1&category=4

This file seems broken... or is it? Maybe a couple of bytes could make all the difference. Can you figure out how to bring it back to life?

Download the file [here](https://challenge-files.picoctf.net/c_amiable_citadel/1d613da899d7f96093bbd88b62a4497b850484fa7f0501e55af4052364a0f0fd/file).


## 解題

>[圖片探查面向](../../筆記/圖片探查面向.md)

使用 `exiftool` 探查 `file` ：

```bash
exiftool file
```

```bash
ExifTool Version Number         : 13.55
File Name                       : file
Directory                       : .
File Size                       : 8.8 kB
File Modification Date/Time     : 2026:09:19 00:47:54-04:00
File Access Date/Time           : 2026:09:19 00:51:40-04:00
File Inode Change Date/Time     : 2026:09:19 00:55:16-04:00
File Permissions                : -rwxrwx---
Error                           : Unknown file type
```

發現是 **Unknown file type**

使用 `xxd` 探查：
```bash
xxd file | head
```

```bash
00000000: 5c78 ffe0 0010 4a46 4946 0001 0100 0001  \x....JFIF......
00000010: 0001 0000 ffdb 0043 0008 0606 0706 0508  .......C........
00000020: 0707 0709 0908 0a0c 140d 0c0b 0b0c 1912  ................
00000030: 130f 141d 1a1f 1e1d 1a1c 1c20 242e 2720  ........... $.' 
00000040: 222c 231c 1c28 3729 2c30 3134 3434 1f27  ",#..(7),01444.'
00000050: 393d 3832 3c2e 3334 32ff db00 4301 0909  9=82<.342...C...
00000060: 090c 0b0c 180d 0d18 3221 1c21 3232 3232  ........2!.!2222
00000070: 3232 3232 3232 3232 3232 3232 3232 3232  2222222222222222
00000080: 3232 3232 3232 3232 3232 3232 3232 3232  2222222222222222
00000090: 3232 3232 3232 3232 3232 3232 3232 ffc0  22222222222222..
```

發現出現 **JFIF**，但開頭 3 Bytes (**5C 78 FF**) 並非 jpg 的 Magic Bytes **FF D8 FF** ，代表此檔案的前兩個 Bytes 損毀了，故撰寫 python 程式，將前兩個 Bytes **5C 78** 修復回 **FF D8** ：

```python
with open("file", "rb") as f:
    data = f.read()   

# JPEG : FF D8 FF
fix_data = b''
for i in range(len(data)):
    if i == 0:
        fix_data += bytes([0xFF])
    elif i == 1:
        fix_data += bytes([0xD8])
    else:
        fix_data += bytes([data[i]])


with open("output.jpg", "wb") as f:
    f.write(fix_data)
```

最後輸出 `output.jpg` 即可看到 flag :  

~[](../../assets/images/Forensics/Easy/Corrupted%20file1.png)

picoCTF{r3st0r1ng_th3_by73s_b67c1558}

