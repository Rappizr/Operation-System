# Laporan Pertemuan ke-12 Sistem Operasi

**Tanggal:** 15 Mei 2026  
**Disusun Oleh:** Mukhammad Raffi Zabra  
**NIM:** 254107020059  
**Kelas/No:** TI-1G/24  

---

# Praktek 10.1: Amati Layanan Aktif Saat Boot
**1. Lihat semua layanan yang sedang berjalan.**
```bash
root@ubuntu-server:~/lab-os/chapter10-services# systemctl list-units --type=service --state=running
  UNIT                        LOAD   ACTIVE SUB     DESCRIPTION
  cron.service                loaded active running Regular background program processing daemon
  dbus.service                loaded active running D-Bus System Message Bus
  fwupd.service               loaded active running Firmware update daemon
  getty@tty1.service          loaded active running Getty on tty1
  ModemManager.service        loaded active running Modem Manager
  multipathd.service          loaded active running Device-Mapper Multipath Device Controller
  polkit.service              loaded active running Authorization Manager
  rsyslog.service             loaded active running System Logging Service
  ssh.service                 loaded active running OpenBSD Secure Shell server
  systemd-journald.service    loaded active running Journal Service
  systemd-logind.service      loaded active running User Login Management
  systemd-networkd.service    loaded active running Network Configuration
  systemd-resolved.service    loaded active running Network Name Resolution
  systemd-timesyncd.service   loaded active running Network Time Synchronization
  systemd-udevd.service       loaded active running Rule-based Manager for Device Events and Files
  udisks2.service             loaded active running Disk Manager
  unattended-upgrades.service loaded active running Unattended Upgrades Shutdown
  upower.service              loaded active running Daemon for power management
  user@1000.service           loaded active running User Manager for UID 1000

Legend: LOAD   → Reflects whether the unit definition was properly loaded.
        ACTIVE → The high-level unit activation state, i.e. generalization of SUB.
        SUB    → The low-level unit activation state, values depend on unit type.

19 loaded units listed.
```

**2. Lihat semua unit service yang ada (aktif maupun tidak).**
```bash
root@ubuntu-server:~/lab-os/chapter10-services# systemctl list-unit-files --type=service | head -30
UNIT FILE                                    STATE           PRESET
apparmor.service                             enabled         enabled
apport-autoreport.service                    static          -
apport-coredump-hook@.service                static          -
apport-forward@.service                      static          -
apport.service                               enabled         enabled
apt-daily-upgrade.service                    static          -
apt-daily.service                            static          -
apt-news.service                             static          -
autovt@.service                              alias           -
blk-availability.service                     enabled         enabled
bolt.service                                 static          -
cloud-config.service                         enabled         enabled
cloud-final.service                          enabled         enabled
cloud-init-hotplugd.service                  static          -
cloud-init-local.service                     enabled         enabled
cloud-init.service                           enabled         enabled
console-getty.service                        disabled        disabled
console-setup.service                        enabled         enabled
container-getty@.service                     static          -
cron.service                                 enabled         enabled
cryptdisks-early.service                     masked          enabled
cryptdisks.service                           masked          enabled
dbus-org.freedesktop.hostname1.service       alias           -
dbus-org.freedesktop.locale1.service         alias           -
dbus-org.freedesktop.login1.service          alias           -
dbus-org.freedesktop.ModemManager1.service   alias           -
dbus-org.freedesktop.resolve1.service        alias           -
dbus-org.freedesktop.thermald.service        alias           -
dbus-org.freedesktop.timedate1.service       alias           -
```

**3. Analisis waktu boot dan temukan layanan paling lambat.**
```bash
root@ubuntu-server:~/lab-os/chapter10-services# systemd-analyze
Startup finished in 5.299s (kernel) + 2.905s (userspace) = 8.204s
graphical.target reached after 2.890s in userspace.
root@ubuntu-server:~/lab-os/chapter10-services# systemd-analyze blame | head -15
11.823s apt-daily-upgrade.service
 2.572s motd-news.service
 2.516s man-db.service
 2.209s fwupd.service
 1.983s plocate-updatedb.service
 1.449s systemd-networkd-wait-online.service
  753ms update-notifier-download.service
  735ms snapd.seeded.service
  708ms dev-sda2.device
  679ms snapd.service
  670ms upower.service
  516ms dpkg-db-backup.service
  512ms systemd-networkd.service
  498ms e2scrub_reap.service
  445ms systemd-tmpfiles-clean.service
  ```

  **Tantangan**
  1. Identifikasi tiga layanan dengan waktu inisialisasi terlama menggunakan systemd-analyze
blame. Gunakan pipeline dari Bab 3 (| sort -rh | head -3) untuk mempercepat pen-
cariannya. Untuk setiap layanan, cari tahu fungsinya dengan systemctl cat nama-layanan.
Tuliskan nama layanan, waktu inisialisasinya, dan penjelasan singkat fungsinya.
```bash
root@ubuntu-server:~/lab-os/chapter10-services# systemd-analyze blame | head -3
11.823s apt-daily-upgrade.service
 2.572s motd-news.service
 2.516s man-db.service
 ```
|Nama Layanan	|Waktu Inisialisasi	|Fungsi Singkat|
|---------------------------|---------------------|----------------------|
|apt-daily-upgrade.service	|11.823s	|Memeriksa dan menginstal pembaruan keamanan harian secara otomatis.|
|motd-news.service	|2.572s	|Mengambil pesan berita harian dari server Canonical untuk ditampilkan saat login.|
|man-db.service	|2.516s	|Memperbarui indeks database untuk manual pages (man) agar pencarian dokumentasi tetap mutakhir.|

# Praktek 10.2: Kelola Layanan SSH
**1. Periksa status SSH secara menyeluruh.**
```bash
root@ubuntu-server:~/lab-os/chapter10-services# systemctl status ssh && systemctl is-active ssh && systemctl is-enabled ssh
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/usr/lib/systemd/system/ssh.service; disabled; preset: enabled)
     Active: active (running) since Wed 2026-05-06 06:17:09 UTC; 1 week 2 days ago
TriggeredBy: ● ssh.socket
       Docs: man:sshd(8)
             man:sshd_config(5)
    Process: 1131 ExecStartPre=/usr/sbin/sshd -t (code=exited, status=0/SUCCESS)
   Main PID: 1132 (sshd)
      Tasks: 1 (limit: 4697)
     Memory: 4.0M (peak: 6.8M)
        CPU: 392ms
     CGroup: /system.slice/ssh.service
             └─1132 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups"

May 06 06:17:09 ubuntu-server systemd[1]: Starting ssh.service - OpenBSD Secure Shell server...
May 06 06:17:09 ubuntu-server sshd[1132]: Server listening on 0.0.0.0 port 22.
May 06 06:17:09 ubuntu-server systemd[1]: Started ssh.service - OpenBSD Secure Shell server.
May 06 06:17:09 ubuntu-server sshd[1132]: Server listening on :: port 22.
May 06 06:17:50 ubuntu-server sshd[1134]: Accepted password for Rappizr7 from 192.168.63.166 port 61538 ssh2
May 06 06:17:50 ubuntu-server sshd[1134]: pam_unix(sshd:session): session opened for user Rappizr7(uid=1000) by Rappizr7(uid=0)
May 06 12:17:46 ubuntu-server sshd[1535]: Accepted password for Rappizr7 from 192.168.1.63 port 55177 ssh2
May 06 12:17:46 ubuntu-server sshd[1535]: pam_unix(sshd:session): session opened for user Rappizr7(uid=1000) by Rappizr7(uid=0)
May 06 16:18:13 ubuntu-server sshd[3572]: Accepted password for Rappizr7 from 192.168.1.63 port 64675 ssh2
May 06 16:18:13 ubuntu-server sshd[3572]: pam_unix(sshd:session): session opened for user Rappizr7(uid=1000) by Rappizr7(uid=0)
active
disabled
```

