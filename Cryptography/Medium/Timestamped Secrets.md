https://learn.cylabacademy.org/library/722?page=1&category=2

## 題目

Someone encrypted a message using AES in ECB mode but they weren’t very careful with their key. Turns out it’s derived from something as simple as the current time! Can you uncover the key and decrypt the flag?

Download the encrypted message: [message](https://challenge-files.picoctf.net/c_plain_mesa/95b5340b7c7992206c61b8092b01ab36a563e2df91c5fb36aee43b647d720dbe/message.txt)  
You may also find the encryption script helpful: [code](https://challenge-files.picoctf.net/c_plain_mesa/95b5340b7c7992206c61b8092b01ab36a563e2df91c5fb36aee43b647d720dbe/encryption.py)

## 附檔

### message.txt

```text
Hint: The encryption was done around 1770242606 UTC
Ciphertext (hex): 24162f53d9b29255e635230b821cb8baca14461d54b2955401a049477e201fe9
```

### encryption.py

```python
from hashlib import sha256
import time
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad

def encrypt(plaintext: str, timestamp: int) -> str:
    timestamp = int(time.time())
    key = sha256(str(timestamp).encode()).digest()[:16]
    cipher = AES.new(key, AES.MODE_ECB)
    padded = pad(plaintext.encode(), AES.block_size)
    ciphertext = cipher.encrypt(padded)
    return ciphertext.hex()

if __name__ == "__main__":
  
    plaintext = "picoCTF{...}"
    result = encrypt(plaintext, key)
    print(f"Hint: The encryption was done around {timestamp} UTC\n")
    print(f"Ciphertext (hex): {ciphertext.hex()}\n")

```



## 解題

使用 python 撰寫 decryption 程式，其實只要拿 `encryption.py` 的 encrypt function 進行修改即可：

```python
from hashlib import sha256
from Crypto.Cipher import AES
from Crypto.Util.Padding import unpad


def decrypt(ciphertext: str, timestamp: int):
    ciphertext = bytes.fromhex(ciphertext)
    key = sha256(str(timestamp).encode()).digest()[:16]
    cipher = AES.new(key, AES.MODE_ECB)
    decrypted = unpad(
        cipher.decrypt(ciphertext),
        AES.block_size
    )
    return decrypted


if __name__ == "__main__":
    result = decrypt(
        "24162f53d9b29255e635230b821cb8baca14461d54b2955401a049477e201fe9",
        1770242606
    )

    print(result.decode("utf-8"))


```

執行後輸出 flag :  
picoCTF{sa3S_sEc9t_081e3371}
