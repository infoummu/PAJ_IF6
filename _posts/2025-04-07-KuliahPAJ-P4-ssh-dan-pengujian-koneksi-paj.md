---
title: PAJ Pertemuan 04 2025 - SSH Untuk Uji Koneksi
published: true
author: Ikhwan Elyas
description: Menguji koneksi SSH manual sebelum coding Python (Netmiko/Paramiko) krusial untuk memverifikasi konektivitas dasar, kredensial, dan konfigurasi SSH perangkat. Langkah ini mengisolasi masalah jaringan dari potensi kesalahan kode, mempercepat debugging, dan memastikan fondasi yang benar sebelum automasi.
---

### **Penting: Uji Koneksi SSH Manual Sebelum Coding (Netmiko/Paramiko)**

Sebelum Anda mulai menulis kode Python yang menggunakan library seperti Netmiko atau Paramiko untuk berinteraksi dengan perangkat jaringan melalui SSH, **sangat penting untuk memastikan bahwa Anda dapat terhubung ke perangkat tersebut secara manual melalui shell atau terminal.** Ini akan membantu Anda mengisolasi masalah jika kode Python Anda tidak berfungsi seperti yang diharapkan.

Berikut langkah-langkah untuk menguji koneksi SSH secara manual:

1.  **Buka Terminal/Command Prompt:**
    * **Linux/macOS:** Buka aplikasi Terminal.
    * **Windows:** Buka Command Prompt (cmd) atau PowerShell.

2.  **Gunakan Perintah `ssh`:**
    * Ketik perintah `ssh` diikuti dengan `username@alamat_ip_perangkat`.
    * Ganti `username` dengan nama pengguna yang valid untuk mengakses perangkat jaringan.
    * Ganti `alamat_ip_perangkat` dengan alamat IP atau hostname perangkat jaringan yang ingin Anda hubungkan.

    * **Contoh:** `ssh admin@192.168.1.10`

3.  **Tekan Enter:**
    * Setelah mengetik perintah, tekan tombol Enter.

4.  **Verifikasi Host Key (Jika Pertama Kali):**
    * Jika ini adalah pertama kalinya Anda terhubung ke perangkat ini dari komputer Anda, Anda mungkin akan melihat pesan peringatan tentang host key yang tidak dikenal.
    * **Hati-hati!** Pastikan Anda mengenali dan mempercayai perangkat yang Anda hubungkan. Jika Anda yakin, ketik `yes` dan tekan Enter. Host key perangkat akan disimpan di file `known_hosts` komputer Anda untuk koneksi berikutnya.

5.  **Masukkan Kata Sandi (Jika Diperlukan):**
    * Anda akan diminta untuk memasukkan kata sandi untuk pengguna yang Anda tentukan.
    * Ketik kata sandi dengan benar (karakter yang Anda ketik mungkin tidak terlihat di terminal karena alasan keamanan) dan tekan Enter.

6.  **Verifikasi Koneksi Berhasil:**
    * Jika kata sandi benar dan tidak ada masalah jaringan, Anda akan berhasil masuk ke perangkat jaringan melalui SSH. Anda akan melihat prompt perintah perangkat jaringan (misalnya, `Router>`, `Switch#`, dll.).

7.  **Uji Perintah Sederhana (Opsional):**
    * Setelah berhasil masuk, coba jalankan beberapa perintah dasar perangkat jaringan (misalnya, `show version`, `show ip interface brief`) untuk memastikan komunikasi berjalan dengan baik.

8.  **Keluar dari Sesi SSH:**
    * Untuk mengakhiri sesi SSH, ketik perintah `exit` atau `logout` dan tekan Enter. Anda akan kembali ke prompt terminal/command prompt komputer Anda.

### **Mengapa Langkah Ini Penting?**

* **Memastikan Konektivitas Dasar:** Jika Anda tidak dapat terhubung secara manual, kemungkinan besar ada masalah jaringan (misalnya, perangkat tidak dapat dijangkau, firewall memblokir port 22, konfigurasi SSH yang salah pada perangkat). Masalah ini perlu diselesaikan sebelum Anda mencoba menggunakan kode Python.
* **Memverifikasi Kredensial:** Menguji koneksi manual memastikan bahwa nama pengguna dan kata sandi yang Anda gunakan benar. Kesalahan kredensial adalah penyebab umum kegagalan koneksi.
* **Memastikan Konfigurasi SSH:** Koneksi manual memverifikasi bahwa layanan SSH berjalan dengan benar pada perangkat jaringan dan dikonfigurasi untuk menerima koneksi dari alamat IP komputer Anda (jika ada batasan akses).
* **Mempermudah Debugging:** Jika koneksi manual berhasil tetapi kode Python Anda gagal, Anda dapat lebih fokus pada masalah yang mungkin ada dalam kode Python Anda (misalnya, penggunaan library yang salah, kesalahan sintaks, logika yang keliru).

Dengan mengikuti langkah-langkah ini, Anda akan memiliki dasar yang lebih kuat sebelum mulai mengotomatiskan interaksi dengan perangkat jaringan menggunakan Python dan library SSH.

### **Latihan Lanjutan Code Materi 03 & 04:**


* ### **4. Contoh UDP Client/Server Sederhana:**

  * **Server UDP:**

```python
import socket

udp_server = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
udp_server.bind(("0.0.0.0", 9999))

print("UDP Server listening on port 9999...")

while True:
    data, addr = udp_server.recvfrom(1024)
    print(f"Received from {addr}: {data.decode()}")
    
    # Kirim balasan
    udp_server.sendto(b"Message received", addr)
```

  * **Client UDP:**

```python
import socket

udp_client = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

try:
    message = "Hello UDP Server!"
    udp_client.sendto(message.encode(), ("localhost", 9999))
    
    response, _ = udp_client.recvfrom(1024)
    print(f"Server response: {response.decode()}")

finally:
    udp_client.close()

```

**Catatan Penting:**

### Kerjakan Latihan Praktikum ke 4

1. Tulis Code diatas dengan Aplikasi Editor 
2. Simpan dengan Beri nama file dengan `praktikum4_ssh_paramiko/netmiko/socket_NPM.py` (ubah NPM dengan lima digit terakhir npm anda)
3. Contoh nama file : `praktikum4_ssh_netmiko_22001.py`
4. Jalankan di CLI, perintahnya : python praktikum4_ssh_netmiko_22020.py
5. Setelah Jalankan dan Tidak Jika Tidak Ada Error, Silahkan Upload/Kirim 
6. Masuk ke link ini : [GDRIVE (Google Drive)](https://drive.google.com/drive/folders/1aekuG1Nf9gNFl3vfIVfq-GQcS47r3qvJ?usp=sharing){:target="_blank"} dan BUAT folder dengan nama `PAJ_NPM`
7. Setelah itu masuk ke folder `PAJ_NPM` yg telah anda buat di [GDRIVE (Google Drive)](https://drive.google.com/drive/folders/1aekuG1Nf9gNFl3vfIVfq-GQcS47r3qvJ?usp=sharing){:target="_blank"}, kemudian UPLOAD hasil praktikum anda. 
6. Batas Waktu Kumpul sampai sebelum Pertemuan berikutnya masuk 25/03/2025.

***
by: ikhwan@fedora.linux 

