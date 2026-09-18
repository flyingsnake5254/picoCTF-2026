## 題目

https://learn.cylabacademy.org/library/412?page=2&category=2

Can you get sense of this code file and write the function that will decode the given encrypted file content.

Find the encrypted file here [flag_info](https://artifacts.picoctf.net/c_titan/93/enc_flag) and [code file](https://artifacts.picoctf.net/c_titan/93/custom_encryption.py) might be good to analyze and get the flag.


## 附檔

**enc_flag**

```text
a = 88
b = 26
cipher is: [97965, 185045, 740180, 946995, 1012305, 21770, 827260, 751065, 718410, 457170, 0, 903455, 228585, 54425, 740180, 0, 239470, 936110, 10885, 674870, 261240, 293895, 65310, 65310, 185045, 65310, 283010, 555135, 348320, 533365, 283010, 76195, 130620, 185045]
```

**custom_encryption.py**

```python
from random import randint
import sys


def generator(g, x, p):
    return pow(g, x) % p


def encrypt(plaintext, key):
    cipher = []
    for char in plaintext:
        cipher.append(((ord(char) * key*311)))
    return cipher


def is_prime(p):
    v = 0
    for i in range(2, p + 1):
        if p % i == 0:
            v = v + 1
    if v > 1:
        return False
    else:
        return True


def dynamic_xor_encrypt(plaintext, text_key):
    cipher_text = ""
    key_length = len(text_key)
    for i, char in enumerate(plaintext[::-1]):
        key_char = text_key[i % key_length]
        encrypted_char = chr(ord(char) ^ ord(key_char))
        cipher_text += encrypted_char
    return cipher_text


def test(plain_text, text_key):
    p = 97
    g = 31
    if not is_prime(p) and not is_prime(g):
        print("Enter prime numbers")
        return
    a = randint(p-10, p)
    b = randint(g-10, g)
    print(f"a = {a}")
    print(f"b = {b}")
    u = generator(g, a, p)
    v = generator(g, b, p)
    key = generator(v, a, p)
    b_key = generator(u, b, p)
    shared_key = None
    if key == b_key:
        shared_key = key
    else:
        print("Invalid key")
        return
    semi_cipher = dynamic_xor_encrypt(plain_text, text_key)
    cipher = encrypt(semi_cipher, shared_key)
    print(f'cipher is: {cipher}')


if __name__ == "__main__":
    message = sys.argv[1]
    test(message, "trudeau")

```

## 解題

本題只須根據 `custom_encryption.py` 裡的加密邏輯，反向撰寫一個解密邏輯即可。可以透過 Debug 模式觀察加密邏輯

```python
from random import randint
import sys


def generator(g, x, p):
    return pow(g, x) % p

def decrypt(cipher, key):
    temp = ''
    for c in cipher:
        temp += chr(c // key // 311)
    return temp

def is_prime(p):
    v = 0
    for i in range(2, p + 1):
        if p % i == 0:
            v = v + 1
    if v > 1:
        return False
    else:
        return True

def dynamic_xor_decrypt(cipher_text, text_key):
    key_length = len(text_key)
    cipher_text = cipher_text[::-1]
    plain_text = ""
    for i, char in enumerate(cipher_text[::-1]):
        key_char = text_key[i % key_length]

        decrypt_char = chr(ord(char) ^ ord(key_char))
        plain_text += decrypt_char
    plain_text = plain_text[::-1]
    return plain_text

def test2(cipher, text_key):
    p = 97
    g = 31
    a = 88
    b = 26
    u = generator(g, a, p)
    v = generator(g, b, p)
    key = generator(v, a, p)
    b_key = generator(u, b, p)
    shared_key = None
    if key == b_key:
        shared_key = key
    else:
        print("Invalid key")
        return
    
    semi_cipher = decrypt(cipher, key)
    plain_text = dynamic_xor_decrypt(semi_cipher, text_key)
    print(plain_text)

if __name__ == "__main__":
    cipher = [97965, 185045, 740180, 946995, 1012305, 21770, 827260, 751065, 718410, 457170, 0, 903455, 228585, 54425, 740180, 0, 239470, 936110, 10885, 674870, 261240, 293895, 65310, 65310, 185045, 65310, 283010, 555135, 348320, 533365, 283010, 76195, 130620, 185045]
    test2(cipher, "trudeau")

```

最後輸出 flag :  
picoCTF{custom_d2cr0pt6d_019c831c}