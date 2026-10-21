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
rm -rf /var/www/html/*
rm -rf /var/www/html/.*
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
sudo nano /var/www/html/reservasi_app/config/database.php
```

Sesuaikan dengan konfigurasi berikut:

```php
$DB_HOST = '10.100.1.18';           // IP server MariaDB
$DB_NAME = 'db_reservasi_ruangan';
$DB_USER = 'admin';
$DB_PASS = 'admin123';
```

#### 7. (Opsional) Konfigurasi Virtual Host Apache2

Jika ingin mengakses aplikasi via domain atau path khusus, buat Virtual Host baru:

```bash
sudo nano /etc/apache2/sites-available/reservasi.conf
```

Isi dengan:

```apache
<VirtualHost *:80>
    ServerName reservasi.domain.local
    DocumentRoot /var/www/html/reservasi_app

    <Directory /var/www/html/reservasi_app>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/reservasi_error.log
    CustomLog ${APACHE_LOG_DIR}/reservasi_access.log combined
</VirtualHost>
```

Aktifkan site dan modul rewrite:

```bash
sudo a2ensite reservasi.conf
sudo a2enmod rewrite
sudo systemctl reload apache2
```

#### 8. Akses Aplikasi

Buka browser dan akses:

```
http://<IP-server-web>/reservasi_app/
```

Atau jika menggunakan Virtual Host:

```
http://reservasi.domain.local/
```

---

### Instalasi di Windows Lokal (XAMPP / Laragon)

#### 1. Siapkan Database

Import file `reservasi_ruangan.sql` ke server MySQL/MariaDB kamu.

> **Catatan:** File SQL sudah menyertakan perintah `CREATE DATABASE` secara otomatis.
> Jika database `db_reservasi_ruangan` belum ada, akan dibuat saat proses import.
> Jika sudah ada, pastikan tidak ada konflik nama tabel.

---

**Lewat Terminal / CLI:**

Jika database belum ada, buat dulu secara manual:

```bash
mysql -u root -p -e "CREATE DATABASE IF NOT EXISTS db_reservasi_ruangan;"
```

Lalu import file SQL-nya:

```bash
mysql -u root -p db_reservasi_ruangan < reservasi_ruangan.sql
```

---

**Lewat phpMyAdmin:**

1. Buka phpMyAdmin di browser (biasanya `http://localhost/phpmyadmin`)
2. Jika database belum ada: klik tab **Databases** → isi nama `db_reservasi_ruangan` → klik **Create**
3. Klik database `db_reservasi_ruangan` di panel kiri
4. Pilih menu **Import** di bagian atas
5. Klik **Choose File** → pilih file `reservasi_ruangan.sql`
6. Klik **Go / Kirim**

---

**Lewat HeidiSQL:**

1. Buka HeidiSQL → hubungkan ke server MySQL/MariaDB kamu
2. Jika database belum ada: klik kanan pada koneksi di panel kiri → **Create new** → **Database** → isi nama `db_reservasi_ruangan` → **OK**
3. Klik kanan pada database `db_reservasi_ruangan` → pilih **Run SQL file...**
4. Pilih file `reservasi_ruangan.sql` → klik **Open**
5. Tunggu hingga proses selesai, pastikan tidak ada error di tab output

---

**Lewat MySQL Front:**

1. Buka MySQL Front → login ke server kamu
2. Jika database belum ada: klik kanan pada server → **New** → **Database** → isi nama `db_reservasi_ruangan` → **OK**
3. Pilih database `db_reservasi_ruangan` di panel kiri
4. Klik menu **File** → **Run Script...** (atau tekan `Ctrl+F3`)
5. Pilih file `reservasi_ruangan.sql` → klik **Open**
6. Konfirmasi eksekusi → tunggu hingga selesai

---

#### 2. Konfigurasi Koneksi Database

Buka file `config/database.php`, sesuaikan dengan kredensial server database kamu:

```php
$DB_HOST = 'localhost';
$DB_NAME = 'db_reservasi_ruangan';
$DB_USER = 'root';   // ganti sesuai user MySQL/MariaDB kamu
$DB_PASS = '';       // ganti sesuai password MySQL/MariaDB kamu
```

#### 3. Jalankan Aplikasi

- Letakkan folder `reservasi_app` ke dalam direktori web server:
  - XAMPP → `htdocs/`
  - Laragon → `www/`
- Akses melalui browser: `http://localhost/reservasi_app/`

---

## Alur Penggunaan

1. **Dashboard** — melihat ringkasan statistik dan reservasi terbaru
2. **Data Ruangan** — tambah ruangan baru (nama, tipe, kapasitas, harga per jam, fasilitas, status)
3. **Reservasi** — buat booking dengan memilih ruangan dan rentang waktu; sistem otomatis mengecek apakah jadwal bentrok sebelum data disimpan

## Catatan Pengembangan Lanjutan (Opsional)

Beberapa hal yang bisa ditambahkan sesuai kebutuhan:

- Halaman login berbasis tabel `users` (session-based auth)
- Tampilan kalender visual (misal pakai FullCalendar.js) untuk jadwal booking per ruangan
- Export data reservasi ke Excel/PDF
- Upload gambar ruangan (kolom `gambar` sudah tersedia di tabel `rooms`)
- Notifikasi email saat status booking berubah

## Teknologi

| Komponen        | Versi / Sumber             |
|-----------------|----------------------------|
| Bootstrap       | 5.3.3 (CDN)                |
| Bootstrap Icons | 1.11.3 (CDN)               |
| jQuery          | 3.7.1 (CDN)                |
| PHP             | 7.4+ / 8.x (disarankan)   |
| Database        | MySQL 5.7+ / MariaDB 10.x |