**2. Lakukan restart dan pantau perubahannya.**
```bash
root@ubuntu-server:~/lab-os/chapter10-services# sudo systemctl restart ssh && systemctl status ssh
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/usr/lib/systemd/system/ssh.service; disabled; preset: enabled)
     Active: active (running) since Fri 2026-05-15 13:25:12 UTC; 115ms ago
TriggeredBy: ● ssh.socket
       Docs: man:sshd(8)
             man:sshd_config(5)
    Process: 4012 ExecStartPre=/usr/sbin/sshd -t (code=exited, status=0/SUCCESS)
   Main PID: 4014 (sshd)
      Tasks: 1 (limit: 4697)
     Memory: 1.2M (peak: 1.5M)
        CPU: 207ms
     CGroup: /system.slice/ssh.service
             └─4014 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups"

May 15 13:25:12 ubuntu-server systemd[1]: Starting ssh.service - OpenBSD Secure Shell server...
May 15 13:25:12 ubuntu-server sshd[4014]: Server listening on 0.0.0.0 port 22.
May 15 13:25:12 ubuntu-server sshd[4014]: Server listening on :: port 22.
May 15 13:25:12 ubuntu-server systemd[1]: Started ssh.service - OpenBSD Secure Shell server.
```

**3. Lihat dependensi SSH.**
```bash
root@ubuntu-server:~/lab-os/chapter10-services# systemctl list-dependencies ssh
ssh.service
● ├─-.mount
● ├─ssh.socket
● ├─system.slice
● └─sysinit.target
●   ├─apparmor.service
●   ├─blk-availability.service
●   ├─dev-hugepages.mount
●   ├─dev-mqueue.mount
●   ├─finalrd.service
●   ├─keyboard-setup.service
●   ├─kmod-static-nodes.service
○   ├─ldconfig.service
●   ├─lvm2-lvmpolld.socket
●   ├─lvm2-monitor.service
●   ├─multipathd.service
○   ├─open-iscsi.service
●   ├─plymouth-read-write.service
●   ├─plymouth-start.service
●   ├─proc-sys-fs-binfmt_misc.automount
●   ├─setvtrgb.service
●   ├─sys-fs-fuse-connections.mount
●   ├─sys-kernel-config.mount
●   ├─sys-kernel-debug.mount
●   ├─sys-kernel-tracing.mount
○   ├─systemd-ask-password-console.path
●   ├─systemd-binfmt.service
○   ├─systemd-firstboot.service
○   ├─systemd-hwdb-update.service
○   ├─systemd-journal-catalog-update.service
●   ├─systemd-journal-flush.service
●   ├─systemd-journald.service
○   ├─systemd-machine-id-commit.service
●   ├─systemd-modules-load.service
○   ├─systemd-pcrmachine.service
○   ├─systemd-pcrphase-sysinit.service
○   ├─systemd-pcrphase.service
○   ├─systemd-pstore.service
●   ├─systemd-random-seed.service
○   ├─systemd-repart.service
●   ├─systemd-resolved.service
●   ├─systemd-sysctl.service
○   ├─systemd-sysusers.service
●   ├─systemd-timesyncd.service
●   ├─systemd-tmpfiles-setup-dev-early.service
●   ├─systemd-tmpfiles-setup-dev.service
●   ├─systemd-tmpfiles-setup.service
○   ├─systemd-tpm2-setup-early.service
○   ├─systemd-tpm2-setup.service
●   ├─systemd-udev-trigger.service
●   ├─systemd-udevd.service
○   ├─systemd-update-done.service
●   ├─systemd-update-utmp.service
●   ├─cryptsetup.target
●   ├─integritysetup.target
●   ├─local-fs.target
●   │ ├─-.mount
○   │ ├─systemd-fsck-root.service
●   │ └─systemd-remount-fs.service
●   ├─swap.target
●   │ └─swapfile.swap
●   └─veritysetup.target
lines 10-62/62 (END)
```

**4. Cek semua unit yang gagal di sistem.**
```bash
root@ubuntu-server:~/lab-os/chapter10-services# systemctl --failed
  UNIT LOAD ACTIVE SUB DESCRIPTION

0 loaded units listed.
```

**Tantangan**
1. Buat skrip Bash (referensi Bab 7) bernama cek-layanan.sh yang memeriksa status daftar layanan
dari sebuah berkas teks. Berkas teks daftar-layanan.txt berisi satu nama layanan per baris (isi
minimal: ssh, cron, rsyslog). Skrip membaca setiap nama layanan, memeriksa statusnya dengan
systemctl is-active, lalu menulis laporan ke berkas laporan-layanan.log dengan format:
[TANGGAL] nama-layanan: ACTIVE/INACTIVE. Gunakan date untuk mendapatkan tanggal.
```bash
root@ubuntu-server:~/lab-os/chapter10-services# nano daftar-layanan.txt
isi :
ssh
cron
rsyslog
root@ubuntu-server:~/lab-os/chapter10-services# nano cek-layanan.sh
isi :
#!/bin/bash

# File konfigurasi
INPUT="daftar-layanan.txt"
LOG="laporan-layanan.log"

# Pastikan file input ada
if [ ! -f "$INPUT" ]; then
    echo "Error: $INPUT tidak ditemukan!"
    exit 1
fi

# Proses pengecekan
while IFS= read -r svc; do
    # Skip baris kosong
    [ -z "$svc" ] && continue

    # Ambil waktu saat ini
    TS=$(date "+%Y-%m-%d %H:%M:%S")

    # Cek status (0 = active, selain itu = inactive)
    systemctl is-active --quiet "$svc"
    if [ $? -eq 0 ]; then
        STATUS="ACTIVE"
    else
        STATUS="INACTIVE"
    fi

    # Catat ke log
    echo "[$TS] $svc: $STATUS" >> "$LOG"
done < "$INPUT"

echo "Selesai! Cek file $LOG untuk melihat hasilnya."
root@ubuntu-server:~/lab-os/chapter10-services# chmod +x cek-layanan.sh
root@ubuntu-server:~/lab-os/chapter10-services# ./cek-layanan.sh
Selesai! Cek file laporan-layanan.log untuk melihat hasilnya.
root@ubuntu-server:~/lab-os/chapter10-services# cat laporan-layanan.log
[2026-05-15 14:01:40] ssh: ACTIVE
[2026-05-15 14:01:40] cron: ACTIVE
[2026-05-15 14:01:40] rsyslog: ACTIVE
```

# Praktek 10.3: Buat Layanan Sederhana dari Skrip Bash
**1. Siapkan konten yang akan dilayani.**
```bash
root@ubuntu-server:~/lab-os/chapter10-services# cd ~/lab-os/chapter10-services
root@ubuntu-server:~/lab-os/chapter10-services# mkdir -p situs-demo
root@ubuntu-server:~/lab-os/chapter10-services# nano situs-demo/index.html
isi :
<!DOCTYPE html>
<html>
<body>
    <h1>Halo dari layanan systemd kustom!</h1>
    <p>Layanan ini dibuat pada praktek Bab 10.</p>
</body>
</html>
```

**2. Buat skrip wrapper untuk server HTTP.**
```bash
root@ubuntu-server:~/lab-os/chapter10-services# nano jalankan-server.sh
isi :
#!/bin/bash
DIREKTORI="$HOME/lab-os/chapter10-services/situs-demo"
PORT=9090
echo "Memulai server di port $PORT..."
exec python3 -m http.server $PORT --directory "$DIREKTORI"
```

**3. Buat berkas unit systemd untuk layanan ini.**
```bash
root@ubuntu-server:~/lab-os/chapter10-services# nano demo-web.service
isi :
[Unit]
Description=Demo Web Server Praktek Bab 10
After=network.target

[Service]
Type=simple
User=Rappizr7
WorkingDirectory=/home/Rappizr7/lab-os/chapter10-services/situs-demo
ExecStart=/usr/bin/python3 -m http.server 9090
Restart=on-failure
RestartSec=3s

[Install]
WantedBy=multi-user.target
root@ubuntu-server:~/lab-os/chapter10-services# sudo cp demo-web.service /etc/systemd/system/
root@ubuntu-server:~/lab-os/chapter10-services# sudo systemctl daemon-reload
```

**4. Jalankan layanan dan verifikasi.**
```bash
root@ubuntu-server:~/lab-os/chapter10-services/situs-demo# sudo systemctl daemon-reload
sudo systemctl restart demo-web
systemctl status demo-web
● demo-web.service - Demo Web Server Praktek Bab 10
     Loaded: loaded (/etc/systemd/system/demo-web.service; disabled; preset: enabled)
     Active: active (running) since Fri 2026-05-15 14:30:25 UTC; 51ms ago
   Main PID: 5438 (python3)
      Tasks: 1 (limit: 4697)
     Memory: 2.0M (peak: 2.0M)
        CPU: 26ms
     CGroup: /system.slice/demo-web.service
             └─5438 /usr/bin/python3 -m http.server 9090

May 15 14:30:25 ubuntu-server systemd[1]: Started demo-web.service - Demo Web Server Praktek Bab 10.
root@ubuntu-server:~/lab-os/chapter10-services/situs-demo# curl http://localhost:9090
<!DOCTYPE html>
<html>
<body>
    <h1>Halo dari layanan systemd kustom!</h1>
    <p>Layanan ini dibuat pada praktek Bab 10.</p>
</body>
</html>
```

