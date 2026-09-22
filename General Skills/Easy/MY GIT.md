## 題目

https://learn.cylabacademy.org/library/764?page=1&category=5

I have built my own Git server with my own rules!


## 解題

>[Git 基本指令](../../筆記/Git%20基本指令.md)

Launch Instance 後：

```bash
You can clone the challenge repo using the command below.

`git clone ssh://git@foggy-cliff.picoctf.net:56316/git/challenge.git`

Here's the password: `d9df7038`

Check the README to get your flag!
```

在終端輸入：

```bash
git clone ssh://git@foggy-cliff.picoctf.net:56316/git/challenge.git
```

輸入 `yes` 、密碼 `d9df7038` 後，即可成功 Clone：

```css
challenge
└── README.md
```

打開 `README.md` ：

```markdown
# MyGit

### If you want the flag, make sure to push the flag!

Only flag.txt pushed by ```root:root@picoctf``` will be updated with the flag.

GOOD LUCK!
```

更改 `user.name` 、`user.email`：

```bash
git config --global user.name "root"
git config --global user.email "root@picoctf"
```

新增 `flag.txt`：

```bash
echo "flag" > flag.txt
```

推上 git：

```bash
git add .
git commit -m "flag.txt"
git push
```

輸入密碼後，即可得到 flag ：  

```bash
❯ git push
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
git@foggy-cliff.picoctf.net's password: 
枚舉物件: 4, 完成.
正在計算物件數量: 100% (4/4), 完成.
使用 24 個執行緒進行壓縮
壓縮物件中: 100% (2/2), 完成.
寫入物件中: 100% (3/3), 267 位元組 | 267.00 KiB/秒, 完成.
總共 3 (差異 0)，復用 0 (差異 0)，重用包 0 (總共 0)
remote: Author matched and flag.txt found in commit...
remote: Congratulations! You have successfully impersonated the root user
remote: Here's your flag: picoCTF{1mp3rs0n4t4_g17_345y_e522152d}
To ssh://foggy-cliff.picoctf.net:60077/git/challenge.git
   b4df14d..5a38a29  master -> master
```

`picoCTF{1mp3rs0n4t4_g17_345y_e522152d}`
