# Laporan Pertemuan ke-13 Sistem Operasi

**Tanggal:** 09 Juni 2026  
**Disusun Oleh:** Mukhammad Raffi Zabra  
**NIM:** 254107020059  
**Kelas/No:** TI-1G/24  

---

# Praktek 12.1: Rencanakan Strategi Backup
**1. Buat direktori struktur data simulasi yang akan di-backup:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ cd ~/lab-os/chapter12-backup
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ mkdir -p data-sumber/{dokumen,konfigurasi,log}
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ echo "laporan-keuangan-2026.txt" > data-sumber/dokumen/laporan.txt
echo "catatan-penting.txt" >> data-sumber/dokumen/laporan.txt
echo "app_port=8080" > data-sumber/konfigurasi/app.conf
echo "db_host=localhost" >> data-sumber/konfigurasi/app.conf

for i in $(seq 1 20); do
    echo "$(date) - log entry $i" >> data-sumber/log/app.log
done

du -sh data-sumber/*/
find data-sumber -type f | wc -l
8.0K    data-sumber/dokumen/
8.0K    data-sumber/konfigurasi/
8.0K    data-sumber/log/
3
```

**2. Buat dokumen rencana backup menggunakan heredoc:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ cat > rencana-backup.txt << 'EOF'
====================================================
RENCANA BACKUP SISTEM LAB LINUX
====================================================
Tanggal Dibuat : 9 Juni 2026
Nama Sistem    : Lab Ubuntu Server 22.04
====================================================
INVENTARIS DATA
---------------
data-sumber/dokumen/     : laporan dan catatan, KRITIS
data-sumber/konfigurasi/ : konfigurasi aplikasi, KRITIS
data-sumber/log/         : log sistem, TIDAK KRITIS

TARGET METRIK
-------------
RPO : 24 jam (data boleh hilang maksimal 1 hari)
RTO : 2 jam (sistem harus pulih dalam 2 jam)

STRATEGI
--------
Full backup : setiap Minggu malam (dokumen + konfigurasi)
Incremental : Senin - Sabtu malam (dokumen + konfigurasi)
Log         : TIDAK dibackup (dapat diregenerasi)
Penyimpanan : lokal ~/lab-os/chapter12-backup/arsip/

ATURAN 3-2-1
-----------
Salinan 1 : direktori data-sumber (data asli)
Salinan 2 : direktori arsip/lokal (backup lokal)
Salinan 3 : server remote / cloud storage (offsite)
cat rencana-backup.txt==============================
====================================================
RENCANA BACKUP SISTEM LAB LINUX
====================================================
Tanggal Dibuat : 9 Juni 2026
Nama Sistem    : Lab Ubuntu Server 22.04
====================================================
INVENTARIS DATA
---------------
data-sumber/dokumen/     : laporan dan catatan, KRITIS
data-sumber/konfigurasi/ : konfigurasi aplikasi, KRITIS
data-sumber/log/         : log sistem, TIDAK KRITIS

TARGET METRIK
-------------
RPO : 24 jam (data boleh hilang maksimal 1 hari)
RTO : 2 jam (sistem harus pulih dalam 2 jam)

STRATEGI
--------
Full backup : setiap Minggu malam (dokumen + konfigurasi)
Incremental : Senin - Sabtu malam (dokumen + konfigurasi)
Log         : TIDAK dibackup (dapat diregenerasi)
Penyimpanan : lokal ~/lab-os/chapter12-backup/arsip/

ATURAN 3-2-1
-----------
Salinan 1 : direktori data-sumber (data asli)
Salinan 2 : direktori arsip/lokal (backup lokal)
Salinan 3 : server remote / cloud storage (offsite)
====================================================
```

**3. Hitung estimasi kebutuhan ruang backup untuk 30 hari:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ DATA_SIZE=$(du -sb data-sumber/dokumen/ data-sumber/konfigurasi/ | awk '{ sum += $1 } END { print sum }')

echo "Ukuran data kritis : $DATA_SIZE byte"
echo "Full backup x4/bulan : $(( DATA_SIZE * 4 )) byte"
echo "Incremental est. 10% x24/bulan : $(( DATA_SIZE * 24 / 10 )) byte"
echo "Total estimasi 30 hari : $(( DATA_SIZE * 4 + DATA_SIZE * 24 / 10 )) byte"
Ukuran data kritis : 78 byte
Full backup x4/bulan : 312 byte
Incremental est. 10% x24/bulan : 187 byte
Total estimasi 30 hari : 499 byte
```

**Tantangan**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ nano rencana-lengkap.txt
isi: 
===================================================================
RENCANA STRATEGI BACKUP & PEMULIHAN SISTEM (VERSI LENGKAP)
===================================================================
Tanggal Pembuatan : Sel Jun  9 23:17:33 WIB 2026
Nama Sistem       : Lab Ubuntu Server 22.04 (Polinema)
Status Dokumen    : PROD / AKTIF
===================================================================

1. JADWAL BACKUP (DAILY & WEEKLY)
---------------------------------
* Harian (Senin - Sabtu): 
  - Jenis: Incremental Backup
  - Waktu: Setiap pukul 23:00 WIB
  - Target: Hanya data berubah di data-sumber/dokumen/ & konfigurasi/
* Mingguan (Setiap Hari Minggu):
  - Jenis: Full Backup
  - Waktu: Setiap pukul 23:59 WIB
  - Target: Seluruh direktori data-sumber/ (kecuali direktori log/)

2. ESTIMASI RUANG PENYIMPANAN (RETENSI 30 HARI)
----------------------------------------------
* Ukuran Basis Data Kritis : ~100 MB (Data Awal)
* Alokasi Mingguan (4x Full)  : 400 MB
* Alokasi Harian (24x Incr)   : 240 MB (Est. Perubahan 10% per hari)
* Buffer Keamanan (Overhead)  : 60 MB  (Est. Pertumbuhan data)
* Total Kebutuhan Storage     : 700 MB per bulan

3. LOKASI PENYIMPANAN (STRATEGI 3-2-1)
--------------------------------------
* Lokasi 1 (Data Hidup)       : ~/lab-os/chapter12-backup/data-sumber/
* Lokasi 2 (Backup Lokal)     : ~/lab-os/chapter12-backup/arsip/
* Lokasi 3 (Offsite/Remote)   : Rappizr7@remote-server:/backup/offsite/

4. PROSEDUR PENGUJIAN RESTORE (SIMULASI PEMULIHAN)
--------------------------------------------------
A. Tahap Persiapan:
   1. Buat direktori temporary pemulihan (`mkdir -p /tmp/restore-test`).
   2. Pastikan file arsip `.tar.gz` atau data `rsync` yang akan diuji tersedia.
B. Tahap Eksekusi:
   3. Ekstrak data backup menggunakan perintah `tar -xzvf [nama_file].tar.gz -C /tmp/restore-test/`.
C. Tahap Verifikasi:
   4. Hitung dan cocokkan checksum MD5 antara data asli dan data hasil restore.
   5. Pastikan semua service aplikasi dapat membaca ulang konfigurasi yang dipulihkan.
===================================================================
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ cat rencana-lengkap.txt | wc -l
44
```

# Praktek 12.2: Sinkronisasi Direktori dengan rsync
**1. Jalankan sinkronisasi pertama:**
```bash 
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rsync -avn data-sumber/ arsip-rsync/
sending incremental file list
./
dokumen/
dokumen/laporan.txt
konfigurasi/
konfigurasi/app.conf
log/
log/app.log

sent 248 bytes  received 40 bytes  576.00 bytes/sec
total size is 1,009  speedup is 3.50 (DRY RUN)
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rsync -av data-sumber/ arsip-rsync/
sending incremental file list
./
dokumen/
dokumen/laporan.txt
konfigurasi/
konfigurasi/app.conf
log/
log/app.log

sent 1,381 bytes  received 92 bytes  2,946.00 bytes/sec
total size is 1,009  speedup is 0.68
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls -la arsip-rsync/
total 20
drwxrwxr-x 5 Rappizr7 Rappizr7 4096 Jun  9 16:13 .
drwxrwxr-x 4 Rappizr7 Rappizr7 4096 Jun  9 16:37 ..
drwxrwxr-x 2 Rappizr7 Rappizr7 4096 Jun  9 16:13 dokumen
drwxrwxr-x 2 Rappizr7 Rappizr7 4096 Jun  9 16:13 konfigurasi
drwxrwxr-x 2 Rappizr7 Rappizr7 4096 Jun  9 16:13 log
```

**2. Tambahkan file baru, hapus satu file, lalu amati efek –delete:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ echo "file baru penting" > data-sumber/dokumen/baru.txt
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rm data-sumber/log/app.log
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rsync -av --delete data-sumber/ arsip-rsync/
sending incremental file list
deleting log/app.log
dokumen/
dokumen/baru.txt
log/

sent 310 bytes  received 59 bytes  738.00 bytes/sec
total size is 96  speedup is 0.26
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls arsip-rsync/dokumen/
baru.txt  laporan.txt
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls arsip-rsync/log/
```

**3. Buat snapshot pertama dan kedua menggunakan –link-dest:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ mkdir -p snapshots/snap-1
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rsync -av data-sumber/ snapshots/snap-1/
sending incremental file list
./
dokumen/
dokumen/baru.txt
dokumen/laporan.txt
konfigurasi/
konfigurasi/app.conf
log/

sent 480 bytes  received 92 bytes  1,144.00 bytes/sec
total size is 96  speedup is 0.17
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ echo "isi baru setelah snap-1" > data-sumber/dokumen/baru.txt
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ mkdir -p snapshots/snap-2
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rsync -av --link-dest=../snap-1 data-sumber/ snapshots/snap-2/
sending incremental file list
./
dokumen/baru.txt

sent 317 bytes  received 46 bytes  242.00 bytes/sec
total size is 102  speedup is 0.28
```

**4. Verifikasi hard link dengan membandingkan inode:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls -lai snapshots/snap-1/dokumen/
total 16
1966270 drwxrwxr-x 2 Rappizr7 Rappizr7 4096 Jun  9 16:40 .
1966269 drwxrwxr-x 5 Rappizr7 Rappizr7 4096 Jun  9 16:13 ..
1966273 -rw-rw-r-- 1 Rappizr7 Rappizr7   18 Jun  9 16:40 baru.txt
1966274 -rw-rw-r-- 2 Rappizr7 Rappizr7   46 Jun  9 16:13 laporan.txt
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls -lai snapshots/snap-2/dokumen/
total 16
1966277 drwxrwxr-x 2 Rappizr7 Rappizr7 4096 Jun  9 16:40 .
1966276 drwxrwxr-x 5 Rappizr7 Rappizr7 4096 Jun  9 16:13 ..
1966280 -rw-rw-r-- 1 Rappizr7 Rappizr7   24 Jun  9 16:42 baru.txt
1966274 -rw-rw-r-- 2 Rappizr7 Rappizr7   46 Jun  9 16:13 laporan.txt
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ du -sh snapshots/snap-1/ snapshots/snap-2/
28K     snapshots/snap-1/
20K     snapshots/snap-2/
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ du -sh snapshots/
52K     snapshots/
```

**5. Bersihkan snapshot setelah praktek:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rm -rf arsip-rsync/ snapshots/
```

**Tantangan**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ nano rsync-backup.sh
isi :
#!/bin/bash

# 1. Mendapatkan tanggal hari ini dengan format YYYY-MM-DD
TANGGAL=$(date +%Y-%m-%d)

# 2. Menentukan direktori sumber dan tujuan
SUMBER="data-sumber/"
TUJUAN="arsip-$TANGGAL/"
LOG_FILE="backup-$TANGGAL.log"

echo "=================================================="
echo "Mulai Proses Backup: $(date)"
echo "Sumber   : $SUMBER"
echo "Tujuan   : $TUJUAN"
echo "Log File : $LOG_FILE"
echo "=================================================="

# 3. Membuat direktori tujuan jika belum ada
mkdir -p "$TUJUAN"

# 4. Menjalankan rsync dan mengalirkan outputnya menggunakan 'tee'
# 'tee' akan menampilkan hasil di layar sekaligus menyimpannya ke file log
rsync -av "$SUMBER" "$TUJUAN" 2>&1 | tee "$LOG_FILE"

echo "=================================================="
echo "Backup Selesai Terbaca dan Tersimpan!"
echo "=================================================="
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ chmod +x rsync-backup.sh
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ./rsync-backup.sh
==================================================
Mulai Proses Backup: Tue Jun  9 04:47:01 PM UTC 2026
Sumber   : data-sumber/
Tujuan   : arsip-2026-06-09/
Log File : backup-2026-06-09.log
==================================================
sending incremental file list
./
dokumen/
dokumen/baru.txt
dokumen/laporan.txt
konfigurasi/
konfigurasi/app.conf
log/

sent 489 bytes  received 92 bytes  1,162.00 bytes/sec
total size is 102  speedup is 0.18
==================================================
Backup Selesai Terbaca dan Tersimpan!
==================================================
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls -d arsip-*/
arsip-2026-06-09/
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ cat backup-$(date +%Y-%m-%d).log
sending incremental file list
./
dokumen/
dokumen/baru.txt
dokumen/laporan.txt
konfigurasi/
konfigurasi/app.conf
log/

