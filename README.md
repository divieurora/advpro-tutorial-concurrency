# Advance Programming - Module 6 Reflection

Reflections List:
- [Reflection 1](#reflection-(1))

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