# Laporan Pertemuan ke-4 Sistem Operasi

**Tanggal:** 09 maret 2026  
**Disusun Oleh:** Mukhammad Raffi Zabra  
**NIM:** 254107020059  
**Kelas/No:** TI-1G/24  

---

## Tugas Pendahuluan
      Jawablah pertanyaan-pertanyaan di bawah ini :

1. Apa yang dimaksud perintah-perintah direktory : pwd, cd, mkdir, rmdir.

- pwd = Menampilkan lokasi direktori kerja saat ini, misalnya /home/Rappizr7/Documents
- cd = Berpindah dari satu direktori ke direktori lain
- mkdir = Membuat direktori baru. Bisa buat banyak direktori sekaligus atau bertingkat
- rmdir = Menghapus direktori kosong, kalau ada isi biasanya si terjadi error

2. Apa yang dimaksud perintah-perintah manipulasi file : cp, mv dan rm (sertakan
format yang digunakan)

- cp = fungsinya itu mencopy file. contoh untuk copy file cp backup backup1, sedangkan copy folder itu contohnya cp -r folder1 folder2
- mv = fungsinya bisa memindahkan file/rename. contoh untuk pindahkan file itu mv contoh1 A/B, dan kalau rename itu mv contoh1 contoh2 
- rm = fungsinya untuk menghapus file. contoh rm contoh1

3. Jelaskan perbedaan Symbolic link menggunakan hard link (direct) dan soft link
(indirect).

- Hard link (direct): nama file kedua menunjuk ke data/inode yang sama → link count naik, perubahan di satu nama ikut di nama lain, tidak bisa beda partisi/filesystem, dan tidak bisa link ke file yang belum ada. 

- Soft link (indirect): file bertipe l yang menunjuk ke path/asal file → link count file asal tidak berubah, bisa beda partisi/filesystem, dan bisa dibuat walau target belum ada (tapi bisa jadi broken kalau target hilang).

4. Tuliskan maksud perintah-perintah : file, find, which, locate dan grep.

- file = Menentukan jenis/tipe suatu file berdasarkan isinya. contohnya file doc1
- find = Mencari file di pohon direktori (real-time scan). contohnya find /home -name "*.txt" -print
- which = Menunjukkan lokasi exec dari sebuah command berdasarkan env PATH. contohnya which ls
- locate = Mencari file dengan cepat memakai index database, lebih cepat dari find tapi tergantug update database. contohnya locate "*.txt"
- grep = mencari teks di dalam file. contohnya grep hallo *.txt


## PERCOBAAN:
1. Login sebagai user.
2. Bukalah Console Terminal dan lakukan percobaan-percobaan di bawah ini.
Perhatikan hasilnya.
3. Selesaikan soal-soal latihan

## Percobaan 1 : Direktory
1. Melihat direktori HOME
$ pwd
Rappizr7@ubuntu-server:~$ pwd
/home/Rappizr7

$ echo $HOME
Rappizr7@ubuntu-server:~$ echo $HOME
/home/Rappizr7

2. Melihat direktori aktual dan parent direktori
$ pwd
Rappizr7@ubuntu-server:~$ pwd
/home/Rappizr7

$ cd .
Rappizr7@ubuntu-server:~$ cd .

$ pwd
Rappizr7@ubuntu-server:~$ pwd
/home/Rappizr7

$ cd ..
Rappizr7@ubuntu-server:~$ cd ..

$ pwd
Rappizr7@ubuntu-server:/home$ pwd
/home

$ cd
Rappizr7@ubuntu-server:/home$ cd
Rappizr7@ubuntu-server:~$

3. Membuat satu direktori, lebih dari satu direktori atau sub direktori
$ pwd
Rappizr7@ubuntu-server:~$ pwd
/home/Rappizr7

$ mkdir A B C A/D A/E B/F A/D/A
Rappizr7@ubuntu-server:~$ mkdir A B C A/D A/E B/F A/D/A
Rappizr7@ubuntu-server:~$

$ ls -l
^CRappizr7@ubuntu-server:~$ ls -l
total 76
drwxrwxr-x 4 Rappizr7 Rappizr7  4096 Mar  9 15:02 A
drwxrwxr-x 3 Rappizr7 Rappizr7  4096 Mar  9 15:02 B
-rw-rw-r-- 1 Rappizr7 Rappizr7  1066 Feb 28 17:29 backup_20260228_172946.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7  1066 Feb 28 17:35 backup_20260228_173505.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7   146 Feb 28 17:35 backup-error.log
-rwxrwxr-x 1 Rappizr7 Rappizr7   687 Feb 28 17:29 backup_script.sh
-rw-rw-r-- 1 Rappizr7 Rappizr7   658 Feb 28 17:35 backup-success.log
-rw-r--r-- 1 root     root        15 Feb 18 09:02 blacklist-loop.conf
drwxrwxr-x 2 Rappizr7 Rappizr7  4096 Mar  9 15:02 C
-rw-rw-r-- 1 Rappizr7 Rappizr7 16828 Feb 28 17:24 config-files.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7     0 Feb 28 16:56 error.log
-rw-rw-r-- 1 Rappizr7 Rappizr7   807 Feb 28 16:56 large-logs.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7  1355 Feb 28 17:14 monitoring_log.txt
-rwxrwxr-x 1 Rappizr7 Rappizr7   978 Feb 28 17:13 monitor.sh
-rw-r--r-- 1 root     root       194 Feb 20 19:52 server.log
-rw-rw-r-- 1 Rappizr7 Rappizr7   251 Feb 28 17:08 sorted-users.txt
Rappizr7@ubuntu-server:~$

$ ls -l A
Rappizr7@ubuntu-server:~$ ls -l A
total 8
drwxrwxr-x 3 Rappizr7 Rappizr7 4096 Mar  9 15:02 D
drwxrwxr-x 2 Rappizr7 Rappizr7 4096 Mar  9 15:02 E
Rappizr7@ubuntu-server:~$

$ ls -l A/D
Rappizr7@ubuntu-server:~$ ls -l A/D
total 4
drwxrwxr-x 2 Rappizr7 Rappizr7 4096 Mar  9 15:02 A
Rappizr7@ubuntu-server:~$Rappizr7@ubuntu-server:~$ ls -l A/D
total 4
drwxrwxr-x 2 Rappizr7 Rappizr7 4096 Mar  9 15:02 A
Rappizr7@ubuntu-server:~$

4. Menghapus satu atau lebih direktori hanya dapat dilakukan pada direktori
kosong dan hanya dapat dihapus oleh pemiliknya kecuali bila diberikan ijin
aksesnya
$ rmdir B (Terdapat pesan error, mengapa ?)
Rappizr7@ubuntu-server:~$ rmdir B
rmdir: failed to remove 'B': Directory not empty
karna : Karena direktori B tidak kosong

$ ls -l B
Rappizr7@ubuntu-server:~$ ls -l B
total 4
drwxrwxr-x 2 Rappizr7 Rappizr7 4096 Mar  9 15:02 F

$ rmdir B/F B
Rappizr7@ubuntu-server:~$ rmdir B/F B

$ ls -l B (Terdapat pesan error, mengapa ?)
Rappizr7@ubuntu-server:~$ ls -l B
ls: cannot access 'B': No such file or directory
karna : karena direktori B sudah dihapus

5. Navigasi direktori dengan instruksi cd untuk pindah dari satu direktori ke
direktori lain.
$ pwd
Rappizr7@ubuntu-server:~$ pwd
/home/Rappizr7

$ ls -l
Rappizr7@ubuntu-server:~$ ls -l
total 72
drwxrwxr-x 4 Rappizr7 Rappizr7  4096 Mar  9 15:02 A
-rw-rw-r-- 1 Rappizr7 Rappizr7  1066 Feb 28 17:29 backup_20260228_172946.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7  1066 Feb 28 17:35 backup_20260228_173505.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7   146 Feb 28 17:35 backup-error.log
-rwxrwxr-x 1 Rappizr7 Rappizr7   687 Feb 28 17:29 backup_script.sh
-rw-rw-r-- 1 Rappizr7 Rappizr7   658 Feb 28 17:35 backup-success.log
-rw-r--r-- 1 root     root        15 Feb 18 09:02 blacklist-loop.conf
drwxrwxr-x 2 Rappizr7 Rappizr7  4096 Mar  9 15:02 C
-rw-rw-r-- 1 Rappizr7 Rappizr7 16828 Feb 28 17:24 config-files.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7     0 Feb 28 16:56 error.log
-rw-rw-r-- 1 Rappizr7 Rappizr7   807 Feb 28 16:56 large-logs.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7  1355 Feb 28 17:14 monitoring_log.txt
-rwxrwxr-x 1 Rappizr7 Rappizr7   978 Feb 28 17:13 monitor.sh
-rw-r--r-- 1 root     root       194 Feb 20 19:52 server.log
-rw-rw-r-- 1 Rappizr7 Rappizr7   251 Feb 28 17:08 sorted-users.txt

$ cd A
Rappizr7@ubuntu-server:~$ cd A
Rappizr7@ubuntu-server:~/A$

$ pwd
Rappizr7@ubuntu-server:~/A$ pwd
/home/Rappizr7/A

$ cd ..
Rappizr7@ubuntu-server:~/A$ cd ..
Rappizr7@ubuntu-server:~$

$ pwd
Rappizr7@ubuntu-server:~$ pwd
/home/Rappizr7

$ cd /home/<user>/C
Rappizr7@ubuntu-server:~$ cd /home/Rappizr7/C
Rappizr7@ubuntu-server:~/C$

$ pwd
Rappizr7@ubuntu-server:~/C$ pwd
/home/Rappizr7/C

$ cd /<user/C (Terdapat pesan error, mengapa ?)
Rappizr7@ubuntu-server:~/C$ cd /<Rappizr7/C
-bash: Rappizr7/C: No such file or directory
Karna : karena penulisan path salah

$ pwd
Rappizr7@ubuntu-server:~/C$ pwd
/home/Rappizr7/C