**5. Uji fitur restart otomatis.**
```bash
root@ubuntu-server:~/lab-os/chapter10-services/situs-demo# systemctl status demo-web | grep "Main PID"
   Main PID: 5438 (python3)
root@ubuntu-server:~/lab-os/chapter10-services/situs-demo# sudo kill -9 $(systemctl show demo-web --property=MainPID --value)
root@ubuntu-server:~/lab-os/chapter10-services/situs-demo# sleep 5
root@ubuntu-server:~/lab-os/chapter10-services/situs-demo# systemctl status demo-web
● demo-web.service - Demo Web Server Praktek Bab 10
     Loaded: loaded (/etc/systemd/system/demo-web.service; disabled; preset: enabled)
     Active: active (running) since Fri 2026-05-15 14:32:38 UTC; 50s ago
   Main PID: 5460 (python3)
      Tasks: 1 (limit: 4697)
     Memory: 9.3M (peak: 9.6M)
        CPU: 277ms
     CGroup: /system.slice/demo-web.service
             └─5460 /usr/bin/python3 -m http.server 9090

May 15 14:32:38 ubuntu-server systemd[1]: demo-web.service: Scheduled restart job, restart counter is>
May 15 14:32:38 ubuntu-server systemd[1]: Started demo-web.service - Demo Web Server Praktek Bab 10.
```

**6. Bersihkan layanan uji setelah selesai.**
```bash
root@ubuntu-server:~/lab-os/chapter10-services/situs-demo# sudo systemctl disable --now demo-web
root@ubuntu-server:~/lab-os/chapter10-services/situs-demo# sudo rm /etc/systemd/system/demo-web.service
root@ubuntu-server:~/lab-os/chapter10-services/situs-demo# sudo systemctl daemon-reload
```

**Tantangan**
1. Modifikasi berkas unit demo-web.service sebelum menghapusnya: tambahkan
RestartSec=10s agar sistemmenunggu 10 detik sebelum mencoba restart, dan tambahkan
Environment="PORT=9091" lalu ubah ExecStart agar menggunakan variabel tersebut. Aktifkan
layanan dengan enable dan WantedBy=multi-user.target, lalu uji apakah layanan aktif
setelah systemctl daemon-reload. Dokumentasikan perbedaan perilaku dibanding versi
sebelumnya.
```bash
root@ubuntu-server:~/lab-os/chapter10-services# sudo bash -c 'cat <<EOF > /etc/systemd/system/demo-web.service
[Unit]
Description=Demo Web Server Praktek Bab 10 Modifikasi
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/root/lab-os/chapter10-services/situs-demo
# Menambahkan variabel lingkungan untuk port
Environment="PORT=9091"
# Menggunakan variabel \$PORT pada ExecStart
ExecStart=/usr/bin/python3 -m http.server \$PORT
Restart=on-failure
# Menunggu 10 detik sebelum mencoba restart otomatis
RestartSec=10s

[Install]
WantedBy=multi-user.target
EOF'
root@ubuntu-server:~/lab-os/chapter10-services# sudo systemctl daemon-reload
root@ubuntu-server:~/lab-os/chapter10-services# sudo systemctl enable demo-web
root@ubuntu-server:~/lab-os/chapter10-services# sudo systemctl restart demo-web
root@ubuntu-server:~/lab-os/chapter10-services# systemctl status demo-web
● demo-web.service - Demo Web Server Praktek Bab 10 Modifikasi
     Loaded: loaded (/etc/systemd/system/demo-web.service; enabled; preset: enabled)
     Active: active (running) since Fri 2026-05-15 15:05:09 UTC; 6s ago
   Main PID: 6313 (python3)
      Tasks: 1 (limit: 4697)
     Memory: 9.3M (peak: 9.7M)
        CPU: 163ms
     CGroup: /system.slice/demo-web.service
             └─6313 /usr/bin/python3 -m http.server 9091

May 15 15:05:09 ubuntu-server systemd[1]: Started demo-web.service - Demo Web Server Praktek Bab 10 M>
root@ubuntu-server:~/lab-os/chapter10-services# curl http://localhost:9091
<!DOCTYPE html>
<html>
<body>
    <h1>Halo dari layanan systemd kustom!</h1>
    <p>Layanan ini dibuat pada praktek Bab 10.</p>
</body>
</html>
```
|Fitur	|Versi Awal	|Versi Modifikasi|
|----------|----------|---------|
|Port Layanan	|Terkunci pada port 9090.	|Menggunakan variabel $PORT yang disetel ke 9091.|
|Waktu Restart	|Cepat (3 detik) setelah kegagalan.	|Lebih lambat (10 detik), memberikan jeda bagi sistem sebelum mencoba kembali.|
|Status Boot	|disabled (tidak aktif otomatis).	|enabled (otomatis aktif saat sistem menyala).|
|Fleksibilitas	|konfigurasi port tertanam di baris perintah.	|Port mudah diubah melalui Environment tanpa mengedit baris ExecStart.|

# Praktek 10.4: Filter dan Analisis Log Layanan
**1. Lihat log SSH dari satu jam terakhir.**
```bash
root@ubuntu-server:~/lab-os/chapter10-services# journalctl -u ssh --since "1 hour ago" --no-pager
-- No entries --
root@ubuntu-server:~/lab-os/chapter10-services# journalctl -u cron --since "1 hour ago" --no-pager
May 15 14:45:02 ubuntu-server CRON[5963]: pam_unix(cron:session): session opened for user root(uid=0) by root(uid=0)
May 15 14:45:02 ubuntu-server CRON[5964]: (root) CMD (command -v debian-sa1 > /dev/null && debian-sa1 1 1)
May 15 14:45:02 ubuntu-server CRON[5963]: pam_unix(cron:session): session closed for user root
May 15 14:55:01 ubuntu-server CRON[6082]: pam_unix(cron:session): session opened for user root(uid=0) by root(uid=0)
May 15 14:55:01 ubuntu-server CRON[6083]: (root) CMD (command -v debian-sa1 > /dev/null && debian-sa1 1 1)
May 15 14:55:01 ubuntu-server CRON[6082]: pam_unix(cron:session): session closed for user root
May 15 15:05:01 ubuntu-server CRON[6260]: pam_unix(cron:session): session opened for user root(uid=0) by root(uid=0)
May 15 15:05:01 ubuntu-server CRON[6261]: (root) CMD (command -v debian-sa1 > /dev/null && debian-sa1 1 1)
May 15 15:05:02 ubuntu-server CRON[6260]: pam_unix(cron:session): session closed for user root
May 15 15:15:02 ubuntu-server CRON[6331]: pam_unix(cron:session): session opened for user root(uid=0) by root(uid=0)
May 15 15:15:02 ubuntu-server CRON[6332]: (root) CMD (command -v debian-sa1 > /dev/null && debian-sa1 1 1)
May 15 15:15:02 ubuntu-server CRON[6331]: pam_unix(cron:session): session closed for user root
May 15 15:17:01 ubuntu-server CRON[6335]: pam_unix(cron:session): session opened for user root(uid=0) by root(uid=0)
May 15 15:17:01 ubuntu-server CRON[6336]: (root) CMD (cd / && run-parts --report /etc/cron.hourly)
May 15 15:17:01 ubuntu-server CRON[6335]: pam_unix(cron:session): session closed for user root
May 15 15:25:01 ubuntu-server CRON[6349]: pam_unix(cron:session): session opened for user root(uid=0) by root(uid=0)
May 15 15:25:01 ubuntu-server CRON[6350]: (root) CMD (command -v debian-sa1 > /dev/null && debian-sa1 1 1)
May 15 15:25:01 ubuntu-server CRON[6349]: pam_unix(cron:session): session closed for user root
May 15 15:35:02 ubuntu-server CRON[6364]: pam_unix(cron:session): session opened for user root(uid=0) by root(uid=0)
May 15 15:35:02 ubuntu-server CRON[6365]: (root) CMD (command -v debian-sa1 > /dev/null && debian-sa1 1 1)
May 15 15:35:02 ubuntu-server CRON[6364]: pam_unix(cron:session): session closed for user root
```

