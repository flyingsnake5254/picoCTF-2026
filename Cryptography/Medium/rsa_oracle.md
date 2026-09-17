## 題目

https://learn.cylabacademy.org/library/422?page=2&category=2

Can you abuse the oracle?

An attacker was able to intercept communications between a bank and a fintech company. They managed to get the [message](https://artifacts.picoctf.net/c_titan/34/secret.enc) (ciphertext) and the [password](https://artifacts.picoctf.net/c_titan/34/password.enc) that was used to encrypt the message.


## 附檔

**password.enc**

```text
873224563026311790736191809393138825971072101706285228102516279725246082824238887755080848591049817640245481028953722926586046994669540835757705139131212
```

**secret.enc**

```text
Salted__W6+N7���$+���ֱ�zP=��5I�A]	�����������D�KaϬa+A�
```


## 提示

- **Hint 1：** Crytography Threat models: chosen plaintext attack.
- **Hint 2：**  OpenSSL can be used to decrypt the message. e.g `openssl enc -aes-256-cbc -d ...`
- **Hint 3：** The key to getting the flag is by sending a custom message to the server by taking advantage of the RSA encryption algorithm.
- **Hint 4：** Minimum requirements for a useful cryptosystem is CPA security.


## 解題

大致步驟如下：
- **1. 連線至伺服器後，利用選擇明文攻擊破解 password.enc**
- **2. 利用 openssl aes 解密 secret.enc**

首先連線至 server 後，利用以下原理，發動選擇明文攻擊：

**Step 1 : 輸入一明文至 server**
向 server 輸入 `a(0x61)` ，得到密文：`1894792376935242028465556366618011019548511575881945413668351305441716829547731248120542989065588556431978903597240454296152579184569578379625520200356186`  

即：
$$C_a=M_a^e \mod n$$

**Step 2 : 計算欲解之密文，與剛剛得到的密文之乘積**
假設 password.enc 裡的密文為 $C$，及其明文為 $M$，則計算以下：
$$C \cdot C_a \equiv (M \cdot M_a)^e \mod n$$
$C \cdot C_a$ 之結果為：
`1654579245374863380116520579044322357142195197975098865920568334219864905213447287275954994939383811608813422021215575930875353985873254535434835456586499020739740956904167950930371925999309739633810358681845376711949047859870374458686957738610844765998834500752829202477245578132025141560925519168989877432`

**Step 3 : 將乘積丟到 server 進行解密**
```bash
what should we do for you? 
E --> encrypt D --> decrypt. 
D
Enter text to decrypt: 1654579245374863380116520579044322357142195197975098865920568334219864905213447287275954994939383811608813422021215575930875353985873254535434835456586499020739740956904167950930371925999309739633810358681845376711949047859870374458686957738610844765998834500752829202477245578132025141560925519168989877432
decrypted ciphertext as hex (c ^ d mod n): 15ac17f82853
decrypted ciphertext: ¬ø(S
```

得到 $M \cdot M_a$ 為 `15ac17f82853`

**Step 4 : 計算出明文 M**
計算 0x15ac17f82853 / 0x61 即可得到 $M$：

```python
m1 = 0x61
m2 = 0x15ac17f82853
print(bytes.fromhex(hex(m2//m1).replace('0x', '')))
```

輸出：b'92d53'

故 92d53 就是 password


**Step 5 : 利用 openssl 進行 AES 解密 secret.enc**

```bash
openssl enc -d -aes-256-cbc -in secret.enc -out flag.txt
```

然後 password 輸入 92d53

最後輸出 flag.txt :  
picoCTF{su((3ss_(r@ck1ng_r3@_92d53250}