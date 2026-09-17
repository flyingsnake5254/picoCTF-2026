## 題目

https://learn.cylabacademy.org/library/474?page=2&category=2

The imposter was able to fool us last time, so we've strengthened our defenses!

Here's our [list](https://challenge-files.picoctf.net/c_verbal_sleep/02bab2314d443811c9863f7a22f9e75edda564a88ab4d7051e1d1e89c231e8ab/cheese_list.txt) of cheeses.

## 提示

```bash
Remember Squeexy, we enjoy our cheese with exactly _2 nibbles_ of _hexadecimal-character salt_!
```


## 解題

本題使用 nc 連線後：

```bash
*******************************************
***             Part 2                  ***
***    The Mystery of the CLONED RAT    ***
*******************************************

DRAT! The evil Dr. Lacktoes Inn Tolerant's clone was able to guess the cheese last time! I guess simple ciphers aren't good hashing methods. But now I've strengthened my encryption scheme so that now ONLY SQUEEXY can guess it...

Here's my secret cheese -- if you're Squeexy, you'll be able to guess it:  e2598325a5f548be9f3c582897a7e80df041c1afdaaef11bd1ec893c1dd79175

Commands: (g)uess my cheese
What would you like to do?
```

可看到一串 hash。

寫一個 python ，將附檔 `cheese_list.txt` 裡面每行 cheese 都計算出雜湊，並比對尋找符合的 cheese。

根據提示，本題有加入兩個十六進位當成 salt（也就是 0 ~ 255），但沒有說明加到哪個位置，也沒說明怎麼加。故須嘗試所有可能。

撰寫程式執行以下嘗試：

- **1. 將 cheese 去掉頭尾空白**
- **2. 將 cheese 字串轉成 bytes**
- **3. 嘗試 salt = 0 ~ 255，並且將 salt 轉成 bytes**
- **4. 嘗試將 salt 插入 cheese bytes 的每個位置**
- **5. 嘗試 cheese 的原始字串、全小寫、全大寫**
- **6. 計算 Hash**

```python
import hashlib

target = 'e2598325a5f548be9f3c582897a7e80df041c1afdaaef11bd1ec893c1dd79175'

with open("cheese_list.txt", "r", encoding="utf-8") as f:
    contents = f.readlines()

for i in range(len(contents)):
    contents[i] = contents[i].strip()

# 嘗試原始字串
for content in contents:

    for i in range(256):

        salt = bytes([i])

        # 嘗試原始字串
        content_bytes = content.encode('utf-8')
        for salt_pos in range(len(content_bytes) + 1):
            temp = content_bytes[:salt_pos] + salt + content_bytes[salt_pos:]
            h = hashlib.sha256(temp).hexdigest()
            if target == h:
                print(content, i, hex(i))
        
        # 嘗試小寫字串
        content_bytes = (content.lower()).encode('utf-8')
        for salt_pos in range(len(content_bytes) + 1):
            temp = content_bytes[:salt_pos] + salt + content_bytes[salt_pos:]
            h = hashlib.sha256(temp).hexdigest()
            if target == h:
                print(content, i, hex(i))

        # 嘗試大寫字串
        content_bytes = (content.upper()).encode('utf-8')
        for salt_pos in range(len(content_bytes) + 1):
            temp = content_bytes[:salt_pos] + salt + content_bytes[salt_pos:]
            h = hashlib.sha256(temp).hexdigest()
            if target == h:
                print(content, i, hex(i))
        

```

執行後，找到該 hash 原本的 cheese 字串、salt： `Stilton 67 0x43`  

最後輸入 `Stilton` 、`43` 即可得到 flag  

```bash
Commands: (g)uess my cheese
What would you like to do?
g

   _   _
  (q\_/p)
   /. .\.-.....-.     ___,
  =\_t_/=     /  `\  (
    )\ ))__ __\   |___)
   (/-(/`  `nn---'

SQUEAK SQUEAK SQUEAK

         _   _
        (q\_/p)
         /. .\        
  ,__   =\_t_/=   
     )   /   \      
    (   ((   ))   
     \  /\) (/\    
      `-\  Y  /    
         nn^nn        
                          

Is that you, Squeexy? Are you ready to GUESS...MY...CHEEEEEEESE?
Remember, this is my encrypted cheese:  e2598325a5f548be9f3c582897a7e80df041c1afdaaef11bd1ec893c1dd79175
So...what's my cheese?
Stilton
Annnnd...what's my salt?
43

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
Here's the password to the cloning room:  picoCTF{cHeEsY80eed518}
```

