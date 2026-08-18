https://learn.cylabacademy.org/library/716?page=1&category=2

## 題目

I learned about lfsr today in school so i decided to implement it in my program. It must be safe right? [chall.py](https://challenge-files.picoctf.net/c_plain_mesa/0cd8d68d4aacefd8d1924ea6452a8727990af562d813838e2bc5b4e7a57f79f8/chall.py) [output.txt](https://challenge-files.picoctf.net/c_plain_mesa/0cd8d68d4aacefd8d1924ea6452a8727990af562d813838e2bc5b4e7a57f79f8/output.txt)

## 附檔

**chall.py**

```python nums
from Crypto.Util.number import bytes_to_long, long_to_bytes
from Crypto.Random import get_random_bytes

key = bytes_to_long(get_random_bytes(126))

def steplfsr(lfsr):
    b7 = (lfsr >> 7) & 1
    b5 = (lfsr >> 5) & 1
    b4 = (lfsr >> 4) & 1
    b3 = (lfsr >> 3) & 1

    feedback = b7 ^ b5 ^ b4 ^ b3
    lfsr = (feedback << 7) | (lfsr >> 1)
    return lfsr

def encrypt_lfsr(pt_bytes):
    output = bytearray()
    lfsr = key & 0xFF
    for p in pt_bytes:
        lfsr = steplfsr(lfsr)
        ks = lfsr
        output.append(p ^ ks)
    return bytes_to_long(bytes(output))

pt = b"[redacted]"
ct = encrypt_lfsr(pt)

print(long_to_bytes(ct).hex())

```

**output.txt**

```text
21c1b705764e4bfdafd01e0bfdbc38d5eadf92991cdd347064e37444e517d661cea9
```

## 解題

>[LFSR](../../筆記/LFSR.md)

從 **chall.py** 的 $\boxed{Line 18}$ :
```python
lfsr = key & 0xFF
```

得知其 key 只取後 8 bits，也就是 0 ~ 255，故只要嘗試 key = 0 ~ 255，就可以解密。

LFSR 加密時：
	密文 = 明文 XOR Key
解密：
	明文 = 密文 XOR Key

撰寫以下 python 進行解密：

```python
def steplfsr(lfsr):
    b7 = (lfsr >> 7) & 1
    b5 = (lfsr >> 5) & 1
    b4 = (lfsr >> 4) & 1
    b3 = (lfsr >> 3) & 1

    feedback = b7 ^ b5 ^ b4 ^ b3
    lfsr = (feedback << 7) | (lfsr >> 1)
    return lfsr

# 內容與原本 encrypt_lfsr 一樣，只差在 key 那行與 return 的值直接回傳 bytes
def decrypt_lfsr(c_bytes, key):
    output = bytearray()
    lfsr = key
    for p in c_bytes:
        lfsr = steplfsr(lfsr)
        ks = lfsr
        output.append(p ^ ks)

    # 這裡改成直接回傳 bytes
    return bytes(output)

# 設置 output.txt 的密文
c_bytes = bytes.fromhex("21c1b705764e4bfdafd01e0bfdbc38d5eadf92991cdd347064e37444e517d661cea9")

# 嘗試 key = 0 ~ 255
for k in range(256):
    m_bytes = decrypt_lfsr(c_bytes, k)
    if b"picoCTF" in m_bytes:
        print("key =", k)
        print(m_bytes)
```

輸出：  
key = 162  
b'picoCTF{l1n3ar_f33dback_sh1ft_r3g}'  
key = 163  
b'picoCTF{l1n3ar_f33dback_sh1ft_r3g}'  

