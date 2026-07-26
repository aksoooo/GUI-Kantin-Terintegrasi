# Kantin Terintegrasi — Mini Project Database (Kelompok 11)

Aplikasi **Kantin Terintegrasi** adalah sistem manajemen kios dan transaksi kantin berbasis basis data relasional (SQLite) dengan antarmuka web interaktif yang dibangun menggunakan [Gradio](https://www.gradio.app/). Aplikasi ini dikembangkan sebagai mini project mata kuliah Basis Data oleh Kelompok 11.

Aplikasi mendukung dua peran pengguna dengan hak akses dan fitur yang berbeda:

- **Pembeli** — mendaftar/login, melihat dan mengubah profil, memilih kios & menu, mengelola keranjang belanja, serta melakukan checkout (order).
- **Penjual** — mendaftar/login, mengelola profil kios, menambah/mengubah/menghapus menu, serta memantau riwayat transaksi dan daftar kios lain.

## Daftar Isi

- [Fitur](#fitur)
- [Arsitektur & Skema Basis Data](#arsitektur--skema-basis-data)
- [Struktur Proyek](#struktur-proyek)
- [Cara Menjalankan](#cara-menjalankan)
- [Alur Penggunaan Aplikasi](#alur-penggunaan-aplikasi)
- [Catatan Teknis & Keterbatasan](#catatan-teknis--keterbatasan)
- [Anggota Kelompok](#anggota-kelompok)
- [Lisensi](#lisensi)

## Fitur

### Autentikasi
- Registrasi akun baru sebagai `pembeli` atau `penjual`, dengan validasi input sesuai peran.
- Login berbasis email & password, dengan deteksi otomatis peran pengguna.

### Halaman Pembeli
- Melihat dan memperbarui profil (email, password, nomor telepon).
- Menjelajahi daftar kios beserta menu yang tersedia di masing-masing kios.
- Mengelola keranjang belanja (tambah item, lihat isi cart, kosongkan cart).
- Memilih metode pembayaran (Tunai / Non Tunai) dan mengonfirmasi order, yang akan tercatat sebagai transaksi baru pada basis data.

### Halaman Penjual
- Melihat profil kios yang dimiliki.
- Menambah, mengubah, dan menghapus menu milik kios sendiri (dengan validasi kepemilikan).
- Melihat rekap seluruh menu milik kios, daftar seluruh kios yang terdaftar, dan riwayat transaksi kios.
- Memperbarui data kios (nama kios, nama pedagang) sekaligus data akun terkait (email, password).

## Arsitektur & Skema Basis Data

Basis data `database.db` (SQLite) diunduh secara otomatis dari Google Drive saat notebook dijalankan. Tabel-tabel utama yang digunakan aplikasi:

| Tabel | Deskripsi |
|---|---|
| `Akun` | Menyimpan kredensial login (`ID Akun`, `Email`, `Password`). |
| `Pembeli` | Data pembeli (`NPM`, `Nama`, `Jurusan`, `Nomor Telepon`, `ID Akun`). |
| `Kios` | Data kios milik penjual (`ID Kios`, `Nama Kios`, `Nama Pedagang`, `ID Akun`). |
| `Menu` | Daftar menu tiap kios (`ID Menu`, `Menu`, `Kategori`, `Harga`, `ID Kios`). |
| `Transaksi Penjualan` | Riwayat transaksi (`ID Transaksi`, `Tanggal`, `NPM`, `ID Menu`, `Jumlah`, `Metode Pembayaran`). |

Relasi antar tabel bersifat satu-ke-satu antara `Akun` dan `Pembeli`/`Kios` (tergantung peran), serta satu-ke-banyak antara `Kios` dan `Menu`, serta antara `Menu`/`Pembeli` dan `Transaksi Penjualan`.

## Struktur Proyek

```
.
├── Mini_Project_Database_Kelompok_11.ipynb   # Notebook utama aplikasi
└── README.md                                  # Dokumentasi proyek (berkas ini)
```

Notebook disusun ke dalam beberapa bagian utama:

1. Persiapan lingkungan (import pustaka & pengunduhan basis data).
2. Konfigurasi global (path basis data, state keranjang, kategori menu).
3. Modul autentikasi (login & registrasi).
4. Modul halaman pembeli (profil, kios/menu, cart, checkout).
5. Modul halaman penjual (profil kios, manajemen menu, rekap transaksi).
6. Antarmuka pengguna berbasis Gradio yang merakit seluruh modul di atas.

## Cara Menjalankan

Proyek ini dirancang untuk dijalankan pada **Google Colab**, namun juga dapat dijalankan secara lokal dengan Jupyter Notebook.

### 1. Clone repository

```bash
git clone https://github.com/<username>/<nama-repo>.git
cd <nama-repo>
```

### 2. Instal dependensi

```bash
pip install gdown gradio
```

### 3. Jalankan notebook

Buka `Mini_Project_Database_Kelompok_11.ipynb` menggunakan Jupyter Notebook, JupyterLab, atau Google Colab, lalu jalankan seluruh sel secara berurutan (**Run All**).

Basis data akan diunduh secara otomatis dari Google Drive pada sel pertama, sehingga tidak diperlukan pengaturan tambahan.

### 4. Akses aplikasi

Setelah sel terakhir (`app.launch()`) dijalankan, Gradio akan menampilkan URL lokal (misalnya `http://127.0.0.1:7860`) yang dapat dibuka melalui browser untuk mengakses aplikasi.

> Jika dijalankan di Google Colab dan ingin mendapatkan tautan publik yang dapat diakses dari luar, tambahkan parameter `share=True` pada `app.launch()`.

## Alur Penggunaan Aplikasi

1. **Registrasi/Login** — pengguna baru mendaftar melalui accordion "Belum punya akun? Daftar di sini", memilih peran (`pembeli`/`penjual`), lalu login menggunakan email & password yang terdaftar.
2. **Sebagai Pembeli** — memilih kios dan menu, menambahkannya ke keranjang, memilih metode pembayaran, lalu mengonfirmasi order untuk mencatat transaksi.
3. **Sebagai Penjual** — mengelola menu kios (tambah/ubah/hapus), memantau profil kios, serta meninjau riwayat transaksi dan daftar kios lain sebagai referensi.

## Catatan Teknis & Keterbatasan

- **State keranjang belanja bersifat global**, bukan per sesi pengguna — cocok untuk lingkup demo/mini project dengan satu pengguna aktif, namun perlu disesuaikan (misalnya menggunakan `gr.State`) apabila hendak mendukung banyak pengguna secara bersamaan.
- **Pembentukan `ID Transaksi`** menggunakan kombinasi tanggal dan penghitungan jumlah transaksi pada hari berjalan, sehingga berpotensi mengalami *race condition* apabila dua transaksi diproses secara bersamaan dalam skala besar.
- **Skema `ID Menu`** diturunkan dari `ID Kios` (setiap kios memiliki rentang ID tersendiri), sehingga terdapat keterkaitan langsung antara struktur ID kios dan ID menu.
- Aplikasi ini dibangun untuk tujuan pembelajaran (mini project) dan belum mengimplementasikan praktik keamanan produksi, seperti hashing password atau proteksi terhadap SQL injection tingkat lanjut (meskipun seluruh query telah menggunakan parameterized query).

## Lisensi

Proyek ini dibuat untuk keperluan akademik dan tidak dilisensikan untuk penggunaan komersial.
