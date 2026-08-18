LFSR 是 **Linear Feedback Shift Register，線性回饋移位暫存器**。

它是一種會依照固定規則產生 bit 序列的電路/演算法，常用在：

- 偽隨機數產生
    
- CRC / 錯誤偵測
    
- 通訊擾碼
    
- 串流加密
    
- 硬體測試
    

---

## 1. 基本概念

LFSR 由一串 bit 組成，例如 4-bit：

```text
[b3 b2 b1 b0]
```

每次 clock 時：

1. 所有 bit 向左或向右移一格
    
2. 某幾個位置的 bit 做 XOR
    
3. XOR 結果作為新 bit 填回去
    

例如：

```text
state = 1 0 0 1
```

假設 feedback 是：

```text
new_bit = b3 XOR b0
```

那麼：

```text
1 XOR 1 = 0
```

移位後可能變成：

```text
0 1 0 0
```

然後一直重複。

---

# 2. 為什麼叫「線性」？

因為它的 feedback 只使用像 XOR 這種線性運算。

在 GF(2) 裡：

```text
加法 = XOR
乘法 = AND
```

例如：

```text
new_bit = b0 XOR b2 XOR b3
```

這就是線性回饋。

---

# 3. 一個簡單例子

假設有 4-bit LFSR：

```text
[b3 b2 b1 b0]
```

feedback 規則：

```text
new = b3 XOR b2
```

初始狀態：

```text
1001
```

每次右移：

```text
1001
1100
0110
1011
0101
0010
0001
1000
...
```

實際序列會依 tap 位置不同而不同。

---

# 4. tap 是什麼？

tap 就是「拿哪些 bit 來做 XOR」。

例如：

```text
new_bit = b3 XOR b0
```

那 tap 就是：

```text
b3, b0
```

不同 tap 會讓 LFSR 的週期不同。

---

# 5. 最大週期

對 n-bit LFSR 而言，理論最大週期是：$2^n - 1$

不是 $2^n$，因為全 0 狀態：

```text
0000
```

如果 feedback 都是 XOR，那麼：

```text
0000 -> 0000 -> 0000
```

會永遠卡死。

例如 4-bit LFSR 最大週期：$2^4 - 1 = 15$

也就是所有非零狀態都跑一次才重複。

---

# 6. 特徵多項式

LFSR 常用 polynomial 表示。

例如：$x^4 + x + 1  $

表示一個 4-bit LFSR。

如果這個 polynomial 是 primitive polynomial，  
那 LFSR 可以達到最大週期：$2^4 - 1 = 15$

---

# 7. Python 簡單實作

例如 4-bit LFSR：

```python
state = 0b1001

for _ in range(15):
    print(f"{state:04b}")

    b3 = (state >> 3) & 1
    b0 = state & 1

    new_bit = b3 ^ b0

    state = ((state << 1) & 0b1111) | new_bit
```

輸出會是一串週期性的 bit pattern。

---

# 8. LFSR 和密碼學

LFSR 很常出現在古典串流加密裡。

概念是：

```text
LFSR -> keystream
```

然後：

```text
ciphertext = plaintext XOR keystream
```

例如：

```text
plaintext : 10110010
keystream : 01101100
            --------
ciphertext: 11011110
```

解密時：

```css
plaintext = ciphertext XOR keystream
```

---

# 9. 單獨 LFSR 安全嗎？

**不安全。**

因為 LFSR 是線性的。

如果你拿到足夠長的輸出 bit，  
可以利用：

- Berlekamp–Massey algorithm
    
- 線性方程組
    
- 已知明文攻擊
    

反推出：

- LFSR 長度
    
- feedback polynomial
    
- 初始 state
    

所以現代密碼通常不會直接只用一個 LFSR。

---

# 10. LFSR 在 CTF 常見用途

CTF crypto 題很常出現：

```python
bit = ((state >> 0) ^ (state >> 2) ^ (state >> 3)) & 1
state = (state >> 1) | (bit << 31)
```

你通常要找：

```text
1. state 長度
2. tap
3. output bit
4. 初始 state
```

如果已知很多輸出 bit，  
可以用 Berlekamp–Massey 直接破解。

---

## 一句話記住

LFSR 就是：

> 用 XOR 當回饋規則，不斷移位，產生週期 bitstream 的 shift register。

如果你願意，我可以下一步直接畫一個 **4-bit LFSR 電路圖**，再帶你一步一步算出每一輪 state。