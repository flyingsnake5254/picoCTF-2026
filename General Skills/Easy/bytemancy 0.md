## 題目

https://learn.cylabacademy.org/library/742?page=1&category=5

Can you conjure the right bytes? The program's source code can be downloaded [here](https://challenge-files.picoctf.net/c_candy_mountain/81b07aa870948333a313fa7a1a9dd09b5c159d161ed45d0e14c00b0ed77321cb/app.py).


## 附檔

**app.py**

```python
while(True):
  try:
    print('⊹──────[ BYTEMANCY-0 ]──────⊹')
    print("☍⟐☉⟊☽☈⟁⧋⟡☍⟐☉⟊☽☈⟁⧋⟡☍⟐☉⟊☽☈⟁⧋⟡☍⟐")
    print()
    print('Send me ASCII DECIMAL 101, 101, 101, side-by-side, no space.')
    print()
    print("☍⟐☉⟊☽☈⟁⧋⟡☍⟐☉⟊☽☈⟁⧋⟡☍⟐☉⟊☽☈⟁⧋⟡☍⟐")
    print('⊹─────────────⟡─────────────⊹')
    user_input = input('==> ')
    if user_input == "\x65\x65\x65":
      print(open("./flag.txt", "r").read())
      break
    else:
      print("That wasn't it. I got: " + str(user_input))
      print()
      print()
      print()
  except Exception as e:
    print(e)
    break
```


## 解題

從附檔可看到：

```python
Send me ASCII DECIMAL 101, 101, 101, side-by-side, no space.
```

故使用 python 輸出 ASCII = 101 的字元：

```python
print(chr(101), chr(101), chr(101), sep='')
```

輸出：

```python
eee
```

接下來 Launch Instance 後，使用 `nc` 連線：

```bash
nc candy-mountain.picoctf.net 55162
```

```bash
⊹──────[ BYTEMANCY-0 ]──────⊹
☍⟐☉⟊☽☈⟁⧋⟡☍⟐☉⟊☽☈⟁⧋⟡☍⟐☉⟊☽☈⟁⧋⟡☍⟐

Send me ASCII DECIMAL 101, 101, 101, side-by-side, no space.

☍⟐☉⟊☽☈⟁⧋⟡☍⟐☉⟊☽☈⟁⧋⟡☍⟐☉⟊☽☈⟁⧋⟡☍⟐
⊹─────────────⟡─────────────⊹
==>
```

接下來輸入 `eee` 即可取得 flag :  

```bash
picoCTF{pr1n74813_ch4r5_184029cd}
```