# Jarkom-Modul-2-IT25-2023

## Modul 2 Jarkom IT25

### Anggota :

- ANDYANA MUHANDHATUL NABILA - 5027211029
- ANDREAS TIMOTIUS PARHORASAN SIHOMBING - 5027211019

## No 1

Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian untuk kelompok IT25 adalah pada topologi 8 dan saya buat dan hasilnya sebagai berikut

[![Whats-App-Image-2023-10-17-at-19-21-11-b4e6fe44.jpg](https://i.postimg.cc/d3Nxj5Dr/Whats-App-Image-2023-10-17-at-19-21-11-b4e6fe44.jpg)](https://postimg.cc/H85zp4wk)

## Soal no 2-8 "pengaturan GNS"

saya buat script untuk me setup dulu semuanya nama scriptnya `dns_master.sh`untuk no 2-8

```
#!/bin/bash

apt install -y bind9 bind9utils bind9-doc dnsutils
rm -f /var/cache/bind/*
# Konfigurasi di Server Yudhistira

echo "Memperbaiki konfigurasi DNS untuk Yudhistira..."

cat > /etc/bind/named.conf.local <<EOL
zone "it25.com" {
    type master;
    file "/etc/bind/db.it25";
    allow-transfer { 192.168.2.3; };
};

zone "1.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/1.168.192.in-addr.arpa";
};
EOL

cat > /etc/bind/db.it25 <<EOL
\$TTL    604800
@       IN      SOA     it25.com. admin.it25.com. (
                              3
                         604800
                          86400
                        2419200
                         604800 )
@               IN      NS      Yudhistira.it25.com.
@               IN      NS      Werkudara.it25.com.
Yudhistira      IN      A       192.168.2.4
Werkudara       IN      A       192.168.2.3
arjuna          IN      A       192.168.2.2
www.arjuna      IN      A       192.168.2.2
abimanyu        IN      A       192.168.1.4
www.abimanyu    IN      A       192.168.1.4
parikesit.abimanyu  IN  A       192.168.1.4
www.parikesit.abimanyu IN A     192.168.1.4
baratayuda.abimanyu     IN      NS      Werkudara.it25.com.
EOL

# Konfigurasi zona terbalik
cat > /etc/bind/1.168.192.in-addr.arpa <<EOL
; BIND reverse data file for broadcast zone
\$TTL    604800
@       IN      SOA     abimanyu.it25.com. root.abimanyu.it25.com. (
                              1         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS      abimanyu.it25.com.
1.168.192.in-addr.arpa          IN      NS      abimanyu.it25.com.
4               IN      PTR     abimanyu.it25.com.
EOL

# Restart BIND9
/etc/init.d/bind9 restart

echo "Perbaikan konfigurasi DNS untuk Yudhistira selesai."

```

Kode bash di atas digunakan untuk mengkonfigurasi layanan DNS (Domain Name System). Berikut adalah penjelasan singkat langkah-langkah dalam kode tersebut:

1. Pertama, kode ini menginstal paket-paket terkait DNS, seperti "bind9", "bind9utils", "bind9-doc", dan "dnsutils" dengan perintah `apt install`.

2. Kemudian, kode ini menghapus cache DNS dengan `rm -f /var/cache/bind/*` untuk membersihkan cache yang ada.

3. Konfigurasi DNS lokal dimulai dengan menciptakan atau mengedit file "named.conf.local" pada direktori /etc/bind. Dalam file ini, dua zona DNS didefinisikan:

   - Zona "it25.com" adalah zona master yang merujuk pada file "db.it25". Hanya IP address 192.168.2.3 yang diizinkan untuk melakukan transfer zona.
   - Zona "1.168.192.in-addr.arpa" juga adalah zona master yang merujuk pada file "1.168.192.in-addr.arpa".

4. Selanjutnya, kode ini membuat atau mengedit file "db.it25" yang berisi informasi tentang nama domain dan alamat IP mereka untuk zona "it25.com". Ini mencakup definisi Name Server (NS), record A (alamat IP) untuk beberapa nama domain, seperti "Yudhistira.it25.com," "Werkudara.it25.com," dan lainnya.

5. Konfigurasi zona terbalik juga diatur dalam file "1.168.192.in-addr.arpa" yang mencocokkan alamat IP dengan nama domain. Ada satu entri PTR (Pointer) yang menghubungkan alamat IP "192.168.1.4" dengan nama domain "abimanyu.it25.com".

6. Setelah konfigurasi DNS selesai, kode ini merestart layanan BIND9 dengan perintah `/etc/init.d/bind9 restart` untuk menerapkan perubahan.

7. Terakhir, kode mencetak pesan "Perbaikan konfigurasi DNS untuk Yudhistira selesai" untuk memberi tahu pengguna bahwa konfigurasi selesai dan layanan DNS telah dikonfigurasi dengan benar pada server "Yudhistira".

## Web server No2 dan 3

Pada nomor 2 dan 3 memiliki persamaan yaitu deploy maka script nya sebagai berikut :

```
#!/bin/bash

echo -e "\nnameserver 192.168.2.4\nnameserver 192.168.2.3\nnameserver 192.168.122.1" > /etc/resolv.conf

# Instal paket yang diperlukan
apt-get update
apt-get install -y software-properties-common unzip
add-apt-repository ppa:ondrej/php
apt-get install -y nginx

# Instal PHP
apt-get install -y php7.0 php7.0-fpm php7.0-cli php7.0-common php7.0-json php7.0-opcache php7.0-mysql php7.0-mbstring php7.0-mcrypt php7.0-zip

service php7.0-fpm start
service php7.0-fpm restart
# Tautan Google Drive (tanpa perlu konfirmasi)
google_drive_link="https://drive.usercontent.google.com/uc?id=17tAM_XDKYWDvF-JJix1x7txvTBEax7vX&export=download"

# Nama file yang akan disimpan
output_file="arjuna.zip"

# Unduh file dari Google Drive
curl -L -o "$output_file" "$google_drive_link"

# Mengekstrak file (jika perlu)
unzip "$output_file"

# Ganti nama direktori arjuna.yyy.com menjadi arjuna.it25.com
mv arjuna.yyy.com arjuna.it25.com

# Pindahkan direktori ke /var/www
mv arjuna.it25.com /var/www/html

# Buat file konfigurasi arjuna.it25.com
echo 'server {
    listen 80;
    listen [::]:80;

    root /var/www/html/arjuna.it25.com/;
    index index.php index.html index.htm;

    server_name arjuna.it25.com www.arjuna.it25.com;

    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
' > /etc/nginx/sites-available/default

# rm /etc/nginx/sites-enabled/default
# ln -s /etc/nginx/sites-available/arjuna.it25.com /etc/nginx/sites-enabled/

# Restart Nginx
service nginx restart
# Mengecek konfigurasi Nginx
nginx -t

```

Skrip bash di atas adalah serangkaian perintah yang bertujuan untuk mengatur lingkungan server. Pertama-tama, skrip ini mengatur DNS dengan menambahkan tiga nameserver ke file /etc/resolv.conf. Selanjutnya, skrip melakukan instalasi paket yang diperlukan, termasuk 'software-properties-common', 'unzip', dan 'nginx'. Selain itu, PHP versi 7.0 dan modul-modul yang diperlukan juga diinstal.

Setelah instalasi paket selesai, skrip ini mengunduh sebuah file dari Google Drive menggunakan curl, dan jika perlu, mengekstraknya dan mengganti nama direktori. Direktori tersebut kemudian dipindahkan ke /var/www/html.

Selanjutnya, skrip membuat konfigurasi Nginx untuk situs web 'arjuna.it25.com', menentukan root direktori server web, file index, server name, dan aturan untuk meng-handle file PHP. Setelah konfigurasi selesai, skrip merestart server Nginx untuk menerapkan perubahan.

Terakhir, skrip menggunakan perintah 'nginx -t' untuk memeriksa validitas konfigurasi Nginx. Perlu diingat bahwa beberapa baris dalam skrip yang di-comment mungkin merupakan komentar atau perintah yang dinonaktifkan sementara. Keseluruhan skrip ini adalah langkah-langkah yang kompleks dan perlu dieksekusi dengan hati-hati, serta pastikan bahwa izin yang diperlukan telah diberikan.

## no 9 - 10

Pada nomor diminta untuk men-deploy yang berasal dari resource arjuna ke semua worker
Berikut script workernya

```
#!/bin/bash
#!/bin/bash

echo -e "\nnameserver 192.168.2.4\nnameserver 192.168.2.3\nnameserver 192.168.122.1" > /etc/resolv.conf

# Instal paket yang diperlukan
apt-get update
apt-get install -y software-properties-common unzip
add-apt-repository ppa:ondrej/php
apt-get install -y nginx

# Instal PHP
apt-get install -y php7.0 php7.0-fpm php7.0-cli php7.0-common php7.0-json php7.0-opcache php7.0-mysql php7.0-mbstring php7.0-mcrypt php7.0-zip

service php7.0-fpm start
service php7.0-fpm restart

# Buat konfigurasi Load Balancer dengan Nginx untuk Arjuna
echo 'upstream backend_servers {
    server 192.168.1.5:8001; # Prabakusuma
    server 192.168.1.4:8002; # Abimanyu
    server 192.168.1.6:8003; # Wisanggeni
}

server {
    listen 80;
    listen [::]:80;

    server_name arjuna.it25.com www.arjuna.it25.com;

    location / {
        proxy_pass http://backend_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    location ~ /\.ht {
        deny all;
    }
}
' > /etc/nginx/sites-available/default

# Restart Nginx
service nginx restart

# Mengecek konfigurasi Nginx
nginx -t


```

Pertama-tama, kode ini mengatur DNS dengan menambahkan tiga nameserver ke file /etc/resolv.conf. Kemudian, ia melakukan instalasi paket yang diperlukan, termasuk 'software-properties-common', 'unzip', dan 'nginx', serta PHP versi 7.0 beserta beberapa modul yang dibutuhkan.

Setelah instalasi paket selesai, kode ini membuat konfigurasi Load Balancer dengan Nginx untuk situs web 'arjuna.it25.com'. Konfigurasi ini menentukan backend servers yang terdiri dari tiga alamat IP dan port yang berbeda, serta mengarahkan lalu lintas ke server-server tersebut.

Selanjutnya, kode ini membuat file konfigurasi Nginx untuk situs 'arjuna.it25.com', yang mengatur proxy pass ke backend servers. Setelah konfigurasi selesai, ia merestart server Nginx untuk menerapkan perubahan. Terakhir, kode ini menggunakan perintah 'nginx -t' untuk memeriksa validitas konfigurasi Nginx.

Untuk worker yang lainnya konfigurasinya sama hanya saja beda port sesuai soalnya Prabakusuma:8001 Abimanyu:8002 Wisanggeni:8003

dan untuk load balancernya yaitu arjuna

```
#!/bin/bash
#!/bin/bash

echo -e "\nnameserver 192.168.2.4\nnameserver 192.168.2.3\nnameserver 192.168.122.1" > /etc/resolv.conf

# Instal paket yang diperlukan
apt-get update
apt-get install -y software-properties-common unzip
add-apt-repository ppa:ondrej/php
apt-get install -y nginx

# Instal PHP
apt-get install -y php7.0 php7.0-fpm php7.0-cli php7.0-common php7.0-json php7.0-opcache php7.0-mysql php7.0-mbstring php7.0-mcrypt php7.0-zip

service php7.0-fpm start
service php7.0-fpm restart

# Buat konfigurasi Load Balancer dengan Nginx untuk Arjuna
echo 'upstream backend_servers {
    server 192.168.1.5:8001; # Prabakusuma
    server 192.168.1.4:8002; # Abimanyu
    server 192.168.1.6:8003; # Wisanggeni
}

server {
    listen 80;
    listen [::]:80;

    server_name arjuna.it25.com www.arjuna.it25.com;

    location / {
        proxy_pass http://backend_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    location ~ /\.ht {
        deny all;
    }
}
' > /etc/nginx/sites-available/default

# Restart Nginx
service nginx restart

# Mengecek konfigurasi Nginx
nginx -t


```

Konfigurasi Nginx untuk membuat load balancer yang akan mengarahkan permintaan ke tiga server backend dengan alamat IP yang berbeda pada port masing-masing (8001, 8002, dan 8003). Load balancer akan mendengarkan permintaan di port 80 lalu akan menangani permintaan untuk nama domain "arjuna.it25.com" dan "www.arjuna.it25.com". Setiap permintaan akan diarahkan ke salah satu server backend dalam grup "backend_servers" menggunakan metode load balancing round-robin.

Setelah itu lakukan test dengan me `curl arjuna.it25.com` maka respon yang didapatkan akan berbeda beda dari 3 workernya

## no 11-17

```
#!/bin/bash

# Update repositori
apt-get update

# Instalasi Apache
apt-get install -y apache2

# Instalasi modul PHP untuk Apache
apt-get install -y libapache2-mod-php7.0 unzip curl

# Buat direktori untuk situs web Abimanyu
mkdir -p /var/www/abimanyu.it25

google_drive_link="https://drive.usercontent.google.com/download?id=1a4V23hwK9S7hQEDEcv9FL14UkkrHc-Zc&export=download&authuser=0&confirm=t&uuid=a0a1d5d3-1b88-41f3-a805-57dc7883a1f0&at=APZUnTVJ1TucA9LeM7rwt43EgR2J:1697073963527"

# Nama file yang akan disimpan
output_file="abimanyu.zip"

# Unduh file dari Google Drive
curl -L -o "$output_file" "$google_drive_link"

# Mengekstrak file (jika perlu)
unzip "$output_file"
mv abimanyu.yyy.com/* /var/www/abimanyu.it25

# Buat file konfigurasi virtual host untuk Abimanyu
echo "
<VirtualHost *:80>
    ServerAdmin webmaster@abimanyu.it25.com
    ServerName abimanyu.it25.com
    ServerAlias www.abimanyu.it25.com
    DocumentRoot /var/www/abimanyu.it25
    ErrorLog \${APACHE_LOG_DIR}/error.log
    CustomLog \${APACHE_LOG_DIR}/access.log combined

    <Directory /var/www/abimanyu.it25>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
" | tee /etc/apache2/sites-available/abimanyu.it25.conf

# Aktifkan situs dan modul yang diperlukan
a2ensite abimanyu.it25.conf
a2enmod rewrite

# Restart Apache
service apache2 restart

# Buat file .htaccess dengan aturan rewrite
echo "
RewriteEngine On
RewriteRule ^index.php/home$ /home [R=301,L]
" > /var/www/abimanyu.it25/.htaccess

# Tambahkan entri di file hosts (jika Anda melakukan ini di mesin lokal atau jika tidak memiliki DNS yang terkonfigurasi)
# echo "127.0.0.1 abimanyu.it25.com" | tee -a /etc/hosts

echo "Setup selesai!"

```

Kode bash ini adalah script konfigurasi yang bertujuan untuk menginstal dan mengonfigurasi sebuah server web Apache dengan modul PHP dan situs web "abimanyu.it25.com". Berikut adalah penjelassannya :

1. Mengupdate repositori dengan perintah `apt-get update`.

2. Menginstal server web Apache dengan perintah `apt-get install -y apache2`.

3. Menginstal modul PHP yang diperlukan untuk Apache dengan perintah `apt-get install -y libapache2-mod-php7.0 unzip curl`.

4. Membuat direktori untuk situs web "abimanyu.it25" di `/var/www/abimanyu.it25`.

5. Mengunduh file dari Google Drive menggunakan `curl`, mengekstraknya (jika perlu), dan memindahkan kontennya ke direktori situs web yang baru dibuat.

6. Membuat file konfigurasi virtual host Apache untuk situs "abimanyu.it25.com" yang mencakup pengaturan server, alamat situs, direktori dokumen, dan catatan error.

7. Mengaktifkan konfigurasi situs dengan perintah `a2ensite` dan modul rewrite dengan `a2enmod rewrite`.

8. Merestart Apache untuk menerapkan perubahan dengan `service apache2 restart`.

9. Membuat file .htaccess di direktori situs web dengan aturan rewrite khusus.

10. Opsional: Menambahkan entri di file hosts jika Anda menjalankan ini di mesin lokal atau jika tidak memiliki DNS yang terkonfigurasi.
