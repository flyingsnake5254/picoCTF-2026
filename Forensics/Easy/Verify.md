## 題目

https://learn.cylabacademy.org/library/450?page=1&category=4

People keep trying to trick my players with imitation flags. I want to make sure they get the real thing! I'm going to provide the SHA-256 hash and a decrypt script to help you know that my flags are legitimate.


## 解題

>[Shell 基本語法](../../筆記/Shell%20基本語法.md)

Launch Instance 後：

`ssh -p 60425 ctf-player@rhea.picoctf.net`

Using the password `83dcefb7`. Accept the fingerprint with `yes`, and `ls` once connected to begin. Remember, in a shell, passwords are hidden!

- Checksum: 467a10447deb3d4e17634cacc2a68ba6c2bb62a6637dad9145ea673bf0be5e02
- To decrypt the file once you've verified the hash, run `./decrypt.sh files/<file>`.

先使用 `ssh` 連線：

```bash
ssh -p 60425 ctf-player@rhea.picoctf.net
```

輸入 yes 後，再輸入密碼：`83dcefb7`

連線成功後，使用 `ls -al` 查看檔案：

```bash
total 20
drwxr-xr-x 3 ctf-player ctf-player   57 Mar  9  2024 .
drwxr-xr-x 1 ctf-player ctf-player   20 Sep 20 02:45 ..
-rw-r--r-- 1 root       root         65 Mar  9  2024 checksum.txt
-rwxr-xr-x 1 root       root        856 Mar  9  2024 decrypt.sh
drwxr-xr-x 2 ctf-player ctf-player 8192 Mar  9  2024 files
```

其中 `files` 為資料夾，底下有多個檔案，根據題目，需使用以下進行解密：

```bash
./decrypt.sh files/<file>
```

利用 shell 的 for 語法，對 files 底下的每個檔案執行 `./decrypt.sh files/<file>` ：

```bash
for file in files/*; do ./decrypt.sh "$file" >> output.txt; done
```

其會將執行結果全部輸出至 `output.txt`，接下來輸出 `output.txt` 的內容，並使用 `grep` 過濾出 flag :  

```bash
cat output.txt | grep "picoCTF"
```

即可得到 flag ：  
picoCTF{trust_but_verify_c6c8b911}