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
