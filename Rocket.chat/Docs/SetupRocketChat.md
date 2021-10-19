## Hướng dẫn
Hướng dẫn cài đặt này được triển khai trên môi trường sau:
- Rocket.Chat 3.9.0
- Hệ điều hành Ubuntu 19.04 and Ubuntu 20.04(Latest)
- Mongodb 4.0.9
- NodeJS 12.18.4

### Bước 1: Cài đặt các gói package
> sudo apt-get -y update

![image](https://user-images.githubusercontent.com/59860781/137444410-9ff4da78-a9ec-4340-b16b-8cd0ffa3f317.png)

- Hệ thống yêu cầu cần phải có MongoDB, thế nên ta cần cài đặt. Đầu tiên ta add vào keyserver:
> sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 9DA31620334BD75D9DCB49F368818C72E52529D4

![image](https://user-images.githubusercontent.com/59860781/137444499-d2118731-2542-467d-8662-aefc3590e6df.png)

> echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.0.list

![image](https://user-images.githubusercontent.com/59860781/137444525-3d76e72d-092b-4deb-9306-cb1cf77001ef.png)

- Thêm gói cài đặt Node.js bằng lệnh sau:

> sudo apt-get -y update && sudo apt-get install -y curl && curl -sL https://deb.nodesource.com/setup_12.x | sudo bash -

![image](https://user-images.githubusercontent.com/59860781/137444640-3715133d-6f54-4a30-9a64-623170766171.png)

- Cài đặt build tools, MongoDB, nodejs và graphicsmagick:

> sudo apt-get install -y build-essential mongodb-org nodejs graphicsmagick

![image](https://user-images.githubusercontent.com/59860781/137444722-20a829ae-929f-4b23-9ef7-e511479cef25.png)

- Cài đặt npm cho ubuntu:

> sudo apt-get install -y npm

![image](https://user-images.githubusercontent.com/59860781/137444838-15554f19-9872-41bd-8219-c244baf01a1e.png)

- Kế thừa và cài đặt node mà Rocket.Chat yêu cầu:

> sudo npm install -g inherits n && sudo n 12.18.4

![image](https://user-images.githubusercontent.com/59860781/137445134-bbeb3172-9158-446b-aef1-58d23c39c139.png)

### Bước 2: Tải Rocket.Chat
- Dowload phiên bản Rocket.Chat mới nhất

> curl -L https://releases.rocket.chat/latest/download -o /tmp/rocket.chat.tgz

![image](https://user-images.githubusercontent.com/59860781/137445513-2fdf1be5-21f1-42ea-8f67-dc6eae796a33.png)

- Giải nén file rocket.chat.tgz

> tar -xzf /tmp/rocket.chat.tgz -C /tmp

![image](https://user-images.githubusercontent.com/59860781/137445547-fb8d8854-db25-47da-81b4-4b6b940bb493.png)

- Tiến hành cài đặt Rocket.Chat (sử dụng thư mục /opt  để cài đặt hoặc có thể sử dụng thư mục khác):

> cd /tmp/bundle/programs/server && npm install
> sudo mv /tmp/bundle /opt/Rocket.Chat

![image](https://user-images.githubusercontent.com/59860781/137445654-000167a3-6172-41eb-9aa6-83de9093bdfe.png)

### Bước 3: Cầu hình Rocket.Chat service:
- Thêm rocketchat user và tiến hành set owner cho thư mục Rocket.Chat:

>  sudo useradd -M rocketchat && sudo usermod -L rocketchat
>  sudo chown -R rocketchat:rocketchat /opt/Rocket.Chat

![image](https://user-images.githubusercontent.com/59860781/137445975-becd0d1c-ba78-421e-a0f7-8331431e39b4.png)

- Tạo file Rocket.Chat service bằng lệnh sau,

> cat << EOF |sudo tee -a /lib/systemd/system/rocketchat.service

> [Unit]

> Description=The Rocket.Chat server

> After=network.target remote-fs.target nss-lookup.target nginx.service mongod.service

> [Service]

> ExecStart=/usr/local/bin/node /opt/Rocket.Chat/main.js

> StandardOutput=syslog

> StandardError=syslog

> SyslogIdentifier=rocketchat

> User=rocketchat

> Environment=MONGO_URL=mongodb://localhost:27017/rocketchat?replicaSet=rs01 MONGO_OPLOG_URL=mongodb://localhost:27017/local?replicaSet=rs01 ROOT_URL= **http://localhost:3000/ **PORT=3000

> [Install]

> WantedBy=multi-user.target

> EOF

- Lưu ý: Thay http://localhost:3000/ bằng tên miền của bạn

- Thiết lập công cụ lưu trữ và sao chép cho MongoDB, đồng thời khởi động MongoDB và Rocket.Chat bằng cách chạy lần lượt các lệnh dưới đây:

> sudo sed -i "s/^#  engine:/  engine: mmapv1/"  /etc/mongod.conf

> sudo sed -i "s/^#replication:/replication:\n  replSetName: rs01/" /etc/mongod.conf

> sudo systemctl enable mongod && sudo systemctl start mongod

> mongo --eval "printjson(rs.initiate())"

> sudo systemctl enable rocketchat && sudo systemctl start rocketchat

- Truy cập vào Rocket.Chat của bạn thông qua tên miền đã thiết lập
> localhost:3000

![image](https://user-images.githubusercontent.com/59860781/137447196-441c69a3-f6c4-487f-bf2d-2db299d90570.png)

