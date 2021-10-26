# Snipe-IT
## Mục lục
- [Snipe-IT](#snipeit)
  - [Tổng quan.](#tongquan)
  - [Chức năng.](#chucnang)
- [Hướng dẫn cài đặt.](#caidat)


## Snipe-IT<a name="snipeit"></a>
### Tổng quan.<a name="tongquan"></a>

## Hướng dẫn cài đặt.<a name="caidat"></a>
Chuẩn bị :
- Ubuntu live 20.04.

Sau khi cài đặt máy Ubuntu, ta tiến hành cài đặt Snipe-IT theo các bước sau:

Bước 1: Update server và cài đặt dịch vụ đi kèm.
```
apt update -y
apt upgrade -y
apt-get install unzip -y
```
Bước 2: Cài đặt Apache.
```
apt install apache2 -y
systemctl start apache2 && systemctl enable apache2
```
Bước 3: Bật tính năng `Apache’s mod_rewrite module`.
```
sudo a2enmod rewrite
systemctl restart apache2
```
Bước 4: Cài đặt MySQL Database.
- Cài `MySQL`
```
sudo apt install mysql-server
```
- Chạy lệnh.
```
sudo mysql_secure_installation
```
Hệ thống sẽ có những tùy chọn cho `server`, tùy chọn theo nhu cầu cá nhân.
Trong quá trình tùy chọn, MySQL sẽ cho phép thay đổi `password` của `user` root MySQL.

- Đăng nhập vào MySQL bằng tài khoản `root`.
```
sudo mysql
```
- Tạo `user` mới dùng để đăng nhập `database` của Mattermost sau này.

*Lưu ý: Phải tạo `user` sử dụng `native password`. Nếu `native password` không được tạo cho `user` ở phiên bản MySQL8.0.11 trở về sau, nó sẽ thông báo chứng thực thất bại. Để khắc phục hãy sử dụng `native-password`.

```
CREATE USER '<tên user>'@'localhost' IDENTIFIED WITH mysql_native_password BY '<password>';
```
- Tạo một `database` đặt tên là `snipe_it`.
```
create database snipe_it;
```
- Cấp quyền truy cập cho `user` vừa được tạo ở các bước trên và thoát ra.
```
grant all privileges on snipe_it.* to '<tên user>'@'localhost';
exit
```
Bước 5: Cài PHP and PHP Composer.
- Cài PHP:
Ở đây ta sử dụng php mặc định bản 7.4 và `modules`.
```
apt install php php-bcmath php-bz2 php-intl php-gd php-mbstring php-mysql php-zip php-opcache php-pdo php-calendar php-ctype php-exif php-ffi php-fileinfo php-ftp php-iconv php-intl php-json php-mysqli php-phar php-posix php-readline php-shmop php-sockets php-sysvmsg php-sysvsem php-sysvshm php-tokenizer php-curl php-ldap -y
```
- Cài PHP Composer:
Tải PHP Composer.
```
curl -sS https://getcomposer.org/installer | php
```
Di chuyển `composer.phar` sang `/usr/local/bin/`.
```
mv composer.phar /usr/local/bin/composer
```
Bước 6: Cài đặt `Snipe-IT`.
Di chuyển đến thư mục chứa web server:
```
cd /var/www/
```
Tải snipeit biên bản mới nhất.
```
git clone https://github.com/snipe/snipe-it snipe-it
```
Di chuyển đến thư mục snipe-it.
```
cd /var/www/snipe-it
```
Sao chép file `.env.example`sang `/var/www/snipe-it/.env`.
```
cp /var/www/snipe-it/.env.example /var/www/snipe-it/.env
```
Chỉnh sửa file `.env`.
```
sudo nano /var/www/snipe-it/.env
```
Trong file setting, tại vị trí :
```
APP_URL=null
APP_TIMEZONE=’UTC'
```
Thay `null` của APP_URL bằng domain của bạn hoặc ip public. Nếu làm trên máy local có thể bỏ qua bước này.
Tại vị trí :
```
DB_DATABASE=null
DB_USERNAME=null
DB_PASSWORD=null
```
Thay vào vị trí `null` bằng những giá trị tương ứng khi tạo Databe tại MySQL ở các bước trên.

Lưu và thoát.

Bước 7: Thay đổi chủ sở hữu và cấp quyền cho thư mực `/var/www/snipe-it`.
```
chown -R www-data:www-data /var/www/snipe-it
chmod -R 755 /var/www/snipe-it
```
Bước 8: Cài đặt một số dịch vụ phụ thuộc giữa `Snipe-IT` với `Composer`. Gõ `yes` và ENTER.
```
composer update --no-plugins --no-scripts
composer install --no-dev --prefer-source --no-plugins --no-scripts
```
Sau khi cài đặt, phát sinh một `Laravel APP_Key` theo đường dẫn `/var/www/snipe-it/.env`. Key sẽ được lưu tại file này.
```
php artisan key:generate
```
Bước 9: Tạo một file Host ảo.
- Vô hiệu hóa file cấu hình mặc định của Apache.
```
a2dissite 000-default.conf
```
- Tạo một file cấu hình mới.
```
nano /etc/apache2/sites-available/snipe-it.conf
```
- Thêm vào file cấu hình những dòng dưới đây.
```
<VirtualHost *:80>
ServerName example.com
DocumentRoot /var/www/snipe-it/public
<Directory /var/www/snipe-it/public>
Options Indexes FollowSymLinks MultiViews
AllowOverride All
Order allow,deny
allow from all
</Directory>
</VirtualHost>
```
Lưu và thoát.

Bước 10: Khởi động file cấu hình mới.
```
a2ensite snipe-it.conf
```
Bước 11: Khởi chạy lại dịch vụ của Apache để áp dụng cấu hình.
```
systemctl restart apache2
```
Bước 12: Setup Web `Snipe-IT`.
Sau khi hoàn thành các bước cài đặt trên, đăng nhập vào web của server thông qua IP hoặc domain.

![install-snipe-it-768x655](https://user-images.githubusercontent.com/80932769/138681515-7e7669a5-dea9-4ab1-bbcd-934447a38597.png)

Hệ thống sẽ kiểm tra qua hết cấu hình, nếu đặt điều kiện thì sẽ xanh hết như trong hình.
Tiến hành cài đặt server theo nhu câu cá nhân.

Sau khi cài đặt xong thì đây là kết quả.

![image](https://user-images.githubusercontent.com/80932769/138681644-68a43887-7eb5-4b80-b4d1-cc92f63be16a.png)



![image](https://user-images.githubusercontent.com/80932769/138678075-ec1f5ab0-d623-41fe-a889-c053ea99fb65.png)

