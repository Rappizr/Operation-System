# Laporan Pertemuan ke-9 Sistem Operasi

**Tanggal:** 27 April 2026  
**Disusun Oleh:** Mukhammad Raffi Zabra  
**NIM:** 254107020059  
**Kelas/No:** TI-1G/24  

---

# Praktikum 7.1 Script Pertama: Laporan Sistem

**1. Langkah 1: Buat workspace praktikum:**
- Rappizr7@ubuntu-server:~$ mkdir -p ~/praktikum-os/week09/{scripts,logs,data}
- Rappizr7@ubuntu-server:~$ cd ~/praktikum-os/week09/scripts

**2. Langkah : Buat script dengan nano:**
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ nano laporan-sistem.sh

**3. Langkah : Masukkan isi dibawah ini:**
```bash
# !/ bin / bash
# Script : laporan - sistem . sh
echo " ================================ "
echo " LAPORAN SISTEM "
echo " ================================ "
echo " Tanggal : $( date '+%A , %d %B %Y ')"
echo " Jam : $( date '+% H :% M :% S ')"
echo " Hostname : $( hostname )"
echo " User : $( whoami )"
echo " CPU core : $( nproc )"
echo " RAM bebas : $( free -h | awk '/^ Mem / { print $4 } ') "
echo " Disk / : $( df -h / | awk 'NR ==2 { print $5 } ')
terpakai "
echo " ================================ "
```

**4. Langkah: Beri izin dan jalankan:**
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ Rappizr7@ubuntu-server:/praktikum-os/week09 scripts$ chmod +x lapchmod +x laporan-sistem.sh
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./laporan-sistem.sh

- hasil:
```bash
 ================================
 LAPORAN SISTEM
 ================================
 Tanggal : Sunday , 26 April 2026
 Jam : % H :% M :% S
 Hostname : ubuntu-server
 User : Rappizr7
 CPU core : 4
 RAM bebas :
 Disk / : 16%
terpakai
 ================================
 ```

 **- Latihan Latihan 9.1**
 - Modifikasi laporan-sistem.sh agar menyimpan output ke file
laporan-YYYY-MM-DD.txt sekaligus menampilkannya di terminal. Petunjuk:
gunakan tee yang sudah dipelajari di bab sebelumnya.

**1. Langkah:**
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ nano laporan-sistem.sh

**2. Langkah: isi dengan dibawah ini**
```bash
#!/bin/bash
# Script: laporan-sistem.sh

# Menentukan nama file output
NAMA_FILE="laporan-$(date '+%Y-%m-%d').txt"

{
  echo "================================"
  echo "        LAPORAN SISTEM          "
  echo "================================"
  echo "Tanggal    : $(date '+%A, %d %B %Y')"
  echo "Jam        : $(date '+%H:%M:%S')"
  echo "Hostname   : $(hostname)"
  echo "User       : $(whoami)"
  echo "CPU core   : $(nproc)"
  echo "RAM bebas  : $(free -h | grep Mem | awk '{print $4}')"
  echo "Disk /     : $(df -h / | awk 'NR==2 {print $5}') terpakai"
  echo "================================"
} | tee "$NAMA_FILE"
```

**3. Langkah:**
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ chmod +x laporan-sistem.sh
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./laporan-sistem.sh
- hasil:
```bash
================================
        LAPORAN SISTEM
================================
Tanggal    : Sunday, 26 April 2026
Jam        : 13:19:00
Hostname   : ubuntu-server
User       : Rappizr7
CPU core   : 4
RAM bebas  : 3.3Gi
Disk /     : 16% terpakai
================================
```

# Praktikum 7.2 Script Info Sistem dengan Argumen

**1. Langkah: Buat script:**
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ nano ~/praktikum-os/week09/scripts/info-sistem.sh
- isi
```bash
#!/bin/bash
# Penggunaan: ./info-sistem.sh [nama-admin] [batas-disk-persen]

# Menentukan nilai default jika argumen tidak diisi
ADMIN=${1:-"Tidak dikenal"}
BATAS=${2:-80}

TANGGAL=$(date '+%F %T')
# Mengambil angka penggunaan disk (menghapus tanda %)
DISK_PERSEN=$(df / | awk 'NR==2 { print $5 }' | tr -d '%')

echo "================================"
echo "Admin      : $ADMIN"
echo "Tanggal    : $TANGGAL"
echo "Disk /     : ${DISK_PERSEN}% terpakai"
echo "Batas      : ${BATAS}%"
echo "--------------------------------"

# Logika pengecekan ambang batas disk
if [ "$DISK_PERSEN" -gt "$BATAS" ]; then
    echo "STATUS     : PERINGATAN - disk melebihi batas!"
else
    SISA=$(( BATAS - DISK_PERSEN ))
    echo "STATUS     : Normal (sisa toleransi ${SISA}%)"
fi
echo "================================"
```

**2. Langkah: Memberi Izin dan Menguji Script:**
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ chmod +x ~/praktikum-os/week09/scripts/info-sistem.sh
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./info-sistem.sh

