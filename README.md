# Grid Stop Trading V1 – Bản Lite

**Grid Stop Trading** · Phiên bản Lite 1.0

EA lưới **chỉ lệnh Stop** (Buy Stop / Sell Stop) trên MetaTrader 5 – bản **ngắn gọn, tối ưu**: lot cố định mọi bậc, **không dùng TP từng lệnh**, **gồng lãi từng lệnh luôn bật**, gồng lãi tổng **chỉ theo lệnh mở**, **dừng/Reset theo âm USD** (không có SL %).

**File EA:** `GridStopTradingV1_Lite.mq5`

---

## Giới thiệu

Bản **Lite** tập trung vào:

- **Chỉ lệnh Stop (lot cố định):** Mọi bậc lưới dùng cùng một lot; không TP từng lệnh, đóng lệnh nhờ gồng lãi từng lệnh hoặc khi reset/dừng.
- **Gồng lãi tổng theo lệnh mở:** Khi tổng lãi lệnh đang mở đạt ngưỡng → EA đặt SL điểm A, gồng theo step; khi giá chạm SL → Dừng EA hoặc Reset EA (đặt gốc mới).
- **Dừng/Reset theo âm USD:** Khi lỗ phiên (so vốn khởi động) ≥ X USD → Dừng EA hoặc Reset EA. Không dùng SL % lỗ.

Phù hợp khi bạn chỉ cần lưới Stop đơn giản, gồng lãi từng lệnh và bảo vệ tài khoản theo âm USD.

---

## Thông số mặc định cho BTC/USD

Gợi ý cài đặt khi chạy **BTC/USD** với **vốn 50.000 USD** hoặc **50.000 cent** (tài khoản cent). Có thể bật **Đánh theo % tài khoản** để lot và các ngưỡng USD tự scale theo vốn.

| Nhóm | Input | Gợi ý (50.000 USD / 50.000 cent) |
|------|--------|-----------------------------------|
| **Cài đặt chung** | Gửi push notification | true |
| | Đánh theo % tài khoản | true |
| | Tỷ lệ tăng theo vốn (%) | 100 |
| | Giới hạn tăng lot/hàm số (%) | 0 (= trần 10.000%) |
| **Lưới** | Khoảng cách lưới (pips) | 1500 (hoặc chỉnh theo biên độ BTC) |
| | Số bậc lưới tối đa mỗi chiều | 10 |
| **Lệnh Stop** | Lot cố định | 1 (hoặc 0,01–0,1 tùy broker; cent: có thể 0,1–1) |
| | Gồng lãi từng lệnh: cách entry (pips) | 300 |
| | Mỗi X pips dịch SL | 100 |
| **Trading Stop Step Tổng** | Bật | true |
| | Lãi lệnh mở ≥ X USD | 120 |
| | Hủy gồng nếu lãi lệnh mở < X USD | 100 |
| | Điểm A (pips) | 2000 |
| | Step (pips) | 1000 |
| | Khi giá chạm SL | Reset EA |
| **Cân bằng lệnh** | Bật | true |
| | ĐK lưới ≥ X | 3 |
| | Lãi phiên ≥ X USD | 150 |
| **Dừng EA** | Dừng/Reset khi phiên lỗ | true |
| | Lỗ phiên (USD) | 3000 (50k USD) / 3000 cent (50k cent) |
| | Khi kích hoạt SL âm USD | Reset EA |

**Lưu ý:** Với tài khoản **cent**, số dư 50.000 cent = 500 USD quy đổi; các ngưỡng USD trong EA nhập theo đơn vị tài khoản (cent thì 3000 = 3000 cent). Điều chỉnh khoảng cách lưới (pips) và lot theo quy định broker cho BTC/USD và khẩu vị rủi ro.

---

## Mục lục

