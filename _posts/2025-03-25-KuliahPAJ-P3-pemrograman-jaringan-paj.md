---
title: PAJ Pertemuan 03 & 04 2025 - Dasar Pemrograman Jaringan
published: true
author: Ikhwan Elyas
description: Automasi jaringan mengacu pada penggunaan perangkat lunak dan alat untuk mengelola jaringan tanpa memerlukan banyak campur tangan manusia. Ini mencakup berbagai tugas, mulai dari konfigurasi perangkat hingga pemantauan jaringan. Dengan mengotomatisasi tugas-tugas ini, organisasi dapat meningkatkan efisiensi operasional, mengurangi kesalahan manusia, dan memastikan konsistensi dalam pengelolaan jaringan.
---

## Pertemuan 03 dan 04: Dasar Pemrograman untuk Jaringan (Python)

**Tujuan Pembelajaran**:
1. Memahami penggunaan Python untuk automasi jaringan
2. Menguasai library dasar seperti `paramiko` dan `netmiko`
3. Mampu membuat koneksi SSH ke perangkat jaringan secara programatik

---

## 1. Istilah yang perlu diketahui: 

### Port, TCP, UDP, Socket dan SSH (08/04/2025)
**Penjelasan Singkat Beberapa Istilah fundamental tools dan protokol dalam jaringan komputer untuk pengiriman data, manajemen koneksi, dan keamanan**

### **1. Port:**
  **Apa itu?** 

  Port adalah angka 16-bit (0 hingga 65535) yang digunakan untuk mengidentifikasi proses atau layanan tertentu yang berjalan pada sebuah perangkat jaringan. Fungsinya mirip dengan nomor kamar di sebuah gedung; alamat IP menunjukkan gedungnya, dan nomor port menunjukkan kamar mana yang dituju. Port memungkinkan banyak aplikasi atau layanan yang berbeda untuk berbagi satu koneksi jaringan fisik. Beberapa port sudah dikenal dan digunakan untuk layanan standar (misalnya, port 80 untuk HTTP, port 443 untuk HTTPS).

  - Sistem "pintu" virtual di komputer (0-65535)  
  - Contoh:  
    - Port 80: HTTP  
    - Port 22: SSH  
    - Port 443: HTTPS  

  - **Analogi**:  
  Seperti nomor kamar di hotel, dimana hotel adalah komputer/server


### **2. TCP (Transmission Control Protocol):**
  **Apa itu?** 

  TCP adalah protokol komunikasi yang berorientasi koneksi (*connection-oriented*). Ini berarti sebelum data dikirim, sebuah koneksi harus dibuat terlebih dahulu antara pengirim dan penerima (melalui proses *handshake*). TCP menjamin pengiriman data yang handal dan terurut. Jika ada paket data yang hilang atau tidak berurutan, TCP akan mendeteksi dan mengirimkannya kembali. Ini cocok untuk aplikasi yang membutuhkan keandalan data, seperti transfer file, browsing web (HTTPS), dan email.

### **3. UDP (User Datagram Protocol):**
  **Apa itu?**

  UDP adalah protokol komunikasi yang tidak berorientasi koneksi (*connectionless*). Data dikirim dalam bentuk datagram tanpa perlu membuat koneksi terlebih dahulu. UDP tidak menjamin pengiriman yang handal atau urutan data. Paket data mungkin hilang atau tiba tidak berurutan. Namun, karena tidak ada overhead untuk membuat dan memelihara koneksi, UDP umumnya lebih cepat daripada TCP. Ini cocok untuk aplikasi yang toleran terhadap kehilangan paket atau yang membutuhkan kecepatan tinggi, seperti streaming video/audio, game online, dan DNS.

* ### **TCP vs UDP**

| **TCP** | **UDP** |
|---------|---------|
| Connection-oriented | Connectionless |
| Reliable (ada ACK) | Unreliable |
| Contoh: Web, Email | Contoh: Video Stream, DNS |

**Diagram TCP Handshake**:

