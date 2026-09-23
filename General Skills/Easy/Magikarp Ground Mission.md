## 題目

https://learn.cylabacademy.org/library/189?page=3&category=5

Do you know how to move between directories and read files in the shell? Start the container, `ssh` to it, and then `ls` once connected to begin.


## 解題

Launch Instance 後：

Login via `ssh` as `ctf-player` with the password, `8c606eb1` on the host `wily-courier.picoctf.net` and port `61992`.


接著使用 `ssh` 連線：

```bash
ssh -p 61992 ctf-player@wily-courier.picoctf.net
```

並輸入 `yes` 與密碼 `8c606eb1`

連線完成後，使用 `ls`：

```bash
ctf-player@pico-chall$ ls
1of3.flag.txt  instructions-to-2of3.txt
```

先輸出 `1of3.flag.txt` ，得到 flag 的第一部份：

```bash
ctf-player@pico-chall$ cat 1of3.flag.txt
picoCTF{xxsh_
```

然後輸出 `instructions-to-2of3.txt` ，取得第二部份的提示：

```bash
ctf-player@pico-chall$ cat instructions-to-2of3.txt
Next, go to the root of all things, more succinctly `/`
```

根據提示，前往 `/` ：

```bash
ctf-player@pico-chall$ cd /
```

並使用 `ls` 查看：

```bash
ctf-player@pico-chall$ ls
2of3.flag.txt  boot       dev  home                      lib    media  opt   root  sbin  sys  usr
bin            challenge  etc  instructions-to-3of3.txt  lib64  mnt    proc  run   srv   tmp  var
```

輸出 `2of3.flag.txt` ，取得第二部份 flag :

```bash
ctf-player@pico-chall$ cat 2of3.flag.txt
0ut_0f_//4t3r_
```

輸出 `instructions-to-3of3.txt` ，取得第三部份提示：

```bash
ctf-player@pico-chall$ cat instructions-to-3of3.txt
Lastly, ctf-player, go home... more succinctly `~`
```

前往 `~`：

```bash
ctf-player@pico-chall$ cd ~
```

使用 `ls` 查看：

```bash
ctf-player@pico-chall$ ls
3of3.flag.txt  drop-in
```

輸出 `3of3.flag.txt` ：

```bash
ctf-player@pico-chall$ cat 3of3.flag.txt
0b24fc4f}
```

組合三個部份的 flag，即可取得完整 flag :  

```bash
picoCTF{xxsh_0ut_0f_//4t3r_0b24fc4f}
```