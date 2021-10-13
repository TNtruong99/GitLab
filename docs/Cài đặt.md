# II. Cài đặt GitLab trên Ubuntu.
- [< mục lục](https://github.com/TNtruong99/GitLab/blob/main/GitLab.md)

## 1. Cài đặt GitLab trên Ubuntu.
Trong bài hướng dẫn này sử dụng công cụ `VMware` để mô phỏng môi trường cài đặt, và sử dụng phiên bản `Gitlab community edition` (`CE`).

  Bước 1: Tải phiên bản `Ubuntu` phù hợp với nhu cầu sử dụng.
  - [Ubuntu 20.04 Live](https://releases.ubuntu.com/20.04/ubuntu-20.04.3-live-server-amd64.iso)
  
  Bước 2: Mở `VMware` và tiến hành cài đặt Ubuntu như bình thường.
  
  Bước 3: Sau khi cài xong `Ubuntu` tiến hành update hệ thống và cài đặt một số dịch vụ đi kèm.
  Cập nhật hệ thống:
  ```
  sudo apt update
  sudo apt upgrade -y
  ```
  Cài dịch vụ:
  ```
  sudo apt install -y ca-certificates curl openssh-server
  ```
  
  ![1231](https://user-images.githubusercontent.com/80932769/136925825-5df49320-00cf-4990-bd64-574baf4943ed.png)
  
  Bước 4: Tải gói `GitLab CE`.
  ```
  curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
  ```
  ![image](https://user-images.githubusercontent.com/80932769/136926888-ad81b545-cc9e-4254-97f0-daec765f866e.png)

  
  Bước 5: Tiến hành cài đặt gói `Gitlab CE`.
  ```
  sudo apt -y install gitlab-ce
  ```
  Sau khi cài đặt hoàn tất, ta được một giao diện như hình.
  
  ![image](https://user-images.githubusercontent.com/80932769/136927422-e7e9de7b-9ae5-4789-9ca1-170df17ad96d.png)
  
  Ta khởi động server bằng lệnh:
  ```
  sudo gitlab-ctl reconfigure
  ```
  Do server phải khởi động  `services` nên sẽ mất một khoản thời gian tùy thuộc vào phần cứng của mỗi server.
  
  Bước 6: Sử dụng lệnh `ifconfig` để kiểm tra `IP` của server.
  ```
  ifconfig
  ```
  
  ![image](https://user-images.githubusercontent.com/80932769/136928000-e62b0944-68cf-454e-a504-533d54967f34.png)
  
  Ta dùng địa chỉ của server nhập vào một trình duyệt web để vào trang Web của GitLab, ở đây là: `192.168.15.130`.
  
  ![image](https://user-images.githubusercontent.com/80932769/136928399-9ef76a5d-6f6d-449c-aa5d-e0a5b3f12469.png)
  
  
  Sau khi vào web hệ thống sẽ cho phép ta đổi `password` của tài khoản `root` của GitLab.
  Ta chỉ cẩn nhập mật khẩu mới thì sẽ thay đổi được `password`.
  
  ![install-gitlab-centos-7-fedora-29-set-root-password-01](https://user-images.githubusercontent.com/80932769/136929907-687845ff-840d-494b-9c2d-eea57f09ce5c.png)
  
  Lưu ý: Tuy nhiên sẽ có trường hợp ta không thay đổi được `password của tài khoản `root` mà sẽ hiện lên một `form` đăng nhập sử dụng `username` hoặc `email` đã được đăng kí vào hệ thống. Nhưng vì do server vừa được tạo nên ta không có tài khoản cá nhân từ đó không thể đăng nhập vào server.
  
  ![image](https://user-images.githubusercontent.com/80932769/136930525-aa0fcd96-393a-4f44-8674-004e9cc737df.png)
  
  Trong trường hợp này, để đăng nhập bằng tài khoản `root` ta cần phải reset `password` cho tài khoản `root`:
  Đầu tiên, ta phải đăng nhập vào Rails Console:
  ```
  sudo gitlab-rails console
  ```
  ![image](https://user-images.githubusercontent.com/80932769/136932546-c3fd638e-4caa-4fe4-8d23-483e1fa12971.png)
  
  Ta tìm user root:
  ```
  u = User.where(id:1).first
  ```
  ![image](https://user-images.githubusercontent.com/80932769/136932953-4e2cf159-ac36-445c-a66e-28a0917d21f1.png)
  
  Ta nhập mật khẩu cho user:
  ```
  u.password = '<mật khẩu>'
  ```
  ![image](https://user-images.githubusercontent.com/80932769/136933554-d1b982ac-f84e-4f12-88c4-8299aed5f7af.jpg)
 
  Nhập lại mật khẩu:
  ```
  u.password.comfirmation = '<nhập lại mật khẩu>'
  ```
  
  ![image1](https://user-images.githubusercontent.com/80932769/136934530-9485c9d4-1427-4e0e-a008-10631998f11c.jpg)
  
  Lưu lại mật khẩu cho `User`:
  ```
  u.save!
  ```
  
  ![image](https://user-images.githubusercontent.com/80932769/136934716-a6d8347d-8847-4f90-8598-221212c13ac0.png)
  
  Hệ thống thông báo cập nhật thành công.
  
  ![image](https://user-images.githubusercontent.com/80932769/136935011-3d555627-b1a2-424f-b90a-90a0a8e5456c.png)

  Sử dụng câu lệnh `exit` để thoát chế độ `Rails Console`
  ```
  exit
  ```
  Sau khi `exit` thực hiện khởi động lại server để hệ thống cập nhật những thay đổi:
  ```
  sudo gillab-ctl reconfigure
  ```
  Tiến hành đăng nhập vào vào hệ thống trên web `192.168.15.130`.
  
  ![image](https://user-images.githubusercontent.com/80932769/136936831-d5826473-b28c-4f9a-a693-ba109c1ece32.png)

  Trang hệ thống sau khi đăng nhập thành công.
  
  ![image](https://user-images.githubusercontent.com/80932769/136936970-cd3b839b-a8c1-4c24-ae60-9d4c095e5ffa.png)
