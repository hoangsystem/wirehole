# 🌐 WireHole UI - Private VPN + Ad-block DNS in Docker

WireHole UI là một dự án mã nguồn mở triển khai hệ thống VPN bảo mật cá nhân bằng [WireGuard](https://www.wireguard.com/), đồng thời tích hợp khả năng chặn quảng cáo nhờ [Pi-hole](https://pi-hole.net/) và [Unbound](https://nlnetlabs.nl/projects/unbound/about/). Tất cả chạy trong các container được quản lý bởi Docker Compose, đơn giản hóa việc cài đặt và vận hành.

## 🎯 Tính năng chính

- 🔐 **VPN cá nhân** với `wg-easy` giúp tạo và quản lý cấu hình WireGuard qua giao diện web.
- 🚫 **Chặn quảng cáo toàn hệ thống** bằng `Pi-hole`.
- 🛡️ **DNS Resolver riêng tư** với `Unbound`, tránh rò rỉ DNS và không phụ thuộc bên thứ ba.
- 📊 Giao diện trực quan để theo dõi kết nối và hoạt động chặn quảng cáo.
- 🐳 Triển khai đơn giản bằng `Docker Compose`.

---

## 📦 Yêu cầu hệ thống

- Hệ điều hành: Linux (Ubuntu/Debian/CentOS...), đã cài đặt Docker và Docker Compose.
- Port mở: `51820/UDP`, `51821/TCP`, `80/TCP`, `443/TCP`.
- Quyền root hoặc user thuộc nhóm `docker`.

---

## 🚀 Cài đặt

### 1. Clone dự án

```bash
git clone https://github.com/hoangsystem/wirehole.git
cd wirehole-ui
```

### 2. Cấu hình

- Mở và chỉnh sửa `docker-compose.yml`
- Thay đổi thông tin sau theo nhu cầu:

```yaml
environment:
  - WG_HOST=my.ddns.net       # Địa chỉ IP public hoặc tên miền DDNS của VPS
  - PASSWORD_HASH=BCRYPT_HASH   # Mật khẩu đăng nhập UI wg-easy được băm bởi thuật toán Bcrypt
```

> ⚠️ Nếu bạn dùng Cloudflare DNS, hãy chắc chắn rằng DNS proxy đang **tắt** cho subdomain VPN.

### 3. Tạo file cấu hình cho Unbound (nếu chưa có)

Đường dẫn: `./unbound/unbound.conf`

Bạn có thể sử dụng mẫu cấu hình tại:  
https://github.com/MatthewVance/unbound-docker/blob/master/unbound.conf

### 4. Khởi động hệ thống

```bash
docker-compose up -d
```

Đợi khoảng 1-2 phút, sau đó truy cập:

- Giao diện quản lý wg-easy: http://<IP-VPS>:51821  
- Giao diện quản lý Pi-hole: http://<IP-VPS> (hoặc qua port 80)

---

## 🔧 Quản lý người dùng WireGuard

Truy cập http://<IP-VPS>:51821 và đăng nhập bằng mật khẩu đã đặt.  
Tại đây bạn có thể:

- Tạo cấu hình mới cho client
- Xem QR code
- Kích hoạt/tắt user
- Tải file `.conf` để sử dụng trên máy tính/điện thoại

---

## 🔍 Kiểm tra DNS đang đi qua Pi-hole & Unbound

Từ client Windows/Linux/macOS đã kết nối VPN:

```bash
nslookup google.com
```

Kết quả sẽ hiển thị server DNS là `10.2.0.100` (Pi-hole).  
Tại web UI của Pi-hole bạn có thể xác minh truy vấn được gửi về và truy ngược đến Unbound.

---

## 🗑️ Gỡ cài đặt

Để dừng toàn bộ container và xóa:

```bash
docker-compose down
```

Nếu bạn muốn xóa toàn bộ volume và dữ liệu:

```bash
docker-compose down -v
```

Xóa luôn image (tuỳ chọn):

```bash
docker image rm pihole/pihole klutchell/unbound ghcr.io/wg-easy/wg-easy
```

---

## 🧱 Cấu trúc mạng nội bộ

```
[Client] <== WireGuard ==> [wg-easy] --> [Pi-hole] --> [Unbound] --> [Internet]
```

Mỗi dịch vụ đều chạy trong container riêng và kết nối qua một mạng Docker ảo `private_network`.

---

## 📝 Ghi chú bảo mật

- **Không sử dụng mật khẩu mặc định.**
- **Chỉ mở port cần thiết ra internet.**
- **Cập nhật image thường xuyên (`latest`) để tránh lỗ hổng bảo mật.**

---

## 🧑‍💻 Tác giả & Giấy phép

- Tác giả: [Hoàng](https://github.com/hoangsystem)
- Giấy phép: MIT License
- Dựa trên các dự án mã nguồn mở tuyệt vời:
  - [wg-easy](https://github.com/WeeJeWel/wg-easy)
  - [pi-hole](https://github.com/pi-hole/docker-pi-hole)
  - [unbound](https://github.com/MatthewVance/unbound-docker)

---

## 💬 Hỗ trợ

Nếu bạn gặp lỗi, vui lòng mở issue tại repo hoặc liên hệ qua Telegram.
