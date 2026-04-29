# Laporan Pertemuan ke-10 Sistem Operasi

**Tanggal:** 29 April 2026  
**Disusun Oleh:** Mukhammad Raffi Zabra  
**NIM:** 254107020059  
**Kelas/No:** TI-1G/24  

---

# Praktikum 10.1 Melihat Penggunaan Memori
**1. jalankan free -h untuk melihat ringkasan RAM dan swap.**
```bash
Rappizr7@ubuntu-server:~$ free -h
               total        used        free      shared  buff/cache   available
Mem:           3.9Gi       610Mi       773Mi       1.1Mi       2.8Gi       3.3Gi
Swap:          4.0Gi          0B       4.0Gi
```

**2. Lihat detail memori dari kernel melalui /proc/meminfo.**
```bash
Rappizr7@ubuntu-server:~$ cat /proc/meminfo | head -n 20
MemTotal:        4092200 kB
MemFree:          871916 kB
MemAvailable:    3377324 kB
Buffers:            2064 kB
Cached:          2627600 kB
SwapCached:            0 kB
Active:           541068 kB
Inactive:        2291084 kB
Active(anon):     212368 kB
Inactive(anon):        0 kB
Active(file):     328700 kB
Inactive(file):  2291084 kB
Unevictable:       27316 kB
Mlocked:           27316 kB
SwapTotal:       4194300 kB
SwapFree:        4194300 kB
Zswap:                 0 kB
Zswapped:              0 kB
Dirty:               228 kB
Writeback:             0 kB
```

**3. Analisis:**
1. Hitung persentase memori tersedia: available / total × 100%. Jika hasilnya
di bawah 10%, sistem mulai kekurangan memori.
```bash
Total Memori: 3.9 GiB

    Available Memori: 3.3 GiB

Rumus:
TotalAvailable​×100%

Perhitungan:
3.93.3​×100% = 84.6%

Analisis: sistem sangat sehat. karena 84.6% jauh di atas ambang batas (10%), 
server saya masih memiliki ruang yang sangat luas untuk menjalankan aplikasi tambahan.
```

2. Pada baris Swap, apakah kolom used bernilai 0? Jika lebih dari 0, kernel sudah
pernah memindahkan data ke disk karena RAM tidak cukup.
```bash
Swap Used: 0B

Swap Free: 4.0GiB

Analisis: nilai 0B menunjukkan bahwa kernel Linux sama sekali belum menyentuh area disk untuk meminta 
memori
```

3. Perhatikan field Cached dan Buffers di /proc/meminfo. Nilai ini sesuai
dengan kolom buff/cache pada free -h.
```bash
Buff/Cache pada free -h: Bernilai 2.8 GiB.

    Detail di /proc/meminfo:

        Buffers: 2064 kB

        Cached: 2627600 kB (-2.5 GiB)

        SReclaimable (biasanya ada di baris bawah): Melengkapi sisa nilai tersebut.

Analisis: RAM (2.8 GiB) saat ini digunakan sebagai Cache. Ini  normal Linux untuk mempercepat sistem.
nilai Inactive(file) sebesar 2,291,084 kB; ini adalah bagian dari cache yang berisi data file yang sudah tidak dipakai dan bisa langsung dihapus oleh kernel jika tiba-tiba 
ada aplikasi yang butuh RAM besar.
```

# Studi Kasus 10.1 Server Lambat karena Memori
**1. Periksa kondisi memori secara keseluruhan.**
```bash
Rappizr7@ubuntu-server:~$ free -h
               total        used        free      shared  buff/cache   available
Mem:           3.9Gi       518Mi       1.8Gi       1.1Mi       1.8Gi       3.4Gi
Swap:          4.0Gi          0B       4.0Gi
```

**2. Pantau proses secara real-time.**
```bash
 Rappizr7@ubuntu-server:~$ top
top - 13:58:05 up  1:18,  2 users,  load average: 0.10, 0.06, 0.15
Tasks: 139 total,   1 running, 138 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.2 sy,  0.0 ni, 99.7 id,  0.0 wa,  0.0 hi,  0.2 si,  0.0 st
MiB Mem : 13.0/3996.3   [|||||||||                                                                  ]
MiB Swap:  0.0/4096.0   [                                                                           ]

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND

  18796 root      20   0       0      0      0 I   2.0   0.0   0:00.86 kworker/3:3-events
   1235 root      20   0       0      0      0 I   0.3   0.0   0:08.56 kworker/1:2-events
  18807 Rappizr7  20   0   11960   5996   3740 R   0.3   0.1   0:00.32 top

      1 root      20   0   22552  13848   9728 S   0.0   0.3   0:06.39 systemd
      2 root      20   0       0      0      0 S   0.0   0.0   0:00.14 kthreadd
      3 root      20   0       0      0      0 S   0.0   0.0   0:00.00 pool_workqueue_release
      4 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-rcu_g
      5 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-rcu_p
      6 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-slub_
      7 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-netns
     11 root      20   0       0      0      0 I   0.0   0.0   0:00.00 kworker/u8:0-ipv6_addrconf
     12 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-mm_pe
     13 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_kthread
     14 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_rude_kthread
     15 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_trace_kthread
     16 root      20   0       0      0      0 S   0.0   0.0   0:00.29 ksoftirqd/0
     17 root      20   0       0      0      0 I   0.0   0.0   0:09.80 rcu_preempt
     18 root      rt   0       0      0      0 S   0.0   0.0   0:00.40 migration/0
     19 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/0
     20 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/0
     21 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/1
     22 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/1
     23 root      rt   0       0      0      0 S   0.0   0.0   0:00.79 migration/1
     24 root      20   0       0      0      0 S   0.0   0.0   0:03.09 ksoftirqd/1
     27 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/2
     28 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/2
     29 root      rt   0       0      0      0 S   0.0   0.0   0:00.58 migration/2
     30 root      20   0       0      0      0 S   0.0   0.0   0:01.62 ksoftirqd/2
     33 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/3
     34 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/3
     35 root      rt   0       0      0      0 S   0.0   0.0   0:00.71 migration/3
     36 root      20   0       0      0      0 S   0.0   0.0   0:00.29 ksoftirqd/3
     43 root      20   0       0      0      0 S   0.0   0.0   0:00.01 kdevtmpfs
     44 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-inet_
     45 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kauditd
     46 root      20   0       0      0      0 S   0.0   0.0   0:00.00 khungtaskd
     47 root      20   0       0      0      0 S   0.0   0.0   0:00.00 oom_reaper
     50 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-write
     51 root      20   0       0      0      0 S   0.0   0.0   0:00.62 kcompactd0
     52 root      25   5       0      0      0 S   0.0   0.0   0:00.00 ksmd
     54 root      39  19       0      0      0 S   0.0   0.0   0:00.00 khugepaged
     55 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-kinte
     56 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-kbloc
     57 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-blkcg
     58 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 irq/9-acpi
     60 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-
```

**3. Analisis:**
1. Apakah nilai available sangat kecil (misalnya di bawah 200 MB pada server
dengan RAM 2 GB)? Jika ya, server kemungkinan kekurangan memori.
```bash
Data: Memori available adalah 3.4 GiB dari total 3.9 GiB.

Analisis: Ambang batas kritis yang disebutkan adalah 200 MB (0.2 GiB). 
Nilai 3.4 GiB jauh di atas ambang batas tersebut.
```

