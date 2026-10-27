# 💻 Firmware — Chương trình cho STM32

Thư mục này chứa toàn bộ code chạy trên **STM32F103C8T6 (Blue Pill)** của bộ nguồn đa năng.

```
firmware/
├── README.md                      ← File bạn đang đọc
├── platformio.ini                 ← Cấu hình PlatformIO (VS Code) — mở thư mục này là build được ngay
└── nguon_dc_da_nang/
    └── nguon_dc_da_nang.ino       ← Toàn bộ chương trình (1 file duy nhất)
```

## Firmware làm được gì?

| Màn hình | Chức năng |
|---|---|
| **Dashboard** | 3 số lớn: Điện áp (V) — Dòng (A) — Công suất (W), cập nhật liên tục |
| **Oscilloscope** | Vẽ dạng sóng mini 120 điểm: Gợn AC (qua module LM358 2 tầng) / Volt / Dòng, 5 mức thước thời gian (FAST → 10ms/cột) |
| **Thông tin** | Trang giới thiệu + ghi chú 4 nút bấm |
| **HOLD** | Đóng băng màn hình để chụp ảnh / đọc số chính xác |

## Cấu trúc code (cho ai muốn sửa)

Code được chia thành 12 khối đánh số rõ ràng, tìm nhanh bằng tiêu đề `/* ==== N. TÊN KHỐI ==== */`:

1. Định nghĩa chân
2. **Thông số hiệu chỉnh** ← *bạn sẽ sửa khối này nhiều nhất*
3. Cấu hình màn hình
4. Biến trạng thái
5. `setup()` — khởi động
6. `loop()` — vòng lặp chính
7. Đo & lọc số liệu (trung bình trượt 16 mẫu)
8. Dashboard
9. Oscilloscope (lấy mẫu + vẽ)
10. Cờ HOLD
11. Đọc nút nhấn (debounce 250ms)
12. Trang thông tin

## Ý tưởng mở rộng (cho bạn nào muốn hack tiếp)

- **Đo Vpp tự động:** trong `captureWave()`, lưu `min/max` của `waveBuf` rồi tính `(max−min) × VREF / 4095` — hiển thị Vpp ở góc màn hình scope.
- **Tần số:** đo khoảng cách giữa 2 lần sóng cắt trục giữa → 1/tần số. Só gợn 100Hz của cầu chỉnh lưu sẽ hiện rõ.
- **Cảnh báo quá dòng:** nếu `fabs(iAvg) > ngưỡng`, nhấp nháy số dòng màu đỏ. Chưa cần thêm phần cứng!
- **Đo năng lượng:** cộng dồn `P × Δt` vào biến `Wh` — bộ nguồn đo được cả điện năng tiêu thụ.

## Cài môi trường & nạp code

Xem hướng dẫn từng bước kèm bảng xử lý lỗi: **[docs/05-huong-dan-nap-code.md](../docs/05-huong-dan-nap-code.md)**. Có 2 cách, chọn 1:

**Cách A — VS Code + PlatformIO (khuyên dùng, cách tác giả dùng):**

1. Cài **VS Code** + extension **PlatformIO IDE**
2. **File → Open Folder...** → chọn thư mục `firmware/` này (chứa `platformio.ini`)
3. PlatformIO tự cài platform `ststm32` + core Arduino + 2 thư viện màn hình (khai báo sẵn trong `lib_deps`)
4. Bấm nút **➜ (Upload)** trên thanh dưới → nạp qua **ST-Link V2** (4 dây: 3V3, GND, SWDIO, SWCLK)

**Cách B — Arduino IDE:**

1. Cài **Arduino IDE 2.x**
2. Thêm URL STM32duino vào Preferences → cài board **STM32 MCU based boards**
3. Cài 2 thư viện: **Adafruit GFX** + **Adafruit ST7735 and ST7789**
4. Board = *Generic STM32F1 series / BluePill F103C8*, Upload = *STM32CubeProgrammer (SWD)*, nạp qua **ST-Link V2**

## Lưu ý khi chỉnh sửa

- **Đừng in chữ có dấu** (Đ, ệ, ữ...) lên màn hình — font 5x7 của Adafruit GFX không có ký tự tiếng Việt, sẽ hiện ký tự lạ. In không dấu như trong code.
- Nếu đổi màn hình lớn hơn/nhỏ hơn, sửa `SCREEN_W`, `SCREEN_H` và tọa độ vẽ trong khối 8, 9, 12.
- Nếu dùng ACS712 bản 20A hoặc 30A, sửa `ACS_SENS` thành `0.100` hoặc `0.066`.
