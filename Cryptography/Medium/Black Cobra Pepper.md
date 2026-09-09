https://learn.cylabacademy.org/library/701?page=2&category=2

- [題目](#題目)
- [附檔](#附檔)
- [解題](#解題)


<hr>

## 題目

i like peppers. (change!) [chall.py](https://challenge-files.picoctf.net/c_plain_mesa/bc10b96aea8f8766a0e83494d770a2a542eb1d83b4f295568fa5911a0267c02c/chall.py) [output.txt](https://challenge-files.picoctf.net/c_plain_mesa/bc10b96aea8f8766a0e83494d770a2a542eb1d83b4f295568fa5911a0267c02c/output.txt)


## 附檔

**chall.py**

```python
from pwn import xor

pt = ""
key = ""

def split(full_key):
    k = full_key
    k1 = ""
    k2 = ""
    k3 = ""
    k4 = ""
    sub_keys = [k1, k2, k3, k4]
    for i in range(len(k)):
        sub_keys[i%4] = str(sub_keys[i%4]) + str(k[0])
        k = k[1:]
    return sub_keys

def glue(parts):
    k = ""
    for i in range(32):
        k = str(k) + str(parts[i%4][0])
        parts[i%4] = str(parts[i%4][1:])
    return k

def rot_word(word):
    return str(word[2:]) + str(word[0:2])

def sub_word(word):
    return word

def rcon(word):
    return word

def gen_keys(master_key):
    keys = []
    rounds = 0
    k = master_key

    while (rounds < 11):
        keys.append(k)
        sub_keys = split(k)
        sub_keys[-1] = rot_word(sub_keys[-1])
        sub_keys[-1] = sub_word(sub_keys[-1])
        sub_keys[-1] = rcon(sub_keys[-1])
        sub_keys[0] = xor(bytes.fromhex(sub_keys[0]), bytes.fromhex(sub_keys[-1])).hex()
        sub_keys[1] = xor(bytes.fromhex(sub_keys[1]), bytes.fromhex(sub_keys[0])).hex()
        sub_keys[2] = xor(bytes.fromhex(sub_keys[2]), bytes.fromhex(sub_keys[1])).hex()
        sub_keys[3] = xor(bytes.fromhex(sub_keys[3]), bytes.fromhex(sub_keys[2])).hex()
        k = glue(sub_keys)
        rounds += 1
    
    return keys

def to_matrix(key):
    bytes_list = [int(key[i:i+2], 16) for i in range(0, 32, 2)]

    array = [[0] * 4 for _ in range(4)]
    for i in range(16):
        row = i % 4
        col = i // 4
        array[row][col] = hex(bytes_list[i])[2:]
    
    return array

def from_matrix(matrix):
    reconstructed = ""
    for col in range(4):
        for row in range(4):
            reconstructed += matrix[row][col].zfill(2)
    return reconstructed

def sub_bytes(state):
    return state

def shift_rows(state):
    placeholder = state[1][0]
    state[1][0], state[1][1], state[1][2], state[1][3] = state[1][1], state[1][2], state[1][3], state[1][0]
    state[2][0], state[2][1], state[2][2], state[2][3] = state[2][2], state[2][3], state[2][0], state[2][1]
    state[3][0], state[3][1], state[3][2], state[3][3] = state[3][3], state[3][0], state[3][1], state[3][2]
    return state

#adopted and insipred by the code from the wikipedia article Rijndael MixColumns. 
def gmul(a, b):
    b = int(b, 16)
    p = 0
    for c in range(8):
        if b & 1:
            p ^= a
        a <<= 1
        if a & 0x100:
            a ^= 0x11b
        b>>=1
    return p

def mix_columns(s):
    ss = [[0] * 4 for _ in range(4)]

    for c in range(4):
        ss[0][c] = hex(gmul(0x02, s[0][c]) ^ gmul(0x03, s[1][c]) ^ int(s[2][c], 16) ^ int(s[3][c], 16))[2:].zfill(2)
        ss[1][c] = hex(int(s[0][c], 16) ^ gmul(0x02, s[1][c]) ^ gmul(0x03, s[2][c]) ^ int(s[3][c], 16))[2:].zfill(2)
        ss[2][c] = hex(int(s[0][c], 16) ^ int(s[1][c], 16) ^ gmul(0x02, s[2][c]) ^ gmul(0x03, s[3][c]))[2:].zfill(2)
        ss[3][c] = hex(gmul(0x03, s[0][c]) ^ int(s[1][c], 16) ^ int(s[2][c], 16) ^ gmul(0x02, s[3][c]))[2:].zfill(2)
    
    for i in range(4):
        for j in range(4):
            s[i][j] = ss[i][j]
    return s

def AES(plaintext, key):
    ciphertext = plaintext
    round_keys = gen_keys(key)
    ciphertext = xor(bytes.fromhex(round_keys[0]), bytes.fromhex(ciphertext)).hex()
    for i in range(1,10):
        ciphertext = to_matrix(ciphertext)
        sub_bytes(ciphertext)
        shift_rows(ciphertext)
        mix_columns(ciphertext)
        ciphertext = from_matrix(ciphertext)
        ciphertext = xor(bytes.fromhex(round_keys[i]), bytes.fromhex(ciphertext)).hex()
    ciphertext = to_matrix(ciphertext)
    sub_bytes(ciphertext)
    shift_rows(ciphertext)
    ciphertext = from_matrix(ciphertext)
    ciphertext = xor(bytes.fromhex(round_keys[10]), bytes.fromhex(ciphertext)).hex()
    return ciphertext

flag = [redacted]
key = [redacted]
pt1 = "72616e646f6d64617461313131313131"

print((AES(pt1, key)))
print(AES(flag, key))
```

**output.txt**

```text
d7481d89f1aaf5a857f56edd2ae8994c
8c7d66558130eb5796d131beb43c9934
```


## 解題

>[AES](../../筆記/AES.md)

本題可看到 `sub_bytes` 、`rot_word`、`sub_word` 在 `chall.py` 中的 AES，是完全沒任何作用的，整個加密系統中，只剩 `xor` 、`shiftRows`、`MixColumns`，變成「**純線性**」運算。

在密碼學中，若加密系統沒有非線性運算（S-box），則輸出的密文 $C$ 與明文 $P$ 及金鑰 $K$ 之間滿足以下線性關係：
```math
AES(P, K) = AES(P, 0)\ \oplus \ AES(0, K)
```

本題 `chall.py` 內，已有明文 `pt1` ，以及 `output.txt` 給了明文 `AES(pt1, key)` 、`AES(flag, key)` 的結果，故可列出以下：

$$
\begin{aligned}
AES(pt1, key) = AES(pt1, 0) \ \oplus \ AES(0, key)\\
⟹\ AES(0, key) = AES(pt1, key)\ \oplus \ AES(pt1, 0)\\\\
AES(flag, key)=AES(flag, 0)\ \oplus \ AES(0, key)\\
⟹\ AES(flag, 0)=AES(flag, key)\ \oplus \ AES(0, key)

\end{aligned}
$$

得出 `AES(flag, 0)` 後，可以利用 AES 解密，解出 flag，因為 「**key 全為 0，故所有 round key 為 0**」

寫出以下程式解密，主要在原本 `chall.py` 新增 `inv_shift_rows`、`inv_mix_columns` ，以及解密 AES 的 `DecryptAES`：

```python
def inv_shift_rows(state):
    state[1][0], state[1][1], state[1][2], state[1][3] = state[1][3], state[1][0], state[1][1], state[1][2]
    state[2][0], state[2][1], state[2][2], state[2][3] = state[2][2], state[2][3], state[2][0], state[2][1]
    state[3][0], state[3][1], state[3][2], state[3][3] = state[3][1], state[3][2], state[3][3], state[3][0]
    return state
    
def inv_mix_columns(s):
    ss = [[0] * 4 for _ in range(4)]

    for c in range(4):
        ss[0][c] = hex(gmul(0x0e, s[0][c]) ^ gmul(0x0b, s[1][c]) ^ gmul(0x0d, s[2][c]) ^ gmul(0x09, s[3][c]))[2:].zfill(2)
        ss[1][c] = hex(gmul(0x09, s[0][c]) ^ gmul(0x0e, s[1][c]) ^ gmul(0x0b, s[2][c]) ^ gmul(0x0d, s[3][c]))[2:].zfill(2)
        ss[2][c] = hex(gmul(0x0d, s[0][c]) ^ gmul(0x09, s[1][c]) ^ gmul(0x0e, s[2][c]) ^ gmul(0x0b, s[3][c]))[2:].zfill(2)
        ss[3][c] = hex(gmul(0x0b, s[0][c]) ^ gmul(0x0d, s[1][c]) ^ gmul(0x09, s[2][c]) ^ gmul(0x0e, s[3][c]))[2:].zfill(2)
    
    for i in range(4):
        for j in range(4):
            s[i][j] = ss[i][j]
    return s

def DecryptAES(ciphertext, zero):
    plaintext = ciphertext
    plaintext = xor(bytes.fromhex(zero), bytes.fromhex(plaintext)).hex()
    plaintext = to_matrix(plaintext)
    for round in range(9, 0, -1):
        inv_shift_rows(plaintext)
        plaintext = from_matrix(plaintext)
        plaintext = xor(bytes.fromhex(plaintext), bytes.fromhex(zero)).hex()
        plaintext = to_matrix(plaintext)
        inv_mix_columns(plaintext)
    inv_shift_rows(plaintext)
    plaintext = from_matrix(plaintext)
    plaintext = xor(bytes.fromhex(plaintext), bytes.fromhex(zero))
    return plaintext.hex()
```

完整程式：

```python
from pwn import xor

pt = ""
key = ""

def split(full_key):
    k = full_key
    k1 = ""
    k2 = ""
    k3 = ""
    k4 = ""
    sub_keys = [k1, k2, k3, k4]
    for i in range(len(k)):
        sub_keys[i%4] = str(sub_keys[i%4]) + str(k[0])
        k = k[1:]
    return sub_keys

def glue(parts):
    k = ""
    for i in range(32):
        k = str(k) + str(parts[i%4][0])
        parts[i%4] = str(parts[i%4][1:])
    return k

def rot_word(word):
    return str(word[2:]) + str(word[0:2])

def sub_word(word):
    return word

def rcon(word):
    return word

def gen_keys(master_key):
    keys = []
    rounds = 0
    k = master_key

    while (rounds < 11):
        keys.append(k)
        sub_keys = split(k)
        sub_keys[-1] = rot_word(sub_keys[-1])
        sub_keys[-1] = sub_word(sub_keys[-1])
        sub_keys[-1] = rcon(sub_keys[-1])
        sub_keys[0] = xor(bytes.fromhex(sub_keys[0]), bytes.fromhex(sub_keys[-1])).hex()
        sub_keys[1] = xor(bytes.fromhex(sub_keys[1]), bytes.fromhex(sub_keys[0])).hex()
        sub_keys[2] = xor(bytes.fromhex(sub_keys[2]), bytes.fromhex(sub_keys[1])).hex()
        sub_keys[3] = xor(bytes.fromhex(sub_keys[3]), bytes.fromhex(sub_keys[2])).hex()
        k = glue(sub_keys)
        rounds += 1
    
    return keys

def to_matrix(key):
    bytes_list = [int(key[i:i+2], 16) for i in range(0, 32, 2)]

    array = [[0] * 4 for _ in range(4)]
    for i in range(16):
        row = i % 4
        col = i // 4
        array[row][col] = hex(bytes_list[i])[2:]
    
    return array

def from_matrix(matrix):
    reconstructed = ""
    for col in range(4):
        for row in range(4):
            reconstructed += matrix[row][col].zfill(2)
    return reconstructed

def sub_bytes(state):
    return state

def shift_rows(state):
    placeholder = state[1][0]
    state[1][0], state[1][1], state[1][2], state[1][3] = state[1][1], state[1][2], state[1][3], state[1][0]
    state[2][0], state[2][1], state[2][2], state[2][3] = state[2][2], state[2][3], state[2][0], state[2][1]
    state[3][0], state[3][1], state[3][2], state[3][3] = state[3][3], state[3][0], state[3][1], state[3][2]
    return state

#adopted and insipred by the code from the wikipedia article Rijndael MixColumns. 
def gmul(a, b):
    b = int(b, 16)
    p = 0
    for c in range(8):
        if b & 1:
            p ^= a
        a <<= 1
        if a & 0x100:
            a ^= 0x11b
        b>>=1
    return p

def mix_columns(s):
    ss = [[0] * 4 for _ in range(4)]

    for c in range(4):
        ss[0][c] = hex(gmul(0x02, s[0][c]) ^ gmul(0x03, s[1][c]) ^ int(s[2][c], 16) ^ int(s[3][c], 16))[2:].zfill(2)
        ss[1][c] = hex(int(s[0][c], 16) ^ gmul(0x02, s[1][c]) ^ gmul(0x03, s[2][c]) ^ int(s[3][c], 16))[2:].zfill(2)
        ss[2][c] = hex(int(s[0][c], 16) ^ int(s[1][c], 16) ^ gmul(0x02, s[2][c]) ^ gmul(0x03, s[3][c]))[2:].zfill(2)
        ss[3][c] = hex(gmul(0x03, s[0][c]) ^ int(s[1][c], 16) ^ int(s[2][c], 16) ^ gmul(0x02, s[3][c]))[2:].zfill(2)
    
    for i in range(4):
        for j in range(4):
            s[i][j] = ss[i][j]
    return s

def AES(plaintext, key):
    ciphertext = plaintext
    round_keys = gen_keys(key)
    ciphertext = xor(bytes.fromhex(round_keys[0]), bytes.fromhex(ciphertext)).hex()
    for i in range(1,10):
        ciphertext = to_matrix(ciphertext)
        shift_rows(ciphertext)
        mix_columns(ciphertext)
        ciphertext = from_matrix(ciphertext)
        ciphertext = xor(bytes.fromhex(round_keys[i]), bytes.fromhex(ciphertext)).hex()
    ciphertext = to_matrix(ciphertext)
    shift_rows(ciphertext)
    ciphertext = from_matrix(ciphertext)
    ciphertext = xor(bytes.fromhex(round_keys[10]), bytes.fromhex(ciphertext)).hex()
    return ciphertext

def DecryptAES(ciphertext, zero):
    plaintext = ciphertext
    plaintext = xor(bytes.fromhex(zero), bytes.fromhex(plaintext)).hex()
    plaintext = to_matrix(plaintext)
    for round in range(9, 0, -1):
        inv_shift_rows(plaintext)
        plaintext = from_matrix(plaintext)
        plaintext = xor(bytes.fromhex(plaintext), bytes.fromhex(zero)).hex()
        plaintext = to_matrix(plaintext)
        inv_mix_columns(plaintext)
    inv_shift_rows(plaintext)
    plaintext = from_matrix(plaintext)
    plaintext = xor(bytes.fromhex(plaintext), bytes.fromhex(zero))
    return plaintext.hex()


def inv_shift_rows(state):
    state[1][0], state[1][1], state[1][2], state[1][3] = state[1][3], state[1][0], state[1][1], state[1][2]
    state[2][0], state[2][1], state[2][2], state[2][3] = state[2][2], state[2][3], state[2][0], state[2][1]
    state[3][0], state[3][1], state[3][2], state[3][3] = state[3][1], state[3][2], state[3][3], state[3][0]
    return state
    
def inv_mix_columns(s):
    ss = [[0] * 4 for _ in range(4)]

    for c in range(4):
        ss[0][c] = hex(gmul(0x0e, s[0][c]) ^ gmul(0x0b, s[1][c]) ^ gmul(0x0d, s[2][c]) ^ gmul(0x09, s[3][c]))[2:].zfill(2)
        ss[1][c] = hex(gmul(0x09, s[0][c]) ^ gmul(0x0e, s[1][c]) ^ gmul(0x0b, s[2][c]) ^ gmul(0x0d, s[3][c]))[2:].zfill(2)
        ss[2][c] = hex(gmul(0x0d, s[0][c]) ^ gmul(0x09, s[1][c]) ^ gmul(0x0e, s[2][c]) ^ gmul(0x0b, s[3][c]))[2:].zfill(2)
        ss[3][c] = hex(gmul(0x0b, s[0][c]) ^ gmul(0x0d, s[1][c]) ^ gmul(0x09, s[2][c]) ^ gmul(0x0e, s[3][c]))[2:].zfill(2)
    
    for i in range(4):
        for j in range(4):
            s[i][j] = ss[i][j]
    return s

zero_bytes ="00000000000000000000000000000000"

# pt1 明文
pt1 = "72616e646f6d64617461313131313131"

# pt1 密文
ct1 = "d7481d89f1aaf5a857f56edd2ae8994c"

# flag 密文
flag_ct = "8c7d66558130eb5796d131beb43c9934"

'''
step 1 : 計算出 AES(zero_bytes, key)
    AES(pt1, key)[已知] = AES(pt1, zero_bytes)[已知] xor AES(zero_bytes, key)[未知]
    >> 計算 AES(zero_bytes, key) = AES(pt1, key)[已知] xor AES(pt1, zero_bytes)[已知]
'''
aes_zero_key = (xor(bytes.fromhex(ct1), bytes.fromhex(AES(pt1, zero_bytes)))).hex()

'''
step 2 : 計算出 AES(flag, zero_bytes)
    AES(flag, key)[已知] = AES(flag, zero_bytes)[未知] xor AES(zero_bytes, key)[已知]
    >> 計算 AES(flag, zero_bytes) = AES(flag, key)[已知] xor AES(zero_bytes, key)[已知]
'''
aes_flag_zero = (xor(bytes.fromhex(flag_ct), bytes.fromhex(aes_zero_key))).hex()

'''
step 3 : 解密 AES(flag, zero_bytes)
'''
flag_decrypt_hex = DecryptAES(aes_flag_zero, zero_bytes)
flag = bytes.fromhex(flag_decrypt_hex).decode('utf-8')
print(flag)
```

得到 flag : `picoCTF{spi1cy!}`
