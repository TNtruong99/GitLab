# Snipe-IT
## Mục lục
- [Snipe-IT](#snipeit)
  - [Tổng quan.](#tongquan)
- [Hướng dẫn cài đặt.](#caidat)
- [Chức năng.](#chucnang)
  - [Quản lý tài sản.](#qltaisan)
  - [Backup.](#backup)
  - [Restore.](#restore)


## Snipe-IT<a name="snipeit"></a>
### Tổng quan.<a name="tongquan"></a>
`Snipe-IT` là phần mềm mã nguồn mở được dùng để quản lý tài sản công nghệ thông tin. Với phần mềm này sẽ cho phép người quản trị quản lý được laptop được bàn giao cho ai, bao nhiêu phần mềm bản quyền được kích hoạt và còn bao lâu hết hạn,… Ví dụ như phần mềm mà các máy tính nhân viên, máy chủ đang sử dụng (license window, phần mềm bản quyền…) và phần cứng dùng để phục vụ công việc, hệ thống (laptop, màn hình, bàn phím, máy in, thiết bị …) trong môi trường công nghệ thông tin của doanh nghiệp.

`Snipe–IT` được xây dựng trên mã nguồn PHP Framework Laravel, theo sát cấu trúc lập trình MVC. Lợi ích mà Snipe–IT mang lại:
– Giao diện trực quan, dễ quản lý.
– Thể hiện tính chuyên nghiệp của một công ty công nghệ.
– Đưa ra thống kê, kiểm kê tài sản, báo cáo nhanh và chính xác.


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
Tại `TIMEZONE` ta có thể thay bằng múi giờ của Việt Nam để thời gian được chính xác.
```
APP_TIMEZONE ='Asia/Ho_Chi_Minh'
```
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

Ngoài ra, Snipe-IT còn hỗ trợ tiếng Việt, hoặc những ngôn ngữ khác và chúng ta hoàn toàn có thể kiểm soát.
Admin của server có thể chọn ngôn ngữ cho từng tài khoản riêng biệt tùy theo nhu cầu sử dụng.
Khi tạo `user` mới hoặc tùy chỉnh `user` ta có thể chọn ngôn ngữ cho `user` đó tại mục `Language`. 

![image](https://user-images.githubusercontent.com/80932769/139023874-0be2f0c6-5364-4e24-8b05-df7ef8873c5c.png)

## Chức năng. <a name="chucnang"></a>
### Quản lý tài sản. <a name="qltaisan"></a>
Để quản lý tài sản của một công ty, ta cần có những thông tin cơ bản như :
- Công ty.
- Danh mục tài sản.
- Nhà sản xuất.
- Nhà cung cấp.
- Địa điểm.
- Thành viên.
- Tài sản.
### Công ty.

Có thể tạo nhiều công ty dựa theo quy mô và cách tổ chức của công ty cần sử dụng:
`Setting -> Companies`

Giao diện quản lý công ty.

![image](https://user-images.githubusercontent.com/80932769/138832485-48efbc95-6b78-49cb-b7aa-38e5e7c797b4.png)

Thêm công ty.

![image](https://user-images.githubusercontent.com/80932769/138832336-c53d5dd4-ccfc-445a-9c3c-f2db70f17b36.png)

### Danh mục tài sản.
Chúng ta xác định tài sản này thuộc về dòng tài sản nào – tùy vào tài sản mà bạn cần quản lý thuộc danh mục nào mà tạo (vd nội ngoại thất, dụng cụ văn phòng, vật liệu xây dựng…), nên sẽ tạo danh mục như sau:
`Setting -> Categories`

Giao diện quản lý danh mục tài sản.

![image](https://user-images.githubusercontent.com/80932769/138834494-2d340bb3-5468-4c64-9bfb-05d6072a9e55.png)

Giao diện thêm danh mục tài sản.

![image](https://user-images.githubusercontent.com/80932769/138834644-ad6eeacf-f47d-4060-8f90-b3221653f81c.png)

- Category Name : tên danh mục tài sản.
- Type : loại tài sản. ( Chọn loại tài sản phù hợp.)

### Nhà sản xuất.
Xác định tài sản thuộc nhà sản xuất nào, tạo danh mục nhà sản xuất:
`Setting -> Manufacturers`

![image](https://user-images.githubusercontent.com/80932769/138835751-594f3b2a-8a23-486e-8703-8cc0cfd60e36.png)

Giao diện thêm nhà sản xuất.

![image](https://user-images.githubusercontent.com/80932769/138835728-d2a7d1e4-28b6-4bbb-84c8-7c97cfd1c2d6.png)

### Kiểu tài sản.
Xác định tùy theo các loại thiết bị trong công ty bạn:
`Setting -> Asset Models`

![image](https://user-images.githubusercontent.com/80932769/138836766-79626435-af89-418c-8870-7689a0cf224d.png)

Giao diện thêm kiểu tài sản.

![image](https://user-images.githubusercontent.com/80932769/138837085-f23d73d0-fb3a-41ce-af6d-44532221dcab.png)

### Nhà cung cấp.
Xác định tài sản được bên nào cung cấp, tạo danh mục nhà cung cấp:
`Setting -> Suppliers`

![image](https://user-images.githubusercontent.com/80932769/138839986-cce09a64-2353-40cc-be16-8e0909d9a529.png)

Giao diện thêm nhà cung cấp.

![image](https://user-images.githubusercontent.com/80932769/138839785-aa892192-60af-4356-8c37-6a91e874cdc3.png)

### Địa điểm.
Xác định đia chỉ công ty bạn, nếu có chi nhánh khác thì tạo thêm, hoặc dạng tập đoàn đứng tên nhiều công ty thì bạn tạo thêm công ty ở phần 1.
`Setting -> Locations`

![image](https://user-images.githubusercontent.com/80932769/138841155-9b70cf36-0686-45d6-ae10-4abaeee00b01.png)

Giao diện thêm địa điểm của công ty.

![image](https://user-images.githubusercontent.com/80932769/138841127-3c986056-a785-4bea-8aa9-da3b7db86e03.png)

### Thành viên.
Tạo người dùng để gán tài sản.
`People`

![image](https://user-images.githubusercontent.com/80932769/138841648-c49e02ef-3805-4bc3-9dd2-f30d23951efe.png)

Giao diện thêm thành viên.

![image](https://user-images.githubusercontent.com/80932769/138841559-553634e6-d916-47bb-84f9-601f8482be92.png)

### Tài sản.

Bắt đầu tạo tài sản dựa theo thông tin đã tạo.
` Assets -> List All`

![image](https://user-images.githubusercontent.com/80932769/138843541-1dcad130-62ee-43df-8949-ceaf043e3fba.png)

Lưu ý:
- Thẻ tài sản bạn phải thống nhất theo chuẩn bên kế toán đã lưu để sau này kiểm kê trích xuất dữ liệu cho đồng bộ 2 bên dễ dàng kiểm tra.
- Kiểu tài sản bạn sẽ thấy có tùy chọn theo các thông tin đã tạo bên trên với logic: danh mục – hãng / kiểu tài sản (còn ở phần cài đặt – kiểu tài sản thì hiển thị sẽ không giống).
- Tình trạng: thì thường sẽ là ready to deploy do hiện tại chúng ta kiểm kê và nhập liệu cho tài sản đang sử dụng được, khác cái là bạn có check out vào đối tượng nào hay chưa – nếu chưa check out thì xem như đó là hàng tồn kho chờ được cấp, phần này bạn nhập chừng 10 20 thiết bị thì sẽ thấy được. Và bạn có thể tạo thêm nhiều tình trạng tài sản khác tùy theo nhu cầu của bạn tại Cài đặt -> tình trạng nhãn.
- Check out đến: thì thường bạn sẽ tạo danh sách thành viên cho toàn bộ nhân viên trong công ty rồi check out tài sản theo người dùng, hoặc tùy nhu cầu mà check out theo địa phương.
- Tên tài sản: ở đây bạn có thể ghi cho đầy đủ thông tin cơ bản của tài sản để nhìn vào biết cơ bản đó là món gì, thông tin này có thể giống nhau giữa các tài sản.
- Nên có hình ảnh cho từng tài sản để kiểm kê dễ hơn.

![image](https://user-images.githubusercontent.com/80932769/138843484-bf540911-9cd3-4fa3-8e48-34f8ca2c6910.png)

Ngoài tài sản thông thường như bàn, ghế, màn hình, máy in, ... thì Snipe-IT còn hỗ trợ kiểm soát những phần tài sản khác như phần mềm trả phí, phụ kiện, vật tư tiêu hao, ... .

Người dùng khi được cấp tài sản, có thể theo giỏi được toàn bộ thông tin những tài sản mình được cấp.
Bằng cách đăng nập vào web server của công ty bằng tài khoản được cấp:

![image](https://user-images.githubusercontent.com/80932769/138846752-80afbd7e-fbb7-47a1-b64c-42c0e92de296.png)

### Backup. <a name="backup"></a>
Snipe-IT hỗ trợ công cụ backup trực tiếp trên web server, rất dễ dàng sử dụng.
Để có thể backup database của server, ta cần đăng nhập vào web server bằng tài khoản admin.
Vào phần Setting của tài khoản.

![Setiing](https://user-images.githubusercontent.com/80932769/139022285-f95cdef4-07ac-4eaf-aaab-81d8d88ea90f.png)

Tại đây có công cụ hỗ trợ backup.

![backup](https://user-images.githubusercontent.com/80932769/139022547-4e183586-2f23-4d95-be64-659be937dc42.png)

Sau khi chọn backup, ta sẽ được di chuyển đến giao diên backup của server.

![image](https://user-images.githubusercontent.com/80932769/139022682-7a8736d2-6aed-47d4-bbbf-bf7d8f3dbb08.png)

Để tạo file backup, ta chọn `Generate Backup`. File backup sẽ được tự động tạo với các thông tin như : tên file, ngày, giờ, kích thước file.
Để có thể tải file backup chỉ cần nhấn vào file backup đã được tạo, file sẽ được tự động tải.

### Restore. <a name="restore"></a>
Sử dụng Winscp kết nối tới server

![image](https://user-images.githubusercontent.com/80932769/138865578-c374287c-371c-4803-980a-d102c6f276d3.png)

File backup sau khi được tải về -> tiến hành giải nén.

Có 2 file var và db-dumps 

truy cập file var, ta được /var/www/snipe-it/ có 2 file storage và public 

bên phía server ta cũng truy cập theo đường dẫn /var/www/snipe-it/

tiến hành upload 2 file storage và public ta tải về lên server

![image](https://user-images.githubusercontent.com/80932769/138865722-93c8f3fb-10e6-47fd-82db-9c0cb9aac80a.png)

Với file db_dumps, ta có file mysql-snipe_it.sql . Để đơn giản nhất ta upload file này lên một folder được tạo sẵn tại server để lưu backup, đặt tên folder tùy ý.

![image](https://user-images.githubusercontent.com/80932769/138865803-95a450db-7354-4b49-9dfc-1ecfd29a0761.png)

Ở bài viết ta đặt backups : /root/backups
mkdir /root/backups

![image](https://user-images.githubusercontent.com/80932769/138864712-f664dc8f-e301-4cc3-885c-40eb657c4da7.png)

Tiến hành restore file backup
Sử dụng câu lệnh:
mysql -u [uname] -p [dbname] < [backupfile.sql]

Trong đó:
[uname] : User của database
[dbname] : Tên của database
[backupfile.sql] : Tên file backup đã lưu (file backup muốn phục hồi)

Theo như bài, ta sử dụng câu lệnh: mysql -u snipe_it_user -p snipe_it < mysql-snipe_it.sql
Sau khi restore lại database
Ta vào vị trí web server snipe-it
cd /var/www/snipe-it

Chạy câu lệnh để đảm bảo data được restore.
php artisan migrate
Chạy lệnh để xóa cấu hình được lưu trữ.
php artisan config:clear

![image](https://user-images.githubusercontent.com/80932769/138866133-795430c1-9b1b-4aa0-a13d-fba708f1c203.png)

Thành công server sẽ báo `Nothing to migrate`.

Lên Web Server để kiểm tra.

