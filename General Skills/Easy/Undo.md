## 題目

https://learn.cylabacademy.org/library/766?page=1&category=5

Can you reverse a series of Linux text transformations to recover the original flag?


## 解題

Launch Instance 後，使用 `nc` 連線：

```bash
nc foggy-cliff.picoctf.net 65522
```

```bash
===Welcome to the Text Transformations Challenge!===

Your goal: step by step, recover the original flag.
At each step, you'll see the transformed flag and a hint.
Enter the correct Linux command to reverse the last transformation.

--- Step 1 ---
Current flag: KTUzbjg4MDI3LWZhMDFnQHplMHNmYTRlRy1nazNnLXRhMWZlcmlyRShTR1BicHZj
Hint: Base64 encoded the string.
Enter the Linux command to reverse it:
```

輸入：`base64 -d`

```bash
--- Step 2 ---
Current flag: )53n88027-fa01g@ze0sfa4eG-gk3g-ta1ferirE(SGPbpvc
Hint: Reversed the text.
Enter the Linux command to reverse it:
```

輸入：`rev`

```bash
--- Step 3 ---
Current flag: cvpbPGS(Eriref1at-g3kg-Ge4afs0ez@g10af-72088n35)
Hint: Replaced underscores with dashes.
Enter the Linux command to reverse it:
```

輸入：`tr '-' '_'`


```bash
--- Step 4 ---
Current flag: cvpbPGS(Eriref1at_g3kg_Ge4afs0ez@g10af_72088n35)
Hint: Replaced curly braces with parentheses.
Enter the Linux command to reverse it:
```

輸入：`tr '()' '{}'`

```bash
--- Step 5 ---
Current flag: cvpbPGS{Eriref1at_g3kg_Ge4afs0ez@g10af_72088n35}
Hint: Applied ROT13 to letters.
Enter the Linux command to reverse it:
```

輸入：`tr 'A-Za-z' 'N-ZA-Mn-za-m'`

```bash
ongratulations! You've recovered the original flag:
>>> picoCTF{Revers1ng_t3xt_Tr4nsf0rm@t10ns_72088a35}
```

