# 💻 Hướng dẫn nạp code cho STM32 — từ số 0 cho người mới

> Mục tiêu: nạp file `firmware/nguon_dc_da_nang/nguon_dc_da_nang.ino` lên board Blue Pill bằng ST-Link V2. Có **2 cách làm**, chọn 1 trong 2:
>
> - **Cách A — VS Code + PlatformIO** *(khuyên dùng — cách tác giả dự án dùng hàng ngày)*: cài 1 lần, sau đó biên dịch + nạp đều bằng 1 nút bấm, thư viện tự tải về.
> - **Cách B — Arduino IDE**: quen thuộc với ai đã từng dùng Arduino, thao tác thủ công hơn chút.
>
> Lần đầu làm mất khoảng 30–45 phút (chủ yếu là chờ tải), các lần sau chỉ mất 2 phút.

---

## CÁCH A — VS Code + PlatformIO (khuyên dùng)

### A1. Cài VS Code + extension PlatformIO

1. Tải **Visual Studio Code** tại: <https://code.visualstudio.com/> — cài như phần mềm bình thường.
2. Mở VS Code, bấm biểu tượng **Extensions** (4 ô vuông, thanh trái) → gõ `PlatformIO IDE` → **Install** (tác giả PlatformIO.org).
3. Chờ cài xong (~5 phút, có lúc tải phụ thuộc Python — cứ để yên). Khởi động lại VS Code, thấy biểu tượng **kiểu con kiến (anteater)** xuất hiện trên thanh trái là OK.

### A2. Mở dự án và để PlatformIO tự sắp xếp phần còn lại

1. Tải repo này về máy (nút **Code → Download ZIP**, giải nén) — hoặc git clone nếu bạn đã quen.
2. Trong VS Code: **File → Open Folder...** → chọn thư mục **`firmware/`** (thư mục chứa file `platformio.ini`, KHÔNG phải thư mục gốc của repo).
3. PlatformIO đọc thấy `platformio.ini` → tự động cài: platform **ststm32**, core Arduino cho STM32, và 2 thư viện màn hình **Adafruit GFX** + **Adafruit ST7735/ST7789** (khai báo sẵn trong mục `lib_deps`). Tải khoảng 500MB–1GB lần đầu, đợi tới khi góc dưới phải hiện `Project has been successfully imported`.
4. Mở file `firmware/nguon_dc_da_nang/nguon_dc_da_nang.ino` — không cần chỉnh sửa gì thêm.

> 💡 File `platformio.ini` đã cấu hình sẵn mọi thứ: board `bluepill_f103c8`, framework `arduino`, nạp qua `stlink`. Bạn không phải chọn board gì cả — khác với Arduino IDE phải chỉnh tay từng mục trong menu Tools.

### A3. Nối ST-Link với Blue Pill

Nối **4 dây** giữa ST-Link V2 và board STM32 (không cần cắm nguồn ngoài):

| ST-Link V2 | Blue Pill |
|---|---|
| 3.3V | 3V3 |
| GND | GND |
| SWDIO | SWDIO (chân có chữ DIO cạnh chân 3V3) |
| SWCLK | SWCLK (chân cạnh SWDIO) |

> 💡 Nối xong, cắm ST-Link vào máy tính. Nếu đèn xanh trên ST-Link sáng và Windows phát hiện thiết bị là đã sẵn sàng.

### A4. Nạp!

1. Nhìn **thanh trạng thái dưới cùng** của VS Code: bấm nút **➜ (Upload)** — hoặc biểu tượng con kiến → sidebar PlatformIO → **Project Tasks → bluepill_f103c8 → General → Upload**.
2. Lần đầu biên dịch mất 1–2 phút, sau đó nạp qua ST-Link trong ~20 giây.
3. Thấy dòng `**SUCCESS** [upload]` trong cửa sổ Terminal → rút ST-Link, cắm nguồn 5V từ LM2596 vào chân 5V của STM32 → **màn hình TFT sáng lên hiển thị Dashboard**.
4. Muốn xem bản biên dịch có lỗi hay không mà chưa nạp: nút **✓ (Build)** trên cùng thanh đó.

### A5. Nếu nạp lỗi — các lỗi kinh điển với PlatformIO