- hasil :
```bash
================================
Admin      : Tidak dikenal
Tanggal    : 2026-04-26 14:00:42
Disk /     : 16% terpakai
Batas      : 80%
--------------------------------
STATUS     : Normal (sisa toleransi 64%)
================================
```

- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./info-sistem.sh "Dian" 50
```bash
================================
Admin      : Dian
Tanggal    : 2026-04-26 14:01:06
Disk /     : 16% terpakai
Batas      : 50%
--------------------------------
STATUS     : Normal (sisa toleransi 34%)
================================
```

- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./info-sistem.sh "Dian" 10
```bash
================================
Admin      : Dian
Tanggal    : 2026-04-26 14:01:13
Disk /     : 16% terpakai
Batas      : 10%
--------------------------------
STATUS     : PERINGATAN - disk melebihi batas!
================================
```

**- Latihan Latihan 9.2**
- Buat script kalkulator.sh yang menerima tiga argumen: angka1,
operator, angka2 dengan operator +, -, *, atau /. Contoh:
./kalkulator.sh 20 + 5 menghasilkan 25. Gunakan case untuk memilih
operasi, dan validasi jika argumen tidak lengkap.

**1. Langkah: Buat script:**
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ nano kalkulator.sh
- isi:
```bash
#!/bin/bash
# Script: kalkulator.sh
# Penggunaan: ./kalkulator.sh <angka1> <operator> <angka2>

# 1. Validasi: Pastikan ada tepat 3 argumen
if [ $# -ne 3 ]; then
    echo "Error: Argumen tidak lengkap!"
    echo "Penggunaan: ./kalkulator.sh <angka1> <operator> <angka2>"
    echo "Contoh: ./kalkulator.sh 20 + 5"
    exit 1
fi

ANGKA1=$1
OPERATOR=$2
ANGKA2=$3

# 2. Menggunakan case untuk memilih operasi
case $OPERATOR in
    +)
        HASIL=$((ANGKA1 + ANGKA2))
        ;;
    -)
        HASIL=$((ANGKA1 - ANGKA2))
        ;;
    \*)
        # Gunakan backslash atau kutip untuk operator perkalian
        HASIL=$((ANGKA1 * ANGKA2))
        ;;
    /)
        # Validasi pembagian dengan nol
        if [ $ANGKA2 -eq 0 ]; then
            echo "Error: Tidak bisa membagi dengan nol!"
            exit 1
        fi
        HASIL=$((ANGKA1 / ANGKA2))
        ;;
    *)
        echo "Error: Operator tidak dikenal! Gunakan (+, -, *, /)"
        exit 1
        ;;
esac

echo "Hasil: $ANGKA1 $OPERATOR $ANGKA2 = $HASIL"
```

**2. Langkah: Memberi Izin dan Menguji Script:**
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ chmod +x kalkulator.sh
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./kalkulator.sh 20 + 5
- hasil:
```bash
Hasil: 20 + 5 = 25
```
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./kalkulator.sh 10 - 3
- hasil:
```bash
Hasil: 10 - 3 = 7
```
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./kalkulator.sh 5 \* 4
- hasil:
```bash
Hasil: 5 * 4 = 20
```
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./kalkulator.sh 20 / 4
- hasil:
```bash
Hasil: 20 / 4 = 5
```
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./kalkulator.sh 10
- hasil:
```bash
Error: Argumen tidak lengkap!
Penggunaan: ./kalkulator.sh <angka1> <operator> <angka2>
Contoh: ./kalkulator.sh 20 + 5
```

# Praktikum 7.3 Script Grading dan Menu Interaktif
**1. Langkah: Buat script:**
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ nano ~/praktikum-os/week09/scripts/grading-batch.sh
- isi:
```bash
#!/bin/bash
# Script: grading-batch.sh

# Daftar nilai mahasiswa dalam array
MAHASISWA=("Andi:92" "Budi:73" "Citra:55" "Deni:80" "Eka:45")

echo " === HASIL GRADING === "
for ENTRI in "${MAHASISWA[@]}"; do
    NAMA=$(echo "$ENTRI" | cut -d: -f1)
    NILAI=$(echo "$ENTRI" | cut -d: -f2)

    if [ "$NILAI" -ge 85 ]; then
        GRADE="A"
    elif [ "$NILAI" -ge 75 ]; then
        GRADE="B"
    elif [ "$NILAI" -ge 65 ]; then
        GRADE="C"
    elif [ "$NILAI" -ge 55 ]; then
        GRADE="D"
    else
        GRADE="E"
    fi
    # printf digunakan agar tampilan kolom lurus/rapi
    printf "%-10s %3d %s\n" "$NAMA" "$NILAI" "$GRADE"
done
echo " ===================== "
```

