## 題目

https://learn.cylabacademy.org/library/523?page=1&category=4

The SOC team discovered a suspiciously large log file after a recent breach. When they opened it, they found an enormous block of encoded text instead of typical logs. Could there be something hidden within? Your mission is to inspect the resulting file and reveal the real purpose of it. The team is relying on your skills to uncover any concealed information within this unusual log.

Download the encoded data here: [Logs Data](https://challenge-files.picoctf.net/c_amiable_citadel/929daf6ef01bba32b165e0a7c649ff4c953f2af21c28b024e8af5276b7716de5/logs.txt). Be prepared—the file is large, and examining it thoroughly is crucial .


## 解題

先使用 `base64` 解碼 `logs.txt`：

```bash
base64 -d logs.txt > output.txt
```

使用 `head` 輸出 `output.txt` 前三行：

```bash
head -n 3 output.txt
```

```bash
�PNG

IHDR�!�IDATx�����,ɕ'���q����������Fczzg�fi\�h���O4���F�m�fC[��
                                                              ��3��J��՟�̈p�s�p""=է��jq��w3##<\-���/HA(�*�P�
                                                                                                           "0T5j"u�9"UE"f&�""TՏ���L��89;y��YU2Q�����CR��fm]�u���������|>o���7�������Ÿ�B��_����'
```


看到 「PNG」，故將原本 `output.txt` 改成 `output.png` 檔案，得到圖片：

![](../../assets/images/Forensics/Easy/Flag%20in%20Flame1.png)


可以看到圖片中有一串編碼，將其轉成 bytes 後，並 decode 即可得到 flag :  
```python
s = '7069636F4354467B666F72656E736963735F616E616C797369735F69735F616D617A696E675F65633139383466637D'
print(bytes.fromhex(s).decode('utf-8'))
```

輸出：  
```bash
picoCTF{forensics_analysis_is_amazing_ec1984fc}
```


