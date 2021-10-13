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


[III. Thao tác hệ thống.](#tthethong)
  - [1. Thao tác với Web GitLab.](#ttgitweb)
    - [1.1 Sign-up.](#signup)
  - [2. Thao tác với Server.](#ttserver)
    - [2.1 Backup.](#ttserver_backup)
  

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
  Sau khi `exit` thực hiện khởi động lại server để hệ thống cập nhật những thay đổi:
  ```
  sudo gillab-ctl reconfigure
  ```
  Tiến hành đăng nhập vào vào hệ thống trên web `192.168.15.130`.
  
  ![image](https://user-images.githubusercontent.com/80932769/136936831-d5826473-b28c-4f9a-a693-ba109c1ece32.png)

  Trang hệ thống sau khi đăng nhập thành công.
  
  ![image](https://user-images.githubusercontent.com/80932769/136936970-cd3b839b-a8c1-4c24-ae60-9d4c095e5ffa.png)
# <a name="tthethong"></a>III. Thao tác hệ thống.  
  ## <a name="ttgitweb"></a>1. Thao tác với Web GitLab.
  ### <a name="signup"></a>1.1 Sign-up.
  Để đảm bảo an toàn cho hệ thống, chúng ta không thể để người ngoài có thể tự đăng kí tài khoản để đăng nhập vào hệ thống.
  Chúng ta cần tắt đi chức năng đăng kí của `server`.
  
  ![image](https://user-images.githubusercontent.com/80932769/136938005-70980e9e-73fb-4081-9d7c-474cb7ca3332.png)
  
  Đầu tiên, đăng nhập vào Web của server ( như các bước trên)
  Tại đây ta chọn theo thứ tự : `Menu` > `Admin`
  
  ![image](https://user-images.githubusercontent.com/80932769/136938343-0e5532d8-ecb2-4459-a932-c64d62bc7a24.png)
  
  Tại tab quản lý của `Admin` chọn `Settings`
  
  ![image](https://user-images.githubusercontent.com/80932769/136938533-8b304311-6a2b-474f-a6b0-7277ba357cfc.png)
  
  Tại trang `Settings` ta chú y vào mục `Sign-up retriction`.
  
  ![sigup](https://user-images.githubusercontent.com/80932769/136938753-5da9b38a-3d80-4a2e-b1aa-65d830478438.png)
  
  
  Ta `Expand` mục `Sign-up retriction` và tiến hành bỏ chọn `Sign-up enabled` và `Save changes`.
  
  ![image](https://user-images.githubusercontent.com/80932769/136939019-508f8a57-47d6-4383-9f86-16fa80825b8e.png)
  
  Lúc này, tính năng đăng ký tài khoản đã bị loại bỏ. Nếu muốn có tài khoản đăng nhập thì phải thông qua `Admin` của server.
  
  ![image](https://user-images.githubusercontent.com/80932769/136939499-7e2a7f63-fbe1-48ee-afa9-2d1fc6fff31c.png)
  
  ## <a name="ttserver"></a>2. Thao tác với Server.
  ### <a name="ttserver_backup"></a>2.1 Backup.
  
  `Backup` là một trong những vấn đề đáng lưu ý nhất khi làm việc vì khi làm việc có thể xảy ra nhiều lỗi gây mất dữ liệu, việc `backup` giúp ta có thể khôi phục lại hệ thống. Khi làm việc với `GitLab` dữ liệu của chúng ta sẽ phình to ra trong quá trình làm việc mà những phân vùng `root` lại thường có sức chứa khá nhỏ. Đó là lý do chúng ta nên chuyển dữ liệu `backup` ra một phân vùng khác cũng rất quan trọng.
  
   Để `backup` một server `GitLab` cơ bản có 2 bước chính:
  - Bước 1 : `backup` những file cấu hình quan trọng của server.
  - Bước 2 : `backup` dữ liệu của server.
  
  #### `Backup` những file cấu hình của server.
  Bước 1 : Tạo một `folder` tại vị trí mà ta muốn lưu file `backup`.
  VD: Tạo `folder` **/home/truongtn/data/backups** để lưu dữ liệu tại đây.
  
  ![image](https://user-images.githubusercontent.com/80932769/137049348-41ddbd4f-bf22-426c-8e62-33d480ae83d6.png)

  Bước 2 : Sao chép 2 file `gitlab.rb` và gitlab-secrets.json` vào folder đã tạo ở bước trên.
  ```
  sudo cp /etc/gitlab/gitlab.rb /home/truongtn/data/backups/
  sudo cp /etc/gitlab/gitlab-secrets.json /home/truongtn/data/backups/
  ```
  Kiểm tra, ta được:
  
  ![image](https://user-images.githubusercontent.com/80932769/137051055-01c9e856-9df3-4d4b-8b0d-5b9efdc61d0d.png)

  #### `Backup` dữ liệu của server.
  Ta sẽ dùng chung folder `backups` của bước trên.
  Bước 1 : Truy cập vào file `gitlab.rb`.
  ```
  sudo vi /etc/gitlab/gitlab.rb
  ```
  Bước 2 : Nhấn `/` và nhập `backup` tìm đến vị trí đường dẫn `backup`.
  ```
  /backup
  ```
  
  ![image](https://user-images.githubusercontent.com/80932769/137049761-d360aab8-61bc-492b-99f3-a12a1a96a002.png)

  Đây là đường dẫn mặc định của file `backup` việc của chúng ta là thay đổi đường dẫn này đến vị trí folder mà ta đã tạo ở trên.
  
  Bước 3 : Thay đổi đường dẫn.
  
  ![image](https://user-images.githubusercontent.com/80932769/137049895-08358af0-2cc6-417d-bade-b6512ebb0c59.png)

  Bước 4 : Cập nhật lại hệ thống.
  ```
  sudo gitlab-ctl reconfigure
  ```
  Bước 5 : Cài gói `rsync` hỗ trợ backup.
  ```
  sudo apt-get install rsync
  ```
  
  Bước 6 : Chạy câu lệnh `backup`.
  Tùy vào phiên bản sử dụng có những câu lệnh khác nhau:
  - GitLab 12.2 hoặc mới hơn:
  ```
  sudo gitlab-backup create
  ```
  - GitLab 12.1 và cũ hơn:
  ```
  gitlab-rake gitlab:backup:create
  ```
  Để kiểm tra phiên bản GitLab sử dụng câu lệnh:
  ```
  sudo gitlab-rake gitlab:env:info
  ```
  
  ![image](https://user-images.githubusercontent.com/80932769/137051840-3a6728c6-24ae-47c7-8737-e58c902dfd51.png)
  
  `GitLab` cung cấp nhiều tùy chọn để ta có thể chọn trong quá trình `backup` như:
  - db (database)
  - uploads (attachments)
  - builds (CI job output logs)
  - artifacts (CI job artifacts)
  - lfs (LFS objects)
  - registry (Container Registry images)
  - pages (Pages content)
  - repositories (Git repositories data)
  
  Ta có thể tùy chính để chỉ `backup` những phần mà ta mong muốn bằng cách `skip` qua những cái khác.
  VD: Ở đây sẽ `backup` toàn bộ trừ `db` và `uploads`.
  - GitLab 12.2 hoặc mới hơn:
  ```
  sudo gitlab-backup create SKIP=db,uploads
  ```
  - GitLab 12.1 và cũ hơn:
  ```
  sudo -u git -H bundle exec rake gitlab:backup:create SKIP=db,uploads RAILS_ENV=production
  ```
  
  Để tìm hiểu chi tiết, có thể đọc tại : [GitLab backup and restore](https://docs.gitlab.com/ee/raketasks/backup_restore.html)
  
  Bước 7 : Kiểm tra file `backup` được tạo ra theo đường dẫn
  ```
  sudo ls -l /home/truongtn/data/backups
  ```
  
  ![image](https://user-images.githubusercontent.com/80932769/137052742-eaa38f04-988b-45cf-a145-af3c765ab2e1.png)

  Tới đây ta có đẩy nhưng file `backup` ở các bước trên lên một nền tảng nào khác, đó là lựa chọn của mỗi người.
  Ngoài ra, còn một tùy chọn khá hay đó là việc xóa đi các file `backup` sau một khoảng thời gian được tạo ra.
  Ta tùy chỉnh file `/etc/gitlab/gitlab.rb`:
  ```
  ## Limit backup lifetime to 7 days - 604800 seconds
  gitlab_rails['backup_keep_time'] = 604800
  ```
  Ở đây thời gian sẽ được tính bằng giây, ta có thể tùy chỉnh thời gian mong muốn tồn tại của mỗi file `backup` sau khoảng thời gian quy định file `backup` sẽ được xóa. Một tính năng cũng khá cần thiết để quản lý bộ nhớ cho server.
  
 
  
  

  
  ### <a name="ttserver_backup"></a>2.2 Mail.
  Đây là tính năng cần thiết mỗi khi thiết lập một `Server`. Khi người dùng được `Admin` cấp cho một tài khoản `GitLab` thì ta sẽ không thể biết được mật khẩu của tài khoản này khi đó `Server` sẽ gửi một `Mail` để ta có thể kích hoạt tài khoản và nhập mật khẩu cho tài khoản hoặc trong trường hợp ta quên mật khẩu và cần reset lại mật khẩu ta cần nhập `Mail` để khôi phục lại mật khẩu.
  
  Bắt đầu cấu hình dịch vụ `Mail` cho `Server`:
  Bước 1 : Ta cần phải tùy chỉnh lại file `gitlab.rb` theo đường dẫn `/etc/gitlab/gitlab.rb`.
  ```
  sudo vi /etc/gitlab/gitlab.rb
  ```
  Bước 2 : Nhấn phím `/` trên bàn phím và nhập `/smtp` để tìm đến phần dịch vụ `smtp` của `Server`.
  ```
  /smtp
  ```
  
  ![image](https://user-images.githubusercontent.com/80932769/136942763-84fcb145-0343-43cf-8196-fc3c81299a8a.png)
  
  Ở đây ta có nhiều dịch vụ `Mail` tùy chọn như Gmail, Mailgun, Amazon Simple Email System (AWS SES), ... .
  Để quen dễ sử dụng, bài viết chọn `Gmail`.
  Có thể xem `docs` tại đường link bên dưới để chọn dịch vụ `Mail`.
  
  [SMTP Services](https://docs.gitlab.com/omnibus/settings/smtp.html)
  
  Bước 3 : Copy toàn bộ câu lệnh của dịch vụ `Gmail` vào file. 
  ```
  gitlab_rails['smtp_enable'] = true
  gitlab_rails['smtp_address'] = "smtp.gmail.com"
  gitlab_rails['smtp_port'] = 587
  gitlab_rails['smtp_user_name'] = "<Gmail admin>"
  gitlab_rails['smtp_password'] = "<Password của gmail admin>"
  gitlab_rails['smtp_domain'] = "smtp.gmail.com"
  gitlab_rails['smtp_authentication'] = "login"
  gitlab_rails['smtp_enable_starttls_auto'] = true
  gitlab_rails['smtp_tls'] = false
  gitlab_rails['smtp_openssl_verify_mode'] = 'peer'
  ```
  Tuy nhiên nếu chỉ copy phần `command` của `Gmail` thì hệ thống vẫn không thể hoạt động. Nên ta phải thêm vào một số `command` sau:
  ```
  gitlab_rails['gitlab_email_from'] = '<Gmail admin>'
  gitlab_rails['gitlab_email_reply_to'] = '<Gmail admin>'
  gitlab_rails['gitlab_email_display_name'] = '<tên tài khoản Gmail admin>'
  ```
  Ta có được một bộ `command` chính xác như sau:
  
  ```
  gitlab_rails['smtp_enable'] = true
  gitlab_rails['smtp_address'] = "smtp.gmail.com"
  gitlab_rails['smtp_port'] = 587
  gitlab_rails['smtp_user_name'] = "<gmail admin>"
  gitlab_rails['smtp_password'] = "<password của gmail admin>"
  gitlab_rails['smtp_domain'] = "smtp.gmail.com"
  gitlab_rails['smtp_authentication'] = "login"
  gitlab_rails['smtp_enable_starttls_auto'] = true
  gitlab_rails['smtp_tls'] = false
  gitlab_rails['smtp_openssl_verify_mode'] = 'peer'
  gitlab_rails['gitlab_email_from'] = '<gmail admin>'
  gitlab_rails['gitlab_email_reply_to'] = '<gmail admin>'
  gitlab_rails['gitlab_email_display_name'] = '<tên tài khoản gmail admin>'
  ```
  Lưu lại và thoát ra.
  Bước 4 : Cập nhật lại hệ thống.
  ```
  sudo gitlab-ctl reconfigure
  ```
  
  *Lưu ý: Hệ thống `Gmail` có một tính năng bảo vệ tài khoản, từ chối từ những bên thứ 3. Để sử dụng dịch vụ `Gmail` của `Server` ta cần phải vô hiệu tính năng này tại `Gmail` của Admin.
  Để vô hiệu tính năng bảo vệ, ta làm theo các bước:
  Bước 1 : Đăng nhập vào tài khoản `Gmail` Admin.
  Bước 2 : Chọn mục quản lý tài khoản.
  
  ![image](https://user-images.githubusercontent.com/80932769/136946679-26e41be0-6733-4f63-b185-1b751f2c6560.png)

  Bước 3 : Chọn mục ` Bảo mật `.
  
  ![image](https://user-images.githubusercontent.com/80932769/136946777-ce1ad206-f9c5-4ad1-8a4d-4b378330f400.png)

  Bước 4 : Kéo xuống phần ` Quyền truy cập của ứng dụng kém an toàn `. Mặc định nó sẽ tắt, lúc này ta cần bật nó lên.
  
  ![136946679-26e41be0-6733-4f63-b185-1b751f2c6560](https://user-images.githubusercontent.com/80932769/136947969-a5321250-5c86-40a0-af65-d6def742391d.jpg)

   
  Làm theo các bước.
  
  Bước 5 : Xác nhận lại lần cuối. Lúc này Gmail sẽ gửi cho ta một lá thư cảnh bảo
  
  ![image](https://user-images.githubusercontent.com/80932769/136947206-1aa8a080-edf7-451a-af67-3856fd8028ee.png)

  Việc của chúng ta là xác nhận.
  
  ![222](https://user-images.githubusercontent.com/80932769/136947991-a891f589-85d0-4cb5-b2cc-8bf110387bf0.jpg)

  Để `test` xem dịch vụ có hoạt động hay không, thực hiện các bước sau :
  Bước 1 : Truy cập vào `Rails Console`.
  ```
  gitlab-rails console
  ```
  Bước 2 : Nhập câu lệnh.
  ```
  Notify.test_email('<Mail người nhận>', '<Subject>', '<Nội dung>').deliver_now
  ```
  
  VD: Notify.test_email('abc@gmail.com', 'Test', 'Day la email test').deliver_now
  
  
  ![image (1)](https://user-images.githubusercontent.com/80932769/136949240-f02c2083-9765-4088-951e-09692f1b54f2.jpg)
  
  
  Sau khi thực hiện câu lệnh hệ thống sẽ báo log lại đầy đủ thông tin. Để kiểm tra lần nữa ta vào `Gmail` của người nhận để kiểm tra.
  
  
  ![image (2)](https://user-images.githubusercontent.com/80932769/136949543-6da6c21a-de4f-4389-b9a1-20cdb6e8e75f.jpg)
  
  
  Xong các bước `test` ta tiến hành tạo `User` thông qua tài khoản `root`. Bây giờ tài khoản mới sau khi tạo có thể kích hoạt và thay đổi mật khẩu thông qua `Gmail` do `Server` gửi đến.
  
  
  
  ![image (3)](https://user-images.githubusercontent.com/80932769/136950316-7529e0a1-ab69-4acd-9cca-b8098d35d3f7.jpg) 
  
  Vào `Gmail` của tài khoản vừa được tạo để kích hoạt và `reset` mật khẩu.
  
  ![image](https://user-images.githubusercontent.com/80932769/136950738-998195ee-65ad-410d-b33b-e6e9459fa42e.png)
  
  Nhập mật khẩu mới và đăng nhập.
  Thay đổi mật khẩu:
  
  ![image](https://user-images.githubusercontent.com/80932769/136951117-dee7c1aa-3203-442b-b6ae-a0418f33949e.png)
  
  Đăng nhập:
  
  ![image](https://user-images.githubusercontent.com/80932769/136951263-bd039cf5-62ef-4ff6-ae99-ba27742adbb8.png)

  Thành công:
  
  ![image](https://user-images.githubusercontent.com/80932769/136951403-be610a6f-3780-490a-9507-b57ef5252f5f.png)



  
  
  
  

  




 

  
  

  


  
  
  