**2. Filter log berprioritas error ke atas.**
```bash
root@ubuntu-server:~/lab-os/chapter10-services# journalctl -b -p err --no-pager
May 06 06:16:38 ubuntu-server kernel: vmwgfx 0000:00:02.0: [drm] *ERROR* vmwgfx seems to be running on an unsupported hypervisor.
May 06 06:16:38 ubuntu-server kernel: vmwgfx 0000:00:02.0: [drm] *ERROR* This configuration is likely broken.
May 06 06:16:38 ubuntu-server kernel: vmwgfx 0000:00:02.0: [drm] *ERROR* Please switch to a supported graphics device to avoid problems.
May 06 06:16:43 ubuntu-server login[945]: PAM unable to dlopen(pam_lastlog.so): /usr/lib/security/pam_lastlog.so: cannot open shared object file: No such file or directory
May 06 06:16:43 ubuntu-server login[945]: PAM adding faulty module: pam_lastlog.so
May 06 07:00:28 ubuntu-server systemd[1]: Failed to start fwupd-refresh.service - Refresh fwupd metadata and update motd.
May 06 16:16:56 ubuntu-server kernel: e1000 0000:00:03.0 enp0s3: Reset adapter
May 15 14:21:59 ubuntu-server (python3)[4988]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:22:02 ubuntu-server (python3)[4993]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:22:06 ubuntu-server (python3)[4997]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:22:10 ubuntu-server (python3)[4999]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:22:14 ubuntu-server (python3)[5001]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:22:18 ubuntu-server (python3)[5003]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:22:21 ubuntu-server (python3)[5006]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:22:24 ubuntu-server (python3)[5008]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:22:28 ubuntu-server (python3)[5010]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:22:31 ubuntu-server (python3)[5012]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:22:34 ubuntu-server (python3)[5014]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:22:38 ubuntu-server (python3)[5016]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:22:41 ubuntu-server (python3)[5021]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:22:44 ubuntu-server (python3)[5023]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:22:47 ubuntu-server (python3)[5025]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:22:50 ubuntu-server (python3)[5029]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:22:54 ubuntu-server (python3)[5031]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:22:57 ubuntu-server (python3)[5033]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:23:00 ubuntu-server (python3)[5035]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:23:04 ubuntu-server (python3)[5037]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:23:07 ubuntu-server (python3)[5040]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:23:10 ubuntu-server (python3)[5042]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:23:13 ubuntu-server (python3)[5044]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:23:17 ubuntu-server (python3)[5046]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:23:20 ubuntu-server (python3)[5049]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:23:24 ubuntu-server (python3)[5051]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:23:27 ubuntu-server (python3)[5053]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:23:30 ubuntu-server (python3)[5057]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:23:34 ubuntu-server (python3)[5059]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:23:37 ubuntu-server (python3)[5061]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:23:40 ubuntu-server (python3)[5063]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:23:46 ubuntu-server (python3)[5065]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:23:49 ubuntu-server (python3)[5067]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:23:52 ubuntu-server (python3)[5069]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:23:57 ubuntu-server (python3)[5071]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:24:00 ubuntu-server (python3)[5073]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:24:04 ubuntu-server (python3)[5075]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:24:07 ubuntu-server (python3)[5077]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:24:10 ubuntu-server (python3)[5079]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:24:13 ubuntu-server (python3)[5081]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:24:17 ubuntu-server (python3)[5083]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:24:20 ubuntu-server (python3)[5085]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:24:23 ubuntu-server (python3)[5087]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:24:28 ubuntu-server (python3)[5089]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:24:31 ubuntu-server (python3)[5092]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:24:35 ubuntu-server (python3)[5094]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:24:40 ubuntu-server (python3)[5097]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:24:43 ubuntu-server (python3)[5099]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:24:46 ubuntu-server (python3)[5102]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:24:50 ubuntu-server (python3)[5104]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:24:53 ubuntu-server (python3)[5106]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:24:56 ubuntu-server (python3)[5108]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:25:00 ubuntu-server (python3)[5111]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:25:04 ubuntu-server (python3)[5116]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:25:07 ubuntu-server (python3)[5118]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:25:11 ubuntu-server (python3)[5120]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:25:14 ubuntu-server (python3)[5123]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:25:17 ubuntu-server (python3)[5129]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:25:21 ubuntu-server (python3)[5133]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:25:24 ubuntu-server (python3)[5135]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:25:27 ubuntu-server (python3)[5140]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:25:31 ubuntu-server (python3)[5142]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:25:35 ubuntu-server (python3)[5144]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:25:38 ubuntu-server (python3)[5146]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:25:41 ubuntu-server (python3)[5148]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:25:45 ubuntu-server (python3)[5150]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:25:48 ubuntu-server (python3)[5152]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:25:51 ubuntu-server (python3)[5154]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:25:54 ubuntu-server (python3)[5156]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:25:58 ubuntu-server (python3)[5159]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:26:01 ubuntu-server (python3)[5162]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:26:04 ubuntu-server (python3)[5164]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:26:08 ubuntu-server (python3)[5166]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:26:11 ubuntu-server (python3)[5168]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:26:14 ubuntu-server (python3)[5170]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:26:18 ubuntu-server (python3)[5172]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:26:21 ubuntu-server (python3)[5175]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:26:24 ubuntu-server (python3)[5178]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:26:28 ubuntu-server (python3)[5180]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:26:33 ubuntu-server (python3)[5182]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:26:37 ubuntu-server (python3)[5184]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:26:41 ubuntu-server (python3)[5186]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:26:44 ubuntu-server (python3)[5188]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:26:47 ubuntu-server (python3)[5190]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:26:51 ubuntu-server (python3)[5192]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:26:54 ubuntu-server (python3)[5194]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:26:57 ubuntu-server (python3)[5196]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:27:01 ubuntu-server (python3)[5198]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:27:04 ubuntu-server (python3)[5202]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:27:07 ubuntu-server (python3)[5204]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:27:11 ubuntu-server (python3)[5206]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:27:15 ubuntu-server (python3)[5208]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:27:19 ubuntu-server (python3)[5210]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:27:22 ubuntu-server (python3)[5212]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:27:27 ubuntu-server (python3)[5214]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:27:30 ubuntu-server (python3)[5216]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:27:33 ubuntu-server (python3)[5218]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:27:36 ubuntu-server (python3)[5220]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:27:40 ubuntu-server (python3)[5223]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:27:43 ubuntu-server (python3)[5225]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:27:46 ubuntu-server (python3)[5227]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:27:49 ubuntu-server (python3)[5229]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:27:53 ubuntu-server (python3)[5231]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:27:56 ubuntu-server (python3)[5233]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:27:59 ubuntu-server (python3)[5235]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:28:03 ubuntu-server (python3)[5237]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:28:06 ubuntu-server (python3)[5239]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:28:09 ubuntu-server (python3)[5241]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:28:13 ubuntu-server (python3)[5243]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:28:16 ubuntu-server (python3)[5245]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:28:20 ubuntu-server (python3)[5247]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:28:23 ubuntu-server (python3)[5249]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:28:26 ubuntu-server (python3)[5251]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:28:29 ubuntu-server (python3)[5253]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:28:33 ubuntu-server (python3)[5255]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:28:36 ubuntu-server (python3)[5258]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:28:39 ubuntu-server (python3)[5260]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:28:43 ubuntu-server (python3)[5262]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:28:46 ubuntu-server (python3)[5265]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:28:49 ubuntu-server (python3)[5267]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:28:53 ubuntu-server (python3)[5269]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:28:56 ubuntu-server (python3)[5271]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:28:59 ubuntu-server (python3)[5273]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:29:03 ubuntu-server (python3)[5275]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:29:06 ubuntu-server (python3)[5277]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:29:09 ubuntu-server (python3)[5279]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:29:13 ubuntu-server (python3)[5281]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:29:17 ubuntu-server (python3)[5283]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:29:20 ubuntu-server (python3)[5329]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:29:23 ubuntu-server (python3)[5331]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:29:25 ubuntu-server (python3)[5337]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:29:29 ubuntu-server (python3)[5339]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:29:32 ubuntu-server (python3)[5343]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:29:35 ubuntu-server (python3)[5346]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:29:39 ubuntu-server (python3)[5348]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:29:42 ubuntu-server (python3)[5350]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:29:45 ubuntu-server (python3)[5352]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:29:49 ubuntu-server (python3)[5354]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:29:52 ubuntu-server (python3)[5357]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:29:55 ubuntu-server (python3)[5359]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:29:59 ubuntu-server (python3)[5361]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:30:02 ubuntu-server (python3)[5363]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:30:05 ubuntu-server (python3)[5368]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:30:08 ubuntu-server (python3)[5370]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:30:12 ubuntu-server (python3)[5373]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:30:15 ubuntu-server (python3)[5375]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:30:18 ubuntu-server (python3)[5378]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:30:21 ubuntu-server (python3)[5384]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:36:16 ubuntu-server (python3)[5709]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:36:26 ubuntu-server (python3)[5715]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:36:36 ubuntu-server (python3)[5720]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:36:49 ubuntu-server (python3)[5723]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:36:59 ubuntu-server (python3)[5725]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:37:10 ubuntu-server (python3)[5727]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:37:22 ubuntu-server (python3)[5729]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:37:33 ubuntu-server (python3)[5731]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:37:43 ubuntu-server (python3)[5735]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:37:53 ubuntu-server (python3)[5737]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:38:04 ubuntu-server (python3)[5740]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:38:14 ubuntu-server (python3)[5742]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:38:25 ubuntu-server (python3)[5744]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:38:35 ubuntu-server (python3)[5746]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:38:46 ubuntu-server (python3)[5748]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:38:56 ubuntu-server (python3)[5750]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:39:07 ubuntu-server (python3)[5752]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:39:18 ubuntu-server (python3)[5754]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:39:28 ubuntu-server (python3)[5756]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:39:39 ubuntu-server (python3)[5758]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:39:50 ubuntu-server (python3)[5760]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:40:01 ubuntu-server (python3)[5762]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:40:12 ubuntu-server (python3)[5768]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:40:22 ubuntu-server (python3)[5771]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:40:33 ubuntu-server (python3)[5773]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:40:44 ubuntu-server (python3)[5775]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:40:56 ubuntu-server (python3)[5777]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:41:06 ubuntu-server (python3)[5779]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:41:17 ubuntu-server (python3)[5781]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:41:27 ubuntu-server (python3)[5783]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:41:38 ubuntu-server (python3)[5787]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:41:48 ubuntu-server (python3)[5791]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:41:58 ubuntu-server (python3)[5796]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:42:08 ubuntu-server (python3)[5802]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:42:19 ubuntu-server (python3)[5853]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:42:23 ubuntu-server (python3)[5855]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:42:26 ubuntu-server (python3)[5858]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:42:29 ubuntu-server (python3)[5863]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:42:32 ubuntu-server (python3)[5868]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:42:36 ubuntu-server (python3)[5870]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:42:39 ubuntu-server (python3)[5872]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:42:44 ubuntu-server (python3)[5874]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:42:47 ubuntu-server (python3)[5878]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:42:51 ubuntu-server (python3)[5880]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:42:55 ubuntu-server (python3)[5882]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:42:59 ubuntu-server (python3)[5884]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:43:02 ubuntu-server (python3)[5886]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:43:06 ubuntu-server (python3)[5888]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:43:09 ubuntu-server (python3)[5890]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:43:12 ubuntu-server (python3)[5892]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:43:16 ubuntu-server (python3)[5895]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:43:19 ubuntu-server (python3)[5897]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:43:22 ubuntu-server (python3)[5899]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:43:26 ubuntu-server (python3)[5901]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:43:29 ubuntu-server (python3)[5903]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:43:32 ubuntu-server (python3)[5905]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:43:35 ubuntu-server (python3)[5907]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:43:39 ubuntu-server (python3)[5909]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:43:42 ubuntu-server (python3)[5911]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:43:46 ubuntu-server (python3)[5913]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:43:50 ubuntu-server (python3)[5915]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:43:53 ubuntu-server (python3)[5917]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:43:56 ubuntu-server (python3)[5919]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:44:00 ubuntu-server (python3)[5921]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:44:03 ubuntu-server (python3)[5923]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:44:07 ubuntu-server (python3)[5925]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:44:10 ubuntu-server (python3)[5927]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:44:14 ubuntu-server (python3)[5929]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:44:17 ubuntu-server (python3)[5935]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:44:21 ubuntu-server (python3)[5937]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:44:24 ubuntu-server (python3)[5939]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:44:28 ubuntu-server (python3)[5941]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:44:31 ubuntu-server (python3)[5943]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:44:34 ubuntu-server (python3)[5945]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:44:38 ubuntu-server (python3)[5947]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:44:41 ubuntu-server (python3)[5949]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:44:44 ubuntu-server (python3)[5951]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:44:48 ubuntu-server (python3)[5953]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:44:51 ubuntu-server (python3)[5955]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:44:55 ubuntu-server (python3)[5957]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:44:58 ubuntu-server (python3)[5959]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:45:01 ubuntu-server (python3)[5961]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:45:04 ubuntu-server (python3)[5966]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:45:08 ubuntu-server (python3)[5968]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:45:11 ubuntu-server (python3)[5970]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:45:14 ubuntu-server (python3)[5972]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:45:18 ubuntu-server (python3)[5974]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:45:22 ubuntu-server (python3)[5976]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:45:25 ubuntu-server (python3)[5978]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:45:29 ubuntu-server (python3)[5980]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:45:34 ubuntu-server (python3)[5982]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:45:37 ubuntu-server (python3)[5984]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:45:40 ubuntu-server (python3)[5986]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:45:44 ubuntu-server (python3)[5988]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:45:47 ubuntu-server (python3)[5991]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:45:51 ubuntu-server (python3)[5994]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:45:54 ubuntu-server (python3)[6000]: demo-web.service: Changing to the requested working directory failed: No such file or directory
May 15 14:45:58 ubuntu-server (python3)[6002]: demo-web.service: Changing to the requested working directory failed: No such file or directory
```