2. Apakah kolom used pada baris Swap lebih dari 0? Jika ya, kernel sedang
menggunakan swap, yang berarti performa menurun.
```bash
Data: Kolom used pada baris Swap adalah 0B.

Analisis: Kernel Linux sama sekali tidak memindahkan data ke disk (Swap). Hal ini menunjukkan 
bahwa RAM fisik masih sangat lega untuk menangani semua beban kerja yang ada.
```

3. Di tampilan top, proses apa yang memiliki %MEM terbesar? Proses tersebut
menjadi kandidat utama penyebab lambatnya server.
```bash
Proses Teratas: systemd (PID 1) menggunakan 0.3% MEM.
Proses User: top (PID 18807) menggunakan 0.1% MEM.
Lainnya: Sisanya adalah proses kernel (kworker, rcu_preempt, dll.) yang menggunakan 0.0% MEM.

Analisis:
Saat ini, tidak ada proses yang rakus memori. Proses yang menggunakan memori paling besar 
pun hanya memakan 0.3%.
```

# Praktikum 10.2 Mengamati Aktivitas Paging
**1. Jalankan vmstat dengan interval 1 detik, 5 sampel.**
```bash
Rappizr7@ubuntu-server:~$ vmstat 1 5
procs -----------memory---------- ---swap-- -----io---- -system-- -------cpu-------
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st gu
 0  0      0 1942480   7460 1916376    0    0   261   431  324    0  1  1 98  0  0  0
 0  0      0 1942480   7460 1916384    0    0     0     0  466  130  0  2 98  0  0  0
 1  0      0 1942480   7460 1916424    0    0     0     0  220   85  0  0 100  0  0  0
 2  0      0 1942480   7460 1916424    0    0     0     0  106   42  0  0 100  0  0  0
 1  0      0 1942480   7460 1916424    0    0     0     0  235   94  0  0 100  0  0  0
 ```

**2. Analisis:**
1. Amati nilai si dan so pada kelima baris. Pada sistem normal dengan RAM cukup, kedua nilai ini selalu 0.
```bash
Data: Nilai pada kolom si (Swap-In) dan so (Swap-Out) di seluruh baris sampel menunjukkan angka 0.
Interpretasi: * si = 0: Tidak ada data yang sedang dipindahkan dari disk kembali ke RAM.

so = 0: Tidak ada data yang sedang dibuang dari RAM ke disk.

Analisis: Sistem Anda berada dalam kondisi sangat stabil. Tidak ada indikasi memory pressure. 
Kernel merasa RAM fisik masih sangat cukup sehingga tidak perlu melibatkan mekanisme memori virtual (swap) yang lambat.
```

2. Jika nilai si atau so sesekali muncul lebih dari 0, artinya pernah ada aktivitas swap. Ini masih wajar jika tidak terus-menerus.
```bash
Kolom free: Sekitar 1.94 GB. Ini adalah jumlah RAM yang benar-benar kosong dan 
belum digunakan untuk apapun.

Kolom cache: Sekitar 1.91 GB. Angka ini cukup besar, menunjukkan Linux sedang 
mengoptimalkan performa dengan menyimpan data file di RAM.

Kolom swpd: Bernilai 0. Ini sinkron dengan data sebelumnya; artinya saat ini tidak 
ada memori virtual yang sedang terpakai sama sekali.
```

3. Jika si dan so terus-menerus lebih dari 0, sistem dalam kondisi memory
pressure serius — performa turun drastis karena akses disk jauh lebih lambat
dari RAM.
```bash 
Kolom id (Idle CPU): Berada di angka 98% - 100%. Artinya CPU Anda hampir tidak bekerja (sangat santai).

Kolom bi/bo (Block In/Out): Sempat ada aktivitas di baris pertama (261/431), namun baris berikutnya 
kembali ke 0. Ini menandakan aktivitas tulis-baca disk yang bersifat sementara dan tidak 
membebani sistem secara kontinu.
```
4. Perhatikan juga kolom free (RAM kosong) dan buff (buffer) untuk memahami
kondisi keseluruhan RAM saat itu.
```bash
Data: free berada di kisaran 1.942.480 KB (-1,85 GiB) dan buff di 7.460 KB.

Analisis: Kapasitas free yang besar menunjukkan sistem masih memiliki banyak untuk
menjalankan aplikasi baru. Sementara nilai buff yang kecil menunjukkan aktivitas I/O (input/output) 
mentah ke disk sedang rendah, yang konsisten dengan nilai bi/bo yang mayoritas 0.
```

# Praktikum 10.3 Membuat dan Mengonfigurasi Swap File
**1. Buat file berukuran 512 MB sebagai calon swap.**
```bash
Rappizr7@ubuntu-server:~$ sudo fallocate -l 512M /swapfile-week10
```

**2. Atur permission file menjadi 600 — hanya root yang boleh membaca dan menulis.**
```bash
Rappizr7@ubuntu-server:~$ sudo chmod 600 /swapfile-week10
```

**3. Format file sebagai area swap, lalu aktifkan.**
```bash
Rappizr7@ubuntu-server:~$ sudo mkswap /swapfile-week10
Setting up swapspace version 1, size = 512 MiB (536866816 bytes)
no label, UUID=e97ab3d9-1f58-464b-aa30-70faa6ead94c
Rappizr7@ubuntu-server:~$ sudo swapon /swapfile-week10
```

**4. Verifikasi swap aktif. Anda akan melihat entri /swapfile-week10 dengan ukuran 512M, dan nilai total pada baris Swap di free -h bertambah 512M.**
```bash
Rappizr7@ubuntu-server:~$ swapon --show
NAME             TYPE SIZE USED PRIO
/swapfile        file   4G   0B   -2
/swapfile-week10 file 512M   0B   -3
Rappizr7@ubuntu-server:~$ free -h
               total        used        free      shared  buff/cache   available
Mem:           3.9Gi       509Mi       1.9Gi       1.1Mi       1.8Gi       3.4Gi
Swap:          4.5Gi          0B       4.5Gi
```

**5. Periksa nilai swappiness, ubah sementara, dan verifikasi perubahan.**
```bash
Rappizr7@ubuntu-server:~$ cat /proc/sys/vm/swappiness
60
Rappizr7@ubuntu-server:~$ sudo sysctl vm.swappiness=10
vm.swappiness = 10
Rappizr7@ubuntu-server:~$ cat /proc/sys/vm/swappiness
10
```

**2. Analisis:**
1. Berapa nilai swappiness default? Apa artinya bagi perilaku kernel dalam
menggunakan swap?
```bash
Nilai Default: Berdasarkan perintah cat pertama, nilai default sistem Anda adalah 60.

Artinya bagi Kernel: Angka 60 adalah nilai moderat. Ini berarti kernel akan mulai memindahkan data dari RAM
ke Swap bahkan ketika RAM belum benar-benar penuh. Tujuannya agar sistem selalu memiliki ruang RAM yang
lega untuk cache file, namun dampaknya bisa sedikit memperlambat respons jika disk yang digunakan lambat.
```

2. Setelah diubah ke 10, konfirmasi nilai berubah pada output cat kedua. Apa
dampak nilai 10 terhadap penggunaan swap dibanding nilai 60?
```bash
Konfirmasi: Nilai berhasil berubah menjadi 10 pada output cat kedua.

Dampak Penggunaan Swap: Dengan nilai 10, kernel menjadi jauh lebih pasif dalam menggunakan swap.

Prioritas RAM: Kernel akan berusaha menggunakan RAM fisik sampai batas maksimal dan hanya akan 
melakukan swapping jika RAM benar-benar hampir habis.

Performa: Ini biasanya meningkatkan performa pada sistem dengan RAM yang cukup,
karena akses ke RAM jauh lebih cepat daripada akses ke file swap di disk.
```

