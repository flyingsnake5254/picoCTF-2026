## 題目

https://learn.cylabacademy.org/library/530?page=1&category=4

Hi, intrepid investigator! 📄🔍 You've stumbled upon a peculiar PDF filled with what seems like nothing more than garbled nonsense. But beware! Not everything is as it appears. Amidst the chaos lies a hidden treasure—an elusive flag waiting to be uncovered.

Find the PDF file here [Hidden Confidential Document](https://challenge-files.picoctf.net/c_amiable_citadel/ccce62e3458a26952b9b4ca1fae3254b0bf94516cacca39e06620c55063e6ef9/confidential.pdf) and uncover the flag within the metadata.


## 解題

使用 `exiftool` 探查附檔 `confidential.pdf` ：

```shell
exiftool confidential.pdf
```

得到以下：

```bash
ExifTool Version Number         : 13.55
File Name                       : confidential.pdf
Directory                       : .
File Size                       : 183 kB
File Modification Date/Time     : 2026:09:18 12:04:10-04:00
File Access Date/Time           : 2026:09:18 12:15:09-04:00
File Inode Change Date/Time     : 2026:09:18 12:15:09-04:00
File Permissions                : -rwxrwx---
File Type                       : PDF
File Type Extension             : pdf
MIME Type                       : application/pdf
PDF Version                     : 1.7
Linearized                      : No
Page Count                      : 1
Producer                        : PyPDF2
Author                          : cGljb0NURntwdXp6bDNkX20zdGFkYXRhX2YwdW5kIV84N2JlNjBjMH0=
```

可以看到 Author 很可疑，從結尾等號判斷使用 base64 編碼，故使用 OpenSSL 進行解碼：

```bash
echo "cGljb0NURntwdXp6bDNkX20zdGFkYXRhX2YwdW5kIV84N2JlNjBjMH0=" > flag_enc
```

```bash
openssl base64 -d -in flag_enc -out flag.txt
```

```bash
cat flag.txt

# 輸出
picoCTF{puzzl3d_m3tadata_f0und!_87be60c0}
```

