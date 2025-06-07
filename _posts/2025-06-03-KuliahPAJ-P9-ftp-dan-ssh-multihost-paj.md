---
title: PAJ Pertemuan 09 2025 - Praktikum FTP dan SSH Multiple Host
published: true
author: Ikhwan Elyas
description: Pemahaman tentang FTP, SSH, dan multiple host sangat penting dalam dunia jaringan modern. Ketiganya merupakan fondasi utama bagi siapa saja yang ingin menguasai **automasi jaringan berbasis pemrograman**, khususnya dalam skala menengah hingga besar..
---

## Pentingnya FTP, SSH, dan Multiple Host 

Dalam dunia teknologi informasi dan jaringan komputer, protokol komunikasi seperti **FTP (File Transfer Protocol)** dan **SSH (Secure Shell)** memainkan peran yang sangat penting, terutama dalam konteks *pemrograman dan automasi jaringan*. Keduanya memungkinkan pengelolaan sistem dan pertukaran data secara remote, yang menjadi fondasi utama dalam pengembangan sistem jaringan modern.

- Perintah-perintah dasar yang harus diperhatikan dan di kuasai 


| Perintah    | Keterangan                                                                 |
|-------------|----------------------------------------------------------------------------|
| `ping`      | Mengirim paket ICMP untuk menguji konektivitas jaringan ke host tertentu. |
| `ifconfig`  | Menampilkan atau mengkonfigurasi pengaturan jaringan (umumnya pada sistem lama). |
| `ip addr`   | Menampilkan informasi alamat IP dari semua antarmuka jaringan.            |
| `nmap`      | Alat pemindaian jaringan untuk mendeteksi host dan layanan terbuka.       |
| `ssh`       | Mengakses komputer lain secara remote dengan aman melalui protokol SSH.   |
| `find`      | search for files in a directory hierarchy                                 |
| `scp`       | Copy File menggunakan protokol ssh; OpenSSH standar for secure file copy  |



### FTP (File Transfer Protocol)

FTP merupakan protokol standar untuk mentransfer file antar komputer melalui jaringan. FTP digunakan secara luas untuk mengunggah (*upload*) dan mengunduh (*download*) file dari server. Dalam konteks **automasi jaringan**, FTP sangat berguna untuk:

- Distribusi konfigurasi jaringan ke banyak perangkat,
- Backup otomatis konfigurasi router/switch,
- Mengirim laporan jaringan harian atau data log ke server pusat.

Meskipun FTP konvensional tidak terenkripsi, versi aman seperti **FTPS (FTP Secure)** atau penggantinya seperti **SFTP (SSH File Transfer Protocol)** memberikan keamanan yang lebih baik, terutama dalam lingkungan yang sensitif terhadap data.

### SSH (Secure Shell)

SSH merupakan protokol terenkripsi yang digunakan untuk mengakses dan mengelola perangkat jaringan secara aman. Dalam dunia **DevOps dan automasi jaringan**, SSH memungkinkan skrip atau sistem otomatis untuk:

- Masuk ke perangkat jaringan (router, switch, server),
- Menjalankan perintah konfigurasi secara remote,
- Menyalin file antar server dengan aman menggunakan SFTP atau SCP,
- Melakukan pemantauan dan troubleshooting sistem tanpa interaksi manual.

Kekuatan SSH bukan hanya pada keamanannya, tetapi juga pada kemampuannya untuk diintegrasikan ke dalam berbagai bahasa pemrograman seperti Python (dengan modul seperti `paramiko`) untuk membuat proses automasi menjadi fleksibel dan dapat dikustomisasi.

### Konsep Multiple Host

Dalam lingkungan jaringan nyata, organisasi biasanya memiliki banyak server dan perangkat yang tersebar di lokasi berbeda. Konsep **multiple host** merujuk pada kemampuan sistem untuk berinteraksi dengan banyak server atau perangkat sekaligus.

Dalam automasi jaringan, dukungan terhadap multiple host sangat penting karena:

- Proses seperti update konfigurasi, backup data, atau monitoring harus dilakukan ke banyak perangkat secara serentak atau berurutan,
- Mengurangi kebutuhan kerja manual dan mempercepat respons terhadap insiden atau pembaruan sistem,
- Memungkinkan pendekatan skalabel dalam mengelola jaringan besar.

Dengan dukungan pemrograman, proses ini bisa dijalankan otomatis menggunakan skrip yang memanfaatkan **threading**, **SSH keys**, dan **kontrol paralel**, sehingga administrator dapat melakukan pekerjaan yang dulunya membutuhkan waktu berjam-jam hanya dalam beberapa menit.

### Keterkaitan dengan Pemrograman dan Automasi

Pemrograman memberikan fleksibilitas dan efisiensi dalam mengelola jaringan. Dengan memanfaatkan bahasa seperti Python, administrator dapat menulis skrip untuk:

- Menjadwalkan backup otomatis menggunakan FTP/SFTP,
- Mengatur login otomatis ke banyak server menggunakan SSH,
- Menggabungkan data log dari beberapa host menjadi satu laporan terpusat,
- Menyebarkan pembaruan ke semua perangkat dengan satu perintah.

Automasi jaringan tidak hanya meningkatkan efisiensi, tetapi juga mengurangi risiko human error, meningkatkan konsistensi, dan mempercepat respon terhadap perubahan atau gangguan sistem.


---
### Kode Python untuk Latihan Praktikum 9
#### Contoh Kode 1
* Kode untuk copy multiple file dari multiple server/host

```python 
import paramiko
from concurrent.futures import ThreadPoolExecutor

# Konfigurasi server sumber
# Server A infra5:172.16.85.103
# Server B infra13:172.16.85.116

sources = {
    "fileA.txt": {"host": "172.16.85.103", "username": "infra5", "password": "123456", "path": "/home/infra5/fileA.txt"},
    "fileB.txt": {"host": "172.16.85.116", "username": "infra13", "password": "123456", "path": "/home/infra13/fileB.txt"}
}

# Konfigurasi server tujuan (server X)
target = {
    "host": "172.16.85.111",
    "username": "infra1",
    "password": "1231",
    "upload_path": "/home/infra1/"
}

def download_file(filename, config):
    print(f"Downloading {filename} from {config['host']}")
    transport = paramiko.Transport((config["host"], 22))
    transport.connect(username=config["username"], password=config["password"])
    sftp = paramiko.SFTPClient.from_transport(transport)
    sftp.get(config["path"], filename)
    sftp.close()
    transport.close()
    print(f"{filename} downloaded.")
    return filename

def upload_file(filename):
    print(f"Uploading {filename} to {target['host']}")
    transport = paramiko.Transport((target["host"], 2222))
    transport.connect(username=target["username"], password=target["password"])
    sftp = paramiko.SFTPClient.from_transport(transport)
    sftp.put(filename, target["upload_path"] + filename)
    sftp.close()
    transport.close()
    print(f"{filename} uploaded.")

def main():
    # Download secara paralel
    with ThreadPoolExecutor() as executor:
        futures = [
            executor.submit(download_file, filename, config)
            for filename, config in sources.items()
        ]
        downloaded_files = [f.result() for f in futures]

    # Upload satu per satu
    for file in downloaded_files:
        upload_file(file)

if __name__ == "__main__":
    main()


```

***
by: ikhwan@fedora.linux 