3. Apakah entri /swapfile-week10 muncul di swapon –show? Jika tidak,
pastikan Langkah 2 (chmod 600) sudah dijalankan sebelum Langkah 3.
```bash
Status: Ya, muncul. File /swapfile-week10 terlihat dengan ukuran 512M.

Analisis Keamanan: Munculnya entri ini menandakan Langkah 2 (chmod 600) telah dilakukan dengan benar.

Pentingnya Permission: Jika izin file tetap terbuka (misal 644), Linux akan memberikan peringatan keamanan saat swapon dijalankan karena data sensitif aplikasi yang ada di memori bisa dibaca oleh pengguna lain. Dengan izin 600, hanya root yang memiliki akses penuh terhadap data di dalam swap tersebut.
```

# Praktikum 10.4 Monitoring Memory
**1. Ambil snapshot proses diurutkan dari penggunaan memori terbesar.**
```bash
Rappizr7@ubuntu-server:~$ ps aux --sort=-%mem | head
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root       17674  0.0  1.0 478628 42668 ?        Ssl  13:37   0:01 /usr/libexec/fwupd/fwupd
root       12518  0.1  0.6 223568 27308 ?        SLsl 13:34   0:07 /sbin/multipathd -d -s
root         688  0.0  0.5 109688 23252 ?        Ssl  12:39   0:00 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
root       12526  0.0  0.3  50340 15128 ?        S<s  13:34   0:01 /usr/lib/systemd/systemd-journald
root           1  0.0  0.3  22552 13848 ?        Ss   12:39   0:06 /usr/lib/systemd/systemd --system --deserialize=130 splash noprompt noshell automatic-ubiquity
root       17675  0.0  0.3 468976 13592 ?        Ssl  13:37   0:00 /usr/libexec/udisks2/udisksd
systemd+   12549  0.0  0.3  21584 13320 ?        Ss   13:34   0:00 /usr/lib/systemd/systemd-resolved
root       17608  0.0  0.3 392100 13048 ?        Ssl  13:37   0:00 /usr/sbin/ModemManager
Rappizr7     960  0.0  0.2  20284 11512 ?        Ss   12:40   0:00 /usr/lib/systemd/systemd --user
```

**2. Pantau secara real-time dengan top.**
```bash
Rappizr7@ubuntu-server:~$ top
top - 15:06:15 up  2:26,  2 users,  load average: 0.19, 0.05, 0.01
Tasks: 139 total,   1 running, 138 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.3 sy,  0.0 ni, 99.6 id,  0.0 wa,  0.0 hi,  0.2 si,  0.0 st
MiB Mem : 12.8/3996.3   [|||||||||                                                            ]
MiB Swap:  0.0/4608.0   [                                                                     ]

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
   8360 Rappizr7  20   0   14972   7168   5204 S   1.0   0.2   0:10.68 sshd
  18962 root      20   0       0      0      0 I   1.0   0.0   0:00.97 kworker/3:2-events
   1236 root      20   0       0      0      0 I   0.6   0.0   0:03.54 kworker/u9:0-events_unb+
  18886 root      20   0       0      0      0 I   0.3   0.0   0:02.02 kworker/1:1-events
  18964 Rappizr7  20   0   11960   6004   3752 R   0.3   0.1   0:00.36 top
      1 root      20   0   22552  13848   9728 S   0.0   0.3   0:06.90 systemd
      2 root      20   0       0      0      0 S   0.0   0.0   0:00.34 kthreadd
      3 root      20   0       0      0      0 S   0.0   0.0   0:00.00 pool_workqueue_release
      4 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-rcu_g
      5 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-rcu_p
      6 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-slub_
      7 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-netns
     11 root      20   0       0      0      0 I   0.0   0.0   0:00.00 kworker/u8:0-ipv6_addrc+
     12 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-mm_pe
     13 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_kthread
     14 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_rude_kthread
     15 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_trace_kthread
     16 root      20   0       0      0      0 S   0.0   0.0   0:00.32 ksoftirqd/0
     17 root      20   0       0      0      0 I   0.0   0.0   0:11.73 rcu_preempt
     18 root      rt   0       0      0      0 S   0.0   0.0   0:00.67 migration/0
     19 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/0
     20 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/0
     21 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/1
     22 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/1
     23 root      rt   0       0      0      0 S   0.0   0.0   0:01.07 migration/1
     24 root      20   0       0      0      0 S   0.0   0.0   0:03.57 ksoftirqd/1
     27 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/2
     28 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/2
     29 root      rt   0       0      0      0 S   0.0   0.0   0:00.69 migration/2
     30 root      20   0       0      0      0 S   0.0   0.0   0:02.02 ksoftirqd/2
     33 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/3
     34 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/3
     35 root      rt   0       0      0      0 S   0.0   0.0   0:00.94 migration/3
     36 root      20   0       0      0      0 S   0.0   0.0   0:00.32 ksoftirqd/3
     43 root      20   0       0      0      0 S   0.0   0.0   0:00.01 kdevtmpfs
     44 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-inet_
     45 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kauditd
     46 root      20   0       0      0      0 S   0.0   0.0   0:00.01 khungtaskd
     47 root      20   0       0      0      0 S   0.0   0.0   0:00.00 oom_reaper
     50 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-write
     51 root      20   0       0      0      0 S   0.0   0.0   0:01.13 kcompactd0
     52 root      25   5       0      0      0 S   0.0   0.0   0:00.00 ksmd
     54 root      39  19       0      0      0 S   0.0   0.0   0:00.00 khugepaged
     55 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-kinte
     56 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-kbloc
     57 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-blkcg
```

**3. Analisis:**
1. Proses apa yang berada di urutan pertama? Catat nilai %MEM dan RSS-nya.
```bash
Analisis Data:
Berdasarkan hasil ps aux, proses yang berada di urutan pertama (setelah header) adalah:

COMMAND: /usr/libexec/fwupd/fwupd
%MEM: 1.0%
RSS: 42668 KB
```

2. Konversikan RSS dari KB ke MB (bagi 1024). Misalnya, RSS=524288 be-
rarti proses menggunakan 512 MB RAM. Apakah wajar untuk jenis program
tersebut?
```bash
Proses fwupd tersebut menggunakan sekitar 41,6 MB RAM fisik.

Apakah wajar? Sangat wajar. Untuk sebuah layanan latar belakang yang harus mengelola data perangkat keras, 
angka 41 MB itu tergolong kecil dan ringan. Tidak ada tanda-tanda penggunaan memori yang tidak normal.
```

3. Mengapa VSZ selalu lebih besar dari RSS pada proses yang sama?
```bash
- VSZ mencakup segala hal yang mungkin dibutuhkan proses.
- RSS hanya menghitung apa yang benar-benar ada di dalam keping RAM fisik sekarang.
Itulah mengapa VSZ selalu lebih besar proses seringkali memesan ruang yang luas untuk berjaga-jaga,
tapi hanya mengisi sebagian kecil saja dengan data aktif.
```

