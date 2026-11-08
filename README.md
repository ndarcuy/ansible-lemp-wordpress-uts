# ansible-lemp-wordpress-uts
Otomasi deployment multi-tier LEMP Stack (Nginx + PHP) dan MariaDB Database Server untuk WordPress menggunakan Vagrant dan Ansible Tugas UTS Komputasi Awan

Repositori ini berisi seluruh berkas konfigurasi infrastruktur sebagai kode (*Infrastructure as Code*) untuk mendistribusikan arsitektur multi-tier web server dan database server menggunakan **Vagrant** dan **Ansible**.

---

## 👨‍💻 Identitas Mahasiswa
* **Nama** : Cahyo Ndaru Suryo Bawono
* **NIM** : 112025002
* **Program Studi / Kampus** : Politeknik NSC Surabaya
* **Mata Kuliah** : Komputasi Awan

---

## 📌 Deskripsi Proyek
Proyek ini mengimplementasikan pemisahan layanan web dan basis data ke dalam dua server target yang berbeda (*Multi-Tier Architecture*):
1. **VM 1 (Web Server)**: Menjalankan Nginx, modul PHP-FPM, dan source code aplikasi web WordPress.
2. **VM 2 (Database Server)**: Menjalankan MariaDB Server yang dikonfigurasi untuk menerima koneksi remote database secara aman.
3. **Control Node**: Mesin pengelola utama berbasis Ansible yang secara otomatis mengonfigurasi kedua server target melalui protokol SSH.

Pemisahan ini bertujuan untuk meningkatkan keamanan (isolasi lapis ganda), mencegah perebutan sumber daya sistem (*resource contention*), serta memudahkan skalabilitas dan pemeliharaan server secara independen.

---

## 📐 Topologi & Spesifikasi Jaringan

Sistem dibangun di atas lingkungan virtualisasi **Oracle VM VirtualBox** dengan alokasi jaringan privat *Host-Only*:

| Node / Virtual Machine | Sistem Operasi | Peran Sistem | Alamat IP Host-Only |
| :--- | :--- | :--- | :--- |
| **Control Node (`komputasiAwan`)** | Debian | Ansible Controller | `192.168.56.5` |
| **Target VM 1 (`belajar_vagrant`)** | Debian (`alvistack/debian-13`) | Web Server (Nginx + PHP) | `192.168.56.10` |
| **Target VM 2 (`uts_dbserver`)** | Debian (`debian/bullseye64`) | Database Server (MariaDB) | `192.168.56.11` |

---

## 📁 Struktur Berkas Repositori

```text
├── Vagrantfile.web       # Konfigurasi Vagrant untuk provisioning VM Web Server
├── Vagrantfile.db        # Konfigurasi Vagrant untuk provisioning VM DB Server
├── inventory.ini         # Berkas Ansible inventory pemetaan grup target & kredensial SSH
├── playbook.yaml         # Playbook otomasi LEMP stack & setup WordPress
└── README.md             # Dokumentasi teknis proyek
```

## ⚙️ Ringkasan Alur Otomasi (Playbook)
Playbook playbook.yaml mengeksekusi tahapan otomatisasi berikut:

1. Database Server (192.168.56.11)
Memperbarui cache repositori paket (apt update).

Menginstalasi paket mariadb-server dan python3-pymysql.

Mengaktifkan dan memastikan layanan sistem MariaDB berjalan (running & enabled).

Membuka parameter bind-address = 0.0.0.0 pada konfigurasi MariaDB agar port 3306 dapat diakses dari Web Server.

Membuat basis data baru: wordpress_db.

Membuat pengguna database baru: wp_user dengan izin akses penuh (ALL PRIVILEGES) dari host remote (%).

2. Web Server (192.168.56.10)
Menginstalasi paket web server nginx, mesin pemroses php-fpm, driver php-mysql, serta pustaka utilitas curl dan tar.

Mengunduh arsip WordPress rilis terbaru langsung dari sumber resmi (https://wordpress.org/latest.tar.gz).

Mengekstrak arsip ke direktori /var/www/html/wordpress.

Mengonfigurasi parameter berkas wp-config.php:

DB_NAME = wordpress_db

DB_USER = wp_user

DB_PASSWORD = wp_password123

DB_HOST = 192.168.56.11 (menunjuk ke IP Database Server)

Memberikan izin kepemilikan direktori kepada akun layanan web server (www-data:www-data).

Mengonfigurasi Virtual Host Nginx untuk menyajikan WordPress dan meneruskan skrip PHP ke socket aktif PHP-FPM.

Memuat ulang (restart) layanan Nginx.

##🚀 Panduan Menjalankan Proyek

1. Inisialisasi Mesin Target (Host OS Windows)

Jalankan VM Web Server pada direktori proyek web:
cd C:\direktori_web_mu\foldermu
vagrant up

Jalankan VM Database Server pada direktori Proyek DB:
cd C:\direktori_db_mu\foldermu
vagrant up

2. Eksekusi Ansible(Control Node)

Buka terminal pada Control Node, masuk ke direktori kerja proyek:
cd ~/proyekmu

Uji konektivitas Ansible ke seluruh node target:
ansible all -i inventory.ini -m ping

jalankan playbook otomatisasi LEMP dan WordPress:
ansible-playbook -i inventory.ini playbook.yaml

📊 Hasil Pengujian & Verifikasi
Hasil Eksekusi Ansible (PLAY RECAP):

Target 192.168.56.10: ok=16, changed=13, unreachable=0, failed=0

Target 192.168.56.11: ok=8, changed=5, unreachable=0, failed=0

Pengujian Respons HTTP:

Respons curl lokal http://localhost dari Web Server mengembalikan status HTTP/1.1 302 Found yang diarahkan langsung oleh WordPress ke /wp-admin/install.php.

Akses Browser:

WordPress berhasil diakses dari peramban sistem Host di alamat http://192.168.56.10/wp-admin/install.php dan instalasi berhasil terhubung penuh ke MariaDB di VM DB Server.
