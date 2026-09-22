## 題目

https://learn.cylabacademy.org/library/371?page=2&category=5

Can you make sense of this file?

Download the file [here](https://artifacts.picoctf.net/c/477/enc_flag).



## 解題

>[base64 編碼、解碼](../../筆記/base64%20編碼、解碼.md)
>[Python 讀寫檔案](../../筆記/Python%20讀寫檔案.md)


寫一隻 python 程式，一直解碼 base64 ，直到解碼文字包含 `picoCTF` 時停止：

```python
import base64

with open("enc_flag", "r", encoding="utf-8") as f:
    encoded = f.read()

while True:
    decoded_bytes = base64.b64decode(encoded)
    decoded_text = decoded_bytes.decode("utf-8")
    if 'picoCTF' in decoded_text:
        break
    encoded = decoded_text

print(decoded_text)
```

輸出：

```bash
picoCTF{base64_n3st3d_dic0d!n8_d0wnl04d3d_de523f49}
```