**2. Langkah: Memberi Izin dan Menguji Script:**
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ chmod +x ~/praktikum-os/week09/scripts/grading-batch.sh
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ~/praktikum-os/week09/scripts/grading-batch.sh
- hasil:
```bash
 === HASIL GRADING ===
Andi        92 A
Budi        73 C
Citra       55 D
Deni        80 B
Eka         45 E
 =====================
```

**3. Langkah: Buat script:**
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ nano ~/praktikum-os/week09/scripts/menu-sistem.sh
- isi:
```bash
#!/bin/bash
# Menu interaktif pemantauan sistem

while true; do
    echo ""
    echo " ===== MENU MONITOR ===== "
    echo " 1) Info disk "
    echo " 2) Info memori "
    echo " 3) Proses teratas "
    echo " 4) Keluar "
    echo -n " Pilih [1-4]: "
    read PILIHAN

    case $PILIHAN in
        1) df -h ;;
        2) free -h ;;
        3) ps aux --sort=-%cpu | head -6 ;;
        4) echo "Sampai jumpa!"; exit 0 ;;
        *) echo "Pilihan tidak valid." ;;
    esac
done
```

**4. Langkah: Memberi Izin dan Menguji Script:**
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ chmod +x ~/praktikum-os/week09/scripts/menu-sistem.sh
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ~/praktikum-os/week09/scripts/menu-sistem.sh
- hasil:
```bash
 ===== MENU MONITOR =====
 1) Info disk
 2) Info memori
 3) Proses teratas
 4) Keluar
 Pilih [1-4]: 1
Filesystem      Size  Used Avail Use% Mounted on
tmpfs           400M  1.1M  399M   1% /run
/dev/sda2        52G  7.6G   42G  16% /
tmpfs           2.0G     0  2.0G   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           400M   12K  400M   1% /run/user/1000

 ===== MENU MONITOR =====
 1) Info disk
 2) Info memori
 3) Proses teratas
 4) Keluar
 Pilih [1-4]: 2
               total        used        free      shared  buff/cache   available
Mem:           3.9Gi       459Mi       3.3Gi       1.1Mi       345Mi       3.5Gi
Swap:          4.0Gi          0B       4.0Gi

 ===== MENU MONITOR =====
 1) Info disk
 2) Info memori
 3) Proses teratas
 4) Keluar
 Pilih [1-4]: 3
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
Rappizr7    1774  200  0.1  10884  4604 pts/1    R+   16:10   0:00 ps aux --sort=-%cpu
Rappizr7    1775 20.0  0.0   5696  2056 pts/1    S+   16:10   0:00 head -6
root        1765  0.8  0.0      0     0 ?        I    16:09   0:00 [kworker/1:0-events]
root        1695  0.7  0.0      0     0 ?        I    15:45   0:11 [kworker/1:2-events]
Rappizr7    1771  0.6  0.0   7340  3732 pts/1    S+   16:10   0:00 /bin/bash /home/Rappizr7/praktikum-os/week09/scripts/menu-sistem.sh

 ===== MENU MONITOR =====
 1) Info disk
 2) Info memori
 3) Proses teratas
 4) Keluar
 Pilih [1-4]: 4
Sampai jumpa!
```

**Latihan Latihan 9.3**
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ nano ~/praktikum-os/week09/scripts/grading-batch.sh
- isi:
```bash
#!/bin/bash
# Script: grading-batch.sh

# Daftar nilai mahasiswa
MAHASISWA=("Andi:92" "Budi:73" "Citra:55" "Deni:80" "Eka:45")

# Inisialisasi variabel penghitung grade
JML_A=0; JML_B=0; JML_C=0; JML_D=0; JML_E=0

echo " === HASIL GRADING === "
for ENTRI in "${MAHASISWA[@]}"; do
    NAMA=$(echo "$ENTRI" | cut -d: -f1)
    NILAI=$(echo "$ENTRI" | cut -d: -f2)

    if [ "$NILAI" -ge 85 ]; then
        GRADE="A"; JML_A=$((JML_A + 1))
    elif [ "$NILAI" -ge 75 ]; then
        GRADE="B"; JML_B=$((JML_B + 1))
    elif [ "$NILAI" -ge 65 ]; then
        GRADE="C"; JML_C=$((JML_C + 1))
    elif [ "$NILAI" -ge 55 ]; then
        GRADE="D"; JML_D=$((JML_D + 1))
    else
        GRADE="E"; JML_E=$((JML_E + 1))
    fi
    printf "%-10s %3d %s\n" "$NAMA" "$NILAI" "$GRADE"
done

echo " ===================== "
echo " === RINGKASAN === "

# Perulangan kedua untuk menampilkan statistik (sesuai instruksi latihan)
GRADES=("A" "B" "C" "D" "E")
for G in "${GRADES[@]}"; do
    case $G in
        "A") TOTAL=$JML_A ;;
        "B") TOTAL=$JML_B ;;
        "C") TOTAL=$JML_C ;;
        "D") TOTAL=$JML_D ;;
        "E") TOTAL=$JML_E ;;
    esac
    echo "Grade $G : $TOTAL mahasiswa"
done
echo " ===================== "
```