4. Apakah urutan proses di ps konsisten dengan tampilan top saat diurutkan
berdasarkan %MEM?
```bash
Analisis Data:
Pada ps aux, urutannya adalah: fwupd (1.0%), lalu multipathd (0.6%).
Pada top, urutan teratas adalah sshd (0.2%) dan top itu sendiri (0.1%).
```

# Praktikum 10.5 Script Monitor Memori
**1.  Pindah ke direktori dan buka nano**
```bash
Rappizr7@ubuntu-server:~$ cd ~/praktikum-os/week10-memory
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory$ nano monitor-memori.sh
- isi
#!/bin/bash
set -euo pipefail

THRESHOLD=20

echo "=== Monitor Memori ==="
date
echo

free -h
echo

AVAIL=$(free | awk '/Mem/ {printf "%d", $7/$2 * 100}')

if [ "$AVAIL" -lt "$THRESHOLD" ]; then
    echo "PERINGATAN: Memori tersedia hanya ${AVAIL}%!"
else
    echo "Status: Memori tersedia ${AVAIL}% (normal)"
fi

echo
echo "--- 5 Proses Memori Tertinggi ---"
# Mengambil 5 proses teratas setelah baris header
ps aux --sort=-%mem | head -n 6 | tail -n 5
```

**2.  Menjalankan script monitor**
```bash
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory$ chmod +x monitor-memori.sh
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory$ ./monitor-memori.sh
=== Monitor Memori ===
Wed Apr 29 03:39:40 PM UTC 2026

               total        used        free      shared  buff/cache   available
Mem:           3.9Gi       510Mi       1.8Gi       1.1Mi       1.8Gi       3.4Gi
Swap:          4.5Gi          0B       4.5Gi

Status: Memori tersedia 87% (normal)

--- 5 Proses Memori Tertinggi ---
root       17674  0.0  1.0 478628 42668 ?        Ssl  13:37   0:02 /usr/libexec/fwupd/fwupd
root       12518  0.1  0.6 223568 27308 ?        SLsl 13:34   0:10 /sbin/multipathd -d -s
root         688  0.0  0.5 109688 23252 ?        Ssl  12:39   0:00 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
root       12526  0.0  0.3  50340 15140 ?        S<s  13:34   0:01 /usr/lib/systemd/systemd-journald
root           1  0.0  0.3  22552 13848 ?        Ss   12:39   0:07 /usr/lib/systemd/systemd --system --deserialize=130 splash noprompt noshell automatic-ubiquity
```

**3. Analisis:**
1. Variabel THRESHOLD=20 menetapkan batas persentase. Perintah free | awk
’/Mem/ {printf "%d", $7/$2*100}’ mengambil kolom ke-7 (available) dibagi
kolom ke-2 (total) dari baris Mem, lalu dikalikan 100 untuk menghasilkan
persentase bilangan bulat.
```bash
Analisis:
Script ini tidak hanya sekadar menampilkan angka, tapi melakukan pengolahan data.

THRESHOLD=20: Ini adalah "garis peringatan". Kamu memberitahu sistem: "Kalau memori tersedia sudah 
di bawah 20%, berarti kita dalam masalah."

Proses awk: Perintah free memberikan angka dalam satuan KB. awk berfungsi sebagai kalkulator 
otomatis yang mencari baris Mem, mengambil angka Available, membaginya dengan Total, 
lalu mengubahnya menjadi persentase bulat.
```

2. Kondisi if [ "$AVAIL" -lt "$THRESHOLD" ] bernilai benar jika persentase
memori tersedia di bawah 20.
```bash
Analisis:
Perintah [ "$AVAIL" -lt "$THRESHOLD" ] menggunakan operator -lt (less than atau kurang dari).

Logika: Jika angka hasil perhitungan lebih kecil dari 20, maka script akan menjalankan 
baris perintah di bawah then (mencetak PERINGATAN).
```

3. Ubah THRESHOLD menjadi 90 dan jalankan ulang. Apa yang berubah pada
output? Mengapa demikian?
```bash
Output yang tadinya bertuliskan "Status : Memori tersedia 87 % ( normal )" akan 
berubah menjadi pesan "PERINGATAN : Memori tersedia hanya 87 %!" yang dicetak dengan huruf kapital.
```

# Studi Kasus 10.2 Gagal Akses File
**1. Buat direktori dan file konfigurasi contoh.**
```bash
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory$ mkdir -p ~/praktikum-os/week10-memory/syscall-case
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory$ cd ~/praktikum-os/week10-memory/syscall-case
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory/syscall-case$ echo "PORT=8080" > app.conf
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory/syscall-case$ ls -l app.conf
-rw-rw-r-- 1 Rappizr7 Rappizr7 10 Apr 29 16:05 app.conf
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory/syscall-case$ cat app.conf
PORT=8080
```

**2. Simulasikan permission bermasalah.**
```bash
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory/syscall-case$ chmod 000 app.conf
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory/syscall-case$ cat app.conf
cat: app.conf: Permission denied
```

**3. Kembalikan permission dan verifikasi.**
```bash
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory/syscall-case$ chmod 644 app.conf
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory/syscall-case$ cat app.conf
PORT=8080
```

**4. Analisis**
Analisis:
1. Mengapa cat menghasilkan Permission denied setelah chmod 000? System
call apa yang gagal?
```bash
Analisis:
Ketika menjalankan chmod 000, kamu mencabut semua hak antara pengguna dan file tersebut.

Penyebab: Meskipun kita pemilik file, Kernel Linux sangat patuh pada aturan permission. Karena bit izin baca bernilai 0, Kernel memblokir akses apa pun.

System Call yang Gagal: System call yang gagal adalah open() atau openat().
```

2. Apa perbedaan pesan error Permission denied vs No such file or directory?
Coba rm app.conf lalu cat app.conf untuk melihat perbedaannya.
```bash
Eksperimen:
Jika kamu menjalankan rm app.conf (menghapus file) lalu mencoba cat app.conf, pesannya berubah menjadi No such file or directory.

Perbedaannya:

- Permission Denied: Filenya ada lokasinya BENAR, tapi kamu DILARANG menyentuhnya. Ini adalah masalah Hak Akses.

- No such file or directory: Filenya TIDAK ADA di folder tersebut. System call open() gagal karena jalur path yang dicari tidak ditemukan di indeks disk.
```

3. Permission 644 berarti apa untuk owner, group, dan others?
```bash
Angka 644 adalah kombinasi izin dalam sistem oktal jadi Permission 644 adalah standar paling 
umum untuk file konfigurasi karena menjaga keseimbangan antara keamanan (hanya pemilik yang 
bisa edit) dan fungsionalitas (sistem/aplikasi tetap bisa baca).
```

