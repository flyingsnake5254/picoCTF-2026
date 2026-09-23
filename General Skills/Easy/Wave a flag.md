## 題目

https://learn.cylabacademy.org/library/170?page=4&category=5

Can you invoke help flags for a tool or binary? This program has extraordinarily helpful information...

[warm](https://challenge-files.picoctf.net/c_wily_courier/1e14db3a752e16eae2b0e0d73d9779f9c4ddfd8942f60f3285a2986068480316/warm)


## 解題

使用 `strings` 與 `grep` 找到 flag :  

```bash
strings warm | grep "picoCTF"
```

輸出：

```bash
Oh, help? I actually don't do much, but I do have this flag here: picoCTF{b1scu1ts_4nd_gr4vy_ac5832c}
```

