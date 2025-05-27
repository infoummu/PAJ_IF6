---
title: PAJ Pertemuan 05 2025 - SSH Untuk Aksess File
published: true
author: Ikhwan Elyas
description: Menggunakan SSH untuk mengakses file di komputer yang di remote secara manual dan menggunakan coding Python (Netmiko/Paramiko). Langkah ini mengisolasi masalah jaringan dari potensi kesalahan kode, mempercepat debugging, dan memastikan fondasi yang benar sebelum automasi.
---


### Praktikum SSH dengan Python untuk Akses file 
#### Manual Menggunakan SSH (contoh kasus mesin linux)
- Langkah2 untuk ssh dan apa saja yang diperlukan ketika ssh silahkan lihat materi sebelumnya 
- Perintah-perintah dasar yang harus diperhatikan dan di kuasai 
    - ping 
    - ifconfig 
    - ip addr (dst)
    - nmap
    - ssh
    - whoami 
    - pwd
    - touch
    - nano 
    - cd
    - ls 
    - cat 


| Perintah    | Keterangan                                                                 |
|-------------|----------------------------------------------------------------------------|
| `ping`      | Mengirim paket ICMP untuk menguji konektivitas jaringan ke host tertentu. |
| `ifconfig`  | Menampilkan atau mengkonfigurasi pengaturan jaringan (umumnya pada sistem lama). |
| `ip addr`   | Menampilkan informasi alamat IP dari semua antarmuka jaringan.            |
| `nmap`      | Alat pemindaian jaringan untuk mendeteksi host dan layanan terbuka.       |
| `ssh`       | Mengakses komputer lain secara remote dengan aman melalui protokol SSH.   |
| `whoami`    | Menampilkan nama pengguna saat ini yang sedang login.                     |
| `pwd`       | Menampilkan direktori kerja saat ini (print working directory).           |
| `touch`     | Membuat file kosong baru atau memperbarui waktu akses file.               |
| `nano`      | Editor teks berbasis terminal yang sederhana dan mudah digunakan.         |
| `cd`        | Mengubah direktori kerja saat ini.                                        |
| `ls`        | Menampilkan daftar file dan direktori dalam direktori saat ini.           |
| `cat`       | Menampilkan isi file ke layar, bisa juga untuk menggabungkan file.        |


### Automation dengan code python

#### The Python Code : 

```python
import paramiko

# Konfigurasi SSH
hostname = "172.16.85.127"     # Ganti dengan IP atau hostname komputer remote
port = 22                     # Port SSH, default 22
username = "infra18"         # Ganti dengan username SSH
password = "123456"     # Ganti dengan password SSH


# Fungsi Untuk Lihat File 
def lihatfile(namafile):
    # Membuat koneksi SSH
    ssh = paramiko.SSHClient()
    ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    remote_file_path = "/mnt/" + namafile

    try:
        ssh.connect(hostname, port, username, password)
        stdin, stdout, stderr = ssh.exec_command(f"cat {remote_file_path}")
        
        # Cetak hasil output
        print("Isi file:")
        print(stdout.read().decode())

        # Jika ada error
        err = stderr.read().decode()
        if err:
            print("Error:", err)

    finally:
        ssh.close()


# Panggil Fungsi 
# contoh namafile = fachrul
# silahkan menyesuaikan namafilenya 
lihatfile("fachrul")

```

### Kerjakan Latihan Praktikum ke 5

1. Tulis Code diatas dengan Aplikasi Editor 
2. Simpan dengan Beri nama file dengan `praktikum5_ssh_aksesfile_NPM.py` (ubah NPM dengan lima digit terakhir npm anda)
3. Contoh nama file : `praktikum5_ssh_aksesfile_22001.py`
4. Jalankan di CLI, perintahnya : python praktikum2_ssh_netmiko_22020.py
5. Setelah Jalankan dan Tidak Jika Tidak Ada Error, Silahkan Upload/Kirim 
6. Masuk ke link ini : [GDRIVE (Google Drive)](https://drive.google.com/drive/folders/1aekuG1Nf9gNFl3vfIVfq-GQcS47r3qvJ?usp=sharing){:target="_blank"} dan BUAT folder dengan nama `PAJ_NPM`
7. Setelah itu masuk ke folder `PAJ_NPM` yg telah anda buat di [GDRIVE (Google Drive)](https://drive.google.com/drive/folders/1aekuG1Nf9gNFl3vfIVfq-GQcS47r3qvJ?usp=sharing){:target="_blank"}, kemudian UPLOAD hasil praktikum anda. 
6. Batas Waktu Kumpul sampai sebelum Pertemuan berikutnya masuk 25/03/2025.

***
by: ikhwan@fedora.linux 

