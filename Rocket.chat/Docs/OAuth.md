### Rocket.Chat hỗ trợ một số cách khác nhau để xác thực, ngoài xác thực tên người dùng / mật khẩu cơ bản. 

Để thiết lập nhà cung cấp dịch vụ:

1. Chọn phần quản trị

![image](https://user-images.githubusercontent.com/59860781/138020141-e5bb9705-d266-4b65-a54f-a1264c085fe4.png)

2. Tìm đến oAuth

![image](https://user-images.githubusercontent.com/59860781/138020297-cde83115-8880-43d7-b68e-e87759235b72.png)

3. Thiết lập đăng nhập bằng gmail
- Đăng nhập vào [google console](https://console.cloud.google.com/apis/dashboard)
- Chọn tạo mới project

![image](https://user-images.githubusercontent.com/59860781/138020882-f4e90819-b017-476d-8385-1be0d51ac1d4.png)

- Tiến hành đặt tên cho dự án mới

![image](https://user-images.githubusercontent.com/59860781/138020965-57c01a61-4172-4d82-a21e-deb8be58b2d5.png)

- Sau khi tạo dự án xong, quay trở lại trang chủ và chọn OAuth consent screen

![image](https://user-images.githubusercontent.com/59860781/138021134-bd1c7b10-5d47-431f-8622-f46d2361e405.png)

- Chọn External để mọi người đều có thể truy cập

![image](https://user-images.githubusercontent.com/59860781/138021182-6db8f0f7-352b-4637-a6a3-3a434f816611.png)

- Đặt tên và gmail của người support dự án

![image](https://user-images.githubusercontent.com/59860781/138021326-379dce28-9eee-4328-a8b6-cd7f5637dc3f.png)

- Ấn save and continue tới khi xuất hiện như hình

![image](https://user-images.githubusercontent.com/59860781/138021791-6090799c-350e-4389-a9fe-f18add876933.png)

- Chọn phần Credentials => Create Credentials => OAuth client ID

![image](https://user-images.githubusercontent.com/59860781/138021881-cec12489-dc94-4cd4-a20a-0eeb40867201.png)

- Chọn hệ điều hành mà ứng dụng của bạn đang chạy

![image](https://user-images.githubusercontent.com/59860781/138021980-1dd970bf-9f42-4c4b-bacf-26d2007326b4.png)+

- Truy cập vào OAuth bên Rocket.Chat để lấy URL call back google

![image](https://user-images.githubusercontent.com/59860781/138022133-12a327c1-fe2b-49d4-8a02-a604882ec468.png)

- Đặt tên và nhập URIs

![image](https://user-images.githubusercontent.com/59860781/138022199-40850eee-c032-4c1a-aa28-4125df670640.png)

-  Lưu ý: cần thêm **?close** sau URIs để có thể truy vấn về lại google

- Sau khi tạo xong, sẽ xuất hiện 2 key

![image](https://user-images.githubusercontent.com/59860781/138022538-4f52cb91-5c12-4039-9809-2e6182e5e01c.png)

- Dùng 2 key trên để add ngược vào Rocket.Chat

![image](https://user-images.githubusercontent.com/59860781/138022586-92c5bbf6-50eb-4e31-9719-3adc59ff2216.png)

- Tiến hành lưu thay đổi.
- Thử đăng nhập bằng gmail

![image](https://user-images.githubusercontent.com/59860781/138022681-cc25a6aa-cc82-470c-82e5-886778a26595.png)