- hasil:
```bash
 === HASIL GRADING ===
Andi        92 A
Budi        73 C
Citra       55 D
Deni        80 B
Eka         45 E
 =====================
 === RINGKASAN ===
Grade A : 1 mahasiswa
Grade B : 1 mahasiswa
Grade C : 1 mahasiswa
Grade D : 1 mahasiswa
Grade E : 1 mahasiswa
 =====================
 ```

 # Praktikum 7.4 Library Fungsi Validasi
 **1. Langkah: Buat script:**
 - Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$  nano ~/praktikum-os/week09/scripts/lib-validasi.sh
 - isi:
 ```bash
 #!/bin/bash
# lib-validasi.sh - Library fungsi validasi

# Mengecek apakah input adalah angka bulat positif
adalah_angka () {
  [[ "$1" =~ ^[0-9]+$ ]]
}

# Mengecek apakah file ada (-f) dan bisa dibaca (-r)
file_bisa_dibaca () {
  [ -f "$1" ] && [ -r "$1" ]
}

# Menampilkan pesan error ke stderr dan menghentikan script
error_exit () {
  echo " ERROR : $1 " >&2
  exit 1
}

# Fungsi pembantu untuk informasi dan sukses
info () { echo "[ INFO ] $1 "; }
sukses () { echo "[ OK ] $1 "; }
```

- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ nano ~/praktikum-os/week09/scripts/pakai-library.sh
- isi:
```bash
#!/bin/bash
# Muat library (seperti import di Java)
source ~/praktikum-os/week09/scripts/lib-validasi.sh

ANGKA=$1
FILE=$2

# Validasi awal: pastikan argumen tidak kosong
if [ -z "$ANGKA" ] || [ -z "$FILE" ]; then
  error_exit "Penggunaan: $0 <angka> <path-file>"
fi

# Menggunakan fungsi dari library
if adalah_angka "$ANGKA"; then
  sukses "Input '$ANGKA' adalah angka valid"
else
  error_exit "'$ANGKA' bukan angka"
fi

if file_bisa_dibaca "$FILE"; then
  sukses "File '$FILE' bisa dibaca"
  info "Jumlah baris: $(wc -l < "$FILE")"
else
  error_exit "File '$FILE' tidak ada atau tidak bisa dibaca"
fi
```

**2. Langkah: Memberi Izin dan Menguji Script:**
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ chmod +x ~/praktikum-os/week09/scripts/pakai-library.sh
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./pakai-library.sh 42 /etc/hostname
- hasil:
```bash
[ OK ] Input '42' adalah angka valid
[ OK ] File '/etc/hostname' bisa dibaca
[ INFO ] Jumlah baris: 1
```
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./pakai-library.sh abc /etc/hostname
- hasil:
```bash
ERROR : 'abc' bukan angka
```
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./pakai-library.sh 42 /file-palsu.txt
- hasil:
```bash
[ OK ] Input '42' adalah angka valid
ERROR : File '/file-palsu.txt' tidak ada atau tidak bisa dibaca
```
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./pakai-library.sh
- hasil:
```bash
ERROR : Penggunaan: ./pakai-library.sh <angka> <path-file>
```

**Latihan Latihan 9.4**
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ nano ~/praktikum-os/week09/scripts/lib-validasi.sh
- isi:
```bash
#!/bin/bash
# lib-validasi.sh - Library fungsi validasi

# Mengecek apakah input adalah angka bulat positif
adalah_angka () {
  [[ "$1" =~ ^[0-9]+$ ]]
}

# Mengecek apakah file ada (-f) dan bisa dibaca (-r)
file_bisa_dibaca () {
  [ -f "$1" ] && [ -r "$1" ]

}

# Menampilkan pesan error ke stderr dan menghentikan script
error_exit () {
  echo " ERROR : $1 " >&2
  exit 1
}

# Fungsi pembantu untuk informasi dan sukses
info () { echo "[ INFO ] $1 "; }
sukses () { echo "[ OK ] $1 "; }
# Fungsi konfirmasi (Y/N)
konfirmasi () {
    echo -n "$1 (y/n): "
    read JAWABAN
    case "$JAWABAN" in
        [yY]|[yY][eE][sS])
            return 0
            ;;
        *)
            return 1
            ;;
    esac
}
```

- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ nano ~/praktikum-os/week09/scripts/hapus-file.sh
- isi:
```bash
#!/bin/bash
# Script: hapus-file.sh
source ~/praktikum-os/week09/scripts/lib-validasi.sh

FILE_TARGET=$1

# Validasi apakah argumen file diberikan
if [ -z "$FILE_TARGET" ]; then
    error_exit "Penggunaan: $0 <nama-file>"
fi

# Validasi apakah file benar-benar ada
if ! [ -e "$FILE_TARGET" ]; then
    error_exit "File '$FILE_TARGET' tidak ditemukan!"
fi

# Memanggil fungsi konfirmasi dari library
if konfirmasi "Apakah Anda yakin ingin menghapus file '$FILE_TARGET'?"; then
    rm "$FILE_TARGET"
    sukses "File '$FILE_TARGET' telah dihapus."
else
    info "Penghapusan dibatalkan."
fi
```

