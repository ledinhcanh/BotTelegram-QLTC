# 🤖 Bot Quản Lý Chi Tiêu Cá Nhân (Telegram + Google Sheets)

Bot Telegram giúp ghi chép thu nhập, chi tiêu hàng ngày và tự động lưu trữ, báo cáo thống kê trực tiếp trên Google Sheets.

## ✨ Tính Năng Nổi Bật

- **Ghi chép siêu tốc:** Hỗ trợ nhập liệu tự nhiên, không cần đúng cấu trúc cứng nhắc.
  - *Ví dụ:* `50k an phở` ➔ Bot hiểu là: Chi 50.000đ cho Ăn uống.
- **Nhận diện số tiền thông minh:** Hiểu được các cách viết tắt của người Việt.
  - `1tr5` ➔ 1.500.000
  - `1tr600` ➔ 1.600.000
  - `500k` ➔ 500.000
- **Dashboard Tự Động:** Dữ liệu tự động đổ vào Sheet, tự tạo Sheet mới theo tháng. Các chỉ số Tổng thu/chi/dư được tính tự động ngay trên đầu trang.
- **Tiện ích mở rộng:**
  - 🌤️ **Thời tiết:** Xem nhiệt độ, độ ẩm, UV và chất lượng không khí (AQI) tại Hải Phòng (hoặc địa điểm bạn cài đặt).
  - 🏆 **Giá vàng:** Cập nhật giá vàng SJC Mua vào/Bán ra mới nhất.
- **Báo cáo:** Xem báo cáo tổng quan hoặc chi tiết từng nhóm chi tiêu ngay trên Telegram (`/report`).
- **An toàn:** Chỉ những ID Telegram được cấp quyền (Admin) mới sử dụng được Bot.

---

## 🛠️ Hướng Dẫn Cài Đặt

