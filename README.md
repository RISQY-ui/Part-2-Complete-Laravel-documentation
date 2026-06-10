# Part-2-Complete-Laravel-documentation
## Deskripsi untuk GitHub (Part 2):

**Deskripsi singkat:**
> Log dokumentasi pembuatan Model & Migration di Laravel 13, struktur skema database, penanganan error (ParseError & typo), dan eksekusi migrasi ke SQLite.
---------------------------------------

# Log Dokumentasi Komprehensif: Proyek Laravel Faris - Part 2

## 📋 Daftar Isi

- [1. Dasar & Setup Proyek](#1-dasar--setup-proyek)
- [2. Model & Database (Langkah Teknis)](#2-model--database-langkah-teknis)
- [3. Troubleshooting & Keberhasilan](#3-troubleshooting--keberhasilan)

---

## 1. Dasar & Setup Proyek

| Komponen | Keterangan |
|----------|-------------|
| **Lingkungan** | PHP 8.3 dan Composer 2.7.1 di sistem Linux |
| **Inisialisasi** | `composer create-project laravel/laravel proyek-pertama-laravel` |
| **Hasil** | Berhasil dijalankan di `http://127.0.0.1:8000` |

---

## 2. Model & Database (Langkah Teknis)

### Pembuatan File

Perintah yang digunakan:

```bash
php artisan make:model NamaModel -m
```

Penjelasan Perintah:

Bagian Perintah Fungsi
php artisan Panggilan alat bantu Laravel
make:model Membuat representasi data di kode
-m Flag otomatis untuk membuat file migration (skema database)

---

Struktur Skema

File migrasi berada di: database/migrations/..._create_nama_models_table.php

Kode Penjelasan
Schema::create Perintah untuk membuat tabel baru bernama nama_models
$table->id() Membuat kolom id sebagai Primary Key dengan auto-increment
$table->string('nama_model') Membuat kolom teks pendek untuk menyimpan data nama
$table->boolean('status')->default(false) Membuat kolom boolean (benar/salah) dengan nilai default false
$table->timestamps() Otomatis menambah kolom created_at dan updated_at

---

3. Troubleshooting & Keberhasilan

Penanganan Error

Error Solusi
ParseError (kurung kurawal tidak lengkap pada fungsi up dan down) Memperbaiki kurung kurawal yang kurang
Typo variabel ($tabel menjadi $table) Memperbaiki penulisan variabel yang salah

Finalisasi

```bash
php artisan migrate
```

Hasil: Migrasi berhasil dieksekusi dan membuat tabel nama_models di database SQLite secara resmi ✅

---

📝 Ringkasan Part 2

No Materi Status
1 Setup Lingkungan (PHP 8.3 + Composer) ✅
2 Inisialisasi proyek Laravel ✅
3 Membuat Model dengan flag -m ✅
4 Memahami struktur skema migration ✅
5 Perbaikan ParseError (kurung kurawal) ✅
6 Perbaikan typo $tabel → $table ✅
7 Menjalankan php artisan migrate ✅

---

Dokumentasi untuk keperluan pembelajaran Laravel 13 oleh Faris - Part 2.



---

laravel, laravel13, migration, model, database, sqlite, php-artisan, troubleshooting
