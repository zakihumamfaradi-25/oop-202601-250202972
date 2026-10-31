# Catatan Minggu 04: Polymorphism

## Yang saya kerjakan
Mengimplementasikan pilar polimorfisme (*polymorphism*) baik dalam bentuk *runtime polymorphism* (*method overriding*) maupun *compile-time polymorphism* (*method overloading*). Pada tingkat model, saya meng-override metode `hitungDenda(int hariTerlambat)` pada kelas `Aset` dan ketiga subclass-nya (`Ruang`, `Laptop`, dan `AlatLab`) dengan menerapkan multiplier jenis masing-masing (1x, 2x, 4x) terhadap tarif varian NIM. Pada tingkat katalog, saya mengimplementasikan dua bentuk *overload* metode `cari` pada kelas `KatalogAset` untuk pencarian berbasis kata kunci dan filter kombinasi jenis aset.

## Keputusan desain
- **Dynamic Binding pada Denda**: Setiap kelas turunan bertanggung jawab menentukan multiplier dendanya sendiri (Ruang 1x, Laptop 2x, AlatLab 4x) yang dikalikan dengan tarif dasar varian (`Varian.aktif().dendaPerHari`). Pengecekan defensif (`hariTerlambat < 0`) diterapkan secara konsisten sebelum kalkulasi dilakukan, melempar `IllegalArgumentException`.
- **Kode Bersih pada Overloading Pencarian**: Metode `cari(String kata, JenisAset jenis)` memanfaatkan kembali (*reusing*) keluaran dari metode `cari(String kata)` sebelum melakukan penyaringan lanjutan menggunakan `getJenis() == jenis`. Pendekatan ini meminimalkan duplikasi logika pencarian *case-insensitive* (prinsip *Don't Repeat Yourself* / DRY).
- **Pencegahan Null Pointer**: Penanganan input pencarian bernilai `null` dilakukan secara defensif dengan langsung mengembalikan list kosong (`List.of()`), menjaga keamanan pemanggilan pada saat aplikasi berjalan.

## Kendala
Memastikan konsistensi penanganan nilai 0 hari agar mengembalikan denda 0 tanpa memicu kesalahan aritmatika atau exception, serta memastikan pencarian nama bersifat *case-insensitive* tanpa terpengaruh oleh perbedaan huruf kapital di data aset maupun kata kunci masukan. Kendala diatasi dengan memanfaatkan `toLowerCase()` pada kedua sisi perbandingan string.

## Penggunaan AI
- Proporsi Kontribusi: **Sekitar 20% dibantu AI sebagai mitra diskusi teknis konseptual dan tinjauan teori polimorfisme, sedangkan 80% perancangan logika denda varian, penulisan metode overloading, dan implementasi kode saya kerjakan secara mandiri.**
- Alat yang dipakai: Antigravity AI
- Bagian yang dibantu AI: Diskusi perbandingan efisiensi *dynamic method dispatch* (runtime) versus *compile-time method resolution* (overloading) di lingkungan Java 21.
- Prompt utama:
  1. "Bagaimana best practice implementasi method overloading pada repositori katalog in-memory agar menghindari duplikasi logika pencarian string case-insensitive?"
  2. "Bagaimana JVM menangani dynamic method dispatch (vtable) saat memanggil metode hitungDenda polimorfis dari referensi bertipe parent class Aset?"
- Yang saya ubah atau tolak dari saran AI, dan alasannya: Menolak saran AI untuk memisahkan pencarian jenis menggunakan abstraksi *Specification pattern* atau antarmuka predikat terpisah, karena tugas minggu 4 berfokus pada esensi dasar *method overloading* murni; saya memilih delegasi langsung antar-metode *overload* agar kode tetap ringkas dan mudah dipelihara.
- Satu hal yang saya pahami lebih baik sekarang: Pemahaman konkret perbedaan antara *compile-time polymorphism* (di mana resolusi metode ditentukan saat kompilasi berdasarkan parameter) dan *runtime polymorphism* (di mana eksekusi kode ditentukan saat runtime berdasarkan instans objek sesungguhnya di memori heap).
