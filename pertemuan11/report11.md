# Laporan Pertemuan ke-11 Sistem Operasi

**Tanggal:** 06 Mei 2026  
**Disusun Oleh:** Mukhammad Raffi Zabra  
**NIM:** 254107020059  
**Kelas/No:** TI-1G/24  

---

# Praktikum 9.1 — Permissions
**Langkah 1: Buat direktori kerja dan dua file uji.**
```bash
Rappizr7@ubuntu-server:~/praktikum-os/pertemuan11$ mkdir -p ~/lab-permissions && cd ~/lab-permissions
echo "data rahasia" > secret.txt
echo '#!/bin/bash' > myscript.sh
echo 'echo Hello' >> myscript.sh
ls -la
total 16
drwxrwxr-x  2 Rappizr7 Rappizr7 4096 May  6 06:20 .
drwxr-x--- 18 Rappizr7 Rappizr7 4096 May  6 06:20 ..
-rw-rw-r--  1 Rappizr7 Rappizr7   23 May  6 06:20 myscript.sh
-rw-rw-r--  1 Rappizr7 Rappizr7   13 May  6 06:20 secret.txt
```

**Langkah 2: Jadikan secret.txt privat hanya untuk owner.**
```bash
Rappizr7@ubuntu-server:~/lab-permissions$ chmod 600 secret.txt
Rappizr7@ubuntu-server:~/lab-permissions$ ls -l secret.txt
-rw------- 1 Rappizr7 Rappizr7 13 May  6 06:20 secret.txt
```

**Langkah 3: Jadikan myscript.sh dapat dijalankan.**
```bash
Rappizr7@ubuntu-server:~/lab-permissions$ chmod 755 myscript.sh
ls -l myscript.sh
./myscript.sh
-rwxr-xr-x 1 Rappizr7 Rappizr7 23 May  6 06:20 myscript.sh
Hello
```

**Langkah 4: Buat direktori bersama dan amati efek SGID sederhana.**
```bash
Rappizr7@ubuntu-server:~/lab-permissions$ mkdir shared-dir
chmod g+s shared-dir
ls -ld shared-dir
drwxrwsr-x 2 Rappizr7 Rappizr7 4096 May  6 06:34 shared-dir
```

**Langkah 5: Uji efek umask pada file baru**
```bash
Rappizr7@ubuntu-server:~/lab-permissions$ umask
umask 027
touch testfile-027
ls -l testfile-027
0002
-rw-r----- 1 Rappizr7 Rappizr7 0 May  6 06:35 testfile-027
```

**Analisis**
1. Mengapa secret.txt tidak dapat dibaca oleh group dan others setelah chmod 600?
```bash 
Karena angka 0 pada chmod 600 artinya "tidak ada izin sama sekali" bagi Group dan Others. Hanya Pemilik 
yang boleh baca dan tulis. jika digit izinnya nol, maka siapa pun selain 
pemilik akan ditolak aksesnya.
```

2. Apa perbedaan arti 600 dan 755 terhadap file yang diuji?
```bash
- 600: Fokus pada Privasi. Hanya pemilik yang bisa baca/tulis. File tidak bisa dijalankan sebagai program. 

- 755: Fokus pada Berbagi & Menjalankan. Semua orang bisa baca dan menjalankan file tersebut, 
tapi hanya pemilik yang boleh mengubah isinya. 
```

3. Setelah umask 027, permission apa yang dihasilkan untuk file baru, dan mengapa bukan 777?
```bash
- hasilnya 640 (rw-r-----).

- Kenapa bukan 777? Karena demi Keamanan. Linux tidak pernah memberikan izin "eksekusi" secara 
otomatis pada file baru agar virus atau skrip berbahaya tidak bisa langsung berjalan sendiri tanpa 
izin manual dari pemiliknya.
```

# Praktikum 9.2 — ACL
**Langkah 1: Siapkan file dan lihat permission standar tanpa ACL tambahan.**
```bash
Rappizr7@ubuntu-server:~/praktikum-os/pertemuan11$ mkdir -p ~/lab-acl && cd ~/lab-acl
Rappizr7@ubuntu-server:~/lab-acl$ echo "Data penting" > confidential.txt
Rappizr7@ubuntu-server:~/lab-acl$ chmod 640 confidential.txt
Rappizr7@ubuntu-server:~/lab-acl$ ls -l confidential.txt
-rw-r----- 1 Rappizr7 Rappizr7 13 May  6 12:29 confidential.txt
Rappizr7@ubuntu-server:~/lab-acl$ getfacl confidential.txt
# file: confidential.txt
# owner: Rappizr7
# group: Rappizr7
user::rw-
group::r--
other::---
```

