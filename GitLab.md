# GITLAB
## Mục lục.
- [GitLab](#GitLab)
  - [KH](#1.Khái quát)
- 





















# I.GitLab
#### 1.Khái quát.
`GitLab` là hệ thống self-hosted mã nguồn mở dựa trên hệ thống máy chủ Git dùng để quản lý mã nguồn của bạn. `GitLab` cung cấp giải pháp server một cách hoàn hảo và nhận được sự đánh giá cao từ cộng đồng.
#### 2.Đặc điểm của GitLab.
- Phiên bản GitLab.
- Protected branches.
- Tầng vật lý của GitLab.
- System layout.
##### 2.1.Phiên bản GitLab.
- `Gitlab community edition` (`CE`) là phiên bản cộng đồng, mã nguồn mở, cung cấp qua Git từ kho lưu trữ chứa GitLab. Bản mới nhất của `GitLab` được các nhà phát triển release tại các nhánh stable và nhánh master.
- GitLab enterprise edition (`EE`) là Gitlab phiên bản doanh nghiệp, có sẵn không lâu sau khi phát hành bản `CE`, được cung cấp từ kho lưu trữ của `gitlab.com`. Khi một doanh nghiệp đăng ký `GitLab` sẽ nhận được sự support của `GitLab` BV khi gặp khó khăn trong quá trình cài đặt và sử dụng.
- `itlab continuous integration` (`CI`) là một giải pháp tích hợp được thực hiện bởi nhóm phát triển `Gitlab`.
##### 2.2.Protected branches.
Cho phép đọc hoặc ghi vào `repository` và các `branches`. `Protected branches` cấp quyền cho những người được phép commit và pushing code. Một protected branch gồm 3 điều cơ bản sau:
- Ngăn chặn việc push từ tất cả mọi người trừ user và master.
- Ngăn chặn việc push code lên branch từ những người không có quyền truy cập.
- Ngăn chặn bất kỳ ai thực hiện xóa branch.
`aster branch` được mặc định là `rotected branch`. User cần được cấp ít nhất một quyền từ master branch để bảo mật branch.
##### 2.3.Tầng vật lý của GitLab.
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
##### 2.4.System layout.
`Repositories` bare trong đường dẫn `/home/git/repositories`. `Gitlab` là một ứng dụng được viết bằng ngôn ngữ ruby on rails. Do đó, để biết rõ các hoạt động bên trong bạn có thể tìm hiểu về hoạt động của ruby on rails.

Ứng dụng GitLab-shell được cài đặt tại thư mục: `home/git/gitlab-shell` cho phép sử dụng kho dữ liệu qua SSH.
