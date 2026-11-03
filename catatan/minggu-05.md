# Catatan Minggu 05: Abstraction (Abstract Class dan Interface)

## Yang saya kerjakan
Mengimplementasikan pilar abstraksi (*abstraction*) dalam sistem Aset-Pinjam dengan merefaktor kelas `Aset` menjadi kelas abstrak (`abstract class`) dan mendeklarasikan metode abstrak `hitungDenda(int)` serta `getJenis()`. Mengimplementasikan kontrak antarmuka `DapatDipinjam` pada kelas `Aset` melalui metode `bisaDipinjam()`. Selain itu, mengimplementasikan antarmuka `AturanDenda` (pola *Strategy*) pada kelas strategi denda `DendaFlat` dan `DendaProgresif` dengan perumusan perhitungan denda berjenjang yang aman dan non-decreasing.

## Keputusan desain
- **Refaktor `Aset` Menjadi Abstract Class**: Menjadikan `Aset` sebagai kelas abstrak mencegah instansiasi langsung entitas generik di memori, karena dalam domain fisik kampus setiap aset selalu berwujud spesifik (`Ruang`, `Laptop`, atau `AlatLab`). Atribut bersama (`kode`, `nama`, `status`, `kondisi`) serta perilaku bersama (`bisaDipinjam`, getter/setter) tetap dipusatkan di kelas abstrak ini untuk penggunaan ulang kode (*code reuse*).
- **Invarian Bisnis pada `bisaDipinjam()`**: Metode ini mengembalikan nilai boolean berdasarkan evaluasi kondisi simultan: `this.status == StatusAset.TERSEDIA && this.kondisi != Kondisi.RUSAK_BERAT`. Keputusan ini memastikan bahwa aset dalam kondisi rusak berat tidak dapat dipinjam meskipun status administratifnya belum sempat diperbarui oleh petugas.
- **Pemisahan Perilaku dengan Interface `AturanDenda`**: Menggunakan antarmuka murni untuk kalkulasi denda memisahkan entitas data aset dari variasi algoritma perhitungan penalti. Hal ini memungkinkan fleksibilitas pergantian aturan denda pada saat *runtime* tanpa perlu mengubah kode model aset (*Open/Closed Principle*).
- **Formulasi Denda Progresif Terukur**: Denda progresif dirancang dengan menggabungkan denda flat dan komponen eskalasi beban harian: `dendaFlat + (hariTerlambat * (hariTerlambat - 1) / 2) * (dendaSatuHari / 10)`. Rumus ini secara matematis menjamin bahwa pada hari ke-0 denda adalah 0, hari ke-1 sama dengan denda flat, nilai selalu monotonik naik (tidak pernah turun), dan pada hari ke-30 nilai penalti terbukti lebih tinggi dibanding denda flat secara proporsional terhadap tarif varian.

## Kendala
Memastikan transisi kelas `Aset` menjadi abstrak tidak memecah pengujian unit minggu-minggu sebelumnya. Kendala ini teratasi dengan baik karena kelas pembantu pengujian `TestSupport.aset(...)` telah dirancang secara dinamis oleh dosen untuk otomatis menginstansiasi subclass konkret (`Ruang`) begitu mendeteksi `Modifier.isAbstract(Aset.class.getModifiers())`.

## Penggunaan AI
- Proporsi Kontribusi: **Sekitar 20% dibantu AI sebagai mitra diskusi teknis dan verifikasi pembuktian matematis formula progresif, sedangkan 80% perancangan hierarki abstraksi, implementasi antarmuka, penentuan logika invarian bisnis, dan penulisan kode saya kerjakan secara mandiri.**
- Alat yang dipakai: Antigravity AI
- Bagian yang dibantu AI: Evaluasi komparatif antara Abstract Class vs Interface dalam arsitektur OOP serta validasi sifat monotonik formula denda progresif.
- Prompt utama:
  1. "Bagaimana pertimbangan arsitektur OOP dalam memilih antara abstract class dan interface untuk memodelkan entitas domain dan strategi bisnis yang dapat dipertukarkan?"
  2. "Formulasikan fungsi denda progresif diskrit f(h) yang memenuhi invarian: f(0)=0, f(h) >= flat(h), f(h+1) >= f(h), dan f(30) > flat(30) dengan ketergantungan pada multiplier aset."
- Yang saya ubah atau tolak dari saran AI, dan alasannya: Menolak saran AI untuk menggunakan fungsi eksponensial (`Math.pow`) pada kalkulasi denda progresif karena rentan memicu *integer overflow* pada tipe data `long` jika durasi keterlambatan cukup panjang, serta menyulitkan transparansi rincian tagihan bagi mahasiswa peminjam; saya memilih formula berbasis deret aritmatika terukur yang sepenuhnya aman dan deterministik.
- Satu hal yang saya pahami lebih baik sekarang: Pemahaman konseptual yang mendalam mengenai kapan harus menggunakan *abstract class* (untuk relasi kuat *is-a* dengan kode dan state bersama) versus *interface* (untuk kontrak perilaku *can-do* / peran fungsional murni tanpa membatasi hierarki pewarisan kelas).
