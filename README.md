# Grid Stop Trading V1 – Bản Lite

EA lưới **chỉ lệnh Stop** (Buy Stop / Sell Stop) trên MetaTrader 5 – bản **ngắn gọn, tối ưu**: lot cố định mọi bậc, **không dùng TP từng lệnh**, **gồng lãi từng lệnh luôn bật**, gồng lãi tổng **chỉ theo lệnh mở**, dừng EA **chỉ theo âm USD** (không có SL %).

**File EA:** `GridStopTradingV1_Lite.mq5`

---

## Tổng quan

| Nội dung | Mô tả |
|----------|--------|
| **Đường gốc** | Giá Bid khi EA khởi động (hoặc sau mỗi lần reset). |
| **Lưới** | Khoảng cách **luôn cố định**: bậc n cách gốc = n × X pips. |
| **Lệnh Stop** | Luôn bật; mỗi level tối đa 1 lệnh chờ (Buy Stop trên gốc, Sell Stop dưới gốc). |
| **Lot** | Cố định mọi bậc (một input duy nhất). |
| **Take Profit** | Không dùng TP cho từng lệnh; lệnh đóng nhờ **gồng lãi từng lệnh** (SL) hoặc thủ công. |
| **Gồng lãi từng lệnh** | Luôn bật: giá cách entry X pips → đặt SL hòa vốn, sau đó dịch SL theo step. |
| **Tự động đặt lại lệnh chờ** | Khi lệnh tại một level đóng (bất kỳ lý do) → EA tự đặt lại lệnh chờ tại level đó. |

---

## Thứ tự input trong EA

Các nhóm input hiển thị theo thứ tự:

1. **Cài đặt chung** → **Cài đặt lưới** → **Cài đặt lệnh Stop** → **Trading Stop Step Tổng** → **Cân bằng lệnh** → **Giờ hoạt động** → **Dừng EA**.

---

## 1. Cài đặt chung

| Input trong EA | Mô tả |
|----------------|--------|
| Magic Number | Phân biệt lệnh do EA với lệnh tay/EA khác. |
| Comment | Comment trên lệnh (EA tự thêm " B"). |
| Gửi push notification khi EA reset/dừng | Bật: gửi thông báo điện thoại khi EA reset hoặc dừng. |

---

## 2. Cài đặt lưới

| Input trong EA | Mô tả |
|----------------|--------|
| Khoảng cách lưới cố định (pips) | Bậc 1 = x pips, bậc 2 = 2x, bậc 3 = 3x... (luôn cố định, không đổi chế độ). |
| Số bậc lưới tối đa mỗi chiều | Số level mỗi phía (1–100). VD 10 = 10 level trên gốc + 10 level dưới gốc. |

---

## 3. Cài đặt lệnh Stop

| Input trong EA | Mô tả |
|----------------|--------|
| Lot cố định cho mọi bậc | Khối lượng cho tất cả các lệnh Stop. |
| Gồng lãi từng lệnh: khi giá cách entry X pips | Khi giá cách giá vào lệnh X pips → đặt SL hòa vốn (break-even). |
| Mỗi X pips giá đi thêm → dịch SL | Bước dịch SL theo giá (gồng lãi từng lệnh). |

**Lưu ý:** Gồng lãi từng lệnh luôn bật; chỉ chỉnh hai tham số khoảng cách và step. Không có input bật/tắt.

---

## 4. Trading Stop Step Tổng (gồng lãi theo lệnh mở)

Khi **lãi lệnh đang mở** đạt ngưỡng → EA xóa lệnh chờ gần giá, đặt SL tại điểm A cho lệnh cùng hướng, đóng lệnh ngược chiều, rồi gồng lãi (dịch SL theo step). **Bản Lite chỉ dùng ngưỡng lệnh mở** (không có ngưỡng phiên).

| Input trong EA | Mô tả |
|----------------|--------|
| Bật | Bật/tắt gồng lãi tổng. |
| Lãi lệnh đang mở ≥ X USD | Ngưỡng kích hoạt (0 = tắt). |
| Hủy gồng nếu lãi lệnh mở < X USD | Ngưỡng hủy gồng (chỉ khi chưa kéo SL). |
| Điểm A (pips) | SL đặt cách lệnh dương gần nhất X pips. |
| Step (pips) | Giá đi thêm X pips → dịch SL. |
| Khi giá chạm SL | **Dừng EA** hoặc **Reset EA** (đóng hết, đặt gốc mới). |

