

## install

```bash
sudo apt update
sudo apt install openssl

# 確認是否安裝好
openssl version
```

<hr>

## 基本功能

|類別|功能|常見指令|
|---|---|---|
|1. 對稱式加密|AES 等資料加解密|`openssl enc`|
|2. 非對稱式加密|RSA / ECC 金鑰與加解密|`genpkey`、`pkey`、`pkeyutl`|
|3. Hash / 數位簽章|SHA-256、簽章、驗章|`dgst`|
|4. 憑證 / PKI|CSR、X.509、CA、憑證查看|`req`、`x509`、`ca`|
|5. TLS / SSL|測試 HTTPS/TLS 連線與伺服器|`s_client`、`s_server`|
|6. 編碼與工具|Base64、亂數、格式轉換|`base64`、`rand`|


<hr>

## 基本使用

基本型式：<code>openssl &lt子命令&gt &lt參數&gt</code>


### 查詢

```bash
# 查詢所有命令
openssl (list|help)

# 查詢所有命令
openssl list -command

# 查詢子命令
openssl <子命令> -help

# 查詢支援的所有演算法
openssl list -all-algorithms

# 查詢支援的雜湊演算法
openssl list -digest-algorithms

# 查詢支援的對稱式加密
openssl list -cipher-algorithm
```

<hr class="分隔線4">

### 對稱式加密 - openssl enc

<span class="rec">明文</span><span class="r-arrow">AES + 密鑰</span> <span class="rec">密文</span>

使用指令：<code>enc</code>

#### 查詢資訊

```bash
# 基本資訊
openssl enc -help

# 完整資訊
man openssl-enc
```


#### example

```bash
echo "Hello OpenSSL" > plain.txt
```

使用 AES-256-CBC 加密：
```bash
openssl enc -aes-256-cbc \
  -salt \
  -pbkdf2 \
  -in plain.txt \
  -out cipher.bin
```

解密：
```bash
openssl enc -d -aes-256-cbc \
  -pbkdf2 \
  -in cipher.bin \
  -out decrypted.txt
```


<hr class="分隔線4">

### 非對稱式加密

#### 產生私鑰 / 金鑰對 - genpkey

產生 2048-bit RSA 私鑰：
```bash
openssl genpkey \
  -algorithm RSA \
  -pkeyopt rsa_keygen_bits:2048 \
  -out private.pem
```

private.pem 就是私鑰

產生 EC 私鑰，例如 P-256：
```bash
openssl genpkey \
  -algorithm EC \
  -pkeyopt ec_paramgen_curve:P-256 \
  -out ec_private.pem
```

#### 查看、轉換、提取公私鑰 -pkey

假設剛才有：private.pem

##### 從私鑰取得公鑰
```bash
openssl pkey \
  -in private.pem \
  -pubout \
  -out public.pem
```

此時
```bash
private.pem    RSA 私鑰
public.pem     RSA 公鑰
```

##### 查看私鑰內容
```bash
openssl pkey \
  -in private.pem \
  -text \
  -noout
```

##### 查看公鑰
```bash
openssl pkey \
  -pubin \
  -in public.pem \
  -text \
  -noout
```

##### 驗證私鑰
```bash
openssl pkey \
    -in prik.pem \
    -check \
    -noout
```

#### 使用非對稱式金鑰進行實際運算 - pkeyutl

```css
pkey
→ 管理、查看 key

pkeyutl
→ 用 key 做加密、解密、簽章、驗證等運算
```

假設已經有：
```css
private.pem
public.pem
```

先建立資料：
```bash
echo -n "Secret Message" > message.txt
```

##### RSA 公鑰加密

使用 RSA-OAEP
```bash
openssl pkeyutl \
  -encrypt \
  -pubin \
  -inkey public.pem \
  -in message.txt \
  -out encrypted.bin \
  -pkeyopt rsa_padding_mode:oaep \
  -pkeyopt rsa_oaep_md:sha256
```

##### RSA 私鑰解密
```bash
openssl pkeyutl \
  -decrypt \
  -inkey private.pem \
  -in encrypted.bin \
  -out decrypted.txt \
  -pkeyopt rsa_padding_mode:oaep \
  -pkeyopt rsa_oaep_md:sha256
```


<hr class="分隔線4">

### HASH 數位簽章 - dgst

#### 計算 SHA-256
```bash
openssl dgst -sha256 message.txt
```


#### 使用 RSA 私鑰簽章
```bash
openssl dgst \
  -sha256 \
  -sign private.pem \
  -out signature.bin \
  message.txt
```

得到：signature.bin

#### 使用公鑰驗證
```bash
openssl dgst \
  -sha256 \
  -verify public.pem \
  -signature signature.bin \
  message.txt
```


<hr class="分隔線4">

### 憑證

假設：private.pem

#### CSR - req

##### 建立 CSR

```bash
openssl req \
  -new \
  -key private.pem \
  -out server.csr
```

得到：server.csr

##### 查看 CSR

```bash
openssl req \
  -in server.csr \
  -text \
  -noout
```

#### 查看 / 處理 X.509 憑證 - x509

假設：server.crt

##### 查看完整憑證

```bash
openssl x509 \
  -in server.crt \
  -text \
  -noout
```

##### 只看 Subject

```bash
openssl x509 \
  -in server.crt \
  -subject \
  -noout
```

##### 只看簽發者

```bash
openssl x509 \
  -in server.crt \
  -issuer \
  -noout
```

##### 查看有效期限

```bash
openssl x509 \
  -in server.crt \
  -dates \
  -noout
```

### Base64 編碼 / 解碼 - base64

假設：
```bash
echo -n "Hello" > message.txt
```


#### Base64 編碼

```bash
openssl base64 \
  -in message.txt \
  -out message.b64
```

#### Base64 解碼

```bash
openssl base64 \
  -d \
  -in message.b64 \
  -out decoded.txt
```