**3. Ikuti log secara real-time sambil memicu aktivitas.**
```bash
root@ubuntu-server:/lab-os/chapter10-services# journalctl -u ssh -f
May 15 13:25:12 ubuntu-server systemd[1]: Starting ssh.service - OpenBSD Secure Shell server.
May 15 13:25:12 ubuntu-server sshd[4014]: Server listening on 0.0.0.0 port 22.
May 15 13:25:12 ubuntu-server sshd[4014]: Server listening on:: port 22.
May 15 13:25:12 ubuntu-server systemd[1]: Started ssh.service OpenBSD Secure Shell server.
May 15 13:53:13 ubuntu-server sshd[4518]: Accepted password for Rappizr? from 192.168.1.63 port 53867 ssh2
May 15 13:53:13 ubuntu-server sshd[4518]: pam_unix(sshd:session): session opened for user Rappizr7(uid=1000) by Rappizr7(uid=0)
May 15 13:56:38 ubuntu-server sshd[4680]: Accepted password for Rappizr? from 192.168.1.63 port 50221 ssh2
15 13:56:38 ubuntu-server sshd[4680]: pam_unix(sshd:session): session opened for user Rappizr7(uid=1000) by Rappizr7(uid=0)
May May 15 13:59:23 ubuntu-server sshd[4762]: Accepted password for Rappizr? from 192.168.1.63 port 55924 ssh2
May 15 13:59:23 ubuntu-server sshd[4762]: pam_unix(sshd:session): session opened for user Rappizr7(uid=1000) by Rappizr7(uid=0)
16:11:25 ubuntu-server sshd[6451]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty-ssh ruser= rhost=::1 user=root
May 15 May 15 16:11:27 ubuntu-server sshd[6451]: Failed password for root from::1 port 41258 ssh2
May 15 16:11:36 ubuntu-server sshd[6451]: Failed password for root from::1 port 41258 ssh2
May 15 16:14:05 ubuntu-server sshd[6462]: Accepted password for Rappizr? from ::1 port 41012 ssh2
May 15 16:14:05 ubuntu-server sshd[6462]: pam_unix(sshd:session): session opened for user Rappizr7(uid=1000) by Rappizr7(uid=0)

terminal sisi :
root@ubuntu-server:~/lab-os/chapter10-services# ssh Rappizr7@localhost
Rappizr7@localhost's password:
Welcome to Ubuntu 24.04.4 LTS (GNU/Linux 6.8.0-110-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Fri May 15 04:14:06 PM UTC 2026

  System load:             0.0
  Usage of /:              16.3% of 51.58GB
  Memory usage:            12%
  Swap usage:              0%
  Processes:               158
  Users logged in:         1
  IPv4 address for enp0s3: 192.168.1.92
  IPv6 address for enp0s3: 2001:448a:50a0:ee43:a00:27ff:fe96:83c6

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

Expanded Security Maintenance for Applications is not enabled.

78 updates can be applied immediately.
28 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable

7 additional security updates can be applied with ESM Apps.
Learn more about enabling ESM Apps service at https://ubuntu.com/esm


Last login: Fri May 15 13:59:23 2026 from 192.168.1.63
```

