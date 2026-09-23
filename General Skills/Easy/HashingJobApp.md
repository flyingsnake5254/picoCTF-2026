## 題目

https://learn.cylabacademy.org/library/243?page=3&category=5

If you want to hash with the best, beat this test!


## 解題

Launch Instance 後，使用 `nc` 連線：

```bash
nc saturn.picoctf.net 63564
```

之後要求輸入特定字串的 md5 hash ，可以使用以下指令輸出 md5 hash :  

```bash
echo -n "字串" | md5sum
```

依序回答 hash ，即可得到 flag：

```bash
Please md5 hash the text between quotes, excluding the quotes: 'apple pie'
Answer: 
b9fcf57e16fa23b2d81bc587ffde4788
b9fcf57e16fa23b2d81bc587ffde4788
Correct.
Please md5 hash the text between quotes, excluding the quotes: 'gravity'
Answer: 
67f2a835697e7c9c2c5146c76eca6038
67f2a835697e7c9c2c5146c76eca6038
Correct.
Please md5 hash the text between quotes, excluding the quotes: 'apple pie'
Answer: 
b9fcf57e16fa23b2d81bc587ffde4788
b9fcf57e16fa23b2d81bc587ffde4788
Correct.
picoCTF{4ppl1c4710n_r3c31v3d_bf2ceb02}
```

