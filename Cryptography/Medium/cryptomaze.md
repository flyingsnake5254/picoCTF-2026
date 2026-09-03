https://learn.cylabacademy.org/library/703?page=2&category=2

## 題目

In this challenge, you are tasked with recovering a hidden flag that has been encrypted using a combination of Linear Feedback Shift Register (LFSR) and AES encryption. The LFSR is used to derive a key for AES encryption, making it crucial to understand its workings to decrypt the message.

The flag has been stored in a file and encrypted. Your goal is to derive the key used for encryption from the LFSR state and taps provided in the output, and then decrypt the flag to retrieve it.

Download the encrypted flag from [here](https://challenge-files.picoctf.net/c_plain_mesa/885fd6cc41821fc4a8307f84cadcd7606d80a7befc621762472a3ab35d32555e/output.txt). which contains the following information:

- The initial state of the LFSR
- The taps used for the LFSR
- The encrypted flag in hexadecimal format


## 附檔

**output.txt**
```css
LFSR Initial State:
[0, 0, 1, 0, 0, 1, 0, 1, 1, 1, 1, 0, 1, 1, 0, 0, 1, 0, 0, 1, 0, 1, 1, 0, 1, 0, 0, 1, 0, 1, 0, 1, 0, 1, 0, 0, 1, 1, 0, 1, 1, 0, 0, 0, 1, 0, 1, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 1, 1, 0, 1, 1]
LFSR Taps:
[63, 61, 60, 58]
Encrypted Flag:
8f0e6d0f5b0dc1db201948b9e0cebd8f81d250455a05ee7c9e2ba57a1bc5428938338e7e04fbddef0c6260a4eb758417

```

## 解題

>[AES](../../筆記/AES.md)  
>[LFSR](../../筆記/LFSR.md)

### 說明

本題使用 LFSR 生成 AES 的 Key。
找 Key 時，需要找出 LFSR 是向左位移 or 向右位移。

本題使用之 LFSR 是使用向左位移，其流程如下：
1. 計算 Feedback :
$$feedback = Initial State[63] \oplus Initial State[61] \oplus Initial State[60] \oplus Initial State[58]$$
2. 向左位移一格：
   將最左位元，也就是 $Initial\ State\ [0]$ 做為本拍的輸出，並且將輸出存於 $key$ 
   
3. 將 $feedback$ 填補至最右端，也就是 $Initial\ State$ 的最尾端
4. 重複迭代，直到 $key$ 的大小為 $16、24、32$ Bytes

本題最終的 $key$ 長度是 $16$ Bytes

### 完整 Python 程式

```python
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad

lfsr_init = [0, 0, 1, 0, 0, 1, 0, 1, 1, 1, 1, 0, 1, 1, 0, 0, 1, 0, 0, 1, 0, 1, 1, 0, 1, 0, 0, 1, 0, 1, 0, 1, 0, 1, 0, 0, 1, 1, 0, 1, 1, 0, 0, 0, 1, 0, 1, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 1, 1, 0, 1, 1]
lfsr_taps = [63, 61, 60, 58]
ciphertext = '8f0e6d0f5b0dc1db201948b9e0cebd8f81d250455a05ee7c9e2ba57a1bc5428938338e7e04fbddef0c6260a4eb758417'

# 將存放 bit 的 list 轉成字串
# b = [0, 1, 0] → '010'
def bin_list_to_string(bin_list):
    bin_str = ''
    for i in bin_list:
        bin_str += str(i)
    return bin_str

# 將二進位字串轉成 bytes
# '010' > bytes
def bin_str_to_bytes(s):
    bytes_big = int(s, 2).to_bytes((len(s) + 7) // 8, byteorder="big")
    return bytes_big

# 生成 AES Key，輸入參數為 key 的長度，單位為 Bytes
# 常見長度為 16, 24, 32 Bytes
def gen_aes_key(key_size):
    global lfsr_init
    global lfsr_taps

    init_state = lfsr_init.copy()
    key = ''
    for _ in range(key_size * 8):
        # 計算 feedback
        feedback = init_state[lfsr_taps[0]] ^ init_state[lfsr_taps[1]] ^ init_state[lfsr_taps[2]] ^ init_state[lfsr_taps[3]]
        
        # 將最左端 bit 做為本拍輸出
        key += str(init_state[0])

        # 向左位移一格，移除最左端 bit
        init_state.remove(init_state[0])

        # 將 feedback 補回最右端
        init_state += [feedback]
        
    return bin_str_to_bytes(key)

key = [gen_aes_key(16), gen_aes_key(24), gen_aes_key(32)]

# 在此行嘗試不同長度的 AES Key
cipher = AES.new(key[0], AES.MODE_ECB)

decrypted = unpad(
    cipher.decrypt(bytes.fromhex(ciphertext)),
    AES.block_size
)

print("Decrypted text:", decrypted.decode("utf-8"))


```