1. [Giới thiệu](#giới-thiệu)
2. [Thông số mặc định cho BTC/USD](#thông-số-mặc-định-cho-btcusd)
3. [Tổng quan](#tổng-quan)
4. [Thứ tự input trong EA](#thứ-tự-input-trong-ea)
5. [Cài đặt chung](#1-cài-đặt-chung)
6. [Cài đặt lưới](#2-cài-đặt-lưới)
7. [Cài đặt lệnh Stop](#3-cài-đặt-lệnh-stop)
8. [Trading Stop Step Tổng](#4-trading-stop-step-tổng-gồng-lãi-theo-lệnh-mở)
9. [Cân bằng lệnh](#5-cân-bằng-lệnh)
10. [Giờ hoạt động](#6-giờ-hoạt-động)
11. [Dừng EA](#7-dừng-ea)
12. [Chế độ Đánh theo % tài khoản](#chế-độ-đánh-theo--tài-khoản) — gồm [Giải thích hai thông số: Tỷ lệ và Giới hạn](#giải-thích-hai-thông-số-tỷ-lệ-và-giới-hạn)
13. [Luồng xử lý và ưu tiên](#luồng-xử-lý-và-ưu-tiên)
14. [So với bản đầy đủ](#so-với-bản-đầy-đủ)
15. [Yêu cầu và cài đặt](#yêu-cầu-và-cài-đặt) — gồm [Push notification](#push-notification-khi-ea-resetdừng), [Giá trị mặc định trong EA](#giá-trị-mặc-định-trong-ea), [Gợi ý sử dụng](#gợi-ý-sử-dụng)

---

## Tổng quan

| Nội dung | Mô tả |
|----------|--------|
| **Đường gốc** | Giá Bid tại thời điểm EA khởi động hoặc sau mỗi lần reset. Tất cả mức lưới tính từ đường gốc. |
| **Lưới** | Khoảng cách **cố định**: bậc 1 = X pips, bậc 2 = 2X, bậc 3 = 3X... (n × X pips). Không có chế độ cấp số cộng. |
| **Lệnh Stop** | Chỉ dùng Buy Stop (phía trên gốc) và Sell Stop (phía dưới gốc). Mỗi bậc lưới tối đa 1 lệnh chờ mỗi hướng. |
| **Lot** | Một giá trị lot cố định cho mọi bậc. Có thể scale theo % tài khoản nếu bật chế độ tương ứng. |
| **Take Profit từng lệnh** | Không dùng. Lệnh đóng nhờ **gồng lãi từng lệnh** (SL trailing) hoặc đóng thủ công / khi reset. |
| **Gồng lãi từng lệnh** | Luôn bật: khi giá cách entry X pips → đặt SL hòa vốn (break-even), sau đó mỗi X pips giá đi thêm thì dịch SL theo step. |
| **Tự động đặt lại lệnh chờ** | Khi lệnh tại một level đóng (TP, SL, SO hoặc thủ công) → EA tự đặt lại lệnh chờ tại level đó. |
| **Gồng lãi tổng** | Chỉ theo **lệnh mở** (không có ngưỡng phiên). Khi lãi lệnh mở đạt ngưỡng → xóa lệnh chờ gần giá, đặt SL điểm A, gồng theo step. |
| **Dừng EA** | Theo tích lũy lãi (Balance) hoặc theo **âm USD** (lỗ phiên so vốn khởi động). Không có SL % lỗ. |

---

## Thứ tự input trong EA

Các nhóm input hiển thị theo thứ tự:

1. **Cài đặt chung** → **Cài đặt lưới** → **Cài đặt lệnh Stop** → **Trading Stop Step Tổng** → **Cân bằng lệnh** → **Giờ hoạt động** → **Dừng EA**.

---

## 1. Cài đặt chung

| Input trong EA | Kiểu | Mô tả chi tiết |
|----------------|------|----------------|
| **Magic Number** | int | Dùng để phân biệt lệnh do EA với lệnh tay hoặc EA khác. Mỗi EA/symbol nên dùng magic riêng. |
| **Comment** | string | Comment trên lệnh; EA tự thêm hậu tố `" B"` (ví dụ: "Grid Stop V1 B"). |
| **Gửi push notification khi EA reset/dừng** | bool | Bật: gửi thông báo đến điện thoại khi EA reset hoặc dừng (cần bật push trong MT5: Tools → Options → Notifications). |
| **Đánh theo % tài khoản** | bool | **Bật:** Khi **thêm EA vào biểu đồ** lấy vốn lúc đó làm **vốn gốc**. Mỗi lần EA reset so sánh vốn tăng/giảm bao nhiêu % so vốn gốc → dùng % đó để tính lot và 4 ngưỡng USD. **Tắt:** Luôn dùng đúng giá trị input. |
| **Tỷ lệ tăng theo vốn (%)** | int | Xem [Giải thích hai thông số](#giải-thích-hai-thông-số-tỷ-lệ-và-giới-hạn) bên dưới. **Giới hạn cao nhất 100%** — cài hơn cũng chỉ 100%. |
| **Giới hạn tăng lot/hàm số (%)** | double | Xem [Giải thích hai thông số](#giải-thích-hai-thông-số-tỷ-lệ-và-giới-hạn) bên dưới. **Tối đa 10.000%** — cài 0 hoặc >10.000 đều chỉ 10.000%. |

---

## 2. Cài đặt lưới

| Input trong EA | Kiểu | Mô tả chi tiết |
|----------------|------|----------------|
| **Khoảng cách lưới cố định (pips)** | double | Một bậc cách gốc = X pips; bậc n = n × X pips. Ví dụ 20 → bậc 1 = 20 pips, bậc 2 = 40 pips, bậc 3 = 60 pips. |
| **Số bậc lưới tối đa mỗi chiều** | int (1–100) | Số level mỗi phía đường gốc. VD 10 = 10 level trên gốc (Buy Stop) + 10 level dưới gốc (Sell Stop). Tổng tối đa 20 level đặt lệnh. |

---

## 3. Cài đặt lệnh Stop

| Input trong EA | Kiểu | Mô tả chi tiết |
|----------------|------|----------------|
| **Lot cố định cho mọi bậc** | double | Khối lượng cho tất cả lệnh Stop (mọi level). Khi bật **Đánh theo % tài khoản**, giá trị hiệu dụng = input × hệ số (hệ số tính từ vốn gốc và tỷ lệ / giới hạn). |
| **Gồng lãi từng lệnh: khi giá cách entry X pips** | double | Khi giá cách giá vào lệnh X pips (theo hướng có lợi) → EA đặt SL tại hòa vốn (entry). |
| **Mỗi X pips giá đi thêm → dịch SL** | double | Sau khi SL đã ở hòa vốn, mỗi khi giá đi thêm X pips theo hướng có lợi → dịch SL lên/xuống X pips. |

**Lưu ý:** Gồng lãi từng lệnh **luôn bật**; không có input bật/tắt, chỉ chỉnh hai tham số trên.

---

## 4. Trading Stop Step Tổng (gồng lãi theo lệnh mở)

Khi **lãi lệnh đang mở** (tổng profit + swap của tất cả position) đạt ngưỡng → EA thực hiện: xóa lệnh chờ gần giá hiện tại, xóa TP các lệnh mở, xóa lệnh chờ còn lại, chọn hướng (Buy/Sell theo giá so gốc), tính **điểm A** từ lệnh dương gần giá nhất trong hướng đó, rồi gồng lãi (khi giá chạm A ± step thì đặt SL tại A, đóng lệnh ngược chiều, sau đó dịch SL theo step). **Bản Lite chỉ dùng ngưỡng theo lệnh mở**, không có ngưỡng theo phiên.

| Input trong EA | Kiểu | Mô tả chi tiết |
|----------------|------|----------------|
| **Bật** | bool | Bật/tắt chức năng gồng lãi tổng theo lệnh mở. |
| **Lãi lệnh đang mở ≥ X USD** | double | Ngưỡng kích hoạt. Khi tổng lãi lệnh mở (floating) ≥ X USD → kích hoạt quy trình trên. 0 = tắt (coi như không dùng). Khi bật Đánh theo % tài khoản: dùng giá trị hiệu dụng = input × hệ số. |
| **Hủy gồng nếu lãi lệnh mở < X USD** | double | Chỉ áp dụng **trước khi** đã đặt SL (bước step đầu chưa xong). Nếu tổng lãi lệnh mở giảm xuống dưới X USD → hủy gồng lãi tổng, khôi phục TP/SL và lệnh chờ. Khi bật Đánh theo % tài khoản: dùng giá trị hiệu dụng. |
| **Điểm A (pips)** | double | SL đặt cách lệnh dương gần giá nhất (trong hướng được chọn) X pips. Buy: A = giá lệnh Buy dương gần nhất + X pips; Sell: A = giá lệnh Sell dương gần nhất − X pips. |
| **Step (pips)** | double | Giá đi thêm X pips (theo hướng có lợi) → dịch SL thêm 1 step. |
| **Khi giá chạm SL** | enum | **Dừng EA:** đóng hết, EA dừng hẳn. **Reset EA:** đóng hết, đặt đường gốc mới tại giá hiện tại, tiếp tục chạy. |

---

## 5. Cân bằng lệnh

Khi **cả hai** điều kiện đều đạt → đóng hết lệnh mở và lệnh chờ → đặt đường gốc mới tại giá hiện tại → tiếp tục đặt lệnh. Kiểm tra **ưu tiên trước** Trading Stop Step Tổng (nếu đạt cân bằng thì reset luôn, không kích hoạt gồng lãi tổng).

| Input trong EA | Kiểu | Mô tả chi tiết |
|----------------|------|----------------|
| **Bật** | bool | Bật/tắt chế độ cân bằng lệnh. |
| **Điều kiện 1 (ĐK lưới): Số bậc lưới có lệnh đang mở ≥ X** | int | Tính bằng **số bậc lưới** (1, 2, 3, ...) mà có **ít nhất một lệnh đang mở** (position) tại bậc đó. VD X = 3: kích hoạt khi có lệnh mở ở 3 bậc lưới trở lên (có thể 3 bậc trên, hoặc 2 trên + 1 dưới, v.v.). **0** = bỏ qua điều kiện này. |
| **Điều kiện 2: Lãi phiên ≥ X USD** | double | Lãi phiên = Equity hiện tại − Equity khi bắt đầu phiên (sau lần reset/khởi động gần nhất). Khi lãi phiên ≥ X USD thì đạt điều kiện 2. **0** = bỏ qua. Khi bật Đánh theo % tài khoản: dùng giá trị hiệu dụng. |

Cần **ít nhất một** điều kiện > 0; khi bật thì **cả hai** điều kiện (nếu được cấu hình) đều phải đạt thì mới reset.

---

## 6. Giờ hoạt động

| Input trong EA | Kiểu | Mô tả chi tiết |
|----------------|------|----------------|
| **Bật** | bool | Bật: EA chỉ **đặt lệnh mới** trong khung giờ; **ngoài giờ** vẫn quản lý lệnh đang mở (gồng lãi từng lệnh, Trading Stop, v.v.). Khi vào lại giờ và không còn lệnh → EA tự coi như khởi động lại (đặt gốc mới). |
| **Giờ/phút bắt đầu, kết thúc** | int | Khung giờ được phép đặt lệnh mới: từ StartHour:StartMinute đến EndHour:EndMinute (0–23 giờ, 0–59 phút). Qua nửa đêm: cấu hình End < Start. |

---

## 7. Dừng EA

Bản Lite có **hai** cơ chế dừng (không có SL % lỗ).

### 7.1. Dừng EA theo tích lũy lãi

- **Tích lũy** = Số dư hiện tại (Balance) − Số dư khi EA **khởi động lần đầu** (khi gắn EA vào chart).
- Khi tích lũy ≥ ngưỡng (USD) → EA **dừng** (đóng hết lệnh mở và lệnh chờ, không đặt lệnh mới). Kiểm tra sau mỗi lần reset (sau khi đóng hết).
- Input: bật/tắt + ngưỡng (USD). Đặt ngưỡng = **0** để tắt.

### 7.2. Dừng/Reset EA theo SL (âm USD)

- **Lỗ phiên** = Vốn khởi động (Equity khi bật EA hoặc khi EA tự khởi động lại sau reset) − Equity hiện tại.
- Khi lỗ phiên ≥ X USD → kích hoạt: **Dừng EA** (đóng hết, EA dừng) hoặc **Reset EA** (đóng hết, đặt gốc mới, chạy tiếp).
- **Vốn khởi động** = Equity tại thời điểm EA bật hoặc tại thời điểm sau mỗi lần reset (phiên mới).

| Input trong EA | Kiểu | Mô tả chi tiết |
|----------------|------|----------------|
| **Bật** | bool | Bật/tắt chức năng. |
| **Lỗ phiên (USD)** | double | VD 100 = kích hoạt khi Equity ≤ vốn khởi động − 100. Khi bật Đánh theo % tài khoản: dùng giá trị hiệu dụng = input × hệ số. |
| **Hành động khi kích hoạt** | enum | **Dừng EA** hoặc **Reset EA**. |

**Ví dụ:** Vốn khởi động = 10,000 USD, SL âm USD = 100 → kích hoạt khi Equity ≤ 9,900 USD.

---

## Chế độ "Đánh theo % tài khoản"

Khi bật **Đánh theo % tài khoản** (trong Cài đặt chung):

### Vốn gốc

- **Khi thêm EA vào biểu đồ:** Lấy **vốn lúc đó** (Equity) làm **vốn gốc** — lưu một lần duy nhất (GlobalVariable theo Magic). Lúc này lot và 4 ngưỡng USD dùng đúng giá trị input (hệ số = 1).
- **Vốn gốc không đổi** khi EA reset: sau mỗi lần reset (hoặc khởi động lại), EA so sánh **vốn hiện tại** với **vốn gốc** → vốn **tăng hay giảm bao nhiêu %** so với vốn gốc thì dùng đúng % đó để tính hệ số cho lot và 4 ngưỡng USD. Vốn gốc **không** được cập nhật khi reset.

### Giải thích hai thông số: Tỷ lệ và Giới hạn

#### 1. Tỷ lệ tăng theo vốn (%)

- **Ý nghĩa:** Khi vốn tăng, lot và 4 ngưỡng USD **chỉ được tăng theo một phần** của mức tăng vốn. Thông số này quy định phần đó là bao nhiêu %.
- **100%** = tăng **đủ theo vốn**: vốn tăng bao nhiêu % thì các hàm số tăng bấy nhiêu %. VD: vốn x2 (+100%) → lot và ngưỡng x2.
- **50%** = tăng **một nửa** so với vốn: vốn tăng 100% thì các hàm số chỉ tăng 50%. VD: vốn x2 → lot và ngưỡng x1,5.
- **30%** = vốn tăng 100% thì các hàm số chỉ tăng 30% (hệ số = 1,3).

**Giới hạn:** **Cao nhất là 100%.** Cài 150, 200 hay bất kỳ số nào lớn hơn 100 thì EA **cũng chỉ dùng 100%** — không bao giờ vượt 100%.

#### 2. Giới hạn tăng lot / các hàm số (%)

- **Ý nghĩa:** Dù vốn tăng rất lớn, lot và 4 ngưỡng USD **không được tăng quá** một mức % so với giá trị gốc. Đây là **trần cứng** (tối đa +X%).
- **100** = tối đa tăng 100% → hệ số tối đa = 2 (lot/ngưỡng tối đa x2).
- **0** = dùng **trần mặc định 10.000%** (hệ số tối đa = 101).
- **1–10.000** = dùng đúng số % bạn cài (VD 500 = tối đa +500%, hệ số tối đa = 6).

**Giới hạn:** **Cao nhất là 10.000%.** Cài **0** hoặc **lớn hơn 10.000** thì EA **cũng chỉ áp dụng tối đa 10.000%** — không bao giờ vượt 10.000%.

---

### Công thức

1. **Hệ số thô:** `scale_raw = Vốn hiện tại / Vốn gốc` — phản ánh vốn **tăng hay giảm bao nhiêu %** so với vốn gốc (vốn lúc thêm EA vào biểu đồ). Chưa có vốn gốc thì coi scale_raw = 1.
2. **Tỷ lệ tăng theo vốn (Rate %):**  
   `scale = 1 + (scale_raw − 1) × (Rate / 100)`  
   — Rate bị giới hạn **tối đa 100%** (cài >100 vẫn chỉ 100).
3. **Giới hạn tăng tối đa (MaxIncrease %):**  
   MaxIncrease ≤ 0 hoặc >10000 → dùng 10.000%; 1–10.000 → dùng đúng giá trị; >10.000 → chỉ 10.000%.  
   `scale = min(scale, 1 + MaxIncrease/100)` (trần scale tương ứng, tối đa 101 = +10.000%).

### Năm giá trị được scale

1. Lot cố định cho mọi bậc  
2. Lãi lệnh đang mở ≥ X USD (kích hoạt gồng lãi tổng)  
3. Hủy gồng nếu lãi lệnh mở < X USD  
4. Lãi phiên ≥ X USD (điều kiện 2 cân bằng lệnh)  
5. Lỗ phiên (USD) – kích hoạt SL âm USD  

Công thức chung: **Giá trị hiệu dụng = Input × scale** (scale đã qua tỷ lệ và giới hạn).

### Ví dụ

- **Vốn gốc = 10,000 USD.** Tỷ lệ = 100%, Giới hạn = 100%.
  - Sau reset vốn 11,000 USD → scale_raw 1,1 → scale 1,1 → lot 0,01 → 0,011.
  - Sau reset vốn 20,000 USD → scale_raw 2 → scale 2 (đạt giới hạn 100%) → lot 0,02.
  - Sau reset vốn 1,000,000 USD → scale_raw 100 → scale vẫn 2 (giới hạn) → lot 0,02.

- **Tỷ lệ = 50%:** Vốn 20,000 USD (tăng 100%) → scale_raw 2 → scale = 1 + (2−1)×0,5 = 1,5 → các hàm số chỉ tăng 50%.

- **Tỷ lệ = 30%:** Vốn tăng 100% → scale_raw 2 → scale = 1,3 → các hàm số chỉ tăng 30%.

---

## Luồng xử lý và ưu tiên

Trong mỗi tick (khi EA chưa dừng):

1. **Giờ hoạt động:** Nếu bật và ngoài giờ, không đặt lệnh mới; nếu vào lại giờ và đang tắt do giờ → tự coi như khởi động lại (gốc mới).
2. **SL âm USD:** Nếu bật và lỗ phiên ≥ ngưỡng → Dừng EA hoặc Reset EA (theo cấu hình), không làm bước sau.
3. **Cân bằng lệnh:** Nếu bật và đạt **cả hai** điều kiện (ĐK lưới + Lãi phiên nếu cấu hình) → Reset (đóng hết, gốc mới). **Ưu tiên trước** Trading Stop Step Tổng.
4. **Trading Stop Step Tổng:** Nếu bật và chưa active → kiểm tra ngưỡng lãi lệnh mở; nếu đang active → quản lý SL/step. Nếu giá chạm SL → Dừng EA hoặc Reset EA theo cấu hình.
5. **Lưới:** Trong giờ (hoặc có lệnh mở), không đang Trading Stop → quản lý lệnh chờ và gồng lãi từng lệnh.

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
| Cân bằng lệnh – ĐK 1 | Số bậc lưới có lệnh mở ≥ X | Tổng lot mở ≥ X (tùy bản) |
| Tự động đặt lại lệnh chờ | Luôn bật, không giới hạn | Bật + có thể giới hạn |
| Đánh theo % tài khoản | Có (vốn gốc + tỷ lệ + giới hạn) | Tùy bản |
| Panel trên chart | Không | Có (bản Panel) |

---

## Yêu cầu và cài đặt

### Yêu cầu

- MetaTrader 5  
- Tài khoản cho phép giao dịch tự động (Algo Trading)  
- Push notification (nếu dùng): **Tools → Options → Notifications → Enable push notifications**

### Cài đặt

1. Copy `GridStopTradingV1_Lite.mq5` vào thư mục `MQL5/Experts/` (hoặc Experts của data folder).
2. Mở MetaEditor → mở file → biên dịch (F7).
3. Trong MT5: gắn EA lên chart → bật **Allow Algo Trading** → cấu hình input theo nhu cầu.

### Push notification khi EA reset/dừng

Khi bật **Gửi push notification khi EA reset/dừng**, tin nhắn gửi khi:

- **Reset do Cân bằng lệnh** (đủ ĐK lưới + lãi phiên)
- **Reset do Trading Stop** (giá chạm SL → Reset EA)
- **Dừng/Reset do SL âm USD**

Nội dung: Biểu đồ (symbol), Chức năng (lý do reset/dừng), Số dư, Tích lũy (lần N: trước + lần này = sau), Lỗ lớn nhất (số tiền / vốn %), Lot (max/tổng). Đơn vị tiền format K/M (vd. 12.50K$).

### Giá trị mặc định trong EA

Khi gắn EA lần đầu (chưa lưu set), các input mặc định tương ứng gợi ý cho **BTC/USD** (vốn 50.000 USD hoặc 50.000 cent): push notification = true, Đánh theo % tài khoản = true, lưới 1500 pips / 10 bậc, lot 1, gồng từng lệnh 300/100 pips, Trading Stop Tổng bật 120/100 USD / 2000/1000 pips / Reset EA, Cân bằng bật 3 bậc / 150 USD, Dừng EA theo âm USD bật 3000 / Reset EA. Có thể chỉnh lại theo symbol và rủi ro.

### Gợi ý sử dụng

- Bản Lite không có panel; code gọn, phù hợp khi chỉ cần lưới Stop đơn giản, gồng lãi từng lệnh và dừng theo âm USD.
- Dùng **Magic Number** khác nhau nếu chạy nhiều EA hoặc nhiều symbol.
- **Đánh theo % tài khoản:** vốn gốc = vốn lúc **thêm EA vào biểu đồ**; muốn đổi vốn gốc thì gỡ EA và gắn lại (hoặc xóa GlobalVariable tương ứng).
