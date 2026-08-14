https://learn.cylabacademy.org/library/144?page=1&category=2

## 題目

Cryptography can be easy, do you know what ROT13 is?

[values.txt](https://challenge-files.picoctf.net/c_wily_courier/390ad965b8e98943a33a68fdd4433081e6f67c5368a5205740fb1143045f9f1a/values.txt)


## 附檔

**values.txt**

```
cvpbPGS{arkg_gvzr_V'yy_gel_2_ebhaqf_bs_ebg13_45559noq}
```

## 解題

看附檔內容，推測為凱薩加密。假設前面 7 個字元對應 `picoCTF`，觀察其位移值：

```bash
>>> (ord('c') - ord('p')) % 26
13
>>> (ord('v') - ord('i')) % 26
13
>>> (ord('p') - ord('c')) % 26
13
>>> (ord('b') - ord('o')) % 26
13
```

確認為位移 13 的凱薩加密。寫以下 python 程式進行解密
```python
s = "cvpbPGS{arkg_gvzr_V'yy_gel_2_ebhaqf_bs_ebg13_45559noq}"

flag = ''
for i in s:
    # 是大寫字母
    if ord(i) >= ord('A') and ord(i) <= ord('Z'):
        flag += chr((ord(i) - ord('A') - 13) % 26 + ord('A'))
    # 是小寫字母
    elif ord(i) >= ord('a') and ord(i) <= ord('z'):
        flag += chr((ord(i) - ord('a') - 13) % 26 + ord('a'))
    # 是數字或特殊符號
    else:
        flag += i
print(flag)

```


輸出結果即為 flag :  
picoCTF{next_time_I'll_try_2_rounds_of_rot13_45559abd}