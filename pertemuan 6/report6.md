# Laporan Pertemuan ke-4 Sistem Operasi

**Tanggal:** 02 April 2026  
**Disusun Oleh:** Mukhammad Raffi Zabra  
**NIM:** 254107020059  
**Kelas/No:** TI-1G/24  

---

## Tugas Pendahuluan

## Latihan 6.1
Jalankan ps aux dan amati outputnya:

1. Berapa total proses yang berjalan? Proses apa yang memiliki PID
terkecil?
- root@ubuntu-server:/home/Rappizr7# ps aux | wc -l
161 (total proses yang berjalan)

- root@ubuntu-server:/home/Rappizr7# ps aux | sort -nk2 | head
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.3  22236 13404 ?        Ss   Apr01   0:12 /sbin/init splash noprompt noshell automatic-ubiquity
root           2  0.0  0.0      0     0 ?        S    Apr01   0:00 [kthreadd]
root           3  0.0  0.0      0     0 ?        S    Apr01   0:00 [pool_workqueue_release]
root           4  0.0  0.0      0     0 ?        I<   Apr01   0:00 [kworker/R-rcu_g]
root           5  0.0  0.0      0     0 ?        I<   Apr01   0:00 [kworker/R-rcu_p]
root           6  0.0  0.0      0     0 ?        I<   Apr01   0:00 [kworker/R-slub_]
root           7  0.0  0.0      0     0 ?        I<   Apr01   0:00 [kworker/R-netns]
root          12  0.0  0.0      0     0 ?        I<   Apr01   0:00 [kworker/R-mm_pe]
root          13  0.0  0.0      0     0 ?        I    Apr01   0:00 [rcu_tasks_kthread]
(proses pid terkecil)