## Percobaan 2 : Manipulasi file
1. Perintah cp untuk mengkopi file atau seluruh direktori
$ cat > contoh Membuat sebuah file [Ctrl-d]
Rappizr7@ubuntu-server:~$ cat > contoh
membuat sebuah file

$ cp contoh contoh1
Rappizr7@ubuntu-server:~$ cp contoh contoh1

$ ls -l
Rappizr7@ubuntu-server:~$ ls -l
total 80
drwxrwxr-x 4 Rappizr7 Rappizr7  4096 Mar  9 15:02 A
-rw-rw-r-- 1 Rappizr7 Rappizr7  1066 Feb 28 17:29 backup_20260228_172946.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7  1066 Feb 28 17:35 backup_20260228_173505.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7   146 Feb 28 17:35 backup-error.log
-rwxrwxr-x 1 Rappizr7 Rappizr7   687 Feb 28 17:29 backup_script.sh
-rw-rw-r-- 1 Rappizr7 Rappizr7   658 Feb 28 17:35 backup-success.log
-rw-r--r-- 1 root     root        15 Feb 18 09:02 blacklist-loop.conf
drwxrwxr-x 2 Rappizr7 Rappizr7  4096 Mar  9 15:40 C
-rw-rw-r-- 1 Rappizr7 Rappizr7 16828 Feb 28 17:24 config-files.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7    19 Mar  9 15:42 contoh
-rw-rw-r-- 1 Rappizr7 Rappizr7    19 Mar  9 15:42 contoh1
-rw-rw-r-- 1 Rappizr7 Rappizr7     0 Feb 28 16:56 error.log
-rw-rw-r-- 1 Rappizr7 Rappizr7   807 Feb 28 16:56 large-logs.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7  1355 Feb 28 17:14 monitoring_log.txt
-rwxrwxr-x 1 Rappizr7 Rappizr7   978 Feb 28 17:13 monitor.sh
-rw-r--r-- 1 root     root       194 Feb 20 19:52 server.log
-rw-rw-r-- 1 Rappizr7 Rappizr7   251 Feb 28 17:08 sorted-users.txt

$ cp contoh A
Rappizr7@ubuntu-server:~$ cp contoh A

$ ls –l A
Rappizr7@ubuntu-server:~$ ls -l A
total 12
-rw-rw-r-- 1 Rappizr7 Rappizr7   19 Mar  9 15:42 contoh
drwxrwxr-x 3 Rappizr7 Rappizr7 4096 Mar  9 15:02 D
drwxrwxr-x 2 Rappizr7 Rappizr7 4096 Mar  9 15:02 E

$ cp contoh contoh1 A/D
Rappizr7@ubuntu-server:~$ cp contoh contoh1 A/D

$ ls –l A/D
Rappizr7@ubuntu-server:~$ ls -l A/D
total 12
drwxrwxr-x 2 Rappizr7 Rappizr7 4096 Mar  9 15:02 A
-rw-rw-r-- 1 Rappizr7 Rappizr7   19 Mar  9 15:43 contoh
-rw-rw-r-- 1 Rappizr7 Rappizr7   19 Mar  9 15:43 contoh1

2. Perintah mv untuk memindah file
$ mv contoh contoh2
Rappizr7@ubuntu-server:~$ mv contoh contoh2

$ ls -l
Rappizr7@ubuntu-server:~$ ls -l
total 80
drwxrwxr-x 4 Rappizr7 Rappizr7  4096 Mar  9 15:42 A
-rw-rw-r-- 1 Rappizr7 Rappizr7  1066 Feb 28 17:29 backup_20260228_172946.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7  1066 Feb 28 17:35 backup_20260228_173505.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7   146 Feb 28 17:35 backup-error.log
-rwxrwxr-x 1 Rappizr7 Rappizr7   687 Feb 28 17:29 backup_script.sh
-rw-rw-r-- 1 Rappizr7 Rappizr7   658 Feb 28 17:35 backup-success.log
-rw-r--r-- 1 root     root        15 Feb 18 09:02 blacklist-loop.conf
drwxrwxr-x 2 Rappizr7 Rappizr7  4096 Mar  9 15:40 C
-rw-rw-r-- 1 Rappizr7 Rappizr7 16828 Feb 28 17:24 config-files.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7    19 Mar  9 15:42 contoh1
-rw-rw-r-- 1 Rappizr7 Rappizr7    19 Mar  9 15:42 contoh2
-rw-rw-r-- 1 Rappizr7 Rappizr7     0 Feb 28 16:56 error.log
-rw-rw-r-- 1 Rappizr7 Rappizr7   807 Feb 28 16:56 large-logs.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7  1355 Feb 28 17:14 monitoring_log.txt
-rwxrwxr-x 1 Rappizr7 Rappizr7   978 Feb 28 17:13 monitor.sh
-rw-r--r-- 1 root     root       194 Feb 20 19:52 server.log
-rw-rw-r-- 1 Rappizr7 Rappizr7   251 Feb 28 17:08 sorted-users.txt

$ mv contoh1 contoh2 A/D
Rappizr7@ubuntu-server:~$ mv contoh1 contoh2 A/D

$ ls –l A/D
Rappizr7@ubuntu-server:~$ ls -l A/D
total 16
drwxrwxr-x 2 Rappizr7 Rappizr7 4096 Mar  9 15:02 A
-rw-rw-r-- 1 Rappizr7 Rappizr7   19 Mar  9 15:43 contoh
-rw-rw-r-- 1 Rappizr7 Rappizr7   19 Mar  9 15:42 contoh1
-rw-rw-r-- 1 Rappizr7 Rappizr7   19 Mar  9 15:42 contoh2

$ mv contoh contoh1 C
Rappizr7@ubuntu-server:~$ mv contoh contoh1 C
mv: cannot stat 'contoh': No such file or directory
mv: cannot stat 'contoh1': No such file or directory

$ ls –l C
Rappizr7@ubuntu-server:~$ ls -l C
total 8
-rw-rw-r-- 1 Rappizr7 Rappizr7 19 Mar  9 15:40 contoh
-rw-rw-r-- 1 Rappizr7 Rappizr7 19 Mar  9 15:40 contoh1

3. Perintah rm untuk menghapus file
$ rm contoh2
Rappizr7@ubuntu-server:~$ rm contoh2
rm: cannot remove 'contoh2': No such file or directory

$ ls -l
Rappizr7@ubuntu-server:~$ ls -l
total 72
drwxrwxr-x 4 Rappizr7 Rappizr7  4096 Mar  9 15:42 A
-rw-rw-r-- 1 Rappizr7 Rappizr7  1066 Feb 28 17:29 backup_20260228_172946.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7  1066 Feb 28 17:35 backup_20260228_173505.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7   146 Feb 28 17:35 backup-error.log
-rwxrwxr-x 1 Rappizr7 Rappizr7   687 Feb 28 17:29 backup_script.sh
-rw-rw-r-- 1 Rappizr7 Rappizr7   658 Feb 28 17:35 backup-success.log
-rw-r--r-- 1 root     root        15 Feb 18 09:02 blacklist-loop.conf
drwxrwxr-x 2 Rappizr7 Rappizr7  4096 Mar  9 15:40 C
-rw-rw-r-- 1 Rappizr7 Rappizr7 16828 Feb 28 17:24 config-files.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7     0 Feb 28 16:56 error.log
-rw-rw-r-- 1 Rappizr7 Rappizr7   807 Feb 28 16:56 large-logs.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7  1355 Feb 28 17:14 monitoring_log.txt
-rwxrwxr-x 1 Rappizr7 Rappizr7   978 Feb 28 17:13 monitor.sh
-rw-r--r-- 1 root     root       194 Feb 20 19:52 server.log
-rw-rw-r-- 1 Rappizr7 Rappizr7   251 Feb 28 17:08 sorted-users.txt

$ rm –i contoh
Rappizr7@ubuntu-server:~$ rm -i contoh
rm: cannot remove 'contoh': No such file or directory

$ rm –rf A C
Rappizr7@ubuntu-server:~$ rm -rf A C

$ ls -l
Rappizr7@ubuntu-server:~$ ls -l
total 64
-rw-rw-r-- 1 Rappizr7 Rappizr7  1066 Feb 28 17:29 backup_20260228_172946.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7  1066 Feb 28 17:35 backup_20260228_173505.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7   146 Feb 28 17:35 backup-error.log
-rwxrwxr-x 1 Rappizr7 Rappizr7   687 Feb 28 17:29 backup_script.sh
-rw-rw-r-- 1 Rappizr7 Rappizr7   658 Feb 28 17:35 backup-success.log
-rw-r--r-- 1 root     root        15 Feb 18 09:02 blacklist-loop.conf
-rw-rw-r-- 1 Rappizr7 Rappizr7 16828 Feb 28 17:24 config-files.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7     0 Feb 28 16:56 error.log
-rw-rw-r-- 1 Rappizr7 Rappizr7   807 Feb 28 16:56 large-logs.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7  1355 Feb 28 17:14 monitoring_log.txt
-rwxrwxr-x 1 Rappizr7 Rappizr7   978 Feb 28 17:13 monitor.sh
-rw-r--r-- 1 root     root       194 Feb 20 19:52 server.log
-rw-rw-r-- 1 Rappizr7 Rappizr7   251 Feb 28 17:08 sorted-users.txt


## Percobaan 3 : Symbolic Link
1. Membuat shortcut (file link)
$ echo "Hallo apa khabar" > halo.txt
Rappizr7@ubuntu-server:~$ echo "hallo apa khabar" > halo.txt

$ ls -l
Rappizr7@ubuntu-server:~$ ls -l
total 68
-rw-rw-r-- 1 Rappizr7 Rappizr7  1066 Feb 28 17:29 backup_20260228_172946.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7  1066 Feb 28 17:35 backup_20260228_173505.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7   146 Feb 28 17:35 backup-error.log
-rwxrwxr-x 1 Rappizr7 Rappizr7   687 Feb 28 17:29 backup_script.sh
-rw-rw-r-- 1 Rappizr7 Rappizr7   658 Feb 28 17:35 backup-success.log
-rw-r--r-- 1 root     root        15 Feb 18 09:02 blacklist-loop.conf
-rw-rw-r-- 1 Rappizr7 Rappizr7 16828 Feb 28 17:24 config-files.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7     0 Feb 28 16:56 error.log
-rw-rw-r-- 1 Rappizr7 Rappizr7    17 Mar  9 15:51 halo.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7   807 Feb 28 16:56 large-logs.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7  1355 Feb 28 17:14 monitoring_log.txt
-rwxrwxr-x 1 Rappizr7 Rappizr7   978 Feb 28 17:13 monitor.sh
-rw-r--r-- 1 root     root       194 Feb 20 19:52 server.log
-rw-rw-r-- 1 Rappizr7 Rappizr7   251 Feb 28 17:08 sorted-users.txt

