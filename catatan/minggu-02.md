# Catatan Minggu 02: Struktur OOP: Class, Object, Encapsulation

## Yang saya kerjakan
Mengimplementasikan struktur kelas dasar `Aset` pada paket `model` dengan menerapkan prinsip enkapsulasi (*encapsulation*). Menambahkan atribut-atribut privat, konstruktor berparameter dengan validasi input (menolak nilai `null` dan string kosong), nilai default awal (`TERSEDIA` dan `BAIK`), serta metode *accessor* (getter) dan *mutator* (setter) yang dilengkapi validasi ketat.

## Keputusan desain
- **Immutability pada Identitas (`kode`)**: Atribut `kode` dideklarasikan sebagai `private final` dan sengaja tidak disediakan metode setter (`setKode`). Hal ini menjamin bahwa identitas aset bersifat permanen dan tidak dapat diubah setelah instansiasi objek, mencegah anomali referensi data pada siklus peminjaman.
- **Validasi Nilai String**: Validasi `kode` dan `nama` menggunakan pengecekan `isBlank()` selain `null`, guna memastikan nilai tidak berupa rangkaian spasi kosong. Jika tidak valid, dilempar `IllegalArgumentException`.
- **Nilai Status Awal yang Terstandarisasi**: Konstruktor secara otomatis menetapkan status awal aset ke `StatusAset.TERSEDIA` dan kondisi awal ke `Kondisi.BAIK` tanpa memerlukan parameter tambahan, memastikan integritas *state* awal setiap entitas baru.
- **Defensive Mutation**: Pada metode `setNama`, `setStatus`, dan `setKondisi`, validasi dilakukan di awal sebelum mutasi field terjadi (prinsip *fail-fast*), sehingga state objek tidak akan berada dalam kondisi inkonsisten jika terjadi masukan yang tidak sah.

## Kendala
Memastikan agar field `kode` tetap mematuhi seluruh batasan refleksi pada unit test (`Minggu02AsetTest`), di mana seluruh deklarasi field diwajibkan privat murni dan tidak ada method setter untuk kode. Kendala diatasi dengan memeriksa assertion refleksi kelas secara teliti.

## Penggunaan AI
- Alat yang dipakai: Antigravity AI
- Bagian yang dibantu AI: Diskusi perancangan mekanisme enkapsulasi kelas `Aset`, evaluasi pemilihan exception yang tepat (`IllegalArgumentException` vs `NullPointerException`), dan tinjauan pola *defensive programming*.
- Prompt utama:
  1. "Bagaimana menerapkan pola encapsulation yang idiomatis di Java untuk entitas model domain dengan atribut identitas yang immutable dan atribut state yang mutable?"
  2. "Apakah lebih baik melempar IllegalArgumentException atau NullPointerException saat memvalidasi parameter setter ber-tipe Enum pada model OOP?"
  3. "Analisis potensi masalah keamanan objek jika field identifier (kode aset) diekspos melalui setter, dan bagaimana implikasinya terhadap konsistensi relasi transaksi."
- Yang saya ubah atau tolak dari saran AI, dan alasannya: Menolak saran AI untuk menggunakan *fluent setter* (*method chaining* yang mengembalikan `this`) atau anotasi Lombok, karena spesifikasi kontrak pengujian mengharuskan setter bertipe return `void` standar JavaBeans dan menjaga kode tetap murni tanpa dependensi tambahan.
- Satu hal yang saya pahami lebih baik sekarang: Pemahaman mendalam mengenai peran enkapsulasi bukan hanya sekadar membungkus variabel dengan getter/setter, melainkan menjaga *invarian bisnis* suatu objek agar tidak pernah berada dalam kondisi data yang tidak valid (*invalid state*).
