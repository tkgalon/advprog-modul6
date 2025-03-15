# Refleksi

Nama: Muhammad Zaid Ats Tsabit
NPM: 2306224410
Kelas: Pemograman Lanjut B

---

## Commit 1:Handle-Connection - Check Response
Pada tahap ini, kita telah membuat fungsi bernama handle_connection dengan tujuan menangani koneksi masuk dari browser. Fungsi ini menerima sebuah koneksi dalam bentuk TcpStream, yang didapatkan dari TcpListener. Setiap koneksi yang diterima akan dibaca dengan bantuan BufReader yang bertugas membaca request HTTP secara baris per baris. Proses pembacaan ini terus berlangsung hingga ditemukan sebuah baris kosong, yang menandakan akhir dari header request tersebut. Setelah selesai dibaca, request yang dikirim browser disimpan dalam sebuah vektor (list) berisi kumpulan baris dalam bentuk string, yang kemudian ditampilkan di konsol. Jadi, intinya pada tahap ini kita sudah bisa menerima request HTTP dari browser dan mencetak request tersebut, tetapi kita belum bisa memberikan respons balik ke browser. 
