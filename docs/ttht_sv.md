# III. Thao tác hệ thống.
[Backup.](#backup)

[Restore.](#restore)

[Auto Backup và Backup lên Drive](#autoanddrive)

[Mail.](#mail)

[< Mục lục.](https://github.com/TNtruong99/GitLab/blob/main/GitLab.md)

## 2. Thao tác với Server.
## <a name="backup"></a>2.1 Backup.
  
  `Backup` là một trong những vấn đề đáng lưu ý nhất khi làm việc vì khi làm việc có thể xảy ra nhiều lỗi gây mất dữ liệu, việc `backup` giúp ta có thể khôi phục lại hệ thống. Khi làm việc với `GitLab` dữ liệu của chúng ta sẽ phình to ra trong quá trình làm việc mà những phân vùng `root` lại thường có sức chứa khá nhỏ. Đó là lý do chúng ta nên chuyển dữ liệu `backup` ra một phân vùng khác cũng rất quan trọng.
  
   Để `backup` một server `GitLab` cơ bản có 2 bước chính:
  - Bước 1 : `backup` những file cấu hình quan trọng của server.
  - Bước 2 : `backup` dữ liệu của server.
  
  ### `Backup` những file cấu hình của server.
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

  ### `Backup` dữ liệu của server.
  
  Ta sẽ dùng chung folder `backups` của bước trên.
  
  Bước 1 : Tạo  folder`data` và  `backup` theo đường dẫn data/backups.
  ```
  mkdir -p data/backups
  ```
  Bước 2 : Truy cập vào file `gitlab.rb`.
  ```
  sudo vi /etc/gitlab/gitlab.rb
  ```
  Bước 3 : Nhấn `/` và nhập `backup` tìm đến vị trí đường dẫn `backup`.
  ```
  /backup
  ```
  
  ![image](https://user-images.githubusercontent.com/80932769/137049761-d360aab8-61bc-492b-99f3-a12a1a96a002.png)

  Đây là đường dẫn mặc định của file `backup` việc của chúng ta là thay đổi đường dẫn này đến vị trí folder mà ta đã tạo ở trên `/data/backups`.
  
  Bước 3 : Thay đổi đường dẫn.
  
  ![image](https://user-images.githubusercontent.com/80932769/137058962-be6274d5-7723-4549-b2bb-2563460c8a27.png)


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
  
  Bước 7 : Copy file `backup` vừa được tạo tại đường dẫn `/data/backups` sang thư mục `/home/truongtn/data/backup`.
  Kiểm tra file `backup` vừa được tạo:
  ```
  sudo ls -l /data/backups/
  ```
  
  ![image](https://user-images.githubusercontent.com/80932769/137059271-23588136-159e-4510-bc2d-20725f6b792c.png)

  Copy sang thư mục `/home/truongtn/data/backups`:
  ```
  sudo cp /data/backups/<file backup> /home/truongtn/data/backups
  ```
  
  ![image](https://user-images.githubusercontent.com/80932769/137059612-3b0d30fb-553c-4bae-8391-0b9796314710.png)

  Bước 8 : Xóa file `backup` tại đường dẫn /data/backups/<tên file>
  
  ```
  sudo rm -rf /data/backups/<tên file>
  ```
  
  

  Tới đây ta có thể đẩy những file `backup` ở các bước trên lên một nền tảng nào khác, đó là lựa chọn của mỗi người.
  Ngoài ra, còn một tùy chọn khá hay đó là việc xóa đi các file `backup` sau một khoảng thời gian được tạo ra.
  Ta tùy chỉnh file `/etc/gitlab/gitlab.rb`:
  ```
  ## Limit backup lifetime to 7 days - 604800 seconds
  gitlab_rails['backup_keep_time'] = 604800
  ```
  Ở đây thời gian sẽ được tính bằng giây, ta có thể tùy chỉnh thời gian mong muốn tồn tại của mỗi file `backup` sau khoảng thời gian quy định file `backup` sẽ được xóa. Một tính năng cũng khá cần thiết để quản lý bộ nhớ cho server.
  
 
  ## <a name="restore"></a>2.2 Restore.
  Quá trình `restore` cơ bản cũng sẽ có 2 bước :
  - Bước 1 : `restore` dữ liệu của server.
  - Bước 2 : `restore` những file cấu hình quan trọng của server.
  
  ### `restore` dữ liệu của server.
  Bước 1 : Copy file `backup` dữ liệu vào thư mục /data/backups
  ```
  sudo cp /home/truong/data/backups/<tên file> /data/backups/
  ```
  
  ![image](https://user-images.githubusercontent.com/80932769/137060270-fe3781ae-3e8c-412f-af1e-2fd84bcfa8d9.png)
  
  Bước 2 : Cấp quyền cho `user` git có thể đọc đươc file này.
  ```
  sudo chown git.git /data/backups/<tên file>
  ```
  Bước 3 : Tạm ừng một số `services` của server.
  ```
  sudo gitlab-ctl stop puma
  sudo gitlab-ctl stop sidekiq
  ```
  Sử dụng lệnh để kiểm trạng thái của `services`.
  ```
  sudo gitlab-ctl status
  ```
  
  ![image](https://user-images.githubusercontent.com/80932769/137060635-5b82f7d4-9920-4ac0-a567-be33f805784e.png)
  
  Bước 4 : Chạy lệnh `restore`.
  ```
  sudo gitlab-backup restore BACKUP=<tên file>
  ```
  *Lưu ý: <tên file> ở đây chỉ ghi từ đầu tiên tới phiên bản của `GitLab` thôi.
  VD : 1634093707_2021_10_13_14.3.2_gitlab_backup.tar thì ta sẽ ghi là 1634093707_2021_10_13_14.3.2
  
  Quá trình `restore` hệ thống sẽ `untar` file `backup` ra tại folder `/data/backups`.
  Trong quá trình `restore` hệ thống sẽ có 1 vài thông báo, chỉ cần `yes`.
  
  ![image](https://user-images.githubusercontent.com/80932769/137061301-61ad8e67-2f88-461a-b8cf-200af8ce6987.png)

  ![image](https://user-images.githubusercontent.com/80932769/137061331-985ae229-af8e-4bae-9a54-58984e57e208.png)
  
  ### `restore` những file cấu hình của server.
  Bước 1 : Copy 2 file cấu hình ở bước `backup` được lưu tại `/home/truongtn/data/backups` vào thư mục `/etc/gitlab/`
  ```
  sudo cp -f /home/truongtn/data/backups/gitlab.rb
  sudo cp -f /home/truongtn/data/backups/gitlab-secrets.json
  ```
  Dùng -f để ghi đè.
  Bước 2 : Cập nhật lại hệ thống.
  ```
  sudo gitlab-ctl reconfigure
  sudo gitlab-ctl restart
  ```
  Để kiểm tra quá trình `restore` diễn ra tốt, dùng lệnh:
  ```
  sudo gitlab-rake gitlab:check SANITIZE=true
  ```
  

  
  
  ## <a name="autoanddrive"></a>2.3 Auto Backup và Backup lên Drive.
  ## <a name="mail"></a>2.4 Mail.
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
