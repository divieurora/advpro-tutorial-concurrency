# Advance Programming - Module 6 Reflection

Reflections List:
- [Reflection 1](#reflection-(1))
- [Reflection 2](#reflection-(2))

<hr>

### Reflection (1)

What is inside the handle connection method?

Pada method `main()` sebelumnya, tidak ada proses membaca pesan response dan hanya memberi tahu bahwa koneksi berhasil terhubung. Sedangkan pada method `handle_connection`, terdapat bagian:

```rust
let buf_reader = BufReader::new(&stream); 
```

`BufReader` berfungsi untuk me-wrap `TcpStream` sehingga pesan response dapat dibaca dengan mudah dan cepat. 

Selain itu terdapat juga bagian

```rust
let http_request: Vec<_> = buf_reader 
    .lines() 
    .map(|result| result.unwrap())
    .take_while(|line| !line.is_empty()) 
    .collect();
```

yang berfungdi untuk membaca setiap lines dan memisahkan setiap barisnya, menggunakan `.map` untuk me-wrap `result` menjadi String, melakukan iterasi sampai ditemukan line yang berisi empty string, dan menggabungkan hasilnya menggunakan `collect()` menjadi Vector.

### Reflection (2)

What have I learned about the new code in the handle connection method?

Pada rust documentation, dijelaskan bahwa Content-Length adalah header yang diatur sesuai dengan ukuran file response body yaitu `hello.html`.

```rust
let status_line = "HTTP/1.1 200 OK";
let contents = fs::read_to_string("hello.html").unwrap();
let length = contents.len();

let response = format!("{status_line}\r\nContent-Length:{length}\r\n\r\n{contents}");
stream.write_all(response.as_bytes()).unwrap();
```
Kode tambahan di atas memungkinkan file dibaca dan diubah menjadi string dengan `fs::read_to_string()` yang panjangnya diketahui dengan fungsi `len()`. Begitu pula ketika gagal dibaca, maka `unwrap()` akan menghentikan jalannya program.Dengan Content-Length ketika dilakukan run, maka hello.html akan dirender dan dikirimkan sebagai response.

Berikut adalah hasil run program dengan html:
![](/assets/images/commit2.png)