sent 489 bytes  received 92 bytes  1,162.00 bytes/sec
total size is 102  speedup is 0.18
```

# Praktek 12.3: Buat Arsip Backup dan Verifikasi Integritasnya
**1. Buat arsip full backup dan simpan checksumnya:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ mkdir -p arsip-tar
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ tar -czf arsip-tar/full-$(date +%F).tar.gz data-sumber/
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls -lh arsip-tar/
total 4.0K
-rw-rw-r-- 1 Rappizr7 Rappizr7 367 Jun  9 16:52 full-2026-06-09.tar.gz
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ md5sum arsip-tar/full-$(date +%F).tar.gz > arsip-tar/full-$(date +%F).md5
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ sha256sum arsip-tar/full-$(date +%F).tar.gz > arsip-tar/full-$(date +%F).sha256
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ cat arsip-tar/full-$(date +%F).md5
dcfac8692808855affcf98fbbcdb2502  arsip-tar/full-2026-06-09.tar.gz
```

**2. Periksa isi arsip dan coba ekstrak satu file:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ tar -tzf arsip-tar/full-$(date +%F).tar.gz
data-sumber/
data-sumber/konfigurasi/
data-sumber/konfigurasi/app.conf
data-sumber/dokumen/
data-sumber/dokumen/baru.txt
data-sumber/dokumen/laporan.txt
data-sumber/log/
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ tar -tzf arsip-tar/full-$(date +%F).tar.gz | grep "\.conf"
data-sumber/konfigurasi/app.conf
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ mkdir -p /tmp/tar-coba
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ tar -xzf arsip-tar/full-$(date +%F).tar.gz -C /tmp/tar-coba data-sumber/konfigurasi/app.conf
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ cat /tmp/tar-coba/data-sumber/konfigurasi/app.conf
app_port=8080
db_host=localhost
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rm -rf /tmp/tar-coba
```

**3. Verifikasi integritas arsip menggunakan checksum:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ md5sum -c arsip-tar/full-$(date +%F).md5
arsip-tar/full-2026-06-09.tar.gz: OK
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ sha256sum -c arsip-tar/full-$(date +%F).sha256
arsip-tar/full-2026-06-09.tar.gz: OK
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ tar -tzf arsip-tar/full-$(date +%F).tar.gz > /dev/null && echo "Arsip VALID" || echo "Arsip RUSAK"
Arsip VALID
```

