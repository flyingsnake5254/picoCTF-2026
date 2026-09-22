## 題目

https://learn.cylabacademy.org/library/710?page=1&category=5

We intercepted a suspiciously encoded message, but it’s clearly hiding a flag. No encryption, just multiple layers of obfuscation. Can you peel back the layers and reveal the truth?

Download the [message](https://challenge-files.picoctf.net/c_plain_mesa/4986bcdd15422ff14839a371ad1807f27508401eab11d33c48acf3b4633cf6ef/message.txt).


## 附檔

**message.txt**

```text
NjM3NjcwNjI1MDQ3NTMyNTM3NDI2MTcyNjY2NzcyNzE1ZjcyNjE3MDMwNzE3NjYxNzQ1ZjM2MzY2ZjM1MzQzMjM1MzcyNTM3NDQ=
```

## 解題

### 方法一：CyberChef

提示說可以使用 [CyberChef](https://gchq.github.io/CyberChef/)

先將 `message.txt` 內容貼到 Input，接著從 Operations 依序拖曳：
1. From Base64
2. From Hex
3. URL Decode
4. ROT 13

即可解出 flag :  

```bash
picoCTF{nested_enc0ding_66b54257}
```


### 方法二：用各種指令、Python 程式解題

>[base64 編碼、解碼](../../筆記/base64%20編碼、解碼.md)
>[URL 編碼、解碼](../../筆記/URL%20編碼、解碼.md)

```python
import base64
from urllib.parse import unquote

def ROT13(s):
    result = ''
    for c in s:
        if c.isalpha():
            if c.isupper():
                result += chr((((ord(c) - ord('A')) + (ord('P') - ord('C'))) % 26) + ord('A'))
            else:
                result += chr((((ord(c) - ord('a')) + (ord('p') - ord('c'))) % 26) + ord('a'))
        else:
            result += c
    return result

encoded = "NjM3NjcwNjI1MDQ3NTMyNTM3NDI2MTcyNjY2NzcyNzE1ZjcyNjE3MDMwNzE3NjYxNzQ1ZjM2MzY2ZjM1MzQzMjM1MzcyNTM3NDQ="

# 1. base64 解碼
decoded_hex = base64.b64decode(encoded).decode('utf-8')

# 2. 16 進位解碼
url_encode_text = bytes.fromhex(decoded_hex).decode('utf-8')

# 3. URL 解碼
url_decoded_text = unquote(url_encode_text)

# 4. ROT 13
print(ROT13(url_decoded_text))
```

輸出：

```bash
picoCTF{nested_enc0ding_66b54257}
```