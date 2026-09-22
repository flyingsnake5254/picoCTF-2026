- [文字擋](#文字擋)
  - [讀取](#讀取)
  - [寫入](#寫入)
- [二進位檔案](#二進位檔案)
  - [讀取](#讀取-1)
  - [寫入](#寫入-1)




|模式|用途|
|---|---|
|`"r"` / `"w"`|讀／寫文字|
|`"a"`|追加文字|
|`"rb"` / `"wb"`|讀／寫原始 bytes|

## 文字擋

### 讀取

```python
with open("input.txt", "r", encoding="utf-8") as f:
    content = f.read()

print(content)
```

逐行讀取

```python
with open("input.txt", "r", encoding="utf-8") as f:
    for line in f:
        print(line.strip())
```


### 寫入

```python
with open("output.txt", "w", encoding="utf-8") as f:
    f.write("Hello\n")
    f.write("World\n")
```

`"w"` 會**覆蓋原有內容**；若要加在檔案末尾，改用 `"a"`



## 二進位檔案

### 讀取

```python
with open("input.bin", "rb") as f:
    data = f.read()          # 得到 bytes
```


### 寫入

```python
with open("output.bin", "wb") as f:
    f.write(data)
```

