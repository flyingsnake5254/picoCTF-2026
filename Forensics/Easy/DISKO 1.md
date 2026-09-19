## 題目

https://learn.cylabacademy.org/library/505?page=1&category=4

Can you find the flag in this disk image?

Download the disk image [here](https://artifacts.picoctf.net/c/536/disko-1.dd.gz).


## 解題

附檔為 `disko-1.dd.gz` ，解壓縮後，得到 `disko-1.dd` 

接下來使用 `strings` 取得 flag :  

```bash
strings disko-1.dd | grep "picoCTF"
```

輸出：

```bash
picoCTF{1t5_ju5t_4_5tr1n9_c63b02ef}
```