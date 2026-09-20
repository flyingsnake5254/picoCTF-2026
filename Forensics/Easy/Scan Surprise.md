## 題目

https://learn.cylabacademy.org/library/444?page=1&category=4

I've gotten bored of handing out flags as text. Wouldn't it be cool if they were an image instead?

You can download the challenge files here:

- [challenge.zip](https://artifacts.picoctf.net/c_atlas/13/challenge.zip)


## 解題

>[[探查面向]]

先將 `challenge.zip` 解壓縮後，`challenge` 資料夾內容如下：

```css
└── home
    └── ctf-player
        └── drop-in
            └── flag.png
```

其中 `flag.png` 是一張 QRCode 照片：

![](../../assets/images/Forensics/Easy/Scan%20Surprise1.png)

使用 `zbarimg` 探查圖片，即可得到 flag :  

```bash
zbarimg flag.png
```

輸出：

```bash
QR-Code:picoCTF{p33k_@_b00_d4ca652e}
scanned 1 barcode symbols from 1 images in 0.02 seconds
```