# Praktikum 10.6 Mengamati System Call dengan strace
**1. Lihat 30 baris pertama system call dari perintah ls.**
```bash
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory/syscall-case$ strace ls 2>&1 | head -n 30
execve("/usr/bin/ls", ["ls"], 0x7ffe63307290 /* 23 vars */) = 0
brk(NULL)                               = 0x56bdc95c7000
mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7555e2828000
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=32555, ...}) = 0
mmap(NULL, 32555, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7555e2820000
close(3)                                = 0
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libselinux.so.1", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\0\0\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=174472, ...}) = 0
mmap(NULL, 181960, PROT_READ, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7555e27f3000
mmap(0x7555e27f9000, 118784, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x6000) = 0x7555e27f9000
mmap(0x7555e2816000, 24576, PROT_READ, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x23000) = 0x7555e2816000
mmap(0x7555e281c000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x29000) = 0x7555e281c000
mmap(0x7555e281e000, 5832, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7555e281e000
close(3)                                = 0
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\220\243\2\0\0\0\0\0"..., 832) = 832
pread64(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784, 64) = 784
fstat(3, {st_mode=S_IFREG|0755, st_size=2125328, ...}) = 0
pread64(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784, 64) = 784
mmap(NULL, 2170256, PROT_READ, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7555e2400000
mmap(0x7555e2428000, 1605632, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x28000) = 0x7555e2428000
mmap(0x7555e25b0000, 323584, PROT_READ, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1b0000) = 0x7555e25b0000
mmap(0x7555e25ff000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1fe000) = 0x7555e25ff000
mmap(0x7555e2605000, 52624, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7555e2605000
close(3)                                = 0
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libpcre2-8.so.0", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\0\0\0\0\0\0\0\0"..., 832) = 832
```

**2. Lihat ringkasan statistik dan bandingkan dua direktori berbeda.**
```bash
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory/syscall-case$ strace -c ls
app.conf
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
 93.22    0.004894        4894         1           execve
  1.71    0.000090           5        18           mmap
  1.45    0.000076          25         3           brk
  0.84    0.000044          22         2           getdents64
  0.63    0.000033           4         7           openat
  0.42    0.000022           4         5           mprotect
  0.32    0.000017           3         5           read
  0.32    0.000017           1         9           close
  0.25    0.000013           1         8           fstat
  0.15    0.000008           4         2         2 statfs
  0.13    0.000007           7         1           munmap
  0.13    0.000007           3         2         2 access
  0.10    0.000005           5         1           write
  0.06    0.000003           1         2           ioctl
  0.06    0.000003           1         2           pread64
  0.06    0.000003           3         1           getrandom
  0.04    0.000002           2         1           arch_prctl
  0.04    0.000002           2         1           prlimit64
  0.04    0.000002           2         1           rseq
  0.02    0.000001           1         1           set_tid_address
  0.02    0.000001           1         1           set_robust_list
------ ----------- ----------- --------- --------- ----------------
100.00    0.005250          70        74         4 total
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory/syscall-case$ strace -c ls /etc 2>&1 | tail -5
  0.65    0.000238         238         1           statx
  0.50    0.000182         182         1           set_robust_list
  0.04    0.000014           7         2           pread64
------ ----------- ----------- --------- --------- ----------------
100.00    0.036486         493        74         5 total
```

**3. Analisa.**
1. Dari output Langkah 1, identifikasi minimal 4 system call berbeda. 
Jelaskan fungsi singkat masing-masing berdasarkan argumen yang terlihat.
```bash
- execve: Digunakan untuk mengeksekusi program. Argumen "/usr/bin/ls" menunjukkan jalur file biner yang dijalankan. Ini adalah titik awal proses.

- openat: Digunakan untuk membuka file. Argumen "/lib/x86_64-linux-gnu/libc.so.6" menunjukkan ls sedang membuka perpustakaan sistem agar fungsi-fungsi dasar bisa berjalan.

- mmap: Digunakan untuk pemetaan memori. Terlihat banyak dipanggil setelah membuka library untuk mengalokasikan ruang di RAM agar isi file library bisa diakses langsung oleh program.

- fstat: Digunakan untuk mengambil status/informasi file. Argumen 3 (file descriptor) merujuk pada file yang baru dibuka untuk mengecek ukurannya sebelum dibaca.
``` 

2. Dari ringkasan strace -c, system call mana yang paling sering dipanggil?
Mengapa?
```bash
Berdasarkan tabel strace -c, system call yang paling sering dipanggil adalah mmap (sebanyak 18 kali).
Karena setiap kali ls memuat library pendukung (libc, libselinux, libpcre2), ia harus memetakan berbagai 
bagian file tersebut ke dalam memori. Proses ini memerlukan beberapa kali panggilan mmap per satu file library.
```

3. Apakah ada system call dengan errors lebih dari 0? Apakah itu berarti
program bermasalah, ataukah bagian normal dari logika program?
```bash
Ya, ada beberapa system call dengan error > 0, yaitu statfs (2 error) dan access (2 error).

Apakah program bermasalah? Tidak, ini adalah bagian normal dari logika program.
Penjelasan: Program sering melakukan cek ombak. Contohnya, ls mencoba mengecek keberadaan file /etc/ld.so.
preload. Karena file itu tidak ada di sistemmu, Kernel memberikan balasan -1 ENOENT. Program sudah 
mengantisipasi ini dan akan lanjut ke langkah berikutnya tanpa crash.
```

4. Apakah jumlah system call berbeda antara ls dan ls /etc? Faktor apa yang
menyebabkan perbedaan tersebut?
```bash
Berdasarkan data tail -5, terlihat ada perbedaan jumlah dan jenis system call.

Faktor Penyebab:
- Volume Data
- Atribut File
- Waktu Eksekusi
```

# 1.6 Tugas Praktikum
# Tugas 10.1 Audit Penggunaan Memori Sistem
**1. Buka file dan isi dengan nano**
```bash
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory$ nano ~/praktikum-os/week10-memory/memory-audit.sh
- isi
#!/bin/bash
set -euo pipefail

# Menentukan nama file laporan
LAPORAN="memory-report.txt"

# Memulai blok penulisan (semua output di dalam {} akan masuk ke file LAPORAN)
{
    echo "=== LAPORAN MEMORI SISTEM ==="
    date
    echo
    echo "--- Ringkasan free -h ---"
    free -h
    echo
    echo "--- /proc/meminfo (20 Baris Pertama) ---"
    cat /proc/meminfo | head -n 20
} > "$LAPORAN"

# Menampilkan konfirmasi dan isi laporan ke terminal
echo "Laporan disimpan ke : $LAPORAN"
echo "--------------------------------------"
cat "$LAPORAN"
```

**2. Jalankan script audit**
```bash
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory$ chmod +x ~/praktikum-os/week10-memory/memory-audit.sh
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory$ cd ~/praktikum-os/week10-memory
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory$ ./memory-audit.sh
Laporan disimpan ke : memory-report.txt
--------------------------------------
=== LAPORAN MEMORI SISTEM ===
Wed Apr 29 04:53:34 PM UTC 2026

--- Ringkasan free -h ---
               total        used        free      shared  buff/cache   available
Mem:           3.9Gi       508Mi       1.8Gi       1.1Mi       1.8Gi       3.4Gi
Swap:          4.5Gi          0B       4.5Gi

--- /proc/meminfo (20 Baris Pertama) ---
MemTotal:        4092200 kB
MemFree:         1937788 kB
MemAvailable:    3571776 kB
Buffers:           11632 kB
Cached:          1747208 kB
SwapCached:            0 kB
Active:           320748 kB
Inactive:        1478596 kB
Active(anon):      50364 kB
Inactive(anon):        0 kB
Active(file):     270384 kB
Inactive(file):  1478596 kB
Unevictable:       27300 kB
Mlocked:           27300 kB
SwapTotal:       4718584 kB
SwapFree:        4718584 kB
Zswap:                 0 kB
Zswapped:              0 kB
Dirty:                 0 kB
Writeback:             0 kB
```