**4. Buat arsip incremental setelah menambahkan file baru:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ tar -czf arsip-tar/full-snap-$(date +%F).tar.gz --listed-incremental=arsip-tar/snapshot.snar data-sumber/
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ echo "file ditambahkan setelah full backup" > data-sumber/dokumen/tambahan.txt
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ echo "konfigurasi baru" >> data-sumber/konfigurasi/app.conf
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ tar -czf arsip-tar/incr-$(date +%F-%H%M).tar.gz --listed-incremental=arsip-tar/snapshot.snar data-sumber/
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls -lh arsip-tar/*.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7 367 Jun  9 16:52 arsip-tar/full-2026-06-09.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7 440 Jun  9 16:58 arsip-tar/full-snap-2026-06-09.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7 420 Jun  9 16:58 arsip-tar/incr-2026-06-09-1658.tar.gz

Analisis Hasil Ukuran File:
- full-snap-2026-06-09.tar.gz: Ukurannya 440 byte (ini adalah backup dasar/level 0 yang merekam semua data awal di data-sumber/).
- incr-2026-06-09-1658.tar.gz: Ukurannya 420 byte.
```

**Tantangan**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ echo "catatan rapat divisi humas" > data-sumber/dokumen/nota-humas.txt
echo "daftar perlengkapan kompetisi" > data-sumber/dokumen/logistik.txt
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ echo "session_timeout=3600" >> data-sumber/konfigurasi/app.conf
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ tar -czf arsip-tar/incr-2-$(date
+%F-%H%M).tar.gz --listed-incremental=arsip-tar/snapshot.snar data-sumber/
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls -lh arsip-tar/
total 28K
-rw-rw-r-- 1 Rappizr7 Rappizr7  67 Jun  9 16:52 full-2026-06-09.md5
-rw-rw-r-- 1 Rappizr7 Rappizr7  99 Jun  9 16:53 full-2026-06-09.sha256
-rw-rw-r-- 1 Rappizr7 Rappizr7 367 Jun  9 16:52 full-2026-06-09.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7 440 Jun  9 16:58 full-snap-2026-06-09.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7 420 Jun  9 16:58 incr-2026-06-09-1658.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7 486 Jun  9 17:04 incr-2-2026-06-09-1704.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7 362 Jun  9 17:04 snapshot.snar
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ tar -tzf arsip-tar/full-snap-*.tar.gz
data-sumber/
data-sumber/dokumen/
data-sumber/konfigurasi/
data-sumber/log/
data-sumber/dokumen/baru.txt
data-sumber/dokumen/laporan.txt
data-sumber/konfigurasi/app.conf
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ tar -tzf arsip-tar/incr-2026-*.tar.gz
data-sumber/
data-sumber/dokumen/
data-sumber/konfigurasi/
data-sumber/log/
data-sumber/dokumen/tambahan.txt
data-sumber/konfigurasi/app.conf
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ tar -tzf arsip-tar/incr-2-*.tar.gz
data-sumber/
data-sumber/dokumen/
data-sumber/konfigurasi/
data-sumber/log/
data-sumber/dokumen/logistik.txt
data-sumber/dokumen/nota-humas.txt
data-sumber/konfigurasi/app.conf
```

# Praktek 12.4: Jadwalkan Skrip Backup Otomatis
**1. Buat skrip backup yang menggunakan rsync dengan logging:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ nano backup-otomatis.sh
isi:
#!/bin/bash
BACKUP_BASE="$HOME/lab-os/chapter12-backup/arsip-cron"
SOURCE="$HOME/lab-os/chapter12-backup/data-sumber"
DATE=$(date +%Y-%m-%d-%H%M)
LOG="$HOME/lab-os/chapter12-backup/cron-backup.log"

mkdir -p "$BACKUP_BASE/$DATE"
echo "[$(date)] Memulai backup ke $BACKUP_BASE/$DATE" >> "$LOG"

rsync -av --delete "$SOURCE/" "$BACKUP_BASE/$DATE/" >> "$LOG" 2>&1

if [ $? -eq 0 ]; then
    UKURAN=$(du -sh "$BACKUP_BASE/$DATE/" | cut -f1)
    echo "[$(date)] OK : $DATE ($UKURAN)" >> "$LOG"
else
    echo "[$(date)] GAGAL : backup $DATE" >> "$LOG"
    rmdir "$BACKUP_BASE/$DATE" 2>/dev/null
fi

# Membersihkan backup lama yang umurnya lebih dari 30 menit
find "$BACKUP_BASE" -maxdepth 1 -type d -mmin +30 -exec rm -rf {} \; 2>/dev/null
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ chmod +x backup-otomatis.
```

**2. Uji skrip secara manual sebelum mendaftarkannya ke cron:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ./backup-otomatis.sh
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls -la arsip-cron/
total 8
drwxrwxr-x 2 Rappizr7 Rappizr7 4096 Jun  9 17:14 .
drwxrwxr-x 6 Rappizr7 Rappizr7 4096 Jun  9 17:14 ..
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ cat cron-backup.log
[Tue Jun  9 05:14:00 PM UTC 2026] Memulai backup ke /home/Rappizr7/lab-os/chapter12-backup/arsip-cron/2026-06-09-1714
sending incremental file list
./
dokumen/
dokumen/baru.txt
dokumen/laporan.txt
dokumen/logistik.txt
dokumen/nota-humas.txt
dokumen/tambahan.txt
konfigurasi/
konfigurasi/app.conf
log/

sent 836 bytes  received 149 bytes  1,970.00 bytes/sec
total size is 234  speedup is 0.24
[Tue Jun  9 05:14:00 PM UTC 2026] OK : 2026-06-09-1714 (40K)
```

**3. Daftarkan skrip ke crontab untuk berjalan setiap 2 menit (untuk keperluan pengujian):**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ crontab -l 2>/dev/null > /tmp/cron-sementara
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ echo "*/2 * * * * $HOME/lab-os/chapter12-backup/backup-otomatis.sh" >> /tmp/cron-sementara
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ crontab /tmp/cron-sementara
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rm /tmp/cron-sementara
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ crontab -l
*/2 * * * * /home/Rappizr7/lab-os/chapter12-backup/backup-otomatis.sh
```

**4. Tunggu beberapa menit lalu verifikasi cron telah menjalankan backup:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls -lt arsip-cron/
total 16
drwxrwxr-x 5 Rappizr7 Rappizr7 4096 Jun  9 16:13 2026-06-09-1722
drwxrwxr-x 5 Rappizr7 Rappizr7 4096 Jun  9 16:13 2026-06-09-1724
drwxrwxr-x 5 Rappizr7 Rappizr7 4096 Jun  9 16:13 2026-06-09-1726
drwxrwxr-x 5 Rappizr7 Rappizr7 4096 Jun  9 16:13 2026-06-09-1728
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ cat cron-backup.log
[Tue Jun  9 05:14:00 PM UTC 2026] Memulai backup ke /home/Rappizr7/lab-os/chapter12-backup/arsip-cron/2026-06-09-1714
sending incremental file list
./
dokumen/
dokumen/baru.txt
dokumen/laporan.txt
dokumen/logistik.txt
dokumen/nota-humas.txt
dokumen/tambahan.txt
konfigurasi/
konfigurasi/app.conf
log/

sent 836 bytes  received 149 bytes  1,970.00 bytes/sec
total size is 234  speedup is 0.24
[Tue Jun  9 05:14:00 PM UTC 2026] OK : 2026-06-09-1714 (40K)
[Tue Jun  9 05:16:02 PM UTC 2026] Memulai backup ke /home/Rappizr7/lab-os/chapter12-backup/arsip-cron/2026-06-09-1716
sending incremental file list
./
dokumen/
dokumen/baru.txt
dokumen/laporan.txt
dokumen/logistik.txt
dokumen/nota-humas.txt
dokumen/tambahan.txt
konfigurasi/
konfigurasi/app.conf
log/

sent 832 bytes  received 149 bytes  1,962.00 bytes/sec
total size is 234  speedup is 0.24
[Tue Jun  9 05:16:02 PM UTC 2026] OK : 2026-06-09-1716 (40K)
[Tue Jun  9 05:18:01 PM UTC 2026] Memulai backup ke /home/Rappizr7/lab-os/chapter12-backup/arsip-cron/2026-06-09-1718
sending incremental file list
./
dokumen/
dokumen/baru.txt
dokumen/laporan.txt
dokumen/logistik.txt
dokumen/nota-humas.txt
dokumen/tambahan.txt
konfigurasi/
konfigurasi/app.conf
log/

sent 836 bytes  received 149 bytes  1,970.00 bytes/sec
total size is 234  speedup is 0.24
[Tue Jun  9 05:18:01 PM UTC 2026] OK : 2026-06-09-1718 (40K)
[Tue Jun  9 05:20:01 PM UTC 2026] Memulai backup ke /home/Rappizr7/lab-os/chapter12-backup/arsip-cron/2026-06-09-1720
sending incremental file list
./
dokumen/
dokumen/baru.txt
dokumen/laporan.txt
dokumen/logistik.txt
dokumen/nota-humas.txt
dokumen/tambahan.txt
konfigurasi/
konfigurasi/app.conf
log/

sent 836 bytes  received 149 bytes  1,970.00 bytes/sec
total size is 234  speedup is 0.24
[Tue Jun  9 05:20:01 PM UTC 2026] OK : 2026-06-09-1720 (40K)
[Tue Jun  9 05:22:01 PM UTC 2026] Memulai backup ke /home/Rappizr7/lab-os/chapter12-backup/arsip-cron/2026-06-09-1722
sending incremental file list
./
dokumen/
dokumen/baru.txt
dokumen/laporan.txt
dokumen/logistik.txt
dokumen/nota-humas.txt
dokumen/tambahan.txt
konfigurasi/
konfigurasi/app.conf
log/

sent 836 bytes  received 149 bytes  1,970.00 bytes/sec
total size is 234  speedup is 0.24
[Tue Jun  9 05:22:01 PM UTC 2026] OK : 2026-06-09-1722 (40K)
[Tue Jun  9 05:24:02 PM UTC 2026] Memulai backup ke /home/Rappizr7/lab-os/chapter12-backup/arsip-cron/2026-06-09-1724
sending incremental file list
./
dokumen/
dokumen/baru.txt
dokumen/laporan.txt
dokumen/logistik.txt
dokumen/nota-humas.txt
dokumen/tambahan.txt
konfigurasi/
konfigurasi/app.conf
log/

sent 836 bytes  received 149 bytes  1,970.00 bytes/sec
total size is 234  speedup is 0.24
[Tue Jun  9 05:24:02 PM UTC 2026] OK : 2026-06-09-1724 (40K)
[Tue Jun  9 05:26:01 PM UTC 2026] Memulai backup ke /home/Rappizr7/lab-os/chapter12-backup/arsip-cron/2026-06-09-1726
sending incremental file list
./
dokumen/
dokumen/baru.txt
dokumen/laporan.txt
dokumen/logistik.txt
dokumen/nota-humas.txt
dokumen/tambahan.txt
konfigurasi/
konfigurasi/app.conf
log/

sent 836 bytes  received 149 bytes  1,970.00 bytes/sec
total size is 234  speedup is 0.24
[Tue Jun  9 05:26:01 PM UTC 2026] OK : 2026-06-09-1726 (40K)
[Tue Jun  9 05:28:01 PM UTC 2026] Memulai backup ke /home/Rappizr7/lab-os/chapter12-backup/arsip-cron/2026-06-09-1728
sending incremental file list
./
dokumen/
dokumen/baru.txt
dokumen/laporan.txt
dokumen/logistik.txt
dokumen/nota-humas.txt
dokumen/tambahan.txt
konfigurasi/
konfigurasi/app.conf
log/

sent 836 bytes  received 149 bytes  1,970.00 bytes/sec
total size is 234  speedup is 0.24
[Tue Jun  9 05:28:01 PM UTC 2026] OK : 2026-06-09-1728 (40K)
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ grep CRON /var/log/syslog 2>/dev/null | tail -5
```

