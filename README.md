# SỬ DỤNG WORDPRESS ĐỂ TẠO WEB SITE

## WordPress – Hệ quản trị nội dung (CMS) "mì ăn liền" vĩ đại nhất thế giới, chiếm tới hơn 40% website trên toàn cầu.
## Dự án này sẽ nhàn về mặt code, thiên về kỹ năng Quản trị hệ thống (SysAdmin) và thao tác giao diện.
---
# Dọn dẹp máy ảo và Chuẩn bị thư mục

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

# Viết file docker-compose.yml

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

## Cài đặt WordPress (Mì ăn liền)

Sau khi `F5`, làm theo các bước nó hướng dẫn:

- Chọn ngôn ngữ: `Tiếng Việt` (cho dễ dùng).

- Tiêu đề trang: `Website của ...`.

- Tên người dùng: (Đặt gì cũng được, ví dụ `admin`).

- Mật khẩu: (đặt mật khẩu dễ nhớ, hoặc copy cái nó gợi ý).

- Email: Điền email.

- Bấm `Cài đặt WordPress`.
<img width="1919" height="1020" alt="Screenshot 2026-05-11 213357" src="https://github.com/user-attachments/assets/b2e149e5-6fe8-466a-abd6-b863dda409c7" />

- Thành công tạo tài khoản WordPress
<img width="1919" height="1023" alt="Screenshot 2026-05-11 213438" src="https://github.com/user-attachments/assets/1e462b25-7dc0-48ce-8d72-c20fcdffb765" />

## Đăng nhập vào quản trị

Bấm vào chữ `Đăng nhập` trên màn hình đó (hoặc vào link `http://192.168.227.128:8001/wp-admin`). Nhập `Username` (`admin`) và mật khẩu vừa đặt ở bước trước.
<img width="1919" height="1023" alt="Screenshot 2026-05-11 213556" src="https://github.com/user-attachments/assets/7b893bc5-aaab-48e1-88d4-4044c4428420" />

---

## Viết 2 bài báo theo yêu cầu

Trong giao diện quản trị (`Dashboard`), nhìn cột bên trái, chọn `Bài viết (Posts)` -> `Viết bài mới (Add New)`. 
<img width="1919" height="1015" alt="Screenshot 2026-05-11 213757" src="https://github.com/user-attachments/assets/6952ce8b-46ee-4c54-806d-40344fd269aa" />

Viết 2 bài viết giới thiệu bản thân và giới thiệu ngành học yêu thích (có thể chèn ảnh, video, âm thanh).
<img width="1919" height="1020" alt="Screenshot 2026-05-11 215605" src="https://github.com/user-attachments/assets/5f923bc7-29dd-442d-8d0c-ae6aa9bc37b5" />
<img width="1919" height="1025" alt="Screenshot 2026-05-11 221351" src="https://github.com/user-attachments/assets/e8e555dc-1201-44b6-86a4-ee9cf88acf8a" />

# Cuối cùng, để mở online (public) website từ máy ảo Ubuntu ra Internet bằng Cloudflare Tunnel, thực hiện một chuỗi các lệnh theo trình tự "đào hầm" như sau:

## 1. Lệnh tạo đường hầm (Khởi tạo)

Đây là lệnh để đăng ký một đường hầm mới với server của Cloudflare.

```bash
cloudflared tunnel create wordpress_tunnel
```
<img width="1914" height="224" alt="Screenshot 2026-05-11 221748" src="https://github.com/user-attachments/assets/27e9a2d1-50e8-44c4-bba8-24a492ead319" />

Mục đích: Tạo ra một định danh (ID) duy nhất cho đường hầm trên hệ thống Cloudflare.

---

## 2. Lệnh gắn tên miền (Trỏ DNS)

Lệnh này giúp kết nối cái sub-domain muốn (`wp.sunning.id.vn`) với cái đường hầm vừa tạo.

```bash
cloudflared tunnel route dns wordpress_tunnel wp.sunning.id.vn
```
<img width="1919" height="156" alt="Screenshot 2026-05-11 221809" src="https://github.com/user-attachments/assets/3856816a-4810-43da-94fe-de9a749ae9f3" />

Mục đích: Để khi bất kỳ ai gõ `wp.sunning.id.vn` trên trình duyệt, Cloudflare sẽ biết đường dẫn tín hiệu vào cái hầm `wordpress_tunnel` đang nằm trong máy ảo.

---

## 3. Lệnh chạy đường hầm (Kích hoạt online)

Đây là lệnh quan trọng nhất, dùng để "thông" tín hiệu từ máy ảo ra ngoài.Sử dụng 2 phiên bản của lệnh này:

