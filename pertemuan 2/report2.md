# Laporan pertemuan ke -2 operasition sistem
**Tanggal:** 18 Februari 2026  
**Disusun Oleh:** Mukhammad Raffi Zabra
**NIM:** 254107020059
**Kelas/No:** TI-1G/24


## 1.1 Deteksi Perangkat Keras di Linux

## 1. Praktikum 2.1 — Identifikasi CPU dan Memori
1. tampilan informasi cpu:
![informasi cpu](images2/S1-1.png)

2. tampilan ringkasan memori:
![informasi memory](images2/S1-2.png)

3. cek informasi hardware dari DMI/BIOS:
![informasi hardware dari bios](images2/S1-3.png)

4. Catat: (1) jumlah CPU(s), core/thread, (2) total RAM, (3) total swap. Jelaskan perbedaan RAM vs swap dalam 2–3 kalimat.

jawab : jumlah cpu = 4, core/thread = 4/1, swap = 4GB.
perbedaan ram & swap = ram adalah memori fisik berkecepatan tinggi yang menyimpan data aplikasi yang sedang aktif agar bisa diakses secara instan oleh prosesor. sedangkan swap adalah area di penyimpanan SSD/HDD yang berfungsi sebagai memori cadangan ketika kapasitas RAM sudah habis terpakai. Karena menggunakan media penyimpanan fisik. seacara umum swap lebih lambat dibandingkan ram


## 2.2 — Identifikasi Perangkat PCI/USB dan Driver
1. Lihat daftar perangkat PCI:
![informasi perangkat PCI](images2/S2-1.png)

2. Lihat perangkat PCI beserta driver kernel yang digunakan:
![informasi perangkat pci beserta driver](images2/S2-2.png)

3. Fokus pada NIC (Ethernet) untuk mencari modul driver:
![informasi untuk memastikan ethernet terdeteksi dan memiliki driver yang sesuai](images2/S2-3.png)

4. Lihat perangkat USB:
![informasi untuk melihat perangkan usb](images2/S2-4.png)

5. Lihat topologi USB (tree):
![informasi untuk melihat topologi usb tree](images2/S2-5.png)

6. Temukan 1 perangkat PCI (misal NIC) dan tuliskan: Vendor:Device ID (angka heksadesimal), nama driver/modul kernel, dan deskripsi singkat fungsinya.

jawab : Nama perangkat = Intel Corporation 82540EM Gigabit Ethernet Controller, vendor:device id = 8086:100e, kernel driver = e1000, kernel moduls = e1000.
Deskripsi singkat& fungsi = pengontrol jaringan berkecepatan tinggi yang memungkinkan sistem untuk melakukan pertukaran data secara stabil. Fungsinya agar Ubuntu dapat memperoleh alamat IP melalui protokol DHCP dan memungkinkan akses jarak jauh melalui SSH.

## 2.3 — Identifikasi Storage dan Filesystem
1. Lihat daftar disk/partisi:
![informasi untuk melihat daftar blok perangkat beserta sistem filenya](images2/S3-1.png)

2. Tampilkan UUID dan tipe filesystem:
![informasi untuk mendapatkan identitas unik dari setiap blok penyimpanan](images2/S3-2.png)

3. Lihat mount point untuk root filesystem:
![informasi untuk memastikan partisi sistem terpasang dengan benar](images2/S3-3.png)


## 2.4 — Melihat Modul Aktif dan Informasinya
1. Cek versi kernel:
![informasi versi kernel](images2/S4-1.png)

2. Tampilkan daftar modul aktif:
![informasi daftar modul aktif](images2/S4-2.png)

3. Pilih salah satu modul (contoh aman: loop) dan lihat detailnya:
![informasi detail modul dengan modinfo](images2/S4-3.png)

4. Muat modul (jika belum aktif), lalu verifikasi:
![load modul dan verifikasi](images2/S4-4.png)

5. (Opsional) lihat pesan kernel terbaru:
![informasi log kernel terbaru](images2/S4-5.png)


## 2.5 — Konfigurasi Auto-load dan Blacklist:
1. konfigurasi autoload dan blacklist modul
![Melakukan konfigurasi pada modul kernel loop auto load dan blacklist modul.](images2/S5-1.png)


## 2.6 — Mengenali block vs character  device:
1. Lihat detail salah satu disk (sesuaikan dengan perangkat Anda, misal sda):
![Melihat detail device node disk](images2/S6-1.png)

2. Lihat detail device terminal:
![Melihat detail device node terminal](images2/S6-2.png) 

3. Lihat disk dan partisi untuk mengaitkan dengan /dev:
![Mapping disk/partisi](images2/S6-3.png)

4. Dari output ls -l, jelaskan perbedaan penanda file untuk block device dan
character device. (Hint: karakter pertama pada permission string)

jawab : ls -l /dev/sda	karakter pertama b jenis filenya Block Device (Penyimpanan),
        ls -l /dev/tty	karakter pertama c jenis filenya Character Device (Terminal)


## 2.7 — Melihat Informasi udev:
1. Cek atribut udev untuk disk:
![Melihat atribut udev untuk disk](images22/S7-1.png)

