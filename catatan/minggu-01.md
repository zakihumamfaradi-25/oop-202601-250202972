# Catatan Minggu 01: Paradigma Pemrograman dan Setup

## Yang saya kerjakan
Menyelesaikan implementasi pengenalan tiga paradigma pemrograman (Prosedural, OOP, dan Fungsional) untuk menampilkan pesan sapaan Aset-Pinjam. Mengisi identitas mahasiswa pada berkas `core/src/main/resources/mahasiswa.properties` dengan NIM `250202972` dan nama `ZAKI HUMAM FARADI`. Memastikan ketiga implementasi menghasilkan keluaran teks yang identik dan lolos seluruh kriteria uji unit `Minggu01Test`.

## Keputusan desain
- Format teks sapaan diseragamkan menjadi: `"Hello Aset-Pinjam, saya " + nama + " (" + nim + ")"`. Format ini memenuhi seluruh kriteria pengujian yaitu memuat kata "Aset-Pinjam", nama, serta NIM.
- Pada `HelloProsedural.java`, fungsi `sapa` dibuat sebagai metode statis (`public static`) murni tanpa instansiasi objek.
- Pada `HelloOOP.java`, data disimpan dalam atribut `private final` (`nama` dan `nim`) yang diinisialisasi melalui konstruktor, lalu diakses melalui metode instans `sapa()`.
- Pada `HelloFungsional.java`, metode `pembuatSapaan(String nim)` memanfaatkan ekspresi lambda untuk mengembalikan `Function<String, String>` yang menerima parameter nama.

### Analisis Paradigma untuk Sistem Peminjaman Aset
1. **Gaya Prosedural**:
   - *Kelebihan*: Sangat sederhana dan efisien untuk fungsi-fungsi utilitas atau kalkulasi matematis independen (misalnya menghitung formula denda sederhana) tanpa overhead pembuatan objek.
   - *Keterbatasan*: Logika dan data terpisah, sehingga sulit dikelola saat sistem peminjaman membesar; rawan terjadi inkonsistensi state jika data aset dimodifikasi di banyak tempat berbeda.
2. **Gaya OOP (Object-Oriented Programming)**:
   - *Kelebihan*: Sangat representatif untuk memodelkan entitas nyata seperti `Aset`, `Peminjam`, dan `JadwalPeminjaman`. Enkapsulasi melindungi status aset dari perubahan tidak sah.
   - *Keterbatasan*: Menghasilkan lebih banyak boilerplate code dan struktur hierarki yang bisa menjadi terlalu kompleks (*over-engineered*) untuk operasi sederhana.
3. **Gaya Fungsional**:
   - *Kelebihan*: Bersifat bebas efek samping (*pure function*) dan immutable, memudahkan pengujian unit serta sangat andal untuk transformasi data koleksi (misalnya filtering aset yang tersedia atau agregasi laporan).
   - *Keterbatasan*: Pemodelan siklus hidup (*lifecycle*) peminjaman yang stateful (misalnya perubahan status dari *pending*, *disetujui*, hingga *kembali*) kurang intuitif jika dipaksakan tanpa paradigma berbasis objek.

## Kendala
Memastikan tanda tangan metode pada `HelloFungsional.pembuatSapaan(nim)` yang menerima NIM terlebih dahulu lalu mengembalikan fungsi penerima nama (*higher-order function / currying*). Kendala diatasi dengan meninjau ekspektasi di berkas `Minggu01Test.java`.

## Penggunaan AI
- Alat yang dipakai: Antigravity AI
- Bagian yang dibantu AI: Penjelasan alur tugas, implementasi dasar class `HelloProsedural`, `HelloOOP`, `HelloFungsional`, serta konfigurasi `mahasiswa.properties`.
- Prompt utama:
  1. "itu disuruh ngapain"
  2. "klo seperti ini udah belom"
  3. "iya clone kan dan kerjakandisini"
- Yang saya ubah atau tolak dari saran AI, dan alasannya: Memastikan NIM dan Nama disesuaikan persis dengan identitas mahasiswa resmi (`250202972` - `ZAKI HUMAM FARADI`).
- Satu hal yang saya pahami lebih baik sekarang: Pemahaman konkret perbedaan cara kerja fungsi statis, enkapsulasi OOP dengan private field, dan ekspresi lambda higher-order function dalam Java.
