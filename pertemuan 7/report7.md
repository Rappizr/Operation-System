# Laporan Pertemuan ke-4 Sistem Operasi

**Tanggal:** 08 April 2026  
**Disusun Oleh:** Mukhammad Raffi Zabra  
**NIM:** 254107020059  
**Kelas/No:** TI-1G/24  

---

## Tugas Pendahuluan

1.8 Tugas Praktikum
pribadi yang otomatis aktif setiap login.
Instruksi tugas:
1. Tambahkan konfigurasi pada .bashrc untuk:
• menambahkan direktori bin pribadi ke PATH,
• membuat minimal 2 alias yang membantu kerja harian,
• membuat minimal 1 fungsi shell yang berguna untuk administrasi.

- Rappizr7@ubuntu-server:~$ mkdir -p ~/praktikum-os/week07-bash/{bin,backup,logs,sampel,ruang-nama}

cd ~/praktikum-os/week07-bash

touch sample-app.conf
touch logs/app-{01,02,03}.log
touch sampel/catatan-{a,b}.txt
touch sampel/backup-{01,02}.tar
touch "ruang-nama/laporan server april.txt"
touch "ruang-nama/backup [mingguan] server.conf"

echo "Workspace siap"
Workspace siap
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$  mkdir -p ~/praktikum-os/week07-bash/{bin,backup,logs,sampel,ruang-nama}
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$  cd ~/praktikum-os/week07-bash
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ touch sample-app.conf
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$  touch logs/app-{01,02,03}.log
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ touch sampel/catatan-{a,b}.txt
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ touch sampel/backup-{01,02}.tar
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ touch "ruang-nama/laporan server april.txt"
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ touch "ruang-nama/backup [mingguan] server.conf"
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$  echo "Workspace siap"
Workspace siap

2. Pastikan konfigurasi tersebut aktif kembali saat membuka shell login.

- Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ cp ~/.bashrc ~/.bashrc.bak-praktikum-$(date +%Y%m%d)
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ cat <<'EOF' >> ~/.bashrc
>  ===== PRAKTIKUM BASH SHELL - TUGAS 1 =====
# 1. PATH ke direktori bin pribadi
export PATH="$HOME/praktikum-os/week07-bash/bin:$PATH"

# 2. Alias untuk kerja harian
alias ll='ls -lah --color=auto'
alias hist10='history | tail -10'
alias dfh='df -h'
alias freeh='free -h'

