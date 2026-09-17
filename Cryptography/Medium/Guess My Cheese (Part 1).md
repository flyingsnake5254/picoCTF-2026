## 題目

https://learn.cylabacademy.org/library/473?page=2&category=2

Try to decrypt the secret cheese password to prove you're not the imposter!


## 解題

Launch Instance 後，使用 `nc` 連線：

```bash
nc verbal-sleep.picoctf.net 64087
```

連線後看到：

```bash
*******************************************
***             Part 1                  ***
***    The Mystery of the CLONED RAT    ***
*******************************************

The super evil Dr. Lacktoes Inn Tolerant told me he kidnapped my best friend, Squeexy, and replaced him with an evil clone! You look JUST LIKE SQUEEXY, but I'm not sure if you're him or THE CLONE. I've devised a plan to find out if YOU'RE the REAL SQUEEXY! If you're Squeexy, I'll give you the key to the cloning room so you can maul the imposter...

Here's my secret cheese -- if you're Squeexy, you'll be able to guess it:  WQPNKZNKRPT
Hint: The cheeses are top secret and limited edition, so they might look different from cheeses you're used to!
Commands: (g)uess my cheese or (e)ncrypt a cheese
What would you like to do?
```

輸入 `e` ：

```bash
What cheese would you like to encrypt?
```

接下來可以嘗試常見起司名字：
- **切達起司 (Cheddar cheese)**：味道濃郁，常用于漢堡、三明治。
- **莫札瑞拉起司 (Mozzarella cheese)**：質地柔軟，加熱會牽絲，常用於披薩。
- **帕瑪森起司 (Parmesan cheese)**：硬質起司，常磨成粉撒在義大利麵上。
- **高達起司 (Gouda cheese)**：荷蘭半硬質起司，帶有溫和奶香。
- **布里起司 (Brie cheese)**：法國白黴軟質起司，口感滑順。
- **奶油起司 (Cream cheese)**：質地綿密的新鮮起司，常用於貝果或做蛋糕。

輸入 `cheddar` ：

```bash
What cheese would you like to encrypt? cheddar
Here's your encrypted cheese:  JUDGGPQ
Not sure why you want it though...*squeak* - oh well!

I don't wanna talk to you too much if you're some suspicious character and not my BFF Squeexy!
You have 2 more chances to prove yourself to me!
```

得到 `JUDGGPQ` ，本題使用線性加密，也就是：
$$
C=(aM+b) \mod 26
$$

利用 sage 解出線性關係後，解密 `WQPNKZNKRPT` 即可得到 flag

```python
from sage.all import *

def decrypt(s, x, y):
    for _ in s:
        print(chr(ord('A') + (((ord(_) - ord('A')) * x + y) % 26)), end='')
    print()

s1 = 'cheddar'
s2 = 'JUDGGPQ'

x, y = var("x y")

solutions = solve_mod([
    (ord(s2[0]) - ord('A'))*x + y == (ord(s1[0]) - ord('a')),
    (ord(s2[1]) - ord('A'))*x + y == (ord(s1[1]) - ord('a'))
], 26)

print(solutions)

decrypt('WQPNKZNKRPT', int(solutions[0][0]), int(solutions[0][1]))
```

輸出結果：

```python
[(17, 5)]
PRASTOSTIAQ
```

接下來輸入 command `g` 回覆 `PRASTOSTIAQ`，即可得到 flag :  

```bash
Is that you, Squeexy? Are you ready to GUESS...MY...CHEEEEEEESE?
Remember, this is my encrypted cheese:  WQPNKZNKRPT
So...what's my cheese?
PRASTOSTIAQ

         _   _
        (q\_/p)
         /. .\         __
  ,__   =\_t_/=      .'o O'-.
     )   /   \      / O o_.-`|   
    (   ((   ))    /O_.-'  O |  
     \  /\) (/\    | o   o  o|   
      `-\  Y  /    |o   o O.-`  
         nn^nn     | O _.-'      
                   '--`         

munch...

         _   _
        (q\_/p)
         /. .\         __
  ,__   =\_t_/=      .'o O'-.
     )   /   \      / O o_.-`|   
    (   ((   ))      ).-'  O |  
     \  /\) (/\      )   o  o|   
      `-\  Y  /    |o   o O.-`  
         nn^nn     | O _.-'      
                   '--`         

munch...

         _   _
        (q\_/p)
         /. .\         __
  ,__   =\_t_/=      .'o O'-.
     )   /   \      / O o_.-`|   
    (   ((   ))        )'  O |  
     \  /\) (/\          )  o|   
      `-\  Y  /         ) O.-`  
         nn^nn        ) _.-'      
                   '--`         

MUNCH.............

YUM! MMMMmmmmMMMMmmmMMM!!! Yes...yesssss! That's my cheese!
Here's the password to the cloning room:  picoCTF{ChEeSydd15526f}
```

