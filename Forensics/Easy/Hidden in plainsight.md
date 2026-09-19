## 題目

https://learn.cylabacademy.org/library/524?page=1&category=4

You’re given a seemingly ordinary JPG image. Something is tucked away out of sight inside the file. Your task is to discover the hidden payload and extract the flag.

Download the jpg image [here](https://challenge-files.picoctf.net/c_amiable_citadel/58f49cf585a3e6073ccd6a3ae03903dfd71567a2db9101645b428ac6820fb3cf/img.jpg).


## 附檔

![](../../assets/images/Forensics/Easy/Hidden%20in%20plainsight1.jpg)



## 解題

>[探查面向](../../筆記/圖片探查面向.md)
>[OpenSSL](../../筆記/OpenSSL.md)

先使用 `exiftool` 探查圖片：

```bash
exiftool img.jpg
```

```bash
ExifTool Version Number         : 13.55
File Name                       : img.jpg
Directory                       : .
File Size                       : 74 kB
File Modification Date/Time     : 2026:09:18 23:41:03-04:00
File Access Date/Time           : 2026:09:18 23:42:44-04:00
File Inode Change Date/Time     : 2026:09:18 23:42:34-04:00
File Permissions                : -rwxrwx---
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
Comment                         : c3RlZ2hpZGU6Y0VGNmVuZHZjbVE9
Image Width                     : 640
Image Height                    : 640
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 640x640
Megapixels                      : 0.410
```

發現 Comment 很可疑，使用 OpenSSL 進行 base64 解碼：

```bash
echo "c3RlZ2hpZGU6Y0VGNmVuZHZjbVE9" > enc
```

```bash
openssl base64 -d -in enc -out flag
```

```bash
cat flag
```

輸出：

```bash
steghide:cEF6endvcmQ=
```


得到兩個提示：
- **steghide**
- **cEF6endvcmQ=**

先將 `cEF6endvcmQ=` 再次使用 OpenSSL 進行 base64 解碼：

```bash
echo "cEF6endvcmQ=" > enc2
```

```bash
openssl base64 -d -in enc2 -out flag2
```

```bash
cat flag2
```

輸出：

```bash
pAzzword
```

再使用 `steghide` 探查圖片：

```bash
steghide info img.jpg
```

```bash
"img.jpg":
  format: jpeg
  capacity: 4.0 KB
Try to get information about embedded data ? (y/n) y
Enter passphrase: 
  embedded file "flag.txt":
    size: 34.0 Byte
    encrypted: rijndael-128, cbc
    compressed: yes
```

其中 `Enter passphrase:` 輸入 `pAzzword` 

發現存在 `flag.txt` 檔案

利用 `steghide` 取出該檔案：

```bash
steghide extract -sf img.jpg
Enter passphrase: 
wrote extracted data to "flag.txt".
```

其中 Enter passphrase 輸入 `pAzzword`，成功取出 flag.txt，輸出 flag.txt 內容：

```bash
cat flag.txt
picoCTF{h1dd3n_1n_1m4g3_871ba555}
```