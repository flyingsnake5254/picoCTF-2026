## 題目

https://learn.cylabacademy.org/library/242?page=3&category=5

Our flag printing service has started glitching!



## 解題

Launch Instance 後，使用 `nc` 連線：

```bash
nc saturn.picoctf.net 59818
```

接著得到：

```python
'picoCTF{gl17ch_m3_n07_' + chr(0x61) + chr(0x34) + chr(0x33) + chr(0x39) + chr(0x32) + chr(0x64) + chr(0x32) + chr(0x65) + '}'
```

使用 python 輸出這段，即可得到 flag :  

```python
print('picoCTF{gl17ch_m3_n07_' + chr(0x61) + chr(0x34) + chr(0x33) + chr(0x39) + chr(0x32) + chr(0x64) + chr(0x32) + chr(0x65) + '}')
```

輸出：

```bash
picoCTF{gl17ch_m3_n07_a4392d2e}
```