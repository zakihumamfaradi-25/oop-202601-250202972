# Class Diagram: Sistem Aset-Pinjam

Diagram kelas ini memodelkan arsitektur berorientasi objek sistem **Aset-Pinjam** pada modul `core` (minggu 1 sampai 6). Model mencakup seluruh atribut, metode, tipe data, tingkat visibilitas, hubungan pewarisan, implementasi antarmuka, serta relasi agregasi dan asosiasi antar-paket.

## 1. Diagram UML (Mermaid)

```mermaid
classDiagram
    direction TB

    class DapatDipinjam {
        <<interface>>
        +bisaDipinjam() boolean
    }

    class AturanDenda {
        <<interface>>
        +hitung(Aset aset, int hariTerlambat) long
    }

    class StatusAset {
        <<enumeration>>
        TERSEDIA
        DIPINJAM
        RUSAK
    }

    class Kondisi {
        <<enumeration>>
        BAIK
        RUSAK_RINGAN
        RUSAK_BERAT
    }

    class JenisAset {
        <<enumeration>>
        RUANG
        LAPTOP
        ALAT_LAB
    }

    class TingkatRisiko {
        <<enumeration>>
        RENDAH
        SEDANG
        TINGGI
    }

    class StatusPeminjaman {
        <<enumeration>>
        DIAJUKAN
        DISETUJUI
        DITOLAK
        DIPINJAM
        DIBATALKAN
        DIKEMBALIKAN
        TERLAMBAT
    }

    class Peminjam {
        <<record>>
        -String id
        -String nama
        +Peminjam(String id, String nama)
        +id() String
        +nama() String
    }

    class Aset {
        <<abstract>>
        -String kode
        -String nama
        -StatusAset status
        -Kondisi kondisi
        +Aset(String kode, String nama)
        +getKode() String
        +getNama() String
        +getStatus() StatusAset
        +getKondisi() Kondisi
        +setNama(String nama) void
        +setStatus(StatusAset status) void
        +setKondisi(Kondisi kondisi) void
        +bisaDipinjam() boolean
        +getJenis()* JenisAset
        +hitungDenda(int hariTerlambat)* long
    }

    class Ruang {
        -int kapasitas
        +Ruang(String kode, String nama, int kapasitas)
        +getKapasitas() int
        +getJenis() JenisAset
        +hitungDenda(int hariTerlambat) long
    }

    class Laptop {
        -String spesifikasi
        -String nomorInventaris
        +Laptop(String kode, String nama, String spesifikasi, String nomorInventaris)
        +getSpesifikasi() String
        +getNomorInventaris() String
        +getJenis() JenisAset
        +hitungDenda(int hariTerlambat) long
    }

    class AlatLab {
        -String laboratorium
        -TingkatRisiko tingkatRisiko
        +AlatLab(String kode, String nama, String laboratorium, TingkatRisiko tingkatRisiko)
        +getLaboratorium() String
        +getTingkatRisiko() TingkatRisiko
        +getJenis() JenisAset
        +hitungDenda(int hariTerlambat) long
    }

    class DendaFlat {
        +hitung(Aset aset, int hariTerlambat) long
    }

    class DendaProgresif {
        +hitung(Aset aset, int hariTerlambat) long
    }

    class KatalogAset {
        -List~Aset~ daftar
        +tambah(Aset aset) void
        +semua() List~Aset~
        +cari(String kata) List~Aset~
        +cari(String kata, JenisAset jenis) List~Aset~
    }

    class Peminjaman {
        -String id
        -Peminjam peminjam
        -Aset aset
        -LocalDateTime mulai
        -LocalDateTime selesai
        -StatusPeminjaman status
        +Peminjaman(String id, Peminjam peminjam, Aset aset, LocalDateTime mulai, LocalDateTime selesai)
        +getId() String
        +getPeminjam() Peminjam
        +getAset() Aset
        +getMulai() LocalDateTime
        +getSelesai() LocalDateTime
        +getStatus() StatusPeminjaman
        +setStatusDariPenyimpanan(StatusPeminjaman status) void
        +ubahStatus(StatusPeminjaman baru) void
    }

    class JadwalPeminjaman {
        -List~Peminjaman~ daftar
        -int kuotaLaptop
        +JadwalPeminjaman(int kuotaLaptop)
        #kuotaLaptop() int
        #daftarMutable() List~Peminjaman~
        +tambah(Peminjaman p) void
        +bentrok(Peminjaman baru) boolean
        +semuaUrutMulai() List~Peminjaman~
        +perAset() Map~String, List~Peminjaman~~
        +hapus(String id) boolean
        +ajukan(Peminjaman baru) void
    }

    DapatDipinjam <|.. Aset : implements
    Aset <|-- Ruang : extends
    Aset <|-- Laptop : extends
    Aset <|-- AlatLab : extends

    Aset --> StatusAset : status
    Aset --> Kondisi : kondisi
    AlatLab --> TingkatRisiko : tingkatRisiko

    AturanDenda <|.. DendaFlat : implements
    AturanDenda <|.. DendaProgresif : implements
    AturanDenda ..> Aset : uses

    KatalogAset "1" o-- "*" Aset : aggregates

    Peminjaman "1" --> "1" Peminjam : peminjam
    Peminjaman "1" --> "1" Aset : aset
    Peminjaman --> StatusPeminjaman : status

    JadwalPeminjaman "1" o-- "*" Peminjaman : aggregates
```

