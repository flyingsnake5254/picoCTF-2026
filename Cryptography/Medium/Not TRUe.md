https://learn.cylabacademy.org/library/711?page=2&category=2

## 題目

no. no. that's Not TRUe. that's impossible!

Download [encryption script](https://challenge-files.picoctf.net/c_plain_mesa/888cf71c088b236ee86eea3055cae9e3be35821e1677d8d8083e80c1ded4948c/encrypt.py) and [public info](https://challenge-files.picoctf.net/c_plain_mesa/888cf71c088b236ee86eea3055cae9e3be35821e1677d8d8083e80c1ded4948c/public.txt).

## 附檔

**encrypt.py**

```python
from random import randint
from sage.all import *

N = 48
p = 3
q = 509

R = PolynomialRing(ZZ, 'x')
x = R.gen()
R_modq = PolynomialRing(Integers(q), 'x').quotient(x**N - 1, 'xbar')
R_modp = PolynomialRing(Integers(p), 'x').quotient(x**N - 1, 'xbar')

def gen_poly():
    return R([randint(-1,1) for _ in range(N)])

def gen_msg(text):
    binary_str = ''.join(format(ord(char), '08b') for char in text)
    
    padding_length = (N - (len(binary_str) % N)) % N
    binary_str += '0' * padding_length
    
    chunks = [binary_str[i:i+N] for i in range(0, len(binary_str), N)]
    
    polynomials = [
        R([int(bit) for bit in chunk])
        for chunk in chunks
    ]
    
    return polynomials

def encrypt(h, m): 
    r = gen_poly()
    return R_modq(p*(h*r) + m)

def generate_keys():
    while True:
        # Random ternary polynomials f and g
        f = gen_poly()
        g = gen_poly()
        
        # Check if f is invertible modulo p and q
        try:
            f_p_inv = R_modp(f)**-1
            f_q_inv = R_modq(f)**-1
            break
        except:
            continue

    h = R_modq(p*(f_q_inv*g))
    
    private_key = (f, g, f_p_inv, f_q_inv)
    public_key = h
    return public_key, private_key

with open("flag.txt", "r") as f:
    flag = f.read().strip()

public_key, private_key = generate_keys()
print(f"h = {public_key.list()}")

ciphertext = []
encoded = gen_msg(flag)
for part in encoded:
    ciphertext.append(encrypt(public_key, part))
ct = [c.list() for c in ciphertext]
print(f"ct = {ct}")

with open("public.txt", "w") as f:
    f.write(f"N = {N}\n")
    f.write(f"p = {p}\n")
    f.write(f"q = {q}\n")
    f.write(f"h = {public_key.list()}\n")
    f.write(f"ct = {ct}\n")
```

**public.txt**

```css
N = 48
p = 3
q = 509
h = [225, 178, 235, 46, 157, 102, 304, 255, 79, 297, 230, 142, 351, 40, 386, 82, 201, 122, 107, 327, 246, 347, 119, 265, 285, 230, 449, 340, 207, 119, 483, 20, 391, 217, 80, 242, 350, 141, 206, 52, 333, 155, 164, 54, 277, 379, 421, 113]
ct = [[142, 59, 272, 73, 92, 356, 402, 68, 147, 292, 80, 107, 435, 384, 99, 486, 51, 433, 462, 493, 204, 89, 406, 289, 111, 340, 97, 1, 279, 174, 494, 106, 173, 419, 486, 484, 162, 293, 261, 462, 284, 28, 353, 76, 173, 152, 338, 29], [500, 78, 282, 100, 324, 57, 49, 161, 30, 498, 90, 208, 192, 171, 215, 269, 13, 83, 494, 437, 63, 14, 264, 284, 278, 428, 405, 424, 98, 384, 247, 184, 179, 487, 3, 424, 243, 168, 278, 406, 77, 352, 451, 256, 446, 391, 12, 172], [189, 473, 244, 424, 350, 451, 73, 504, 246, 336, 196, 64, 128, 362, 452, 28, 277, 332, 457, 182, 246, 327, 428, 340, 498, 247, 381, 377, 242, 90, 375, 248, 446, 266, 293, 222, 498, 258, 132, 389, 250, 452, 394, 411, 250, 477, 137, 347], [314, 474, 281, 230, 60, 179, 464, 376, 375, 350, 495, 41, 198, 197, 257, 58, 67, 497, 129, 371, 431, 181, 5, 335, 105, 298, 69, 352, 226, 96, 439, 321, 274, 260, 441, 156, 240, 363, 398, 241, 216, 506, 136, 174, 132, 368, 62, 285], [72, 187, 465, 127, 218, 200, 314, 212, 204, 366, 415, 8, 177, 99, 72, 58, 342, 38, 420, 302, 373, 65, 27, 203, 455, 104, 251, 485, 147, 440, 154, 162, 275, 239, 21, 115, 163, 311, 317, 429, 156, 396, 469, 400, 132, 429, 296, 233], [415, 375, 371, 463, 337, 151, 140, 81, 370, 486, 165, 490, 28, 319, 126, 394, 146, 283, 331, 153, 144, 380, 232, 484, 95, 113, 323, 344, 341, 173, 169, 447, 120, 235, 400, 73, 365, 296, 125, 360, 56, 416, 384, 163, 322, 304, 196, 250]]
```


## 解題

>[NTRU](../../筆記/NTRU.md)


### 運行程式

本題要跑 `encrypt.py` ，需要在 conda 裝 sage 環境：

- **若已有 conda 環境，輸入以下建置 sage**

```bash
conda install -c conda-forge sage
```

- **若尚未創建 conda 環境，輸入以下，創建環境**

```bash
conda create -n sage conda-forge sage python=3.14
```



### 解題步驟

從 `public.txt` 可看到 $N$ 很小，故使用 LLL 攻擊。
簡要流程：

**1. 建立 NTRU Basic Matrix**
$$
B=
\begin{pmatrix}
I_N, H\\
O, qI_N
\end{pmatrix}
$$
其中：
- $H$ : 為 $h$ 的循環矩陣
- $I_N$ ：為 size = N 的單位矩陣
- $O$ ：為 $N\times N$ 的零矩陣

**2. LLL 攻擊**
$$B.LLL()$$
**3. 在 LLL 結果中，找私鑰 f**
	$f$ 位於 LLL 結果中的前半段（後半段是 $pg$，需驗證 $f$ 的所有係數皆 >= -1 and <=1 and 不全為 0

**4. 找到 f 後，進行解密**
$$
\begin{aligned}
a=fc\pmod q\\
m=f_p^{-1}a\pmod p
\end{aligned}
$$
	$m$ 的結果為 bit list，將其每 8 bit 一組，轉成十進位後，再轉成 ASCII 字元


### 完整程式

```python
from sage.all import *

N = 48
p = 3
q = 509
h = [225, 178, 235, 46, 157, 102, 304, 255, 79, 297, 230, 142, 351, 40, 386, 82, 201, 122, 107, 327, 246, 347, 119, 265, 285, 230, 449, 340, 207, 119, 483, 20, 391, 217, 80, 242, 350, 141, 206, 52, 333, 155, 164, 54, 277, 379, 421, 113]
ct = [[142, 59, 272, 73, 92, 356, 402, 68, 147, 292, 80, 107, 435, 384, 99, 486, 51, 433, 462, 493, 204, 89, 406, 289, 111, 340, 97, 1, 279, 174, 494, 106, 173, 419, 486, 484, 162, 293, 261, 462, 284, 28, 353, 76, 173, 152, 338, 29], [500, 78, 282, 100, 324, 57, 49, 161, 30, 498, 90, 208, 192, 171, 215, 269, 13, 83, 494, 437, 63, 14, 264, 284, 278, 428, 405, 424, 98, 384, 247, 184, 179, 487, 3, 424, 243, 168, 278, 406, 77, 352, 451, 256, 446, 391, 12, 172], [189, 473, 244, 424, 350, 451, 73, 504, 246, 336, 196, 64, 128, 362, 452, 28, 277, 332, 457, 182, 246, 327, 428, 340, 498, 247, 381, 377, 242, 90, 375, 248, 446, 266, 293, 222, 498, 258, 132, 389, 250, 452, 394, 411, 250, 477, 137, 347], [314, 474, 281, 230, 60, 179, 464, 376, 375, 350, 495, 41, 198, 197, 257, 58, 67, 497, 129, 371, 431, 181, 5, 335, 105, 298, 69, 352, 226, 96, 439, 321, 274, 260, 441, 156, 240, 363, 398, 241, 216, 506, 136, 174, 132, 368, 62, 285], [72, 187, 465, 127, 218, 200, 314, 212, 204, 366, 415, 8, 177, 99, 72, 58, 342, 38, 420, 302, 373, 65, 27, 203, 455, 104, 251, 485, 147, 440, 154, 162, 275, 239, 21, 115, 163, 311, 317, 429, 156, 396, 469, 400, 132, 429, 296, 233], [415, 375, 371, 463, 337, 151, 140, 81, 370, 486, 165, 490, 28, 319, 126, 394, 146, 283, 331, 153, 144, 380, 232, 484, 95, 113, 323, 344, 341, 173, 169, 447, 120, 235, 400, 73, 365, 296, 125, 360, 56, 416, 384, 163, 322, 304, 196, 250]]


R = PolynomialRing(ZZ, 'x')
x = R.gen()
R_modq = PolynomialRing(Integers(q), 'x').quotient(x**N - 1, 'xbar')
R_modp = PolynomialRing(Integers(p), 'x').quotient(x**N - 1, 'xbar')


def center_lift(poly , 模數):
    poly_係數 = poly.list()
    center_lift_係數 = []
    for 係數 in poly_係數:
        if 係數 > 模數 // 2:
            # 下方務必加上 int() ，因為 lsit() 取出的係數，不是一般整數，而是環元素係數
            center_lift_係數.append(int(係數) - 模數)
        else:
            center_lift_係數.append(int(係數))
    return R(center_lift_係數)

# Step 1 : 建立 NTRU 基底矩陣 B
'''
基底矩陣 B = [
    [I(N) , H]
    [Z(N*N) , q*I(N)]
]

其中：
- H 為 h 的循環矩陣
- I 為單位矩陣
- Z 為零矩陣
'''


# Step 1.1 : 建立 h 的循環矩陣 H
h_vec = vector(ZZ, h)
H = matrix.circulant(h_vec)

# Step 1.2 : 建立單位矩陣
I = identity_matrix(ZZ, N)

# Step 1.3 : 建立零矩陣
Z = zero_matrix(ZZ, N, N)

# Step 1.4 : 建立 NTRU 格的基底矩陣
B = block_matrix([
    [I, H],
    [Z, q*I]
])

# Step 2 : LLL 攻擊
B_LLL_result = B.LLL()


# Step 3 : 找私鑰
for result in B_LLL_result:
    # result 前半段為私鑰，後半段為 pg
    f_候選 = list(result[:N])

    # 檢查 f 係數是否介於 -1 ~ 1 且不全為 0
    if all(abs(_) <= 1 for _ in f_候選) and any(_ != 0 for _ in f_候選):
        
        # Step 4 : 解密
        f_poly_modp = R_modp(f_候選)
        f_poly_modq = R_modq(f_候選)

        print('私鑰 f：', R(f_候選))

        # f 在模 p 下的反元素
        f_p_inv = f_poly_modp ** (-1)

        print('明文 m : ', end='')
        for c in ct:
            # a = fc (mod q , x**N - 1)
            a = R_modq(f_poly_modq * R_modq(c))

            # 進行 center lift
            a_center_lift = center_lift(a, q)

            # 計算 m = a * f_p^{-1} (mod p)
            m = R_modp(a_center_lift * f_p_inv).list()

            # 將 m 多項式環係數，轉成一般 int，並且若長度不足 N，補 0 於末端
            m_bit_list = [int(_) for _ in m]
            m_bit_list += [0] * (N - len(m_bit_list))

            # 將 list 中的 bit 組合成 bit string
            m_bit_string = ''
            for _ in m_bit_list:
                m_bit_string += str(_)

            # 將 bit string 每 8 bits 一組，轉成十進位後，輸出 ASCII 字元
            for i in range(0, len(m_bit_string), 8):
                bit_string = m_bit_string[i:i+8]
                print(chr(int(bit_string, 2)), end='')
        print('\n--------------------------')
```