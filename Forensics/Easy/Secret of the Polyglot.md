## 題目

https://learn.cylabacademy.org/library/423?page=1&category=4

The Network Operations Center (NOC) of your local institution picked up a suspicious file, they're getting conflicting information on what type of file it is. They've brought you in as an external expert to examine the file. Can you extract all the information from this strange file?

Download the suspicious file [here](https://artifacts.picoctf.net/c_titan/99/flag2of2-final.pdf).


## 解題

>[探查面向](../../筆記/圖片探查面向.md)  

附檔 `flag2of2-final.pdf` 打開後，可以看到：

![](../../assets/images/Forensics/Easy/Secret%20of%20the%20Polyglot1.png)

接下來使用 `file` 探查，發現其實是 PNG 圖片：

```bash
file flag2of2-final.pdf
```

```bash
flag2of2-final.pdf: PNG image data, 50 x 50, 8-bit/color RGBA, non-interlaced
```

將 `flag2of2-final.pdf` 的副檔名改成 `.png`，即可看到：

![](../../assets/images/Forensics/Easy/Secret%20of%20the%20Polyglot2.png)

故 flag 為：  
`picoCTF{f1u3n7_1n_pn9_&_pdf_2a6a1ea8}`

