## 題目

https://learn.cylabacademy.org/library/322?page=3&category=5

Unzip this archive and find the flag.

- [Download zip file](https://artifacts.picoctf.net/c/504/big-zip-files.zip)


## 解題

先將附檔解壓縮：

```bash
unzip big-zip-files.zip
```

接下來使用 `grep` 搜尋 `big-zip-files` 內的所有子資料夾、檔案內容：

```bash
grep -rn "picoCTF" big-zip-files/**/*
```

輸出：

```bash
big-zip-files/folder_pmbymkjcya/folder_cawigcwvgv/folder_ltdayfmktr/folder_fnpfclfyee/whzxrpivpqld.txt:1:information on the record will last a billion years. Genes and brains and books encode picoCTF{gr3p_15_m4g1c_ef8790dc}
big-zip-files/folder_pmbymkjcya/folder_cawigcwvgv/folder_ltdayfmktr/folder_fnpfclfyee/whzxrpivpqld.txt:1:information on the record will last a billion years. Genes and brains and books encode picoCTF{gr3p_15_m4g1c_ef8790dc}
big-zip-files/folder_pmbymkjcya/folder_cawigcwvgv/folder_ltdayfmktr/folder_fnpfclfyee/whzxrpivpqld.txt:1:information on the record will last a billion years. Genes and brains and books encode picoCTF{gr3p_15_m4g1c_ef8790dc}
big-zip-files/folder_pmbymkjcya/folder_cawigcwvgv/folder_ltdayfmktr/folder_fnpfclfyee/whzxrpivpqld.txt:1:information on the record will last a billion years. Genes and brains and books encode picoCTF{gr3p_15_m4g1c_ef8790dc}
big-zip-files/folder_pmbymkjcya/folder_cawigcwvgv/folder_ltdayfmktr/folder_fnpfclfyee/whzxrpivpqld.txt:1:information on the record will last a billion years. Genes and brains and books encode picoCTF{gr3p_15_m4g1c_ef8790dc}
```

得到 flag :  

```bash
picoCTF{gr3p_15_m4g1c_ef8790dc}
```