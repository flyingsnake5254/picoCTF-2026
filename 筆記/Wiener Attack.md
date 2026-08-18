可以，用一個刻意設計成「$d$很小」的 RSA 例子來看 Wiener attack。

假設：

$$
p=223,\quad q=229  
$$

所以：

$$
n=pq=223\times229=51067  
$$

$$
\phi(n)=(223-1)(229-1)=50616  
$$

故意選一個很小的私鑰：

$$
d=5  
$$

RSA 要滿足：

$$
ed\equiv1\pmod{\phi(n)}  
$$

因此算出：

$$
e=40493  
$$

也就是：

```text
Public key:
n = 51067
e = 40493

Private key:
d = 5
```

攻擊者正常情況下只知道：

$$  
(n,e)=(51067,40493)  
$$

不知道 $p,q,\phi(n),d$。

---

## 1. 為什麼這個 (d) 有問題？

Wiener attack 的典型條件大約是：

$$
d<\frac{1}{3}n^{1/4}  
$$

這裡：

$$
n^{1/4}\approx15.03  
$$

因此：

$$
\frac{1}{3}n^{1/4}\approx5.01  
$$

而我們的：

$$
d=5<5.01  
$$

剛好落在 Wiener attack 的範圍內。

---

## 2. 對 (e/n) 做 continued fraction

>[Continued Fraction](../筆記/Continued%20Fraction.md)

計算：

$$
\frac{e}{n}=

\frac{40493}{51067}  
$$

continued fraction 為：

$$
\frac{40493}{51067}=

[0;1,3,1,4,1,6,\dots]  
$$

然後計算它的 convergents：

$$
\frac01,  
\frac11,  
\frac34,  
\frac45,  
\frac{19}{24},  
\frac{23}{29},  
\dots  
$$

Wiener attack 的核心就是：

> 把每個 convergent $\frac{k}{d}$ 的分母拿來當作候選私鑰 $d$。

所以我們會依序測試：

```text
k     d
---------
0     1
1     1
3     4
4     5   ← !!!
19    24
23    29
...
```

真正的答案就是：

$$
\frac{k}{d}=\frac45  
$$

所以：

$$
d=5  
$$

---

## 3. 怎麼確認 (d=5) 真的是答案？

RSA 關係式：

$$  
ed-k\phi(n)=1  
$$

因此：

$$
\phi(n)=\frac{ed-1}{k}  
$$

代入：

$$
e=40493  
$$
$$
d=5  
$$

$$
k=4  
$$

得到：

$$
\phi(n)=\frac{40493\times5-1}{4}  
=\frac{202465-1}{4}  
=50616  
$$


成功得到：

$$
\boxed{\phi(n)=50616}  
$$

接下來甚至可以把 (p,q) 找回來。

因為：

$$
\phi(n)=(p-1)(q-1)  
$$

展開：

$$
\phi(n)=pq-p-q+1  
$$

又因為：

$$
pq=n  
$$

所以：

$$
p+q=n-\phi(n)+1  
$$

代入：

$$
p+q=51067-50616+1=452  
$$

因此 (p,q) 是：

$$
x^2-452x+51067=0  
$$

判別式：

$$
\Delta =

452^2-4(51067)  
=36  
$$
所以：

$$
\sqrt{\Delta}=6  
$$

得到：

$$
p=\frac{452-6}{2}=223  
$$

$$
q=\frac{452+6}{2}=229  
$$

完全恢復原本的：

$$
\boxed{p=223,\quad q=229,\quad d=5}  
$$

---

## 4. Python 實作 Wiener Attack

這個版本不需要額外套件：

```python
from math import isqrt


def continued_fraction(numerator, denominator):
    result = []

    while denominator:
        a = numerator // denominator
        result.append(a)

        numerator, denominator = (
            denominator,
            numerator - a * denominator
        )

    return result


def convergents(cf):
    p2, p1 = 0, 1
    q2, q1 = 1, 0

    for a in cf:
        p = a * p1 + p2
        q = a * q1 + q2

        yield p, q

        p2, p1 = p1, p
        q2, q1 = q1, q


def wiener_attack(e, n):

    cf = continued_fraction(e, n)

    for k, d in convergents(cf):

        if k == 0:
            continue

        # ed - 1 必須可以被 k 整除
        if (e * d - 1) % k != 0:
            continue

        phi = (e * d - 1) // k

        # p + q
        s = n - phi + 1

        # discriminant
        D = s * s - 4 * n

        if D < 0:
            continue

        sqrt_D = isqrt(D)

        # D 必須是完全平方
        if sqrt_D * sqrt_D != D:
            continue

        # p、q 必須是整數
        if (s + sqrt_D) % 2 != 0:
            continue

        p = (s + sqrt_D) // 2
        q = (s - sqrt_D) // 2

        if p * q == n:
            return d, p, q

    return None


n = 51067
e = 40493

result = wiener_attack(e, n)

print(result)
```

輸出：

```text
(5, 229, 223)
```

也就是：

```text
d = 5
p = 229
q = 223
```

---

## 5. 再示範實際 RSA 解密

例如我們把：

```text
HI
```

轉成整數：

```python
m = int.from_bytes(b"HI", "big")
```

得到：

$$
m=18505  
$$

公鑰加密：

```python
n = 51067
e = 40493

m = int.from_bytes(b"HI", "big")

c = pow(m, e, n)

print(c)
```

得到：

```text
12542
```

攻擊者現在只拿到：

```text
n = 51067
e = 40493
c = 12542
```

先 Wiener attack：

```python
d, p, q = wiener_attack(e, n)

print("d =", d)
```

得到：

```text
d = 5
```

再解密：

```python
m = pow(c, d, n)

plaintext = m.to_bytes(
    (m.bit_length() + 7) // 8,
    "big"
)

print(plaintext)
```

輸出：

```text
b'HI'
```

所以整個攻擊流程其實就是：

```text
公開資訊
n = 51067
e = 40493
c = 12542
        │
        ▼
計算 e/n 的 continued fraction
        │
        ▼
Convergents
0/1
1/1
3/4
4/5   ← 找到
...
        │
        ▼
猜測
k = 4
d = 5
        │
        ▼
φ(n) = (ed-1)/k
     = 50616
        │
        ▼
恢復 p,q
223 × 229
        │
        ▼
得到 private key
d = 5
        │
        ▼
m = c^d mod n
        │
        ▼
"HI"
```

最重要的是理解這個近似：

$$
ed-k\phi(n)=1  
$$

所以：

$$
\frac ed  
$$

不是我們要的；整理後是：

$$
\frac e{\phi(n)}  
\approx  
\frac{k}{d}  
$$

又因為：

$$
\phi(n)\approx n  
$$

因此：

$$
\boxed{\frac en\approx\frac kd}  
$$

當 $d$ 很小時，$\frac{k}{d}$ 會成為 $\frac en$ 的一個非常好的有理數近似，而 continued fraction 正好可以把這種近似找出來。這就是 Wiener attack 的核心。