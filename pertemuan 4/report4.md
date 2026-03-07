# Laporan Pertemuan ke-4 Sistem Operasi

**Tanggal:** 04 maret 2026  
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