**Langkah 2: Beri akses baca ke satu user tertentu tanpa mengubah owner atau group.**
```bash
Rappizr7@ubuntu-server:~/lab-acl$ setfacl -m u:Rappizr7:r confidential.txt
Rappizr7@ubuntu-server:~/lab-acl$ ls -l confidential.txt
-rw-r-----+ 1 Rappizr7 Rappizr7 13 May  6 12:29 confidential.txt
Rappizr7@ubuntu-server:~/lab-acl$ getfacl confidential.txt
# file: confidential.txt
# owner: Rappizr7
# group: Rappizr7
user::rw-
user:Rappizr7:r--
group::r--
mask::r--
other::---
```

**Langkah 3: Buat direktori bersama yang mewariskan ACL ke file baru.**
```bash 
Rappizr7@ubuntu-server:~/lab-acl$ mkdir shared
Rappizr7@ubuntu-server:~/lab-acl$ sudo setfacl -d -m u:Rappizr7:rwx shared
Rappizr7@ubuntu-server:~/lab-acl$ sudo setfacl -d -m u:www-data:r-x shared
Rappizr7@ubuntu-server:~/lab-acl$ getfacl shared
# file: shared
# owner: Rappizr7
# group: Rappizr7
user::rwx
group::rwx
other::r-x
default:user::rwx
default:user:www-data:r-x
default:user:Rappizr7:rwx
default:group::rwx
default:mask::rwx
default:other::r-x

Rappizr7@ubuntu-server:~/lab-acl$ touch shared/inherited.txt
Rappizr7@ubuntu-server:~/lab-acl$ getfacl shared/inherited.txt
# file: shared/inherited.txt
# owner: Rappizr7
# group: Rappizr7
user::rw-
user:www-data:r-x               #effective:r--
user:Rappizr7:rwx               #effective:rw-
group::rwx                      #effective:rw-
mask::rw-
other::r--
```

**Analisis**
1. Mengapa getfacl confidential.txt awalnya tidak menampilkan user tertentu?
```bash
Karena saat pertama kali dibuat, file tersebut hanya pakai aturan standar (Owner, Group, Others). 
Belum ada "tamu khusus" yang didaftarkan. getfacl baru akan memunculkan nama user tertentu jika kita 
sudah memberikan izin khusus menggunakan perintah setfacl.
```
2. Setelah setfacl -m u:userA:r confidential.txt, apa perbedaan output ls -l dan getfacl?
```bash
- ls -l: Hanya memberi tahu lewat simbol. Kamu akan melihat tanda plus (+) di ujung izin file 
(contoh: -rw-r-----+). Itu tandanya: "Ada aturan tambahan di sini, tapi saya tidak bisa kasih 
tahu detailnya siapa."

- getfacl: Memberi tahu detailnya. Ia akan membongkar daftar siapa saja user tambahan yang punya 
izin dan apa saja hak mereka (misal: user:www-data:r-x).
```

3. Mengapa file inherited.txt mewarisi ACL dari direktori shared?
```bash
Karena folder induknya (shared) sudah dipasangi "cetakan otomatis" yang disebut Default ACL (opsi -d).
```