### Phiên bản cơ bản (Lúc đầu):

```bash
cloudflared tunnel run --url http://localhost:8001 wordpress_tunnel
```
<img width="1919" height="894" alt="Screenshot 2026-05-11 221848" src="https://github.com/user-attachments/assets/cf158f3b-87d3-4293-b01d-2844eff276d4" />

Mục đích: Mở cổng hầm, đón khách từ internet vào và đẩy vào cổng `8001` (nơi WordPress đang chạy).

### Phiên bản nâng cao (Để sửa lỗi Redirect/HTTPS nãy giờ):

```bash
cloudflared tunnel run --url http://localhost:8001 --http-header "X-Forwarded-Proto:https" wordpress_tunnel
```

Mục đích: Giống lệnh trên nhưng thêm cái `"nhãn"` HTTPS để báo cho WordPress biết là khách đang vào bằng kết nối bảo mật, tránh lỗi quay vòng mòng.

---

## 4. Các lệnh hỗ trợ xử lý lỗi "Online"

Trong quá trình làm, do WordPress `"cứng đầu"` không chịu nhận tên miền mới,Phải dùng thêm lệnh này để ép nó chạy online chuẩn:

```bash
docker compose exec wordpress sed -i "3i define('WP_HOME', 'https://wp.sunning.id.vn'); define('WP_SITEURL', 'https://wp.sunning.id.vn');" wp-config.php
```

Mục đích: Ép WordPress phải lấy tên miền online làm địa chỉ gốc, không được tự ý nhảy về IP nội bộ hay chèn thêm cổng `:8001`.

---

## Lưu ý:

Để web luôn online, phải giữ nguyên cái tab PowerShell đang chạy lệnh `cloudflared tunnel run`. Nếu tắt tab đó hoặc nhấn `Ctrl + C`, cái hầm sẽ bị sập và web sẽ báo lỗi `"Không thể truy cập trang web này"` ngay lập tức!

# KẾT QUẢ
<p align="center">
  <img width="49%" alt="Screenshot 2026-05-11 223436" src="https://github.com/user-attachments/assets/f718206b-3e08-4370-8066-1507737fdb02" />
  <img width="49%" alt="Screenshot 2026-05-11 223459" src="https://github.com/user-attachments/assets/2ac2a832-2de3-4d1c-892a-c99de46aca47" />
</p>

# NHẬN XÉT THỰC TẾ VIỆC TRIỂN KHAI WORDPRESS QUA DOCKER

## 1. Về công sức triển khai:

- Tốn nhiều công sức cấu hình: Không tốn sức viết code nhưng cực kỳ vất vả ở khâu xử lý lỗi hệ thống (Troubleshooting). Phải giải quyết các lỗi mạng khi tải Image (Timeout), máy ảo đổi IP đột ngột và lỗi vòng lặp chuyển hướng (Redirect Loop) khi chạy Cloudflare Tunnel.

- Xử lý thủ công: Phải dùng lệnh `sed` can thiệp trực tiếp vào file cấu hình `wp-config.php` để ép tên miền chuẩn và xử lý lỗi hiển thị Media (`Mixed Content`).

## 2. Độ dễ và khó sử dụng:

- Khó (Kỹ thuật): Đòi hỏi kiến thức vững về Linux, Docker và Network để cấu hình Container và DNS. Việc sửa lỗi rất phức tạp vì file nằm sâu trong bộ nhớ Container.

- Dễ (Nội dung): Khi hệ thống đã chạy, việc quản trị bài viết rất nhàn. Giao diện kéo thả cho phép chèn ảnh, âm thanh, video giới thiệu ngành KTMT – TNUT cực nhanh mà không cần code.

## 3. Tiêu tốn tài nguyên máy chủ:

- Ổ cứng: Rất tốn kém; bộ Image Docker và dữ liệu Media chiếm tới `7.2GB / 10GB` (hơn `70%`) dung lượng máy ảo ngay khi vừa cài xong.

- RAM/CPU: WordPress và MariaDB ngốn tài nguyên lớn để duy trì các tiến trình PHP và truy vấn Database, gây tải nặng khi thực hiện upload file media.
<img width="922" height="187" alt="image" src="https://github.com/user-attachments/assets/8193048d-5e54-429d-baab-b23091c2479f" />

## => Kết luận:

WordPress + Docker giúp tạo web nhanh, chuyên nghiệp nhưng yêu cầu kỹ năng Quản trị hệ thống (`SysAdmin`) tốt để vận hành và xử lý các xung đột phát sinh.
