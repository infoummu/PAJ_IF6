---
title: PAJ Pertemuan 03 2025 - Dasar Pemrograman Jaringan
published: true
author: Ikhwan Elyas
description: Automasi jaringan mengacu pada penggunaan perangkat lunak dan alat untuk mengelola jaringan tanpa memerlukan banyak campur tangan manusia. Ini mencakup berbagai tugas, mulai dari konfigurasi perangkat hingga pemantauan jaringan. Dengan mengotomatisasi tugas-tugas ini, organisasi dapat meningkatkan efisiensi operasional, mengurangi kesalahan manusia, dan memastikan konsistensi dalam pengelolaan jaringan.
---

## Pertemuan 2: Dasar Pemrograman untuk Jaringan (Python)

**Tujuan Pembelajaran**:
1. Memahami penggunaan Python untuk automasi jaringan
2. Menguasai library dasar seperti `paramiko` dan `netmiko`
3. Mampu membuat koneksi SSH ke perangkat jaringan secara programatik

---

## Istilah yang perlu diketahui : 

### Penjelasan Singkat Socket, Port, TCP, UDP, dan SSH  
    
#### 1. **Port**  
- **Fungsi**:  
  - Nomor virtual (16-bit, range `0–65535`) yang mengidentifikasi layanan/aplikasi pada perangkat.  
- **Contoh**:  
  - `Port 80` → HTTP  
  - `Port 22` → SSH  
- **Tujuan**:  
  - Mengarahkan data ke aplikasi yang sesuai.  

#### 2. **Socket**  
- **Fungsi**:  
  - Kombinasi **IP Address + Port** (contoh: `192.168.1.1:80`).  
- **Peran**:  
  - Titik akhir (*endpoint*) komunikasi antara dua perangkat.  

#### 3. **TCP (Transmission Control Protocol)**  
- **Karakteristik**:  
  - **Connection-oriented** → Membuat koneksi dulu (*3-way handshake*).  
  - **Reliable** → Data dikirim berurutan dan di-retransmit jika hilang.  
  - **Contoh Penggunaan**:  
    - HTTP (web), SMTP (email), SSH.  

#### 4. **UDP (User Datagram Protocol)**  
  - **Karakteristik**:  
    - **Connectionless** → Tidak ada handshake.  
    - **Unreliable** → Cepat, tapi tanpa jaminan pengiriman.  
  - **Contoh Penggunaan**:  
    - Video streaming, VoIP, DNS.  

#### 5. **SSH (Secure Shell)**  
  - **Fungsi**:  
    - Protokol untuk akses jarak jauh **terenkripsi**.  
  - **Port Default**:  
    - `22` (menggunakan TCP).  
  - **Keamanan**:  
    - Enkripsi data dan autentikasi aman.  


- Aplikasi **(SSH)** → Port **(22)** → Socket **(IP:22)** → Protokol **(TCP)** → Jaringan

---
 

### 1. Review Python Dasar

#### Penjelasan
Sebelum masuk ke automasi jaringan, perlu memahami sintaks Python dasar:

- **Variabel & Tipe Data**: String, integer, list, dictionary
- **Struktur Kontrol**: `if-else`, `for/while` loop
- **Fungsi**: Definisikan fungsi untuk modularitas kode
- **File Handling**: Membaca/menulis file (e.g., konfigurasi jaringan)

#### Contoh Kode

```python
# Fungsi menghitung subnet
def calculate_subnet(ip, prefix):
    import ipaddress
    network = ipaddress.IPv4Network(f"{ip}/{prefix}", strict=False)
    return str(network.network_address)
```

## 2. Library Jaringan Python

### Penjelasan
Python memiliki library khusus untuk berinteraksi dengan perangkat jaringan:

- **`socket`**: Untuk komunikasi low-level (TCP/UDP)
- **`paramiko`**: Implementasi SSHv2 (remote login, command execution)
- **`netmiko`** (ekstensi paramiko): Support multi-vendor (Cisco, Juniper, dll.)

### Perbedaan Paramiko vs Netmiko

| **Paramiko** | **Netmiko** |
|--------------|-------------|
| Library dasar SSH | High-level wrapper untuk `paramiko` |
| Butuh manual parsing output | Auto-handle konsistensi output perangkat |
| Cocok untuk protokol custom | Optimasi untuk perangkat jaringan |
| Lebih fleksibel untuk berbagai use case | Khusus untuk automasi jaringan |
| Membutuhkan lebih banyak kode boilerplate | Menyederhanakan operasi jaringan umum |
| Support SSH exec dan shell channel | Menambahkan abstraksi vendor-specific |