Client ------------------------ Server
|---- SYN --------→|
|←--- SYN-ACK ----|
|---- ACK -------→|

**Diagram UDP Handshake (NO handshake)**:

Client ------------------------ Server
|---- Data Packet --------→|

### **4. Socket:**
**Apa itu?** 

  Secara sederhana, socket adalah *endpoint* dari sebuah koneksi jaringan. Ini merupakan kombinasi dari alamat IP sebuah perangkat dan nomor port tertentu pada perangkat tersebut. Bayangkan seperti stopkontak listrik (IP Address - lokasi perangkat) dan colokan alat elektronik (Port - aplikasi/layanan spesifik di perangkat). Dua socket yang berkomunikasi membentuk sebuah koneksi jaringan.
 
  - Endpoint komunikasi (IP Address + Port + Protocol)  
  - Interface programming untuk jaringan  

  **Contoh Socket Address**:  
  `192.168.1.10:80/TCP`

  - **Level Kerja**:

    - A[Aplikasi (HTTP/FTP)] --> B[Socket API]
    - B --> C[TCP/UDP]
    - C --> D[IP]


### **5. SSH (Secure Shell):**
**Apa itu?** 

  SSH adalah protokol jaringan yang aman yang digunakan untuk membuat koneksi terenkripsi antara klien dan server. Ini sering digunakan untuk mengakses dan mengelola perangkat jaringan (seperti server, router, dan switch) dari jarak jauh melalui *command-line interface* (CLI). SSH mengenkripsi semua komunikasi, termasuk *username*, *password*, dan perintah yang diketik, sehingga mencegah *eavesdropping* dan serangan *man-in-the-middle*. SSH secara default menggunakan port TCP 22.

- **SSH adalah Protokol yang menggunakan:**
  - TCP Port 22
  - Socket untuk komunikasi
  - Enkripsi untuk keamanan

- **Arsitektur SSH:**
  - Client Socket (Random Port) → TCP Connection → Server Socket (Port 22)

- **The Flow:**
  - Aplikasi **(SSH)** → Port **(22)** → Socket **(IP:22)** → Protokol **(TCP)** → Jaringan

  * **Alur Komunikasi:** Sebuah aplikasi (misalnya, klien SSH) akan membuat socket dengan alamat IP lokalnya dan port yang dipilih secara acak (port ephemeral). Aplikasi ini kemudian akan mencoba membuat koneksi ke socket server (alamat IP server dan port SSH 22) menggunakan protokol TCP.


---

## 2. Python dan Library untuk Jaringan: 

Python memiliki beberapa library khusus untuk berinteraksi dengan perangkat jaringan, diataranya:

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

### **Kapan menggunakan yang mana?**
- Gunakan `paramiko` ketika:
  - Membutuhkan kontrol penuh atas SSH connection
  - Bekerja dengan protokol non-standard
  - Membangun solusi custom dari ground up

- Gunakan `netmiko` ketika:
  - Bekerja dengan perangkat jaringan umum (Cisco, Juniper, dll)
  - Membutuhkan implementasi cepat automasi jaringan
  - Ingin menghindari parsing output manual

