# Sistem Reservasi Ruangan (Hotel / Meeting Room)

Aplikasi CRUD sederhana untuk mengelola data ruangan dan reservasi/booking, dibangun dengan PHP native (PDO), MySQL/MariaDB, Bootstrap 5, dan AJAX (jQuery). Cocok untuk kebutuhan hotel, meeting room, atau fasilitas serupa.

## Fitur

- Dashboard ringkasan: jumlah ruangan, ruangan tersedia, booking hari ini, booking pending
- CRUD Data Ruangan (tambah, edit, hapus, lihat) — tanpa reload halaman
- CRUD Data Reservasi (tambah, edit, hapus, lihat) — tanpa reload halaman
- Validasi otomatis cek bentrok jadwal: sistem menolak booking baru/edit jika rentang waktu bertabrakan dengan booking lain pada ruangan yang sama
- Filter reservasi berdasarkan status: pending, dikonfirmasi, dibatalkan, selesai

## Struktur Folder

```
reservasi_app/
├── ajax/
│   ├── rooms_ajax.php       # Handler AJAX CRUD ruangan
│   └── bookings_ajax.php    # Handler AJAX CRUD booking + cek bentrok
├── config/
│   └── database.php         # Koneksi PDO ke database
├── includes/
│   ├── header.php           # Navbar & head HTML (Bootstrap)
│   └── footer.php           # Footer & script bersama
├── index.php                # Dashboard
├── rooms.php                # Halaman data ruangan
├── bookings.php             # Halaman data reservasi
└── reservasi_ruangan.sql    # Script SQL (schema + data awal)
```

## Cara Instalasi

### Instalasi di Ubuntu (Apache2 + MariaDB Remote)

Panduan ini untuk instalasi di **Ubuntu 24.04** dengan **Apache2** sebagai web server dan **MariaDB** yang berjalan di server terpisah pada IP `10.100.1.18`.

#### 1. Update Sistem

```bash
sudo apt update -y
```

#### 2. Install Apache2 dan PHP

```bash
sudo apt install apache2 php libapache2-mod-php php-pdo php-mysql php-mbstring php-xml git mariadb-client-core  -y
```

#### 3. Clone Repositori

Clone repositori langsung ke direktori web Apache2:

```bash
sudo rm -rf /var/www/html/*
sudo rm -rf /var/www/html/.*
git clone https://github.com/paknux/appReservasi.git /var/www/html
```

Atur kepemilikan folder agar Apache bisa mengaksesnya:

```bash
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html
```

#### 4. Siapkan Database di Server MariaDB (10.100.1.18)

Akses MariaDB remote dari server web:

```bash
mysql -h 10.100.1.18 -u admin -padmin123
```

Setelah masuk, buat database-nya:

```sql
CREATE DATABASE IF NOT EXISTS db_reservasi_ruangan;
EXIT;
```

> User MariaDB yang digunakan adalah `admin` dengan password `admin123` yang sudah tersedia di server.

#### 5. Import File SQL ke Server MariaDB Remote

Dari server web (Ubuntu), jalankan:

```bash
mysql -h 10.100.1.18 -u admin -padmin123 db_reservasi_ruangan < /var/www/html/reservasi_ruangan.sql
```

File SQL akan otomatis:

- Membuat tabel `users`, `rooms`, `bookings`
- Mengisi beberapa data contoh (5 ruangan, 3 booking, 1 user admin)

> **Catatan:** Password pada tabel `users` disimpan dalam bentuk **plain text** (tanpa hash), cocok untuk keperluan belajar/demo. Untuk aplikasi produksi, sangat disarankan menggunakan hashing (`password_hash()` / `password_verify()`).

Login default aplikasi:
- Username: `admin`
- Password: `123`

#### 6. Konfigurasi Koneksi Database

Buka file `config/database.php`:

```bash
sudo nano /var/www/html/config/database.php
```

Sesuaikan dengan konfigurasi berikut:

```php
$DB_HOST = '10.100.1.18';           // IP server MariaDB
$DB_NAME = 'db_reservasi_ruangan';
$DB_USER = 'admin';
$DB_PASS = 'admin123';
```


#### 7. Akses Aplikasi

Buka browser dan akses:

```
http://<IP-webserver>
```

## Teknologi

| Komponen        | Versi / Sumber             |
|-----------------|----------------------------|
| Bootstrap       | 5.3.3 (CDN)                |
| Bootstrap Icons | 1.11.3 (CDN)               |
| jQuery          | 3.7.1 (CDN)                |
| PHP             | 7.4+ / 8.x (disarankan)   |
| Database        | MySQL 5.7+ / MariaDB 10.x |