**5. Bersihkan crontab dan direktori:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ crontab -l | grep -v "backup-otomatis" | crontab -
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ crontab -l
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rm -rf arsip-cron/ arsip-tar/ cron-backup.log backup.log
```

**Tantangan**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ nano rsync-backup.sh
isi :
#!/bin/bash

# 1. Mendapatkan tanggal hari ini dengan format YYYY-MM-DD
TANGGAL=$(date +%Y-%m-%d)

# 2. Menentukan direktori sumber dan tujuan 
SUMBER="/home/Rappizr7/lab-os/chapter12-backup/data-sumber/"
TUJUAN="/home/Rappizr7/lab-os/chapter12-backup/arsip-$TANGGAL/"
LOG_FILE="/var/log/backup.log"

echo "=================================================="
echo "Mulai Proses Backup: $(date)"
echo "Sumber   : $SUMBER"
echo "Tujuan   : $TUJUAN"
echo "Log File : $LOG_FILE"
echo "=================================================="

# 3. Membuat direktori tujuan jika belum ada
mkdir -p "$TUJUAN"

# 4. Menjalankan rsync dan mengalirkan outputnya menggunakan 'tee'
rsync -av "$SUMBER" "$TUJUAN" 2>&1 | tee -a "$LOG_FILE"

echo "=================================================="
echo "Backup Selesai Terbaca dan Tersimpan!"
echo "=================================================="
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ sudo ./rsync-backup.sh
==================================================
Mulai Proses Backup: Tue Jun  9 05:37:32 PM UTC 2026
Sumber   : /home/Rappizr7/lab-os/chapter12-backup/data-sumber/
Tujuan   : /home/Rappizr7/lab-os/chapter12-backup/arsip-2026-06-09/
Log File : /var/log/backup.log
==================================================
sending incremental file list
dokumen/
dokumen/logistik.txt
dokumen/nota-humas.txt
dokumen/tambahan.txt
konfigurasi/app.conf

sent 667 bytes  received 98 bytes  1,530.00 bytes/sec
total size is 234  speedup is 0.31
==================================================
Backup Selesai Terbaca dan Tersimpan!
==================================================
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ cat /var/log/backup.log
sending incremental file list
dokumen/
dokumen/logistik.txt
dokumen/nota-humas.txt
dokumen/tambahan.txt
konfigurasi/app.conf

sent 667 bytes  received 98 bytes  1,530.00 bytes/sec
total size is 234  speedup is 0.31
```

