## 題目

https://learn.cylabacademy.org/library/759?page=1&category=5

Oops! Someone accidentally sent an important file to a network printer—can you retrieve it from the print server?


## 提示

1. knowing how SMB protocol works would be helpful!
2. smbclient and smbutil are good tools


## 解題

>[smbclient](../../筆記/smbclient.md)

Launch Instance 後，先使用 `nc` 連線：

```bash
nc -vz mysterious-sea.picoctf.net 59932
```

```bash
Connection to mysterious-sea.picoctf.net (3.130.79.223) 59932 port [tcp/*] succeeded!
```


根據提示，接下來使用 `smbclient` 連線，並且先匿名列出分享：

```bash
smbclient -L //mysterious-sea.picoctf.net -p 59932 -N
```

```bash
	Sharename       Type      Comment
	---------       ----      -------
	shares          Disk      Public Share With Guests
	IPC$            IPC       IPC Service (Samba 4.19.5-Ubuntu)
SMB1 disabled -- no workgroup available
```

看到分享名 `shares` ，接著使用匿名連線到該分享：

```bash
smbclient //mysterious-sea.picoctf.net/shares -p 59932 -N
```

先用 `ls` 查看有哪些檔案：

```bash
smb: \> ls
  .                                   D        0  Sat Mar  7 04:25:45 2026
  ..                                  D        0  Sat Mar  7 04:25:45 2026
  dummy.txt                           N     1142  Thu Feb  5 05:22:17 2026
  flag.txt                            N       37  Sat Mar  7 04:25:45 2026

		65536 blocks of size 1024. 58696 blocks available
```

看到 `flag.txt` ，接下來使用 `get` 把 `flag.txt` 抓到本地端：

```bash
get flag.txt
```

接下來輸入 `Ctrl + C` 或 `exit` 離開。

並在本地端終端，輸出 `flag.txt` 檔案內容，即可得到 flag :  

```bash
cat flag.txt
```

```bash
picoCTF{5mb_pr1nter_5h4re5_b3f2f855}
```