$ ln halo.txt z
Rappizr7@ubuntu-server:~$ ln halo.txt z

$ ls -l
Rappizr7@ubuntu-server:~$ ls -l
total 72
-rw-rw-r-- 1 Rappizr7 Rappizr7  1066 Feb 28 17:29 backup_20260228_172946.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7  1066 Feb 28 17:35 backup_20260228_173505.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7   146 Feb 28 17:35 backup-error.log
-rwxrwxr-x 1 Rappizr7 Rappizr7   687 Feb 28 17:29 backup_script.sh
-rw-rw-r-- 1 Rappizr7 Rappizr7   658 Feb 28 17:35 backup-success.log
-rw-r--r-- 1 root     root        15 Feb 18 09:02 blacklist-loop.conf
-rw-rw-r-- 1 Rappizr7 Rappizr7 16828 Feb 28 17:24 config-files.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7     0 Feb 28 16:56 error.log
-rw-rw-r-- 2 Rappizr7 Rappizr7    17 Mar  9 15:51 halo.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7   807 Feb 28 16:56 large-logs.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7  1355 Feb 28 17:14 monitoring_log.txt
-rwxrwxr-x 1 Rappizr7 Rappizr7   978 Feb 28 17:13 monitor.sh
-rw-r--r-- 1 root     root       194 Feb 20 19:52 server.log
-rw-rw-r-- 1 Rappizr7 Rappizr7   251 Feb 28 17:08 sorted-users.txt
-rw-rw-r-- 2 Rappizr7 Rappizr7    17 Mar  9 15:51 z

$ cat z
Rappizr7@ubuntu-server:~$ cat z
hallo apa khabar

$ mkdir mydir
Rappizr7@ubuntu-server:~$ mkdir mydir

$ ln z mydir/halo.juga
Rappizr7@ubuntu-server:~$ ln z mydir/halo.juga

$ cat mydir/halo.juga
Rappizr7@ubuntu-server:~$ cat mydir/halo.juga
hallo apa khabar

$ ln -s z bye.txt
Rappizr7@ubuntu-server:~$ ln -s z bye.txt

$ ls -l bye.txt
Rappizr7@ubuntu-server:~$ ls -l bye.txt
lrwxrwxrwx 1 Rappizr7 Rappizr7 1 Mar  9 15:53 bye.txt -> z

$ cat bye.txt
Rappizr7@ubuntu-server:~$ cat bye.txt
hallo apa khabar


## Percobaan 4 : Melihat Isi File
$ ls –l
Rappizr7@ubuntu-server:~$ ls -l
total 76
-rw-rw-r-- 1 Rappizr7 Rappizr7  1066 Feb 28 17:29 backup_20260228_172946.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7  1066 Feb 28 17:35 backup_20260228_173505.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7   146 Feb 28 17:35 backup-error.log
-rwxrwxr-x 1 Rappizr7 Rappizr7   687 Feb 28 17:29 backup_script.sh
-rw-rw-r-- 1 Rappizr7 Rappizr7   658 Feb 28 17:35 backup-success.log
-rw-r--r-- 1 root     root        15 Feb 18 09:02 blacklist-loop.conf
lrwxrwxrwx 1 Rappizr7 Rappizr7     1 Mar  9 15:53 bye.txt -> z
-rw-rw-r-- 1 Rappizr7 Rappizr7 16828 Feb 28 17:24 config-files.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7     0 Feb 28 16:56 error.log
-rw-rw-r-- 3 Rappizr7 Rappizr7    17 Mar  9 15:51 halo.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7   807 Feb 28 16:56 large-logs.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7  1355 Feb 28 17:14 monitoring_log.txt
-rwxrwxr-x 1 Rappizr7 Rappizr7   978 Feb 28 17:13 monitor.sh
drwxrwxr-x 2 Rappizr7 Rappizr7  4096 Mar  9 15:53 mydir
-rw-r--r-- 1 root     root       194 Feb 20 19:52 server.log
-rw-rw-r-- 1 Rappizr7 Rappizr7   251 Feb 28 17:08 sorted-users.txt
-rw-rw-r-- 3 Rappizr7 Rappizr7    17 Mar  9 15:51 z

$ file halo.txt
Rappizr7@ubuntu-server:~$ file halo.txt
halo.txt: ASCII text

$ file bye.txt
Rappizr7@ubuntu-server:~$ file bye.txt
bye.txt: symbolic link to z


## Percobaan 5 : Mencari file
1. Perintah find
$ find /home –name “*.txt” –print > myerror.txt
Rappizr7@ubuntu-server:~$ find /home -name "*.txt" -print > myerror.txt

$ cat myerror.txt
Rappizr7@ubuntu-server:~$ cat myerror.txt
/home/Rappizr7/myerror.txt
/home/Rappizr7/bye.txt
/home/Rappizr7/sorted-users.txt
/home/Rappizr7/config-files.txt
/home/Rappizr7/monitoring_log.txt
/home/Rappizr7/halo.txt
/home/Rappizr7/large-logs.txt

$ find . –name “*.txt” –exec wc –l ‘{}’ ‘;’
Rappizr7@ubuntu-server:~$ find . -name "*.txt" -exec wc -l '{}' ';'
7 ./myerror.txt
1 ./bye.txt
34 ./sorted-users.txt
402 ./config-files.txt
48 ./monitoring_log.txt
1 ./halo.txt
10 ./large-logs.txt

2. Perintah which
$ which ls
Rappizr7@ubuntu-server:~$ witch ls
Command 'witch' not found, did you mean:
  command 'watch' from deb procps (2:4.0.4-4ubuntu3.2)
  command 'hitch' from deb hitch (1.7.2-1)
Try: sudo apt install <deb name>

3. Perintah locate
$ locate “*.txt”
/usr/share/vim/vim91/doc/usr_21.txt
/usr/share/vim/vim91/doc/usr_22.txt
/usr/share/vim/vim91/doc/usr_23.txt
/usr/share/vim/vim91/doc/usr_24.txt
/usr/share/vim/vim91/doc/usr_25.txt
/usr/share/vim/vim91/doc/usr_26.txt
/usr/share/vim/vim91/doc/usr_27.txt
/usr/share/vim/vim91/doc/usr_28.txt
/usr/share/vim/vim91/doc/usr_29.txt
/usr/share/vim/vim91/doc/usr_30.txt
/usr/share/vim/vim91/doc/usr_31.txt
/usr/share/vim/vim91/doc/usr_32.txt
/usr/share/vim/vim91/doc/usr_40.txt
/usr/share/vim/vim91/doc/usr_41.txt
/usr/share/vim/vim91/doc/usr_42.txt
/usr/share/vim/vim91/doc/usr_43.txt
/usr/share/vim/vim91/doc/usr_44.txt
/usr/share/vim/vim91/doc/usr_45.txt
/usr/share/vim/vim91/doc/usr_50.txt
/usr/share/vim/vim91/doc/usr_51.txt
/usr/share/vim/vim91/doc/usr_52.txt
/usr/share/vim/vim91/doc/usr_90.txt
/usr/share/vim/vim91/doc/usr_toc.txt
/usr/share/vim/vim91/doc/various.txt
/usr/share/vim/vim91/doc/version4.txt
/usr/share/vim/vim91/doc/version5.txt
/usr/share/vim/vim91/doc/version6.txt
/usr/share/vim/vim91/doc/version7.txt
/usr/share/vim/vim91/doc/version8.txt
/usr/share/vim/vim91/doc/version9.txt
/usr/share/vim/vim91/doc/vi_diff.txt
/usr/share/vim/vim91/doc/vim9.txt
/usr/share/vim/vim91/doc/vim9class.txt
/usr/share/vim/vim91/doc/visual.txt
/usr/share/vim/vim91/doc/windows.txt
/usr/share/vim/vim91/doc/workshop.txt
/usr/share/vim/vim91/pack/dist/opt/editorconfig/doc/editorconfig.txt
/usr/share/vim/vim91/pack/dist/opt/matchit/doc/matchit.txt
/usr/share/vim/vim91/tutor/README.ru.utf-8.txt
/usr/src/linux-headers-6.8.0-100/arch/sh/include/mach-ecovec24/mach/partner-jet-setup.txt
/usr/src/linux-headers-6.8.0-100/arch/sh/include/mach-kfr2r09/mach/partner-jet-setup.txt
/usr/src/linux-headers-6.8.0-100/scripts/head-object-list.txt
/usr/src/linux-headers-6.8.0-100/scripts/spelling.txt
/usr/src/linux-headers-6.8.0-100-generic/scripts/head-object-list.txt
/usr/src/linux-headers-6.8.0-100-generic/scripts/spelling.txt
/usr/src/linux-headers-6.8.0-101/arch/sh/include/mach-ecovec24/mach/partner-jet-setup.txt
/usr/src/linux-headers-6.8.0-101/arch/sh/include/mach-kfr2r09/mach/partner-jet-setup.txt
/usr/src/linux-headers-6.8.0-101/scripts/head-object-list.txt
/usr/src/linux-headers-6.8.0-101/scripts/spelling.txt
/usr/src/linux-headers-6.8.0-101-generic/scripts/head-object-list.txt
/usr/src/linux-headers-6.8.0-101-generic/scripts/spelling.txt
/var/lib/cloud/instances/iid-datasource-none/cloud-config.txt
/var/lib/cloud/instances/iid-datasource-none/user-data.txt
/var/lib/cloud/instances/iid-datasource-none/vendor-data.txt
/var/lib/cloud/instances/iid-datasource-none/vendor-data2.txt
/var/lib/ieee-data/iab.txt
/var/lib/ieee-data/mam.txt
/var/lib/ieee-data/oui.txt
/var/lib/ieee-data/oui36.txt
Rappizr7@ubuntu-server:~$