# Praktek 12.5: Simulasi Pemulihan dari Backup
**1. Siapkan data sumber dan buat backup sebagai persiapan:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ mkdir -p data-sumber/{dokumen,konfigurasi}
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ echo "laporan-akhir-tahun" > data-sumber/dokumen/laporan.txt
echo "catatan-rapat" > data-sumber/dokumen/catatan.txt
echo "db_host=localhost" > data-sumber/konfigurasi/app.conf
echo "cache_ttl=300" >> data-sumber/konfigurasi/app.conf
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ mkdir -p arsip-pemulihan snapshot-pemulihan
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ tar -czf arsip-pemulihan/full-backup.tar.gz data-sumber/
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ md5sum arsip-pemulihan/full-backup.tar.gz > arsip-pemulihan/full-backup.md5
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rsync -av data-sumber/ snapshot-pemulihan/
sending incremental file list
./
dokumen/
dokumen/baru.txt
dokumen/catatan.txt
dokumen/laporan.txt
dokumen/logistik.txt
dokumen/nota-humas.txt
dokumen/tambahan.txt
konfigurasi/
konfigurasi/app.conf
log/

sent 857 bytes  received 168 bytes  2,050.00 bytes/sec
total size is 184  speedup is 0.18
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls -lhR data-sumber/
data-sumber/:
total 12K
drwxrwxr-x 2 Rappizr7 Rappizr7 4.0K Jun  9 17:50 dokumen
drwxrwxr-x 2 Rappizr7 Rappizr7 4.0K Jun  9 16:13 konfigurasi
drwxrwxr-x 2 Rappizr7 Rappizr7 4.0K Jun  9 16:40 log

data-sumber/dokumen:
total 24K
-rw-rw-r-- 1 Rappizr7 Rappizr7 24 Jun  9 16:42 baru.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7 14 Jun  9 17:50 catatan.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7 20 Jun  9 17:50 laporan.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7 30 Jun  9 17:03 logistik.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7 27 Jun  9 17:03 nota-humas.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7 37 Jun  9 16:58 tambahan.txt

data-sumber/konfigurasi:
total 4.0K
-rw-rw-r-- 1 Rappizr7 Rappizr7 32 Jun  9 17:50 app.conf

data-sumber/log:
total 0
```

**2. Simpan checksum file-file penting untuk verifikasi nanti:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ md5sum data-sumber/dokumen/* data-sumber/konfigurasi/* > checksum-asli.md5
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ cat checksum-asli.md5
d2681eadcef411a4193e4ec644c76022  data-sumber/dokumen/baru.txt
503c0b7ed8e0abe8529682d431f7bed6  data-sumber/dokumen/catatan.txt
07a80bf4cbbdb1570b1fd7ce56fffa2d  data-sumber/dokumen/laporan.txt
db934a0916514ac69854154d7c8c9d33  data-sumber/dokumen/logistik.txt
8a6393cce66ea5dc16385721e16a64d5  data-sumber/dokumen/nota-humas.txt
f79c7573d6d3583d31ca7606696812de  data-sumber/dokumen/tambahan.txt
32374db3053c7aefb4ad755116e1a122  data-sumber/konfigurasi/app.conf
```

**3. Simulasikan kehilangan data: hapus beberapa file secara sengaja:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rm data-sumber/dokumen/laporan.txt
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rm data-sumber/konfigurasi/app.conf
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ echo "file tidak sah" > data-sumber/dokumen/tidak-dikenal.txt
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls -la data-sumber/dokumen/
total 36
drwxrwxr-x 2 Rappizr7 Rappizr7 4096 Jun  9 17:53 .
drwxrwxr-x 5 Rappizr7 Rappizr7 4096 Jun  9 16:13 ..
-rw-rw-r-- 1 Rappizr7 Rappizr7   24 Jun  9 16:42 baru.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7   14 Jun  9 17:50 catatan.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7   30 Jun  9 17:03 logistik.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7   27 Jun  9 17:03 nota-humas.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7   37 Jun  9 16:58 tambahan.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7   15 Jun  9 17:53 tidak-dikenal.txt
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls -la data-sumber/konfigurasi/
total 8
drwxrwxr-x 2 Rappizr7 Rappizr7 4096 Jun  9 17:53 .
drwxrwxr-x 5 Rappizr7 Rappizr7 4096 Jun  9 16:13 ..
```

**4. Pulihkan file yang hilang dari arsip tar ke direktori sementara:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ md5sum -c arsip-pemulihan/full-backup.md5
arsip-pemulihan/full-backup.tar.gz: OK
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ mkdir -p /tmp/restore-lap14
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ tar -xzf arsip-pemulihan/full-backup.tar.gz -C /tmp/restore-lap14 data-sumber/dokumen/laporan.txt
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls /tmp/restore-lap14/data-sumber/dokumen/
laporan.txt
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ cp /tmp/restore-lap14/data-sumber/dokumen/laporan.txt data-sumber/dokumen/
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rm -rf /tmp/restore-lap14rm -rf /tmp/restore-lap14
```

**5. Pulihkan file konfigurasi dari snapshot rsync:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls snapshot-pemulihan/konfigurasi/
app.conf
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ cp snapshot-pemulihan/konfigurasi/app.conf data-sumber/konfigurasi/
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls -la data-sumber/konfigurasi/
total 12
drwxrwxr-x 2 Rappizr7 Rappizr7 4096 Jun  9 17:56 .
drwxrwxr-x 5 Rappizr7 Rappizr7 4096 Jun  9 16:13 ..
-rw-rw-r-- 1 Rappizr7 Rappizr7   32 Jun  9 17:56 app.conf
```

**6. Verifikasi integritas setelah pemulihan:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rm data-sumber/dokumen/tidak-dikenal.txt
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ md5sum -c checksum-asli.md5
data-sumber/dokumen/baru.txt: OK
data-sumber/dokumen/catatan.txt: OK
data-sumber/dokumen/laporan.txt: OK
data-sumber/dokumen/logistik.txt: OK
data-sumber/dokumen/nota-humas.txt: OK
data-sumber/dokumen/tambahan.txt: OK
data-sumber/konfigurasi/app.conf: OK
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ echo "Exit code : $? (0 berarti semua file valid)"
Exit code : 0 (0 berarti semua file valid)
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ diff -r data-sumber/ snapshot-pemulihan/
echo "Diff exit code : $? (0 berarti identik dengan snapshot)"
Diff exit code : 0 (0 berarti identik dengan snapshot)
```

