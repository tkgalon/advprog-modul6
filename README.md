# Refleksi

Nama: Muhammad Zaid Ats Tsabit <br>
NPM: 2306224410 <br>
Kelas: Pemograman Lanjut B 

---

## Commit 1: Handle-Connection - Check Response
Pada tahap ini, kita telah membuat fungsi bernama handle_connection dengan tujuan menangani koneksi masuk dari browser. Fungsi ini menerima sebuah koneksi dalam bentuk TcpStream, yang didapatkan dari TcpListener. Setiap koneksi yang diterima akan dibaca dengan bantuan BufReader yang bertugas membaca request HTTP secara baris per baris. Proses pembacaan ini terus berlangsung hingga ditemukan sebuah baris kosong, yang menandakan akhir dari header request tersebut. Setelah selesai dibaca, request yang dikirim browser disimpan dalam sebuah vektor (list) berisi kumpulan baris dalam bentuk string, yang kemudian ditampilkan di konsol. Jadi, intinya pada tahap ini kita sudah bisa menerima request HTTP dari browser dan mencetak request tersebut, tetapi kita belum bisa memberikan respons balik ke browser. 

## Commit 2: Returning HTML
Disini kita mengganti print output dengan beberapa line code berikut:
```rust
    let status_line = "HTTP/1.1 200 OK";
    let contents = fs::read_to_string("hello.html").unwrap();
    let length = contents.len();
    let response = format!("{status_line}\r\nContent-Length:{length}\r\n\r\n{contents}");
    
    stream.write_all(response.as_bytes()).unwrap();
```
Dimana kita menambahkan variable status_line untuk memastikan bahwa browser mengetahui bahwa request berhasil diproses dengan kode 200 OK. Kemudian, kita menggunakan fs::read_to_string() untuk membaca isi file HTML yang akan dikirimkan sebagai response sehingga halaman dapat ditampilkan di browser. Selain itu, kita juga menambahkan header Content-Length, yang memberi tahu browser berapa banyak data yang akan diterima agar proses rendering berjalan dengan baik. Terakhir, stream.write_all(response.as_bytes()).unwrap(); digunakan untuk mengirimkan respon secara langsung ke browser dalam bentuk byte, memastikan bahwa halaman HTML dapat diterima dan ditampilkan dengan benar. Alhasil, kita sudah berhasil membuat server bisa menangani request dan mengirimkan hello.html sebagai file responsenya.

![Commit 2 screen capture](/assets/images/commit2.jpeg)

## Commit 3: Validating request and selectively responding
Pada tahap ini, server kita sudah bisa membedakan antara request yang valid dan yang tidak. Jika pengguna mengakses "/", server akan menampilkan hello.html dengan status 200 OK, sedangkan jika pengguna mengakses path yang tidak dikenal, server akan mengembalikan 404.html dengan status 404 NOT FOUND.

![Commit 2 screen capture](/assets/images/commit3.jpeg)

```rust
    // --snip--
    let request_line = buf_reader.lines().next().unwrap().unwrap();

    let (status_line, filename) = if request_line == "GET / HTTP/1.1" {
        ("HTTP/1.1 200 OK", "hello.html")
    } else {
        ("HTTP/1.1 404 NOT FOUND", "404.html")
    };
```
Untuk melakukan hal tersebut, kita perlu menambahkan kondisi if-else untuk memvalidasi apakah path yang dituju valid atau tidak.
Jika iya, maka menampilkan hello.html sebagai file responsenya. Namun, jika tidak maka akan menampilkan 404.html sebagai file responsenya.
Selain itu, kita juga menggunakan code yang sudah direfactor karena code yang sebelum direfactor masih terdapat redundansi code pada bagian
if-elsenya. 

Berikut code yang belum direfactor:
```rust
    // --snip--
    if request_line == "GET / HTTP/1.1" {
        let status_line = "HTTP/1.1 200 OK";
        let contents = fs::read_to_string("hello.html").unwrap();
        let length = contents.len();

        let response = format!(
            "{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}"
        );

        stream.write_all(response.as_bytes()).unwrap();
    } else {
        let status_line = "HTTP/1.1 404 NOT FOUND";
        let contents = fs::read_to_string("404.html").unwrap();
        let length = contents.len();

        let response = format!(
            "{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}"
        );

        stream.write_all(response.as_bytes()).unwrap();
    }
```