**3. Analisa.**
1. Hitung persentase memori tersedia (available / total × 100%). Apakah
sistem dalam kondisi normal?
```bash
Rumus:
TotalAvailable​×100%

Perhitungan:
3.93.4​×100%≈87.1%

Analisis:
Sistem berada dalam kondisi Sangat Normal. Dengan sisa 87,1%, server punya banyak ruang 
kosong untuk menjalankan beban kerja yang jauh lebih berat.
```

2. Mengapa buff/cache tidak dihitung sebagai memori yang terpakai dari sudut
pandang ketersediaan untuk aplikasi?
```bash
Analisis:
daripada membiarkan RAM kosong, linux menggunakannya untuk menyimpan data file yang sering diakses
agar sistem lebih cepat. Namun data ini bersifat bisa dibuang kapan saja. Jika ada aplikasi yang 
meminta memori, Linux akan langsung melepaskan porsi cache ini dan memberikannya ke aplikasi. 
Itulah sebabnya available dihitung dengan menjumlahkan free + buff/cache yang bisa dilepas.
```

3. Dari /proc/meminfo, apakah SwapTotal lebih besar dari 0? Berapa nilai
SwapFree?
```bash
Data dari Laporanmu:

SwapTotal: 4.718.584 kB (-4.5 GiB)
SwapFree: 4.718.584 kB (-4.5 GiB)

Analisis:
Apakah SwapTotal lebih besar dari 0? Ya benar. Totalnya 4.5 GB. Ini adalah gabungan dari swap asli server
(4 GB) swapfile-week10 (512 MB).
Berapa nilai SwapFree? Nilainya sama persis dengan SwapTotal.
Artinya: swap saat ini adalah 0 byte. Ini menandakan RAM fisik masih sangat lega sehingga 
sistem tidak perlu membuang data ke disk (swap).
```

# Tugas 10.2 Identifikasi Proses dengan Memori Tertinggi
**1. Simpan daftar 10 proses pengguna memori terbesar ke file.**
```bash
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory$ ps aux --sort=-%mem | head -n 10 > top-memory-process.txt
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory$ cat top-memory-process.txt
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root       17674  0.0  1.0 478628 42668 ?        Ssl  13:37   0:03 /usr/libexec/fwupd/fwupd
root       12518  0.1  0.6 223568 27308 ?        SLsl 13:34   0:18 /sbin/multipathd -d -s
root         688  0.0  0.5 109688 23252 ?        Ssl  12:39   0:00 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
root       12526  0.0  0.3  50340 15180 ?        S<s  13:34   0:02 /usr/lib/systemd/systemd-journald
root           1  0.0  0.3  22552 13848 ?        Ss   12:39   0:07 /usr/lib/systemd/systemd --system --deserialize=130 splash noprompt noshell automatic-ubiquity
root       17675  0.0  0.3 468976 13592 ?        Ssl  13:37   0:01 /usr/libexec/udisks2/udisksd
systemd+   12549  0.0  0.3  21584 13320 ?        Ss   13:34   0:00 /usr/lib/systemd/systemd-resolved
root       17608  0.0  0.3 392100 13048 ?        Ssl  13:37   0:00 /usr/sbin/ModemManager
Rappizr7     960  0.0  0.2  20284 11512 ?        Ss   12:40   0:00 /usr/lib/systemd/systemd --user
```

**3. Analisa.**
1. Proses apa di urutan pertama? Catat nilai %MEM dan RSS.
```bash
- Proses: /usr/libexec/fwupd/fwupd (milik user root)
- %MEM: 1.0%
- RSS: 42.668 KB
```

2. Konversikan RSS ke MB (bagi 1024). Apakah wajar?
```bash
Perhitungan: 42668÷1024=41,66 MB
Analisis: Wajar. Penggunaan memori sebesar 41,6 MB untuk sebuah layanan sistem di Ubuntu Server adalah
hal yang normal.
```

3. Jumlahkan %MEM dari 5 proses teratas. Berapa persen RAM yang mereka gunakan bersama?
```bash
- fwupd: 1.0%
- multipathd: 0.6%
- unattended-upgrades: 0.5%
- systemd-journald: 0.3%
- systemd --system: 0.3%
Total: 1.0+0.6+0.5+0.3+0.3=2,7%
```

# Tugas 10.3 Membuat dan Memverifikasi Swap File
**1. Buat dan aktifkan swap file tugas.**
```bash 
Rappizr7@ubuntu-server:~$ sudo fallocate -l 256M /swapfile-tugas-week10
Rappizr7@ubuntu-server:~$ sudo chmod 600 /swapfile-tugas-week10
Rappizr7@ubuntu-server:~$ sudo mkswap /swapfile-tugas-week10
Setting up swapspace version 1, size = 256 MiB (268431360 bytes)
no label, UUID=391c5b3c-ed1d-4b86-aa19-9f1e15ea11da
Rappizr7@ubuntu-server:~$ sudo swapon /swapfile-tugas-week10
```

**2. Verifikasi dan simpan hasil.**
```bash
Rappizr7@ubuntu-server:~$ {
  echo "=== VERIFIKASI SWAP ==="
  swapon --show
  echo
  free -h
} > swap-check.txt
Rappizr7@ubuntu-server:~$ cat swap-check.txt
=== VERIFIKASI SWAP ===
NAME                   TYPE SIZE USED PRIO
/swapfile              file   4G   0B   -2
/swapfile-week10       file 512M   0B   -3
/swapfile-tugas-week10 file 256M   0B   -4

               total        used        free      shared  buff/cache   available
Mem:           3.9Gi       511Mi       1.8Gi       1.1Mi       1.8Gi       3.4Gi
Swap:          4.7Gi          0B       4.7Gi
```

**3. Bersihkan setelah selesai.**
```bash
Rappizr7@ubuntu-server:~$ sudo swapoff /swapfile-tugas-week10 && sudo rm /swapfile-tugas-week10
```

**4. Analisa.**
1. Identifikasi kolom NAME, TYPE, SIZE, dan USED pada output swapon –show.
```bash
- NAME: Menunjukkan lokasi atau nama file swap. Di laporan ada tiga: /swapfile, /swapfile-week10, dan /swapfile-tugas-week10.
- TYPE: Menunjukkan jenis medianya. Di sini tipenya adalah file, yang berarti swap dibuat di dalam partisi hard disk yang sudah ada.
- SIZE: Ukuran kapasitas masing-masing swap. File tugas terlihat tepat berukuran 256M.
- USED: Jumlah swap yang sedang terpakai. Nilainya 0B, artinya RAM fisik masih sangat lega sehingga tidak ada data yang perlu dibuang ke swap.
```

2. Apakah nilai total pada baris Swap di free -h bertambah 256 MB?
```bash
Ya, bertambah. Angka 0.2Gi pada tampilan free -h setara dengan kapasitas 256MB yang ditambahkan
dari file tugas.
```

3. Mengapa permission 600 penting? Apa risiko jika diatur ke 644?
```bash
- Kenapa 600 Penting?: Permission 600 artinya hanya Root yang bisa membaca dan menulis file tersebut.
-Risiko jika 644: Izin 644 membolehkan pengguna lain  untuk membaca isi file tersebut
```