**4. Ekstrak log ke berkas untuk analisis.**
```bash
root@ubuntu-server:/home/Rappizr7# cd ~/lab-os/chapter10-services
root@ubuntu-server:~/lab-os/chapter10-services# journalctl -u ssh --since today --no-pager > log-ssh-hari-ini.txt
root@ubuntu-server:~/lab-os/chapter10-services# wc -l log-ssh-hari-ini.txt
19 log-ssh-hari-ini.txt
root@ubuntu-server:~/lab-os/chapter10-services# grep -i "error\|failed" log-ssh-hari-ini.txt | head -20
May 15 16:11:27 ubuntu-server sshd[6451]: Failed password for root from ::1 port 41258 ssh2
May 15 16:11:36 ubuntu-server sshd[6451]: Failed password for root from ::1 port 41258 ssh2
```

**Tantangan**
1. Ekstrak semua log dengan prioritas error (-p err) dari 24 jam terakhir untuk layanan SSH, simpan
ke berkas error-ssh-24jam.txt. Gunakan pipeline dari Bab 3 untuk menghitung total jumlah
baris error dengan wc -l, lalu tampilkan 10 pesan error yang paling sering muncul menggunakan
sort | uniq -c | sort -rn | head -10. Tuliskan perintah lengkap yang kamu gunakan.
```bash
root@ubuntu-server:~/lab-os/chapter10-services# journalctl -u ssh -p err --since "24 hours ago" --no-pager > error-ssh-24jam.txt && \
echo "Total jumlah baris error:" && wc -l < error-ssh-24jam.txt && \
echo "10 Pesan error yang paling sering muncul:" && \
cut -d' ' -f5- error-ssh-24jam.txt | sort | uniq -c | sort -rn | head -10
Total jumlah baris error:
1
10 Pesan error yang paling sering muncul:
      1
```

# Praktek 10.5: Konfigurasi SSH Server
**1. Periksa konfigurasi SSH saat ini.**
```bash
root@ubuntu-server:~/lab-os/chapter10-services# sudo grep -n "^Port\|^#Port" /etc/ssh/sshd_config
23:#Port 22
root@ubuntu-server:~/lab-os/chapter10-services# ss -tlnp | grep ssh
LISTEN 0      4096         0.0.0.0:22        0.0.0.0:*    users:(("sshd",pid=4014,fd=3),("systemd",pid=1,fd=93))
LISTEN 0      4096            [::]:22           [::]:*    users:(("sshd",pid=4014,fd=4),("systemd",pid=1,fd=94))
```

**2. Buat backup dan ubah port SSH.**
```bash
root@ubuntu-server:~/lab-os/chapter10-services# sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup.lab12v
root@ubuntu-server:~/lab-os/chapter10-services# sudo sed -i 's/^#Port 22/Port 2222/' /etc/ssh/sshd_config
root@ubuntu-server:~/lab-os/chapter10-services# grep "^Port" /etc/ssh/sshd_config
Port 2222
```

**3. Validasi konfigurasi dan restart layanan.**
```bash
root@ubuntu-server:~/lab-os/chapter10-services# sudo sshd -t
root@ubuntu-server:~/lab-os/chapter10-services# echo "Kode keluar sshd -t: $?"
Kode keluar sshd -t: 0
root@ubuntu-server:~/lab-os/chapter10-services# sudo systemctl restart ssh
root@ubuntu-server:~/lab-os/chapter10-services# systemctl status ssh
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/usr/lib/systemd/system/ssh.service; disabled; preset: enabled)
     Active: active (running) since Fri 2026-05-15 16:47:43 UTC; 4s ago
TriggeredBy: ● ssh.socket
       Docs: man:sshd(8)
             man:sshd_config(5)
    Process: 6670 ExecStartPre=/usr/sbin/sshd -t (code=exited, status=0/SUCCESS)
   Main PID: 6671 (sshd)
      Tasks: 1 (limit: 4697)
     Memory: 1.2M (peak: 1.4M)
        CPU: 224ms
     CGroup: /system.slice/ssh.service
             └─6671 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups"

May 15 16:47:43 ubuntu-server systemd[1]: Starting ssh.service - OpenBSD Secure Shell server...
May 15 16:47:43 ubuntu-server sshd[6671]: Server listening on 0.0.0.0 port 22.
May 15 16:47:43 ubuntu-server sshd[6671]: Server listening on :: port 22.
May 15 16:47:43 ubuntu-server systemd[1]: Started ssh.service - OpenBSD Secure Shell server.
```

**4. Verifikasi port baru dengan ss.**
```bash
root@ubuntu-server:~/lab-os/chapter10-services# ss -tlnp | grep ssh
LISTEN 0      4096         0.0.0.0:22        0.0.0.0:*    users:(("sshd",pid=6671,fd=3),("systemd",pid=1,fd=93))
LISTEN 0      4096            [::]:22           [::]:*    users:(("sshd",pid=6671,fd=4),("systemd",pid=1,fd=94))
root@ubuntu-server:~/lab-os/chapter10-services# mkdir -p ~/lab-os/chapter10-services
root@ubuntu-server:~/lab-os/chapter10-services# ss -tlnp | grep ssh > ~/lab-os/chapter10-services/bukti-port-ssh.txt
root@ubuntu-server:~/lab-os/chapter10-services# cat ~/lab-os/chapter10-services/bukti-port-ssh.txt
LISTEN 0      4096         0.0.0.0:22        0.0.0.0:*    users:(("sshd",pid=6671,fd=3),("systemd",pid=1,fd=93))
LISTEN 0      4096            [::]:22           [::]:*    users:(("sshd",pid=6671,fd=4),("systemd",pid=1,fd=94))
```

**5. Kembalikan port SSH ke 22 setelah praktek.**
```bash
root@ubuntu-server:~/lab-os/chapter10-services# sudo cp /etc/ssh/sshd_config.backup.lab12 /etc/ssh/sshd_config
root@ubuntu-server:~/lab-os/chapter10-services# sudo sshd -t
root@ubuntu-server:~/lab-os/chapter10-services# sudo systemctl restart ssh
root@ubuntu-server:~/lab-os/chapter10-services# ss -tlnp | grep ssh
LISTEN 0      4096         0.0.0.0:22        0.0.0.0:*    users:(("sshd",pid=6730,fd=3),("systemd",pid=1,fd=93))
LISTEN 0      4096            [::]:22           [::]:*    users:(("sshd",pid=6730,fd=4),("systemd",pid=1,fd=94))
```