| Lỗi | Ý nghĩa | Cách xử lý |
|---|---|---|
| `Error: failed to connect to target` / `No target connected` | ST-Link không "thấy" board | Kiểm tra 4 dây nối (nhất là SWDIO/SWCLK không bị đảo); cắm lại USB |
| `up_time is limited` / `Chip is protected` | Chip bị khóa read protection (một số board mua trên chợ đã bị cài sẵn) | Dùng **STM32CubeProgrammer** → tab OB (Option Bytes) → tắt RDP → nạp lại. Tải miễn phí: <https://www.st.com/en/development-tools/stm32cubeprog.html> |
| Không thấy nút ➜ trên thanh dưới | Extension chưa load xong hoặc mở sai thư mục | Đảm bảo đã mở đúng thư mục `firmware/` (có `platformio.ini`); khởi động lại VS Code |
| `Library ... not found` khi Build | Chưa nối mạng lúc cài | Nối mạng, xóa thư mục `firmware/.pio/`, bấm Build lại — PlatformIO tải lại thư viện |

---

## CÁCH B — Arduino IDE (cách truyền thống)

### B1. Cài Arduino IDE + hỗ trợ STM32

1. Tải **Arduino IDE 2.x** tại: <https://www.arduino.cc/en/software>, cài đặt Next liên tục đến hết.
2. Mở **File → Preferences**, ô **Additional boards manager URLs** dán:
   ```
   https://github.com/stm32duino/BoardManagerFiles/raw/main/package_stmicroelectronics_index.json
   ```
3. Bấm **OK**, mở **Tools → Board → Boards Manager...**, gõ `STM32`, cài **STM32 MCU based boards** (tác giả STMicroelectronics).

### B2. Cài 2 thư viện màn hình

**Sketch → Include Library → Manage Libraries...**, lần lượt tìm và cài (bấm **Install All** khi được hỏi dependencies):

| Thư viện | Tác giả |
|---|---|
| **Adafruit GFX Library** | Adafruit |
| **Adafruit ST7735 and ST7789 Library** | Adafruit |

### B3. Cấu hình board + mở firmware

Vào menu **Tools**, chọn: Board = **Generic STM32F1 series**, Board part number = **BluePill F103C8**, Upload method = **STM32CubeProgrammer (SWD)**, USB support = **None**, Optimize = Smallest (-Os).

> ⚠️ Nếu IDE báo thiếu STM32CubeProgrammer, tải và cài miễn phí tại <https://www.st.com/en/development-tools/stm32cubeprog.html> (cần đăng ký email miễn phí).

Mở file `firmware/nguon_dc_da_nang/nguon_dc_da_nang.ino` bằng Arduino IDE (double-click). Arduino yêu cầu file `.ino` nằm trong **thư mục cùng tên** — repo đã để đúng cấu trúc nên mở là chạy luôn.

### B4. Nối ST-Link + nạp

Nối 4 dây **giống hệt bảng ở mục A3** ở trên. Sau đó:

1. Bấm **Verify** (✓) — chờ "Done compiling".
2. Bấm **Upload** (→). Thông báo `Upload complete` → rút ST-Link, cắm nguồn 5V từ LM2596 → màn hình sáng lên.
3. Lỗi thường gặp xử lý giống bảng mục A5 (với Arduino IDE: nếu mục `Upload method` không có SWD thì chưa cài STM32CubeProgrammer — cài xong khởi động lại IDE).

---

## Test nhanh sau khi nạp (áp dụng cho cả 2 cách)

- Bấm **MODE** → chuyển qua lại giữa Dashboard ↔ Oscilloscope ↔ Thông tin.
- Vặn **chiết áp Volt** → số điện áp trên Dashboard đổi theo.
- Bấm **CHANNEL** ở màn Oscilloscope → đổi kênh sóng (Gợn AC / Volt / Dòng).
- Bấm **HOLD** → màn hình đóng băng, bấm lại để chạy tiếp.

Nếu mọi thứ chạy như trên → sang [docs/06-hieu-chinh.md](06-hieu-chinh.md) để hiệu chỉnh độ chính xác. Chúc thành công! 🎉
