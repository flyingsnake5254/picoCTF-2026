## 題目

https://learn.cylabacademy.org/library/698?page=1&category=4

This file doesn't look like much... just a bunch of 1s and 0s. But maybe it's not just random noise. Can you recover anything meaningful from this?

Download the file [here](https://challenge-files.picoctf.net/c_plain_mesa/8fa300f2ce84eca8cf2b18d1bd0c9cd92c888b09d838e2462c4edd30d951da59/digits.bin).


## 解題

附檔 `digits.bin` 裡面內容全是 `0101` ，本題只須將這些 0101 每 8 bits 一組，轉成 bytes 後，存成 jpg 圖片即可。

撰寫 python 程式，先讀取 `digits.bin` ，接下來每 8 bits 轉成 byte 後，寫入 output.jpg：

```python
with open("digits.bin", "rb") as f:
    data = f.read()

with open("output.jpg", "ab") as f:
    for i in range(0, len(data), 8):
        f.write(bytes([int(data[i:i+8], 2)]))
```

最後輸出的圖片 `output.jpg` ：

![](../../assets/images/Forensics/Easy/Binary%20Digits1.png)


得到 flag :  
picoCTF{h1dd3n_1n_th3_b1n4ry_8d00e35f}