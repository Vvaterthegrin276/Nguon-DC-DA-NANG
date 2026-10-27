# 💻 Hướng dẫn nạp code cho STM32 — từ số 0 cho người mới

> Mục tiêu: cài Arduino IDE, cài "bộ não" cho STM32, nạp file `firmware/nguon_dc_da_nang/nguon_dc_da_nang.ino` lên board Blue Pill bằng ST-Link V2. Lần đầu làm mất khoảng 30–45 phút, các lần sau chỉ mất 2 phút.

---

## 1. Cài Arduino IDE

1. Tải **Arduino IDE 2.x** tại: <https://www.arduino.cc/en/software> (chọn Windows/Linux tùy máy).
2. Cài đặt như phần mềm bình thường, Next liên tục đến hết.
3. Mở IDE lên — giao diện gồm: nút **Verify** (✓ biên dịch), nút **Upload** (→ nạp), khung soạn code ở giữa.

## 2. Cài hỗ trợ STM32 vào Arduino IDE (STM32duino)

Arduino IDE mặc định chỉ hiểu Arduino Uno/Nano. Muốn nó hiểu STM32, thêm "board package" của ST:

1. Mở **File → Preferences** (Windows) / **Arduino IDE → Settings** (macOS).
2. Ô **Additional boards manager URLs**, dán địa chỉ sau vào (nếu ô đã có URL khác thì ngăn cách bằng dấu phẩy):
   ```
   https://github.com/stm32duino/BoardManagerFiles/raw/main/package_stmicroelectronics_index.json
   ```
3. Bấm **OK**, mở **Tools → Board → Boards Manager...**, gõ `STM32`, cài **STM32 MCU based boards** (tác giả STMicroelectronics). Tải khoảng 300–500MB, đợi chút.
4. Xong phần này, IDE đã "hiểu" Blue Pill.

## 3. Cài 2 thư viện màn hình

Mở **Sketch → Include Library → Manage Libraries...**, lần lượt tìm và cài:

| Thư viện | Tác giả | Cài bản nào |
|---|---|---|
| **Adafruit GFX Library** | Adafruit | Bản mới nhất |
| **Adafruit ST7735 and ST7789 Library** | Adafruit | Bản mới nhất |

> Bấm cài thì IDE hỏi cài thêm thư viện phụ thuộc (dependencies) → chọn **Install All**.

## 4. Cấu hình board cho Blue Pill

Vào menu **Tools**, chọn lần lượt:

| Mục | Chọn |
|---|---|
| Board | **Generic STM32F1 series** |
| Board part number | **BluePill F103C8** (nếu không có thì chọn F103CB) |
| Upload method | **STM32CubeProgrammer (DFU)** hoặc **Mass Storage** — nhưng nạp qua ST-Link ta sẽ chọn **STM32CubeProgrammer (SWD)** nếu có hiện |
| USB support | **None** (chưa cần USB) |
| Optimize | Smallest (-Os) |

> ⚠️ **Lưu ý về phương thức nạp:** Với ST-Link V2, chọn **Upload method = STM32CubeProgrammer (SWD)**. Nếu IDE báo thiếu STM32CubeProgrammer, tải và cài miễn phí tại <https://www.st.com/en/development-tools/stm32cubeprog.html> (cần đăng ký email miễn phí). Đây là bước duy nhất hơi lằng nhằng lần đầu.

## 5. Mở firmware và chỉnh tên thư mục

1. Tải repo này về máy (nút **Code → Download ZIP**, giải nén) — hoặc git clone nếu bạn đã quen.
2. Mở file `firmware/nguon_dc_da_nang/nguon_dc_da_nang.ino` bằng Arduino IDE (double-click).
3. Arduino yêu cầu file `.ino` nằm trong **thư mục cùng tên** — repo này đã để đúng cấu trúc nên mở là chạy luôn. Nếu bạn copy file ra chỗ khác, nhớ giữ tên thư mục trùng tên file.

## 6. Nối ST-Link với Blue Pill

Nối **4 dây** giữa ST-Link V2 và board STM32 (không cần cắm nguồn ngoài):

| ST-Link V2 | Blue Pill |
|---|---|
| 3.3V | 3V3 |
| GND | GND |
| SWDIO | SWDIO (chân có chữ DIO cạnh chân 3V3) |
| SWCLK | SWCLK (chân cạnh SWDIO) |

> 💡 Nối xong, cắm ST-Link vào máy tính. Nếu đèn xanh trên ST-Link sáng và Windows phát hiện thiết bị là đã sẵn sàng.

## 7. Nạp!

1. Chọn cổng/programmer: **Tools → Upload method → STM32CubeProgrammer (SWD)**.
2. Bấm nút **Verify** (✓) — chờ "Done compiling" tức code biên dịch thành công.
3. Bấm nút **Upload** (→). IDE sẽ nạp qua ST-Link trong ~20 giây.
4. Thông báo `Upload complete` → rút ST-Link, cắm nguồn 5V từ LM2596 vào chân 5V của STM32 → **màn hình TFT sáng lên hiển thị Dashboard**.

## 8. Nếu nạp lỗi — 3 lỗi kinh điển

| Lỗi | Ý nghĩa | Cách xử lý |
|---|---|---|
| `No target connected` | ST-Link không "thấy" board | Kiểm tra 4 dây nối (nhất là SWDIO/SWCLK không bị đảo); cắm lại USB |
| `Chip is protected` | Chip bị khóa read protection (một số board mua trên chợ đã bị cài sẵn) | Dùng **STM32CubeProgrammer** → tab OB (Option Bytes) → tắt RDP → nạp lại |
| `Upload method` không có SWD | Chưa cài STM32CubeProgrammer | Cài theo link ở mục 4 rồi khởi động lại Arduino IDE |

## 9. Test nhanh sau khi nạp

- Bấm **MODE** → chuyển qua lại giữa Dashboard ↔ Oscilloscope ↔ Thông tin.
- Vặn **chiết áp Volt** → số điện áp trên Dashboard đổi theo.
- Bấm **CHANNEL** ở màn Oscilloscope → đổi kênh sóng (Gợn AC / Volt / Dòng).
- Bấm **HOLD** → màn hình đóng băng, bấm lại để chạy tiếp.

Nếu mọi thứ chạy như trên → sang [docs/06-hieu-chinh.md](06-hieu-chinh.md) để hiệu chỉnh độ chính xác. Chúc thành công! 🎉