### Documentation/Wiki Source:
- **Paramiko Docs/Wiki** --> [www.paramiko.org](https://www.paramiko.org/){:target="_blank"}
- **Netmiko Docs/Wiki** --> [github.io/netmiko/docs/netmiko](https://ktbyers.github.io/netmiko/docs/netmiko/index.html){:target="_blank"}

---

## 3. Membuat Koneksi SSH Sederhana
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


* ### **1. Contoh Script SSH dan Paramiko:**

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

* ### **2. Contoh Lain SSH dan Paramiko script:**

```python
import paramiko
import time

# MikroTik device details
host = '192.168.88.1'      # MikroTik IP address
username = 'admin'         # Default username
password = ''              # Default password (blank)
port = 22                  # SSH port

# Simple test commands (read-only)
commands = [
    '/system identity print',
    '/ip address print',
    '/interface print brief',
    '/system resource print'
]

# Create SSH client
client = paramiko.SSHClient()
client.set_missing_host_key_policy(paramiko.AutoAddPolicy())

try:
    # Connect to device
    print(f"Connecting to {host}...")
    client.connect(host, port=port, username=username, password=password, look_for_keys=False)
    print("Connected successfully!\n")
    
    # Create interactive shell
    shell = client.invoke_shell()
    
    # Send commands and get output
    for cmd in commands:
        print(f"Executing: {cmd}")
        shell.send(cmd + '\n')
        time.sleep(1)  # Wait for command to execute
        
        # Read output
        output = ''
        while shell.recv_ready():
            output += shell.recv(4096).decode('utf-8')
            time.sleep(0.1)
        
        # Clean and print output
        clean_output = output.split('\r\n', 1)[-1]  # Remove command echo
        print(clean_output)
        print("-" * 50)  # Separator line

except paramiko.AuthenticationException:
    print("Authentication failed, please verify credentials")
except paramiko.SSHException as e:
    print(f"SSH error: {str(e)}")
except Exception as e:
    print(f"Error: {str(e)}")
finally:
    # Close connection
    if client:
        client.close()
    print("\nDisconnected from device")
```


* ### **3. Contoh Script SSH dan netmiko:**
  `netmiko` menyederhanakan koneksi multi-vendor. Contoh untuk mikrotik device:

```python
from netmiko import ConnectHandler

# MikroTik device configuration
mikrotik = {
    'device_type': 'mikrotik_routeros',
    'host': '192.168.88.1',    # Replace with your MikroTik IP
    'username': 'admin',       # Default is 'admin'
    'password': '',            # Default password is blank
    'port': 22,                # SSH port
}

# Simple test commands (read-only)
test_commands = [
    '/system identity print',          # Show device name
    '/ip address print',               # List all IP addresses
    '/interface print where type=ether',  # Show only Ethernet interfaces
    '/system resource print'           # Show CPU/RAM usage
]

try:
    # Connect to device
    print(f"Connecting to {mikrotik['host']}...")
    with ConnectHandler(**mikrotik) as connection:
        print("Connection successful!\n")
        
        # Execute each test command
        for cmd in test_commands:
            print(f"Executing: {cmd}")
            output = connection.send_command(cmd)
            print(output)
            print("-" * 50)  # Separator line
            
except Exception as e:
    print(f"Error: {str(e)}")
finally:
    print("\nTest completed.")
```

**Keunggulan netmiko:**
- Auto-deteksi perangkat (Cisco, Juniper, Huawei).
- Mendukung send_config_set() untuk konfigurasi multi-line.


* ### **4. Contoh Socket TCP Client Sederhana Python:***

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

### Kerjakan Latihan Praktikum ke 3

1. Tulis Code diatas dengan Aplikasi Editor 
2. Simpan dengan Beri nama file dengan `praktikum3_ssh_paramiko/netmiko/socket_NPM.py` (ubah NPM dengan lima digit terakhir npm anda)
3. Contoh nama file : `praktikum2_ssh_netmiko_22001.py`
4. Jalankan di CLI, perintahnya : python praktikum2_ssh_netmiko_22020.py
5. Setelah Jalankan dan Tidak Jika Tidak Ada Error, Silahkan Upload/Kirim 
6. Masuk ke link ini : [GDRIVE (Google Drive)](https://drive.google.com/drive/folders/1aekuG1Nf9gNFl3vfIVfq-GQcS47r3qvJ?usp=sharing){:target="_blank"} dan BUAT folder dengan nama `PAJ_NPM`
7. Setelah itu masuk ke folder `PAJ_NPM` yg telah anda buat di [GDRIVE (Google Drive)](https://drive.google.com/drive/folders/1aekuG1Nf9gNFl3vfIVfq-GQcS47r3qvJ?usp=sharing){:target="_blank"}, kemudian UPLOAD hasil praktikum anda. 
6. Batas Waktu Kumpul sampai sebelum Pertemuan berikutnya masuk 25/03/2025.

***
by: ikhwan@fedora.linux 