# Praktikum 9.3A — Membuat dan Mengelola User
**Tujuan: membuat user baru, memodifikasi propertinya, dan memahami perbedaan opsi useradd dan usermod.**
```bash
Rappizr7@ubuntu-server:~/lab-acl$ sudo useradd -m -s /bin/bash userA
Rappizr7@ubuntu-server:~/lab-acl$ sudo useradd -m -s /bin/bash userB
Rappizr7@ubuntu-server:~/lab-acl$ sudo passwd userA
New password:
Retype new password:
passwd: password updated successfully
Rappizr7@ubuntu-server:~/lab-acl$ sudo passwd userB
New password:
Retype new password:
passwd: password updated successfully
Rappizr7@ubuntu-server:~/lab-acl$ id userA
uid=1001(userA) gid=1001(userA) groups=1001(userA)
Rappizr7@ubuntu-server:~/lab-acl$ getent passwd userA
userA:x:1001:1001::/home/userA:/bin/bash
Rappizr7@ubuntu-server:~/lab-acl$ sudo usermod -s /bin/zsh userA
usermod: Warning: missing or non-executable shell '/bin/zsh'
Rappizr7@ubuntu-server:~/lab-acl$ getent passwd userA
userA:x:1001:1001::/home/userA:/bin/zsh
Rappizr7@ubuntu-server:~/lab-acl$ sudo usermod -L userB
Rappizr7@ubuntu-server:~/lab-acl$ sudo passwd -S userB
userB L 2026-05-06 0 99999 7 -1
Rappizr7@ubuntu-server:~/lab-acl$ sudo usermod -U userB
Rappizr7@ubuntu-server:~/lab-acl$ sudo passwd -S userB
userB P 2026-05-06 0 99999 7 -1
```

**Pertanyaan:**
1. Apa perbedaan output id userA sebelum dan sesudah menambah group?
```bash
- Sebelum: Hanya menampilkan satu grup, yaitu grup aslinya sendiri (userA).

- Sesudah (jika ditambah grup): Akan muncul nama grup baru di bagian groups=... (misalnya ditambah grup sudo atau admin).
```

2. Bagaimana status passwd -S userB berubah saat akun di-lock?
```bash
- Saat di-Lock: Muncul huruf L. Artinya akun digembok dan tidak bisa login.

- Saat di-Unlock: Muncul huruf P. Artinya gembok dibuka dan akun bisa digunakan lagi.
```

# Praktikum 9.3B — Group Management
**Tujuan: membuat group, menambahkan user ke group, dan memverifikasi keanggotaan.**
```bash
Rappizr7@ubuntu-server:~/lab-acl$ sudo groupadd labgroup
Rappizr7@ubuntu-server:~/lab-acl$ sudo groupadd readonly-group
Rappizr7@ubuntu-server:~/lab-acl$ sudo usermod -aG labgroup,readonly-group userA
Rappizr7@ubuntu-server:~/lab-acl$ sudo usermod -aG readonly-group userB
Rappizr7@ubuntu-server:~/lab-acl$ id userA
uid=1001(userA) gid=1001(userA) groups=1001(userA),1003(labgroup),1004(readonly-group)
Rappizr7@ubuntu-server:~/lab-acl$ id userB
uid=1002(userB) gid=1002(userB) groups=1002(userB),1004(readonly-group)
Rappizr7@ubuntu-server:~/lab-acl$ getent group labgroup
labgroup:x:1003:userA
Rappizr7@ubuntu-server:~/lab-acl$ getent group readonly-group
readonly-group:x:1004:userA,userB
```

**Pertanyaan:**
1. Apa yang ditampilkan id userA vs groups userA?
```bash
- id userA: Menampilkan detail teknis lengkap, yaitu angka ID (UID/GID) dan nama semua grup yang 
diikuti. Sangat berguna untuk pengecekan sistem.

- groups userA: Hanya menampilkan nama-nama grup saja tanpa angka ID. Lebih mudah dibaca oleh 
manusia secara cepat.
```

2. Mengapa -a pada usermod -aG penting?
```bash
- -a artinya "Menambah". Tanpa -a, Linux akan menghapus user dari semua grup lamanya dan 
hanya memasukkannya ke grup yang baru saja kita ketik. Dengan -a, user tetap berada di grup lama 
(seperti grup sudo atau grup utama) sambil menambah keanggotaan di grup baru.
```

# Praktikum 9.3C — Password Aging Policy
**Tujuan: menerapkan kebijakan umur password dan mengamati efeknya.**
```bash
Rappizr7@ubuntu-server:~/lab-acl$ sudo chage -M 60 -W 7 -m 1 userA
Rappizr7@ubuntu-server:~/lab-acl$ sudo chage -l userA
Last password change                                    : May 06, 2026
Password expires                                        : Jul 05, 2026
Password inactive                                       : never
Account expires                                         : never
Minimum number of days between password change          : 1
Maximum number of days between password change          : 60
Number of days of warning before password expires       : 7
Rappizr7@ubuntu-server:~/lab-acl$ sudo chage -d 0 userA
Rappizr7@ubuntu-server:~/lab-acl$ sudo passwd -l userB
passwd: password changed.
Rappizr7@ubuntu-server:~/lab-acl$ sudo passwd -S userB
userB L 2026-05-06 0 99999 7 -1
Rappizr7@ubuntu-server:~/lab-acl$ sudo passwd -u userB
passwd: password changed.
Rappizr7@ubuntu-server:~/lab-acl$ sudo passwd -S userB
userB P 2026-05-06 0 99999 7 -1
```

