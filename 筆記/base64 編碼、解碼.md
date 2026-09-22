
## 編碼

```python
import base64

text = "Hello, 世界"

encoded_bytes = base64.b64encode(text.encode("utf-8"))
encoded_text = encoded_bytes.decode("ascii")

print(encoded_text)
```

## 解碼

```python
import base64

encoded = "SGVsbG8sIOS4lueVjA=="

decoded_bytes = base64.b64decode(encoded)
decoded_text = decoded_bytes.decode("utf-8")

print(decoded_text)
```