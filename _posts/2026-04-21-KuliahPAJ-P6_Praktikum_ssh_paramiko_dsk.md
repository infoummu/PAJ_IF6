---
title: PAJ Pertemuan 06 - SSH Automation dengan Paramiko
published: true
author: Ikhwan Elyas
description: Materi praktikum ini membahas otomasi SSH menggunakan library Paramiko di Python. Mencakup dasar-dasar SSH, pengecekan konektivitas dengan ping dan nmap secara manual. Kemudian, diimplementasikan script Python untuk eksekusi perintah remote, sesi interaktif, dan transfer file. Juga disertakan studi kasus untuk backup konfigurasi server jarak jauh secara terprogram, meningkatkan efisiensi administrasi sistem.

---

## Tujuan Praktikum
1. Memahami cara kerja koneksi SSH secara manual dan otomatis.
2. Mampu menggunakan perintah `ping`, `nmap`, dan `ssh` untuk pengecekan konektivitas.
3. Mampu membuat script Python menggunakan library `paramiko` untuk otomasi SSH.
4. Melakukan eksekusi perintah remote ke server LAB secara terprogram.

---

## Dasar Teori

### SSH (Secure Shell)
SSH adalah protokol jaringan yang memungkinkan akses aman ke perangkat remote. Biasa digunakan untuk administrasi server, router, switch, dan perangkat jaringan lainnya.

### Paramiko
Paramiko adalah library Python yang mengimplementasikan protokol SSHv2. Library ini memungkinkan kita membuat koneksi SSH, menjalankan perintah remote, transfer file (SFTP), dan mengelola sesi secara terprogram.

### Keuntungan Otomasi SSH dengan Python
- Menghindari input manual berulang.
- Bisa diparalelkan untuk banyak perangkat.
- Hasil eksekusi dapat langsung disimpan ke file atau database.
- Mudah diintegrasikan dengan log, alert, dan sistem reporting.

---

## Alat dan Bahan
- Laptop praktikan (Windows/Linux/macOS)
- Python 3.7+ terinstal
- Library `paramiko`, `python-nmap` (opsional)
- Koneksi jaringan ke **LAB server dosen** (IP akan diumumkan saat praktikum)
- Text editor (VS Code, Notepad++, atau IDLE)

### Instalasi Library
Jalankan perintah berikut di terminal/command prompt:

```bash
pip install paramiko
pip install python-nmap   # untuk nmap dari Python (opsional)
```



## Bagian 0: KONFIGURASI ONLINE/OFFLINE

### Yang OFFLINE 
```bash
http://192.168.4.1:4000/PAJ_IF6/

tool: ssh
user : infra
pass : infra
host/ip : 192.168.4.1 
port : 2222

# PERINTAH BAWAAN : 
ssh infra@192.168.4.1
# PERINTAH DENGAN PORT BERBEDA: 
ssh infra@192.168.4.1 -p 2222
```

### Yang ONLINE 
```bash
ONLINE MATERI : 
https://infoummu.github.io/PAJ_IF6/

tool: ssh
user : bandit0
pass : bandit0
host/ip : overthewire.org / bandit.labs.overthewire.org 
port : 2220

ssh bandit0@bandit.labs.overthewire.org -p 2220

# PERINTAH BAWAAN : 
ssh bandit0@bandit.labs.overthewire.org
# PERINTAH DENGAN PORT BERBEDA: 
ssh bandit0@bandit.labs.overthewire.org -p 2220

```


## Bagian 1: Praktikum Manual
Tujuan: Memastikan konektivitas dasar dan memahami perintah manual sebelum otomasi.

### 1.1 Pengecekan dengan Ping
Perintah ping digunakan untuk mengecek apakah host tujuan dapat dijangkau.

Sintaks:

```bash
ping <IP_LAB_DOSEN>
```
Contoh:

```bash
ping 192.168.1.100
```
Penjelasan output:

`Reply from` ... → Host aktif

`Request timed out` atau `Destination unreachable` → Host tidak merespon

`TTL` (Time To Live) menunjukkan jumlah hop maksimal

**Catatan:** Di Linux/Mac, ping berjalan terus. Tekan Ctrl+C untuk menghentikan.

### 1.2 Scan Port dengan Nmap
Nmap digunakan untuk mengetahui port apa saja yang terbuka pada host target.

Sintaks dasar:

```bash
nmap <IP_LAB_DOSEN>
```
Scan port SSH (port 22) secara spesifik:

```bash
nmap -p 22 <IP_LAB_DOSEN>
```

Penjelasan output:

`22/tcp open ssh` → Port SSH terbuka

`filtered` → Ada firewall yang memblokir

`closed` → Port tidak terbuka

**Catatan:** Nmap harus diinstal terlebih dahulu (bukan hanya library Python). Unduh dari https://nmap.org jika belum ada.

### 1.3 Koneksi SSH Manual
Gunakan perintah SSH dari terminal untuk login ke server LAB dosen.

Sintaks:

```bash
ssh <username>@<IP_LAB_DOSEN>
```
Contoh:

```bash
ssh student@192.168.1.100
```
Yang akan terjadi:
- Verifikasi fingerprint (ketik yes jika baru pertama kali)
- Masukkan password (tidak akan tampil saat diketik)
- Setelah login, tampil prompt shell server
- Ketik exit atau logout untuk keluar

Perintah yang bisa dicoba setelah login:

```bash
whoami          # menampilkan user saat ini
hostname        # nama server
ip a            # lihat IP server (Linux)
ls -la          # daftar file di home
```

## Bagian 2: Otomasi dengan Python + Paramiko
**Tujuan:** Membuat script Python untuk melakukan ping dan SSH secara otomatis ke server LAB dosen.

