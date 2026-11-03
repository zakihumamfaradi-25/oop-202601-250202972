# Asumsi dan Keputusan Desain

Isi bagian ini sendiri. Satu entri per keputusan. Dinilai dari kejelasan alasan dan konsistensi dengan kode dan tes Anda,
bukan dari jawaban "benar".

Format tiap entri:

```
## A-NN: judul singkat
- Pertanyaan: ...
- Keputusan: ...
- Alasan: ...
- Dampak ke kode: ... (kelas/metode)
- Tes yang membuktikan: ... (nama tes di folder mahasiswa/)
```

## Aturan ambigu dari docs/aturan-bisnis.md

Salin setiap pertanyaan ambigu (nomor 1 sampai 7) ke sini dan jawab. Contoh:

## A-01: batas waktu bentrok
- Pertanyaan: 09:00-11:00 dan 11:00-13:00 pada aset yang sama bentrok atau tidak?
- Keputusan: (isi)
- Alasan: (isi)
- Dampak ke kode: (isi)
- Tes yang membuktikan: (isi)

## Asumsi lain

Tambahkan di sini keputusan lain yang Anda ambil: rumus denda progresif, perubahan skema database, format error API, dan seterusnya.

## A-08: Rumus denda progresif (Minggu 5)
- Pertanyaan: Bagaimana formula perhitungan denda progresif pada implementasi `AturanDenda`?
- Keputusan: Denda progresif dihitung dengan formula `dendaFlat + (hariTerlambat * (hariTerlambat - 1) / 2) * (dendaSatuHari / 10)`.
- Alasan: Memastikan bahwa pada hari ke-0 denda bernilai 0, hari ke-1 identik dengan denda flat, hari-hari berikutnya tidak pernah lebih murah dari denda flat dan selalu monotonik naik (tidak pernah turun), serta pada hari ke-30 beban denda terbukti lebih berat dibanding denda flat secara proporsional terhadap tarif jenis aset.
- Dampak ke kode: `id.ac.upb.asetpinjam.denda.DendaProgresif.hitung(Aset, int)`.
- Tes yang membuktikan: `id.ac.upb.asetpinjam.Minggu05AbstraksiTest.dendaProgresifMemenuhiSyaratMinimal`.