---

## 5. Cân bằng lệnh

Khi **cả hai** điều kiện đều đạt → đóng hết lệnh mở + lệnh chờ → đặt đường gốc mới tại giá hiện tại → tiếp tục đặt lệnh. Ưu tiên kiểm tra trước Trading Stop Step Tổng.

| Input trong EA | Mô tả |
|----------------|--------|
| Bật | Bật/tắt chế độ cân bằng lệnh. |
| Điều kiện 1: Tổng lot mở ≥ X | Chỉ tính lệnh đang mở. 0 = bỏ qua. |
| Điều kiện 2: Lãi phiên ≥ X USD | 0 = bỏ qua. Cần ít nhất một điều kiện > 0. |

---

## 6. Giờ hoạt động

| Input trong EA | Mô tả |
|----------------|--------|
| Bật | EA chỉ đặt lệnh mới trong khung giờ; ngoài giờ vẫn quản lý lệnh đang mở. |
| Giờ/phút bắt đầu, kết thúc | Khung giờ được phép đặt lệnh mới (0–23 giờ, 0–59 phút). |

---

## 7. Dừng EA

Bản Lite có **hai** cơ chế (không có SL % lỗ):

### 7.1. Dừng EA theo tích lũy lãi

- **Tích lũy** = Số dư hiện tại − Số dư khi EA khởi động.
- Khi tích lũy ≥ ngưỡng (USD) → EA dừng (đóng hết, không đặt lệnh mới). Kiểm tra sau mỗi lần reset.
- Input: bật/tắt + ngưỡng (USD). Đặt ngưỡng = 0 để tắt.

### 7.2. Dừng/Reset EA theo SL (âm USD)

- Khi **lỗ phiên** (vốn khởi động − Equity) **≥ X USD** → dừng hoặc reset EA.
- **Vốn khởi động** = Equity khi bật EA hoặc khi EA tự khởi động lại sau reset.

| Input trong EA | Mô tả |
|----------------|--------|
| Bật | Bật/tắt chức năng. |
| Lỗ phiên (USD) | VD 100 = kích hoạt khi Equity ≤ vốn khởi động − 100. |
| Hành động khi kích hoạt | **Dừng EA**: đóng hết, EA dừng. **Reset EA**: đóng hết, đặt gốc mới, chạy tiếp. |

**Ví dụ:** Vốn khởi động = 10,000 USD, SL âm USD = 100 → kích hoạt khi Equity ≤ 9,900 USD.

---

## So với bản đầy đủ (NoPanel / Panel)

| Tính năng | Bản Lite | Bản đầy đủ |
|-----------|----------|------------|
| Loại lệnh | Chỉ Stop (lot cố định) | Stop A (gấp thếp) + Stop B |
| TP từng lệnh | Không | Có (pips cố định / theo lưới) |
| Gồng lãi từng lệnh | Luôn bật | Bật/tắt + tham số |
| Gồng lãi tổng | Chỉ theo lệnh mở | Theo lệnh mở / phiên / cả hai |
| Dừng EA theo SL % lỗ | Không | Có |
| Dừng EA theo âm USD | Có | Có |
| Khoảng cách lưới | Luôn cố định | Cố định / cấp số cộng |
| Tự động đặt lại lệnh chờ | Luôn bật, không giới hạn | Bật + giới hạn X lệnh cùng chiều |

---

## Yêu cầu

- MetaTrader 5  
- Tài khoản cho phép giao dịch tự động  
- Push notification (nếu dùng): **Tools → Options → Notifications → Enable push notifications**  

---

## Cài đặt

1. Copy `GridStopTradingV1_Lite.mq5` vào thư mục `MQL5/Experts/`.
2. Mở MetaEditor → biên dịch file (F7).
3. Gắn EA lên chart, bật **Allow Algo Trading**, cấu hình input theo nhu cầu.

Bản Lite không có panel; code gọn, phù hợp khi chỉ cần lưới Stop đơn giản, gồng lãi từng lệnh và dừng theo âm USD.
