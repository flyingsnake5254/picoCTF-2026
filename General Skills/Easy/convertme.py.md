## 題目

https://learn.cylabacademy.org/library/239?page=3&category=5

Run the Python script and convert the given number from decimal to binary to get the flag.

[Download Python script](https://artifacts.picoctf.net/c/24/convertme.py)


## 解題

執行 `convertme.py` 後，回答問題，即可拿到 flag :  

```bash
If 64 is in decimal base, what is it in binary base?
Answer: 1000000
That is correct! Here's your flag: picoCTF{4ll_y0ur_b4535_722f6b39}
```

或是直接在 `convertme.py` 第 16 行後面，加上：

```python
flag = str_xor(flag_enc, 'enkidu')
print(flag)
```

即可輸出 flag