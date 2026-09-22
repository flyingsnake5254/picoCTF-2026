## 題目

https://learn.cylabacademy.org/library/410?page=2&category=5

My team has been working very hard on new features for our flag printing program! I wonder how they'll work together?

You can download the challenge files here:

- [challenge.zip](https://artifacts.picoctf.net/c_titan/177/challenge.zip)


## 解題

>[Git 基本指令](../../筆記/Git%20基本指令.md)

先解壓縮附檔：

```bash
unzip challenge.zip
```

得到：

```css
drop-in
└── flag.py
```

使用 `git branch` 查看分支：

```bash
  feature/part-1
  feature/part-2
  feature/part-3
* main
```

發現有另外三個分支，直接 `switch` 到這三個分支，並執行 `flag.py` 即可得到 flag :  

```bash
git switch feature/part-1
python flag.py
```

輸出：

```bash
Printing the flag...
picoCTF{t3@mw0rk_
```



```bash
git switch feature/part-2
python flag.py
```

輸出：

```bash
Printing the flag...
m@k3s_th3_dr3@m_
```



```bash
git switch feature/part-1
python flag.py
```

輸出：

```bash
Printing the flag...
w0rk_7ae8dd33}
```


組合出 flag :  

```bash
picoCTF{t3@mw0rk_m@k3s_th3_dr3@m_w0rk_7ae8dd33}
```