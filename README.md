# ihatemoney
<h1 align="left"><img src="https://user-images.githubusercontent.com/70634108/196363916-c18e9297-7c54-4642-b8cc-647201a7dcda.png"></h1>

# Sekilas Tentang
[`^ kembali ke atas ^`](#)

**ihatemoney** adalah aplikasi web untuk memudahkan pengaturan anggaran bersama. Aplikasi ini akan mencatat siapa yang membeli apa, kapan, di mana, dan untuk siapa, serta tentunya juga dapat merapikan tagihan. **ihatemoney** dibangun agar mudah digunakan serta dibuat sesederhana mungkin.


# Instalasi
[`^ kembali ke atas ^`](#)

#### Kebutuhan Sistem :
- Python : versi 3.6 - 3.10
- DBMS : PostgreSQL
- Virtual Environment : Python3 venv
- WSGI : Gunicorn
- Web Server : Nginx

#### Instalasi :
1. Membuat *python virtual environment*

    ```
    $ python3 -m venv ~/ihatemoney
    $ cd ihatemoney
    ```

2. Aktivasi *virtual environment*

    ```
    $ source bin/activate
    ```

3. Install *package* ihatemoney

    ```
    $ pip install ihatemoney
    ```

4. Coba jalankan

    ```
    $ ihatemoney runserver
    ```

5. *Generate file* konfigurasi

    ```
    $ mkdir /etc/ihatemoney /var/lib/ihatemoney
    $ ihatemoney generate-config ihatemoney.cfg > /etc/ihatemoney/ihatemoney.cfg
    $ chmod 740 /etc/ihatemoney/ihatemoney.cfg
    ```

6. Konfigurasi PostgreSQL

    - Install package python untuk PostgreSQL
    
    ```
    $ pip install psycopg2-binary==2.8.6
    ```
    - Masuk ke postgreSQL inactive
    
    ```
    $ sudo -u postgres psql
    ```
    - Buat user dan database
    
    ```
    $ create database mydb;
    $ create user myuser with encrypted password 'mypass';
    $ grant all privileges on database mydb to myuser;
    $ \q
    ```
    - Edit konfigurasi database ihatemoney
    Edit file /etc/ihatemoney/ihatemoney.cfg dengan mengganti value dari variabel 
    SQLALCHEMY_DATABASE_URI menjadi:
    
    ```
    SQLALCHEMY_DATABASE_URI = 'postgresql://myuser:mypass@localhost/mydb?client_encoding=utf8'
    ```

7. Konfigurasi reverse proxy
    
    - Buat user khusus, direktori, dan atur permissions
    
    ```
    $ useradd ihatemoney
    $ chown ihatemoney /var/lib/ihatemoney/
    $ chgrp ihatemoney /etc/ihatemoney/ihatemoney.cfg
    ```
    - Buat file konfigurasi gunicorn
    
    ```
    $ ihatemoney generate-config gunicorn.conf.py > /etc/ihatemoney/gunicorn.conf.py
    ```
    - Buat file
    /etc/systemd/system/ihatemoney.service dengan isi:
    
    ```
    [Unit]
    Description=I hate money
    Requires=network.target postgresql.service
    After=network.target postgresql.service

    [Service]
    Type=simple
    User=ihatemoney
    ExecStart=%h/ihatemoney/bin/gunicorn -c /etc/ihatemoney/gunicorn.conf.py     
    ihatemoney.wsgi:application
    SyslogIdentifier=ihatemoney
    [Install]
    WantedBy=multi-user.target
    ```
    %h pada ExecStart diganti dengan path direktori folder ihatemoney
    
    - Reload systemd dan aktifkan ihatemoney
    
    ```
    $ systemctl daemon-reload
    $ systemctl enable ihatemoney.service
    $ systemctl start ihatemoney.service
    ```
    - Generate konfigurasi nginx
    
    ```
    $ ihatemoney generate-config nginx.conf > /etc/nginx/sites-available/ihatemoney
    $ rm /etc/nginx/sites-enabled/default
    $ ln -s /etc/nginx/sites-available/ihatemoney /etc/nginx/sites-enabled/ihatemoney
    ```
    - Edit konfigurasi nginx sesuai kebutuhan 
    Menggunakan nano
    
    ```
    $ nano /etc/nginx/sites-available/ihatemoney
    ```
    Menggunakan vim
    
    ```
    $ vim /etc/nginx/sites-available/ihatemoney
    ```
    Lakukan pengecekan sintaks dan reload nginx setiap mengubah konfigurasi
    
    ```
    $ nginx -t
    $ nginx -s reload
    ```
    

# Cara Pemakaian
[`^ kembali ke atas ^`](#)

Saat memasuki web, kita akan disuguhkan oleh tampilan yang sederhana seperti gambar di bawah ini:
<img src="https://user-images.githubusercontent.com/70634108/196365124-f11b9d5e-23dc-420b-b9b5-174743d79876.png" width="600px" height="auto"></br>
Terdapat dua pilihan yaitu masuk ke project yang sudah ada, atau membuat project baru, kita asumsikan kita akan membuat project baru di sini
1. Membuat project baru.

    <img src="https://user-images.githubusercontent.com/70634108/196367993-fbc61835-e5b1-407f-a43e-d96bb84aad0f.png" width="300" height="auto">



2. Setelah membuat project baru, kita akan masuk ke halaman utama untuk menambah tagihan. Pada bagian atas terdapat berbagai menu yang dapat kita gunakan.

    <img src="https://user-images.githubusercontent.com/70634108/196369320-4f96bcfb-ea1b-4bb3-b87f-52460c291f9a.png" width="600px" height="auto">


3. Pada bagian samping kiri, terdapat menu untuk menambah pertisipant. Lalu pada menu tagihan kita mencoba menambahkan nama agar sistem keuangannya dapat digunakan dengan baik.  

    <img src="https://user-images.githubusercontent.com/78604686/196496445-20a12c8e-2deb-4b85-8290-9884d9f1ef72.png" width="600px" height="auto">
    
4. Menu **Tagihan** tersebut berguna untuk menambahkan daftar tagihan yang terdiri dari keterangan kapan, apa, siapa yang bayar, berapa banyak, untuk siapa dan dengan opsi tambahan lainnya.

    <img src="https://user-images.githubusercontent.com/78604686/196498933-79f1dbc7-7a4a-4e0a-adae-3cf47d07a8e5.png" width="600px" height="auto">

5. Menu **Atur** berguna untuk melihat informasi lebih detail tentang siapa yang membayar, kepada siapa, dan berapa banyak uang yang harus dibayarkan.

    <img src="https://user-images.githubusercontent.com/78604686/196500150-7c29c7ae-2362-4c27-99aa-7cfa4ebedcd3.png" width="600px" height="auto">

6. Menu **Statistik** berguna untuk meihat informasi lebih detail tentang berapa uang yang dibayarkan, uang yang dihabiskan dan juga pengeluaran menurut bulan.

    <img src="https://user-images.githubusercontent.com/78604686/196500719-4b539a27-e7fe-4ae8-805b-4eb435755d75.png" width="600px" height="auto">

7. Selain menu-menu yang berhubungan dengan pencatatan keuangan, **ihatemoney** juga menyediakan menu untuk memilih bahasa yang akan digunakan di web ini sesuai dengan kemampuan berbahasa dari pengguna.

    <img src="https://user-images.githubusercontent.com/78604686/196501951-711c9871-6a02-4c60-9896-9fd6f6a0814a.png" width="600px" height="auto">

8. Selain itu, terdapat juga menu untuk menambah projek baru, melihat riwayat penggunaan web oleh user, dan juga menu pengaturan.

    <img src="https://user-images.githubusercontent.com/78604686/196502878-1a5ce750-3450-44c7-be65-243a51f8c81d.png" width="600px" height="auto">


# Pembahasan
[`^ kembali ke atas ^`](#)

**ihatemoney** ditulis dalam bahasa pemrograman `Python`, menggunakan `flash` framework. Sebagai salah satu aplikasi yang digunakan dalam pengelolaan keuangan bersama, **ihatemoney** memiliki kelebihan antara lain :
- Tampilan interface yang sederhana dan mudah digunakan oleh orang awam.
- Pengguna dapat mengundang orang lain ke proyek yang dibuat dengan cara membagikan ID dan kode, tautan, ataupun mengirim undangan melalui surel sehingga pengelolaan keuangan bersama lebih transparan.
- Item tagihan atau daftar transaksi yang termasuk ke dalam data proyek dapat diunduh oleh pengguna. 
- Aplikasi tersedia dalam banyak pilihan bahasa dan juga pilihan mata uang sesuai dengan kebutuhan pengguna.
- Pengguna dapat melihat semua riwayat aktivitas yang sudah dilakukan di dalam aplikasi.

Tentu saja, sebuah aplikasi pasti memiliki kekurangan. Kekurangan yang dimiliki **ihatemoney** antara lain :
- Pengguna harus menginput secara manual data-data yang akan digunakan sehingga jika ada kesalahan input akan menimbulkan kesalahan dalam perhitungan juga.
- Belum banyak orang yang mengetahui tentang aplikasi ini.

Ihatemoney tentunya bukan satu-satunya aplikasi pencatatan keuangan/tagihan, salah satu contoh aplikasi lain adalah **HomeBank**. Jika dibandingkan, ihatemoney memiliki beberapa kelebihan dan kekurangan, di antaranya:
- **Ihatemoney** dibuat dengan sangat sederhana yang memungkinkan penggunanya cepat mengerti untuk pemakaiannya. Sedangkan **HomeBank** lebih rumit yang menyebabkan pengguna perlu mempelajari lebih lama.
- Fitur manajemen uang pada **HomeBank** jauh lebih banyak dibandingkan pada **ihatemoney**. Namun, jika berfokus pada *shared budget management* maka **ihatemoney** tetap lebih baik karena lebih mendetail untuk pembayaran tagihan
- **ihatemoney** dapat digunakan langsung pada website sedangkan **HomeBank** harus diunduh terlebih dahulu.




# Referensi
[`^ kembali ke atas ^`](#)

1. [About #ihatemoney](https://ihatemoney.org/) - ihatemoney
2. [ihatemoney documentation](https://ihatemoney.readthedocs.io/en/latest/) - ihatemoney
2. [ihatemoney alternatives](https://alternativeto.net/software/ihatemoney/) - alternativeto