2. Jalankan pstree -p dan temukan proses bash Anda. Proses apa yang
menjadi induk (PPID) dari bash tersebut?
- root@ubuntu-server:/home/Rappizr7# pstree -p
systemd(1)─┬─ModemManager(760)─┬─{ModemManager}(767)
           │                   ├─{ModemManager}(772)
           │                   └─{ModemManager}(774)
           ├─cron(846)
           ├─dbus-daemon(664)
           ├─login(856)───bash(977)───sudo(1020)───sudo(1022)───su(1023)───bash(1024)───sudo(7484)───+
           ├─multipathd(10628)─┬─{multipathd}(10632)
           │                   ├─{multipathd}(10633)
           │                   ├─{multipathd}(10634)
           │                   ├─{multipathd}(10635)
           │                   ├─{multipathd}(10636)
           │                   └─{multipathd}(10637)
           ├─polkitd(687)─┬─{polkitd}(732)
           │              ├─{polkitd}(736)
           │              └─{polkitd}(738)
           ├─rsyslogd(751)─┬─{rsyslogd}(769)
           │               ├─{rsyslogd}(770)
           │               └─{rsyslogd}(771)
           ├─sshd(1046)─┬─sshd(9748)───sshd(9828)───bash(9829)───sudo(9839)───sudo(9840)───su(9841)──+++
           │            └─sshd(15358)───sshd(15441)───bash(15442)───sudo(15492)───sudo(15493)───su(15+
           ├─systemd(965)───(sd-pam)(966)
           ├─systemd-journal(341)
           ├─systemd-logind(703)
           ├─systemd-network(462)
           ├─systemd-resolve(481)
           ├─systemd-timesyn(490)───{systemd-timesyn}(561)
           ├─systemd-udevd(411)
           ├─udisksd(10170)─┬─{udisksd}(10172)
           │                ├─{udisksd}(10173)
           │                ├─{udisksd}(10175)
           │                ├─{udisksd}(10176)
           │                └─{udisksd}(10177)
           ├─unattended-upgr(718)───{unattended-upgr}(810)
           └─upowerd(1621)─┬─{upowerd}(1623)
                           ├─{upowerd}(1624)
                           └─{upowerd}(1625)

- login(856)───bash(977) -> induk ppid

3. Bandingkan output ps aux dan ps aux -L. Apa perbedaan yang Anda
lihat?
- ps aux menampilkan 1 baris = 1 proses, contoh root 760 ... /usr/sbin/ModemManager
- ps aux -L menampilkan thread di dalam proses 1 proses memiliki banyak baris, contoh root 760 760 ...
root 760 767 ...
root 760 772 ...
root 760 774 ...


Latihan 6.2
1. Jalankan sleep 120 & dan amati kolom STAT pada ps aux. Kondisi
apa yang ditampilkan? Mengapa proses sleep berada di kondisi tersebut?
- root@ubuntu-server:/home/Rappizr7# sleep 120 & ps aux | grep sleep
[2] 15561
root       15554  0.0  0.0   5684  2108 pts/5    T    05:29   0:00 sleep 120
root       15561 60.0  0.0   5684  2108 pts/5    S    05:30   0:00 sleep 120
root       15563  100  0.0   6544  2328 pts/5    S+   05:30   0:00 grep --color=auto sleep
- Pada kolom STAT terdapat kondisi S (sleeping) dan T (stopped).
Proses sleep berada pada kondisi S karena hanya menunggu waktu tanpa menggunakan CPU.
Sedangkan status T menunjukkan proses yang sedang dihentikan (paused).

2. Jalankan beberapa perintah yang berhasil dan yang gagal, lalu catat exit
code masing-masing. Pola apa yang Anda temukan?
- root@ubuntu-server:/home/Rappizr7# ls
backup_20260228_172946.tar.gz  blacklist-loop.conf  halo.txt             mydir
backup_20260228_173505.tar.gz  bye.txt              hasil-pencarian.txt  myerror.txt
backup-error.log               config-files.txt     large-logs.txt       server.log
backup_script.sh               error.log            monitoring_log.txt   sorted-users.txt
backup-success.log             error.txt            monitor.sh           z
root@ubuntu-server:/home/Rappizr7# echo $?
0
root@ubuntu-server:/home/Rappizr7# ls file_tidak_ada
ls: cannot access 'file_tidak_ada': No such file or directory
root@ubuntu-server:/home/Rappizr7# echo $?
2
- Dari percobaan yang dilakukan, perintah yang berhasil menghasilkan exit code 0, sedangkan perintah yang gagal menghasilkan exit code 2.
Pola yang ditemukan adalah:
0 menandakan perintah berhasil (sukses)
selain 0 menandakan perintah gagal (error)


Latihan 6.3
1. Jalankan nice -n 5 sleep 200 & dan verifikasi nilai NI-nya dengan
ps.
- root@ubuntu-server:/home/Rappizr7# nice -n 5 sleep 200 &
ps aux | grep sleep
[2] 16066
root       15554  0.0  0.0   5684  2108 pts/5    T    05:29   0:00 sleep 120
root       16066  129  0.0   5684  2108 pts/5    SN   06:02   0:00 sleep 200
root       16068 60.0  0.0   6544  2328 pts/5    S+   06:02   0:00 grep --color=auto sleep

- Proses sleep memiliki nilai NI = 5, sesuai dengan perintah nice -n 5

2. Ubah nilai nice menjadi 10 menggunakan renice, lalu verifikasi kembali.
- root@ubuntu-server:/home/Rappizr7# renice 10 -p 15554
15554 (process ID) old priority 10, new priority 10
root@ubuntu-server:/home/Rappizr7# ps aux | grep sleep
root       15554  0.0  0.0   5684  2108 pts/5    TN   05:29   0:00 sleep 120
root       16141  112  0.0   6544  2328 pts/5    S+   06:29   0:00 grep --color=auto sleep

3. Coba ubah nilai nice menjadi -5 tanpa sudo. Apa yang terjadi? Mengapa
Linux membatasi hal ini untuk user biasa?
- Rappizr7@ubuntu-server:~$ renice -5 -p 15554
renice: failed to get priority for 15554 (process ID): No such process
alasan : karna linux membatasi peningkatan prioritas proses agar tidak mengganggu ke stabilan sistem.


Latihan 6.4
1. Jalankan sleep 400 &, kirim SIGSTOP, dan amati perubahan kolom
STAT. Kondisi apa yang muncul?
- root@ubuntu-server:/home/Rappizr7# sleep 400 &
[1] 16248
root@ubuntu-server:/home/Rappizr7# kill -STOP 16248

[1]+  Stopped                 sleep 400
root@ubuntu-server:/home/Rappizr7# ps aux | grep sleep
root       16248  0.0  0.0   5684  2092 pts/5    T    07:04   0:00 sleep 400
root       16251 20.0  0.0   6544  2328 pts/5    S+   07:04   0:00 grep --color=auto sleep
root@ubuntu-server:/home/Rappizr7# sleep 400 &
[2] 16267
root@ubuntu-server:/home/Rappizr7# ps aux | grep sleep
root       16248  0.0  0.0   5684  2092 pts/5    T    07:04   0:00 sleep 400
root       16267  0.2  0.0   5684  2104 pts/5    S    07:05   0:00 sleep 400
root       16269 50.0  0.0   6544  2320 pts/5    S+   07:05   0:00 grep --color=auto sleep
root@ubuntu-server:/home/Rappizr7#

- kalau sebelum sigstop kode akan s, setelah sigstop kode jadi t

2. Kirim SIGCONT dan verifikasi proses kembali berjalan.
- root@ubuntu-server:/home/Rappizr7# ps aux | grep sleep
root       16248  0.0  0.0   5684  2092 pts/5    T    07:04   0:00 sleep 400
root       16267  0.2  0.0   5684  2104 pts/5    S    07:05   0:00 sleep 400
root       16269 50.0  0.0   6544  2320 pts/5    S+   07:05   0:00 grep --color=auto sleep
root@ubuntu-server:/home/Rappizr7# kill -CONT 16248
root@ubuntu-server:/home/Rappizr7# ps aux | grep sleep
root       16248  0.0  0.0   5684  2092 pts/5    S    07:04   0:00 sleep 400
root       16267  0.0  0.0   5684  2104 pts/5    S    07:05   0:00 sleep 400
root       16273 50.0  0.0   6544  2328 pts/5    S+   07:07   0:00 grep --color=auto sleep

3. Hentikan proses dengan SIGTERM lalu verifikasi sudah tidak ada. Kapan
Anda memilih SIGKILL daripada SIGTERM?
- root@ubuntu-server:/home/Rappizr7# ps aux | grep sleep
root       16248  0.0  0.0   5684  2092 pts/5    S    07:04   0:00 sleep 400
root       16267  0.0  0.0   5684  2104 pts/5    S    07:05   0:00 sleep 400
root       16273 50.0  0.0   6544  2328 pts/5    S+   07:07   0:00 grep --color=auto sleep
root@ubuntu-server:/home/Rappizr7# kill -TERM 16248
root@ubuntu-server:/home/Rappizr7# ps aux | grep sleep
root       16267  0.0  0.0   5684  2104 pts/5    S    07:05   0:00 sleep 400
root       16275 50.0  0.0   6544  2328 pts/5    S+   07:08   0:00 grep --color=auto sleep
[1]-  Terminated              sleep 400
- Gunakan SIGTERM dulu. Kalau prosesnya tidak mau mati setelah ditunggu beberapa saat, barulah keluarkan gunakan SIGKILL


Latihan 6.5
1. Jalankan top di foreground. Apa yang terjadi di terminal?
- root@ubuntu-server:/home/Rappizr7# top
top - 07:11:57 up  8:38,  2 users,  load average: 0.19, 0.08, 0.02
Tasks: 157 total,   1 running, 156 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.3 sy,  0.0 ni, 99.1 id,  0.1 wa,  0.0 hi,  0.5 si,  0.0 st
MiB Mem :   3996.3 total,   2105.0 free,    555.8 used,   1629.8 buff/cache
MiB Swap:   4096.0 total,   4096.0 free,      0.0 used.   3440.5 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
  16056 _apt      20   0   22912  11028   9892 S   1.4   0.3   1:23.24 http
  16231 root      20   0       0      0      0 I   0.9   0.0   0:04.11 kworker/2:2-events
  16234 root      20   0   16768   7424   6180 S   0.6   0.2   0:00.21 sudo
  16283 root      20   0   11972   6016   3748 R   0.6   0.1   0:00.30 top
     16 root      20   0       0      0      0 S   0.3   0.0   1:52.86 ksoftirqd/0
    276 root      20   0       0      0      0 S   0.3   0.0   0:08.09 jbd2/sda2-8
  15441 Rappizr7  20   0   15100   7212   5240 S   0.3   0.2   0:31.84 sshd
  16200 root      20   0       0      0      0 I   0.3   0.0   0:00.54 kworker/u10:1-writeback
  16227 root      20   0       0      0      0 I   0.3   0.0   0:03.44 kworker/0:2-events
      1 root      20   0   22236  13408   9404 S   0.0   0.3   0:18.09 systemd
      2 root      20   0       0      0      0 S   0.0   0.0   0:01.02 kthreadd
      3 root      20   0       0      0      0 S   0.0   0.0   0:00.00 pool_workqueue_release
      4 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-rcu_g
      5 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-rcu_p
      6 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-slub_
      7 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-netns
     12 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-mm_pe
     13 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_kthread
     14 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_rude_kthread
     15 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_trace_kthread
     17 root      20   0       0      0      0 I   0.0   0.0   0:21.15 rcu_preempt
     18 root      rt   0       0      0      0 S   0.0   0.0   0:00.71 migration/0
     19 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/0
     20 root      20   0       0      0      0 S   0.0   0.0   0:00.02 cpuhp/0
     21 root      20   0       0      0      0 S   0.0   0.0   0:00.03 cpuhp/1
     22 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/1
     23 root      rt   0       0      0      0 S   0.0   0.0   0:04.34 migration/1
     24 root      20   0       0      0      0 S   0.0   0.0   0:02.86 ksoftirqd/1
     27 root      20   0       0      0      0 S   0.0   0.0   0:00.01 cpuhp/2
     28 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/2
     29 root      rt   0       0      0      0 S   0.0   0.0   0:04.23 migration/2
     30 root      20   0       0      0      0 S   0.0   0.0   0:00.83 ksoftirqd/2
     33 root      20   0       0      0      0 S   0.0   0.0   0:00.02 cpuhp/3
     34 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/3
     35 root      rt   0       0      0      0 S   0.0   0.0   0:01.33 migration/3
     36 root      20   0       0      0      0 S   0.0   0.0   0:00.88 ksoftirqd/3
     43 root      20   0       0      0      0 S   0.0   0.0   0:00.21 kdevtmpfs
     44 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-inet_
     45 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kauditd
     46 root      20   0       0      0      0 S   0.0   0.0   0:00.07 khungtaskd
     47 root      20   0       0      0      0 S   0.0   0.0   0:00.00 oom_reaper
     50 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-write
     51 root      20   0       0      0      0 S   0.0   0.0   0:03.14 kcompactd0
     52 root      25   5       0      0      0 S   0.0   0.0   0:00.00 ksmd
     54 root      39  19       0      0      0 S   0.0   0.0   0:00.00 khugepaged
     55 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-kinte
     56 root       0 -20       0      0      0 I   0.0   0.0   0:00.01 kworker/R-kbloc
     57 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-blkcg
     58 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 irq/9-acpi
     61 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-tpm_d
     62 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-ata_s
     63 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-md
     64 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-md_bi
- yang terjadi adalah memperbarui daftar proses secara real time

2. Tekan Ctrl+Z dan cek statusnya dengan jobs. Kondisi apa yang
ditampilkan?
- root@ubuntu-server:/home/Rappizr7# top
top - 07:13:03 up  8:39,  2 users,  load average: 0.22, 0.11, 0.03
Tasks: 155 total,   1 running, 154 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  8.9 sy,  0.0 ni, 84.4 id,  0.0 wa,  0.0 hi,  6.7 si,  0.0 st
MiB Mem :   3996.3 total,   2102.5 free,    556.2 used,   1632.0 buff/cache
MiB Swap:   4096.0 total,   4096.0 free,      0.0 used.   3440.1 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
  15441 Rappizr7  20   0   15100   7212   5240 S   3.6   0.2   0:31.99 sshd
  16285 root      20   0   11972   5764   3500 R   3.6   0.1   0:00.24 top
      1 root      20   0   22236  13408   9404 S   0.0   0.3   0:18.15 systemd
      2 root      20   0       0      0      0 S   0.0   0.0   0:01.02 kthreadd
      3 root      20   0       0      0      0 S   0.0   0.0   0:00.00 pool_workqueue_release
      4 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-rcu_g
      5 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-rcu_p
      6 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-slub_
      7 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-netns
     12 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-mm_pe
     13 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_kthread
     14 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_rude_kthread
     15 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_trace_kthread
     16 root      20   0       0      0      0 S   0.0   0.0   1:53.45 ksoftirqd/0
     17 root      20   0       0      0      0 I   0.0   0.0   0:21.18 rcu_preempt
     18 root      rt   0       0      0      0 S   0.0   0.0   0:00.71 migration/0
     19 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/0
     20 root      20   0       0      0      0 S   0.0   0.0   0:00.02 cpuhp/0
     21 root      20   0       0      0      0 S   0.0   0.0   0:00.03 cpuhp/1
     22 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/1
     23 root      rt   0       0      0      0 S   0.0   0.0   0:04.34 migration/1
     24 root      20   0       0      0      0 S   0.0   0.0   0:02.86 ksoftirqd/1
     27 root      20   0       0      0      0 S   0.0   0.0   0:00.01 cpuhp/2
     28 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/2
     29 root      rt   0       0      0      0 S   0.0   0.0   0:04.23 migration/2
     30 root      20   0       0      0      0 S   0.0   0.0   0:00.83 ksoftirqd/2
     33 root      20   0       0      0      0 S   0.0   0.0   0:00.02 cpuhp/3
     34 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/3
     35 root      rt   0       0      0      0 S   0.0   0.0   0:01.33 migration/3
     36 root      20   0       0      0      0 S   0.0   0.0   0:00.88 ksoftirqd/3
     43 root      20   0       0      0      0 S   0.0   0.0   0:00.21 kdevtmpfs
     44 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-inet_
     45 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kauditd
     46 root      20   0       0      0      0 S   0.0   0.0   0:00.07 khungtaskd
     47 root      20   0       0      0      0 S   0.0   0.0   0:00.00 oom_reaper
     50 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-write
     51 root      20   0       0      0      0 S   0.0   0.0   0:03.15 kcompactd0
     52 root      25   5       0      0      0 S   0.0   0.0   0:00.00 ksmd
     54 root      39  19       0      0      0 S   0.0   0.0   0:00.00 khugepaged
     55 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-kinte
     56 root       0 -20       0      0      0 I   0.0   0.0   0:00.01 kworker/R-kbloc
     57 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-blkcg
     58 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 irq/9-acpi
     61 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-tpm_d
     62 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-ata_s
     63 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-md
     64 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-md_bi
     65 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-edac-
     66 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-devfr
     67 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 watchdogd
     70 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kswapd0
     71 root      20   0       0      0      0 S   0.0   0.0   0:00.00 ecryptfs-kthread
[2]   Done                    sleep 400      0 I   0.0   0.0   0:00.00 kworker/R-kthro

[3]+  Stopped                 top
root@ubuntu-server:/home/Rappizr7# jobs
[3]+  Stopped                 top
- Statusnya adalah Stopped (Berhenti). Menekan Ctrl+Z mengirimkan sinyal SIGTSTP yang membekukan proses namun tetap menyimpannya di memori

3. Pindahkan ke background dengan bg. Apakah top dapat berjalan dengan
baik di background? Mengapa?
- root@ubuntu-server:/home/Rappizr7# jobs
[3]+  Stopped                 top
root@ubuntu-server:/home/Rappizr7# bg %3
[3]+ top &

[3]+  Stopped                 top
- tidak, karna top itu aplikasi yang interaktif yang memerlukan akses penuh ke terminal untuk menampilkan output

4. Kembalikan ke foreground dengan fg, lalu keluar dengan q .
- root@ubuntu-server:/home/Rappizr7# top
top - 07:13:03 up  8:39,  2 users,  load average: 0.22, 0.11, 0.03
Tasks: 155 total,   1 running, 154 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  8.9 sy,  0.0 ni, 84.4 id,  0.0 wa,  0.0 hi,  6.7 si,  0.0 st
MiB Mem :   3996.3 total,   2102.5 free,    556.2 used,   1632.0 buff/cache
MiB Swap:   4096.0 total,   4096.0 free,      0.0 used.   3440.1 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
  15441 Rappizr7  20   0   15100   7212   5240 S   3.6   0.2   0:31.99 sshd
  16285 root      20   0   11972   5764   3500 R   3.6   0.1   0:00.24 top
      1 root      20   0   22236  13408   9404 S   0.0   0.3   0:18.15 systemd
      2 root      20   0       0      0      0 S   0.0   0.0   0:01.02 kthreadd
top - 07:16:25 up  8:42,  2 users,  load average: 0.03, 0.08, 0.03
Tasks: 157 total,   1 running, 156 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.1 us,  0.5 sy,  0.0 ni, 97.2 id,  0.6 wa,  0.0 hi,  1.6 si,  0.0 st
MiB Mem :   3996.3 total,   2093.0 free,    558.7 used,   1639.0 buff/cache
MiB Swap:   4096.0 total,   4096.0 free,      0.0 used.   3437.6 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
     16 root      20   0       0      0      0 S   3.3   0.0   1:57.64 ksoftirqd/0
  16056 _apt      20   0   22912  11028   9892 S   2.0   0.3   1:28.35 http
  16284 root      20   0       0      0      0 I   1.3   0.0   0:00.67 kworker/1:2-events
  16285 root      20   0   11972   6024   3756 R   1.0   0.1   0:00.39 top
    341 root      19  -1   66976  19812  18552 S   0.7   0.5   0:05.92 systemd-journal
  16231 root      20   0       0      0      0 I   0.7   0.0   0:05.66 kworker/2:2-mm_percpu_wq
    276 root      20   0       0      0      0 S   0.3   0.0   0:08.29 jbd2/sda2-8
  16223 root      20   0       0      0      0 I   0.3   0.0   0:00.27 kworker/u10:2-events_power_ef+
  16266 root       0 -20       0      0      0 I   0.3   0.0   0:00.10 kworker/0:0H-kblockd
      1 root      20   0   22236  13408   9404 S   0.0   0.3   0:18.32 systemd
      2 root      20   0       0      0      0 S   0.0   0.0   0:01.03 kthreadd
      3 root      20   0       0      0      0 S   0.0   0.0   0:00.00 pool_workqueue_release
      4 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-rcu_g
      5 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-rcu_p
      6 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-slub_
      7 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-netns
     12 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-mm_pe
     13 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_kthread
     14 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_rude_kthread
     15 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_trace_kthread
     17 root      20   0       0      0      0 I   0.0   0.0   0:21.29 rcu_preempt
     18 root      rt   0       0      0      0 S   0.0   0.0   0:00.72 migration/0
     19 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/0
     20 root      20   0       0      0      0 S   0.0   0.0   0:00.02 cpuhp/0
     21 root      20   0       0      0      0 S   0.0   0.0   0:00.03 cpuhp/1
     22 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/1
     23 root      rt   0       0      0      0 S   0.0   0.0   0:04.37 migration/1
     24 root      20   0       0      0      0 S   0.0   0.0   0:02.86 ksoftirqd/1
     27 root      20   0       0      0      0 S   0.0   0.0   0:00.01 cpuhp/2
     28 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/2
     29 root      rt   0       0      0      0 S   0.0   0.0   0:04.25 migration/2
     30 root      20   0       0      0      0 S   0.0   0.0   0:00.84 ksoftirqd/2
     33 root      20   0       0      0      0 S   0.0   0.0   0:00.02 cpuhp/3
     34 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/3
     35 root      rt   0       0      0      0 S   0.0   0.0   0:01.34 migration/3
     36 root      20   0       0      0      0 S   0.0   0.0   0:00.89 ksoftirqd/3
     43 root      20   0       0      0      0 S   0.0   0.0   0:00.21 kdevtmpfs
     44 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-inet_
     45 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kauditd
     46 root      20   0       0      0      0 S   0.0   0.0   0:00.07 khungtaskd
     47 root      20   0       0      0      0 S   0.0   0.0   0:00.00 oom_reaper
     50 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-write
     51 root      20   0       0      0      0 S   0.0   0.0   0:03.20 kcompactd0
     52 root      25   5       0      0      0 S   0.0   0.0   0:00.00 ksmd
     54 root      39  19       0      0      0 S   0.0   0.0   0:00.00 khugepaged
     55 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-kinte
     56 root       0 -20       0      0      0 I   0.0   0.0   0:00.01 kworker/R-kbloc
     57 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-blkcg
     58 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 irq/9-acpi
     61 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-tpm_d
     62 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-ata_s
     63 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-md
     64 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-md_bi


Latihan 6.6
1. Gunakan ps aux –sort=%mem untuk menemukan proses yang menggu-
nakan memori paling banyak di VM Anda. Proses apa itu?
- root@ubuntu-server:/home/Rappizr7# ps aux --sort=-%mem | head -n 10
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root       15989  1.6  3.1 368728 129024 ?       Sl   05:56   1:19 /usr/bin/python3 /usr/bin/unattended-upgrade --download-only
root       16172  0.0  1.0 478176 42236 ?        Ssl  06:31   0:02 /usr/libexec/fwupd/fwupd
root       10628  0.0  0.6 223452 27192 ?        SLsl 05:00   0:06 /sbin/multipathd -d -s
root         718  0.0  0.5 109688 23056 ?        Ssl  Apr01   0:00 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
root         341  0.0  0.4  66976 19816 ?        S<s  Apr01   0:05 /usr/lib/systemd/systemd-journald
root       10170  0.0  0.3 468972 13660 ?        Ssl  05:00   0:00 /usr/libexec/udisks2/udisksd
root           1  0.0  0.3  22236 13408 ?        Ss   Apr01   0:18 /sbin/init splash noprompt noshell automatic-ubiquity
systemd+     481  0.0  0.3  21588 13076 ?        Ss   Apr01   0:00 /usr/lib/systemd/systemd-resolved
root         760  0.0  0.3 318300 12836 ?        Ssl  Apr01   0:00 /usr/sbin/ModemManager
- ini adalah proses menampilkan yang paling banyak memakan memori

2. Di dalam top, tekan 1 . Apa yang berubah pada tampilan? Mengapa
informasi ini berguna?
- yang berubah tampilan baris cpu di atas akan di pecah menjadi beberapa baris, sangat penting untuk mendeteksi keseimbangan beban

3. Di dalam htop, navigasikan ke proses sshd menggunakan tombol panah.
Tekan F9 dan amati opsi sinyal yang tersedia.
- Di sisi kiri layar akan muncul daftar Sinyal yang bisa dikirim ke proses tersebut


1.8 Latihan
Latihan 6.A
Eksplorasi Proses Sistem
1. Jalankan ps aux –forest dan temukan proses dengan PID 1. Apa
nama dan fungsi proses tersebut dalam sistem Linux modern?
- root           1  0.3  0.3  22064 13240 ?        Ss   13:47   0:01 /sbin/init splash noprompt noshell
- systemd fungsi Menjalankan semua service (sshd, cron, dll)

2. Hitung berapa proses yang dimiliki oleh user root dan berapa yang
dimiliki oleh user Anda. Mengapa root memiliki lebih banyak proses?
- root = jumlahnya sangat banyak kira kira pulungan hhingga > 100, user = 7
- kenapa root lebih bannyak proses? karna root menjalankan thread, system service, hardware dan system mmanagement

3. Temukan semua proses yang berada dalam kondisi S. Mengapa sebagian
besar proses di sistem berada dalam kondisi ini?
- Kondisi S pada kolom STAT berarti: Sleeping (interruptible sleep)
Artinya:
Proses sedang tidak menggunakan CPU, Tapi siap dijalankan kembali jika ada event (input, network, dll)


Latihan 6.B
Simulasi Manajemen Job
1. Jalankan tiga perintah sleep dengan durasi 100, 200, dan 300 detik di background. Verifikasi ketiganya dengan jobs.
- Rappizr7@ubuntu-server:~$ sleep 100 &
[1] 2513
Rappizr7@ubuntu-server:~$ sleep 200 &
[2] 6323
Rappizr7@ubuntu-server:~$ sleep 300 &
[3] 6815
Rappizr7@ubuntu-server:~$ jobs
[1]   Running                 sleep 100 &
[2]-  Running                 sleep 200 &
[3]+  Running                 sleep 300 &

2. Bawa job kedua ke foreground, jeda dengan Ctrl+Z , lalu kembalikan
ke background dengan bg.
-  Rappizr7@ubuntu-server:~$ fg %2
sleep 200
^Z
[2]+  Stopped                 sleep 200
Rappizr7@ubuntu-server:~$ bg
[2]+ sleep 200 &
Rappizr7@ubuntu-server:~$ jobs
[1]   Running                 sleep 100 &
[2]-  Running                 sleep 200 &
[3]+  Running                 sleep 300 &

3. Kirim SIGSTOP ke salah satu proses, verifikasi kondisi T-nya, lalu kirim SIGCONT. Akhiri semua proses percobaan dengan pkill sleep.

3. Hentikan job pertama dengan kill %1. Tampilkan kembali daftar job.
Berapa job yang tersisa?
-  Rappizr7@ubuntu-server:~$ kill %1
Rappizr7@ubuntu-server:~$ jobs
[1]   Terminated              sleep 100
[2]-  Running                 sleep 200 &
[3]+  Running                 sleep 300 &
- 2 jobs



Latihan 6.C
Prioritas dan Sinyal
1. Jalankan dua proses sleep: satu dengan nice +5 dan satu dengan nice
+15. Verifikasi nilai NI keduanya dengan ps.
- Rappizr7@ubuntu-server:~$ nice -n 5 sleep 300 &
nice -n 15 sleep 300 &
[4] 16966
[2]   Terminated              sleep 200
[3]   Terminated              sleep 300
[5] 16967
Rappizr7@ubuntu-server:~$ ps -o pid,ni,comm -C sleep
    PID  NI COMMAND
  16966   5 sleep
  16967  15 sleep

2. Gunakan renice untuk mengubah nice proses pertama menjadi +10.
Proses mana yang kini lebih diprioritaskan scheduler?
- Rappizr7@ubuntu-server:~$ ps -o pid,ni,comm | grep sleep | grep " 5 "
  16966   5 sleep
Rappizr7@ubuntu-server:~$
Rappizr7@ubuntu-server:~$ renice 10 -p 12345
renice: failed to get priority for 12345 (process ID): No such process
Rappizr7@ubuntu-server:~$ ps -o pid,ni,comm | grep sleep | grep " 5 "
  16966   5 sleep
Rappizr7@ubuntu-server:~$ renice 10 -p 16966
16966 (process ID) old priority 5, new priority 10
Rappizr7@ubuntu-server:~$ ps -o pid,ni,comm -C sleep
    PID  NI COMMAND
  16966  10 sleep
  16967  15 sleep
