## 題目

https://learn.cylabacademy.org/library/85?page=4&category=5

Can you find the flag in the file? This would be really tedious to look through manually, something tells me there is a better way.

The flag is in this [file](https://challenge-files.picoctf.net/c_fickle_tempest/2e9bfa4e1d90ac25a999fefdfb4feb8a2ff4eb73e4c61af4889a3762687ada01/file).



## 解題

>[grep](../../筆記/grep.md)

使用 `grep` 過濾 `file` ：

```bash
cat file | grep -oE 'picoCTF\{[^}]*\}'
```

輸出：

```bash
picoCTF{grep_is_good_to_find_things_29f42460}
```