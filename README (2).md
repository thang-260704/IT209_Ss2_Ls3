# Bài 3: Cài đặt Nginx và cấu hình Website tĩnh cơ bản

## Mục tiêu
- Cài đặt Nginx trên Ubuntu Server bằng `apt`.
- Tạo Server Block riêng lắng nghe cổng 80, phục vụ trang HTML tĩnh của PTIT.

## Môi trường

| Thông tin | Giá trị |
|---|---|
| Máy chủ | VPS AZVPS – Ubuntu 22.04.5 LTS |
| IPv4 | `160.187.229.88` |
| User thao tác | `devops` (dùng `sudo`) |
| Thư mục web | `/var/www/ptit-web/html/` |
| Server Block | `/etc/nginx/sites-available/ptit-web.conf` |

## File nộp bài

| File | Vị trí trên máy chủ |
|---|---|
| [`ptit-web.conf`](ptit-web.conf) | `/etc/nginx/sites-available/ptit-web.conf` |
| [`index.html`](index.html) | `/var/www/ptit-web/html/index.html` |

---

## Các bước thực hiện

Các lệnh dưới đây chạy trên máy chủ sau khi đã đăng nhập bằng `ssh -i %USERPROFILE%\.ssh\id_ed25519 devops@160.187.229.88`.

### Bước 1 – Cài đặt Nginx

```bash
sudo apt update
sudo apt install nginx -y
nginx -v
```

### Bước 2 – Tạo thư mục mã nguồn và file `index.html`

```bash
sudo mkdir -p /var/www/ptit-web/html
sudo nano /var/www/ptit-web/html/index.html   # nội dung xem file index.html
```

Trang hiển thị dòng chữ: **"Welcome to PTIT DevOps Course - Session 02"**.

### Bước 3 – Tạo Server Block `ptit-web.conf`

```bash
sudo nano /etc/nginx/sites-available/ptit-web.conf   # nội dung xem file ptit-web.conf
```

Ý nghĩa các dòng chính trong cấu hình:
- `listen 80 default_server;` lắng nghe cổng HTTP 80 và nhận mọi request truy cập bằng IP.
- `root /var/www/ptit-web/html;` trỏ tới thư mục chứa mã nguồn tĩnh.
- `index index.html;` chỉ định file mặc định được trả về.
- `try_files $uri $uri/ =404;` trả lỗi 404 nếu không tìm thấy file được yêu cầu.

### Bước 4 – Kích hoạt Server Block và huỷ kích hoạt trang `default`

```bash
sudo ln -s /etc/nginx/sites-available/ptit-web.conf /etc/nginx/sites-enabled/
sudo rm /etc/nginx/sites-enabled/default
ls -l /etc/nginx/sites-enabled/
```

Lệnh `rm` chỉ xoá **symlink** trong `sites-enabled`. File gốc `/etc/nginx/sites-available/default` vẫn còn, nên có thể bật lại khi cần.

### Bước 5 – Kiểm tra cú pháp và reload Nginx

```bash
sudo nginx -t
sudo systemctl reload nginx
```

Kết quả thực tế:

```text
devops@wean2607:~$ ls -l /etc/nginx/sites-enabled/
total 0
lrwxrwxrwx 1 root root 40 Oct  1 21:22 ptit-web.conf -> /etc/nginx/sites-available/ptit-web.conf
devops@wean2607:~$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
devops@wean2607:~$ sudo systemctl reload nginx
devops@wean2607:~$
```

![nginx -t và reload](images/01-nginx-test.png)

### Bước 6 – Truy cập từ trình duyệt

Mở địa chỉ `http://160.187.229.88` trên trình duyệt của máy cá nhân.

![Trang web PTIT](images/02-browser.png)

## Kết quả

| Yêu cầu | Kết quả |
|---|---|
| Cài Nginx từ repository mặc định của Ubuntu | ✅ `apt install nginx` |
| Có `/var/www/ptit-web/html/index.html` với dòng chữ PTIT | ✅ |
| Có `ptit-web.conf`, lắng nghe cổng 80, root trỏ đúng thư mục | ✅ |
| Kích hoạt bằng symlink sang `sites-enabled` | ✅ |
| Huỷ kích hoạt trang `default` | ✅ |
| `nginx -t` báo `syntax is ok` | ✅ |
| Trình duyệt hiển thị trang PTIT | ✅ |