# Tugas 10.4 Analisis System Call dengan strace
**1. Simpan ringkasan dan detail system call.**
```bash
Rappizr7@ubuntu-server:~$ strace -c ls 2> strace-summary.txt
 ,                               backup-success.log    halo.txt              myerror.txt        strace-summary.txt
 -                               blacklist-loop.conf   hasil-pencarian.txt   os                 swap-check.txt
'}'                              bye.txt               large-logs.txt        praktikum          week09
 backup_20260228_172946.tar.gz   config-files.txt      logs                  praktikum-os       z
 backup_20260228_173505.tar.gz   data                  monitoring_log.txt    scripts
 backup-error.log                error.log             monitor.sh            server.log
 backup_script.sh                error.txt             mydir                 sorted-users.txt
Rappizr7@ubuntu-server:~$ strace ls /etc 2> strace-ls-etc.txt
adduser.conf            dhcpcd.conf      landscape       multipath.conf       rc3.d              sysctl.d
alternatives            dpkg             ldap            nanorc               rc4.d              sysstat
apparmor                e2scrub.conf     ld.so.cache     needrestart          rc5.d              systemd
apparmor.d              environment      ld.so.conf      netconfig            rc6.d              terminfo
apport                  ethertypes       ld.so.conf.d    netplan              rcS.d              thermald
apt                     fonts            legal           network              resolv.conf        timezone
bash.bashrc             fstab            libaudit.conf   networkd-dispatcher  rmt                tmpfiles.d
bash_completion         fuse.conf        libblockdev     networks             rpc                ubuntu-advantage
bash_completion.d       fwupd            libibverbs.d    newt                 rsyslog.conf       ucf.conf
bindresvport.blacklist  gai.conf         libnl-3         nftables.conf        rsyslog.d          udev
binfmt.d                ghostscript      libpaper.d      nsswitch.conf        screenrc           udisks2
byobu                   gnutls           locale.alias    opt                  security           ufw
ca-certificates         groff            locale.conf     os-release           selinux            updatedb.conf
ca-certificates.conf    group            locale.gen      overlayroot.conf     sensors3.conf      update-manager
cloud                   group-           localtime       PackageKit           sensors.d          update-motd.d
console-setup           grub.d           logcheck        pam.conf             services           update-notifier
credstore               gshadow          login.defs      pam.d                sgml               UPower
credstore.encrypted     gshadow-         logrotate.conf  papersize            shadow             usb_modeswitch.conf
cron.d                  gss              logrotate.d     passwd               shadow-            usb_modeswitch.d
cron.daily              hdparm.conf      lsb-release     passwd-              shells             vconsole.conf
cron.hourly             host.conf        lvm             perl                 skel               vim
cron.monthly            hostname         machine-id      pki                  sos                vmware-tools
crontab                 hosts            magic           plymouth             ssh                vtrgb
cron.weekly             hosts.allow      magic.mime      pm                   ssl                w3m
cron.yearly             hosts.deny       manpath.config  polkit-1             subgid             wgetrc
cryptsetup-initramfs    ImageMagick-6    mdadm           pollinate            subgid-            X11
crypttab                init.d           mime.types      profile              subuid             xattr.conf
dbus-1                  initramfs-tools  mke2fs.conf     profile.d            subuid-            xdg
debconf.conf            inputrc          ModemManager    protocols            sudo.conf          xml
debian_version          iproute2         modprobe.d      python3              sudoers            zsh_command_not_found
default                 iscsi            modules         python3.12           sudoers.d
deluser.conf            issue            modules-load.d  rc0.d                sudo_logsrvd.conf
depmod.d                issue.net        mtab            rc1.d                supercat
dhcp                    kernel           multipath       rc2.d                sysctl.conf
Rappizr7@ubuntu-server:~$ cat strace-summary.txt
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
 35.55    0.002181         121        18           mmap
 11.26    0.000691          76         9           close
  7.53    0.000462          66         7           openat
  6.85    0.000420          52         8           fstat
  6.72    0.000412          58         7           write
  4.92    0.000302          60         5           read
  3.81    0.000234          46         5           mprotect
  3.77    0.000231         115         2           pread64
  3.60    0.000221         221         1           execve
  2.59    0.000159          53         3           brk
  2.23    0.000137          68         2           ioctl
  1.89    0.000116          58         2         2 statfs
  1.68    0.000103         103         1           arch_prctl
  1.37    0.000084          42         2           getdents64
  1.34    0.000082          82         1           getrandom
  1.19    0.000073          73         1           set_tid_address
  1.12    0.000069          69         1           prlimit64
  0.93    0.000057          28         2         2 access
  0.85    0.000052          52         1           set_robust_list
  0.72    0.000044          44         1           munmap
  0.08    0.000005           5         1           rseq
------ ----------- ----------- --------- --------- ----------------
100.00    0.006135          76        80         4 total
```

**2. Analisa.**
1. Sebutkan minimal 5 system call dari strace-summary.txt beserta fungsi
singkatnya.
```bash
- mmap: Digunakan untuk memetakan file atau perangkat ke dalam memori.
- openat: Berfungsi untuk membuka file atau direktori. 
- write: Digunakan untuk menulis data ke file descriptor.
- getdents64: Singkatan dari Get Directory Entries.
- execve: System call pertama yang dipanggil untuk menjalankan program ls.
```

2. System call mana yang paling sering dipanggil? Mengapa?
```bash
System call yang paling sering dipanggil adalah mmap (sebanyak 18 kali).
Mengapa? karna bergantung pada banyak pustaka sistem (libraries). Setiap kali ls membutuhkan pustaka 
seperti pustaka keamanan (selinux) atau pustaka standar C, Kernel harus memanggil mmap berkali-kali untuk 
mengalokasikan ruang memori bagi bagian-bagian berbeda dari pustaka tersebut.
```

3. Apakah ada errors lebih dari 0? Apakah program tetap berjalan normal
meskipun ada kegagalan tersebut?
```bash
Ya, terdapat 4 error secara total yaitu:
- statfs: 2 errors.
- access: 2 errors.

Apakah program tetap berjalan normal? Ya, program tetap berjalan normal. Error dalam strace tidak
selalu berarti aplikasi rusak.
```

# Tugas 10.5 Studi Kasus Diagnosa Server Lambat
**1. Buka file dan isi dengan nano.**
```bash
Rappizr7@ubuntu-server:~$ nano ~/praktikum-os/week10-memory/diagnosa-server.sh
- isi
#!/bin/bash
set -euo pipefail

LAPORAN="diagnosa-server-lambat.txt"
WARN_MEM=false
WARN_SWAP=0

cek_memori() {
    echo "--- Kondisi Memori ---"
    free -h
    echo
    AVAIL_PCT=$(free | awk '/Mem/ {printf "%d", $7/$2*100}')
    if [ "$AVAIL_PCT" -lt 20 ]; then
        echo "PERINGATAN: Memori tersedia hanya ${AVAIL_PCT}%"
        WARN_MEM=true
    fi
}

cek_swap() {
    echo "--- Penggunaan Swap ---"
    swapon --show 2>/dev/null || echo "Tidak ada swap aktif"
    echo
    WARN_SWAP=$(free | awk '/Swap/ {print $3}')
    if [ "$WARN_SWAP" -gt 0 ]; then
        echo "INFO: Swap digunakan (${WARN_SWAP} kB)"
    fi
}

cek_proses() {
    echo "--- 10 Proses Memori Tertinggi ---"
    ps aux --sort=-%mem | head -n 11
    echo
}

cek_paging() {
    echo "--- Aktivitas Paging (5 sampel) ---"
    vmstat 1 5
    echo
}

ringkasan() {
    echo "=== RINGKASAN ==="
    if [ "$WARN_MEM" = true ]; then
        echo "- Memori: KRITIS - perlu tindakan segera"
    else
        echo "- Memori: normal"
    fi

    if [ "$WARN_SWAP" -gt 0 ]; then
        echo "- Swap: aktif - pantau aktivitas paging"
    else
        echo "- Swap: tidak digunakan"
    fi
}

# Eksekusi dan simpan ke file menggunakan tee
{
    echo "=== LAPORAN DIAGNOSA SERVER ==="
    date
    echo
    cek_memori
    cek_swap
    cek_proses
    cek_paging
    ringkasan
} | tee "$LAPORAN"

echo
echo "Laporan disimpan ke : $LAPORAN"
```

