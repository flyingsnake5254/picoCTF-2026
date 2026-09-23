## 題目

https://learn.cylabacademy.org/library/245?page=3&category=5

Can you crack the password to get the flag?

Download the password checker [here](https://artifacts.picoctf.net/c/12/level1.py) and you'll need the encrypted [flag](https://artifacts.picoctf.net/c/12/level1.flag.txt.enc) in the same directory too.



## 解題

從附檔 `level1.py` 的第 19 行，即可知道 `user_pw` 的值為 `8713` 時，可以解密：

```python
if( user_pw == "8713"):
```

故執行 `level1.py` ，並輸入 `8713` ，即可得到 flag :

```bash
Please enter correct password for flag: 8713
Welcome back... your flag, user:
picoCTF{545h_r1ng1ng_1b2fd683}
```

