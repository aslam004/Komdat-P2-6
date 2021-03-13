<h1 align="center"><img src="https://raw.githubusercontent.com/dreamfactorysoftware/dreamfactory/master/readme/vertical-logo-fullcolor.png"></h1>

[Sekilas Tentang](#sekilas-tentang) | [Instalasi](#instalasi) | [Konfigurasi](#konfigurasi) | [Cara Pemakaian](#cara-pemakaian) | [Pembahasan](#pembahasan) | [Referensi](#referensi)

# Sekilas Tentang
[`^ kembali ke atas ^`](#)

**Dreamfactory** adalah sebuah aplikasi berbasis website yang menyediakan layanan API Management secara gratis dan *Open Source*. **Dreamfactory** DreamFactory Services Platform menyediakan akses ke set resource back-end yang kaya melalui REST interface yang mendukung dokumen JSON dan XML.

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
1. Login kedalam server menggunakan SSH. disini saya menggunakan `node.j`, setelah itu install semua kebutuhan.
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
sudo apt-get install php php-common php-xml php-cli php-curl php-json php-mysqlnd php7.2-sqlite php-soap php-mbstring php-zip php-bcmath
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
    sudo nano ~/etc/php/7.2/apache2/php.ini
    
    ```

5. Install MongoDB.
    ```bash

    sudo apt-get install php-dev php-pear build-essential libssl-dev libssl-dev libcurl4-openssl-dev pkg-config
    
    #PECL
    sudo pecl install mongodb
    
    #Membuat file .ini lalu nyalakan MongoDB
    sudo sh -c 'echo "extension=mongodb.so" > /etc/php/7.2/mods-available/mongodb.ini'
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
8. Kunjungi alamat IP Web Server kita untuk melakukan login menggunakan akun saat setup

    - Login dengan akun yang sudah dibuat
    ![1](https://raw.githubusercontent.com/ditdittdittt/dreamfactory/master/Screenshot/Login%20Page.png)
    - Siap digunakan
    ![2](https://raw.githubusercontent.com/ditdittdittt/dreamfactory/master/Screenshot/Home%20Page.png)

# Konfigurasi
[`^ kembali ke atas ^`](#)
- Untuk melihat info dari sistem yang kita gunakan kita bisa membuka halaman *System Info* yang ada di tab *Config*
    ![1](https://raw.githubusercontent.com/ditdittdittt/dreamfactory/master/Screenshot/Config%20System%20Info.png)
- Untuk melakukan pengaturan pada *cache* guna meningkatkan performa, kita bisa membuka halaman *cache* yang ada di tab *Config*
    ![2](https://raw.githubusercontent.com/ditdittdittt/dreamfactory/master/Screenshot/Config%20Cache.png)
- **Dreamfactory** juga sudah menyediakan kemudahan untuk melakukan pengaturan **CORS** di halaman *CORS* yang ada di tab *Config*
    ![3](https://raw.githubusercontent.com/ditdittdittt/dreamfactory/master/Screenshot/Config%20CORS.png)
- Sedangkan untuk melakukan konfigurasi template dari email yang akan dikirim, kita bisa melakukannya di halaman *Email Templates* yang ada di tab *Config*
    ![4](https://raw.githubusercontent.com/ditdittdittt/dreamfactory/master/Screenshot/Config%20Email%20Templates.png)
- Sedangkan untuk membuat konfigurasi *environment* yang digunakan, kita bisa mengaksesnya di halaman *Global Lookup Keys*
    ![5](https://raw.githubusercontent.com/ditdittdittt/dreamfactory/master/Screenshot/Config%20Global%20Keys.png)

# Cara Pemakaian
[`^ kembali ke atas ^`](#)
- Untuk melakukan management menggunakan **Dreamfactory** cukup mudah, berikuat merupakan beberapa contoh:
    - Membuat dokumentasi untuk aplikasi yang kita miliki
        ![1](https://raw.githubusercontent.com/ditdittdittt/dreamfactory/master/Screenshot/Create%20App.png)
    - Membuat admin baru untuk melakukan manajemen
        ![2](https://raw.githubusercontent.com/ditdittdittt/dreamfactory/master/Screenshot/Create%20Admin.png)
    - Membuat service baru yang ada di dalam aplikasi
        ![3](https://raw.githubusercontent.com/ditdittdittt/dreamfactory/master/Screenshot/Create%20Service.png)

# Pembahasan
[`^ kembali ke atas ^`](#)
**Dreamfactory** adalah sebuah aplikasi berbasis website yang menyediakan layanan API Management yang ditulis dalam bahasa pemrograman PHP. Beberapa hal yang bisa dilakukan untuk melakukan manajemen dengan **Dreamfactory** diantaranya:
- Mempermudah apps management apabila kita memiliki sebuah aplikasi yang banyak
- Mempermudah service management disaat sebuah aplikasi menggunakan berbagai macam layanan service
- Membuat role management kepada setiap user, sehingga setiap user memiliki hak akses yang sesuai dengan role-nya
- Membuat dokumentasi *Api* dengan sangat mudah, bahkan kita bisa membuat *schema* dari database
- Memiliki file manager tersendiri

Kekurangan dari aplikasi ini antara lain:
- Ketika memiliki resource yang banyak, aplikasi akan berjalan dengan lambat
- Sebagian *powerful feature* yang disediakan harus diakses dengan berbayar

Namun, jika dibandingkan dengan aplikasi *API Management* lainnya seperti **Endpoint** atau **Fusio**, aplikasi ini memiliki beberapa kelebihan dan kekurangan seperti:
- **Dreamfactory** lebih *less-coding* dibanding **Endpoint** maupun **Fusio**
- Aplikasi **Dreamfactory** memiliki fitur yang jauh lebih lengkap
- Aplikasi **Endpoint** dan **Fusio** lebih ringan dibanding **Dreamfactory** karena memiliki modul yang lebih sedikit



# Referensi
[`^ kembali ke atas ^`](#)
