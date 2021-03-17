<h1 align="center"><img src="https://raw.githubusercontent.com/dreamfactorysoftware/dreamfactory/master/readme/vertical-logo-fullcolor.png"></h1>

[Sekilas Tentang](#sekilas-tentang) | [Instalasi](#instalasi) | [Konfigurasi](#konfigurasi) | [Pemakaian](#Pemakaian) | [Pembahasan](#pembahasan) | [Referensi](#referensi)

# Sekilas Tentang
[`^ kembali ke atas ^`](#)

DreamFactory merupakan website apps untuk management API yang tersedia secara *Open Source*.  DreamFactory menyediakan akses ke set resource back-end yang kaya melalui REST interface yang mendukung dokumen JSON dan XML.

# Instalasi
[`^ kembali ke atas ^`](#)

### Kebutuhan Sistem :
- Unix, Linux atau Windows.
- PhpMyAdmin
- MySQL 5.0+.
- Apache Web server 1.3+.
- PHP 7.2+
- Git
- Composer



### Proses Instalasi :
1. Login kedalam server menggunakan SSH. disini saya menggunakan `node.js`, setelah itu install semua kebutuhan.
```bash

#LogIn
ssh student@localhost -p 2200

#addition
sudo apt update
sudo apt install composer
sudo apt install git
```


2. Install kebutuhan Web Server (`MySql`,`apache`,dll)
```bash
sudo apt install apache2
sudo apt install mysql-server
sudo mysql_secure_installation
sudo apt install php libapache2-mod-php php-mysql
sudo apt-get install php php-common php-xml php-cli php-curl php-json php-mysqlnd php7.4-sqlite php-soap php-mbstring php-zip php-bcmath
sudo apt install phpmyadmin
```

3. Setup database `MySql`.
    ```bash

    #Masuk menggunakan root
    sudo mysql -p -u root
    
    #create database
    CREATE DATABASE dreamfactory;
    CREATE USER 'dreamfactoryuser'@'localhost' IDENTIFIED BY 'dreamfactorypass';
    GRANT ALL PRIVILEGES ON `dreamfactory`.* TO 'dreamfactoryuser'@'localhost';
    FLUSH PRIVILEGES;
    exit;
    
    #Restart
    sudo service apache2 reload
    ```


4. Konfigurasi file `php`
    - Buka php.ini dengan text editor, lalu ganti `;cgi.fix_pathinfo=1` menjadi `;cgi.fix_pathinfo=0`, setelah itu save dan exit.
    ```bash

    #Buka php.ini 
    sudo nano ~/etc/php/7.4/apache2/php.ini
    
    ```

5. Install MongoDB.
    ```bash

    sudo apt-get install php-dev php-pear build-essential libssl-dev libssl-dev libcurl4-openssl-dev pkg-config
    
    #PECL
    sudo pecl install mongodb
    
    #Membuat file .ini lalu nyalakan MongoDB
    sudo sh -c 'echo "extension=mongodb.so" > /etc/php/7.4/mods-available/mongodb.ini'
    sudo phpenmod mongod
    ```

6. Install DreamFactory
    ```bash

    #Membuat directory project dan atur kepemilikan folder tersebut lalu masuk kedalam direktori tersebut.
    sudo mkdir /opt/dreamfactory
    sudo chown -R $USER /opt/dreamfactory  
    cd /opt/dreamfactory
    
    #Git Clone repository DreamFactory dan install composer
    git clone https://github.com/dreamfactorysoftware/dreamfactory.git ./
    composer install --no-dev --ignore-platform-reqs
    
    #Database, atur file .env
    php artisan df:env
    nano .env
   
    #Cari teks `#DB_CHARSET=` dan `#DB_COLLATION=` ubah menjadi `DB_CHARSET=utf8` dan `DB_COLLATION=utf8_unicode_ci`, lalu save dan exit.
    
    #Jalankan syntax berikut untuk setup dan hapus cache.
    php artisan df:setup
    sudo chown -R www-data:$USER storage/ bootstrap/cache/
    sudo chmod -R 2775 storage/ bootstrap/cache/
    php artisan cache:clear 
    ```

7. Setup server
    ```bash

    sudo a2enmod rewrite
    
    #pindah direktori dan setup default backup lalu edit file default config
    cd /etc/apache2/sites-available
    sudo cp 000-default.conf 000-default.conf.bak
    sudo nano 000-default.conf
    
    #Ubah default config dengan mengubah isinya menjadi
    <VirtualHost *:80>
        DocumentRoot /opt/dreamfactory/public

        <Directory /opt/dreamfactory/public>
            AddOutputFilterByType DEFLATE text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript
            Options -Indexes +FollowSymLinks -MultiViews
            AllowOverride All
            AllowOverride None
            Require all granted
            RewriteEngine on
            RewriteBase /
            RewriteCond %{REQUEST_FILENAME} !-f
            RewriteCond %{REQUEST_FILENAME} !-d
            RewriteRule ^.*$ /index.php [L]

            <LimitExcept GET HEAD PUT DELETE PATCH POST>
                Allow from all
            </LimitExcept>
        </Directory>
    </VirtualHost>
    
    #Setelah itu restart
    sudo service apache2 restart
    ```
8. Kunjungi `localhost:8000` dan login masuk dreamfactory

    - Login dengan akun yang sudah dibuat
    ![1](https://github.com/aslam004/Komdat-P2-6/blob/main/Screenshot/login.png)

# Konfigurasi
[`^ kembali ke atas ^`](#)
- Kita dapat melihat informasi sistem melalui tab config
    ![1](https://github.com/aslam004/Komdat-P2-6/blob/main/Screenshot/sysinfo.png)
- Pengaturan untuk cache 
    ![2](https://github.com/aslam004/Komdat-P2-6/blob/2a0bbf3d21e9430a0446640f5c0d82114ae93d75/Screenshot/cache.png)
- kita juga dapat mengatur *CORS* dalam config
    ![3](https://github.com/aslam004/Komdat-P2-6/blob/main/Screenshot/cors.png)
- Untuk mengatur template email, dapat melalui `email templates`.
    ![4](https://github.com/aslam004/Komdat-P2-6/blob/main/Screenshot/emailtemplate.png)
- Pengaturan untuk *environment* yang digunakan dapat di atur dalam *Global Lookup Key*
    ![5](https://github.com/aslam004/Komdat-P2-6/blob/main/Screenshot/glk.png)

# Pemakaian
[`^ kembali ke atas ^`](#)
Untuk pemakaian, berikut beberapa contoh pemakaian didalam DreamFactory:
    - Melihat API Docs yang ada
        ![1](https://github.com/aslam004/Komdat-P2-6/blob/main/Screenshot/APIDocs.png)
    - Membuat admin baru
        ![2](https://github.com/aslam004/Komdat-P2-6/blob/main/Screenshot/create%20admin.png)
    - Manage apps
        ![3](https://github.com/aslam004/Komdat-P2-6/blob/main/Screenshot/manage%20apps.png)

# Pembahasan
[`^ kembali ke atas ^`](#)
**Dreamfactory** merupakan API manajemen yang berbasis PHP. Berikut beberapa kelebihan dan kekurangan **Dreamfactory**
**Kelebihan**
- Memudahkan dalam manajemen servis aplikasi.
- Adanya dokumentasi *API*.
- Tiap User memiliki role sehingga dapat diatur hak aksesnya.
- Adanya file manajer membuat lebih mudah.

**Kekurangan**
- Banyak fitur yang hanya dapat diakses ketika berlangganan.
- Sulit digunakan.

Aplikasi ini memiliki kelebihan dan kekurangan jika dibandingkan dengan aplikasi *API Management* lainnya seperti **KONG**:
- **Dreamfactory** ada free versionnya , tidak seperti **KONG** yang tidak menyediakan free version.
- **KONG** secara fitur lebih solid dibanging dengan **Dreamfactory**.
- Namun **KONG** jarang adanya update jika dibandingkan dengan harganya.



# Referensi
[`^ kembali ke atas ^`](#)

    - [konghq](https://konghq.com/)
    - [Dreamfactory](https://www.dreamfactory.com/)
    - [capterra](https://www.capterra.com/api-management-software/compare/79098-156756-171497/DreamTeam-vs-Funnel-vs-Kong-Enterprise)
    - [LAMP Installation](https://www.linuxid.net/24341/cara-install-lamp-linux-apache-mysql-php-stack-di-ubuntu/)
    - [DockerCompose](https://www.google.com/search?q=cara+install+docker+compose&rlz=1C1CHBD_enID921ID921&oq=cara+&aqs=chrome.2.69i59l3j69i57j69i59j69i60l2j69i61.2692j0j4&sourceid=chrome&ie=UTF-8)
