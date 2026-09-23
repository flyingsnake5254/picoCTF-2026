## 題目

https://learn.cylabacademy.org/library/156?page=4&category=5

There is a nice program that you can talk to by using this command in a shell:


## 解題

>[Python 讀寫檔案](../../筆記/Python%20讀寫檔案.md)

Launch Instance 後，使用 `nc` 連線：

```bash
nc wily-courier.picoctf.net 49561
```

```bash
112 
105 
99 
111 
67 
84 
70 
123 
103 
48 
48 
100 
95 
107 
49 
116 
116 
121 
33 
95 
110 
49 
99 
51 
95 
107 
49 
116 
116 
121 
33 
95 
100 
53 
100 
56 
56 
125 
10
```

先將上述存成 `input.txt` ：

```bash
nc wily-courier.picoctf.net 49561 > input.txt
```

寫一隻程式輸出上述數字之 ASCII：

```python
with open("input.txt", "r", encoding="utf-8") as f:
    for line in f:
        print(chr(eval(line.strip())), end='')
```

輸出：

```bash
picoCTF{g00d_k1tty!_n1c3_k1tty!_d5d88}
```