### Bước 1: Chuẩn bị tài nguyên
1. **Telegram Bot:** Chat với [@BotFather](https://t.me/BotFather) để tạo bot mới và lấy **API Token**.
2. **Telegram ID:** Chat với [@userinfobot](https://t.me/userinfobot) để lấy **ID** (dãy số) của bạn.
3. **Google Sheet:** Tạo một file Google Sheet mới và copy **ID Sheet** từ thanh địa chỉ URL.
   > URL: `docs.google.com/spreadsheets/d/`**`ID_CUA_BAN_NAM_O_DAY`**`/edit`
4. **Weather API (Khuyên dùng):** Đăng ký tài khoản miễn phí tại [WeatherAPI.com](https://www.weatherapi.com/) để lấy Key riêng (tránh lỗi hết lượt dùng).

### Bước 2: Thiết lập Code
1. Mở Google Sheet của bạn.
2. Vào menu **Tiện ích mở rộng (Extensions)** > **Apps Script**.
3. Xóa code cũ, dán toàn bộ code Bot vào file `Code.gs`.
4. Cập nhật phần **CẤU HÌNH** ở đầu file:

```javascript
const TOKEN = "DÁN_TOKEN_BOT_CỦA_BẠN_VÀO_ĐÂY";
const SHEET_ID = "DÁN_ID_SHEET_CỦA_BẠN_VÀO_ĐÂY";
const ADMIN_IDS = ["ID_TELEGRAM_CỦA_BẠN"]; 
// Tìm hàm getWeather() để thay API Key thời tiết nếu có
````

### Bước 3: Triển khai (Deploy) - *Bước quan trọng nhất*

1.  Nhấn nút **Deploy (Triển khai)** màu xanh \> **New Deployment (Tùy chọn triển khai mới)**.
2.  Bấm vào biểu tượng bánh răng chọn loại: **Web App (Ứng dụng web)**.
3.  Cấu hình y hệt như sau:
      - **Description:** Bot Finance
      - **Execute as (Thực thi dưới dạng):** Me (Tôi)
      - **Who has access (Ai có quyền truy cập):** **Anyone (Bất kỳ ai)** *(Bắt buộc để Telegram gửi được dữ liệu về)*
4.  Nhấn **Deploy**, cấp quyền truy cập Bot và **Copy URL Web App** hiện ra.

### Bước 4: Kích hoạt Webhook & Menu

1.  Quay lại trang code, tìm hàm `setWebhook()`.
2.  Dán URL Web App vừa copy vào biến `var webAppUrl = '...'`.
3.  Chọn hàm `setWebhook` trên thanh công cụ và nhấn **Run (Chạy)**.
4.  Chọn tiếp hàm `setupBotMenu` và nhấn **Run (Chạy)** để hiển thị menu lệnh trên Telegram.

-----

## 📖 Hướng Dẫn Sử Dụng

### 1\. Cú pháp nhập liệu

Bot được thiết kế để bạn nhập thế nào cũng hiểu, miễn là có **Số tiền** và **Từ khóa nhóm**.

| Loại | Cách nhập | Giải thích |
| :--- | :--- | :--- |
| **Cách 1** | `50k an phở bò` | Số tiền trước, từ khóa sau (Phổ biến nhất) |
| **Cách 2** | `an 50k phở bò` | Từ khóa trước, số tiền sau |
| **Cách 3** | `/chi 50k an` | Dùng lệnh truyền thống |
| **Thu nhập** | `/thu 10tr lương` | Ghi nhận thu nhập (Lương, thưởng...) |

### 2\. Quy tắc viết số tiền

  - **k** = nghìn (000), **tr** = triệu (000.000).
  - `50` = 50.000 (Mặc định hiểu là nghìn).
  - `500k` = 500.000.
  - `1tr5` = 1.500.000.
  - `1tr600` = 1.600.000.

### 3\. Danh sách từ khóa viết tắt (Shortcut)

| Từ khóa (Viết tắt) | Nhóm chi tiêu |
| :--- | :--- |
| `an`, `uong` | 🍜 Ăn uống |
| `xe`, `sua` | 🛵 Xe cộ |
| `xang`, `dau` | ⛽ Đổ xăng |
| `mua`, `sam` | 🛍️ Mua sắm |
| `dien`, `nuoc`, `nha` | 🏠 Điện nước |
| `ck`, `bank`, `gui` | 💸 Chuyển khoản |
| `con`, `hoc` | 👶 Con cái |
| `thuoc`, `kham` | 🏥 Thuốc men |
| `dam`, `tham` | 🎉 Đám xá |

### 4\. Các lệnh tiện ích khác

  - `/report`: Xem tổng kết thu chi tháng hiện tại.
  - `/report 10/2025`: Xem lại báo cáo tháng 10/2025.
  - `/report an`: Xem chi tiết nhóm "Ăn uống" tháng này.
  - `/undo`: Xóa giao dịch vừa nhập sai gần nhất (Lưu ý: Chỉ xóa được 1 dòng cuối cùng vừa nhập).
  - `/thoitiet`: Xem thời tiết, bụi mịn PM2.5, cảnh báo UV.
  - `/giavang`: Xem giá vàng SJC.

-----

## 📊 Cấu Trúc Sheet Dữ Liệu

Bot sử dụng layout tối ưu hóa hiển thị:

  - **Dòng 1-4 (Dashboard):** Hiển thị Tổng Thu, Tổng Chi, Số Dư và Top chi tiêu. Đừng xóa khu vực này.
  - **Dòng 15:** Tiêu đề bảng dữ liệu.
  - **Dòng 16 trở đi:** Dữ liệu chi tiết từng giao dịch sẽ được ghi vào đây.

-----

## ❓ Xử Lý Lỗi Thường Gặp

**Q: Bot không phản hồi tin nhắn?**

> A: Kiểm tra lại bước `setWebhook`. Hãy chắc chắn bạn đã Deploy Web App với quyền "Anyone" (Bất kỳ ai). Ngoài ra, kiểm tra lại `ADMIN_IDS` xem đã đúng ID của bạn chưa.

**Q: Thời gian trên Sheet bị sai giờ?**

> A: Vào Google Sheet \> **File (Tệp)** \> **Settings (Cài đặt)** \> Chọn múi giờ **(GMT+07:00) Bangkok/Hanoi**.

**Q: Lệnh `/undo` không hoạt động?**

> A: Lệnh Undo chỉ có tác dụng ngay sau khi bạn nhập sai. Nếu bạn đã nhập thêm giao dịch khác, bạn không thể Undo giao dịch cũ hơn được nữa.

-----