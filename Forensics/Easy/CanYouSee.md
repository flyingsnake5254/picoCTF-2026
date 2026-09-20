## 題目

https://learn.cylabacademy.org/library/408?page=1&category=4

How about some hide and seek?

Download this file [here](https://artifacts.picoctf.net/c_titan/4/unknown.zip).


## 解題

>[探查面向](../../筆記/圖片探查面向.md)

附檔解壓縮後，得到以下：

```css
unknown
└── ukn_reality.jpg
```

先使用 `exiftool` 探查圖片：

```bash
exiftool ukn_reality.jpg
```

輸出：

```bash
ExifTool Version Number         : 13.55
File Name                       : ukn_reality.jpg
Directory                       : .
File Size                       : 2.3 MB
File Modification Date/Time     : 2024:02:15 17:40:14-05:00
File Access Date/Time           : 2026:09:19 23:14:01-04:00
File Inode Change Date/Time     : 2026:09:19 23:13:52-04:00
File Permissions                : -rwxrwx---
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : inches
X Resolution                    : 72
Y Resolution                    : 72
XMP Toolkit                     : Image::ExifTool 11.88
Attribution URL                 : cGljb0NURntNRTc0RDQ3QV9ISUREM05fZGVjYTA2ZmJ9Cg==
Image Width                     : 4308
Image Height                    : 2875
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 4308x2875
Megapixels                      : 12.4
```

看到 `Attribution URL` 很可疑，故使用 base64 解碼：

```bash
echo "cGljb0NURntNRTc0RDQ3QV9ISUREM05fZGVjYTA2ZmJ9Cg==" | base64 -d
```

輸出 flag :  
`picoCTF{ME74D47A_HIDD3N_deca06fb}`

