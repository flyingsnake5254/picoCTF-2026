## 題目

https://learn.cylabacademy.org/library/740?page=1&category=5

After logging in, you will find multiple file parts in your home directory. These parts need to be combined and extracted to reveal the flag.


## 解題

>[解壓縮](../../筆記/解壓縮.md)

Launch Instance 後：

SSH to `dolphin-cove.picoctf.net`:`53534` and login as `ctf-player` with password `fa005713`.

他說要使用 SSH 連線到 `dolphin-cove.picoctf.net`:`53534` ，故先使用 ssh 進行連線：

```bash
ssh -p 53534 ctf-player@dolphin-cove.picoctf.net
```

接著輸入 `yes` 和密碼 `fa005713` 即可連線。

然後輸入 `ls` 查看目前檔案有哪些：

```bash
ls
```

```bash
instructions.txt  part.zip  part_aa  part_ab  part_ac  part_ad	part_ae
```

先看 `instructions.txt` 內容：

```bash
cat instructions.txt
```

```bash
Hint:

- The flag is split into multiple parts as a zipped file.
- Use Linux commands to combine the parts into one file.
- The zip file is password protected. Use this "supersecret" password to extract the zip file.
- After unzipping, check the extracted text file for the flag.
```

他說要先合併所有 zip file，再輸入密碼 `supersecret` 進行解壓縮 

故先進行合併，合併後的 zip 檔案名稱為 `part.zip`：

```bash
cat part* > part.zip
```

接下來進行 `unzip`：

```bash
unzip part.zip
```

然後輸入密碼 `supersecret` ，最後拿到 `flag.txt` ，然後輸出 `flag.txt` 內容，即可取得 flag :  

```bash
cat flag.txt
```

```bash
picoCTF{z1p_and_spl1t_f1l3s_4r3_fun_8fa833a5}
```

