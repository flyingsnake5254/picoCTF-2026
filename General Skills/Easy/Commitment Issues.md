## 題目

https://learn.cylabacademy.org/library/411?page=2&category=5

I accidentally wrote the flag down. Good thing I deleted it!

You download the challenge files here:

- [challenge.zip](https://artifacts.picoctf.net/c_titan/137/challenge.zip)


## 解題

>[Git 基本指令](../../筆記/Git%20基本指令.md)

先解壓縮 `challenge.zip` ：

```bash
unzip challenge.zip
```

得到：

```css
drop-in
└── message.txt
```

輸出 `message.txt` 內如：

```bash
cat message.txt
TOP SECRET
```

查看 git commit 紀錄：

```bash
git log
```

輸出：

```bash
commit ef0b7cc6b98367fa168573c931e0f7098ef59182 (HEAD -> master)
Author: picoCTF <ops@picoctf.com>
Date:   Tue Mar 12 00:06:20 2024 +0000

    remove sensitive info

commit ea859bf3b5d94ee74ce5ee1afa3edd7d4d6b35f0
Author: picoCTF <ops@picoctf.com>
Date:   Tue Mar 12 00:06:20 2024 +0000

    create flag
```

看到前面有個 create flag 的 commit，使用 `git show` 查看該提交紀錄：

```bash
git show ea859bf3b5d94ee74ce5ee1afa3edd7d4d6b35f0
```

結果：

```bash
commit ea859bf3b5d94ee74ce5ee1afa3edd7d4d6b35f0
Author: picoCTF <ops@picoctf.com>
Date:   Tue Mar 12 00:06:20 2024 +0000

    create flag

diff --git a/message.txt b/message.txt
new file mode 100644
index 0000000..fca28bb
--- /dev/null
+++ b/message.txt
@@ -0,0 +1 @@
+picoCTF{s@n1t1z3_cf09a485}
```

得到 flag :  

```bash
picoCTF{s@n1t1z3_cf09a485}
```