catatan : itu belum semua file.txt saya copy karna terlalu banyak


## Percobaan 6 : Mencari text pada file
$ grep Hallo *.txt
Rappizr7@ubuntu-server:~$ grep hallo *.txt
bye.txt:hallo apa khabar
halo.txt:hallo apa khabar


## LATIHAN:
1. Cobalah urutan perintah berikut :
$ cd
Rappizr7@ubuntu-server:~$ cd

$ pwd
Rappizr7@ubuntu-server:~$ pwd
/home/Rappizr7

$ ls –al
Rappizr7@ubuntu-server:~$ ls -al
total 120
drwxr-x--- 7 Rappizr7 Rappizr7  4096 Mar  9 15:56 .
drwxr-xr-x 3 root     root      4096 Feb 11 13:23 ..
-rw-rw-r-- 1 Rappizr7 Rappizr7  1066 Feb 28 17:29 backup_20260228_172946.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7  1066 Feb 28 17:35 backup_20260228_173505.tar.gz
-rw-rw-r-- 1 Rappizr7 Rappizr7   146 Feb 28 17:35 backup-error.log
-rwxrwxr-x 1 Rappizr7 Rappizr7   687 Feb 28 17:29 backup_script.sh
-rw-rw-r-- 1 Rappizr7 Rappizr7   658 Feb 28 17:35 backup-success.log
-rw------- 1 Rappizr7 Rappizr7   335 Feb 28 17:58 .bash_history
-rw-r--r-- 1 Rappizr7 Rappizr7   220 Mar 31  2024 .bash_logout
-rw-r--r-- 1 Rappizr7 Rappizr7  3771 Mar 31  2024 .bashrc
-rw-r--r-- 1 root     root        15 Feb 18 09:02 blacklist-loop.conf
lrwxrwxrwx 1 Rappizr7 Rappizr7     1 Mar  9 15:53 bye.txt -> z
drwx------ 2 Rappizr7 Rappizr7  4096 Feb 11 13:24 .cache
drwxrwxr-x 4 Rappizr7 Rappizr7  4096 Feb 28 17:13 .config
-rw-rw-r-- 1 Rappizr7 Rappizr7 16828 Feb 28 17:24 config-files.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7     0 Feb 28 16:56 error.log
-rw-rw-r-- 3 Rappizr7 Rappizr7    17 Mar  9 15:51 halo.txt
-rw-rw-r-- 1 Rappizr7 Rappizr7   807 Feb 28 16:56 large-logs.txt
drwxrwxr-x 3 Rappizr7 Rappizr7  4096 Feb 28 17:11 .local
-rw-rw-r-- 1 Rappizr7 Rappizr7  1355 Feb 28 17:14 monitoring_log.txt
-rwxrwxr-x 1 Rappizr7 Rappizr7   978 Feb 28 17:13 monitor.sh
drwxrwxr-x 2 Rappizr7 Rappizr7  4096 Mar  9 15:53 mydir
-rw-rw-r-- 1 Rappizr7 Rappizr7   202 Mar  9 15:58 myerror.txt
-rw-r--r-- 1 Rappizr7 Rappizr7   807 Mar 31  2024 .profile
-rw-r--r-- 1 root     root       194 Feb 20 19:52 server.log
-rw-rw-r-- 1 Rappizr7 Rappizr7   251 Feb 28 17:08 sorted-users.txt
drwx------ 2 Rappizr7 Rappizr7  4096 Feb 11 13:23 .ssh
-rw-r--r-- 1 Rappizr7 Rappizr7     0 Feb 11 13:24 .sudo_as_admin_successful
-rw-rw-r-- 3 Rappizr7 Rappizr7    17 Mar  9 15:51 z\

$ cd .
Rappizr7@ubuntu-server:~$ cd .

$ pwd
Rappizr7@ubuntu-server:~$ pwd
/home/Rappizr7

$ cd ..
Rappizr7@ubuntu-server:~$ cd ..
Rappizr7@ubuntu-server:/home$

$ pwd
Rappizr7@ubuntu-server:/home$ pwd
/home

$ ls -al
Rappizr7@ubuntu-server:/home$ ls -al
total 12
drwxr-xr-x  3 root     root     4096 Feb 11 13:23 .
drwxr-xr-x 24 root     root     4096 Feb 25 04:25 ..
drwxr-x---  7 Rappizr7 Rappizr7 4096 Mar  9 15:56 Rappizr7

$ cd ..
Rappizr7@ubuntu-server:/home$ cd ..
Rappizr7@ubuntu-server:/$

$ pwd
Rappizr7@ubuntu-server:/$ pwd
/

$ ls -al
Rappizr7@ubuntu-server:/$ ls -al
total 4194408
drwxr-xr-x  24 root root       4096 Feb 25 04:25 .
drwxr-xr-x  24 root root       4096 Feb 25 04:25 ..
-rw-r--r--   1 root root          9 Feb 22 08:39 ,bashrc
-rw-r--r--   1 root root         47 Feb 25 04:25 .bashrc
lrwxrwxrwx   1 root root          7 Apr 22  2024 bin -> usr/bin
drwxr-xr-x   2 root root       4096 Feb 26  2024 bin.usr-is-merged
drwxr-xr-x   3 root root       4096 Feb 28 17:36 boot
dr-xr-xr-x   2 root root       4096 Feb 11 13:19 cdrom
drwxr-xr-x  19 root root       4040 Feb 28 17:59 dev
drwxr-xr-x 112 root root       4096 Mar  9 16:02 etc
drwxr-xr-x   3 root root       4096 Feb 11 13:23 home
lrwxrwxrwx   1 root root          7 Apr 22  2024 lib -> usr/lib
lrwxrwxrwx   1 root root          9 Apr 22  2024 lib64 -> usr/lib64
drwxr-xr-x   2 root root       4096 Feb 26  2024 lib.usr-is-merged
drwx------   2 root root      16384 Feb 11 13:20 lost+found
drwxr-xr-x   2 root root       4096 Aug  5  2025 media
drwxr-xr-x   2 root root       4096 Aug  5  2025 mnt
drwxr-xr-x   2 root root       4096 Aug  5  2025 opt
drwxr-xr-x   3 root root       4096 Feb 18 12:50 praktikum-os
dr-xr-xr-x 208 root root          0 Feb 28 16:50 proc
drwx------   5 root root       4096 Feb 28 16:51 root
drwxr-xr-x  29 root root        960 Mar  9 16:01 run
lrwxrwxrwx   1 root root          8 Apr 22  2024 sbin -> usr/sbin
drwxr-xr-x   2 root root       4096 Dec 11  2024 sbin.usr-is-merged
drwxr-xr-x   2 root root       4096 Feb 11 13:23 snap
drwxr-xr-x   2 root root       4096 Aug  5  2025 srv
-rw-------   1 root root 4294967296 Feb 18 05:39 swapfile
dr-xr-xr-x  13 root root          0 Feb 28 16:50 sys
drwxrwxrwt  14 root root       4096 Mar  9 16:02 tmp
drwxr-xr-x  12 root root       4096 Aug  5  2025 usr
drwxr-xr-x  13 root root       4096 Feb 11 13:23 var

$ cd /etc
Rappizr7@ubuntu-server:/$ cd /etc
Rappizr7@ubuntu-server:/etc$

$ ls –al | more
Rappizr7@ubuntu-server:/etc$ ls -al | more
total 964
drwxr-xr-x 112 root root       4096 Mar  9 16:02 .
drwxr-xr-x  24 root root       4096 Feb 25 04:25 ..
-rw-r--r--   1 root root       3444 Jul  5  2023 adduser.conf
drwxr-xr-x   2 root root       4096 Mar  9 16:01 alternatives
drwxr-xr-x   2 root root       4096 Feb 22 07:31 apparmor
drwxr-xr-x   9 root root      12288 Feb 22 07:32 apparmor.d
drwxr-xr-x   3 root root       4096 Aug  5  2025 apport
drwxr-xr-x   9 root root       4096 Feb 11 13:20 apt
-rw-r--r--   1 root root       2319 Mar 31  2024 bash.bashrc
-rw-r--r--   1 root root         45 Aug  5  2025 bash_completion
drwxr-xr-x   2 root root       4096 Aug  5  2025 bash_completion.d
-rw-r--r--   1 root root        367 Aug  2  2022 bindresvport.blacklist
drwxr-xr-x   2 root root       4096 Jul  2  2025 binfmt.d
drwxr-xr-x   2 root root       4096 Aug  5  2025 byobu
drwxr-xr-x   3 root root       4096 Aug  5  2025 ca-certificates
-rw-r--r--   1 root root       6288 Aug  5  2025 ca-certificates.conf
drwxr-xr-x   5 root root       4096 Feb 11 13:25 cloud
drwxr-xr-x   2 root root       4096 Feb 11 13:20 console-setup
drwx------   2 root root       4096 Jul  2  2025 credstore
drwx------   2 root root       4096 Jul  2  2025 credstore.encrypted
drwxr-xr-x   2 root root       4096 Aug  5  2025 cron.d
drwxr-xr-x   2 root root       4096 Mar  9 16:01 cron.daily
drwxr-xr-x   2 root root       4096 Aug  5  2025 cron.hourly
drwxr-xr-x   2 root root       4096 Aug  5  2025 cron.monthly
-rw-r--r--   1 root root       1136 Aug  5  2025 crontab
drwxr-xr-x   2 root root       4096 Aug  5  2025 cron.weekly
drwxr-xr-x   2 root root       4096 Aug  5  2025 cron.yearly
drwxr-xr-x   2 root root       4096 Aug  5  2025 cryptsetup-initramfs
-rw-r--r--   1 root root         54 Aug  5  2025 crypttab
drwxr-xr-x   4 root root       4096 Aug  5  2025 dbus-1
-rw-r--r--   1 root root       2967 Apr 12  2024 debconf.conf
-rw-r--r--   1 root root         11 Apr 22  2024 debian_version
drwxr-xr-x   3 root root       4096 Feb 22 07:30 default
-rw-r--r--   1 root root       1706 Jul  5  2023 deluser.conf
drwxr-xr-x   2 root root       4096 Aug  5  2025 depmod.d
drwxr-xr-x   3 root root       4096 Aug  5  2025 dhcp
-rw-r--r--   1 root root       1429 May  7  2024 dhcpcd.conf
drwxr-xr-x   4 root root       4096 Feb 11 13:23 dpkg
-rw-r--r--   1 root root        685 Apr  8  2024 e2scrub.conf
-rw-r--r--   1 root root        106 Aug  5  2025 environment
-rw-r--r--   1 root root       1853 Oct 17  2022 ethertypes
drwxr-xr-x   4 root root       4096 Feb 11 13:22 fonts
-rw-r--r--   1 root root        473 Feb 18 05:40 fstab
-rw-r--r--   1 root root        694 Apr  8  2024 fuse.conf
drwxr-xr-x   4 root root       4096 Feb 22 07:32 fwupd
-rw-r--r--   1 root root       2584 Jan 31  2024 gai.conf
drwxr-xr-x   4 root root       4096 Feb 22 08:34 ghostscript
drwxr-xr-x   2 root root       4096 Feb 20 17:51 gnutls
drwxr-xr-x   2 root root       4096 Aug  5  2025 groff
-rw-r--r--   1 root root        796 Mar  9 16:01 group
-rw-r--r--   1 root root        781 Feb 11 13:23 group-
drwxr-xr-x   2 root root       4096 Feb 22 07:32 grub.d
-rw-r-----   1 root shadow      670 Mar  9 16:01 gshadow
-rw-r-----   1 root shadow      658 Feb 11 13:23 gshadow-
drwxr-xr-x   3 root root       4096 Aug  5  2025 gss
-rw-r--r--   1 root root       4436 Aug  5  2025 hdparm.conf
-rw-r--r--   1 root root         92 Apr 22  2024 host.conf
-rw-r--r--   1 root root   

