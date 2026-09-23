## 題目

https://learn.cylabacademy.org/library/246?page=3&category=5

Can you crack the password to get the flag?

Download the password checker [here](https://artifacts.picoctf.net/c/13/level2.py) and you'll need the encrypted [flag](https://artifacts.picoctf.net/c/13/level2.flag.txt.enc) in the same directory too.



## 解題

附檔 `level2.py` 中的第 18 行：

```python
if( user_pw == chr(0x64) + chr(0x65) + chr(0x37) + chr(0x36) ):
```

可以看到 password 為 `chr(0x64) + chr(0x65) + chr(0x37) + chr(0x36)` ，故直接修改第 17 行 `user_pw` 的值：

```python
user_pw = input("Please enter correct password for flag: ")
```

改成：

```python
user_pw = chr(0x64) + chr(0x65) + chr(0x37) + chr(0x36)
```

然後執行 `level2.py` ，即可得到 flag :

```bash
Welcome back... your flag, user:
picoCTF{tr45h_51ng1ng_489dea9a}
```