- chmod +x ~/praktikum-os/week09/scripts/hapus-file.sh
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ chmod +x ~/praktikum-os/week09/scripts/hapus-file.sh
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ touch tes-hapus.txt
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./hapus-file.sh tes-hapus.txt
```bash
Apakah Anda yakin ingin menghapus file 'tes-hapus.txt'? (y/n): n
[ INFO ] Penghapusan dibatalkan.
```

- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./hapus-file.sh tes-hapus.txt
```bash
Apakah Anda yakin ingin menghapus file 'tes-hapus.txt'? (y/n): y
[ OK ] File 'tes-hapus.txt' telah dihapus.
```

# Praktikum 7.5 Script Backup dengan Opsi
 **1. Langkah: Buat script:**
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ nano ~/praktikum-os/week09/scripts/backup-data.sh
- isi:
```bash
#!/bin/bash
# Penggunaan: ./backup-data.sh [-v] [-c] [-l logfile] <sumber> <tujuan>

VERBOSE=false
COMPRESS=false
LOG_FILE=""

# Memproses opsi menggunakan getopts
while getopts "vcl:" OPSI; do
  case $OPSI in
    v) VERBOSE=true ;;
    c) COMPRESS=true ;;
    l) LOG_FILE="$OPTARG" ;;
    *) echo "Penggunaan: $0 [-v] [-c] [-l logfile] <sumber> <tujuan>"
       exit 1 ;;
  esac
done

# Menggeser pointer argumen setelah memproses opsi
shift $((OPTIND - 1))

SUMBER=$1
TUJUAN=$2

# Fungsi internal untuk logging
log() {
  local MSG="[$(date '+%T')] $1"
  echo "$MSG"
  [ -n "$LOG_FILE" ] && echo "$MSG" >> "$LOG_FILE"
}

# Validasi input sumber dan tujuan
if [ -z "$SUMBER" ] || [ -z "$TUJUAN" ]; then
    echo "ERROR: Sumber dan tujuan wajib diisi"
    exit 1
fi

if [ ! -d "$SUMBER" ]; then
    log "ERROR: $SUMBER tidak ada atau bukan direktori"
    exit 2
fi

# Membuat folder tujuan jika belum ada
mkdir -p "$TUJUAN"
TANGGAL=$(date '+%F-%H%M%S')

[ "$VERBOSE" = true ] && log "Memulai backup. Sumber: $SUMBER | Tujuan: $TUJUAN"

if [ "$COMPRESS" = true ]; then
    ARSIP="$TUJUAN/backup-$(basename "$SUMBER")-$TANGGAL.tar.gz"
    # Mengompres direktori
    tar -czf "$ARSIP" -C "$(dirname "$SUMBER")" "$(basename "$SUMBER")"
    log "Backup dikompresi: $ARSIP ($(du-sh "$ARSIP" | cut -f1))"
else
    # Copy biasa jika tanpa opsi -c
    CP_DEST="$TUJUAN/backup-$(basename "$SUMBER")-$TANGGAL"
    cp -r "$SUMBER" "$CP_DEST"
    log "Backup selesai (copy biasa) ke: $CP_DEST"
fi
```

- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ touch ~/praktikum-os/week09/data/file{1..3}.txt

**2. Langkah: Memberi Izin dan Menguji Script:**
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ chmod +x ~/praktikum-os/week09/scripts/backup-data.sh
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./backup-data.sh -v -c -l ../logs/backup.log ~/praktikum-os/week09/data ~/praktikum-os/week09/logs
```bash
[18:16:33] Memulai backup. Sumber: /home/Rappizr7/praktikum-os/week09/data | Tujuan: /home/Rappizr7/praktikum-os/week09/logs
./backup-data.sh: line 53: du-sh: command not found
[18:16:33] Backup dikompresi: /home/Rappizr7/praktikum-os/week09/logs/backup-data-2026-04-26-181633.tar.gz ()
```

- hasil/cek:
```bash
Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ cat ../logs/backup.log
[18:16:33] Memulai backup. Sumber: /home/Rappizr7/praktikum-os/week09/data | Tujuan: /home/Rappizr7/praktikum-os/week09/logs
[18:16:33] Backup dikompresi: /home/Rappizr7/praktikum-os/week09/logs/backup-data-2026-04-26-181633.tar.gz ()
Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ls -lh ~/praktikum-os/week09/logs
total 8.0K
-rw-rw-r-- 1 Rappizr7 Rappizr7 166 Apr 26 18:16 backup-data-2026-04-26-181633.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7 235 Apr 26 18:16 backup.log
```

