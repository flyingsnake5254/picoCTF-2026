https://learn.cylabacademy.org/library/418?page=1&category=2


## 題目

Can you get the real meaning from this file.

Download the file [here](https://artifacts.picoctf.net/c_titan/109/enc_flag).

## 附檔

**enc_flag**
```bash
YidkM0JxZGtwQlRYdHFhR3g2YUhsZmF6TnFlVGwzWVROclgyMHdNakV5TnpVNGZRPT0nCg==
```

## 解題

根據附檔內容判斷，此為 base64 編碼（後面有兩個等號），故使用 [[OpenSSL]] 解碼：

```bash
openssl base64 -d -in enc_flag -out decode_flag
```

輸出 decode_flag 檔案內容：

```bash
❯ cat decode_flag
b'd3BqdkpBTXtqaGx6aHlfazNqeTl3YTNrX20wMjEyNzU4fQ=='
```

尾端仍然是兩個等號，推測為 base64 encode，先將 decode_flag 文字內容存成另一檔案 enc_flag2：

```bash
echo 'd3BqdkpBTXtqaGx6aHlfazNqeTl3YTNrX20wMjEyNzU4fQ==' > enc_flag2
```

再次使用 openssl 進行 base64 decode :

```bash
openssl base64 -d -in enc_flag2 -out decode_flag2
```

輸出 decode_flag2 的內容：

```bash
wpjvJAM{jhlzhy_k3jy9wa3k_m0212758}
```

推測前面 7 個字母對應 picoCTF，對其字元進行檢查：

```python
>>> ord('w') - ord('p')
7
>>> ord('p') - ord('i')
7
>>> ord('j') - ord('c')
7
>>> ord('v') - ord('o')
7
```

發現將明文使用凱薩加密，故寫以下 python 對密文進行解密（特殊符號、數字、底線保留原本的字元，只有大小寫英文會進行凱薩解密
）：

```python
s = 'wpjvJAM{jhlzhy_k3jy9wa3k_m0212758}'
flag = ''
for i in s:
	# 此字元是小寫字母
    if ord(i) >= ord('a') and ord(i) <= ord('z'):
        flag += chr((ord(i) - ord('a') - 7) % 26 + ord('a'))
	# 此字元是大寫字母    
    elif ord(i) >= ord('A') and ord(i) <= ord('Z'):
        flag += chr((ord(i) - ord('A') - 7) % 26 + ord('A'))
    else:
        flag += i
print(flag)
```

最後執行檔案，得到 flag :  
picoCTF{caesar_d3cr9pt3d_f0212758}