# 3. Fungsi untuk backup dengan timestamp
backup_file() {
    if [ $# -ne 1 ]; then
        echo "Usage: backup_file <nama_file>"
        return 1
    fi
    local src="$1"
    local dst="$HOME/praktikum-os/week07-bash/backup"
    if [ ! -f "$src" ]; then
        echo "Error: File '$src' tidak ditemukan!"
        return 2
    fi
    mkdir -p "$dst"
    local timestamp=$(date +%Y%m%d_%H%M%S)
    cp "$src" "$dst/$(basename "$src").$timestamp.bak"
    echo "Backup berhasil: $dst/$(basename "$src").$timestamp.bak"
}

===== END PRAKTIKUM =====
> EOF
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ source ~/.bashrc
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ echo "Konfigurasi ditambahkan ke .bashrc"
Konfigurasi ditambahkan ke .bashrc

3. Buat satu script sederhana di direktori bin pribadi, misalnya script untuk
menampilkan ringkasan sistem.

- Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$ cat <<'EOF' > ~/praktikum-os/week07-bash/bin/ringkas-sistem
>  #!/usr/bin/env bash

echo "=========================================="
echo "        RINGKASAN SISTEM"
echo "=========================================="
echo "Hostname    : $(hostname)"
echo "User        : $(whoami)"
echo "Tanggal     : $(date '+%Y-%m-%d %H:%M:%S')"
echo "Uptime      : $(uptime -p)"
echo "Memory      : $(free -h | grep Mem | awk '{print $3 "/" $2}')"
echo "Disk /      : $(df -h / | tail -1 | awk '{print $3 "/" $2 " (" $5 ")"}')"
echo "Shell aktif : $SHELL"
echo "=========================================="
> EOF
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ chmod +x ~/praktikum-os/week07-bash/bin/ringkas-sistem
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ echo "Script ringkas-sistem siap"
Script ringkas-sistem siap

4. Uji dari direktori yang berbeda untuk memastikan script dapat dipanggil tanpa
menuliskan path lengkap.

- Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ echo "=== TEST 1: Isi PATH ==="
=== TEST 1: Isi PATH ===
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$  echo "$PATH" | tr ':' '\n' | grep "week07-bash/bin"
/home/Rappizr7/praktikum-os/week07-bash/bin
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ echo -e "\n=== TEST 2: Uji alias ll ==="

=== TEST 2: Uji alias ll ===
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ ll ~/praktikum-os/week07-bash/ | head -5
total 28K
drwxrwxr-x 7 Rappizr7 Rappizr7 4.0K Apr 13 01:13 .
drwxrwxr-x 3 Rappizr7 Rappizr7 4.0K Apr 13 01:13 ..
drwxrwxr-x 2 Rappizr7 Rappizr7 4.0K Apr 13 01:13 backup
drwxrwxr-x 2 Rappizr7 Rappizr7 4.0K Apr 13 01:21 bin
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ echo -e "\n=== TEST 3: Uji fungsi backup_file ==="

=== TEST 3: Uji fungsi backup_file ===
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ backup_file ~/praktikum-os/week07-bash/sample-app.conf
Backup berhasil: /home/Rappizr7/praktikum-os/week07-bash/backup/sample-app.conf.20260413_012320.bak
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ echo -e "\n=== TEST 4: Uji script ringkas-sistem ==="

=== TEST 4: Uji script ringkas-sistem ===
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ cd /tmp
Rappizr7@ubuntu-server:/tmp$ ringkas-sistem
==========================================
        RINGKASAN SISTEM
==========================================
Hostname    : ubuntu-server
User        : Rappizr7
Tanggal     : 2026-04-13 01:23:50
Uptime      : up 1 hour, 4 minutes
Memory      : 477Mi/3.9Gi
Disk /      : 7.6G/52G (16%)
Shell aktif : /bin/bash
==========================================
Rappizr7@ubuntu-server:/tmp$ cd ~/praktikum-os/week07-bash
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$

5. Simpan bukti pengujian ke file toolkit-bash-report.txt.
Minimal luaran:
• isi blok konfigurasi yang ditambahkan ke .bashrc,
• output echo $PATH,
• output type untuk alias, fungsi, dan script,
• file laporan toolkit-bash-report.txt.

- Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ cd /praktikum-os/week07-bash
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ echo "==========================================" > toolkit-bash-report.txt
echo "LAPORAN TUGAS PRAKTIKUM 1" >> toolkit-bash-report.txt
echo "Toolkit Bash Administrator Pribadi" >> toolkit-bash-report.txt
echo "==========================================" >> toolkit-bash-report.txt
echo "" >> toolkit-bash-report.txt
echo "Tanggal: $(date '+%Y-%m-%d %H:%M:%S')" >> toolkit-bash-report.txt
echo "User: $(whoami)" >> toolkit-bash-report.txt
echo "Host: $(hostname)" >> toolkit-bash-report.txt
echo "" >> toolkit-bash-report.txt
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ echo "------------------------------------------" >> toolkit-bash-report.txt
echo "1. KONFIGURASI YANG DITAMBAHKAN KE .bashrc" >> toolkit-bash-report.txt
echo "------------------------------------------" >> toolkit-bash-report.txt
echo "" >> toolkit-bash-report.txt
grep -A 30 "PRAKTIKUM BASH SHELL" /.bashrc >> toolkit-bash-report.txt
echo "" >> toolkit-bash-report.txt
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ echo "------------------------------------------" >> toolkit-bash-report.txt
echo "2. HASIL \$PATH" >> toolkit-bash-report.txt
echo "------------------------------------------" >> toolkit-bash-report.txt
echo "$PATH" | tr ':' '\n' >> toolkit-bash-report.txt
echo "" >> toolkit-bash-report.txt
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$ echo "------------------------------------------" >> toolkit-bash-report.txt
echo "3. TYPE ALIAS, FUNGSI, DAN SCRIPT" >> toolkit-bash-report.txt
echo "------------------------------------------" >> toolkit-bash-report.txt
echo "" >> toolkit-bash-report.txt

echo "--- Alias ll ---" >> toolkit-bash-report.txt
type ll >> toolkit-bash-report.txt 2>&1
echo "" >> toolkit-bash-report.txt

echo "--- Fungsi backup_file ---" >> toolkit-bash-report.txt
type backup_file >> toolkit-bash-report.txt 2>&1
echo "" >> toolkit-bash-report.txt

echo "--- Script ringkas-sistem ---" >> toolkit-bash-report.txt
type ringkas-sistem >> toolkit-bash-report.txt 2>&1
echo "" >> toolkit-bash-report.txt
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$ echo "------------------------------------------" >> toolkit-bash-report.txt
echo "4. HASIL UJI COBA" >> toolkit-bash-report.txt
echo "------------------------------------------" >> toolkit-bash-report.txt
echo "" >> toolkit-bash-report.txt

echo "Hasil uji alias ll:" >> toolkit-bash-report.txt
ll /praktikum-os/week07-bash/ >> toolkit-bash-report.txt 2>&1
echo "" >> toolkit-bash-report.txt

echo "Hasil uji fungsi backup_file:" >> toolkit-bash-report.txt
backup_file /praktikum-os/week07-bash/sample-app.conf >> toolkit-bash-report.txt 2>&1
echo "" >> toolkit-bash-report.txt

echo "Hasil uji script ringkas-sistem:" >> toolkit-bash-report.txt
ringkas-sistem >> toolkit-bash-report.txt 2>&1
echo "" >> toolkit-bash-report.txt
Rappizr7@ubuntu-server/praktikum-os/week07-bash$ echo "==========================================" >> toolkit-bash-report.txt
echo "LAPORAN SELESAI" >> toolkit-bash-report.txt
echo "==========================================" >> toolkit-bash-report.txt
Rappizr7@ubuntu-server:/praktikum-os/week07-bash$ cat toolkit-bash-report.txt
==========================================
LAPORAN TUGAS PRAKTIKUM 1
Toolkit Bash Administrator Pribadi
==========================================

Tanggal: 2026-04-13 01:31:27
User: Rappizr7
Host: ubuntu-server

------------------------------------------
1. KONFIGURASI YANG DITAMBAHKAN KE .bashrc
------------------------------------------

# ===== PRAKTIKUM BASH SHELL - TUGAS 1 =====
# 1. PATH ke direktori bin pribadi
export PATH="$HOME/praktikum-os/week07-bash/bin:$PATH"

# 2. Alias untuk kerja harian
alias ll='ls -lah --color=auto'
alias hist10='history | tail -10'
alias dfh='df -h'
alias freeh='free -h'

# 3. Fungsi untuk backup dengan timestamp
backup_file() {
    if [ $# -ne 1 ]; then
        echo "Usage: backup_file <nama_file>"
        return 1
    fi
    local src="$1"
    local dst="$HOME/praktikum-os/week07-bash/backup"
    if [ ! -f "$src" ]; then
        echo "Error: File '$src' tidak ditemukan!"
        return 2
    fi
    mkdir -p "$dst"
    local timestamp=$(date +%Y%m%d_%H%M%S)
    cp "$src" "$dst/$(basename "$src").$timestamp.bak"
    echo "Backup berhasil: $dst/$(basename "$src").$timestamp.bak"
}
# ===== END PRAKTIKUM =====

------------------------------------------
2. HASIL $PATH
------------------------------------------
/home/Rappizr7/praktikum-os/week07-bash/bin
/usr/local/sbin
/usr/local/bin
/usr/sbin
/usr/bin
/sbin
/bin
/usr/games
/usr/local/games
/snap/bin

------------------------------------------
3. TYPE ALIAS, FUNGSI, DAN SCRIPT
------------------------------------------

--- Alias ll ---
ll is aliased to `ls -lah --color=auto'

--- Fungsi backup_file ---
backup_file is a function
backup_file ()
{
    if [ $# -ne 1 ]; then
        echo "Usage: backup_file <nama_file>";
        return 1;
    fi;
    local src="$1";
    local dst="$HOME/praktikum-os/week07-bash/backup";
    if [ ! -f "$src" ]; then
        echo "Error: File '$src' tidak ditemukan!";
        return 2;
    fi;
    mkdir -p "$dst";
    local timestamp=$(date +%Y%m%d_%H%M%S);
    cp "$src" "$dst/$(basename "$src").$timestamp.bak";
    echo "Backup berhasil: $dst/$(basename "$src").$timestamp.bak"
}

--- Script ringkas-sistem ---
ringkas-sistem is hashed (/home/Rappizr7/praktikum-os/week07-bash/bin/ringkas-sistem)

------------------------------------------
4. HASIL UJI COBA
------------------------------------------

Hasil uji alias ll:
total 32K
drwxrwxr-x 7 Rappizr7 Rappizr7 4.0K Apr 13 01:26 .
drwxrwxr-x 3 Rappizr7 Rappizr7 4.0K Apr 13 01:13 ..
drwxrwxr-x 2 Rappizr7 Rappizr7 4.0K Apr 13 01:23 backup
drwxrwxr-x 2 Rappizr7 Rappizr7 4.0K Apr 13 01:21 bin
drwxrwxr-x 2 Rappizr7 Rappizr7 4.0K Apr 13 01:13 logs
drwxrwxr-x 2 Rappizr7 Rappizr7 4.0K Apr 13 01:13 ruang-nama
drwxrwxr-x 2 Rappizr7 Rappizr7 4.0K Apr 13 01:13 sampel
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 01:14 sample-app.conf
-rw-rw-r-- 1 Rappizr7 Rappizr7 2.4K Apr 13 01:31 toolkit-bash-report.txt

Hasil uji fungsi backup_file:
Backup berhasil: /home/Rappizr7/praktikum-os/week07-bash/backup/sample-app.conf.20260413_013153.bak

Hasil uji script ringkas-sistem:
==========================================
        RINGKASAN SISTEM
==========================================
Hostname    : ubuntu-server
User        : Rappizr7
Tanggal     : 2026-04-13 01:31:53
Uptime      : up 1 hour, 12 minutes
Memory      : 476Mi/3.9Gi
Disk /      : 7.6G/52G (16%)
Shell aktif : /bin/bash
==========================================

==========================================
LAPORAN SELESAI
==========================================
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$


## Tugas Praktikum 2 — Audit File Konfigurasi dan
Logging Aman
Konteks riil: saat troubleshooting, administrator sering perlu menginventarisasi
file konfigurasi dan memisahkan output normal dari pesan error.
Instruksi tugas:
1. Buat file laporan bernama audit-konfigurasi-$(date +%F).txt.
2. Cari file *.conf di dalam /etc dan simpan hasilnya ke file laporan.
3. Catat jumlah total file konfigurasi yang ditemukan.
4. Jika ada pesan error, simpan ke file terpisah, misalnya audit-error.log.
5. Tampilkan isi laporan ke terminal dan sekaligus simpan menggunakan tee.
6. Tambahkan ringkasan singkat 3–5 baris yang menjelaskan mengapa pemisahan
stdout dan stderr penting dalam audit sistem.
20 1 Bash Shell dan Shell Basic
1.8 Tugas Praktikum
Syarat konsep yang harus muncul:
• redirection >, 2>, atau &>,
• pipeline,
• tee,
• penggunaan variabel atau command substitution.
Minimal luaran:
• file laporan audit,
• file log error,
• perintah yang digunakan,
• analisis singkat hasil audit.

-------------------------------------------------
- langkah 1
cd ~/praktikum-os/week07-bash

TGL=$(date +%F)

echo "=== MEMULAI AUDIT KONFIGURASI ==="
echo "Tanggal audit: $TGL"
echo "Laporan: audit-konfigurasi-$TGL.txt"
echo "Error log: audit-error.log"

- langkah 2
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$ find /etc -name "*.conf" 2> audit-error.log > audit-konfigurasi-$TGL.txt

echo "=== HASIL PENCARIAN FILE .conf ==="
echo "Jumlah file ditemukan: $(wc -l < audit-konfigurasi-$TGL.txt)"
echo "Jumlah error: $(wc -l < audit-error.log)"
=== HASIL PENCARIAN FILE .conf ===
Jumlah file ditemukan: 179
Jumlah error: 5

- langkah 3
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$ echo "=== 10 FILE .conf PERTAMA ===" | tee -a audit-ringkasan-$TGL.txt
head -10 audit-konfigurasi-$TGL.txt | tee -a audit-ringkasan-$TGL.txt

echo -e "\n=== JUMLAH TOTAL ===" | tee -a audit-ringkasan-$TGL.txt
echo "Total file konfigurasi: $(wc -l < audit-konfigurasi-$TGL.txt)" | tee -a audit-ringkasan-$TGL.txt
=== 10 FILE .conf PERTAMA ===
/etc/updatedb.conf
/etc/libaudit.conf
/etc/sysctl.conf
/etc/rsyslog.d/50-default.conf
/etc/rsyslog.d/21-cloudinit.conf
/etc/rsyslog.d/20-ufw.conf
/etc/ca-certificates.conf
/etc/usb_modeswitch.conf
/etc/udev/iocost.conf
/etc/udev/udev.conf

=== JUMLAH TOTAL ===
Total file konfigurasi: 179

- langkah 4
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$ echo -e "\n=== FILE YANG BERHUBUNGAN DENGAN NETWORK/SSH ===" | tee -a audit-ringkasan-$TGL.txt

find /etc -name "*.conf" 2>/dev/null | grep -E "network|ssh" | sort | tee -a audit-ringkasan-$TGL.txt

echo -e "\nJumlah file network/ssh: $(find /etc -name "*.conf" 2>/dev/null | grep -E "network|ssh" | wc -l)" | tee -a audit-ringkasan-$TGL.txt

=== FILE YANG BERHUBUNGAN DENGAN NETWORK/SSH ===
/etc/modprobe.d/blacklist-rare-network.conf
/etc/sysctl.d/10-network-security.conf
/etc/systemd/networkd.conf

Jumlah file network/ssh: 3

- langkah 5
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$ cat <<EOF > audit-final-$TGL.txt
==========================================
LAPORAN AUDIT KONFIGURASI SISTEM
==========================================
Waktu Audit : $(date '+%Y-%m-%d %H:%M:%S')
User        : $(whoami)
Hostname    : $(hostname)
==========================================

1. STATISTIK FILE KONFIGURASI
   Total file *.conf      : $(wc -l < audit-konfigurasi-$TGL.txt) file
   Total error saat scan  : $(wc -l < audit-error.log) error

2. DIREKTORI DENGAN FILE .conf TERBANYAK
$(find /etc -name "*.conf" 2>/dev/null | xargs dirname | sort | uniq -c | sort -rn | head -5)

3. CONTOH ISI SALAH SATU FILE KONFIGURASI
   (File: /etc/resolv.conf)
------------------------------------------
$(head -5 /etc/resolv.conf 2>/dev/null || echo "Tidak bisa membaca file")
------------------------------------------

==========================================
EOF

echo "✅ Laporan final selesai dibuat"
cat audit-final-$TGL.txt
✅ Laporan final selesai dibuat
==========================================
LAPORAN AUDIT KONFIGURASI SISTEM
==========================================
Waktu Audit : 2026-04-13 01:39:05
User        : Rappizr7
Hostname    : ubuntu-server
==========================================

1. STATISTIK FILE KONFIGURASI
   Total file *.conf      : 179 file
   Total error saat scan  : 5 error

2. DIREKTORI DENGAN FILE .conf TERBANYAK
     47 /etc/fonts/conf.d
     31 /etc
     14 /etc/fonts/conf.avail
     10 /etc/sysctl.d
     10 /etc/security

3. CONTOH ISI SALAH SATU FILE KONFIGURASI
   (File: /etc/resolv.conf)
------------------------------------------
# This is /run/systemd/resolve/stub-resolv.conf managed by man:systemd-resolved(8).
# Do not edit.
#
# This file might be symlinked as /etc/resolv.conf. If you're looking at
# /etc/resolv.conf and seeing this text, you have followed the symlink.
------------------------------------------

==========================================

- langkah 6
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$ cat <<'EOF' >> audit-final-$TGL.txt

4. ANALISIS PENTINGNYA PEMISAHAN STDOUT DAN STDERR
==================================================
Dalam audit sistem, pemisahan stdout (output normal) dan stderr (pesan error)
sangat penting karena:

1. DIAGNOSIS YANG AKURAT
   - Error seperti "Permission denied" menunjukkan file yang tidak bisa diakses
   - Tanpa pemisahan, error akan tercampur dengan hasil audit yang valid

2. PEMANTAUAN KEAMANAN
   - File yang tidak bisa dibaca mungkin butuh investigasi lebih lanjut
   - Bisa mengindikasikan masalah izin atau kebijakan keamanan

3. OTOMASI DAN REPORTING
   - Dengan 2> error.log, kita bisa mengabaikan error yang tidak relevan
   - Atau sebaliknya, fokus hanya pada error untuk troubleshooting

4. INTEGRITAS DATA
   - Output normal tetap bersih untuk dianalisis lebih lanjut
   - Error tidak mencemari data audit yang sebenarnya

Contoh dalam praktik ini:
- stdout (>) ke audit-konfigurasi-*.txt : daftar file valid
- stderr (2>) ke audit-error.log : pesan error (jika ada)

==================================================
EOF

echo "✅ Analisis ditambahkan ke laporan"
✅ Analisis ditambahkan ke laporan

- langlah 7
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$ echo "=== ISI LENGKAP LAPORAN FINAL ==="
cat audit-final-$TGL.txt

echo ""
echo "=== DAFTAR FILE YANG DIHASILKAN ==="
ls -lh audit-* 2>/dev/null
=== ISI LENGKAP LAPORAN FINAL ===
==========================================
LAPORAN AUDIT KONFIGURASI SISTEM
==========================================
Waktu Audit : 2026-04-13 01:39:05
User        : Rappizr7
Hostname    : ubuntu-server
==========================================

1. STATISTIK FILE KONFIGURASI
   Total file *.conf      : 179 file
   Total error saat scan  : 5 error

2. DIREKTORI DENGAN FILE .conf TERBANYAK
     47 /etc/fonts/conf.d
     31 /etc
     14 /etc/fonts/conf.avail
     10 /etc/sysctl.d
     10 /etc/security

3. CONTOH ISI SALAH SATU FILE KONFIGURASI
   (File: /etc/resolv.conf)
------------------------------------------
# This is /run/systemd/resolve/stub-resolv.conf managed by man:systemd-resolved(8).
# Do not edit.
#
# This file might be symlinked as /etc/resolv.conf. If you're looking at
# /etc/resolv.conf and seeing this text, you have followed the symlink.
------------------------------------------

==========================================

4. ANALISIS PENTINGNYA PEMISAHAN STDOUT DAN STDERR
==================================================
Dalam audit sistem, pemisahan stdout (output normal) dan stderr (pesan error)
sangat penting karena:

1. DIAGNOSIS YANG AKURAT
   - Error seperti "Permission denied" menunjukkan file yang tidak bisa diakses
   - Tanpa pemisahan, error akan tercampur dengan hasil audit yang valid

2. PEMANTAUAN KEAMANAN
   - File yang tidak bisa dibaca mungkin butuh investigasi lebih lanjut
   - Bisa mengindikasikan masalah izin atau kebijakan keamanan

3. OTOMASI DAN REPORTING
   - Dengan 2> error.log, kita bisa mengabaikan error yang tidak relevan
   - Atau sebaliknya, fokus hanya pada error untuk troubleshooting

4. INTEGRITAS DATA
   - Output normal tetap bersih untuk dianalisis lebih lanjut
   - Error tidak mencemari data audit yang sebenarnya

Contoh dalam praktik ini:
- stdout (>) ke audit-konfigurasi-*.txt : daftar file valid
- stderr (2>) ke audit-error.log : pesan error (jika ada)

==================================================

=== DAFTAR FILE YANG DIHASILKAN ===
-rw-rw-r-- 1 Rappizr7 Rappizr7  249 Apr 13 01:37 audit-error.log
-rw-rw-r-- 1 Rappizr7 Rappizr7 2.0K Apr 13 01:39 audit-final-2026-04-13.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7 5.9K Apr 13 01:37 audit-konfigurasi-2026-04-13.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7  507 Apr 13 01:38 audit-ringkasan-2026-04-13.txt


## Tugas Praktikum 3 — Mini Health Check Harian
Server
Konteks riil: administrator perlu membuat pemeriksaan cepat (health check) untuk
mengetahui kondisi dasar server sebelum dan sesudah maintenance.
Instruksi tugas:
1. Buat script Bash bernama daily-healthcheck pada direktori bin pribadi.
2. Script minimal harus menampilkan:
• tanggal dan waktu,
• hostname,
• user aktif,
• shell aktif,
• uptime,
• penggunaan memori,
• penggunaan filesystem root,
• 10 baris terakhir history command yang relevan dengan pengecekan.
3. Simpan hasil ke file log harian, misalnya healthcheck-$(date +%F).log.
4. Tampilkan hasil ke terminal dan ke file secara bersamaan.
5. Jika Anda menggunakan pipeline dengan tee, cek juga status exit command
utama.
Syarat konsep yang harus muncul:
• environment variable,
• PATH,
• alias atau fungsi pendukung,
• history,
• tee,
• penanganan error dasar.
Minimal luaran:
• file script yang executable,
• contoh isi file log hasil eksekusi,
• penjelasan singkat fungsi tiap bagian script.

--------------------------------------------------

- langkah 1:
Rappizr7@ubuntu-server:~$ cd ~/praktikum-os/week07-bash

cat <<'EOF' > bin/daily-healthcheck
#!/usr/bin/env bash

# ============================================
# SCRIPT: daily-healthcheck
# FUNGSI: Health check harian server
# ============================================

# Konfigurasi
LOG_DIR="$HOME/praktikum-os/week07-bash/logs"
TGL=$(date +%F)
LOG_FILE="$LOG_DIR/healthcheck-$TGL.log"

# Buat direktori logs jika belum ada
mkdir -p "$LOG_DIR"

# Fungsi untuk menampilkan header
show_header() {
    echo "=========================================="
    echo "     DAILY HEALTH CHECK SERVER"
    echo "=========================================="
}

# Mulai health check
{
    show_header
    echo ""

    # 1. Tanggal dan Waktu
    echo " [1] TANGGAL & WAKTU"
    echo "    $(date '+%Y-%m-%d %H:%M:%S')"
    echo ""

    # 2. Hostname
    echo " [2] HOSTNAME"
    echo "    $(hostname)"
    echo ""

    # 3. User Aktif
    echo " [3] USER AKTIF"
    echo "    User: $(whoami)"
    echo "    Shell: $SHELL"
    echo ""

    # 4. Uptime
    echo " [4] UPTIME"
    echo "    $(uptime -p)"
    echo ""

    # 5. Penggunaan Memory
    echo " [5] PENGGUNAAN MEMORY"
    free -h | grep -E "Mem|Swap" | while read line; do
        echo "    $line"
    done
    echo ""

    # 6. Penggunaan Filesystem Root
    echo " [6] PENGGUNAAN FILESYSTEM ROOT (/)"
chmod +x bin/daily-healthcheckmenjalankan health check"M:%S')"ia"enctl" | tail -10 | while read line; do

- langkah 2:
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$ daily-healthcheck
==========================================
     DAILY HEALTH CHECK SERVER
==========================================

 [1] TANGGAL & WAKTU
    2026-04-13 05:33:53

 [2] HOSTNAME
    ubuntu-server

 [3] USER AKTIF
    User: Rappizr7
    Shell: /bin/bash

 [4] UPTIME
    up 1 hour, 40 minutes

 [5] PENGGUNAAN MEMORY
    Mem:           3.9Gi       560Mi       2.3Gi       1.1Mi       1.4Gi       3.4Gi
    Swap:          4.0Gi          0B       4.0Gi

 [6] PENGGUNAAN FILESYSTEM ROOT (/)
    Size: 52G | Used: 7.6G | Avail: 42G | Use%: 16%

 [7] 10 PERINTAH TERAKHIR (history)
    (Perintah yang berhubungan dengan monitoring)

 [8] 3 PROSES DENGAN CPU TERTINGGI
    Rappizr7    8513  166  0.1  10884  4612 pts/0    R+   05:33   0:00 ps aux --sort=-%cpu
    root        8427 32.8  3.1 369280 129368 ?       Sl   05:33   0:08 /usr/bin/python3 /usr/bin/unattended-upgrade --download-only
    Rappizr7    8496  8.3  0.0   7340  2120 pts/0    S+   05:33   0:00 bash /home/Rappizr7/praktikum-os/week07-bash/bin/daily-healthcheck

 [9] STATUS SERVICE PENTING
     ssh: running
     cron: running

==========================================
     DAILY HEALTH CHECK SERVER
==========================================
 Health check selesai: 2026-04-13 05:33:53

 Laporan tersimpan di: /home/Rappizr7/praktikum-os/week07-bash/logs/healthcheck-2026-04-13.log

 - langkah 3:
 Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$ cd /tmp
daily-healthcheck
cd ~/praktikum-os/week07-bash
==========================================
     DAILY HEALTH CHECK SERVER
==========================================

 [1] TANGGAL & WAKTU
    2026-04-13 05:34:03

 [2] HOSTNAME
    ubuntu-server

 [3] USER AKTIF
    User: Rappizr7
    Shell: /bin/bash

 [4] UPTIME
    up 1 hour, 40 minutes

 [5] PENGGUNAAN MEMORY
    Mem:           3.9Gi       559Mi       2.3Gi       1.1Mi       1.4Gi       3.4Gi
    Swap:          4.0Gi          0B       4.0Gi

 [6] PENGGUNAAN FILESYSTEM ROOT (/)
    Size: 52G | Used: 7.6G | Avail: 42G | Use%: 16%

 [7] 10 PERINTAH TERAKHIR (history)
    (Perintah yang berhubungan dengan monitoring)

 [8] 3 PROSES DENGAN CPU TERTINGGI
    Rappizr7    8541 50.0  0.1  10884  4592 pts/0    R+   05:34   0:00 ps aux --sort=-%cpu
    root        8427 24.2  3.1 369280 129368 ?       Sl   05:33   0:08 /usr/bin/python3 /usr/bin/unattended-upgrade --download-only
    Rappizr7    8520 13.1  0.0   7340  3724 pts/0    S+   05:34   0:00 bash /home/Rappizr7/praktikum-os/week07-bash/bin/daily-healthcheck

 [9] STATUS SERVICE PENTING
     ssh: running
     cron: running

==========================================
     DAILY HEALTH CHECK SERVER
==========================================
 Health check selesai: 2026-04-13 05:34:03

 Laporan tersimpan di: /home/Rappizr7/praktikum-os/week07-bash/logs/healthcheck-2026-04-13.log

 - langkah 4:
 Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$ cat <<'EOF' >> ~/.bashrc

# Alias untuk health check
alias hc='daily-healthcheck'
alias hclog='tail -20 ~/praktikum-os/week07-bash/logs/healthcheck-*.log | tail -20'
EOF

source ~/.bashrc

echo "=== UJI ALIAS hc ==="
hc 2>&1 | head -10
=== UJI ALIAS hc ===
==========================================
     DAILY HEALTH CHECK SERVER
==========================================

 [1] TANGGAL & WAKTU
    2026-04-13 05:34:14

 [2] HOSTNAME
    ubuntu-server

- langkah 5:

Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$ cat <<'EOF' > healthcheck-documentation.txt
==========================================
 DOKUMENTASI DAILY-HEALTHCHECK
==========================================

LOKASI SCRIPT
   ~/praktikum-os/week07-bash/bin/daily-healthcheck

FUNGSI TIAP BAGIAN
------------------------------------------
1. TANGGAL & WAKTU
   - Menampilkan waktu eksekusi health check
   - Berguna untuk tracking kapan pengecekan dilakukan

2. HOSTNAME
   - Memastikan sedang di server yang benar
   - Mencegah kesalahan maintenance di server salah

3. USER AKTIF
   - Mengetahui siapa yang menjalankan health check
   - Menggunakan environment variable $USER dan $SHELL

4. UPTIME
   - Mengetahui sudah berapa lama server berjalan
   - Indikator apakah server baru restart

5. PENGGUNAAN MEMORY
   - Memonitor RAM dan Swap usage
   - Deteksi dini kebocoran memory

6. PENGGUNAAN FILESYSTEM ROOT
   - Memonitor kapasitas disk partisi root
   - Mencegah disk full yang bisa ganggu service

7. 10 PERINTAH TERAKHIR (history)
   - Melihat perintah monitoring sebelumnya
   - Membantu rekap aktivitas administrator

8. 3 PROSES DENGAN CPU TERTINGGI
   - Identifikasi proses yang boros CPU
   - Untuk investigasi performa

9. STATUS SERVICE PENTING
   - Memastikan service critical berjalan
   - ssh: akses remote, cron: job terjadwal

KONSEP BASH YANG DIGUNAKAN
------------------------------------------
Environment Variable: $HOME, $SHELL, $USER
PATH: Script di bin/ bisa dipanggil dari mana saja
Alias: hc untuk daily-healthcheck
History: Mengambil perintah dari history
tee: Output ke terminal dan file sekaligus
Penanganan error: Cek exit status dengan $?

OUTPUT
------------------------------------------
Terminal    : Menampilkan hasil health check
Log file    : ~/praktikum-os/week07-bash/logs/healthcheck-YYYY-MM-DD.log

cat healthcheck-documentation.txt=========
==========================================
 DOKUMENTASI DAILY-HEALTHCHECK
==========================================

LOKASI SCRIPT
   ~/praktikum-os/week07-bash/bin/daily-healthcheck

FUNGSI TIAP BAGIAN
------------------------------------------
1. TANGGAL & WAKTU
   - Menampilkan waktu eksekusi health check
   - Berguna untuk tracking kapan pengecekan dilakukan

2. HOSTNAME
   - Memastikan sedang di server yang benar
   - Mencegah kesalahan maintenance di server salah

3. USER AKTIF
   - Mengetahui siapa yang menjalankan health check
   - Menggunakan environment variable $USER dan $SHELL

4. UPTIME
   - Mengetahui sudah berapa lama server berjalan
   - Indikator apakah server baru restart

5. PENGGUNAAN MEMORY
   - Memonitor RAM dan Swap usage
   - Deteksi dini kebocoran memory

6. PENGGUNAAN FILESYSTEM ROOT
   - Memonitor kapasitas disk partisi root
   - Mencegah disk full yang bisa ganggu service

7. 10 PERINTAH TERAKHIR (history)
   - Melihat perintah monitoring sebelumnya
   - Membantu rekap aktivitas administrator

8. 3 PROSES DENGAN CPU TERTINGGI
   - Identifikasi proses yang boros CPU
   - Untuk investigasi performa

9. STATUS SERVICE PENTING
   - Memastikan service critical berjalan
   - ssh: akses remote, cron: job terjadwal

KONSEP BASH YANG DIGUNAKAN
------------------------------------------
Environment Variable: $HOME, $SHELL, $USER
PATH: Script di bin/ bisa dipanggil dari mana saja
Alias: hc untuk daily-healthcheck
History: Mengambil perintah dari history
tee: Output ke terminal dan file sekaligus
Penanganan error: Cek exit status dengan $?

OUTPUT
------------------------------------------
Terminal    : Menampilkan hasil health check
Log file    : ~/praktikum-os/week07-bash/logs/healthcheck-YYYY-MM-DD.log

==========================================

- langkah 6:
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$ echo ""
echo "=== DAFTAR FILE YANG DIHASILKAN ==="
echo ""

echo "1. Script:"
ls -lh bin/daily-healthcheck

echo ""
echo "2. Log files:"
ls -lh logs/healthcheck-*.log 2>/dev/null

echo ""
echo "3. Dokumentasi:"
ls -lh healthcheck-documentation.txt

echo ""
echo "=== ISI LOG TERAKHIR ==="
tail -30 logs/healthcheck-*.log 2>/dev/null | head -30

=== DAFTAR FILE YANG DIHASILKAN ===

1. Script:
-rwxrwxr-x 1 Rappizr7 Rappizr7 2.6K Apr 13 05:33 bin/daily-healthcheck

2. Log files:
-rw-rw-r-- 1 Rappizr7 Rappizr7 2.7K Apr 13 05:34 logs/healthcheck-2026-04-13.log

3. Dokumentasi:
-rw-rw-r-- 1 Rappizr7 Rappizr7 1.8K Apr 13 05:34 healthcheck-documentation.txt

=== ISI LOG TERAKHIR ===
 [6] PENGGUNAAN FILESYSTEM ROOT (/)
    Size: 52G | Used: 7.6G | Avail: 42G | Use%: 16%

 [7] 10 PERINTAH TERAKHIR (history)
    (Perintah yang berhubungan dengan monitoring)

 [8] 3 PROSES DENGAN CPU TERTINGGI
    Rappizr7    8541 50.0  0.1  10884  4592 pts/0    R+   05:34   0:00 ps aux --sort=-%cpu
    root        8427 24.2  3.1 369280 129368 ?       Sl   05:33   0:08 /usr/bin/python3 /usr/bin/unattended-upgrade --download-only
    Rappizr7    8520 13.1  0.0   7340  3724 pts/0    S+   05:34   0:00 bash /home/Rappizr7/praktikum-os/week07-bash/bin/daily-healthcheck

 [9] STATUS SERVICE PENTING
     ssh: running
     cron: running

==========================================
     DAILY HEALTH CHECK SERVER
==========================================
 Health check selesai: 2026-04-13 05:34:03
==========================================
     DAILY HEALTH CHECK SERVER
==========================================

 [1] TANGGAL & WAKTU
    2026-04-13 05:34:14

 [2] HOSTNAME
    ubuntu-server

 [3] USER AKTIF
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$


## Tugas Praktikum 4 — Penanganan File dengan Nama
Kompleks dan Arsip Aman
Konteks riil: file hasil backup, ekspor, atau laporan sering memiliki nama yang
mengandung spasi atau karakter khusus. Administrator harus tetap dapat memproses
file-file tersebut tanpa salah target.
Instruksi tugas:
1. Buat minimal 4 file contoh dengan nama yang bervariasi, termasuk:
• nama file yang mengandung spasi,
• nama file yang mengandung tanda kurung siku atau karakter khusus,
• file dengan pola nama serupa untuk diuji dengan wildcard.
2. Tunjukkan perbedaan hasil jika file diakses tanpa quoting dan dengan quoting
yang benar.
3. Lakukan preview wildcard dengan echo sebelum dipakai untuk operasi nyata.
4. Salin file-file tersebut ke direktori backup dengan nama yang aman.
5. Buat arsip tar.gz dari hasil backup.
6. Simpan riwayat perintah yang Anda gunakan ke file riwayat-arsip.txt.
Syarat konsep yang harus muncul:
• single quote, double quote, dan escaping,
• wildcard,
• variabel path,
• history,
• operasi file lanjutan yang aman.
Minimal luaran:
• daftar file awal,
• daftar file hasil backup,
• file arsip tar.gz,
• file riwayat-arsip.txt,
• refleksi singkat tentang pentingnya quoting di Bash.

------------------------------------------------------------

- langkah 1:
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$ cd ~/praktikum-os/week07-bash

mkdir -p tugas4-sample tugas4-backup

cd tugas4-sample

echo "=== MEMBUAT FILE CONTOH DENGAN NAMA BERBEDA ==="

touch "laporan keuangan april.csv"
touch "backup server 2026.tar"
touch "config[production].ini"
touch "data[2026-04-10].json"
touch "log_backup_(server).txt"

touch access-log-01.txt
touch access-log-02.txt
touch access-log-03.txt
touch error-log-01.txt
touch error-log-02.txt
touch error-log-03.txt

touch "this_is_a_very_long_filename_for_testing_wildcard_pattern_matching.log"

touch user_data_2024.csv
touch user_data_2025.csv
touch user_data_2026.csv

echo ""
echo "✅ File contoh telah dibuat"
echo ""

echo "=== DAFTAR FILE DI tugas4-sample ==="
ls -la
=== MEMBUAT FILE CONTOH DENGAN NAMA BERBEDA ===

✅ File contoh telah dibuat

=== DAFTAR FILE DI tugas4-sample ===
total 8
drwxrwxr-x 2 Rappizr7 Rappizr7 4096 Apr 13 05:46  .
drwxrwxr-x 9 Rappizr7 Rappizr7 4096 Apr 13 05:46  ..
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:46  access-log-01.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:46  access-log-02.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:46  access-log-03.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:46 'backup server 2026.tar'
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:46 'config[production].ini'
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:46 'data[2026-04-10].json'
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:46  error-log-01.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:46  error-log-02.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:46  error-log-03.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:46 'laporan keuangan april.csv'
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:46 'log_backup_(server).txt'
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:46  this_is_a_very_long_filename_for_testing_wildcard_pattern_matching.log
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:46  user_data_2024.csv
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:46  user_data_2025.csv
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:46  user_data_2026.csv

- langkah 2:
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash/tugas4-sample$ echo ""
echo "=========================================="
echo "DEMONSTRASI PERBEDAAN QUOTING"
echo "=========================================="
echo ""

FILE_SPASI="laporan keuangan april.csv"

echo "1️. TANPA QUOTING (SALAH)"
echo "   Perintah: ls -la $FILE_SPASI"
echo "   Hasil:"
ls -la $FILE_SPASI 2>&1
echo "Error: Bash memecah jadi 4 argumen terpisah!"
echo ""

echo "2️. DENGAN QUOTING GANDA (BENAR)"
echo "   Perintah: ls -la \"$FILE_SPASI\""
echo "   Hasil:"
ls -la "$FILE_SPASI"
echo "Berhasil: Seluruh nama file dianggap 1 argumen!"
echo ""

FILE_KURUNG="config[production].ini"

echo "3️. TANPA QUOTING untuk file dengan tanda kurung"
echo "   Perintah: ls $FILE_KURUNG"
ls $FILE_KURUNG 2>&1
echo "Error: Tanda [ ] diinterpretasikan sebagai wildcard!"
echo ""

echo "4️. DENGAN QUOTING atau ESCAPE"
echo "   Perintah: ls \"$FILE_KURUNG\""
ls "$FILE_KURUNG"
echo "Berhasil dengan double quote"
echo ""

echo "5️. MENGGUNAKAN ESCAPE (backslash)"
echo "   Perintah: ls config\[production\].ini"
ls config\[production\].ini
echo "Berhasil dengan escape character"
echo ""

==========================================
DEMONSTRASI PERBEDAAN QUOTING
==========================================

1️. TANPA QUOTING (SALAH)
   Perintah: ls -la laporan keuangan april.csv
   Hasil:
ls: cannot access 'laporan': No such file or directory
ls: cannot access 'keuangan': No such file or directory
ls: cannot access 'april.csv': No such file or directory
Error: Bash memecah jadi 4 argumen terpisah!

2️. DENGAN QUOTING GANDA (BENAR)
   Perintah: ls -la "laporan keuangan april.csv"
   Hasil:
-rw-rw-r-- 1 Rappizr7 Rappizr7 0 Apr 13 05:46 'laporan keuangan april.csv'
Berhasil: Seluruh nama file dianggap 1 argumen!

3️. TANPA QUOTING untuk file dengan tanda kurung
   Perintah: ls config[production].ini
'config[production].ini'
Error: Tanda [ ] diinterpretasikan sebagai wildcard!

4️. DENGAN QUOTING atau ESCAPE
   Perintah: ls "config[production].ini"
'config[production].ini'
Berhasil dengan double quote

5️. MENGGUNAKAN ESCAPE (backslash)
   Perintah: ls config\[production\].ini
'config[production].ini'
Berhasil dengan escape character

- langkah 3:
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash/tugas4-sample$ echo ""
echo "=========================================="
echo "PREVIEW WILDCARD DENGAN echo (PRAKTIK AMAN)"
echo "=========================================="
echo ""

echo "Pola 1: *.txt"
echo "   Preview:"
echo *.txt
echo "   Jumlah file: $(echo *.txt | wc -w) file"
echo ""

echo "Pola 2: access-log-*.txt"
echo "   Preview:"
echo access-log-*.txt
echo "   Jumlah file: $(echo access-log-*.txt | wc -w) file"
echo ""

echo "Pola 3: *-log-*.txt"
echo "   Preview:"
echo *-log-*.txt
echo "   Jumlah file: $(echo *-log-*.txt | wc -w) file"
echo ""

echo "Pola 4: user_data_202?.csv"
echo "   Preview:"
echo user_data_202?.csv
echo "   Jumlah file: $(echo user_data_202?.csv | wc -w) file"
echo ""

echo "Pola 5: *[2026]*"
echo "   Preview:"
echo *[2026]* 2>&1
echo "Perhatikan: wildcard dengan [ ] bisa error jika tidak ada yang cocok!"
echo ""

==========================================
PREVIEW WILDCARD DENGAN echo (PRAKTIK AMAN)
==========================================

Pola 1: *.txt
   Preview:
access-log-01.txt access-log-02.txt access-log-03.txt error-log-01.txt error-log-02.txt error-log-03.txt log_backup_(server).txt
   Jumlah file: 7 file

Pola 2: access-log-*.txt
   Preview:
access-log-01.txt access-log-02.txt access-log-03.txt
   Jumlah file: 3 file

Pola 3: *-log-*.txt
   Preview:
access-log-01.txt access-log-02.txt access-log-03.txt error-log-01.txt error-log-02.txt error-log-03.txt
   Jumlah file: 6 file

Pola 4: user_data_202?.csv
   Preview:
user_data_2024.csv user_data_2025.csv user_data_2026.csv
   Jumlah file: 3 file

Pola 5: *[2026]*
   Preview:
access-log-01.txt access-log-02.txt access-log-03.txt backup server 2026.tar data[2026-04-10].json error-log-01.txt error-log-02.txt error-log-03.txt user_data_2024.csv user_data_2025.csv user_data_2026.csv
Perhatikan: wildcard dengan [ ] bisa error jika tidak ada yang cocok!

- langkah 4:
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash/tugas4-sample$ echo ""
echo "=========================================="
echo "SALIN FILE KE DIREKTORI BACKUP"
echo "=========================================="
echo ""

BACKUP_DIR="$HOME/praktikum-os/week07-bash/tugas4-backup"
SOURCE_DIR="$HOME/praktikum-os/week07-bash/tugas4-sample"

TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_SUBDIR="$BACKUP_DIR/backup_$TIMESTAMP"
mkdir -p "$BACKUP_SUBDIR"

echo "Backup akan disimpan di: $BACKUP_SUBDIR"
echo ""

echo "1️. Menyalin file dengan spasi (menggunakan quote):"
cp -v "laporan keuangan april.csv" "$BACKUP_SUBDIR/"
echo ""

echo "2️. Menyalin file dengan tanda kurung (menggunakan escape):"
cp -v config\[production\].ini "$BACKUP_SUBDIR/"
echo ""

echo "3️. Menyalin file dengan wildcard (sudah dipreview):"
cp -v access-log-*.txt "$BACKUP_SUBDIR/"
echo ""

echo "4️. Menyalin file dengan nama panjang:"
cp -v "this_is_a_very_long_filename_for_testing_wildcard_pattern_matching.log" "$BACKUP_SUBDIR/"
echo ""

echo "5️. Menyalin semua file .csv:"
cp -v *.csv "$BACKUP_SUBDIR/"
echo ""

echo "6️. Menyalin file dengan nama berkarakter khusus menggunakan variabel:"
FILE_SPECIAL="log_backup_(server).txt"
cp -v "$FILE_SPECIAL" "$BACKUP_SUBDIR/"
echo ""

echo "Semua file berhasil disalin!"
echo ""

echo "=== ISI DIREKTORI BACKUP ==="
ls -la "$BACKUP_SUBDIR/"

==========================================
SALIN FILE KE DIREKTORI BACKUP
==========================================

Backup akan disimpan di: /home/Rappizr7/praktikum-os/week07-bash/tugas4-backup/backup_20260413_054827

1️. Menyalin file dengan spasi (menggunakan quote):
'laporan keuangan april.csv' -> '/home/Rappizr7/praktikum-os/week07-bash/tugas4-backup/backup_20260413_054827/laporan keuangan april.csv'

2️. Menyalin file dengan tanda kurung (menggunakan escape):
'config[production].ini' -> '/home/Rappizr7/praktikum-os/week07-bash/tugas4-backup/backup_20260413_054827/config[production].ini'

3️. Menyalin file dengan wildcard (sudah dipreview):
'access-log-01.txt' -> '/home/Rappizr7/praktikum-os/week07-bash/tugas4-backup/backup_20260413_054827/access-log-01.txt'
'access-log-02.txt' -> '/home/Rappizr7/praktikum-os/week07-bash/tugas4-backup/backup_20260413_054827/access-log-02.txt'
'access-log-03.txt' -> '/home/Rappizr7/praktikum-os/week07-bash/tugas4-backup/backup_20260413_054827/access-log-03.txt'

4️. Menyalin file dengan nama panjang:
'this_is_a_very_long_filename_for_testing_wildcard_pattern_matching.log' -> '/home/Rappizr7/praktikum-os/week07-bash/tugas4-backup/backup_20260413_054827/this_is_a_very_long_filename_for_testing_wildcard_pattern_matching.log'

5️. Menyalin semua file .csv:
'laporan keuangan april.csv' -> '/home/Rappizr7/praktikum-os/week07-bash/tugas4-backup/backup_20260413_054827/laporan keuangan april.csv'
'user_data_2024.csv' -> '/home/Rappizr7/praktikum-os/week07-bash/tugas4-backup/backup_20260413_054827/user_data_2024.csv'
'user_data_2025.csv' -> '/home/Rappizr7/praktikum-os/week07-bash/tugas4-backup/backup_20260413_054827/user_data_2025.csv'
'user_data_2026.csv' -> '/home/Rappizr7/praktikum-os/week07-bash/tugas4-backup/backup_20260413_054827/user_data_2026.csv'

6️. Menyalin file dengan nama berkarakter khusus menggunakan variabel:
'log_backup_(server).txt' -> '/home/Rappizr7/praktikum-os/week07-bash/tugas4-backup/backup_20260413_054827/log_backup_(server).txt'

Semua file berhasil disalin!

=== ISI DIREKTORI BACKUP ===
total 8
drwxrwxr-x 2 Rappizr7 Rappizr7 4096 Apr 13 05:48  .
drwxrwxr-x 3 Rappizr7 Rappizr7 4096 Apr 13 05:48  ..
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:48  access-log-01.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:48  access-log-02.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:48  access-log-03.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:48 'config[production].ini'
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:48 'laporan keuangan april.csv'
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:48 'log_backup_(server).txt'
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:48  this_is_a_very_long_filename_for_testing_wildcard_pattern_matching.log
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:48  user_data_2024.csv
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:48  user_data_2025.csv
-rw-rw-r-- 1 Rappizr7 Rappizr7    0 Apr 13 05:48  user_data_2026.csv

- langkah 5:
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash/tugas4-sample$ echo ""
echo "=========================================="
echo "MEMBUAT ARSIP TAR.GZ"
echo "=========================================="
echo ""

cd "$BACKUP_DIR"

ARCHIVE_NAME="backup_archive_$TIMESTAMP.tar.gz"
ARCHIVE_PATH="$BACKUP_DIR/$ARCHIVE_NAME"

echo "Membuat arsip: $ARCHIVE_NAME"
tar -czf "$ARCHIVE_PATH" "backup_$TIMESTAMP/"

if [ $? -eq 0 ]; then
    echo "Arsip berhasil dibuat!"
    echo ""
    echo "=== INFORMASI ARSIP ==="
    ls -lh "$ARCHIVE_PATH"
    echo ""
    echo "=== ISI ARSIP (daftar file) ==="
    tar -tzf "$ARCHIVE_PATH" | head -15
else
    echo "Gagal membuat arsip"
fi

cd ~/praktikum-os/week07-bash

==========================================
MEMBUAT ARSIP TAR.GZ
==========================================

Membuat arsip: backup_archive_20260413_054827.tar.gz
Arsip berhasil dibuat!

=== INFORMASI ARSIP ===
-rw-rw-r-- 1 Rappizr7 Rappizr7 387 Apr 13 05:49 /home/Rappizr7/praktikum-os/week07-bash/tugas4-backup/backup_archive_20260413_054827.tar.gz

=== ISI ARSIP (daftar file) ===
backup_20260413_054827/
backup_20260413_054827/log_backup_(server).txt
backup_20260413_054827/access-log-03.txt
backup_20260413_054827/user_data_2024.csv
backup_20260413_054827/user_data_2026.csv
backup_20260413_054827/config[production].ini
backup_20260413_054827/access-log-01.txt
backup_20260413_054827/this_is_a_very_long_filename_for_testing_wildcard_pattern_matching.log
backup_20260413_054827/laporan keuangan april.csv
backup_20260413_054827/access-log-02.txt
backup_20260413_054827/user_data_2025.csv

- langkah 6:
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$ echo ""
echo "=========================================="
echo "MENYIMPAN RIWAYAT PERINTAH"
echo "=========================================="
echo ""

cat <<EOF > riwayat-arsip.txt
==========================================
RIWAYAT PERINTAH TUGAS PRAKTIKUM 4
==========================================
Tanggal: $(date '+%Y-%m-%d %H:%M:%S')
User: $(whoami)
Hostname: $(hostname)
Workspace: $(pwd)

------------------------------------------
PERINTAH-PERINTAH YANG DIGUNAKAN
------------------------------------------

1. MEMBUAT FILE CONTOH:
   touch "laporan keuangan april.csv"
   touch "backup server 2026.tar"
   touch "config[production].ini"
   touch "data[2026-04-10].json"

2. DEMONSTRASI QUOTING:
   # Tanpa quote (SALAH)
   ls -la laporan keuangan april.csv

   # Dengan quote (BENAR)
   ls -la "laporan keuangan april.csv"

   # Dengan escape
   ls config\[production\].ini

3. PREVIEW WILDCARD (PRAKTIK AMAN):
   echo *.txt
   echo access-log-*.txt
   echo user_data_202?.csv

4. COPY FILE DENGAN QUOTING:
   cp -v "laporan keuangan april.csv" "\$BACKUP_DIR/"
   cp -v config\[production\].ini "\$BACKUP_DIR/"

5. COPY DENGAN WILDCARD:
   cp -v access-log-*.txt "\$BACKUP_DIR/"

6. MEMBUAT ARSIP TAR.GZ:
   tar -czf "backup_archive_\$TIMESTAMP.tar.gz" "backup_\$TIMESTAMP/"

7. MEMERIKSA ISI ARSIP:
   tar -tzf backup_archive_*.tar.gz

------------------------------------------
PENTING: SELALU GUNAKAN QUOTING UNTUK VARIABEL
YANG BERISI PATH ATAU NAMA FILE!
------------------------------------------
Contoh yang benar: cp "\$file_asli" "\$file_tujuan"
Contoh yang salah: cp \$file_asli \$file_tujuan

==========================================
EOF

cat riwayat-arsip.txt

==========================================
MENYIMPAN RIWAYAT PERINTAH
==========================================

==========================================
RIWAYAT PERINTAH TUGAS PRAKTIKUM 4
==========================================
Tanggal: 2026-04-13 05:49:30
User: Rappizr7
Hostname: ubuntu-server
Workspace: /home/Rappizr7/praktikum-os/week07-bash

------------------------------------------
PERINTAH-PERINTAH YANG DIGUNAKAN
------------------------------------------

1. MEMBUAT FILE CONTOH:
   touch "laporan keuangan april.csv"
   touch "backup server 2026.tar"
   touch "config[production].ini"
   touch "data[2026-04-10].json"

2. DEMONSTRASI QUOTING:
   # Tanpa quote (SALAH)
   ls -la laporan keuangan april.csv

   # Dengan quote (BENAR)
   ls -la "laporan keuangan april.csv"

   # Dengan escape
   ls config\[production\].ini

3. PREVIEW WILDCARD (PRAKTIK AMAN):
   echo *.txt
   echo access-log-*.txt
   echo user_data_202?.csv

4. COPY FILE DENGAN QUOTING:
   cp -v "laporan keuangan april.csv" "$BACKUP_DIR/"
   cp -v config\[production\].ini "$BACKUP_DIR/"

5. COPY DENGAN WILDCARD:
   cp -v access-log-*.txt "$BACKUP_DIR/"

6. MEMBUAT ARSIP TAR.GZ:
   tar -czf "backup_archive_$TIMESTAMP.tar.gz" "backup_$TIMESTAMP/"

7. MEMERIKSA ISI ARSIP:
   tar -tzf backup_archive_*.tar.gz

------------------------------------------
PENTING: SELALU GUNAKAN QUOTING UNTUK VARIABEL
YANG BERISI PATH ATAU NAMA FILE!
------------------------------------------
Contoh yang benar: cp "$file_asli" "$file_tujuan"
Contoh yang salah: cp $file_asli $file_tujuan

==========================================

- langkah 7:
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$ echo ""
echo "=========================================="
echo "REFLEKSI PENTINGNYA QUOTING DI BASH"
echo "=========================================="
echo ""

cat <<'EOF' > refleksi-quoting.txt
==========================================
REFLEKSI: MENGAPA QUOTING SANGAT PENTING?
==========================================

1. SPASI DALAM NAMA FILE
   Tanpa quote: Bash memecah nama file menjadi beberapa argumen
   Contoh: "laporan keuangan.txt" → laporan, keuangan.txt (2 argumen!)
   Dengan quote: Seluruh nama dianggap 1 argumen

2. KARAKTER SPECIAL ( *, ?, [, ], (, ), &, ;, $, !, `, | )
   Tanpa quote: Karakter ini diinterpretasikan oleh shell
   Contoh: config[prod].ini → [ ] dianggap wildcard pattern
   Dengan quote: Karakter dianggap literal (teks biasa)

3. VARIABEL YANG MENGANDUNG PATH
   Tanpa quote: Jika path mengandung spasi, akan error
   Contoh: $FILE="My Documents/file.txt"
   cp $FILE backup/ → error karena "My" dan "Documents/" terpisah
   Dengan quote: cp "$FILE" backup/ → bekerja

4. KEAMANAN (SECURITY)
   Tanpa quote: Rentan terhadap command injection
   Contoh: nama_file="file.txt; rm -rf /"
   cat $nama_file → bisa menjalankan perintah berbahaya
   Dengan quote: cat "$nama_file" → aman

5. COMMAND SUBSTITUTION
   Tanpa quote: Hasil substitution bisa pecah karena spasi
   Dengan quote: $(command) tetap utuh sebagai 1 argumen

==========================================
PRAKTIK TERBAIK (BEST PRACTICE)
==========================================

SELALU gunakan double quote untuk variabel:
   cp "$source" "$destination"
   echo "User: $USER"

Gunakan single quote untuk teks literal:
   echo 'Karakter $special @tidak &diekspansi'

Gunakan backslash untuk escape karakter tunggal:
   cp file\ dengan\ spasi.txt backup/

PREVIEW wildcard dengan echo sebelum perintah berbahaya:
   echo rm *.log  (lihat dulu filenya)
   rm *.log      (langsung hapus tanpa lihat)

==========================================
EOF

cat refleksi-quoting.txt

==========================================
REFLEKSI PENTINGNYA QUOTING DI BASH
==========================================

==========================================
REFLEKSI: MENGAPA QUOTING SANGAT PENTING?
==========================================

1. SPASI DALAM NAMA FILE
   Tanpa quote: Bash memecah nama file menjadi beberapa argumen
   Contoh: "laporan keuangan.txt" → laporan, keuangan.txt (2 argumen!)
   Dengan quote: Seluruh nama dianggap 1 argumen

2. KARAKTER SPECIAL ( *, ?, [, ], (, ), &, ;, $, !, `, | )
   Tanpa quote: Karakter ini diinterpretasikan oleh shell
   Contoh: config[prod].ini → [ ] dianggap wildcard pattern
   Dengan quote: Karakter dianggap literal (teks biasa)

3. VARIABEL YANG MENGANDUNG PATH
   Tanpa quote: Jika path mengandung spasi, akan error
   Contoh: $FILE="My Documents/file.txt"
   cp $FILE backup/ → error karena "My" dan "Documents/" terpisah
   Dengan quote: cp "$FILE" backup/ → bekerja

4. KEAMANAN (SECURITY)
   Tanpa quote: Rentan terhadap command injection
   Contoh: nama_file="file.txt; rm -rf /"
   cat $nama_file → bisa menjalankan perintah berbahaya
   Dengan quote: cat "$nama_file" → aman

5. COMMAND SUBSTITUTION
   Tanpa quote: Hasil substitution bisa pecah karena spasi
   Dengan quote: $(command) tetap utuh sebagai 1 argumen

==========================================
PRAKTIK TERBAIK (BEST PRACTICE)
==========================================

SELALU gunakan double quote untuk variabel:
   cp "$source" "$destination"
   echo "User: $USER"

Gunakan single quote untuk teks literal:
   echo 'Karakter $special @tidak &diekspansi'

Gunakan backslash untuk escape karakter tunggal:
   cp file\ dengan\ spasi.txt backup/

PREVIEW wildcard dengan echo sebelum perintah berbahaya:
   echo rm *.log  (lihat dulu filenya)
   rm *.log      (langsung hapus tanpa lihat)

==========================================

- langkah 8:
Rappizr7@ubuntu-server:~/praktikum-os/week07-bash$ echo ""
echo "=========================================="
echo "RINGKASAN TUGAS PRAKTIKUM 4"
echo "=========================================="
echo ""

echo "DAFTAR FILE AWAL (di tugas4-sample):"
ls -1 tugas4-sample/ | head -10
echo "   ... dan seterusnya"
echo ""

echo "DAFTAR FILE HASIL BACKUP:"
ls -1 "$BACKUP_SUBDIR/"
echo ""

echo "FILE ARSIP TAR.GZ:"
ls -lh "$BACKUP_DIR"/*.tar.gz
echo ""

echo "FILE RIWAYAT PERINTAH:"
ls -lh riwayat-arsip.txt
echo ""

echo "REFLEKSI QUOTING:"
ls -lh refleksi-quoting.txt
echo ""

echo "=========================================="
echo "TUGAS 4 SELESAI!"
echo "=========================================="

==========================================
RINGKASAN TUGAS PRAKTIKUM 4
==========================================

DAFTAR FILE AWAL (di tugas4-sample):
access-log-01.txt
access-log-02.txt
access-log-03.txt
backup server 2026.tar
config[production].ini
data[2026-04-10].json
error-log-01.txt
error-log-02.txt
error-log-03.txt
laporan keuangan april.csv
   ... dan seterusnya

DAFTAR FILE HASIL BACKUP:
access-log-01.txt
access-log-02.txt
access-log-03.txt
'config[production].ini'
'laporan keuangan april.csv'
'log_backup_(server).txt'
this_is_a_very_long_filename_for_testing_wildcard_pattern_matching.log
user_data_2024.csv
user_data_2025.csv
user_data_2026.csv

FILE ARSIP TAR.GZ:
-rw-rw-r-- 1 Rappizr7 Rappizr7 387 Apr 13 05:49 /home/Rappizr7/praktikum-os/week07-bash/tugas4-backup/backup_archive_20260413_054827.tar.gz

FILE RIWAYAT PERINTAH:
-rw-rw-r-- 1 Rappizr7 Rappizr7 1.5K Apr 13 05:49 riwayat-arsip.txt

REFLEKSI QUOTING:
-rw-rw-r-- 1 Rappizr7 Rappizr7 1.8K Apr 13 05:50 refleksi-quoting.txt

==========================================
TUGAS 4 SELESAI!
==========================================