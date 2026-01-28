# Planet: Earth CTF

[vulnhub_link](https://www.vulnhub.com/entry/blogger-1,675/))

## Mencari Alamat IP

`fping -aqg 192.168.xxx.0/24`

> Note: you can use netdiscover

## Nmap Scan

| Port | State | Service |
| --- | --- | --- |
| 22  | open | ssh |
| 80  | open | http |

## Server HTTP / Port 80

![](file:///home/kal/Documents/Pentest/Vulnhub_Writeup/Done/Blogger/img/web.png?msec=1769584939190)

## Enumerasi Web

> You can use tools like dirbuster or ffuf if you like

```shell
gobuster dir -w your_wordlist.txt -u http://<YOUR_MACHINE_IP>/ -r html,php,txt -o gobuster.log
```

| assets | Status: 301 |
| --- | --- |
| css | Status: 301 |
| images | Status: 301 |
| index.html | Status: 200 |
| js  | Status: 301 |

Didalam direktori /assets

![](file:///home/kal/Documents/Pentest/Vulnhub_Writeup/Done/Blogger/img/assets.png?msec=1769584939075)

Saat menjelajahi direktori font, saya menemukan direktori lain bernama blog.

![](file:///home/kal/Documents/Pentest/Vulnhub_Writeup/Done/Blogger/img/blog.png?msec=1769584939077)

> Agar blog berfungsi, Anda perlu menambahkan blogger.thm ke /etc/hosts. (Baca deskripsi mesin blogger di Vulnhub)

![](file:///home/kal/Documents/Pentest/Vulnhub_Writeup/Done/Blogger/img/blogger.png?msec=1769584939123)

## File Upload Vulnerability

Mengklik salah satu artikel akan memberi kita URL ini.

```html
http://blogger.thm/assets/fonts/blog/?p=29
```

Saat melihat ke bawah, saya menemukan bahwa kita bisa memberikan komentar dengan lampiran gambar.![](file:///home/kal/Documents/Pentest/Vulnhub_Writeup/Done/Blogger/img/upload.png?msec=1769584939078)

Setelah melihat ini, mungkin saya bisa mengunggah file PHP dan mendapatkan reverse shell? Payload yang saya gunakan dalam tulisan ini ada [di sini](https://github.com/pentestmonkey/php-reverse-shell), tetapi mengunggah file PHP tidak diizinkan. Saya mencoba mengubah format menjadi .jpg alih-alih .php dan jelas tidak berhasil.

Jadi saya mencoba menambahkan header format GIF "GIF87a;" ke file PHP reverse shell dan sekarang file PHP berhasil diunggah.

![](file:///home/kal/Documents/Pentest/Vulnhub_Writeup/Done/Blogger/img/uploaded.png?msec=1769584939078)

Pada listener sekarang saya mendapatkan shell![](file:///home/kal/Documents/Pentest/Vulnhub_Writeup/Done/Blogger/img/Reverse_Shell.png?msec=1769584939079)

Saat memasuki direktori /home, saya melihat 3 pengguna yang tersedia.![](file:///home/kal/Documents/Pentest/Vulnhub_Writeup/Done/Blogger/img/user.png?msec=1769584939080)

flag user ada di folder James, tetapi ketika saya mencoba menampilkan isi user.txt, ini yang terjadi.

![](file:///home/kal/Documents/Pentest/Vulnhub_Writeup/Done/Blogger/img/user_flag.png?msec=1769584939080)

## Mencari Jalan Keluar Dan Mendapatkan Akses Root

Karena mesin tersebut memiliki port SSH yang terbuka, saya mencoba melakukan brute force dengan Hydra dan tetap tidak berhasil karena tidak mendukung metode otentikasi kata sandi. Menjalankan perintah `su` juga tidak berfungsi karena perlu dijalankan di lingkungan terminal, dari sini saya mencoba membuat shell dengan menggunakan Python tetapi pertama-tama saya perlu mengetahui apakah mesin ini memiliki Python. Untuk mengetahuinya, saya menjalankan perintah ini.

```shell
$ which python
$ which python3
/usr/bin/python3
```

dan saya menemukan bahwa mesin ini menggunakan python3, jadi saya menjalankan shell menggunakan perintah ini.

```shell
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

![](file:///home/kal/Documents/Pentest/Vulnhub_Writeup/Done/Blogger/img/spawning_shell.png?msec=1769584939081)

Kemudian saya mengubah variabel lingkungan TERM menjadi xterm untuk mendapatkan lingkungan shell lengkap.

```shell
export TERM=xterm
```

Sekarang saya dapat menjalankan perintah `su` untuk mengakses salah satu dari tiga akun yang tersedia. Upaya login yang berhasil adalah 'vagrant' dengan kata sandi 'vagrant'.

![](file:///home/kal/Documents/Pentest/Vulnhub_Writeup/Done/Blogger/img/vagrant.png?msec=1769584939082)

Tapi saya masih belum bisa menampilkan isi file user.txt. Mungkin saya perlu menjadi root terlebih dahulu? Menjalankan perintah 'sudo -l' memberi saya ini.

![](file:///home/kal/Documents/Pentest/Vulnhub_Writeup/Done/Blogger/img/sudo_l.png?msec=1769584939082)

Pada dasarnya, ini berarti saya dapat menjalankan apa pun dengan sudo tanpa perlu kata sandi, jadi saya mencoba menjalankan perintah cat yang sama tetapi dengan sudo.

![](file:///home/kal/Documents/Pentest/Vulnhub_Writeup/Done/Blogger/img/user_flag_success.png?msec=1769584939083)

> Decode flag dari base64 dan Anda akan mendapatkan flag tersebut.

Sekarang untuk mendapatkan hak akses root, cukup tingkatkan hak akses pengguna menjadi root dengan perintah 'sudo su' dan akses direktori /root.

![](img/root_flag.png)
