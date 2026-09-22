## 題目

https://learn.cylabacademy.org/library/471?page=1&category=5

Play this short game to get familiar with terminal applications and some of the most important rules in scope for picoCTF.


## 解題

Launch Instance 後，使用 `nc` 連線：

```bash
nc verbal-sleep.picoctf.net 60489
```

一直按 `Enter` 後：

```bash
Options:
A) *Register multiple accounts*
B) *Share an account with a friend*
C) *Register a single, private account*
[a/b/c] >
```

輸入 `b`，然後一直按 `Enter` ：

```bash
Options:
A) *Play the game*
B) *Search the Ether for the flag*
[a/b] >
```

輸入 `a` ，然後一直按 `Enter` ，就可以看到 flag :  

```bash
"Thanks, Nyx! Here's the flag I found: picoCTF{m1113n1um_3d1710n_1e2b417a}"
```