$ cat passwd
Rappizr7@ubuntu-server:/etc$ cat passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
_apt:x:42:65534::/nonexistent:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:998:998:systemd Network Management:/:/usr/sbin/nologin
systemd-timesync:x:997:997:systemd Time Synchronization:/:/usr/sbin/nologin
dhcpcd:x:100:65534:DHCP Client Daemon,,,:/usr/lib/dhcpcd:/bin/false
messagebus:x:101:102::/nonexistent:/usr/sbin/nologin
systemd-resolve:x:992:992:systemd Resolver:/:/usr/sbin/nologin
pollinate:x:102:1::/var/cache/pollinate:/bin/false
polkitd:x:991:991:User for polkitd:/:/usr/sbin/nologin
syslog:x:103:104::/nonexistent:/usr/sbin/nologin
uuidd:x:104:105::/run/uuidd:/usr/sbin/nologin
tcpdump:x:105:107::/nonexistent:/usr/sbin/nologin
tss:x:106:108:TPM software stack,,,:/var/lib/tpm:/bin/false
landscape:x:107:109::/var/lib/landscape:/usr/sbin/nologin
fwupd-refresh:x:989:989:Firmware update daemon:/var/lib/fwupd:/usr/sbin/nologin
usbmux:x:108:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
Rappizr7:x:1000:1000:Rappizr7:/home/Rappizr7:/bin/bash
sshd:x:109:65534::/run/sshd:/usr/sbin/nologin

$ cd –
Rappizr7@ubuntu-server:/etc$ cd -
/

$ pwd
Rappizr7@ubuntu-server:/$ pwd
/

