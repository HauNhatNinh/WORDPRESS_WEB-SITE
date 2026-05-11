# SỬ DỤNG WORDPRESS ĐỂ TẠO WEB SITE

## WordPress – Hệ quản trị nội dung (CMS) "mì ăn liền" vĩ đại nhất thế giới, chiếm tới hơn 40% website trên toàn cầu.
## Dự án này sẽ nhàn về mặt code, thiên về kỹ năng Quản trị hệ thống (SysAdmin) và thao tác giao diện.
---
# BƯỚC 1: Dọn dẹp máy ảo và Chuẩn bị thư mục

Mở PowerShell (SSH vào Ubuntu) và gõ các lệnh sau để tắt dự án cũ (nếu có), giải phóng cổng mạng và RAM:

```bash
# Tắt dự án Cầm đồ
cd ~/cam_do_project
docker compose down

# Tạo thư mục cho dự án WordPress mới
cd ~
mkdir wordpress_project
cd wordpress_project
```
<img width="1400" height="257" alt="image" src="https://github.com/user-attachments/assets/7a5e67a5-424e-4fcc-ad8f-c8e43a162178" />

# BƯỚC 2: Viết file docker-compose.yml

Tạo file cấu hình bằng lệnh:

```bash
nano docker-compose.yml
```

Dán đoạn cấu hình chuẩn này vào (Lưu ý: Mình đổi cổng phpMyAdmin thành `8081` và WordPress thành `8001` để tránh đụng độ nếu dự án cũ vô tình còn kẹt lại):

```yaml
version: '3.8'

services:
  # 1. Cơ sở dữ liệu MariaDB
  db:
    image: mariadb:latest
    container_name: wp_db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      # KHÔNG dùng MYSQL_DATABASE ở đây để tuân thủ đúng yêu cầu đề bài (tự tạo bằng phpMyAdmin)
    volumes:
      - db_data:/var/lib/mysql

  # 2. phpMyAdmin để quản trị CSDL
  phpmyadmin:
    image: phpmyadmin:latest
    container_name: wp_pma
    restart: always
    environment:
      PMA_HOST: db
      PMA_USER: root
      PMA_PASSWORD: rootpassword
    ports:
      - "8081:80"
    depends_on:
      - db

  # 3. WordPress (Truyền sẵn biến môi trường)
  wordpress:
    image: wordpress:latest
    container_name: wp_web
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: root
      WORDPRESS_DB_PASSWORD: rootpassword
      WORDPRESS_DB_NAME: wordpress_db # Lát nữa phải tạo đúng tên này trong PMA
    ports:
      - "8001:80"
    depends_on:
      - db

volumes:
  db_data:
```
<img width="1919" height="1079" alt="Screenshot 2026-05-11 205629" src="https://github.com/user-attachments/assets/e19e8505-830f-4213-811c-01698ff9ca10" />

Nhấn `Ctrl + O -> Enter -> Ctrl + X` để lưu và thoát. Sau đó chạy lệnh dưới đây để khởi động dự án:

```bash
docker compose up -d
```
<img width="1919" height="262" alt="Screenshot 2026-05-11 210508" src="https://github.com/user-attachments/assets/92dcb5f1-6c73-4311-94cd-940b54d70899" />

## Truy cập Web

Bây giờ, đã có thể dùng các đường link sau trên trình duyệt:

- phpMyAdmin: `http://192.168.227.128:8081`
<img width="1917" height="1025" alt="Screenshot 2026-05-11 212659" src="https://github.com/user-attachments/assets/f0d914b9-9a8d-4441-871d-0655bd729a95" />

- WordPress: `http://192.168.227.128:8001` (chưa hiển thị gì là do là vì WordPress cố tìm cái database tên là wordpress_db mà nó chưa thấy đâu)
<img width="1914" height="1023" alt="Screenshot 2026-05-11 212721" src="https://github.com/user-attachments/assets/414bfcc6-8874-4f81-9baa-f9ee3860646b" />

## Tạo Database trên phpMyAdmin

Quay lại tab phpMyAdmin (`http://192.168.227.128:8081`).

Nhìn cột bên trái, trên cùng có chữ `"Mới"` (`New`) với biểu tượng tờ giấy, bấm vào đó.

Ở ô `"Tên cơ sở dữ liệu"`, gõ chính xác: `wordpress_db` (viết thường, không dấu, có gạch nối dưới).

Bấm nút `"Tạo"` (`Create`).
<img width="1919" height="1025" alt="Screenshot 2026-05-11 213000" src="https://github.com/user-attachments/assets/60b09b64-d2d5-4e80-877a-795e363fc213" />

Sau khi bấm, sẽ thấy tên `wordpress_db` xuất hiện ở danh sách bên trái. Xong nhiệm vụ của phpMyAdmin.
<img width="302" height="351" alt="Screenshot 2026-05-11 213026" src="https://github.com/user-attachments/assets/9ddc6d85-02f2-47e2-bf4f-93b29b4e3acf" />

---

## F5 bên trang WordPress

Quay lại tab WordPress (`http://192.168.227.128:8001`).

Nhấn phím `F5` (hoặc nút Tải lại trang).
<img width="1919" height="1027" alt="Screenshot 2026-05-11 213126" src="https://github.com/user-attachments/assets/c4cccb52-c2fe-456d-82a0-649c752b3266" />
Màn hình chọn ngôn ngữ của WordPress sẽ hiện ra thay vì cái dòng lỗi kia!