### 2.1 Otomasi Ping dari Python
Python bisa menjalankan perintah sistem (termasuk ping) menggunakan modul subprocess.

Buat file ping_otomatis.py:

```python
import subprocess
import sys

def ping_host(ip_address):
    """
    Melakukan ping ke IP address.
    Gunakan parameter -n 1 untuk Windows, -c 1 untuk Linux/Mac
    """
    # Deteksi sistem operasi
    param = '-n' if sys.platform.lower().startswith('win') else '-c'
    
    # Perintah ping
    command = ['ping', param, '1', ip_address]
    
    result = subprocess.run(command, capture_output=True, text=True)
    
    if result.returncode == 0:
        print(f"[SUKSES] {ip_address} merespon ping.")
        print(result.stdout)
    else:
        print(f"[GAGAL] {ip_address} tidak merespon ping.")
        print(result.stderr)

if __name__ == "__main__":
    # Ganti dengan IP LAB dosen yang sesungguhnya
    IP_TUJUAN = "192.168.1.100"  
    ping_host(IP_TUJUAN)
```

Jalankan:

```bash
python ping_otomatis.py
```
### 2.2 Otomasi Ping dari Python (Contoh 2)

```python
import subprocess

def check_ping(ip):
    # -c 1 untuk mengirim 1 paket saja
    response = subprocess.run(['ping', '-c', '1', ip], stdout=subprocess.DEVNULL)
    
    if response.returncode == 0:
        print(f"Device {ip} is UP")
    else:
        print(f"Device {ip} is DOWN")

check_ping("192.168.1.10")
```

### 2.3 Otomasi SSH dengan Paramiko
Script ini akan connect ke server LAB, menjalankan beberapa perintah, lalu menampilkan outputnya.

Buat file ssh_otomatis.py:

```python
import paramiko
import time
import sys

def ssh_execute_command(hostname, username, password, command, port=22):
    """
    Koneksi SSH ke server dan menjalankan satu perintah.
    """
    client = paramiko.SSHClient()
    
    # Auto menambahkan host key (untuk lab, HATI-HATI di produksi)
    client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    
    try:
        print(f"[INFO] Menghubungi {hostname}:{port} sebagai {username}...")
        client.connect(hostname, port=port, username=username, password=password, timeout=5)
        
        print("[INFO] Koneksi berhasil. Menjalankan perintah...")
        stdin, stdout, stderr = client.exec_command(command)
        
        # Baca output
        output = stdout.read().decode('utf-8')
        error = stderr.read().decode('utf-8')
        
        if output:
            print(f"[OUTPUT] {command}\n{output}")
        if error:
            print(f"[ERROR] {command}\n{error}")
            
        return output, error
        
    except paramiko.AuthenticationException:
        print("[ERROR] Gagal login: username atau password salah.")
    except paramiko.SSHException as e:
        print(f"[ERROR] SSH error: {e}")
    except Exception as e:
        print(f"[ERROR] Umum: {e}")
    finally:
        client.close()
        print("[INFO] Koneksi ditutup.")
        
    return None, None

if __name__ == "__main__":
    # === KONFIGURASI LAB ===
    # Ganti dengan data dari dosen
    IP_SERVER = "192.168.1.100"    # IP server LAB dosen
    USERNAME = "student"            # username yang diberikan
    PASSWORD = "lab123"             # password yang diberikan
    # ======================
    
    # Contoh perintah yang akan dijalankan
    perintah_list = [
        "whoami",
        "hostname",
        "uptime",
        "ip a | grep inet"   # menampilkan IP (Linux)
    ]
    
    for cmd in perintah_list:
        print(f"\n--- Menjalankan: {cmd} ---")
        ssh_execute_command(IP_SERVER, USERNAME, PASSWORD, cmd)
        time.sleep(1)  # jeda antar perintah
```

Jalankan:

```bash
python ssh_otomatis.py
```

### 2.4 Eksekusi Interaktif (Shell Session)
Paramiko juga bisa membuat shell interaktif seperti SSH manual.

Buat file ssh_shell_interaktif.py:

```python
import paramiko
import time

def ssh_interactive_session(hostname, username, password, port=22):
    client = paramiko.SSHClient()
    client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    
    try:
        client.connect(hostname, port=port, username=username, password=password)
        print("[INFO] Membuka shell interaktif. Ketik 'exit' untuk keluar.")
        
        # Buka channel shell
        channel = client.invoke_shell()
        time.sleep(1)
        
        # Bersihkan buffer awal
        if channel.recv_ready():
            channel.recv(65535)
        
        while True:
            command = input("$> ")
            if command.lower() == 'exit':
                break
            
            channel.send(command + "\n")
            time.sleep(0.5)  # tunggu output
            
            # Baca output
            while channel.recv_ready():
                output = channel.recv(65535).decode('utf-8')
                print(output, end='')
                
    except Exception as e:
        print(f"[ERROR] {e}")
    finally:
        client.close()
        print("[INFO] Sesi selesai.")

if __name__ == "__main__":
    ssh_interactive_session("192.168.1.100", "student", "lab123")
```

**Catatan:** Mode interaktif berguna untuk perintah yang butuh konfirmasi atau berjalan lama.

## Bagian 3: Studi Kasus & Tugas
### 3.1 Studi Kasus: Backup Konfigurasi Remote

Buat script yang:

- SSH ke server LAB.
- Backup file konfigurasi (misal /etc/hosts atau file tertentu).
- Simpan hasil backup ke file lokal dengan nama backup_YYYYMMDD_HHMMSS.txt.

Kerangka solusi:

```python
import paramiko
from datetime import datetime

def backup_remote_file(host, user, pwd, remote_path, local_folder="./backup"):
    # Implementasikan sendiri
    pass
```



---
By: IkhwanElyas@Fedora.Linux
