## 題目

https://learn.cylabacademy.org/library/320?page=3&category=5

Unzip this archive and find the file named 'uber-secret.txt'

- [Download zip file](https://artifacts.picoctf.net/c/502/files.zip)


## 解題

先解壓縮附檔：

```bash
unzip files.zip
```

得到：

```bash
files
├── 13771.txt.utf-8
├── 14789.txt.utf-8
├── acceptable_books
│   ├── 17879.txt.utf-8
│   ├── 17880.txt.utf-8
│   └── more_books
│       └── 40723.txt.utf-8
├── adequate_books
│   ├── 44578.txt.utf-8
│   ├── 46804-0.txt
│   └── more_books
│       └── 1023.txt.utf-8
└── satisfactory_books
    ├── 16021.txt.utf-8
    ├── 23765.txt.utf-8
    └── more_books
        └── 37121.txt.utf-8
```


使用 `grep` 尋找該資料夾下的檔案內容是否包含 flag :

```bash
grep -rn "picoCTF" files/**/*
```

輸出：

```bash
files/adequate_books/more_books/.secret/deeper_secrets/deepest_secrets/uber-secret.txt:1:picoCTF{f1nd_15_f457_ab443fd1}
files/adequate_books/more_books/.secret/deeper_secrets/deepest_secrets/uber-secret.txt:1:picoCTF{f1nd_15_f457_ab443fd1}
```


得到 flag :  

```bash
picoCTF{f1nd_15_f457_ab443fd1}
```