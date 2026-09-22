## 題目

https://learn.cylabacademy.org/library/425?page=2&category=5

What was I last working on? I remember writing a note to help me remember...

You can download the challenge files here:

- [challenge.zip](https://artifacts.picoctf.net/c_titan/66/challenge.zip)


## 解題

>[Git 基本指令](../../筆記/Git%20基本指令.md)

先解壓縮附檔：

```bash
unzip challenge.zip
```

得到：

```css
drop-in
└── message.txt
```

利用 `cat` 輸出 `message.txt` 內容：

```bash
cat message.txt
```

```bash
This is what I was working on, but I'd need to look at my commit history to know why...
```

查看歷史提交紀錄，即可得到 flag：

```bash
git log
```

```bash
commit 3339c144a0c78dc2fbd3403d2fb37d3830be5d94 (HEAD -> master)
Author: picoCTF <ops@picoctf.com>
Date:   Sat Mar 9 21:10:22 2024 +0000

    picoCTF{t1m3m@ch1n3_d3161c0f}
```