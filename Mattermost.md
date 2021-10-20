# MATTERMOST
[I. Mattermost.](#mattermost)
- [Khái quát.](#khaiquat)
- [Tính năng.](#tinhnang)

[II. Cài đặt](#caidatmattermost)
1. [Cài đặt trên Ubunlu 20.04](#caidat)
    - [MySQL Databse Server](#mysql)
    - [Mattermost](#mattermost)

## <a name=mattermost></a> I.Mattermost.
### <a name=khaiquat></a>Khái quát.
`Mattermost` là giải pháp phần mềm thay thế cho các doanh nghiệp vừa và nhỏ có nhu cầu sử dụng các giải pháp trao đổi nội bộ.
Một lựa chọn khá tốt cho việc thay thế các công cụ chat mà các công ty đang sử dụng vì giao diện và tính năng của `Mattermost` giống như `Slack`.

Hỗ trợ cả app cài đặt trên điện thoại và các thiết bị di động đó cũng là một trong những lợi thế mà `Mattermost` mang đến trải nghiệm hoàn toàn thoải mái cho người dùng. Việc di chuyển sang `Mattermost` từ các ứng dụng chat trước đó không tốn quá nhiều thời gian vì giao diện hoạt động và thao tác hoàn toàn quen thuộc.

### <a name=tinhnang></a>Tính năng.
- Đồng bộ theo thời gian thực trên tất cả các thiết bị và hệ điều hành.
- Tin nhắn được bảo mật.
- Hỗ trợ DevOps.
- Bảo vệ theo IP.
- Bảo vệ chống lại các nguy cơ bảo mật.
- Nhắn tin cá nhân và nhắn tin nhóm.
- Lưu lịch sử tin nhắn.
- Chia sẻ tệp, hình ảnh và liên kết.
- Hỗ trợ đa ngôn ngữ.
- Biểu tượng cảm xúc và định dạng văn bản phong phú.
- Chia sẻ giọng nói, video và màn hình.
- Cho phép tùy chỉnh thông báo.


## <a name=caidatmattermost></a>II.Cài đặt.
### <a name=caidat></a> 1. Cài đặt trên Ubunlu 20.04
Sau khi cài đặt `Ubuntu 20.04` tiến hành cập nhật hệ thống
```
sudo apt update
sudo apt upgrade
```

### <a name=mysql></a>MySQL Database Server.
Tiến hành cài đặt `MySQL Server`
Bước 1: Cài `MySQL`
```
sudo apt install mysql-server
```
Bước 2: Chạy lệnh.
```
sudo mysql_secure_installation
```
Hệ thống sẽ có những tùy chọn cho `server`, tùy chọn theo nhu cầu cá nhân.
Trong quá trình tùy chọn, MySQL sẽ cho phép thay đổi `password` của `user` root MySQL.

Bước 3: Đăng nhập vào MySQL bằng tài khoản `root`.
```
sudo mysql
```
Bước 4: Tạo `user` mới dùng để đăng nhập `database` của Mattermost sau này.

*Lưu ý: Phải tạo `user` sử dụng `native password`. Nếu `native password` không được tạo cho `user` ở phiên bản MySQL8.0.11 trở về sau, nó sẽ thông báo chứng thực thất bại. Để khắc phục hãy sử dụng `native-password`.

```
CREATE USER '<tên user>'@'localhost' IDENTIFIED WITH mysql_native_password BY '<password>';
```
Bước 5: Tạo một `database` đặt tên là `mattermost`.
```
create database mattermost;
```
Bước 6: Cấp quyền truy cập cho `user` vừa được tạo ở các bước trên và thoát ra.
```
grant all privileges on mattermost.* to '<tên user>'@'localhost';
exit
```
### <a name=mattermost></a>Mattermost.
Tiến hành cài đặt `Mattermost`.
Bước 1: Download `Mattermost`.
```
wget https://releases.mattermost.com/5.0.0/mattermost-5.0.0-linux-amd64.tar.gz
```
Bước 2: Giải nén.
```
tar -xvzf mattermost*.gz
```
Bước 3: Di chuyển folder mattermost sang `/opt`.
```
sudo mv mattermost /opt
```
Bước 4: Tạo folder `/data` trong folder `mattermost`.
```
sudo mkdir /opt/mattermost/data
```
Bước 5: Tạo `user` và `group` mattermost.
```
sudo useradd --system --user-group mattermost
```
Bước 6: Thay đổi quyền sở hữu folder `mattermost` cho `user` và `group` : `mattermost`.
```
sudo chown -R mattermost:mattermost /opt/mattermost
```
Bước 7: Cấp quyền cho group `mattermost`.
```
sudo chmod -R g+w /opt/mattermost
```
Bước 8: Tùy chỉnh database driver của `mattermost`.
```
sudo vi /opt/mattermost/config/config.json
```
- Tìm `DriverName` bằng cách nhấn phím `/` và gõ `DriverName`. Thêm `mysql` vào phần thiếu.
- Tìm `DataSource` bằng cách nhẫn phím `/` và gõ `DataSource`. Thêm `<user MySQL>:<pasword>@tcp(localhost:3306)/mattermost?charset=utf8mb4,utf8&readTimeout=30s&writeTimeout=30s`
- Tìm `SiteURL` bằng cách nhẫn phím `/` và gõ `SiteURL`. Thêm `https://mattermost.example.com`

Bước 9: Kiểm tra server `Mattermost` và đảm bảo mọi thứ hoạt động đúng.
```
cd /opt/mattermost
sudo -u mattermost ./bin/mattermost
```
Khi server khởi động, hệ thống sẽ thông báo `Server is listening on :8065`.
Nhấn `Crtl + C` để dừng.

Bước 10: Thiết lập cho Mattermost sử dụng `systemd`.
- Tạo file `systemd`:
```
sudo touch /lib/systemd/system/mattermost.service
```
- Mở file vừa tạo và thêm vào những dòng dưới đây:
```
vi /lib/systemd/system/mattermost.service
```
```
[Unit]
Description=Mattermost
After=network.target
After=mysql.service
BindsTo=mysql.service

[Service]
Type=notify
ExecStart=/opt/mattermost/bin/mattermost
TimeoutStartSec=3600
KillMode=mixed
Restart=always
RestartSec=10
WorkingDirectory=/opt/mattermost
User=mattermost
Group=mattermost
LimitNOFILE=49152

[Install]
WantedBy=mysql.service
```
- Đảm bảo file hoạt động:
```
sudo systemctl daemon-reload
sudo systemctl status mattermost.service
```
- Bắt đầu dịch vụ:
```
sudo systemctl start mattermost.service
sudo systemctl enable mattermost.service
```
Sau khi cài đặt xong đăng nhập vào vào server `Mattermost` thông qua IP của server.
Server sẽ cho phép tạo `user` và `team` đầu tiên. `User` đầu tiên được tạo được mặc định là `user` admin, có quyền điều khiển hệ thống.
Mở `System Console` để có thể tùy chỉnh server theo nhu cầu.

![image](https://user-images.githubusercontent.com/80932769/138053639-b6185625-6fe9-4c60-8670-490b30686d9c.png)

![image](https://user-images.githubusercontent.com/80932769/138053681-7c04acad-f7cb-4442-8e32-93f80683c0ba.png)



