https://learn.cylabacademy.org/library/470?page=1&category=2

## 題目

This service provides you an encrypted flag. Can you decrypt it with just N & e?

Connect to the program with netcat:

`$ nc verbal-sleep.picoctf.net 57525`

The program's source code can be downloaded [here](https://challenge-files.picoctf.net/c_verbal_sleep/c798cbe85b3e431345406f393827b9b905481b5fcd6d4b4a845527ee0602da9b/encrypt.py)

.
## 解題

### 附檔

**encrypt.py**

```python
from sys import exit
from Crypto.Util.number import bytes_to_long, inverse
from setup import get_primes

e = 65537

def gen_key(k):
    """
    Generates RSA key with k bits
    """
    p,q = get_primes(k//2)
    N = p*q
    d = inverse(e, (p-1)*(q-1))

    return ((N,e), d)

def encrypt(pubkey, m):
    N,e = pubkey
    return pow(bytes_to_long(m.encode('utf-8')), e, N)

def main(flag):
    pubkey, _privkey = gen_key(1024)
    encrypted = encrypt(pubkey, flag) 
    return (pubkey[0], encrypted)

if __name__ == "__main__":
    flag = open('flag.txt', 'r').read()
    flag = flag.strip()
    N, cypher  = main(flag)
    print("N:", N)
    print("e:", e)
    print("cyphertext:", cypher)
    exit()


```

### 解題

根據 encrypt.py 可知使用的是 [RSA](../../筆記/RSA.md)，先使用 `nc` 連線：

```bash
❯ nc verbal-sleep.picoctf.net 55932
zsh: correct 'nc' to 'nfc' [nyae]? n
N: 20409970968800327096927781593678043704188879844910815735424587329208661228123527877388513359867770365402759433526449534972120046694306482221229997052039314
e: 65537
cyphertext: 14481276738095791196786170675845565497170930800944519355977243107557301376461073283848879817920533384535097783470414084873202313589750861034230353228466539
```

RSA 的公鑰私鑰如下：

選定兩質數：$p$ 、$q$
- $n = p\times q$
- 公鑰：$(e, n)$
- 私鑰：$(d, n)$

觀察到 $N$ 的尾數是 **偶數** ，故 $p$、$q$ 兩個，要馬兩個都是偶數，要馬其中一個是偶數。又質數當中為偶數的只有 $2$ ，故 $p$ 、$q$ 其中之一為 $2$，因此將 $N \div 2$  即可得到另一個質數。

接下來進行 RSA 公鑰私鑰生成流程：

- Step 1 : 決定 $p$、$q$
- Step 2 : 計算 $n = p × q$ （$N$ 的值題目已經有了）
- Step 3 : 計算歐拉函數 $\phi (n)=(p-1)(q-1)$
* Step 4 : 計算私鑰 $d$
	  $d\equiv e^{-1}\,\,\,mod\,\,\,\phi (n)$

加解密流程：

  $M：明文$  
  $C：密文$
  
- **加密**
  $C=M^e\,\,\,mod\,\,\,n$
  
```css
"ABC"
  ↓ encode()
b'ABC'
  ↓ 每個 byte
0x41 0x42 0x43

把這 3 個 byte 接起來：0x41 42 43
也就是一個十六進位整數：0x414243
換成十進位就是：4276803
```
  
- **解密**
  $M=C^d\,\,\,mod\,\,\,n$
  
  解密後會得到一個大整數，要先把它轉成 16 進位，然後每兩個 16 進位字元代表 1 Byte，故以兩個 16 進位字元為一組，將其轉換為字元

將上述寫成 python 程式：
```python
N = 20409970968800327096927781593678043704188879844910815735424587329208661228123527877388513359867770365402759433526449534972120046694306482221229997052039314
e = 65537
p = 2
q = N // 2
phi = (p-1) * (q-1)

# 計算私鑰
d = pow(e, -1, phi)

# 密文
C = 14481276738095791196786170675845565497170930800944519355977243107557301376461073283848879817920533384535097783470414084873202313589750861034230353228466539

# 解密
M = hex(pow(C, d, N)).replace("0x", "")  # 將解密後的大整數轉成 16 進位

print(bytes.fromhex(M).decode('utf-8')) # 將 16 進位字串轉成 Bytes 後，解碼成 utf-8
```

最後輸出得到 flag :  
picoCTF{tw0_1$_pr!m341c6ed35}