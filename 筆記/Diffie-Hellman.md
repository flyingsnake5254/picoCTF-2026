### <span style="color: #42a5f5; font-weight: bold;"> 定義</span>

Diffie-Hellman 金鑰交換協定是一種「讓雙方在不安全通道上，安全的建立共享密鑰的方法」

*※ 不是用來加密，而是用來「產生共同密鑰」* 

---
### <span style="color: #42a5f5; font-weight: bold;"> 原理</span>

安全性來自離散對數問題的困難：
「已知 $g^a\,\,\,mod\,\,\,p$ ，很難反推出 $a$ （其中） $p、g$ 為公開參數」

---
### <span style="color: #42a5f5; font-weight: bold;"> 流程</span>

- **公開參數：**
	- 質數：$p$
	- 生成元：$g$

- **👤 Alice：**
	- *Step 1 : 選擇祕密參數 $a$*
	- *Step 2 : 計算 $A=g^a\,\,\,mod\,\,\,p$*
	- *Step 3 : 傳送 $A$*
	  
- **👤 Bob：**
	- *Step 1 : 選擇祕密 $b$*
	- *Step 2 : 計算 $B=g^b\,\,\,mod\,\,\,p$*
	- *Step 3 : 傳送 $B$*
  
- **共享密鑰計算：**
	- *👤 Alice：* $K=B^a\,\,\,mod\,\,\,p$
	- *👤 Bob：* $K=A^b\,\,\,mod\,\,\,p$

		兩個式子是相同的（$K=g^{ab}\,\,\,mod\,\,\,p$），故結果相同

**※ 攻擊者即使知道 $p,g,A,B$ ，仍無法算出 $K$**

---
### <span style="color: #42a5f5; font-weight: bold;"> 例子</span>

假設：
- $p=23$
- $g=5$

**Alice：**
- Step 1 : 選擇祕密參數 $a=6$
- Step 2 : 計算 $A=g^a\,\,\,mod\,\,\,p=5^6\,\,\,mod\,\,\,23=8$
- Step 3 : 傳送 $A=8$ 給 Bob

**Bob：**
- Step 1 : 選擇祕密參數 $b=15$
- Step 2 : 計算 $B=g^b\,\,\,mod\,\,\,p=5^15\,\,\,mod\,\,\,23=19$
- Step 3 : 傳送 $B=19$ 給 Alice

**計算共享密鑰：**
- Alice : $K=B^a\,\,\,mod\,\,\,p=19^6\,\,\,mod\,\,\,23=2$
- Bob : $K=A^b\,\,\,mod\,\,\,p=8^15\,\,\,mod\,\,\,23=2$

**成功建立共同密鑰 $K=2$**

---
### <span style="color: #42a5f5; font-weight: bold;"> 常見攻擊</span>

- **中間人攻擊**
  攻擊流程：
	  Alice ↔ 攻擊者 ↔ Bob
  攻擊者假裝是雙方，建立兩條不同密鑰

- **Small Subgroup Attack**
  若群選擇不當，可能導致部分金鑰資訊洩漏
  
- **Logjam Attack**
  利用弱 DH 參數（512-bit），攻擊 TLS
  *解法：使用 ≥ 2048-bit DH，或改用 ECDHE*
  
---
### <span style="color: #70e000; font-weight: bold;"> 優點</span>

- 不須事先共享密鑰
- 安全性高
- 可支援 Perfect Forward Secrecy (PFS)
- 廣泛應用

---
### <span style="color: #e50000; font-weight: bold;"> 缺點</span>

- **無身份驗證：** 無法確認對方是誰，容易被 MITM 攻擊
- **計算成本高：** 大數運算，計算成本大
- **需要安全參數：** 若 $p,g$ 選不好，可能被破解
- **傳統 DH key size 很大：** 比 RSA / ECC 更重（現代多用 ECDH）
  