**Pertanyaan:**
1. Apa arti nilai yang ditampilkan chage -l userA?
```bash
Nilai tersebut adalah aturan masa berlaku password, yaitu:
- Max Days (60): Password wajib diganti setiap 60 hari sekali.
- Min Days (1): User tidak boleh ganti password berkali-kali dalam sehari.
- Warning Days (7): Sistem akan mulai "mengingatkan" user untuk ganti password 7 hari sebelum masa berlaku habis.
```

2. Bagaimana cara membuktikan userB terkunci dari output passwd -S?
```bash
Cukup lihat huruf kode setelah nama user pada output passwd -S:
- Jika muncul huruf L: Artinya Locked (Terkunci). User tidak bisa login.
- Jika muncul huruf P: Artinya Password usable (Aktif). User bisa login kembali.
```
3. Kapan sebaiknya menggunakan chage -d 0 vs passwd -e?
```bash
Keduanya punya fungsi yang sama, yaitu memaksa user ganti password saat login berikutnya.
- chage -d 0: Lebih sering digunakan di skrip otomatis atau admin karena memanipulasi 
tanggal "perubahan terakhir" menjadi angka 0.
- passwd -e: Lebih mudah diingat. Perintah ini adalah cara standar dan paling cepat jika 
hanya ingin membuat password satu user segera kedaluwarsa.
```

# Praktikum 9.4 — Konfigurasi sudo
**Langkah 1: Buat file konfigurasi sudo khusus untuk userA**
```bash
Rappizr7@ubuntu-server:~/lab-acl$ sudo visudo -f /etc/sudoers.d/lab-userA
[sudo] password for Rappizr7:
- isi
userA ALL=(root) NOPASSWD: /usr/bin/apt update, /usr/bin/apt upgrade
userA ALL=(root) /bin/systemctl status *
```

**Langkah 2: Verifikasi aturan yang aktif dan uji hasilnya.**
```bash
Rappizr7@ubuntu-server:~/lab-acl$ sudo -l -U userA
Matching Defaults entries for userA on ubuntu-server:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User userA may run the following commands on ubuntu-server:
    (root) NOPASSWD: /usr/bin/apt update, /usr/bin/apt upgrade
    (root) /bin/systemctl status *
Rappizr7@ubuntu-server:~/lab-acl$ sudo grep "userA" /var/log/auth.log | tail -10
2026-05-06T13:16:46.669481+00:00 ubuntu-server usermod[2371]: add 'userA' to shadow group 'labgroup'
2026-05-06T13:16:46.670371+00:00 ubuntu-server usermod[2371]: add 'userA' to shadow group 'readonly-group'
2026-05-06T13:28:30.931109+00:00 ubuntu-server sudo: Rappizr7 : TTY=pts/2 ; PWD=/home/Rappizr7/lab-acl ; USER=root ; COMMAND=/usr/bin/chage -M 60 -W 7 -m 1 userA
2026-05-06T13:28:31.001532+00:00 ubuntu-server chage[2414]: changed password expiry for userA
2026-05-06T13:28:38.119483+00:00 ubuntu-server sudo: Rappizr7 : TTY=pts/2 ; PWD=/home/Rappizr7/lab-acl ; USER=root ; COMMAND=/usr/bin/chage -l userA
2026-05-06T13:28:45.441165+00:00 ubuntu-server sudo: Rappizr7 : TTY=pts/2 ; PWD=/home/Rappizr7/lab-acl ; USER=root ; COMMAND=/usr/bin/chage -d 0 userAsudo chage -d 0 userA
2026-05-06T13:28:49.110900+00:00 ubuntu-server sudo: Rappizr7 : TTY=pts/2 ; PWD=/home/Rappizr7/lab-acl ; USER=root ; COMMAND=/usr/bin/chage -d 0 userA
2026-05-06T13:28:49.169440+00:00 ubuntu-server chage[2427]: changed password expiry for userA
2026-05-06T13:45:08.515610+00:00 ubuntu-server sudo: Rappizr7 : TTY=pts/2 ; PWD=/home/Rappizr7/lab-acl ; USER=root ; COMMAND=/usr/sbin/visudo -f /etc/sudoers.d/lab-userA
2026-05-06T13:54:48.374150+00:00 ubuntu-server sudo: Rappizr7 : TTY=pts/2 ; PWD=/home/Rappizr7/lab-acl ; USER=root ; COMMAND=/usr/bin/grep userA /var/log/auth.log
```

