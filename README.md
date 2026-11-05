# Tutorial Instalasi dan Konfigurasi FlyEnv di Arch Linux: Alternatif XAMPP

## Pendahuluan

FlyEnv merupakan aplikasi pengembangan lingkungan lokal yang membantu developer menjalankan berbagai layanan web seperti PHP, MySQL, MariaDB, Nginx, dan Apache.

Bagi pengguna Arch Linux, FlyEnv dapat menjadi alternatif XAMPP dengan antarmuka grafis untuk mengelola berbagai runtime dan database.

Dalam tutorial ini, kita akan membahas instalasi FlyEnv di Arch Linux, konfigurasi PHP, aktivasi ekstensi PDO MySQL, serta persiapan database untuk proyek PHP.

## 1. Instalasi FlyEnv

FlyEnv dapat dipasang melalui Arch User Repository (AUR).

Pastikan AUR helper seperti `yay` sudah tersedia.

Jalankan:

```bash
yay -Ss flyenv
```

Pilih paket binary:

```bash
yay -S flyenv-bin
```

Periksa instalasi:

```bash
pacman -Q flyenv-bin
```

Pada pengujian ini, versi yang digunakan adalah:

- FlyEnv: 4.20.2
- Sistem operasi: Arch Linux
- Desktop environment: Hyprland

## 2. Menjalankan FlyEnv

Setelah instalasi selesai, buka FlyEnv melalui menu aplikasi desktop.

FlyEnv menyediakan pengelolaan beberapa layanan, antara lain:

- PHP dan PHP-FPM
- MySQL
- MariaDB
- Nginx
- Apache

Layanan tersebut dapat dikelola melalui antarmuka grafis.

## 3. Memeriksa PHP

Periksa versi PHP melalui terminal:

```bash
php -v
```

Pada pengujian ini, PHP yang tersedia adalah PHP 8.5.12.

Periksa konfigurasi PHP:

```bash
php --ini
```

File konfigurasi utama:

```text
/etc/php/php.ini
```

Direktori konfigurasi ekstensi tambahan:

```text
/etc/php/conf.d
```

## 4. Mengaktifkan PDO MySQL

PHP membutuhkan ekstensi PDO MySQL agar aplikasi dapat berkomunikasi dengan MySQL atau MariaDB.

Periksa keberadaan modul:

```bash
ls /usr/lib/php/modules/pdo_mysql.so
```

Jika tersedia, aktifkan:

```bash
echo 'extension=pdo_mysql' | sudo tee /etc/php/conf.d/pdo_mysql.ini
```

Verifikasi:

```bash
php -m | grep -Ei 'pdo|mysql'
```

Hasil yang diharapkan:

```text
mysqlnd
PDO
pdo_mysql
```

Dengan demikian, PHP CLI sudah memiliki dukungan PDO MySQL.

Catatan: PHP CLI dan PHP-FPM dapat menggunakan konfigurasi yang berbeda. Pastikan ekstensi juga tersedia pada runtime PHP-FPM yang digunakan FlyEnv.

## 5. Menyiapkan Database

Dalam pengujian ini, database dikelola melalui instalasi MySQL milik FlyEnv.

Lokasi direktori data yang terdeteksi:

```text
/home/remu/.config/FlyEnv/server/mysql/data-26.10/
```

Port database:

```text
3306
```

Masuk ke database:

```bash
/home/linuxbrew/.linuxbrew/bin/mysql -u root -p
```

Buat database:

```sql
CREATE DATABASE flyenv_test;
```

Periksa database:

```sql
SHOW DATABASES;
```

## 6. Mengimpor Database Proyek

Contoh proyek menggunakan database bernama `flyenv_test` dan tabel `tasks`.

Keluar dari MySQL:

```sql
EXIT;
```

Impor file SQL:

```bash
/home/linuxbrew/.linuxbrew/bin/mysql -u root -p flyenv_test < "/home/remu/Code RPL/XAMPP/database.sql"
```

Periksa tabel:

```bash
/home/linuxbrew/.linuxbrew/bin/mysql -u root -p flyenv_test
```

Kemudian:

```sql
SHOW TABLES;
```

Hasil pengujian:

```text
+-----------------------+
| Tables_in_flyenv_test |
+-----------------------+
| tasks                 |
+-----------------------+
```

Database dan tabel berhasil dibuat.

## 7. Menghubungkan PHP dengan Database

Contoh konfigurasi `config.php`:

```php
<?php
declare(strict_types=1);

define('DB_HOST', '127.0.0.1');
define('DB_PORT', '3306');
define('DB_NAME', 'flyenv_test');
define('DB_USER', 'root');
define('DB_PASS', 'YOUR_DATABASE_PASSWORD');

function db(): PDO {
    static $pdo = null;

    if ($pdo instanceof PDO) {
        return $pdo;
    }

    $dsn = 'mysql:host=' . DB_HOST .
           ';port=' . DB_PORT .
           ';dbname=' . DB_NAME .
           ';charset=utf8mb4';

    $pdo = new PDO($dsn, DB_USER, DB_PASS, [
        PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
        PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
        PDO::ATTR_EMULATE_PREPARES => false,
    ]);

    return $pdo;
}
```

Ganti `YOUR_DATABASE_PASSWORD` dengan password database yang sebenarnya.

Jangan mengunggah password database ke repositori publik.

## 8. Struktur Proyek

Contoh struktur proyek PHP:

```text
XAMPP/
├── api/
│   ├── health.php
│   └── tasks.php
├── assets/
│   ├── app.js
│   └── style.css
├── config.php
├── database.sql
├── index.php
└── README.md
```

Proyek ini menggunakan:

- PHP untuk backend.
- JavaScript untuk interaksi pengguna.
- CSS untuk tampilan.
- MySQL untuk penyimpanan data.

## 9. Kesimpulan

FlyEnv merupakan alternatif menarik untuk mengelola lingkungan pengembangan web di Arch Linux.

Dalam pengujian ini, beberapa komponen telah berhasil disiapkan:

- FlyEnv 4.20.2 berhasil dipasang.
- PHP 8.5.12 tersedia.
- Ekstensi PDO MySQL berhasil diaktifkan.
- Database `flyenv_test` berhasil dibuat.
- Tabel `tasks` berhasil diimpor.

Konfigurasi situs lokal melalui fitur Add Site dan pengujian akses melalui browser masih memerlukan penyelesaian lebih lanjut.

FlyEnv dapat menjadi pilihan bagi pengguna Linux yang ingin mengelola lingkungan pengembangan web melalui GUI tanpa harus selalu mengatur setiap layanan secara manual melalui terminal.

---

**Lingkungan pengujian:** Arch Linux, Hyprland, FlyEnv 4.20.2, PHP 8.5.12, MySQL Homebrew/FlyEnv 26.10.0.