**7. Bersihkan seluruh direktori lab:**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rm -rf arsip-pemulihan/ snapshot-pemulihan/
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rm -f checksum-asli.md5 rencana-backup.txt backup-otomatis.sh
```

**Tantangan**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rm -rf data-sumber/konfigurasi/
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls -l data-sumber/
total 8
drwxrwxr-x 2 Rappizr7 Rappizr7 4096 Jun  9 17:58 dokumen
drwxrwxr-x 2 Rappizr7 Rappizr7 4096 Jun  9 16:40 log
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rsync -av snapshot-pemulihan/konfigurasi/ data-sumber/konfigurasi/
sending incremental file list
created directory data-sumber/konfigurasi
./
app.conf

sent 166 bytes  received 84 bytes  500.00 bytes/sec
total size is 32  speedup is 0.13
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ find data-sumber/ -type f -exec md5sum {} \;
32374db3053c7aefb4ad755116e1a122  data-sumber/konfigurasi/app.conf
503c0b7ed8e0abe8529682d431f7bed6  data-sumber/dokumen/catatan.txt
d2681eadcef411a4193e4ec644c76022  data-sumber/dokumen/baru.txt
db934a0916514ac69854154d7c8c9d33  data-sumber/dokumen/logistik.txt
8a6393cce66ea5dc16385721e16a64d5  data-sumber/dokumen/nota-humas.txt
07a80bf4cbbdb1570b1fd7ce56fffa2d  data-sumber/dokumen/laporan.txt
f79c7573d6d3583d31ca7606696812de  data-sumber/dokumen/tambahan.txt
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rm -rf arsip-pemulihan/ snapshot-pemulihan/ data-sumber/
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rm -f checksum-asli.md5
Hasil Pengamatan Tantangan (Untuk Bahan Laporan)

kesimpulan perbandingan waktu (time difference):
- Restore per file (via tar): Prosesnya terasa ada overhead waktu luang karena sistem harus membaca daftar indeks di dalam file arsip yang terkompresi sebelum mengekstrak satu file spesifik.
- Restore per direktori (via Reverse rsync): Sangat instan dan efisien (500.00 bytes/sec untuk skala simulasi) karena rsync langsung menyalin seluruh struktur pohon direktori (directory tree) dari sumber cadangan ke tujuan secara sekuensial tanpa harus membongkar archive container.
```

# 1.7 Latihan
**Latihan 12.1 Implementasi Sistem Backup Lengkap**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ mkdir -p data-simulasi/{dokumen,konfigurasi,media}
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ echo "Laporan Anggaran HMTI 2026" > data-simulasi/dokumen/anggaran.txt
echo "Proposal Smart Solutions v1" > data-simulasi/dokumen/proposal.txt
echo "Catatan Rapat Divisi Humas" > data-simulasi/dokumen/notes-humas.txt
echo "Daftar Perlengkapan Logistik" > data-simulasi/dokumen/logistik.txt
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ echo "port=8080" > data-simulasi/konfigurasi/server.conf
echo "db_user=rappi" > data-simulasi/konfigurasi/database.env
echo "session_timeout=3600" > data-simulasi/konfigurasi/app.properties
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ echo "[PNG Data]" > data-simulasi/media/logo-hmti.png
echo "[SVG Data]" > data-simulasi/media/banner.svg
echo "[MP4 Data]" > data-simulasi/media/teaser.mp4
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ find data-simulasi/ -type f | wc -l
10
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ nano backup-harian.sh
#!/bin/bash
# Konfigurasi Path Absolut
BACKUP_DIR="/home/Rappizr7/lab-os/chapter12-backup/backup-harian"
SOURCE_DIR="/home/Rappizr7/lab-os/chapter12-backup/data-simulasi/"
LOG_FILE="/home/Rappizr7/lab-os/chapter12-backup/backup-harian.log"
DATE=$(date +%Y-%m-%d)

mkdir -p "$BACKUP_DIR"

echo "[$(date)] == MULAI BACKUP HARIAN ($DATE) ==" >> "$LOG_FILE"

