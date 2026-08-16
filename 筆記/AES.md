## 使用 Python

### install package

```bash
pip install pycryptodome
```


### code

```python
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad

# AES key 必須是：
# 16 bytes = AES-128
# 24 bytes = AES-192
# 32 bytes = AES-256
key = b"1234567890abcdef"

plaintext = "Hello AES! 你好"

# =====================
# 加密
# =====================

cipher = AES.new(key, AES.MODE_ECB)

ciphertext = cipher.encrypt(
    pad(plaintext.encode("utf-8"), AES.block_size)
)

print("Ciphertext bytes:", ciphertext)
print("Ciphertext hex:", ciphertext.hex())


# =====================
# 解密
# =====================

cipher = AES.new(key, AES.MODE_ECB)

decrypted = unpad(
    cipher.decrypt(ciphertext),
    AES.block_size
)

print("Decrypted bytes:", decrypted)
print("Decrypted text:", decrypted.decode("utf-8"))
```

