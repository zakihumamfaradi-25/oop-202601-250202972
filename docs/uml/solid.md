# Penerapan Prinsip SOLID pada Sistem Aset-Pinjam

Dokumen ini menganalisis implementasi prinsip-prinsip desain berorientasi objek **SOLID** (*Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion*) yang diterapkan secara nyata pada modul `core` sistem **Aset-Pinjam**.

---

## 1. Single Responsibility Principle (SRP)
> *"A class should have one, and only one, reason to change."*

### Kelas yang Terlibat:
- `id.ac.upb.asetpinjam.model.Aset`
- `id.ac.upb.asetpinjam.kontrak.AturanDenda` (`DendaFlat`, `DendaProgresif`)
- `id.ac.upb.asetpinjam.katalog.KatalogAset`
- `id.ac.upb.asetpinjam.jadwal.JadwalPeminjaman`

### Alasan & Analisis Desain:
Setiap kelas memiliki tanggung jawab tunggal yang kohesif dan terisolasi:
1. **`Aset`** hanya bertanggung jawab mengelola status, kondisi, identitas kode, dan properti fisik suatu aset kampus. Kelas ini tidak mengurus mekanisme penyimpanan koleksi maupun logika perumusan kebijakan penalti denda.
2. **`AturanDenda`** beserta implementasinya (`DendaFlat` dan `DendaProgresif`) secara terpisah memikul tanggung jawab atas algoritma perhitungan finansial denda keterlambatan. Jika formula penalti denda kampus mengalami perubahan kebijakan moneter, hanya kelas strategi denda ini yang dimodifikasi tanpa menyentuh kelas `Aset`.
3. **`KatalogAset`** memfokuskan tanggung jawabnya pada penyimpanan in-memory dan fungsionalitas pencarian (*searching/filtering*).
4. **`JadwalPeminjaman`** bertanggung jawab murni atas manajemen kalender reservasi waktu, batas kuota, dan deteksi tumpang tindih waktu peminjaman (*collision detection*).

---

## 2. Open/Closed Principle (OCP)
> *"Software entities should be open for extension, but closed for modification."*

### Kelas yang Terlibat:
- `id.ac.upb.asetpinjam.kontrak.AturanDenda` beserta kelas konkret `DendaFlat` dan `DendaProgresif`
- `id.ac.upb.asetpinjam.model.Aset` beserta subclass `Ruang`, `Laptop`, dan `AlatLab`

### Alasan & Analisis Desain:
1. **Ekstensi Aturan Denda Tanpa Mengubah Kode Lama**:
   Sistem denda menerapkan *Strategy Pattern*. Apabila di masa mendatang kampus memberlakukan kebijakan penalti baru (misalnya `DendaMusimUjian` atau `DendaKeringananOrganisasi`), pengembang cukup membuat kelas baru yang mengimplementasikan antarmuka `AturanDenda`. Komponen lain yang menggunakan antarmuka tersebut tidak perlu diubah sama sekali.
2. **Ekstensi Jenis Aset Baru**:
   Dengan adanya kelas abstrak `Aset`, penambahan kategori fasilitas kampus yang baru (seperti `KendaraanKampus` atau `PerangkatMultimedia`) dilakukan cukup dengan membuat subclass baru turunan dari `Aset` dan meng-override metode `getJenis()` serta `hitungDenda(int)`. Kelas induk `Aset`, `KatalogAset`, maupun modul peminjaman tetap tertutup dari modifikasi.

---

## 3. Liskov Substitution Principle (LSP)
> *"Subtypes must be substitutable for their base types without altering the correctness of the program."*

### Kelas yang Terlibat:
- Subclass `Ruang`, `Laptop`, dan `AlatLab` terhadap tipe dasar `Aset`

### Alasan & Analisis Desain:
Objek dari kelas turunan `Ruang`, `Laptop`, maupun `AlatLab` dapat digunakan di mana pun tipe `Aset` diharapkan tanpa merusak alur program:
1. Pada `KatalogAset`, metode `tambah(Aset aset)` dan `cari(String kata)` menerima dan memperlakukan seluruh subclass secara seragam.
2. Pada `Peminjaman`, konstruktor menerima parameter bertipe `Aset` generik dan memanggil metode polimorfis `bisaDipinjam()` secara transparan.
3. Subclass tidak mempersempit validasi atau melanggar kontrak invariant yang ditetapkan oleh kelas induk (misalnya semua subclass tetap melempar `IllegalArgumentException` pada nilai keterlambatan negatif dan menolak kode/nama kosong).

---

## 4. Interface Segregation Principle (ISP)
> *"Clients should not be forced to depend upon interfaces that they do not use."*

### Kelas yang Terlibat:
- `id.ac.upb.asetpinjam.kontrak.DapatDipinjam`
- `id.ac.upb.asetpinjam.kontrak.AturanDenda`

### Alasan & Analisis Desain:
Antarmuka dalam sistem dirancang ramping (*fine-grained*) dan spesifik pada satu kapabilitas:
1. Antarmuka **`DapatDipinjam`** hanya mengekspos metode tunggal `boolean bisaDipinjam()`. Entitas peminjaman yang hanya berkepentingan memeriksa status ketersediaan peminjaman tidak dibebani oleh dependensi kontrak perhitungan denda.
2. Antarmuka **`AturanDenda`** hanya mengekspos metode tunggal `long hitung(Aset aset, int hariTerlambat)`. Desain ini mencegah terbentuknya antarmuka gemuk (*fat interface*) yang memaksa kelas mengimplementasikan metode yang tidak relevan.

---

## 5. Dependency Inversion Principle (DIP)
> *"High-level modules should not depend on low-level modules. Both should depend on abstractions."*

### Kelas yang Terlibat:
- Hubungan antara komponen denda dengan abstraksi `AturanDenda` dan `Aset`

### Alasan & Analisis Desain:
1. Modul kalkulasi penalti tidak bergantung langsung pada kelas konkret spesifik (seperti langsung bergantung pada `Ruang` atau `DendaFlat`), melainkan berkomunikasi melalui antarmuka abstraksi `AturanDenda` dan kelas abstrak `Aset`.
2. Hal ini meningkatkan *testability* dan *decoupling*, sehingga implementasi strategi denda dapat di-mock atau diganti dengan mudah saat pengujian otomatis dijalankan.
