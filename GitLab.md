# GITLAB
## Mục lục.
[I. GitLab](#GitLab)
  - [1. Khái quát](#KH)
  - [2. Đặc điểm](#DD)
    - [Phiên bản GitLab.](#Phb)
    - [Protected branches.](#Pb)
    - [Tầng vật lý của GitLab.](#Vatly)
    - [System layout.](#syslay)

[II. Cài đặt GitLab](#caidat)
  - [1. Cài đặt GitLab trên Ubuntu.](#ubuntu)


# <a name="GitLab"> I.GitLab
## <a name="KH"></a> 1.Khái quát.
`GitLab` là hệ thống self-hosted mã nguồn mở dựa trên hệ thống máy chủ Git dùng để quản lý mã nguồn của bạn. `GitLab` cung cấp giải pháp server một cách hoàn hảo và nhận được sự đánh giá cao từ cộng đồng.
## <a name="DD"></a> 2.Đặc điểm của GitLab.
- Phiên bản GitLab.
- Protected branches.
- Tầng vật lý của GitLab.
- System layout.
### <a name="Phb"></a>2.1.Phiên bản GitLab.
- `Gitlab community edition` (`CE`) là phiên bản cộng đồng, mã nguồn mở, cung cấp qua Git từ kho lưu trữ chứa GitLab. Bản mới nhất của `GitLab` được các nhà phát triển release tại các nhánh stable và nhánh master.
- GitLab enterprise edition (`EE`) là Gitlab phiên bản doanh nghiệp, có sẵn không lâu sau khi phát hành bản `CE`, được cung cấp từ kho lưu trữ của `gitlab.com`. Khi một doanh nghiệp đăng ký `GitLab` sẽ nhận được sự support của `GitLab` BV khi gặp khó khăn trong quá trình cài đặt và sử dụng.
- `itlab continuous integration` (`CI`) là một giải pháp tích hợp được thực hiện bởi nhóm phát triển `Gitlab`.
### <a name="Pb"></a>2.2.Protected branches.

Cho phép đọc hoặc ghi vào `repository` và các `branches`. `Protected branches` cấp quyền cho những người được phép commit và pushing code. Một protected branch gồm 3 điều cơ bản sau:
- Ngăn chặn việc push từ tất cả mọi người trừ user và master.
- Ngăn chặn việc push code lên branch từ những người không có quyền truy cập.
- Ngăn chặn bất kỳ ai thực hiện xóa branch.
`aster branch` được mặc định là `rotected branch`. User cần được cấp ít nhất một quyền từ master branch để bảo mật branch.
### <a name="Vatly"></a>2.3.Tầng vật lý của GitLab.
- `Kho lưu trữ`: là nơi xử lý các dự án trong GitLab, các dự án hoặc sản phẩm có thể được lưu tại warehouse.
- `ginx có cách thức hoạt động giống như front-desk`, người dùng đến Nginx và yêu cầu hành động được thực hiện bởi worker trong văn phòng.
- Cơ sở dữ liệu là các file của các metal file cabinets chứa các thông tin:
  - Sản phẩm trong warehouse (siêu dữ liệu, issuse, các yêu cầu merge …).
  - Người sử dụng đến front-desk (permissions).
  - `Redis` là phần giao tiếp một broad với cubby holes nơi chứa các nhiệm vụ, yêu cầu cho worker.
  - `Sidekiq` là một worker, công việc chủ yếu là xử lý việc gửi email, nhận nhiệm vụ từ Redis.
  - `A Unicorn worker` là một nhân viên xử lý các nhiệm vụ nhanh chóng và dễ dàng, làm việc cùng với Redis, bao gồm:
  - Kiểm tra quyền truy cập bằng cách kiểm tra các session của người dùng được lưu trữ trong Redis cubby hole.
  - Làm nhiệm vụ cho Sidekiq.
  - Lấy công cụ từ warehouse hoặc di chuyển mọi thứ xung quanh.
- `GitLab-shell`: là loại thứ ba của worker, thực hiện nhiệm vụ tạo các đơn đặt hàng từ một máy fax (SSH) thay vì front-desk (HTTP). GitLab-shell giao tiếp với Sidekiq qua Redis và hỏi những câu hỏi nhanh của Unicorn worker hoặc trực tiếp hoặc qua front-desk.
- `GitLab enterprise edition` là tập hợp các quy trình và hoạt động kinh doanh được điều hành bởi office.
### <a name="syslay"></a>2.4.System layout.
`Repositories` bare trong đường dẫn `/home/git/repositories`. `Gitlab` là một ứng dụng được viết bằng ngôn ngữ ruby on rails. Do đó, để biết rõ các hoạt động bên trong bạn có thể tìm hiểu về hoạt động của ruby on rails.
Ứng dụng GitLab-shell được cài đặt tại thư mục: `home/git/gitlab-shell` cho phép sử dụng kho dữ liệu qua SSH.

# <a name="caidat"></a>II. Cài đặt GitLab.
## <a name="ubuntu"></a>1. Cài đặt GitLab trên Ubuntu.
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
  
  
  
  
  

  




 

  
  

  


  
  
  