2. Lanjutkan penelusuran pohon pada sistem file menggunakan cd, ls, pwd dan cat. Telusuri direktory /bin, /usr/bin, /sbin, /tmp dan /boot.
- cd /bin
root@ubuntu-server:/bin# cd /bin
root@ubuntu-server:/bin# pwd
/bin
root@ubuntu-server:/bin# ls -l | head -15
total 134460
-rwxr-xr-x 1 root root       55744 Jun 22  2025 [
-rwxr-xr-x 1 root root       14640 Mar 31  2024 411toppm
-rwxr-xr-x 1 root root       18744 Aug 15  2025 aa-enabled
-rwxr-xr-x 1 root root       18744 Aug 15  2025 aa-exec
-rwxr-xr-x 1 root root       18736 Aug 15  2025 aa-features-abi
-rwxr-xr-x 1 root root        1622 Feb  6 17:52 acpidbg
-rwxr-xr-x 1 root root       16422 Jul  2  2025 add-apt-repository
-rwxr-xr-x 1 root root       14720 Sep 16 00:08 addpart
lrwxrwxrwx 1 root root          25 Mar 31  2024 animate -> /etc/alternatives/animate
lrwxrwxrwx 1 root root          29 Mar 31  2024 animate-im6 -> /etc/alternatives/animate-im6
-rwxr-xr-x 1 root root       14656 Mar 31  2024 animate-im6.q16
-rwxr-xr-x 1 root root       12556 Mar 31  2024 anytopnm
-rwxr-xr-x 1 root root        2322 Apr 18  2024 apport-bug
-rwxr-xr-x 1 root root       13625 Jul  8  2025 apport-cli

- cd /usr/bin
root@ubuntu-server:/bin# cd /usr/bin
root@ubuntu-server:/usr/bin# pwd
/usr/bin
root@ubuntu-server:/usr/bin# ls -l | head -15
total 134460
-rwxr-xr-x 1 root root       55744 Jun 22  2025 [
-rwxr-xr-x 1 root root       14640 Mar 31  2024 411toppm
-rwxr-xr-x 1 root root       18744 Aug 15  2025 aa-enabled
-rwxr-xr-x 1 root root       18744 Aug 15  2025 aa-exec
-rwxr-xr-x 1 root root       18736 Aug 15  2025 aa-features-abi
-rwxr-xr-x 1 root root        1622 Feb  6 17:52 acpidbg
-rwxr-xr-x 1 root root       16422 Jul  2  2025 add-apt-repository
-rwxr-xr-x 1 root root       14720 Sep 16 00:08 addpart
lrwxrwxrwx 1 root root          25 Mar 31  2024 animate -> /etc/alternatives/animate
lrwxrwxrwx 1 root root          29 Mar 31  2024 animate-im6 -> /etc/alternatives/animate-im6
-rwxr-xr-x 1 root root       14656 Mar 31  2024 animate-im6.q16
-rwxr-xr-x 1 root root       12556 Mar 31  2024 anytopnm
-rwxr-xr-x 1 root root        2322 Apr 18  2024 apport-bug
-rwxr-xr-x 1 root root       13625 Jul  8  2025 apport-cli

- cd /sbin
root@ubuntu-server:/usr/bin# cd /sbin
root@ubuntu-server:/sbin# pwd
/sbin
root@ubuntu-server:/sbin# ls -l | head -15
total 33300
-rwxr-xr-x 1 root root     39680 Aug 15  2025 aa-load
-rwxr-xr-x 1 root root      3225 Aug 15  2025 aa-remove-unknown
-rwxr-xr-x 1 root root     40000 Aug 15  2025 aa-status
-rwxr-xr-x 1 root root       137 Apr 12  2024 aa-teardown
-rwxr-xr-x 1 root root     14904 Aug  5  2025 accessdb
-rwxr-xr-x 1 root root      3075 Jan  5 22:01 addgnupghome
lrwxrwxrwx 1 root root         7 Jul  5  2023 addgroup -> adduser
-rwxr-xr-x 1 root root      1053 Mar 31  2024 add-shell
-rwxr-xr-x 1 root root     55191 Jul  5  2023 adduser
-rwxr-xr-x 1 root root     60992 Sep 16 00:08 agetty
-rwxr-xr-x 1 root root   1629848 Aug 15  2025 apparmor_parser
lrwxrwxrwx 1 root root         9 Aug 15  2025 apparmor_status -> aa-status
-rwxr-xr-x 1 root root      2217 Jan  5 22:01 applygnupgdefaults
-rwxr-xr-x 1 root root     36862 Apr 16  2024 argdist-bpfcc

- cd /tmp 
root@ubuntu-server:/sbin# cd /tmp
root@ubuntu-server:/tmp# pwd
/tmp
root@ubuntu-server:/tmp# ls -l | head -15
total 32
drwx------ 2 root root 4096 Feb 28 16:50 snap-private-tmp
drwx------ 3 root root 4096 Feb 28 17:37 systemd-private-79c48386a7314bce86aaaf2e6c3567d4-fwupd.service-FtGv1J
drwx------ 3 root root 4096 Feb 28 16:50 systemd-private-79c48386a7314bce86aaaf2e6c3567d4-ModemManager.service-V2OH76
drwx------ 3 root root 4096 Feb 28 16:50 systemd-private-79c48386a7314bce86aaaf2e6c3567d4-polkit.service-L7rOgT
drwx------ 3 root root 4096 Feb 28 16:50 systemd-private-79c48386a7314bce86aaaf2e6c3567d4-systemd-logind.service-k8jank
drwx------ 3 root root 4096 Feb 28 16:50 systemd-private-79c48386a7314bce86aaaf2e6c3567d4-systemd-resolved.service-tdOk8B
drwx------ 3 root root 4096 Feb 28 16:50 systemd-private-79c48386a7314bce86aaaf2e6c3567d4-systemd-timesyncd.service-5LTSBn
drwx------ 3 root root 4096 Feb 28 17:32 systemd-private-79c48386a7314bce86aaaf2e6c3567d4-upower.service-lOFzjr

- cd /boot
root@ubuntu-server:/tmp# cd /boot
root@ubuntu-server:/boot# pwd
/boot
root@ubuntu-server:/boot# ls -l | head -15
total 193568
-rw-r--r-- 1 root root   287599 Jan 13 13:56 config-6.8.0-100-generic
-rw-r--r-- 1 root root   287599 Feb  6 17:52 config-6.8.0-101-generic
drwxr-xr-x 5 root root     4096 Feb 28 17:37 grub
lrwxrwxrwx 1 root root       28 Feb 28 17:34 initrd.img -> initrd.img-6.8.0-101-generic
-rw-r--r-- 1 root root 74658687 Feb 22 07:35 initrd.img-6.8.0-100-generic
-rw-r--r-- 1 root root 74655290 Feb 28 17:36 initrd.img-6.8.0-101-generic
lrwxrwxrwx 1 root root       28 Feb 11 13:22 initrd.img.old -> initrd.img-6.8.0-100-generic
-rw------- 1 root root  9120274 Jan 13 13:56 System.map-6.8.0-100-generic
-rw------- 1 root root  9120274 Feb  6 17:52 System.map-6.8.0-101-generic
lrwxrwxrwx 1 root root       25 Feb 28 17:34 vmlinuz -> vmlinuz-6.8.0-101-generic
-rw------- 1 root root 15030664 Jan 13 14:42 vmlinuz-6.8.0-100-generic
-rw------- 1 root root 15030664 Feb  6 18:21 vmlinuz-6.8.0-101-generic
lrwxrwxrwx 1 root root       25 Feb 11 13:22 vmlinuz.old -> vmlinuz-6.8.0-100-generic

3. Telusuri direktory /dev. Identifikasi perangkat yang tersedia. Identifikasi tty (termninal) Anda (ketik who am i); siapa pemilih tty Anda (gunakan ls –l).
- root@ubuntu-server:/boot# cd /dev
root@ubuntu-server:/dev# ls -l |head -15
total 0
crw-r--r--  1 root     root     10, 235 Feb 28 16:50 autofs
drwxr-xr-x  2 root     root         280 Feb 28 16:50 block
drwxr-xr-x  2 root     root          80 Feb 28 16:50 bsg
crw-rw----  1 root     disk     10, 234 Feb 28 16:50 btrfs-control
drwxr-xr-x  3 root     root          60 Feb 28 16:50 bus
lrwxrwxrwx  1 root     root           3 Feb 28 16:50 cdrom -> sr0
drwxr-xr-x  2 root     root        3840 Mar  9 16:10 char
crw-------  1 root     root      5,   1 Feb 28 16:50 console
lrwxrwxrwx  1 root     root          11 Feb 28 16:50 core -> /proc/kcore
drwxr-xr-x  6 root     root         120 Feb 28 16:50 cpu
crw-------  1 root     root     10, 123 Feb 28 16:50 cpu_dma_latency
crw-------  1 root     root     10, 203 Feb 28 16:50 cuse
drwxr-xr-x  7 root     root         140 Feb 28 16:50 disk
drwxr-xr-x  2 root     root          60 Feb 28 16:50 dma_heap

4. Telusuri derectory /proc. Tampilkan isi file interrupts, devices, cpuinfo, meminfo dan uptime menggunakan perintah cat. 
Dapatkah Anda melihat mengapa directory /proc disebut pseudo -filesystem yang memungkinkan akses ke struktur data kernel ?
- root@ubuntu-server:/dev# cd /proc
root@ubuntu-server:/proc# cat interrupts
           CPU0       CPU1       CPU2       CPU3
  0:        230          0          0          0   IO-APIC   2-edge      timer
  1:          0          0        369          0   IO-APIC   1-edge      i8042
  8:          0          0          0          0   IO-APIC   8-edge      rtc0
  9:          0          0          0          0   IO-APIC   9-fasteoi   acpi
 12:          0        158          0          0   IO-APIC  12-edge      i8042
 14:          0          0      13288          0   IO-APIC  14-edge      ata_piix
 15:          0          0          0          0   IO-APIC  15-edge      ata_piix
 18:          0          0          0          0   IO-APIC  18-fasteoi   vmwgfx
 19:      99750          0          0          0   IO-APIC  19-fasteoi   ehci_hcd:usb2, enp0s3
 20:          0          0          0          0   IO-APIC  20-fasteoi   vboxguest
 21:          0     111921          0          0   IO-APIC  21-fasteoi   ahci[0000:00:0d.0], snd_intel8x0
 22:         70          0          0          0   IO-APIC  22-fasteoi   ohci_hcd:usb1
NMI:          0          0          0          0   Non-maskable interrupts
LOC:    2077357     179701     227656     321801   Local timer interrupts
SPU:          0          0          0          0   Spurious interrupts
PMI:          0          0          0          0   Performance monitoring interrupts
IWI:         11          2        151        142   IRQ work interrupts
RTR:          0          0          0          0   APIC ICR read retries
RES:       2536       4385       5146       4891   Rescheduling interrupts
CAL:      68249      75109     115437      71729   Function call interrupts
TLB:        285        229        192        226   TLB shootdowns
TRM:          0          0          0          0   Thermal event interrupts
THR:          0          0          0          0   Threshold APIC interrupts
DFR:          0          0          0          0   Deferred Error APIC interrupts
MCE:          0          0          0          0   Machine check exceptions
MCP:         50         50         50         50   Machine check polls
ERR:          0
MIS:          0
PIN:          0          0          0          0   Posted-interrupt notification event
NPI:          0          0          0          0   Nested posted-interrupt event
PIW:          0          0          0          0   Posted-interrupt wakeup event
root@ubuntu-server:/proc# cat devices
Character devices:
  1 mem
  4 /dev/vc/0
  4 tty
  4 ttyS
  5 /dev/tty
  5 /dev/console
  5 /dev/ptmx
  5 ttyprintk
  7 vcs
 10 misc
 13 input
 21 sg
 29 fb
 89 i2c
108 ppp
116 alsa
128 ptm
136 pts
180 usb
189 usb_device
202 cpu/msr
203 cpu/cpuid
204 ttyMAX
226 drm
241 hidraw
242 ttyDBC
243 bsg
244 watchdog
245 remoteproc
246 ptp
247 pps
248 rtc
249 dma_heap
250 dax
251 dimmctl
252 ndctl
253 tpm
254 gpiochip
261 accel

Block devices:
  7 loop
  8 sd
  9 md
 11 sr
 65 sd
 66 sd
 67 sd
 68 sd
 69 sd
 70 sd
 71 sd
128 sd
129 sd
130 sd
131 sd
132 sd
133 sd
134 sd
135 sd
252 device-mapper
253 virtblk
254 mdp
259 blkext
root@ubuntu-server:/proc# cat cpuinfo
processor       : 0
vendor_id       : GenuineIntel
cpu family      : 6
model           : 183
model name      : Intel(R) Core(TM) i7-14650HX
stepping        : 1
microcode       : 0xffffffff
cpu MHz         : 2419.200
cache size      : 30720 KB
physical id     : 0
siblings        : 4
core id         : 0
cpu cores       : 4
apicid          : 0
initial apicid  : 0
fpu             : yes
fpu_exception   : yes
cpuid level     : 22
wp              : yes
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx rdtscp lm constant_tsc rep_good nopl xtopology nonstop_tsc cpuid tsc_known_freq pni pclmulqdq ssse3 fma cx16 pcid sse4_1 sse4_2 movbe popcnt aes xsave avx f16c rdrand hypervisor lahf_lm abm 3dnowprefetch ibrs_enhanced fsgsbase bmi1 avx2 bmi2 invpcid rdseed adx clflushopt sha_ni arat md_clear flush_l1d arch_capabilities
bugs            : spectre_v1 spectre_v2 spec_store_bypass swapgs retbleed eibrs_pbrsb rfds bhi its
bogomips        : 4838.40
clflush size    : 64
cache_alignment : 64
address sizes   : 39 bits physical, 48 bits virtual
power management:

processor       : 1
vendor_id       : GenuineIntel
cpu family      : 6
model           : 183
model name      : Intel(R) Core(TM) i7-14650HX
stepping        : 1
microcode       : 0xffffffff
cpu MHz         : 2419.200
cache size      : 30720 KB
physical id     : 0
siblings        : 4
core id         : 1
cpu cores       : 4
apicid          : 1
initial apicid  : 1
fpu             : yes
fpu_exception   : yes
cpuid level     : 22
wp              : yes
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx rdtscp lm constant_tsc rep_good nopl xtopology nonstop_tsc cpuid tsc_known_freq pni pclmulqdq ssse3 fma cx16 pcid sse4_1 sse4_2 movbe popcnt aes xsave avx f16c rdrand hypervisor lahf_lm abm 3dnowprefetch ibrs_enhanced fsgsbase bmi1 avx2 bmi2 invpcid rdseed adx clflushopt sha_ni arat md_clear flush_l1d arch_capabilities
bugs            : spectre_v1 spectre_v2 spec_store_bypass swapgs retbleed eibrs_pbrsb rfds bhi its
bogomips        : 4838.40
clflush size    : 64
cache_alignment : 64
address sizes   : 39 bits physical, 48 bits virtual
power management:

processor       : 2
vendor_id       : GenuineIntel
cpu family      : 6
model           : 183
model name      : Intel(R) Core(TM) i7-14650HX
stepping        : 1
microcode       : 0xffffffff
cpu MHz         : 2419.200
cache size      : 30720 KB
physical id     : 0
siblings        : 4
core id         : 2
cpu cores       : 4
apicid          : 2
initial apicid  : 2
fpu             : yes
fpu_exception   : yes
cpuid level     : 22
wp              : yes
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx rdtscp lm constant_tsc rep_good nopl xtopology nonstop_tsc cpuid tsc_known_freq pni pclmulqdq ssse3 fma cx16 pcid sse4_1 sse4_2 movbe popcnt aes xsave avx f16c rdrand hypervisor lahf_lm abm 3dnowprefetch ibrs_enhanced fsgsbase bmi1 avx2 bmi2 invpcid rdseed adx clflushopt sha_ni arat md_clear flush_l1d arch_capabilities
bugs            : spectre_v1 spectre_v2 spec_store_bypass swapgs retbleed eibrs_pbrsb rfds bhi its
bogomips        : 4838.40
clflush size    : 64
cache_alignment : 64
address sizes   : 39 bits physical, 48 bits virtual
power management:

processor       : 3
vendor_id       : GenuineIntel
cpu family      : 6
model           : 183
model name      : Intel(R) Core(TM) i7-14650HX
stepping        : 1
microcode       : 0xffffffff
cpu MHz         : 2419.200
cache size      : 30720 KB
physical id     : 0
siblings        : 4
core id         : 3
cpu cores       : 4
apicid          : 3
initial apicid  : 3
fpu             : yes
fpu_exception   : yes
cpuid level     : 22
wp              : yes
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx rdtscp lm constant_tsc rep_good nopl xtopology nonstop_tsc cpuid tsc_known_freq pni pclmulqdq ssse3 fma cx16 pcid sse4_1 sse4_2 movbe popcnt aes xsave avx f16c rdrand hypervisor lahf_lm abm 3dnowprefetch ibrs_enhanced fsgsbase bmi1 avx2 bmi2 invpcid rdseed adx clflushopt sha_ni arat md_clear flush_l1d arch_capabilities
bugs            : spectre_v1 spectre_v2 spec_store_bypass swapgs retbleed eibrs_pbrsb rfds bhi its
bogomips        : 4838.40
clflush size    : 64
cache_alignment : 64
address sizes   : 39 bits physical, 48 bits virtual
power management:

root@ubuntu-server:/proc# cat meminfo
MemTotal:        4092204 kB
MemFree:         2671932 kB
MemAvailable:    3545712 kB
Buffers:           91328 kB
Cached:           913612 kB
SwapCached:            0 kB
Active:           317724 kB
Inactive:         735944 kB
Active(anon):      58584 kB
Inactive(anon):        0 kB
Active(file):     259140 kB
Inactive(file):   735944 kB
Unevictable:       27316 kB
Mlocked:           27316 kB
SwapTotal:       4194300 kB
SwapFree:        4194300 kB
Zswap:                 0 kB
Zswapped:              0 kB
Dirty:                 0 kB
Writeback:             0 kB
AnonPages:         76064 kB
Mapped:            83436 kB
Shmem:              1104 kB
KReclaimable:     162996 kB
Slab:             227164 kB
SReclaimable:     162996 kB
SUnreclaim:        64168 kB
KernelStack:        2912 kB
PageTables:         3556 kB
SecPageTables:         0 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:     6240400 kB
Committed_AS:     470020 kB
VmallocTotal:   34359738367 kB
VmallocUsed:       20888 kB
VmallocChunk:          0 kB
Percpu:             2240 kB
HardwareCorrupted:     0 kB
AnonHugePages:         0 kB
ShmemHugePages:        0 kB
ShmemPmdMapped:        0 kB
FileHugePages:         0 kB
FilePmdMapped:         0 kB
Unaccepted:            0 kB
HugePages_Total:       0
HugePages_Free:        0
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
Hugetlb:               0 kB
DirectMap4k:      121792 kB
DirectMap2M:     4163584 kB
root@ubuntu-server:/proc# cat uptime
16257.32 63162.74

5. Ubahlah direktory home ke user lain secara langsung menggunakan cd ~username.
- root@ubuntu-server:/proc# cd /home/Rappizr7
root@ubuntu-server:/home/Rappizr7#

6. Ubah kembali ke direktory home Anda.
- root@ubuntu-server:/home/Rappizr7# cd
root@ubuntu-server:~# pwd
/root

7. Buat subdirektory work dan play.
- root@ubuntu-server:~# mkdir work play
root@ubuntu-server:~# ls -l
total 16
drwxr-xr-x 2 root root 4096 Mar 10 12:47 play
-r-xr-xr-x 1 root root 7049 Feb 11 13:23 vboxpostinstall.sh
drwxr-xr-x 2 root root 4096 Mar 10 12:47 work

8. Hapus subdirektory work.
- root@ubuntu-server:~# rmdir work
root@ubuntu-server:~# ls -l
total 12
drwxr-xr-x 2 root root 4096 Mar 10 12:47 play
-r-xr-xr-x 1 root root 7049 Feb 11 13:23 vboxpostinstall.sh

9. Copy file /etc/passwd ke direktory home Anda.
- root@ubuntu-server:~# cp /etc/passwd .
root@ubuntu-server:~# ls -l
total 16
-rw-r--r-- 1 root root 1789 Mar 10 12:49 passwd
drwxr-xr-x 2 root root 4096 Mar 10 12:47 play
-r-xr-xr-x 1 root root 7049 Feb 11 13:23 vboxpostinstall.sh

10. Pindahkan ke subirectory play.
- root@ubuntu-server:~# mv passwd play
root@ubuntu-server:~# ls -l play
total 4
-rw-r--r-- 1 root root 1789 Mar 10 12:49 passwd

11. Ubahlah ke subdirektory play dan buat symbolic link dengan nama terminal yang menunjuk ke perangkat tty.
Apa yang terjadi jika melakukan hard link ke perangkat tty ?
- root@ubuntu-server:~# cd play
root@ubuntu-server:~/play# pwd
/root/play
root@ubuntu-server:~/play# who am i
Rappizr7 pts/3        2026-03-10 10:49 (192.168.1.110)
root@ubuntu-server:~/play# ln /dev/pts/0 hardtty
ln: failed to create hard link 'hardtty' => '/dev/pts/0': Invalid cross-device link

12. Buatlah file bernama hello.txt yang berisi kata ”hello word”. Dapatkah Anda gunakan ”cp” menggunakan ”terminal” sebagai 
file asal untuk menghasilkan efek yang sama ?
- root@ubuntu-server:~/play# echo "hello word" > hello.txt
root@ubuntu-server:~/play# cat hello.txt
hello word

13. Copy hello.txt ke terminal. Apa yang terjadi ?
- root@ubuntu-server:~/play# cp hello.txt /dev/pts/2
hello word

14. Masih direktory home, copy keseluruhan direktory play ke direktory bernama work menggunakan symbolic link.
- root@ubuntu-server:~/play# cd ..
root@ubuntu-server:~# pwd
/root
root@ubuntu-server:~# ln -s play work
root@ubuntu-server:~# ls -l
total 12
drwxr-xr-x 2 root root 4096 Mar 10 12:52 play
-r-xr-xr-x 1 root root 7049 Feb 11 13:23 vboxpostinstall.sh
lrwxrwxrwx 1 root root    4 Mar 10 12:53 work -> play

15. Hapus direktory work dan isinya dengan satu perintah
- root@ubuntu-server:~# rm -rf work
root@ubuntu-server:~# ls -l
total 12
drwxr-xr-x 2 root root 4096 Mar 10 12:52 play
-r-xr-xr-x 1 root root 7049 Feb 11 13:23 vboxpostinstall.sh


## LAPORAN RESMI:
1. Analisa hasil percobaan yang Anda lakukan.
a. Analisa setiap hasil tampilannya.
- Percobaan 1: Direktori

1) Melihat direktori HOME

Perintah pwd menampilkan direktori kerja saat ini, sedangkan echo $HOME menampilkan home directory milik user yang sedang aktif. Pada hasil percobaan, kedua perintah menampilkan /home/Rappizr7, sehingga dapat disimpulkan bahwa saat login user berada pada direktori home miliknya.

2) Melihat direktori aktual dan parent directory