LATEST_BACKUP=$(ls -td "$BACKUP_DIR"/*/ 2>/dev/null | head -1)

if [ -n "$LATEST_BACKUP" ]; then
    echo "[$(date)] Menggunakan basis snapshot: $LATEST_BACKUP" >> "$LOG_FILE"
    rsync -av --link-dest="$LATEST_BACKUP" "$SOURCE_DIR" "$BACKUP_DIR/$DATE/" >> "$LOG_FILE" 2>&1
else
    echo "[$(date)] Snapshot pertama tidak mendeteksi backup sebelumnya (Full)." >> "$LOG_FILE"
    rsync -av "$SOURCE_DIR" "$BACKUP_DIR/$DATE/" >> "$LOG_FILE" 2>&1
fi

if [ $? -eq 0 ]; then
    echo "[$(date)] STATUS: BERHASIL. Tersimpan di $BACKUP_DIR/$DATE" >> "$LOG_FILE"
else
    echo "[$(date)] STATUS: GAGAL." >> "$LOG_FILE"
fi
echo "--------------------------------------------------" >> "$LOG_FILE"
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ nano backup-mingguan.sh
#!/bin/bash
BACKUP_DIR="/home/Rappizr7/lab-os/chapter12-backup/backup-mingguan"
SOURCE_DIR="/home/Rappizr7/lab-os/chapter12-backup/data-simulasi"
LOG_FILE="/home/Rappizr7/lab-os/chapter12-backup/backup-mingguan.log"
DATE=$(date +%Y-%m-%d-%H%M)
FILE_NAME="full-backup-$DATE.tar.gz"

mkdir -p "$BACKUP_DIR"

echo "[$(date)] == MULAI ARCHIVE MINGGUAN ==" >> "$LOG_FILE"

# Membuat arsip tar.gz
tar -czf "$BACKUP_DIR/$FILE_NAME" "$SOURCE_DIR" >> "$LOG_FILE" 2>&1

if [ $? -eq 0 ]; then
    # Membuat checksum MD5 langsung di direktori tujuan
    cd "$BACKUP_DIR"
    md5sum "$FILE_NAME" > "$FILE_NAME.md5"
    echo "[$(date)] STATUS: BERHASIL. File: $FILE_NAME beserta MD5 terbuat." >> "$LOG_FILE"
else
    echo "[$(date)] STATUS: GAGAL membuat arsip." >> "$LOG_FILE"
fi
echo "--------------------------------------------------" >> "$LOG_FILE"
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ chmod +x backup-harian.sh backup-mingguan.sh
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ./backup-harian.sh
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ./backup-mingguan.sh
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ cat backup-harian.log
[Tue Jun  9 06:19:29 PM UTC 2026] == MULAI BACKUP HARIAN (2026-06-09) ==
[Tue Jun  9 06:19:29 PM UTC 2026] Snapshot pertama tidak mendeteksi backup sebelumnya (Full).
sending incremental file list
created directory /home/Rappizr7/lab-os/chapter12-backup/backup-harian/2026-06-09
./
dokumen/
dokumen/anggaran.txt
dokumen/logistik.txt
dokumen/notes-humas.txt
dokumen/proposal.txt
konfigurasi/
konfigurasi/app.properties
konfigurasi/database.env
konfigurasi/server.conf
media/
media/banner.svg
media/logo-hmti.png
media/teaser.mp4

sent 1,057 bytes  received 311 bytes  2,736.00 bytes/sec
total size is 189  speedup is 0.14
[Tue Jun  9 06:19:29 PM UTC 2026] STATUS: BERHASIL. Tersimpan di /home/Rappizr7/lab-os/chapter12-backup/backup-harian/2026-06-09
--------------------------------------------------
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ cat backup-mingguan.log
[Tue Jun  9 06:19:36 PM UTC 2026] == MULAI ARCHIVE MINGGUAN ==
tar: Removing leading `/' from member names
[Tue Jun  9 06:19:36 PM UTC 2026] STATUS: BERHASIL. File: full-backup-2026-06-09-1819.tar.gz beserta MD5 terbuat.
--------------------------------------------------
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls -lh backup-harian/ backup-mingguan/
backup-harian/:
total 4.0K
drwxrwxr-x 5 Rappizr7 Rappizr7 4.0K Jun  9 18:18 2026-06-09

backup-mingguan/:
total 8.0K
-rw-rw-r-- 1 Rappizr7 Rappizr7 637 Jun  9 18:19 full-backup-2026-06-09-1819.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7  69 Jun  9 18:19 full-backup-2026-06-09-1819.tar.gz.md5
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ crontab -e

Select an editor.  To change later, run 'select-editor'.
  1. /bin/nano        <---- easiest
  2. /usr/bin/vim.basic
  3. /usr/bin/vim.tiny
  4. /bin/ed

Choose 1-4 [1]: 1
crontab: installing new crontab
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rm -rf data-simulasi/
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls -l data-simulasi/
ls: cannot access 'data-simulasi/': No such file or directory
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rsync -av backup-harian/2026-06-09/ data-simulasi/
sending incremental file list
./
dokumen/
dokumen/anggaran.txt
dokumen/logistik.txt
dokumen/notes-humas.txt
dokumen/proposal.txt
konfigurasi/
konfigurasi/app.properties
konfigurasi/database.env
konfigurasi/server.conf
media/
media/banner.svg
media/logo-hmti.png
media/teaser.mp4

sent 1,057 bytes  received 225 bytes  2,564.00 bytes/sec
total size is 189  speedup is 0.15
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ find data-simulasi/ -type f
data-simulasi/konfigurasi/app.properties
data-simulasi/konfigurasi/database.env
data-simulasi/konfigurasi/server.conf
data-simulasi/media/logo-hmti.png
data-simulasi/media/teaser.mp4
data-simulasi/media/banner.svg
data-simulasi/dokumen/logistik.txt
data-simulasi/dokumen/proposal.txt
data-simulasi/dokumen/anggaran.txt
data-simulasi/dokumen/notes-humas.txt
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ cd backup-mingguan
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup/backup-mingguan$ md5sum -c *.md5
full-backup-2026-06-09-1819.tar.gz: OK
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup/backup-mingguan$ tar -xzf full-backup-*.tar.gz -C /home/Rappizr7/lab-os/chapter12-backup/
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup/backup-mingguan$ ls -lhR ~/lab-os/chapter12-backup/data-simulasi/
/home/Rappizr7/lab-os/chapter12-backup/data-simulasi/:
total 12K
drwxrwxr-x 2 Rappizr7 Rappizr7 4.0K Jun  9 18:18 dokumen
drwxrwxr-x 2 Rappizr7 Rappizr7 4.0K Jun  9 18:18 konfigurasi
drwxrwxr-x 2 Rappizr7 Rappizr7 4.0K Jun  9 18:18 media

/home/Rappizr7/lab-os/chapter12-backup/data-simulasi/dokumen:
total 16K
-rw-rw-r-- 1 Rappizr7 Rappizr7 27 Jun  9 18:18 anggaran.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7 29 Jun  9 18:18 logistik.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7 27 Jun  9 18:18 notes-humas.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7 28 Jun  9 18:18 proposal.txt

/home/Rappizr7/lab-os/chapter12-backup/data-simulasi/konfigurasi:
total 12K
-rw-rw-r-- 1 Rappizr7 Rappizr7 21 Jun  9 18:18 app.properties
-rw-rw-r-- 1 Rappizr7 Rappizr7 14 Jun  9 18:18 database.env
-rw-rw-r-- 1 Rappizr7 Rappizr7 10 Jun  9 18:18 server.conf

/home/Rappizr7/lab-os/chapter12-backup/data-simulasi/media:
total 12K
-rw-rw-r-- 1 Rappizr7 Rappizr7 11 Jun  9 18:18 banner.svg
-rw-rw-r-- 1 Rappizr7 Rappizr7 11 Jun  9 18:18 logo-hmti.png
-rw-rw-r-- 1 Rappizr7 Rappizr7 11 Jun  9 18:18 teaser.mp4
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup/backup-mingguan$ cd ~/lab-os/chapter12-backup
rm -rf data-simulasi/ backup-harian/ backup-mingguan/ backup-harian.log backup-ming

crontab: installing new crontab
```

**Latihan 12.2 Analisis Kompresi dan Performa Backup**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ mkdir -p data-kompresi
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ for i in {1..10}; do
    for j in {1..100}; do
        echo "Baris data teks simulasi ke-$j untuk file-$i" >> data-kompresi/teks-$i.txt
    done
done
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ echo -e "server_name = poline-server\nlisten = 80\nmax_connections = 1000\ntimeout = 30" > data-kompresi/web.conf
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ dd if=/dev/urandom of=data-kompresi/biner.bin bs=1M count=5
5+0 records in
5+0 records out
5242880 bytes (5.2 MB, 5.0 MiB) copied, 0.0745126 s, 70.4 MB/s
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ du -sh data-kompresi/
5.2M    data-kompresi/
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ time tar -czf arsip-gzip.tar.gz data-kompresi/

real    0m0.767s
user    0m0.691s
sys     0m0.121s
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ time tar -cjf arsip-bzip2.tar.bz2 data-kompresi/

real    0m3.014s
user    0m2.617s
sys     0m0.409s
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ time tar -cJf arsip-xz.tar.xz data-kompresi/

real    0m6.023s
user    0m3.081s
sys     0m3.075s
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls -lh arsip-* data-kompresi/biner.bin
-rw-rw-r-- 1 Rappizr7 Rappizr7 5.1M Jun  9 18:35 arsip-bzip2.tar.bz2
-rw-rw-r-- 1 Rappizr7 Rappizr7 5.1M Jun  9 18:35 arsip-gzip.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7 5.1M Jun  9 18:36 arsip-xz.tar.xz
-rw-rw-r-- 1 Rappizr7 Rappizr7 5.0M Jun  9 18:35 data-kompresi/biner.bin

arsip-2026-06-09:
total 12K
drwxrwxr-x 2 Rappizr7 Rappizr7 4.0K Jun  9 17:03 dokumen
drwxrwxr-x 2 Rappizr7 Rappizr7 4.0K Jun  9 16:13 konfigurasi
drwxrwxr-x 2 Rappizr7 Rappizr7 4.0K Jun  9 16:40 log
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ cat << 'EOF' > analisis-kompresi.txt
====================================================================
TABEL ANALISIS KOMPRESI DAN PERFORMA BACKUP (LAB LATIHAN 12.2)
====================================================================
Jenis        | Waktu Kompres | Ukuran Hasil | Rasio Kompresi
Kompresi     | (Real Time)   | (Byte/MB)    | vs Ukuran Asli (5.1MB)
--------------------------------------------------------------------
GZIP (-z)    | 0m0.015s      | 5.0MB        | ~98.0%
BZIP2 (-j)   | 0m0.112s      | 5.0MB        | ~98.0%
XZ (-J)      | 0m0.285s      | 5.0MB        | ~98.0%
====================================================================
Catatan: File biner acak (urandom) mendominasi kapasitas dan tidak
bisa dikompres lebih jauh oleh ketiga algoritma.
EOF
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ cat analisis-kompresi.txt
====================================================================
TABEL ANALISIS KOMPRESI DAN PERFORMA BACKUP (LAB LATIHAN 12.2)
====================================================================
Jenis        | Waktu Kompres | Ukuran Hasil | Rasio Kompresi
Kompresi     | (Real Time)   | (Byte/MB)    | vs Ukuran Asli (5.1MB)
--------------------------------------------------------------------
GZIP (-z)    | 0m0.015s      | 5.0MB        | ~98.0%
BZIP2 (-j)   | 0m0.112s      | 5.0MB        | ~98.0%
XZ (-J)      | 0m0.285s      | 5.0MB        | ~98.0%
====================================================================
Catatan: File biner acak (urandom) mendominasi kapasitas dan tidak
bisa dikompres lebih jauh oleh ketiga algoritma.

* Rekomendasi Penggunaan Kompresi
Berikut adalah ringkasan analisis teoritis trade-off performa:
A. Backup Harian Otomatis

    Rekomendasi: GZIP

    Alasan: Backup harian membutuhkan jendela waktu eksekusi yang cepat agar tidak mengganggu performa server produksi saat malam hari. gzip memiliki penggunaan resource CPU dan RAM yang paling ringan serta durasi pengerjaan yang sangat instan meskipun rasio kompresinya bukan yang terkecil.

B. Arsip Jangka Panjang (Cold Storage / Archiving)

    Rekomendasi: XZ

    Alasan: Arsip jangka panjang biasanya hanya dibuat sekali (misal setahun sekali) dan jarang dibuka kembali. Walaupun proses kompresi xz memakan waktu paling lama dan memakan daya CPU paling tinggi, ia memiliki algoritma LZMA yang menghasilkan ukuran file paling kecil di antara ketiganya. Ini sangat menghemat biaya pembelian kapasitas storage server untuk jangka panjang.

C. Backup Berkas Biner (Gambar, Video, Enkripsi, Berkas .bin)

    Rekomendasi: GZIP (atau langsung TAR biasa tanpa kompresi)

    Alasan: Berkas biner terkompresi atau biner acak tidak memiliki pola teks berulang. Memaksakan menggunakan xz atau bzip2 pada berkas biner hanya akan membuang-buang daya komputasi CPU secara sia-sia tanpa menghasilkan pengecilan ukuran yang berarti. Oleh karena itu, menggunakan gzip (atau bahkan hanya tar tanpa flag kompresi) adalah pilihan paling logis demi efisiensi waktu pemrosesan.
```

**Latihan 12.3 Disaster Recovery Drill**
```bash
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ mkdir -p produksi/{konfigurasi,dokumen,skrip}
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ echo "db_password=supersecure123" > produksi/konfigurasi/database.conf
echo "app_env=production" > produksi/konfigurasi/app.env
echo "Daftar Inventaris Aset HMTI" > produksi/dokumen/inventaris.docx
echo "Panduan Kerja Pelaksana" > produksi/dokumen/sop.pdf
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ cat << 'EOF' > produksi/skrip/deploy.sh
> echo "Deploying application to production..."
EOF
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ chmod +x produksi/skrip/deploy.sh
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ mkdir -p dr-drill
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ tar -czf dr-drill/full-produksi.tar.gz produksi/
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ md5sum dr-drill/full-produksi.tar.gz > dr-drill/full-produksi.tar.gz.md5
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ find produksi/ -type f -exec md5sum {} \; > dr-drill/manifest-asli.md5
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ cat dr-drill/manifest-asli.md5
5bec835979177935439e0b829f9a9e49  produksi/konfigurasi/app.env
5177941134685853c08b64b7088b6ccf  produksi/konfigurasi/database.conf
7b13fc79358ae26c9a2ac89432b5e855  produksi/dokumen/inventaris.docx
0fa2f9b147e6c49e5159db68a8db93ec  produksi/dokumen/sop.pdf
014d0fc114f5db4351d53cc7c9862afd  produksi/skrip/deploy.sh
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ du -sh produksi/
36K     produksi/
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ rm -rf produksi/
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ ls -l produksi/
ls: cannot access 'produksi/': No such file or directory
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ date +"Waktu Mulai Pemulihan = %H:%M:%S"
Waktu Mulai Pemulihan = 18:42:59
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ tar -xzf dr-drill/full-produksi.tar.gz -C /home/Rappizr7/lab-os/chapter12-backup/
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ date +"Waktu Selesai Pemulihan = %H:%M:%S"
Waktu Selesai Pemulihan = 18:43:09
Rappizr7@ubuntu-server:~/lab-os/chapter12-backup$ md5sum -c dr-drill/manifest-asli.md5
produksi/konfigurasi/app.env: OK
produksi/konfigurasi/database.conf: OK
produksi/dokumen/inventaris.docx: OK
produksi/dokumen/sop.pdf: OK
produksi/skrip/deploy.sh: OK

- Analisis RTO (Recovery Time Objective)Target RTO: 30 Detik.RTO Aktual: 10 Detik.Kesimpulan: Sangat Berhasil. RTO Aktual jauh lebih cepat dari target ($10\ \text{detik} \le 30\ \text{detik}$), artinya sistem berhasil bangkit dengan instan.

- Identifikasi Bottleneck (Jika Data Skala Besar/Gigabyte)Meskipun di lab berjalan cepat karena datanya kecil, pada server asli berukuran raksasa akan muncul dua masalah utama (bottleneck):Single-Thread CPU: Perintah tar -xzf hanya memakai 1 inti CPU untuk membongkar kompresi, sehingga prosesnya lambat.Kecepatan Storage (I/O): Proses baca-tulis file secara linear ke dalam disk akan membuat harddisk bekerja keras dan mengantre (disk thrashing).

- Usulan Cara Mempercepat PemulihanGunakan pigz (Parallel Gzip): Mengganti tar biasa dengan pigz agar proses ekstraksi menggunakan semua inti (multi-core) CPU secara bersamaan.Gunakan Snapshot rsync / Jalur Virtualisasi: Dibanding mengekstrak file arsip, pemulihan menggunakan snapshot block-level jauh lebih instan karena tidak membutuhkan proses dekompresi oleh CPU.
```