---
title: PAJ Pertemuan 02
published: true
author: Ikhwan Elyas
---

## PERTEMUAN KEDUA 02 - Praktikkum 1 :

|Status   | : Online                   |
|Schedule | : Offline                  |
|Waktu    | : 02/03/2023               |
|Tema     | : Praktikum 1              |



## CLI (Command Line Interface) : 
- CLI di Windows: 
    - CMD
    - PS (power Shell)
- CLI di Linux 
    - Tilix/Terminal


## APLIKASI EDITOR :

- Contoh EDITOR  :
    - Sublime Text
    - Visual Studio/VSCodium (yg saya pakai)
    - Atom
    - Notepad++ / NotepadPP


## Pengganti Pertemuan ke 2

1. Tulis Code dibawah dengan Aplikasi Editor 
2. Simpan dengan Beri nama file dengan `praktikum1_ping_NPM.py` (ubah NPM dengan lima digit terakhir npm anda)
3. Contoh nama file : `praktikum1_ping_19001.py`
4. Jalankan di CLI, perintahnya : python praktikum1_ping_19020.py
5. Setelah Jalankan dan Tidak Ada Error, Silahkan Upload/Kirim ke folder `PAJ_NPM` anda yang di [GDRIVE (Google Drive)](https://drive.google.com/drive/folders/1aekuG1Nf9gNFl3vfIVfq-GQcS47r3qvJ?usp=sharing){:target="_blank"}.
6. Batas Waktu Kumpul sampai sebelum Pertemuan berikutnya masuk 28/03/2022.

## CODE 

```python 
# Code/Script praktikum 1
# Tema : ping for test connection 
# nama : ??
# npm  : 19???

import os

HOST="127.0.0.1"
print("\n Ping to " + HOST)
print(" HASILnya:")
# HASIL = os.system("ping -c 1 " + HOST + " &> /dev/null")	# untuk linux
HASIL = os.system("ping -n 1 " + HOST + " > NULL")		# untuk Windows

if HASIL == 0:
    print(" Terkoneksi ke " + HOST)
else:
    print(" TIDAK Terkoneksi ke " + HOST)

print("\n")

```






***
by: ikhwan@fedora.linux 

