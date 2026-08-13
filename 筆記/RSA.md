
### <span style="color: #42a5f5; font-weight: bold;">定義</span>

一種非對稱式加密演算法，使用公開金鑰（Public Key）加密，私密金鑰（Private Key）解密

---
### <span style="color: #42a5f5; font-weight: bold;">原理</span>

RSA 建立在兩個數學難題上：
- **大數質因數分解困難**
	  已知 $n=p\times q$，很難從 $n$ 反推出 $p、q（當\,\,p、q\,\,很大時）$
	  
- **模指數與歐拉定理**
  $M^{ed}\equiv M\,\,\,(mod\,\,\,n)$
  加密再解密會回到原文

---
### <span style="color: #42a5f5; font-weight: bold;">金鑰產生流程</span>

- **Step 1 : 選兩個大質數 $p, q$**
  $p, q$

- **Step 2 : 計算模數 $n$**
  $n = p\times q$

- **Step 3 : 計算歐拉函數$\phi (n)$**
  $\phi (n)=(p-1)(q-1)$

- **Step 4 : 選擇公鑰指數 $e$**
  條件：
	- $1 < e < \phi (n)$
	- $gcd(e, \phi (n))=1$    ......$e$ 是質數
	常見 $e=65537$

- **Step 5 : 計算私鑰 $d$**
  $d\equiv e^{-1}\,\,\,mod\,\,\,\phi (n)$
  也就是
  $d\cdot e\equiv 1\,\,\,mod\,\,\,\phi (n)$
  

<span style="color: #ff758f; font-weight: bold;">最終金鑰</span>
- 公鑰：$(e, n)$
- 私鑰：$(d, n)$

---
### <span style="color: #42a5f5; font-weight: bold;">加解密</span>

  $M：明文$
  $C：密文$
  
- **加密**
  $C=M^e\,\,\,mod\,\,\,n$
  
- **解密**
  $M=C^d\,\,\,mod\,\,\,n$

---
### <span style="color: #42a5f5; font-weight: bold;">適用場景</span>

- **金鑰交換：** RSA 用來加密 AES key
- **HTTPS / TLS：** 用於憑證交換、金鑰交換
- **數位簽章：** 驗證身份、完整性
- **Email 加密：** 如 PGP

---
### <span style="color: #42a5f5; font-weight: bold;">攻擊方式</span>

- **因數分解攻擊：** 
  破解 $n$ → 找到 $p, q$
  可使用 General Number Field Sieve (GNFS)

- **小指數攻擊：**
  若 $e$ 太小，且為使用 padding，則容易被破解

- **中間人攻擊：**
  偽造公鑰

- **旁通道攻擊：**
  如時序攻擊、電力分析攻擊

---
### <span style="color: #70e000; font-weight: bold;">優點</span>

- **安全性高：** 安全性基於數學難題
- **不須事先共享密鑰** 
- **可作加密、數位簽章、金鑰交換**

---
### <span style="color: #e50000; font-weight: bold;">缺點</span>

- 計算成本高
- 金鑰長度需求大（2048+ bits）
- 實做較難
- 不抗量子電腦

---
### <span style="color: #42a5f5; font-weight: bold;">範例</span>

#### 金鑰產生

- **Step 1 : 選兩個質數 $p, q$**
  $p=5,\,\,\,\,\,q=11$
  
- **Step 2 : 計算 $n$**
  $n=p\times q=5\times 11=55$
  
- Step 3 : **計算 $\phi (n)$**
  $\phi (n) = (p-1)(q-1)=4\times 10=40$

- **Step 4 : 選擇 $e$**
  條件：
	- $1 < e < \phi (n) = 40$
	- $gcd(e, \phi (n))=1$  ... $e$ 是質數
	選擇 $e=3$

- **Step 5 : 計算 $d$**
  $d \cdot e \,\,\, mod \,\,\, \phi (n) = 1$
  $3d\,\,\,mod\,\,\,40=1$
  
  也就是
  $3d + 40k=1$
  
  利用$\color{yellow}擴展歐幾里得：$ 
$$
\begin{align}
&40=3\times 13+1\\
&1=40-3\times13\\\\
&\therefore  k=1,\,\,\,d=-13\\\\
&把\,\,\,d\,\,\,轉成正值：\\
&d\,\,\,mod\,\,\,40=(d+40)\,\,\,mod\,\,\,40\\
&d + 40=-13+40=27\\
&\color{yellow} 最終將\,\,\,d\,\,\,定為\,\,\,27
\end{align}
$$
- <span style="color: #00FFFF; font-weight: bold;">最終金鑰</span>
	- 公鑰：$(e,n)=(3,55)$
	- 私鑰：$(d,n)=(27,55)$

> Ref : [模數運算](../筆記/模數運算.md)

---
#### 加密

假設明文 $M=7$
$$
\begin{align}
C&=M^e\,\,\,mod\,\,\,n\\
&=7^3\,\,\,mod\,\,\,55\\
&=\color{yellow}13
\end{align}
$$
---
#### 解密

密文 $C=13$

$$
\begin{align}
M&=C^d\,\,\,mod\,\,\,n\\
&=13^{27}\,\,\,mod\,\,\,55\\
&=7
\end{align}
$$
> Ref : [模數運算](../筆記/模數運算.md)





  