2. (Opsional) monitor event udev (jalankan, lalu colok/lepas USB pada mesin
fisik):
![Monitor event udev](images2/S7-2.png)


## 2.8 — Membuat Workspace Praktikum:
1. Buat direktori praktikum dan masuk ke dalamnya:
![Membuat workspace praktikum](images2/S8-1.png)

2. Buat beberapa file contoh:
![Membuat file contoh](images2/S8-2.png)

3. Isi file log contoh (simulasi):
![Mengisi file log contoh](images2/S8-3.png)

4. Baca file dengan less:
![Membaca file dengan less](images2/S8-4.png)


## 2.9 — Pencarian Pola dengan grep
1. cari barisan error,warn,info dengan grep:
![tampilan dengan grep](images2/S9-1.png)

2. Gunakan grep untuk menampilkan hanya baris yang mengandung INFO atau
WARN dari data.log. (Hint: gunakan grep -E dengan pola alternatif)

jawab: untuk menggunakan alternatif or kita menggunakan opsi -E dan | berfungsi sebagai atau.  berikut contohnya:
![contoh](images2/S9-2.png)


## 2.10 — Substitusi dengan sed (Aman di File Latihan)
1. konfigurasi langkah 1-4:
![konfigurasi dan edit](images2/S10-1.png)

2. Gunakan sed -i dengan hati-hati jika mengedit file sistem. Untuk administrasi
nyata, praktik aman adalah membuat backup: sed -i.bak ’s/old/new/g’
file.conf

jawab: hasil backup
![hasil backup](images2/S10-2.png)


## 2.11  — Ekstraksi Kolom dengan awk
1. langkah 1-3 hasil:
![hasil](images2/S11-1.png)


## 2.12 — Melihat Proses dengan ps
1. langkah 1-2 hasil:
![hasil](images2/S12-1.png)


## 2.13 — Monitoring Real-time dengan top
1. Jalankan top:
![menjalankan top](images2/S13-1.png)


## 2.14 — Menghentikan Proses dengan kill
1. langkah 1-5 hasil:
![hasil](images2/S14-1.png)


## 2.15 — Cek Disk, Load, dan Service
1. Cek penggunaan disk:
![cek kapasitas disk](images2/S15-1.png)

2. Cari direktori yang besar (contoh pada /var):
![Cek ukuran direktori](images2/S15-2.png)

3. Cek load dan uptime:
![cek load average](images2/S15-3.png)

4. Cek service yang gagal:
![service yang gagal](images2/S15-4.png)

5. Ambil log error terbaru:
![log error terbaru](images2/S15-5.png)


## 2.16 — Monitoring Port dan Koneksi (Network Basics)
1. langkah 1-3 hasil:
![hasil](images2/S16.png)

2. Pilih satu port yang listening dari output ss -tulpn(misal port 22), lalu
tuliskan service/proses yang membukanya. Jelaskan kegunaan port tersebut
secara singkat.

jawab: Port 22 dibuka oleh proses sshd, Port ini digunakan untuk layanan ssh yang memungkinkan akses remote ke server secara aman melalui jaringan.


## 1.9 Latihan
1. 2.A Jalankan lspci -nnk. Pilih 1 perangkat PCI dan tuliskan: nama perangkat, ID vendor:device, dan kernel driver in use.
![hasil](images2/sl-1.png)
jawab: Nama perangkat: Intel 82540EM Gigabit Ethernet Controller, ID vendor: device: 8086:100e, Kernel driver in use: e1000.


2. 2.B Tentukan device root filesystem dengan findmnt /. Lalu cocokkan dengan lsblk -f dan tuliskan tipe filesystem serta UUID-nya.
![hasil](images2/sl-2.png)
jawab: Device = /dev/sda2, FSTYPE = ext4, UUID = 975cb584-ddaf-445b-b8df-9a22b654882e.


3. 2.C Buat file server.log berisi minimal 10 baris dengan variasi kata: INFO, WARN, ERROR. Gunakan grep untuk menampilkan hanya baris ERROR.
- ![buat](images2/sl-3.png)
- ![hasil](images2/sl-3.1.png)


4. 2.D Gunakan sed untuk mengganti semua kata server menjadi node pada file latihan. Tunjukkan sebelum dan sesudah.
- ![sebelum](images2/sl-4.png)
- ![sesudah](images2/sl-4.1.png)


5. 2.E Gunakan df -h lalu awk untuk menampilkan filesystem yang penggunaan disk di atas 70%.
![informasi & filter disk](images2/sl-5.png)


6. 2.F Jalankan sleep 600 &. Temukan PID-nya dengan ps. Hentikan dengan SIGTERM. Jelaskan beda SIGTERM vs SIGKILL.
![hasil](images2/sl-6.png)
perbedaan sigterm dan sigkill = SIGTERM : proses bisa cleanup dulu
                                SIGKILL : langsung paksa mati 


7. 2.G Gunakan systemctl –failed. Jika tidak ada yang gagal, pilih satu service aktif (misal ssh) dan tampilkan status serta 30 baris log terakhirnya.
![hasil](images2/sl-7.png)