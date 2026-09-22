## 題目

https://learn.cylabacademy.org/library/405?page=2&category=5

Someone's commits seems to be preventing the program from working. Who is it?

You can download the challenge files here:

- [challenge.zip](https://artifacts.picoctf.net/c_titan/72/challenge.zip)



## 解題

>[Git 基本指令](../../筆記/Git%20基本指令.md)

先解壓縮檔案：

```bash
unzip challenge.zip
```

得到：

```css
drop-in
└── message.py
```

將 `git log` 結合 `grep` 過濾，即可得到 flag :  

```bash
git log | grep "picoCTF{"
```

輸出：

```bash
Author: picoCTF{@sk_th3_1nt3rn_b64c4705} <ops@picoctf.com>
```