# Simulasi Penggunaan Listrik Rumah Tangga - Versi Terbaru

Aplikasi ini dibuat untuk fitur simulasi pada media interaktif edukasi penghematan energi listrik rumah tangga.

## Konsep Final
Simulasi ini menggunakan sistem daftar/keranjang alat:
1. Pengguna mengisi luas rumah.
2. Pengguna memilih alat dari database.
3. Pengguna mengisi jumlah unit, jam/hari, dan hari/bulan.
4. Pengguna klik "Tambahkan ke Daftar".
5. Pengguna dapat menambahkan alat lain sebanyak yang diperlukan.
6. Sistem menjumlahkan seluruh konsumsi alat.
7. Sistem menghitung total kWh, estimasi biaya, nilai IKE, kategori hemat/tidak hemat, grafik, dan saran penghematan.

## Arahan Integrasi Articulate
Pada slide Articulate, gunakan layout singkat:

SIMULASI PENGGUNAAN LISTRIK RUMAH TANGGA

Klik tombol di bawah untuk membuka simulasi.
Simulasi akan terbuka di tab baru pada browser.

Setelah selesai mencoba simulasi, tutup tab simulasi
atau klik tab media interaktif yang masih terbuka di browser.

[ Buka Simulasi ]

[ Kembali ke Main Menu ]

## Pembaruan Versi Ini
- Judul pengguna dibuat lebih ramah: "Simulasi Penggunaan Listrik Rumah Tangga".
- Penjelasan IKE tetap tersedia di bagian petunjuk simulasi.
- Instruksi kembali ke media dibuat lebih jelas: tutup tab simulasi atau klik tab media interaktif.
- Ada fitur pencarian alat langsung agar pengguna lebih cepat menemukan alat.
- Pengguna tetap bisa memilih alat berdasarkan kategori.
- Unduhan hasil diperbarui menjadi PDF dan CSV.
- PDF berisi ringkasan hasil, daftar alat, nilai IKE, kategori, dan saran penghematan.
- CSV berisi data alat yang dimasukkan pengguna.

## Data
Aplikasi menggunakan:
- data_alat.csv
- tarif_pln.csv
- referensi.csv

## Rumus
kWh/bulan = (Watt × Faktor Aktif × Jumlah × Jam/Hari × Hari/Bulan) / 1000

IKE = Total kWh/bulan / Luas rumah (m²)

Biaya = Total kWh/bulan × Tarif listrik per kWh

## Kategori IKE
- IKE < 10 kWh/m²/bulan = Hemat
- IKE ≥ 10 kWh/m²/bulan = Tidak Hemat

## Cara Menjalankan Lokal
```bash
python3 -m pip install -r requirements.txt
python3 -m streamlit run app.py
```

## Cara Deploy ke Link Publik
1. Upload file berikut ke GitHub:
   - app.py
   - data_alat.csv
   - tarif_pln.csv
   - referensi.csv
   - requirements.txt
   - README.md
2. Buka Streamlit Community Cloud.
3. Pilih repository GitHub.
4. Main file path: app.py
5. Klik Deploy.
6. Salin link publik dan masukkan ke Articulate Storyline 3.
