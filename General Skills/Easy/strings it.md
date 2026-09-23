## 題目

https://learn.cylabacademy.org/library/37?page=4&category=5

Can you find the flag in [file](https://challenge-files.picoctf.net/c_fickle_tempest/094a1db42d5ae681cd9e513dcbea2d997495dd3763d50c643b939923ca86e29b/strings) without running it?


## 解題

使用 `strings`、`grep` 過濾：

```bash
strings strings | grep "picoCTF"
```

輸出：

```bash
picoCTF{5tRIng5_1T_47948C73}
```