**Kapan menggunakan yang mana?**
- Gunakan `paramiko` ketika:
  - Membutuhkan kontrol penuh atas SSH connection
  - Bekerja dengan protokol non-standard
  - Membangun solusi custom dari ground up

- Gunakan `netmiko` ketika:
  - Bekerja dengan perangkat jaringan umum (Cisco, Juniper, dll)
  - Membutuhkan implementasi cepat automasi jaringan
  - Ingin menghindari parsing output manual

## 3. Membuat Koneksi SSH Sederhana

### Penjelasan
Langkah-langkah koneksi SSH dengan `paramiko`:

1. **Buat instance `SSHClient`**
   - Inisialisasi objek client SSH
   - Atur kebijakan host key (auto-approve untuk lab)

2. **Hubungkan ke perangkat**
   - Gunakan metode `connect()`
   - Butuh parameter:
     - Alamat IP/hostname
     - Username
     - Password
     - Port (default 22)

3. **Eksekusi command**
   - Gunakan `exec_command()` untuk perintah tunggal
   - Untuk session interaktif, gunakan `invoke_shell()`
   - Tangkap output stdout/stderr

4. **Tutup koneksi**
   - Selalu tutup koneksi di blok `finally`
   - Hindari connection leak

## 4. Contoh Script Paramiko:

```python
import paramiko

# Konfigurasi perangkat
host = "192.168.1.1"
username = "admin"
password = "password123"

# Buat client SSH
ssh = paramiko.SSHClient()
ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())  # Auto-approve host key

try:
    # Koneksi SSH
    ssh.connect(host, username=username, password=password)
    
    # Eksekusi command
    stdin, stdout, stderr = ssh.exec_command("show running-config")
    output = stdout.read().decode()
    print(output)

except Exception as e:
    print(f"Error: {e}")

finally:
    ssh.close()  # Pastikan koneksi ditutup
```

**Penjelasan Script:**
- **AutoAddPolicy():** Otomatis menerima fingerprint perangkat (tidak aman untuk produksi, hanya untuk lab).
- **exec_command():** Mengirim command ke perangkat.
- **stdout.read():** Membaca output perangkat.

## 5. Optimasi dengan netmiko
**Penjelasan:**
`netmiko` menyederhanakan koneksi multi-vendor. Contoh untuk Cisco IOS:

```python
from netmiko import ConnectHandler

device = {
    "device_type": "cisco_ios",
    "host": "192.168.1.1",
    "username": "admin",
    "password": "password123",
}

try:
    # Koneksi dan eksekusi command
    with ConnectHandler(**device) as conn:
        output = conn.send_command("show ip interface brief")
        print(output)
        
except Exception as e:
    print(f"Error: {e}")
```

**Keunggulan netmiko:**
- Auto-deteksi perangkat (Cisco, Juniper, Huawei).
- Mendukung send_config_set() untuk konfigurasi multi-line.


## 6. Penggunaan Library Socket Python

### 1. TCP Client Sederhana
```python
import socket

# Membuat TCP socket
tcp_client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

try:
    # Menghubungkan ke server (contoh: web server)
    tcp_client.connect(("example.com", 80))
    
    # Mengirim request HTTP sederhana
    request = "GET / HTTP/1.1\r\nHost: example.com\r\n\r\n"
    tcp_client.send(request.encode())
    
    # Menerima response (4096 bytes pertama)
    response = tcp_client.recv(4096)
    print(response.decode())

except socket.error as err:
    print(f"Socket error: {err}")

finally:
    tcp_client.close()

```

### 2. UDP Client/Server Sederhana
Server UDP:
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

Client UDP:

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

TCP vs UDP:
   - TCP (SOCK_STREAM): Handshake, reliable, ordered
   - UDP (SOCK_DGRAM): Connectionless, faster, no guarantee

Port yang umum:
   - 80 (HTTP), 443 (HTTPS)
   - 22 (SSH), 53 (DNS)

Keamanan:
- Raw socket (ICMP) memerlukan privilege admin
- Selalu gunakan try-finally untuk close socket
- Gunakan timeout untuk menghindari blocking

***
by: ikhwan@fedora.linux 