---

## 2. Struktur Paket dan Penjelasan Komponen

### A. Paket `id.ac.upb.asetpinjam.kontrak`
1. **`DapatDipinjam`**: Antarmuka yang mendefinisikan kemampuan entitas untuk diverifikasi ketersediaannya sebelum dipinjam via `bisaDipinjam()`.
2. **`AturanDenda`**: Antarmuka pola strategi (*Strategy Pattern*) untuk kalkulasi denda keterlambatan aset.

### B. Paket `id.ac.upb.asetpinjam.model`
1. **`Aset`**: Kelas abstrak induk yang merepresentasikan semua aset kampus. Menerapkan enkapsulasi untuk `kode`, `nama`, `status`, dan `kondisi`, serta menyediakan metode abstrak `getJenis()` dan `hitungDenda(int)`.
2. **`Ruang`**: Subclass `Aset` untuk peminjaman ruangan dengan atribut khusus `kapasitas` (multiplier denda 1x).
3. **`Laptop`**: Subclass `Aset` untuk peminjaman laptop dengan atribut `spesifikasi` dan `nomorInventaris` (multiplier denda 2x).
4. **`AlatLab`**: Subclass `Aset` untuk instrumen laboratorium dengan atribut `laboratorium` dan `tingkatRisiko` (multiplier denda 4x).
5. **`Peminjam`**: Record data peminjam (`id` dan `nama`).
6. **`Peminjaman`**: Entitas transaksi peminjaman yang mengaitkan `Peminjam`, `Aset`, rentang waktu (`mulai`, `selesai`), dan siklus `StatusPeminjaman`.

### C. Paket `id.ac.upb.asetpinjam.denda`
1. **`DendaFlat`**: Strategi perhitungan denda dengan tarif flat harian (`aset.hitungDenda(hari)`).
2. **`DendaProgresif`**: Strategi perhitungan denda progresif di mana denda bertambah secara berjenjang seiring lamanya keterlambatan.

### D. Paket `id.ac.upb.asetpinjam.katalog`
1. **`KatalogAset`**: Pengelola koleksi aset di memori dengan fungsionalitas pencarian berlebih (*method overloading*) berdasarkan nama dan jenis.

### E. Paket `id.ac.upb.asetpinjam.jadwal`
1. **`JadwalPeminjaman`**: Pengelola jadwal dan reservasi peminjaman di memori, mencakup deteksi bentrok waktu dan batas kuota laptop.
