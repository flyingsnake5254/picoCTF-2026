## 題目

https://learn.cylabacademy.org/library/240?page=3&category=5

Fix the syntax error in this Python script to print the flag.

[Download Python script](https://artifacts.picoctf.net/c/26/fixme1.py)


## 解題

將附檔 `fixme1.py` 第 20 行：

```python
  print('That is correct! Here\'s your flag: ' + flag)
```

拿掉前面的空白：

```python
print('That is correct! Here\'s your flag: ' + flag)
```

然後執行 `fixme1.py` ，即可拿到 flag :  

```bash
That is correct! Here's your flag: picoCTF{1nd3nt1ty_cr1515_09ee727a}
```