# Praktikum 7.6 Debugging Script
 **1. Langkah: Buat script:**
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ nano ~/praktikum-os/week09/scripts/debug-latihan.sh
- isi:
```bash
#!/bin/bash
# Script: debug-latihan.sh
# Penggunaan: ./debug-latihan.sh <direktori> <batas-MB>

DIREKTORI=$1
BATAS=$2

# Validasi jumlah argumen
if [ $# -ne 2 ]; then
    echo "Penggunaan: $0 <direktori> <batas-MB>"
    exit 1
fi

# Mengambil ukuran direktori dalam MegaBytes
UKURAN=$(du -sm "$DIREKTORI" | cut -f1)

echo "--------------------------------"
echo "Direktori : $DIREKTORI"
echo "Ukuran    : ${UKURAN} MB"
echo "Batas     : ${BATAS} MB"
echo "--------------------------------"

if [ "$UKURAN" -gt "$BATAS" ]; then
    echo "STATUS    : PERINGATAN - Ukuran melebihi batas!"
    echo "Kelebihan : $((UKURAN - BATAS)) MB"
else
    echo "STATUS    : Normal (sisa: $((BATAS - UKURAN)) MB)"
fi
echo "--------------------------------"
```

**2. Langkah: Memberi Izin dan Menguji Script:**
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ chmod +x debug-latihan.sh
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ bash -n debug-latihan.sh && echo "Sintaks OK"
- hasil:
```bash
Sintaks OK
```

- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ bash -x debug-latihan.sh /etc 10
- hasil:
```bash
+ DIREKTORI=/etc
+ BATAS=10
+ '[' 2 -ne 2 ']'
++ du -sm /etc
++ cut -f1
du: cannot read directory '/etc/polkit-1/rules.d': Permission denied
du: cannot read directory '/etc/multipath': Permission denied
du: cannot read directory '/etc/ssl/private': Permission denied
du: cannot read directory '/etc/credstore.encrypted': Permission denied
du: cannot read directory '/etc/credstore': Permission denied
+ UKURAN=7
+ echo --------------------------------
--------------------------------
+ echo 'Direktori : /etc'
Direktori : /etc
+ echo 'Ukuran    : 7 MB'
Ukuran    : 7 MB
+ echo 'Batas     : 10 MB'
Batas     : 10 MB
+ echo --------------------------------
--------------------------------
+ '[' 7 -gt 10 ']'
+ echo 'STATUS    : Normal (sisa: 3 MB)'
STATUS    : Normal (sisa: 3 MB)
+ echo --------------------------------
--------------------------------
```

- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./debug-latihan.sh /var 50
- hasil:
```bash
du: cannot read directory '/var/spool/cron/crontabs': Permission denied
du: cannot read directory '/var/spool/rsyslog': Permission denied
du: cannot read directory '/var/lib/fwupd/gnupg': Permission denied
du: cannot read directory '/var/lib/update-notifier/package-data-downloads/partial': Permission denied
du: cannot read directory '/var/lib/polkit-1': Permission denied
du: cannot read directory '/var/lib/udisks2': Permission denied
du: cannot read directory '/var/lib/apt/lists/partial': Permission denied
du: cannot read directory '/var/lib/private': Permission denied
du: cannot read directory '/var/lib/ubuntu-advantage/apt-esm/var/lib/apt/lists/partial': Permission denied
du: cannot read directory '/var/lib/snapd/void': Permission denied
du: cannot read directory '/var/lib/snapd/cookie': Permission denied
du: cannot read directory '/var/tmp/systemd-private-f100cd92715347f28a99bd3cf15047f9-ModemManager.service-8UKSng': Permission denied
du: cannot read directory '/var/tmp/systemd-private-f100cd92715347f28a99bd3cf15047f9-systemd-resolved.service-fNAjQS': Permission denied
du: cannot read directory '/var/tmp/systemd-private-f100cd92715347f28a99bd3cf15047f9-systemd-timesyncd.service-szvMTf': Permission denied
du: cannot read directory '/var/tmp/systemd-private-f100cd92715347f28a99bd3cf15047f9-systemd-logind.service-S8337I': Permission denied
du: cannot read directory '/var/tmp/systemd-private-f100cd92715347f28a99bd3cf15047f9-polkit.service-FKxfjv': Permission denied
du: cannot read directory '/var/cache/pollinate': Permission denied
du: cannot read directory '/var/cache/apparmor/2693c843.0': Permission denied
du: cannot read directory '/var/cache/apt/archives/partial': Permission denied
du: cannot read directory '/var/cache/ldconfig': Permission denied
du: cannot read directory '/var/cache/private': Permission denied
du: cannot read directory '/var/log/private': Permission denied
du: cannot read directory '/var/log/installer': Permission denied
du: cannot read directory '/var/log/unattended-upgrades': Permission denied
--------------------------------
Direktori : /var
Ukuran    : 1664 MB
Batas     : 50 MB
--------------------------------
STATUS    : PERINGATAN - Ukuran melebihi batas!
Kelebihan : 1614 MB
--------------------------------
```

- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./debug-latihan.sh
- hasil:
```bash
Penggunaan: ./debug-latihan.sh <direktori> <batas-MB>
```

**Latihan Latihan 9.5**
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ nano ~/praktikum-os/week09/scripts/debug-latihan.sh
- isi:
```bash
#!/bin/bash
set -e
# Script: debug-latihan.sh
# Penggunaan: ./debug-latihan.sh <direktori> <batas-MB>

DIREKTORI=$1
BATAS=$2

# 1. Validasi jumlah argumen
if [ $# -ne 2 ]; then
    echo "Penggunaan: $0 <direktori> <batas-MB>"
    exit 1
fi

# 2. Pengecekan apakah direktori ada
if [ ! -d "$DIREKTORI" ]; then
    echo "ERROR: Direktori '$DIREKTORI' tidak ditemukan!" >&2
    exit 1
fi

# 3. Mengambil ukuran direktori dalam MegaBytes
UKURAN=$(du -sm "$DIREKTORI" | cut -f1)

echo "--------------------------------"
echo "Direktori : $DIREKTORI"
echo "Ukuran    : ${UKURAN} MB"
echo "Batas     : ${BATAS} MB"
echo "--------------------------------"

if [ "$UKURAN" -gt "$BATAS" ]; then
    echo "STATUS    : PERINGATAN - Ukuran melebihi batas!"
    echo "Kelebihan : $((UKURAN - BATAS)) MB"
else
    echo "STATUS    : Normal (sisa: $((BATAS - UKURAN)) MB)"
fi
echo "--------------------------------"
```

- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./debug-latihan.sh /folder/palsu 100
```bash
ERROR: Direktori '/folder/palsu' tidak ditemukan!
```

# Tugas 1
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ nano absensi.sh
- isi:
```bash
#!/bin/bash
# Script: absensi.sh
# Deskripsi: Mencatat kehadiran mahasiswa ke file log harian

# Konfigurasi nama file berdasarkan tanggal
TANGGAL=$(date '+%Y-%m-%d')
FILE_ABSENSI="../logs/absensi-$TANGGAL.txt"

# Fungsi bantuan
usage() {
    echo "Penggunaan: $0 [nama] [status]"
    echo "Status: hadir | izin | alpha"
    echo ""
    echo "Opsi:"
    echo "  -r    Tampilkan rekapitulasi kehadiran hari ini"
    echo "  -h    Tampilkan bantuan"
    exit 0
}

# Fungsi rekapitulasi
rekapitulasi() {
    if [ ! -f "$FILE_ABSENSI" ]; then
        echo "Belum ada data absensi untuk hari ini ($TANGGAL)."
        exit 0
    fi

    echo "=== REKAPITULASI ABSENSI ($TANGGAL) ==="
    hadir=$(grep -c "HADIR" "$FILE_ABSENSI")
    izin=$(grep -c "IZIN" "$FILE_ABSENSI")
    alpha=$(grep -c "ALPHA" "$FILE_ABSENSI")
    
    echo "Hadir : $hadir"
    echo "Izin  : $izin"
    echo "Alpha : $alpha"
    echo "Total : $((hadir + izin + alpha))"
    echo "======================================"
}

# Menangani opsi dengan getopts
while getopts "rh" OPSI; do
    case $OPSI in
        r) rekapitulasi; exit 0 ;;
        h) usage ;;
        *) usage ;;
    esac
done

# Geser argumen setelah opsi
shift $((OPTIND - 1))

# Validasi input (Nama dan Status wajib ada)
if [ $# -lt 2 ]; then
    usage
fi

NAMA=$1
# Mengubah status menjadi huruf besar semua agar konsisten
STATUS=$(echo "$2" | tr '[:lower:]' '[:upper:]')

# Simpan ke file dengan format [HH:MM] NAMA - STATUS
JAM=$(date '+%H:%M')
echo "[$JAM] $NAMA - $STATUS" >> "$FILE_ABSENSI"

echo "Berhasil mencatat: $NAMA ($STATUS)"
```

- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ chmod +x absensi.sh
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./absensi.sh "Andi" hadir
./absensi.sh "Budi" hadir
./absensi.sh "Citra" izin
./absensi.sh "Deni" alpha
./absensi.sh "Eka" hadir
```bash
Berhasil mencatat: Andi (HADIR)
Berhasil mencatat: Budi (HADIR)
Berhasil mencatat: Citra (IZIN)
Berhasil mencatat: Deni (ALPHA)
Berhasil mencatat: Eka (HADIR)
```

- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./absensi.sh -r
```bash
=== REKAPITULASI ABSENSI (2026-04-26) ===
Hadir : 3
Izin  : 1
Alpha : 1
Total : 5
======================================
```

- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ cat ../logs/absensi-$(date '+%Y-%m-%d').txt
```bash
[18:39] Andi - HADIR
[18:39] Budi - HADIR
[18:39] Citra - IZIN
[18:39] Deni - ALPHA
[18:39] Eka - HADIR
```

