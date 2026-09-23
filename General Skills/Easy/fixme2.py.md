## 題目

https://learn.cylabacademy.org/library/241?page=3&category=5

Fix the syntax error in the Python script to print the flag.

[Download Python script](https://artifacts.picoctf.net/c/4/fixme2.py)


## 解題

將附檔 `fixme2.py` 第 22 行：

```python
if flag = "":
```

改成：

```python
if flag == "":
```

然後執行 `fixme2.py` ，即可得到 flag :

```bash
That is correct! Here's your flag: picoCTF{3qu4l1ty_n0t_4551gnm3nt_e8814d03}
```