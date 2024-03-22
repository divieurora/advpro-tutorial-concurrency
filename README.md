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

### Reflection (3)

How to split between response?

Response yang berbeda pada program ini terletak pada endpoint yang dihasilkan. Kalau hanya menggunakan `/` maka akan return response program berhasil dijalankan, sedangkan endpoint lainnya seperti `/something-else` maka akan return response tidak ditemukan. 

Untuk memberikan hasil perbedaan return response, perlu dibaca terlebih dahulu path yang direquest dengan `let request_line = buf_reader.lines().next().unwrap().unwrap();`. Ketika path yang diminta valid, maka akan menghasilkan `request_line == "GET / HTTP/1.1"`. Seperti pada milestones sebelumnya, semua response yang valid akan mereturn hello.html.

Sedangkan response lainnya yang tidak valid perlu ditampilkan juga page not found dengan cara membuat conditional untuk hasil `request_line` yang lain. Perbedaannya terletak pada `status_line` dan file name pada `fs::read_to_string(file)`. Berikut adalah cara menampilkan page not found:

```rust
let status_line = "HTTP/1.1 404 NOT FOUND";
let contents = fs::read_to_string("404.html").unwrap();
let length = contents.len();

let response = format!("{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}");
        
stream.write_all(response.as_bytes()).unwrap();
```

Berikut adalah web page yang ditampilkan jika responsenya tidak valid:
![](/assets/images/commit3.png)

Why the refactoring is needed?

Karena `if` dan `else` block memiliki lebih banyak repetisi untuk bagian code lainnya selain `status_line` dan file name, maka sebaiknya dilakukan refactoring untuk mengurangi duplikasi code. 

Code dapat dibuat lebih concised dengan membuat `if` dan `else` block hanya membedakan `status_line` dan file name, serta mengeluarkan variable lainnya dari conditional block.