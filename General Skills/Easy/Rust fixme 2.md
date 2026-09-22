## 題目

https://learn.cylabacademy.org/library/462?page=2&category=5

The Rust saga continues? I ask you, can I borrow that, pleeeeeaaaasseeeee?

Download the Rust code [here](https://challenge-files.picoctf.net/c_verbal_sleep/babfbee79718a6363826ba86300173ffde6d81577e9dd07d4130c53a7eecf6c3/fixme2.tar.gz).


## 解題

先解壓縮：

```bash
tar -xzf fixme2.tar.gz
```

得到：

```css
fixme2
├── Cargo.lock
├── Cargo.toml
├── src
    └── main.rs
```

需要修改三個地方：

```rust
// 1. 接收可變參考
fn decrypt(encrypted_buffer: Vec<u8>, borrowed_string: &mut String)
```

```rust
// 2. 變數本身宣告為可變
let mut party_foul = String::from(...);
```

```rust
// 3. 傳入可變參考
decrypt(encrypted_buffer, &mut party_foul);
```

完整修改如下：

```rust
use xor_cryptor::XORCryptor;

fn decrypt(
    encrypted_buffer: Vec<u8>,
    borrowed_string: &mut String,
) {
    let key = String::from("CSUCKS");

    borrowed_string.push_str(
        "PARTY FOUL! Here is your flag: "
    );

    let res = XORCryptor::new(&key);

    if res.is_err() {
        return;
    }

    let xrc = res.unwrap();

    let decrypted_buffer =
        xrc.decrypt_vec(encrypted_buffer);

    borrowed_string.push_str(
        &String::from_utf8_lossy(&decrypted_buffer)
    );

    println!("{}", borrowed_string);
}

fn main() {
    let hex_values = [
        "41", "30", "20", "63", "4a", "45", "54", "76",
        "01", "1c", "7e", "59", "63", "e1", "61", "25",
        "0d", "c4", "60", "f2", "12", "a0", "18", "03",
        "51", "03", "36", "05", "0e", "f9", "42", "5b",
    ];

    let encrypted_buffer: Vec<u8> = hex_values
        .iter()
        .map(|&hex| u8::from_str_radix(hex, 16).unwrap())
        .collect();

    // 變數允許被修改
    let mut party_foul =
        String::from("Using memory unsafe languages is a: ");

    // 傳入可變參考
    decrypt(encrypted_buffer, &mut party_foul);
}
```

運行專案：

```bash
cargo run
```

輸出：

```bash
	Compiling rust_proj v0.1.0 (/home/kali/Desktop/fixme2)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.27s
     Running `target/debug/rust_proj`
Using memory unsafe languages is a: PARTY FOUL! Here is your flag: picoCTF{4r3_y0u_h4v1n5_fun_y31?}
```