**Analisis**
1. Mengapa aturan disimpan di /etc/sudoers.d//, bukan langsung di /etc/sudoers?
```bash
- Lebih Aman: Mengurangi risiko file utama rusak akibat salah ketik yang bisa membuat sistem terkunci.
- Lebih Rapi: Memudahkan admin mengelola akses. Jika akses user tidak lagi dibutuhkan, cukup hapus 
file kecil tersebut tanpa mengutak-atik file konfigurasi utama.
``` 

2. Mana perintah yang bisa dijalankan tanpa password, dan mana yang masih perlu autentikasi?
```bash
Berdasarkan output sudo -l:
- Tanpa Password: Perintah apt update dan apt upgrade. UserA bisa langsung menjalankan 
ini secara instan.
- Perlu Autentikasi: Perintah systemctl status *. UserA bisa menjalankannya, tapi sistem akan tetap 
meminta password UserA sebagai verifikasi keamanan.
```

3. Informasi apa saja yang dicatat di log sudo?
```bash
Berdasarkan isi /var/log/auth.log yang tampilkan:
- Waktu kejadian: Kapan perintah dijalankan.
- Identitas: Siapa yang menjalankan perintah (Rappizr7) dan sebagai siapa perintah itu dijalankan (root).
- Lokasi Terminal: Di mana perintah diketik (TTY=pts/2).
- Isi Perintah: Perintah spesifik apa yang dieksekusi.
```

# Praktikum 9.5 — Disk Quota
**Langkah 1: Buat image filesystem kecil dan mount dengan opsi quota.**
```bash
Rappizr7@ubuntu-server:~/lab-acl$ sudo dd if=/dev/zero of=/tmp/quota-test.img bs=1M count=100
100+0 records in
100+0 records out
104857600 bytes (105 MB, 100 MiB) copied, 0.282401 s, 371 MB/s
Rappizr7@ubuntu-server:~/lab-acl$ sudo mkfs.ext4 /tmp/quota-test.img
mke2fs 1.47.0 (5-Feb-2023)
Discarding device blocks: done
Creating filesystem with 25600 4k blocks and 25600 inodes

Allocating group tables: done
Writing inode tables: done
Creating journal (1024 blocks): done
Writing superblocks and filesystem accounting information: done

Rappizr7@ubuntu-server:~/lab-acl$ sudo mkdir -p /mnt/quota-test
Rappizr7@ubuntu-server:~/lab-acl$ sudo mount -o loop,usrquota,grpquota /tmp/quota-test.img /mnt/quota-test
```

**Langkah 2: Buat database quota dan aktifkan enforcement.**
```bash
Rappizr7@ubuntu-server:~/lab-acl$ sudo quotacheck -cug /mnt/quota-test
Rappizr7@ubuntu-server:~/lab-acl$ sudo quotaon -v /mnt/quota-test
quotaon: Your kernel probably supports ext4 quota feature but you are using external quota files. Please switch your filesystem to use ext4 quota feature as external quota files on ext4 are deprecated.
/dev/loop0 [/mnt/quota-test]: group quotas turned on
/dev/loop0 [/mnt/quota-test]: user quotas turned on
Rappizr7@ubuntu-server:~/lab-acl$ sudo repquota /mnt/quota-test
*** Report for user quotas on device /dev/loop0
Block grace time: 7days; Inode grace time: 7days
                        Block limits                File limits
User            used    soft    hard  grace    used  soft  hard  grace
----------------------------------------------------------------------
root      --      20       0       0              2     0     0
```

