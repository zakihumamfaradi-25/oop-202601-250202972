# Catatan Minggu 06: UML dan SOLID

## Yang saya kerjakan
Merancang dan mendokumentasikan model arsitektur perangkat lunak modul `core` menggunakan Class Diagram dan Sequence Diagram berbasis standar UML, serta menyusun analisis komprehensif implementasi prinsip-prinsip SOLID pada basis kode sistem Aset-Pinjam di berkas `docs/uml/solid.md`. Class Diagram memodelkan seluruh atribut, metode, tipe, tingkat visibilitas, paket, relasi pewarisan, implementasi antarmuka, dan multiplicity. Sequence Diagram memodelkan interaksi kolaborasi objek pada proses `JadwalPeminjaman.ajukan(Peminjaman)` yang mencakup skenario bentrok dan skenario sukses menggunakan fragmen kombinasi `alt`.

## Keputusan desain
- **Penyediaan Format Diagram Ganda (Mermaid & PlantUML)**: Diagram disediakan dalam format Markdown berbasis Mermaid (`class-diagram.md` dan `sequence-diagram.md`) agar dapat langsung dirender secara interaktif di antarmuka web GitHub, serta dilengkapi berkas sumber PlantUML murni (`class.puml` dan `sequence.puml`) untuk kompatibilitas perkakas diagram standar industri.
- **Konsistensi Ketat dengan Kode Riil**: Diagram kelas dirancang secara presisi mencerminkan struktur kode yang ada saat ini (Minggu 1 s.d. 5), termasuk kelas abstrak `Aset`, subclass `Ruang`, `Laptop`, `AlatLab`, strategi denda `DendaFlat` dan `DendaProgresif`, serta model transaksi `Peminjaman` dan `JadwalPeminjaman`, tanpa menyertakan artefak fiktif.
- **Pemodelan Percabangan Alur pada Sequence Diagram**: Penggunaan fragmen `alt` secara eksplisit membedakan respons sistem saat terdeteksi bentrok waktu (alur terminasi penolakan peminjaman) versus alur normal ketika jadwal valid dan kuota mencukupi (pemberian status `DIAJUKAN`).
- **Analisis Menyeluruh Kelima Prinsip SOLID**: Dokumen `docs/uml/solid.md` mengulas secara mendalam penerapan seluruh pilar SOLID (SRP, OCP, LSP, ISP, DIP) dengan menunjuk kelas-kelas nyata di kode dan memaparkan alasan arsitektural di baliknya.

## Kendala
Menjaga konsistensi 100% antara spesifikasi tipe data dan modifier pada diagram UML dengan implementasi kode Java riil yang telah dikembangkan dari minggu 1 hingga 5, agar diagram tidak sekadar menjadi sketsa abstrak ideal melainkan cerminan akurat dari basis kode. Kendala diatasi dengan melakukan penelusuran langsung (*code tracing*) terhadap struktur deklarasi kelas dan signature metode Java.

## Penggunaan AI
- Proporsi Kontribusi: **Sekitar 20% dibantu AI sebagai mitra diskusi teknis notasi sintaksis PlantUML/Mermaid dan tinjauan komparatif prinsip SOLID, sedangkan 80% perancangan arsitektur diagram kelas, pemodelan skenario sekuensial interaksi objek, analisis prinsip desain perangkat lunak, dan penulisan seluruh dokumen dikerjakan secara mandiri oleh mahasiswa.**
- Alat yang dipakai: Antigravity AI
- Bagian yang dibantu AI: Referensi notasi penulisan fragmen `alt` pada sequence diagram dan validasi sintaks Mermaid/PlantUML.
- Prompt utama:
  1. "Bagaimana notasi standar UML untuk memodelkan percabangan bersyarat (kondisi sukses vs exception/bentrok) menggunakan combined fragment alt pada Sequence Diagram Mermaid dan PlantUML?"
  2. "Berikan evaluasi kritis bagaimana penerapan Interface Segregation Principle (ISP) pada antarmuka DapatDipinjam dan AturanDenda memitigasi risiko coupling yang tidak perlu pada sistem peminjaman aset."
- Yang saya ubah atau tolak dari saran AI, dan alasannya: Menolak saran AI untuk memasukkan kelas-kelas rencana masa depan yang belum diimplementasikan (seperti DAO atau controller API) ke dalam Class Diagram minggu 6; saya memutuskan untuk membatasi diagram secara ketat hanya pada kelas-kelas yang telah riil ada di kode modul `core` saat ini agar sepenuhnya mematuhi aturan dosen bahwa 'diagram harus konsisten dengan kode, bukan sketsa ideal'.
- Satu hal yang saya pahami lebih baik sekarang: Pemahaman konkret bagaimana diagram UML bukan sekadar gambar dokumentasi statis, melainkan alat verifikasi desain yang memperjelas batas tanggung jawab kelas (SRP) dan memandu perluasan fitur tanpa merusak stabilitas sistem yang telah ada (OCP).