# Tugas 2
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ nano healthcheck.sh
- isi:
```bash
#!/bin/bash
# ====================================================
# Script    : healthcheck.sh
# Deskripsi : Pemeriksaan kondisi server sebelum maintenance
# ====================================================

set -euo pipefail

# --- Konfigurasi & Variabel Global ---
SCRIPT_NAME=$(basename "$0")
BATAS_DISK=80
LOG_FILE="../logs/healthcheck-$(date '+%Y-%m-%d').log"

# --- Fungsi ---

usage() {
    echo "Penggunaan: $SCRIPT_NAME [-t persen] [-h]"
    echo "  -t <persen>  Ubah batas peringatan disk (Default: 80)"
    echo "  -h           Tampilkan bantuan"
    exit 0
}

log_header() {
    echo "========================================"
    echo "         HEALTH CHECK SISTEM            "
    echo "========================================"
}

cleanup() {
    echo "----------------------------------------"
    echo "Pemeriksaan selesai. Log: $LOG_FILE"
}

# Trap untuk menjalankan cleanup saat script berakhir
trap cleanup EXIT

# --- Pengolahan Opsi ---

while getopts "t:h" OPSI; do
    case $OPSI in
        t) BATAS_DISK=$OPTARG ;;
        h) usage ;;
        *) usage ;;
    esac
done

# --- Logika Utama ---

{
    log_header
    echo "Waktu    : $(date '+%Y-%m-%d %H:%M:%S')"
    echo "Hostname : $(hostname)"
    echo "Uptime   : $(uptime -p)"
    echo "----------------------------------------"
    
    # Info CPU
    echo "CPU Load : $(top -bn1 | grep "Cpu(s)" | awk '{print $2 + $4}')%"
    
    # Info Memori
    echo "Memori   : $(free -h | awk '/^Mem:/ {print $3 "/" $2 " terpakai (" $7 " tersedia)"}')"
    echo "----------------------------------------"
    
    # Info Disk & Peringatan
    echo "Pemeriksaan Disk (Batas: ${BATAS_DISK}%):"
    
    # Mengambil semua filesystem terpasang (skip header)
    df -h --output=source,pcent,target | tail -n +2 | while read -r LINE; do
        local FS=$(echo $LINE | awk '{print $1}')
        local PERSEN=$(echo $LINE | awk '{print $2}' | tr -d '%')
        local MOUNT=$(echo $LINE | awk '{print $3}')
        
        if [ "$PERSEN" -ge "$BATAS_DISK" ]; then
            echo "[!] PERINGATAN: $FS ($MOUNT) mencapai ${PERSEN}%"
        else
            echo "[OK] $FS ($MOUNT) - ${PERSEN}%"
        fi
    done
} | tee -a "$LOG_FILE"
```

- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ chmod +x healthcheck.sh
- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./healthcheck.sh
- hasil:
```bash
========================================
         HEALTH CHECK SISTEM
========================================
Waktu    : 2026-04-26 18:44:31
Hostname : ubuntu-server
Uptime   : up 29 minutes
----------------------------------------
CPU Load : 3.1%
Memori   : 427Mi/3.9Gi terpakai (3.5Gi tersedia)
----------------------------------------
Pemeriksaan Disk (Batas: 80%):
./healthcheck.sh: line 68: local: can only be used in a function
----------------------------------------
Pemeriksaan selesai. Log: ../logs/healthcheck-2026-04-26.log
```

- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ ./healthcheck.sh
- hasil:
```bash
========================================
         HEALTH CHECK SISTEM
========================================
Waktu    : 2026-04-26 18:44:38
Hostname : ubuntu-server
Uptime   : up 29 minutes
----------------------------------------
CPU Load : 1.9%
Memori   : 426Mi/3.9Gi terpakai (3.5Gi tersedia)
----------------------------------------
Pemeriksaan Disk (Batas: 10%):
./healthcheck.sh: line 68: local: can only be used in a function
----------------------------------------
Pemeriksaan selesai. Log: ../logs/healthcheck-2026-04-26.log
```

- Rappizr7@ubuntu-server:~/praktikum-os/week09/scripts$ cat ../logs/healthcheck-$(date '+%Y-%m-%d').log
- hasil:
```bash
========================================
         HEALTH CHECK SISTEM
========================================
Waktu    : 2026-04-26 18:44:31
Hostname : ubuntu-server
Uptime   : up 29 minutes
----------------------------------------
CPU Load : 3.1%
Memori   : 427Mi/3.9Gi terpakai (3.5Gi tersedia)
----------------------------------------
Pemeriksaan Disk (Batas: 80%):
========================================
         HEALTH CHECK SISTEM
========================================
Waktu    : 2026-04-26 18:44:38
Hostname : ubuntu-server
Uptime   : up 29 minutes
----------------------------------------
CPU Load : 1.9%
Memori   : 426Mi/3.9Gi terpakai (3.5Gi tersedia)
----------------------------------------
Pemeriksaan Disk (Batas: 10%):
```
