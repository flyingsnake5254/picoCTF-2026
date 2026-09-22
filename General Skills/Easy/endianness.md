## 題目

https://learn.cylabacademy.org/library/414?page=2&category=5

Know of little and big endian?

[Source](https://artifacts.picoctf.net/c_titan/117/flag.c)


## 解題

>[格式轉換](../../筆記/格式轉換.md)

Launch Instance 後，使用 `nc` 連線：

```bash
nc titan.picoctf.net 51413
```

```bash
Welcome to the Endian CTF!
You need to find both the little endian and big endian representations of a word.
If you get both correct, you will receive the flag.
Word: cyyql
Enter the Little Endian representation:
```

寫一隻 python 進行轉換：

```python
s = b'cyyql'
s = s.hex()

bytes_big = (int(s, 16).to_bytes(len(s) // 2, byteorder="big")).hex()
bytes_little = (int(s, 16).to_bytes(len(s) // 2, byteorder="little")).hex()

print('big : ', bytes_big)
print('little : ', bytes_little)
```

輸出：

```bash
big :  637979716c
little :  6c71797963
```

然後分別在連線到的終端輸入，即可得到 flag :  

```bash
Enter the Little Endian representation: 6c71797963
Correct Little Endian representation!
Enter the Big Endian representation: 637979716c
Correct Big Endian representation!
Congratulations! You found both endian representations correctly!
Your Flag is: picoCTF{3ndi4n_sw4p_su33ess_25c5f083}
```