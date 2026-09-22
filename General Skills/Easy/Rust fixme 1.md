## 題目

https://learn.cylabacademy.org/library/461?page=2&category=5

Have you heard of Rust? Fix the syntax errors in this Rust file to print the flag!

Download the Rust code [here](https://challenge-files.picoctf.net/c_verbal_sleep/3f0e13f541928f420d9c8c96b06d4dbf7b2fa18b15adbd457108e8c80a1f5883/fixme1.tar.gz).


## 解題

先解壓縮：

```bash
tar -xzf fixme1.tar.gz
```

得到：

```css
fixme1
├── Cargo.lock
├── Cargo.toml
└── src
    └── main.rs
```

這題 `main.rs` 有三個刻意留下的語法錯誤：

1. 陳述式結尾缺少 `;`
2. `ret` 應改成 `return`
3. `println!` 的格式化符號應使用 `{}`

修改後的完整程式：

```rust
use xor_cryptor::XORCryptor;

fn main() {
    // Rust 陳述式通常以分號結尾
    let key = String::from("CSUCKS");

    let hex_values = [
        "41", "30", "20", "63", "4a", "45", "54", "76",
        "01", "1c", "7e", "59", "63", "e1", "61", "25",
        "7f", "5a", "60", "50", "11", "38", "1f", "3a",
        "60", "e9", "62", "20", "0c", "e6", "50", "d3",
        "35",
    ];

    let encrypted_buffer: Vec<u8> = hex_values
        .iter()
        .map(|&hex| u8::from_str_radix(hex, 16).unwrap())
        .collect();

    let res = XORCryptor::new(&key);

    if res.is_err() {
        return;
    }

    let xrc = res.unwrap();
    let decrypted_buffer = xrc.decrypt_vec(encrypted_buffer);

    println!(
        "{}",
        String::from_utf8_lossy(&decrypted_buffer)
    );
}
```

運行專案：

```bash
cargo run
```

輸出：

```bash
Compiling rust_proj v0.1.0 (/home/kali/Desktop/fixme1)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.16s
     Running `target/debug/rust_proj`
picoCTF{4r3_y0u_4_ru$t4c30n_n0w?}
```