**Langkah 3: Tetapkan quota untuk user uji dan amati hasilnya.**
```bash
Rappizr7@ubuntu-server:~/lab-acl$ sudo setquota -u userA 5120 10240 0 0 /mnt/quota-test
Rappizr7@ubuntu-server:~/lab-acl$ sudo repquota /mnt/quota-test
*** Report for user quotas on device /dev/loop0
Block grace time: 7days; Inode grace time: 7days
                        Block limits                File limits
User            used    soft    hard  grace    used  soft  hard  grace
----------------------------------------------------------------------
root      --      20       0       0              2     0     0
userA     --       0    5120   10240              0     0     0
```

**Langkah 4: Bersihkan lingkungan uji setelah selesai.**
```bash
Rappizr7@ubuntu-server:~/lab-acl$ sudo quotaoff /mnt/quota-test
Rappizr7@ubuntu-server:~/lab-acl$ sudo umount /mnt/quota-test
Rappizr7@ubuntu-server:~/lab-acl$ sudo rm /tmp/quota-test.img
```

**Analisis**
1. Apa perbedaan soft limit dan hard limit saat quota mulai terlampaui?
```bash
- Soft Limit (5120): Adalah batas peringatan. Jika userA melewatinya, ia masih bisa menulis data tetapi
akan muncul pesan peringatan dan diberikan waktu tenggang selama 7 hari untuk mengurangi pemakaian data.

- Hard Limit (10240): Adalah batas mati. Jika penggunaan mencapai angka ini, sistem akan langsung 
menghentikan proses penulisan file dengan pesan error "Disk quota exceeded", sehingga data baru tidak 
bisa disimpan sama sekali.
```

2. Mengapa praktikum ini memakai loopback filesystem, bukan langsung /home/?
```bash
- Keamanan: Agar praktikum tetap aman dan tidak berisiko merusak atau mengubah konfigurasi filesystem 
utama milik sistem operasi.

- Simulasi: Mempermudah pembuatan lingkungan uji coba yang terisolasi tanpa perlu melakukan partisi 
ulang harddisk fisik.
```

3. Dari output repquota, informasi apa yang menunjukkan quota sudah aktif?
```bash
Dilihat dari output repquota terakhir:
- Munculnya nama userA dalam daftar laporan kuota.
- Terisinya nilai pada kolom Block limits di baris userA, yaitu soft: 5120 dan hard: 10240.
- Adanya keterangan Block grace time: 7days di bagian atas laporan, yang menunjukkan aturan penalti 
untuk pelanggaran soft limit sudah siap digunakan.
```

# 1.7 Latihan
**Latihan Latihan 9.A — Audit dan Kolaborasi**
1. Temukan file SUID aktif dengan find / -perm -4000 -type f 2>/dev/null, lalu jelaskan
tiga file yang Anda kenali beserta alasannya.
```bash
Rappizr7@ubuntu-server:~/praktikum-os/pertemuan11$ find / -perm -o+w -type d 2>/dev/null
/dev/mqueue
/dev/shm
/tmp
/tmp/.XIM-unix
/tmp/.X11-unix
/tmp/.ICE-unix
/tmp/.font-unix
/var/tmp
/var/crash
/run/screen
/run/lock

penjelasan:
- /tmp dan /var/tmp: Tempat penyimpanan file sementara aplikasi. 

- /dev/shm: Digunakan untuk Shared Memory sistem agar komunikasi antar proses berjalan cepat.

- /run/lock: Folder standar untuk menyimpan file pengunci aplikasi.
```

2. Cari direktori world-writable dan tentukan mana yang valid dan mana yang berisiko.
```bash
1. Direktori yang Valid (Aman)
Direktori-direktori ini memang didesain secara sistem agar bisa ditulis oleh semua pengguna.

- /tmp dan /var/tmp: Digunakan sebagai tempat penyimpanan file sementara bagi aplikasi. Direktori ini 
aman karena memiliki Sticky Bit (ditandai dengan huruf t pada permission), sehingga pengguna hanya 
bisa menghapus file miliknya sendiri.

- /dev/shm: Merupakan shared memory yang digunakan untuk komunikasi antar-proses (IPC). Sifatnya 
memang terbuka agar aplikasi bisa bertukar data dengan cepat di RAM.

- /run/lock: Digunakan untuk menyimpan file pengunci (lock files) agar dua aplikasi tidak 
mengakses perangkat keras yang sama secara bersamaan.

2. Direktori yang Berisiko (Bahaya)
Direktori berisiko tinggi jika bukan merupakan direktori sistem standar namun bersifat terbuka untuk
semua orang.

-Kriteria Risiko: direktori seperti /etc, /bin, atau direktori proyek di /srv bersifat world-writable.
```

