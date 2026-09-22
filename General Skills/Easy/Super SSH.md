## 題目

https://learn.cylabacademy.org/library/424?page=2&category=5

Using a Secure Shell (SSH) is going to be pretty important.


## 解題

>[SSH](../../筆記/ssh.md)

Launch Instance 後：

Can you `ssh` as `ctf-player` to `titan.picoctf.net` at port `64439` to get the flag?

You'll also need the password `6dd28e9b`. If asked, accept the fingerprint with `yes`.

If your device doesn't have a shell, you can use: [](https://webshell.picoctf.org/)[https://webshell.picoctf.org](https://webshell.picoctf.org/)

If you're not sure what a shell is, check out our Primer: [](https://primer.picoctf.com/#_the_shell)[https://primer.picoctf.com/#_the_shell](https://primer.picoctf.com/#_the_shell)


使用 `ssh` 連線：

```bash
ssh -p 64439 ctf-player@titan.picoctf.net
```

輸入 `yes` 與密碼 `6dd28e9b` 後，即可得到 flag :  

```bash
The authenticity of host '[titan.picoctf.net]:64439 ([3.139.174.234]:64439)' can't be established.
ED25519 key fingerprint is: SHA256:4S9EbTSSRZm32I+cdM5TyzthpQryv5kudRP9PIKT7XQ
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[titan.picoctf.net]:64439' (ED25519) to the list of known hosts.
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
ctf-player@titan.picoctf.net's password: 
Welcome ctf-player, here's your flag: picoCTF{s3cur3_c0nn3ct10n_5d09a462}
Connection to titan.picoctf.net closed.
```

```bash
picoCTF{s3cur3_c0nn3ct10n_5d09a462}
```