Perintah cd . tidak mengubah posisi direktori karena tanda . menunjukkan direktori saat ini. Setelah itu, pwd tetap menampilkan /home/Rappizr7. Perintah cd .. memindahkan posisi ke satu tingkat di atas, yaitu /home. Ketika dijalankan cd tanpa argumen, shell kembali ke home directory user, yaitu /home/Rappizr7.

3) Membuat direktori dan subdirektori

Perintah mkdir A B C A/D A/E B/F A/D/A berhasil membuat tiga direktori utama, yaitu A, B, dan C, beserta beberapa subdirektorinya. Hasil ls -l menunjukkan bahwa direktori tersebut telah terbentuk. Perintah ls -l A menampilkan bahwa di dalam direktori A terdapat direktori D dan E. Selanjutnya, ls -l A/D menunjukkan adanya subdirektori A di dalam A/D.

4) Menghapus direktori

Perintah rmdir B menghasilkan pesan error karena direktori B masih berisi subdirektori F, sehingga belum kosong. Setelah diperiksa dengan ls -l B, terlihat bahwa B memang masih memiliki isi. Saat dijalankan rmdir B/F B, subdirektori B/F dihapus terlebih dahulu, kemudian direktori B yang sudah kosong dapat dihapus. Ketika ls -l B dijalankan kembali, muncul error karena direktori B sudah tidak ada.

5) Navigasi direktori

Perintah cd A memindahkan user ke /home/Rappizr7/A, lalu cd .. mengembalikan lagi ke /home/Rappizr7. Setelah itu, cd /home/Rappizr7/C berhasil masuk ke direktori C menggunakan path absolut. Namun, perintah cd /<Rappizr7/C menghasilkan error karena penulisan path salah dan tidak sesuai format path Linux. Setelah error, posisi direktori tetap berada di /home/Rappizr7/C.

- Percobaan 2: Manipulasi file

1) Perintah cp

File contoh dibuat menggunakan cat > contoh, lalu disalin menjadi contoh1 dengan cp contoh contoh1. Hasil ls -l menunjukkan kedua file tersebut ada dan memiliki ukuran yang sama. Perintah cp contoh A menyalin file contoh ke dalam direktori A, dan hasil ls -l A membuktikan bahwa file berhasil masuk ke direktori tersebut. Perintah cp contoh contoh1 A/D menyalin dua file sekaligus ke direktori A/D, dan hasilnya sesuai dengan output ls -l A/D.

2) Perintah mv

Perintah mv contoh contoh2 mengganti nama file contoh menjadi contoh2. Setelah itu, mv contoh1 contoh2 A/D memindahkan file contoh1 dan contoh2 ke direktori A/D. Hasil ls -l A/D menunjukkan kedua file tersebut sudah berada di sana. Ketika dijalankan mv contoh contoh1 C, muncul error karena file contoh dan contoh1 sudah tidak ada lagi di direktori aktif, sebab sebelumnya sudah dipindahkan atau diubah namanya. Walaupun begitu, isi direktori C tetap menampilkan file contoh dan contoh1 yang kemungkinan berasal dari percobaan sebelumnya.

