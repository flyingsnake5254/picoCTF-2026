## 題目

https://learn.cylabacademy.org/library/67?page=4&category=5

What does this bDNhcm5fdGgzX3IwcDM1 mean? I think it has something to do with bases.


## 解題

使用 `base64` 解碼：

```bash
echo "bDNhcm5fdGgzX3IwcDM1" | base64 -d
```

輸出：

```bash
l3arn_th3_r0p35
```

得到 flag :  

```bash
picoCTF{l3arn_th3_r0p35}
```