3. Rancang konfigurasi permission standar dan ACL untuk direktori proyek /srv/webapp/ agar
group webapp-team dapat menulis, user deploy hanya membaca, dan file baru selalu mewarisi
group proyek.
```bash
A. Permission Standar dan Group Ownership
# Buat direktori dan ganti kepemilikan grup
sudo mkdir -p /srv/webapp
sudo chmod 2770 /srv/webapp
sudo chown :webapp-team /srv/webapp

# Set permission: Owner (rwx), Group (rwx), Others (---)
# Tambahkan SetGID (2) agar file baru otomatis mengikuti grup 'webapp-team'
sudo chmod 2770 /srv/webapp

implementasi:
Rappizr7@ubuntu-server:~/praktikum-os/pertemuan11$ sudo mkdir -p /srv/webapp
[sudo] password for Rappizr7:
Rappizr7@ubuntu-server:~/praktikum-os/pertemuan11$ sudo chmod 2770 /srv/webapp
Rappizr7@ubuntu-server:~/praktikum-os/pertemuan11$ sudo groupadd webapp-team
Rappizr7@ubuntu-server:~/praktikum-os/pertemuan11$ sudo chown :webapp-team /srv/webapp
Rappizr7@ubuntu-server:~/praktikum-os/pertemuan11$ sudo chmod 2770 /srv/webapp

B. Konfigurasi ACL (Access Control List)
# Berikan akses baca-saja (r-x) secara spesifik untuk user 'deploy'
sudo setfacl -m u:deploy:rx /srv/webapp

# Set Default ACL agar file/folder baru di dalamnya mewarisi aturan yang sama
sudo setfacl -d -m g:webapp-team:rwx /srv/webapp
sudo setfacl -d -m u:deploy:rx /srv/webapp

implementasi:
Rappizr7@ubuntu-server:~/praktikum-os/pertemuan11$ sudo useradd deploy
Rappizr7@ubuntu-server:~/praktikum-os/pertemuan11$ sudo setfacl -m u:deploy:rx /srv/webapp
Rappizr7@ubuntu-server:~/praktikum-os/pertemuan11$ sudo setfacl -d -m u:deploy:rx /srv/webapp
```

**Latihan Latihan 9.B — Kebijakan Akun dan Quota**
1. Tuliskan langkah untuk membuat user intern, menambahkannya ke group labgroup, memaksa 
pergantian password tiap 45 hari (warning 7 hari), memberi izin sudo hanya untuk systemctl status, 
dan menetapkan quota ruang serta inode sederhana pada /home/
```bash
1. Membuat User dan Grup
Rappizr7@ubuntu-server:~/praktikum-os/pertemuan11$ sudo groupadd labgroup
groupadd: group 'labgroup' already exists
Rappizr7@ubuntu-server:~/praktikum-os/pertemuan11$ sudo useradd -m -g labgroup intern
Rappizr7@ubuntu-server:~/praktikum-os/pertemuan11$ sudo passwd intern
New password:
Retype new password:
passwd: password updated successfully

2. Konfigurasi Kedaluwarsa Password
Rappizr7@ubuntu-server:~/praktikum-os/pertemuan11$ sudo chage -M 45 -W 7 intern

3. Izin Sudo Spesifik 
- Jalankan perintah: sudo visudo
Rappizr7@ubuntu-server:~/praktikum-os/pertemuan11$ sudo visudo
Tambahkan baris berikut di bagian paling bawah file:
intern ALL=(ALL) NOPASSWD: /usr/bin/systemctl status *

4. Menetapkan Quota
Rappizr7@ubuntu-server:~/praktikum-os/pertemuan11$ sudo setquota -u intern 102400 112640 1000 1100 /mnt/quota-test
Rappizr7@ubuntu-server:~/praktikum-os/pertemuan11$ sudo repquota -u /mnt/quota-test
*** Report for user quotas on device /dev/loop0
Block grace time: 7days; Inode grace time: 7days
                        Block limits                File limits
User            used    soft    hard  grace    used  soft  hard  grace
----------------------------------------------------------------------
root      --      20       0       0              2     0     0
intern    --       0  102400  112640              1  1000  1100
```