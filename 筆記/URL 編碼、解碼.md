
## 編碼

```python
from urllib.parse import quote

url = "https://example.com"
encoded_url = quote(url, safe=":/?=")  # safe 參數用來保留不需編碼的特殊符號
print(encoded_url)
# 輸出: https%3A//://example.com
```


## 解碼

```python
from urllib.parse import unquote

encoded_str = "%E6%B8%AC%E8%A9%A6"
decoded_str = unquote(encoded_str)
## 編碼

```python
from urllib.parse import quote

url = "https://example.com"
encoded_url = quote(url, safe=":/?=")  # safe 參數用來保留不需編碼的特殊符號
print(encoded_url)
# 輸出: https%3A//://example.com
```


## 解碼

```python
from urllib.parse import unquote

encoded_str = "%E6%B8%AC%E8%A9%A6"
decoded_str = unquote(encoded_str)
print(decoded_str)
# 輸出: 測試
```
print(decoded_str)
# 輸出: 測試
```