**Tantangan**
1. Ubah konfigurasi SSH untuk menambahkan dua pengaturan keamanan: PermitRootLogin no
(larang login root langsung) dan MaxAuthTries 3 (maksimal tiga kali percobaan). Lakukan
dengan urutan yang aman: backup, edit, validasi dengan sshd -t, reload. Verifikasi perubahan
dengan grep -E "PermitRoot|MaxAuth" /etc/ssh/sshd_config. Kemudian periksa log
SSH untuk memastikan tidak ada error setelah perubahan dengan journalctl -u ssh -n 20.
Referensi Bab 2 untuk penggunaan ss dan Bab 9 untuk keamanan pengguna.
```bash
root@ubuntu-server:~/lab-os/chapter10-services# sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.keamanan.backup
root@ubuntu-server:~/lab-os/chapter10-services# sudo sed -i 's/^#PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
root@ubuntu-server:~/lab-os/chapter10-services# sudo sed -i 's/^PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
root@ubuntu-server:~/lab-os/chapter10-services# sudo sed -i 's/^#MaxAuthTries.*/MaxAuthTries 3/' /etc/ssh/sshd_config
root@ubuntu-server:~/lab-os/chapter10-services# sudo sed -i 's/^MaxAuthTries.*/MaxAuthTries 3/' /etc/ssh/sshd_config
root@ubuntu-server:~/lab-os/chapter10-services# sudo sshd -t
if [ $? -eq 0 ]; then
    echo "Sintaks Valid. Melakukan Reload..."
    sudo systemctl reload ssh
else
    echo "Sintaks Error! Periksa kembali file konfigurasi."
fi
Sintaks Valid. Melakukan Reload...
root@ubuntu-server:~/lab-os/chapter10-services# grep -E "PermitRoot|MaxAuth" /etc/ssh/sshd_config
PermitRootLogin no
MaxAuthTries 3
# the setting of "PermitRootLogin prohibit-password".
root@ubuntu-server:~/lab-os/chapter10-services# journalctl -u ssh -n 20 --no-pager
May 15 16:49:20 ubuntu-server sshd[6671]: Received signal 15; terminating.
May 15 16:49:20 ubuntu-server systemd[1]: ssh.service: Deactivated successfully.
May 15 16:49:21 ubuntu-server systemd[1]: Stopped ssh.service - OpenBSD Secure Shell server.
May 15 16:49:21 ubuntu-server systemd[1]: Starting ssh.service - OpenBSD Secure Shell server...
May 15 16:49:21 ubuntu-server sshd[6697]: Server listening on 0.0.0.0 port 22.
May 15 16:49:21 ubuntu-server sshd[6697]: Server listening on :: port 22.
May 15 16:49:21 ubuntu-server systemd[1]: Started ssh.service - OpenBSD Secure Shell server.
May 15 16:54:07 ubuntu-server sshd[6697]: Received signal 15; terminating.
May 15 16:54:07 ubuntu-server systemd[1]: Stopping ssh.service - OpenBSD Secure Shell server...
May 15 16:54:07 ubuntu-server systemd[1]: ssh.service: Deactivated successfully.
May 15 16:54:07 ubuntu-server systemd[1]: Stopped ssh.service - OpenBSD Secure Shell server.
May 15 16:54:07 ubuntu-server systemd[1]: Starting ssh.service - OpenBSD Secure Shell server...
May 15 16:54:07 ubuntu-server sshd[6730]: Server listening on 0.0.0.0 port 22.
May 15 16:54:07 ubuntu-server sshd[6730]: Server listening on :: port 22.
May 15 16:54:07 ubuntu-server systemd[1]: Started ssh.service - OpenBSD Secure Shell server.
May 15 16:56:30 ubuntu-server systemd[1]: Reloading ssh.service - OpenBSD Secure Shell server...
May 15 16:56:30 ubuntu-server sshd[6730]: Received SIGHUP; restarting.
May 15 16:56:30 ubuntu-server sshd[6730]: Server listening on 0.0.0.0 port 22.
May 15 16:56:30 ubuntu-server sshd[6730]: Server listening on :: port 22.
May 15 16:56:30 ubuntu-server systemd[1]: Reloaded ssh.service - OpenBSD Secure Shell server.
```

# 1.7 Latihan

# Latihan 10.1 Audit Layanan dan Analisis Boot
1. Jalankan systemctl list-units –type=service –state=running dan catat semua
layanan aktif. Pilih tiga layanan yang kamu kenal, periksa status masing-masing dengan
systemctl status, dan jelaskan fungsinya.
```bash
root@ubuntu-server:~/lab-os/chapter10-services# systemctl list-units --type=service --state=running
  UNIT                        LOAD   ACTIVE SUB     DESCRIPTION
  cron.service                loaded active running Regular background program processing daemon
  dbus.service                loaded active running D-Bus System Message Bus
  demo-web.service            loaded active running Demo Web Server Praktek Bab 10 Modifikasi
  fwupd.service               loaded active running Firmware update daemon
  getty@tty1.service          loaded active running Getty on tty1
  ModemManager.service        loaded active running Modem Manager
  multipathd.service          loaded active running Device-Mapper Multipath Device Controller
  polkit.service              loaded active running Authorization Manager
  rsyslog.service             loaded active running System Logging Service
  ssh.service                 loaded active running OpenBSD Secure Shell server
  systemd-journald.service    loaded active running Journal Service
  systemd-logind.service      loaded active running User Login Management
  systemd-networkd.service    loaded active running Network Configuration
  systemd-resolved.service    loaded active running Network Name Resolution
  systemd-timesyncd.service   loaded active running Network Time Synchronization
  systemd-udevd.service       loaded active running Rule-based Manager for Device Events and Files
  udisks2.service             loaded active running Disk Manager
  unattended-upgrades.service loaded active running Unattended Upgrades Shutdown
  upower.service              loaded active running Daemon for power management
  user@1000.service           loaded active running User Manager for UID 1000

Legend: LOAD   → Reflects whether the unit definition was properly loaded.
        ACTIVE → The high-level unit activation state, i.e. generalization of SUB.
        SUB    → The low-level unit activation state, values depend on unit type.

20 loaded units listed.
```
|Nama Layanan	|Cara Cek Status	|Penjelasan Fungsi|
|--------------|--------------|-----------------|
|ssh.service	|systemctl status ssh	|Menyediakan akses remote terenkripsi sehingga kita bisa mengelola server dari jarak jauh.|
|cron.service	|systemctl status cron	|Penjadwal tugas otomatis (job scheduler) yang menjalankan perintah pada waktu yang telah ditentukan.|
|rsyslog.service	|systemctl status rsyslog	|Mengelola pengumpulan dan penyimpanan log sistem (catatan aktivitas) ke dalam file di direktori /var/log.|

2. Jalankan systemd-analyze blame dan identifikasi lima layanan dengan waktu inisialisasi
terlama. Tampilkan hasilnya menggunakan pipeline: systemd-analyze blame | head -5.
```bash
root@ubuntu-server:~/lab-os/chapter10-services# systemd-analyze blame | head -5
1min 31.828s apt-daily.service
     57.856s apt-daily-upgrade.service
      4.029s plocate-updatedb.service
      3.001s man-db.service
      2.964s motd-news.service
```
|Nama Layanan	|Waktu Inisialisasi|
|----------|-----------|
|apt-daily.service	|1min 31.828s|
|apt-daily-upgrade.service	|57.856s|
|plocate-updatedb.service	|4.029s|
|man-db.service	|3.001s|
|motd-news.service	|2.964s|

3. Jalankan systemctl –failed dan dokumentasikan hasilnya. Jika ada layanan yang gagal, cari
tahu penyebabnya dengan journalctl -u nama-layanan -n 30.
```bash
root@ubuntu-server:~/lab-os/chapter10-services# systemctl --failed
  UNIT LOAD ACTIVE SUB DESCRIPTION

0 loaded units listed.
root@ubuntu-server:~/lab-os/chapter10-services# journalctl -u nama-layanan -n 30 --no-pager
-- No entries --
```

# Latihan 10.2 Layanan Kustom dengan Restart Otomatis
1. Buat skrip Bash (referensi Bab 7) bernama monitor-disk.sh yang setiap 30 detik menuliskan
penggunaan disk ke berkas log. Gunakan df -h dan date.
```bash
root@ubuntu-server:~/lab-os/chapter10-services# cd ~/lab-os/chapter10-services
root@ubuntu-server:~/lab-os/chapter10-services# nano monitor-disk.sh
isi :
#!/bin/bash
while true
do
    echo "$(date): Penggunaan disk saat ini:" >> /tmp/disk-monitor.log
    df -h / >> /tmp/disk-monitor.log
    sleep 30
done
root@ubuntu-server:~/lab-os/chapter10-services# chmod +x ~/lab-os/chapter10-services/monitor-disk.sh
```

2. Buat berkas unit /etc/systemd/system/monitor-disk.service untuk menjalankan skrip
tersebut dengan konfigurasi: Restart=always, RestartSec=5s, dan berjalan sebagai peng-
guna kamu sendiri.
```bash
root@ubuntu-server:~/lab-os/chapter10-services# sudo nano /etc/systemd/system/monitor-disk.service
isi :
[Unit]
Description=Layanan Monitor Disk Latihan 10.2
After=network.target

[Service]
Type=simple
User=root
ExecStart=/root/lab-os/chapter10-services/monitor-disk.sh
# Pengaturan restart otomatis
Restart=always
RestartSec=5s

[Install]
WantedBy=multi-user.target
```

