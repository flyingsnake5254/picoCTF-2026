## 題目

https://learn.cylabacademy.org/library/44?page=2&category=4

This file contains more than it seems.

Get the flag from [garden.jpg](https://challenge-files.picoctf.net/c_fickle_tempest/6013221da747114c37db29c554381dbe4bb4e746cf6bd880f9c3b5d0b495a823/garden.jpg).


## 解題

>[探查面向](../../筆記/圖片探查面向.md)

使用 `strings` 看是否有 flag :  

```bash
strings garden.jpg | grep "picoCTF"
```

輸出：

```bash
Here is a flag: picoCTF{more_than_m33ts_the_3y339140129}
```