3) Perintah rm

Perintah rm contoh2 menghasilkan error karena file contoh2 sudah tidak ada di direktori saat ini, melainkan sudah dipindahkan ke A/D. Hal serupa terjadi pada rm -i contoh, karena file contoh juga sudah tidak ada di direktori aktif. Perintah rm -rf A C berhasil menghapus direktori A dan C beserta seluruh isinya. Hasil ls -l setelah itu menunjukkan bahwa kedua direktori tersebut sudah hilang.

- Percobaan 3: Symbolic Link

File halo.txt dibuat dengan isi hallo apa khabar. Kemudian perintah ln halo.txt z membuat hard link bernama z. Hal ini terlihat dari hasil ls -l, di mana jumlah link untuk halo.txt berubah menjadi 2. Saat isi file dibaca melalui cat z, isi yang tampil sama dengan halo.txt, karena keduanya menunjuk ke inode yang sama.

Setelah dibuat direktori mydir, perintah ln z mydir/halo.juga menambahkan hard link lain di dalam mydir. Hal ini membuat jumlah link untuk file yang sama bertambah menjadi 3, sebagaimana terlihat pada percobaan selanjutnya.

Perintah ln -s z bye.txt membuat symbolic link bernama bye.txt yang menunjuk ke file z. Hasil ls -l bye.txt memperlihatkan format bye.txt -> z, yang menandakan bahwa file tersebut adalah symbolic link. Saat dibaca dengan cat bye.txt, isi file tetap dapat ditampilkan.

- Percobaan 4: Melihat isi file

Perintah file halo.txt menghasilkan ASCII text, artinya halo.txt adalah file teks biasa. Sementara itu, file bye.txt menghasilkan symbolic link to z, yang menunjukkan bahwa bye.txt bukan file biasa, melainkan link simbolik yang menunjuk ke file lain.

- Percobaan 5: Mencari file

1) Perintah find

Perintah find /home -name "*.txt" -print > myerror.txt mencari semua file berekstensi .txt di bawah direktori /home, lalu menyimpan hasilnya ke file myerror.txt. Saat isi file ditampilkan dengan cat myerror.txt, terlihat daftar file .txt yang berhasil ditemukan, seperti sorted-users.txt, config-files.txt, monitoring_log.txt, halo.txt, dan lain-lain.

Perintah find . -name "*.txt" -exec wc -l '{}' ';' mencari file .txt pada direktori aktif, lalu menghitung jumlah baris tiap file dengan wc -l. Hasilnya menunjukkan jumlah baris dari masing-masing file, misalnya config-files.txt berisi 402 baris, monitoring_log.txt 48 baris, dan halo.txt 1 baris.

2) Perintah which

Pada laporan, perintah yang diketik adalah witch ls, sehingga sistem menampilkan error Command 'witch' not found. Seharusnya perintah yang benar adalah which ls. Fungsi which adalah untuk menunjukkan lokasi file executable dari suatu perintah, misalnya which ls biasanya menghasilkan /usr/bin/ls.

3) Perintah locate

Perintah locate "*.txt" menampilkan banyak file .txt yang terdapat di sistem, terutama dari direktori /usr/share, /usr/src, dan /var/lib. Hal ini menunjukkan bahwa locate bekerja berdasarkan database indeks file, sehingga hasil pencariannya sangat cepat dan luas.

- Percobaan 6: Mencari teks pada file

Perintah grep hallo *.txt mencari kata hallo pada semua file berekstensi .txt di direktori aktif. Hasilnya menunjukkan bahwa kata tersebut ditemukan pada bye.txt dan halo.txt. Pada bye.txt, hal ini terjadi karena bye.txt adalah symbolic link yang menunjuk ke file z, yang isinya sama dengan halo.txt.

b. Pada Percobaan 1 point 3 buatlah pohon dari struktur file dan direktori\
-Struktur direktori yang terbentuk dari perintah:
mkdir A B C A/D A/E B/F A/D/A
adalah:
/home/Rappizr7
├── A
│   ├── D
│   │   └── A
│   └── E
├── B
│   └── F
└── C
Struktur tersebut menunjukkan bahwa:
A, B, dan C adalah direktori utama
Di dalam A terdapat D dan E
Di dalam A/D terdapat subdirektori A
Di dalam B terdapat subdirektori F

c. Bila terdapat pesan error, jelaskan penyebabnya.
- 1) rmdir B → Directory not empty
Penyebabnya karena direktori B masih berisi subdirektori F. Perintah rmdir hanya dapat digunakan untuk direktori kosong.

- 2) ls -l B → No such file or directory
Penyebabnya karena direktori B sudah berhasil dihapus sebelumnya dengan rmdir B/F B.

- 3) cd /<Rappizr7/C → No such file or directory
Penyebabnya karena penulisan path salah. Path yang benar adalah /home/Rappizr7/C.

- 4) mv contoh contoh1 C → cannot stat
Penyebabnya karena file contoh dan contoh1 sudah tidak ada di direktori aktif, sebab sebelumnya telah diubah nama atau dipindahkan ke direktori lain.

- 5) rm contoh2 → No such file or directory
Penyebabnya karena file contoh2 sudah dipindahkan ke direktori A/D, sehingga tidak ada lagi di direktori aktif.

- 6) rm -i contoh → No such file or directory
Penyebabnya karena file contoh sudah tidak berada di direktori aktif.

- 7) witch ls → Command not found
Penyebabnya adalah salah ketik. Perintah yang benar adalah which ls.

- 8) Hard link ke /dev/pts/0 → Invalid cross-device link
Penyebabnya karena hard link tidak dapat dibuat melintasi filesystem yang berbeda dan umumnya tidak digunakan pada device file seperti terminal.

2. Kerjakan latihan diatas dan analisa hasil tampilannya.
- Pada latihan pertama, perintah cd, pwd, dan ls -al digunakan untuk menelusuri perpindahan direktori dari /home/Rappizr7, naik ke /home, lalu ke root /, kemudian masuk ke /etc. Hasil ls -al menunjukkan isi direktori lengkap, termasuk file tersembunyi. Perintah cat passwd pada /etc menampilkan daftar akun user sistem, termasuk user Rappizr7.
Penelusuran ke /bin, /usr/bin, dan /sbin menunjukkan banyak file executable sistem. Direktori /bin dan /usr/bin pada sistem ini terlihat mirip karena /bin merupakan symbolic link ke /usr/bin. Direktori /sbin berisi program administrasi sistem.
Direktori /tmp berisi file dan direktori sementara yang digunakan sistem atau layanan aktif. Direktori /boot berisi file penting untuk proses booting, seperti vmlinuz, initrd.img, System.map, dan direktori grub.
Pada penelusuran /dev, terlihat berbagai file perangkat seperti console, cdrom, tty, disk, dan lain-lain. Hal ini menunjukkan bahwa pada Linux, perangkat dipresentasikan sebagai file khusus.
Pada penelusuran /proc, file seperti interrupts, devices, cpuinfo, meminfo, dan uptime berhasil ditampilkan dengan cat. Isi file-file ini memberikan informasi langsung dari kernel tentang interrupt, perangkat, prosesor, memori, dan lama sistem berjalan. Karena informasi tersebut bersifat dinamis dan tidak benar-benar disimpan sebagai file biasa di disk, maka /proc disebut pseudo-filesystem.
Pada latihan perpindahan home directory, user root berpindah ke /home/Rappizr7, lalu kembali ke home miliknya sendiri, yaitu /root. Setelah itu dibuat direktori work dan play, lalu work dihapus dengan rmdir karena masih kosong.
File /etc/passwd kemudian disalin ke home directory root dan dipindahkan ke subdirektori play. Di dalam play, dibuat file hello.txt yang berisi teks hello word. Saat file tersebut disalin ke /dev/pts/2, isi file muncul langsung pada terminal, karena terminal adalah device output.
Pada pembuatan link, direktori play dihubungkan ke work menggunakan symbolic link dengan ln -s play work. Hasil ls -l menampilkan work -> play, yang menandakan bahwa work hanyalah tautan simbolik, bukan salinan direktori sebenarnya. Ketika rm -rf work dijalankan, hanya symbolic link work yang terhapus, sedangkan direktori play tetap ada.
Catatan penting pada latihan nomor 11: instruksi meminta membuat symbolic link ke perangkat tty, tetapi pada hasil yang dicoba justru dibuat hard link dengan ln /dev/pts/0 hardtty. Karena itu yang muncul memang error. Jika ingin sesuai soal, perintah yang benar semestinya berbentuk:
ln -s /dev/pts/3 terminal
karena hasil who am i menunjukkan terminal aktif adalah pts/3

3. Berikan kesimpulan dari praktikum ini.
- Berdasarkan praktikum yang telah dilakukan, dapat disimpulkan bahwa Linux memiliki struktur filesystem yang tersusun secara hirarkis dan dikelola sepenuhnya melalui perintah-perintah shell. User dapat menavigasi direktori menggunakan cd, pwd, dan ls, serta membuat dan menghapus direktori dengan mkdir dan rmdir.
Manipulasi file dapat dilakukan dengan cp, mv, dan rm, masing-masing untuk menyalin, memindahkan atau mengganti nama, serta menghapus file. Praktikum juga menunjukkan pentingnya memahami lokasi file aktif, karena banyak error terjadi akibat file sudah dipindahkan atau path yang ditulis salah.
Selain itu, Linux mendukung dua jenis link, yaitu hard link dan symbolic link. Hard link menunjuk ke inode yang sama, sedangkan symbolic link menunjuk ke path file target. Praktikum juga memperlihatkan bahwa beberapa objek sistem seperti perangkat pada /dev dan informasi kernel pada /proc diperlakukan sebagai bagian dari filesystem. Hal ini menegaskan bahwa Linux menggunakan konsep “everything is a file”.
Melalui praktikum ini, pemahaman terhadap manajemen file, struktur direktori, pencarian file, isi file, serta link pada Linux menjadi lebih baik. Praktikum ini juga melatih ketelitian dalam mengetik perintah, karena kesalahan kecil seperti typo atau path yang salah dapat menimbulkan error.