3. Aktifkan dan jalankan layanan. Verifikasi dengan systemctl status dan pastikan log masuk
ke journal.
```bash
root@ubuntu-server:~/lab-os/chapter10-services# sudo systemctl daemon-reload
root@ubuntu-server:~/lab-os/chapter10-services# sudo systemctl enable --now monitor-disk.service
root@ubuntu-server:~/lab-os/chapter10-services# systemctl status monitor-disk
● monitor-disk.service - Layanan Monitor Disk Latihan 10.2
     Loaded: loaded (/etc/systemd/system/monitor-disk.service; enabled; preset: enabled)
     Active: active (running) since Fri 2026-05-15 17:10:38 UTC; 8min ago
   Main PID: 6913 (monitor-disk.sh)
      Tasks: 2 (limit: 4697)
     Memory: 680.0K (peak: 1.6M)
        CPU: 1.740s
     CGroup: /system.slice/monitor-disk.service
             ├─6913 /bin/bash /root/lab-os/chapter10-services/monitor-disk.sh
             └─7079 sleep 30
May 15 17:10:38 ubuntu-server systemd[1]: Started monitor-disk.service - Layanan Monitor Disk Latiha>
root@ubuntu-server:~/lab-os/chapter10-services# tail -f /tmp/disk-monitor.log
/dev/sda2        52G  8.5G   41G  18% /
Fri May 15 05:16:42 PM UTC 2026: Penggunaan disk saat ini:
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda2        52G  8.5G   41G  18% /
Fri May 15 05:17:12 PM UTC 2026: Penggunaan disk saat ini:
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda2        52G  8.5G   41G  18% /
Fri May 15 05:17:43 PM UTC 2026: Penggunaan disk saat ini:
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda2        52G  8.5G   41G  18% /
Fri May 15 05:18:13 PM UTC 2026: Penggunaan disk saat ini:
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda2        52G  8.5G   41G  18% /
```

4. Simulasikan crash dengan membunuh proses secara paksa (kill -9), tunggu 10 detik, dan
verifikasi bahwa layanan hidup kembali secara otomatis.
```bash
root@ubuntu-server:~/lab-os/chapter10-services# PID_LAMA=$(systemctl show monitor-disk --property=MainPID --value)
echo "PID skrip kamu sekarang adalah: $PID_LAMA"
PID skrip kamu sekarang adalah: 6913
root@ubuntu-server:~/lab-os/chapter10-services# sudo kill -9 $PID_LAMA
root@ubuntu-server:~/lab-os/chapter10-services# echo "Menunggu 10 detik agar systemd sadar dan merestart skrip..."
sleep 10
Menunggu 10 detik agar systemd sadar dan merestart skrip...
root@ubuntu-server:~/lab-os/chapter10-services# PID_BARU=$(systemctl show monitor-disk --property=MainPID --value)
echo "PID baru setelah dihidupkan ulang: $PID_BARU"
PID baru setelah dihidupkan ulang: 7102
root@ubuntu-server:~/lab-os/chapter10-services# systemctl status monitor-disk
● monitor-disk.service - Layanan Monitor Disk Latihan 10.2
     Loaded: loaded (/etc/systemd/system/monitor-disk.service; enabled; preset: enabled)
     Active: active (running) since Fri 2026-05-15 17:21:13 UTC; 19s ago
   Main PID: 7102 (monitor-disk.sh)
      Tasks: 2 (limit: 4697)
     Memory: 568.0K (peak: 1.3M)
        CPU: 136ms
     CGroup: /system.slice/monitor-disk.service
             ├─7102 /bin/bash /root/lab-os/chapter10-services/monitor-disk.sh
             └─7105 sleep 30

May 15 17:21:12 ubuntu-server systemd[1]: monitor-disk.service: Scheduled restart job, restart count>
May 15 17:21:13 ubuntu-server systemd[1]: Started monitor-disk.service - Layanan Monitor Disk Latiha>
```

5. Bersihkan: nonaktifkan layanan dan hapus berkas unit setelah selesai.
```bash
root@ubuntu-server:~/lab-os/chapter10-services# sudo systemctl disable --now monitor-disk.service
Removed "/etc/systemd/system/multi-user.target.wants/monitor-disk.service".
root@ubuntu-server:~/lab-os/chapter10-services# sudo rm /etc/systemd/system/monitor-disk.service
root@ubuntu-server:~/lab-os/chapter10-services# sudo systemctl daemon-reload
root@ubuntu-server:~/lab-os/chapter10-services# rm /tmp/disk-monitor.log
```

# Latihan 10.3 Investigasi Log dan Keamanan SSH
1. Gunakan journalctl -b -p err untuk menemukan semua error sejak boot terakhir. Simpan
hasilnya ke berkas dan hitung jumlah baris dengan wc -l.
```bash
root@ubuntu-server:~/lab-os/chapter10-services# mkdir -p ~/lab-os/chapter10-services && cd ~/lab-os/chapter10-services
root@ubuntu-server:~/lab-os/chapter10-services# journalctl -b -p err --no-pager > error-boot.txt
root@ubuntu-server:~/lab-os/chapter10-services# echo "Jumlah baris error ditemukan:"
wc -l < error-boot.txt
Jumlah baris error ditemukan:
253
```

2. Lakukan tiga perubahan keamanan pada /etc/ssh/sshd_config: tambahkan PermitRootLogin
no, MaxAuthTries 3, dan LoginGraceTime 30. Ikuti alur aman: backup, edit, validasi sshd
-t, reload.
```bash
root@ubuntu-server:~/lab-os/chapter10-services# sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.awal.bak
root@ubuntu-server:~/lab-os/chapter10-services# sudo sed -i 's/^#PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
sudo sed -i 's/^#MaxAuthTries.*/MaxAuthTries 3/' /etc/ssh/sshd_config
sudo sed -i 's/^#LoginGraceTime.*/LoginGraceTime 30/' /etc/ssh/sshd_config
root@ubuntu-server:~/lab-os/chapter10-services# sudo sed -i 's/^PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
sudo sed -i 's/^MaxAuthTries.*/MaxAuthTries 3/' /etc/ssh/sshd_config
sudo sed -i 's/^LoginGraceTime.*/LoginGraceTime 30/' /etc/ssh/sshd_config
root@ubuntu-server:~/lab-os/chapter10-services# sudo sshd -t
echo "Status validasi (0 = aman): $?"
Status validasi (0 = aman): 0
root@ubuntu-server:~/lab-os/chapter10-services# sudo systemctl reload ssh
```

3. Setelah reload, verifikasi tiga hal: layanan masih berjalan (systemctl status ssh), port
masih mendengarkan (ss -tlnp | grep ssh), dan konfigurasi baru terbaca (grep -E
"PermitRoot|MaxAuth|GraceTime" /etc/ssh/sshd_config).
```bash
root@ubuntu-server:~/lab-os/chapter10-services# systemctl status ssh | grep "Active:"
     Active: active (running) since Fri 2026-05-15 16:54:07 UTC; 33min ago
root@ubuntu-server:~/lab-os/chapter10-services# ss -tlnp | grep ssh
LISTEN 0      4096         0.0.0.0:22        0.0.0.0:*    users:(("sshd",pid=6730,fd=3))
LISTEN 0      4096            [::]:22           [::]:*    users:(("sshd",pid=6730,fd=4))
root@ubuntu-server:~/lab-os/chapter10-services# grep -E "PermitRoot|MaxAuth|GraceTime" /etc/ssh/sshd_config
LoginGraceTime 30
PermitRootLogin no
MaxAuthTries 3
```

4. Kembalikan konfigurasi SSH ke kondisi semula menggunakan berkas backup.
```bash
root@ubuntu-server:~/lab-os/chapter10-services# sudo cp /etc/ssh/sshd_config.awal.bak /etc/ssh/sshd_config
root@ubuntu-server:~/lab-os/chapter10-services# sudo sshd -t && sudo systemctl reload ssh
root@ubuntu-server:~/lab-os/chapter10-services# grep -E "PermitRoot|MaxAuth|GraceTime" /etc/ssh/sshd_config
PermitRootLogin no
MaxAuthTries 3
```