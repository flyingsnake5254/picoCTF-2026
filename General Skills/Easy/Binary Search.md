## 題目

https://learn.cylabacademy.org/library/442?page=2&category=5

Want to play a game? As you use more of the shell, you might be interested in how they work! Binary search is a classic algorithm used to quickly find an item in a sorted list. Can you find the flag? You'll have 1000 possibilities and only 10 guesses.

Cyber security often has a huge amount of data to look through - from logs, vulnerability reports, and forensics. Practicing the fundamentals manually might help you in the future when you have to write your own tools!

You can download the challenge files here:

- [challenge.zip](https://artifacts.picoctf.net/c_atlas/19/challenge.zip)


## 解題

先寫一隻 binary search 的 python 程式，協助猜數字：

```python
def f(a, b):
    return a + ((b - a) // 2)

a, b = 1, 1000
while True:
    c = f(a, b)
    print('猜：', c)
    next = input('higher(1) or lower(2) : ')
    if next == '1':
        a = c
    elif next == '2':
        b = c
    else:
        print('輸入錯誤')
        break
```

接著執行此 python 程式。

然後 Launch Instance 後，使用 ssh 連線：

```bash
ssh -p 49375 ctf-player@atlas.picoctf.net
```

然後輸入 `yes` 與密碼 `1db87a14`

然後利用剛剛寫的 python 數字進行猜數字，最後得到 flag :  

python 程式輸入與輸出：

```python
猜： 500
higher(1) or lower(2) : 2
猜： 250
higher(1) or lower(2) : 1
猜： 375
higher(1) or lower(2) : 1
猜： 437
higher(1) or lower(2) : 1
猜： 468
higher(1) or lower(2) : 1
猜： 484
```

題目 server 輸出：

```bash
Welcome to the Binary Search Game!
I'm thinking of a number between 1 and 1000.
Enter your guess: 500
Lower! Try again.
Enter your guess: 250
Higher! Try again.
Enter your guess: 375
Higher! Try again.
Enter your guess: 437
Higher! Try again.
Enter your guess: 468
Higher! Try again.
Enter your guess: 484
Congratulations! You guessed the correct number: 484
Here's your flag: picoCTF{g00d_gu355_1597707f}
Connection to atlas.picoctf.net closed.
```

