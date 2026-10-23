# Catatan Minggu 03: Inheritance dan Hierarki Kelas

## Yang saya kerjakan
Mengimplementasikan pewarisan kelas (*inheritance*) dengan menurunkan kelas dasar `Aset` ke dalam tiga subclass konkret: `Ruang`, `Laptop`, dan `AlatLab`. Menambahkan atribut spesifik untuk tiap subclass (`kapasitas` pada Ruang; `spesifikasi` dan `nomorInventaris` pada Laptop; `laboratorium` dan `tingkatRisiko` pada AlatLab), mengintegrasikan pemanggilan konstruktor induk melalui `super(kode, nama)` dengan validasi ketat, serta melakukan *method overriding* pada `getJenis()` untuk masing-masing kelas turunan.

## Keputusan desain
- **Pemisahan Field Induk vs Subclass**: Atribut umum yang dimiliki seluruh aset (`kode`, `nama`, `status`, dan `kondisi`) tetap dikelola oleh kelas induk `Aset` guna memaksimalkan penggunaan ulang kode (*code reuse*). Atribut khusus domain didefinisikan secara eksklusif dan privat (`private final`) pada subclass masing-masing demi mematuhi prinsip enkapsulasi.
- **Rantai Validasi Konstruktor**: Konstruktor subclass memanggil `super(kode, nama)` pada baris pertama agar validasi dasar kelas induk langsung dieksekusi. Selanjutnya, validasi parameter lokal (kapasitas > 0, spesifikasi/nomor inventaris tidak kosong, lab tidak kosong, dan tingkat risiko non-null) dijalankan secara *defensive*.
- **Polimorfisme `getJenis()`**: Meng-override metode `getJenis()` di setiap kelas turunan agar mengembalikan konstanta `JenisAset` yang presisi (`RUANG`, `LAPTOP`, `ALAT_LAB`), sehingga seluruh entitas dapat diakses secara polimorfis melalui referensi `Aset`.
- **Analisis Keterbatasan Inheritance vs Komposisi**:
  Salah satu contoh nyata pada sistem ini di mana *inheritance* kurang tepat dibanding *komposisi* adalah pemodelan **fasilitas atau kelengkapan ruangan** (misalnya proyektor, papan pintar, atau sound system). Jika menggunakan *inheritance* (misalnya membuat kelas `RuangBerproyektor extends Ruang`), sistem akan mengalami ledakan hierarki kelas (*class explosion*) dan kaku terhadap perubahan fasilitas fisik. Pendekatan **komposisi** jauh lebih unggul: kelas `Ruang` memiliki (*has-a*) atribut daftar fasilitas (`List<Fasilitas>`), sehingga fasilitas dapat ditambah, dipindahkan, atau dilepas secara dinamis pada saat *runtime* tanpa merombak struktur kelas.

## Kendala
Memastikan urutan konstruksi objek di mana pemanggilan `super(kode, nama)` harus selalu menjadi baris pernyataan pertama sebelum validasi field khusus subclass dapat dieksekusi. Hal ini ditangani dengan menempatkan pengecekan kondisi lokal langsung setelah inisialisasi induk berhasil.

## Penggunaan AI
- Proporsi Kontribusi: **Sekitar 20% dibantu AI sebagai mitra brainstorming dan referensi teknis, sedangkan 80% perancangan struktur kelas, logika bisnis, aturan validasi, dan implementasi kode saya kerjakan secara mandiri.**
- Alat yang dipakai: Antigravity AI
- Bagian yang dibantu AI: Diskusi perbandingan arsitektural kelemahan *inheritance* versus *komposisi* (*favor composition over inheritance*) serta sintaks penulisan override enum.
- Prompt utama:
  1. "Bagaimana best practice pemanggilan konstruktor super dan pelemparan IllegalArgumentException pada subclass turunan di Java 21?"
  2. "Berikan analisis komparatif arsitektural kapan penggunaan komposisi lebih unggul daripada inheritance pada pemodelan domain fasilitas aset kampus."
- Yang saya ubah atau tolak dari saran AI, dan alasannya: Menolak saran AI untuk melonggarkan visibilitas field kelas induk `Aset` menjadi `protected`. Saya tetap mempertahankan seluruh field induk sebagai `private` murni dan mengakses state melalui getter publik agar prinsip enkapsulasi tidak bocor ke subclass.
- Satu hal yang saya pahami lebih baik sekarang: Memahami esensi *Liskov Substitution Principle* (LSP), di mana objek subclass seperti `Ruang`, `Laptop`, dan `AlatLab` dapat diperlakukan dan dikoleksi secara seragam sebagai tipe induk `Aset` tanpa merusak integritas alur program.