**2. jalankan script diagnosa.**
```bash
Rappizr7@ubuntu-server:~$ chmod +x ~/praktikum-os/week10-memory/diagnosa-server.sh
Rappizr7@ubuntu-server:~$ cd ~/praktikum-os/week10-memory
Rappizr7@ubuntu-server:~/praktikum-os/week10-memory$ ./diagnosa-server.sh
=== LAPORAN DIAGNOSA SERVER ===
Wed Apr 29 06:24:33 PM UTC 2026

--- Kondisi Memori ---
               total        used        free      shared  buff/cache   available
Mem:           3.9Gi       511Mi       1.8Gi       1.1Mi       1.8Gi       3.4Gi
Swap:          4.5Gi          0B       4.5Gi

--- Penggunaan Swap ---
NAME             TYPE SIZE USED PRIO
/swapfile        file   4G   0B   -2
/swapfile-week10 file 512M   0B   -3

--- 10 Proses Memori Tertinggi ---
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root       17674  0.0  1.0 478628 42668 ?        Ssl  13:37   0:03 /usr/libexec/fwupd/fwupd
root       12518  0.1  0.6 223568 27308 ?        SLsl 13:34   0:21 /sbin/multipathd -d -s
root         688  0.0  0.5 109688 23252 ?        Ssl  12:39   0:00 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
root       12526  0.0  0.3  50340 15852 ?        S<s  13:34   0:02 /usr/lib/systemd/systemd-journald
root           1  0.0  0.3  22552 13848 ?        Ss   12:39   0:08 /usr/lib/systemd/systemd --system --deserialize=130 splash noprompt noshell automatic-ubiquity
root       17675  0.0  0.3 468976 13592 ?        Ssl  13:37   0:01 /usr/libexec/udisks2/udisksd
systemd+   12549  0.0  0.3  21584 13320 ?        Ss   13:34   0:00 /usr/lib/systemd/systemd-resolved
root       17608  0.0  0.3 392100 13048 ?        Ssl  13:37   0:00 /usr/sbin/ModemManager
Rappizr7     960  0.0  0.2  20284 11512 ?        Ss   12:40   0:00 /usr/lib/systemd/systemd --user
root        7915  0.0  0.2  14972 10688 ?        Ss   13:32   0:00 sshd: Rappizr7 [priv]

--- Aktivitas Paging (5 sampel) ---
procs -----------memory---------- ---swap-- -----io---- -system-- -------cpu-------
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st gu
 0  0      0 1932888  13044 1919960    0    0    73   122  189    0  0  1 99  0  0  0
 2  0      0 1932888  13044 1919960    0    0     0     4  970  322  0  0 100  0  0  0
 2  0      0 1932888  13048 1920092    0    0     0     0  554  146  0  0 100  0  0  0
 0  0      0 1932888  13052 1920096    0    0     0    40  236   80  0  0 100  0  0  0
 1  0      0 1932888  13052 1920096    0    0     0     0  162   52  0  0 100  0  0  0

=== RINGKASAN ===
- Memori: normal
- Swap: tidak digunakan

Laporan disimpan ke : diagnosa-server-lambat.txt
```

**3. Analisa.**
1. Jelaskan peran masing-masing fungsi: cek_memori, cek_swap, cek_proses,
cek_paging, dan ringkasan. Mengapa diagnosa dipecah menjadi fungsi
terpisah?
```bash
Berikut adalah peran teknis dari fungsi-fungsi yang ada dalam script:

-cek_memori: Mengukur kesehatan RAM fisik. Ia menghitung persentase ketersediaan RAM agar kita tahu
apakah kapasitas memori masih cukup untuk menjalankan proses baru.
-cek_swap: Mengidentifikasi apakah sistem sudah mulai kelelahan dan terpaksa menggunakan disk 
sebagai memori cadangan.
- cek_proses: Memberikan daftar utamaa (10 besar) yang paling banyak mengonsumsi memori 
jika terjadi lonjakan penggunaan.
- cek_paging: Memantau pergerakan data antara RAM dan Swap secara real-time. 
- ringkasan: Mengolah semua data mentah di atas menjadi kesimpulan sederhana,
agar admin tidak perlu membaca seluruh log jika hanya ingin cek cepat.

Mengapa dipecah menjadi fungsi terpisah?

- Modularitas: Kode menjadi lebih rapi dan terstruktur.
- Reusability: Jika suatu saat kamu hanya butuh cek memori saja, kita tinggal memanggil fungsi 
cek_memori tanpa menjalankan seluruh script.
- Troubleshooting: Mempermudah pencarian kesalahan jika salah satu bagian script error.
```

2. Berdasarkan bagian RINGKASAN, apakah kondisi sistem normal atau kritis?
Jelaskan berdasarkan nilai threshold yang digunakan script.
```bash
Status: Normal
Penjelasan:Script menggunakan nilai ambang batas (threshold) sebesar 20%. Berdasarkan output 
cek_memori, memori yang tersedia (available) adalah 3.4 GiB dari total 3.9 GiB. Jika dihitung: 
(3.4/3.9)×100%=87,1%
Karena 87,1% > 20%, maka kondisi dianggap aman. Selain itu, nilai WARN_SWAP adalah 0, yang 
memperkuat kesimpulan bahwa RAM fisik masih sangat lega sehingga tidak perlu membuang data ke swap.
```

3. Mengapa script menggunakan tee "$LAPORAN" bukan redirection biasa >
"$LAPORAN"? Apa keuntungannya?
```bash
- Redirection Biasa (> atau >>): Hanya mengirim output ke dalam file.
- tee: Mengirim output ke layar terminal DAN file laporan secara bersamaan.

Keuntungan: Administrator bisa langsung memantau proses diagnosa yang sedang berjalan  
sambil tetap memiliki arsip permanen di file .txt untuk bukti laporan.
```

4. Dari output cek_paging, apakah ada aktivitas si atau so? Jika ada, apa
implikasinya terhadap performa server?
```bash
Berdasarkan data vmstat pada output:
- si (Swap-In): 0
- so (Swap-Out): 0

Implikasi terhadap Performa:
Tidak adanya aktivitas si dan so menunjukkan bahwa performa server sangat optimal.
Jika so tinggi: Kernel sedang sibuk memindahkan data dari RAM ke disk (proses melambat).
Jika si tinggi: Kernel sedang mengambil kembali data dari disk ke RAM (proses sangat lambat/lag).
Karena nilainya nol, artinya server tidak mengalami thrashing,  sehingga kecepatan akses data 
tetap berada pada kecepatan penuh RAM fisik, bukan kecepatan disk 
yang jauh lebih lambat.
```