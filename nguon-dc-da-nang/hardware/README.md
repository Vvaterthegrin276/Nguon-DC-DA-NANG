# Phần cứng (Hardware)

Thư mục này chứa mọi thứ liên quan đến **mạch phần cứng** của dự án.

## Hiện có

| Tài nguyên | Vị trí | Trạng thái |
|---|---|---|
| Sơ đồ khối hệ thống | [images/so-do-khoi.png](../images/so-do-khoi.png) | ✅ Hoàn thành |
| Sơ đồ chân STM32 | [images/so-do-chan-stm32.png](../images/so-do-chan-stm32.png) | ✅ Hoàn thành |
| Bảng nối dây chi tiết | [docs/03-bang-chan-stm32.md](../docs/03-bang-chan-stm32.md) | ✅ Hoàn thành |
| Sơ đồ nguyên lý (schematic) vẽ bằng KiCad | `schematic/` | 🚧 Đang vẽ |
| Bo mạch in (PCB) v6.0 | — | 📅 Kế hoạch |

## Đang chuẩn bị bổ sung

- [ ] **Schematic KiCad**: sơ đồ nguyên lý đầy đủ vẽ bằng KiCad 8 (miễn phí) — kèm file `.kicad_sch` để ai cũng mở và sửa được
- [ ] **Ảnh mạch thật**: ảnh bo cấn trước khi lắp hộp + ảnh thành phẩm
- [ ] **File in vỏ 3D**: hộp 180×115×65mm có khoét sẵn chỗ màn hình, nút, chiết áp, cọc

## Ghi chú cho ai muốn vẽ lại schematic

Khi chuyển từ "mô tả bằng chữ" sang schematic, chú ý 3 điểm dễ vẽ sai nhất:

1. **ACS712 nằm NỖI TIẾP trên đường công suất** (OUT+ XL4016 → IP+ → IP− → cọc Đỏ), không phải một nhánh rẽ.
2. **Module LM358 2 tầng** là khối riêng có 3 cụm chân: đầu vào (V–IN–G), đầu ra (G–02–V), giữa (G–01) — khi vẽ schematic hãy vẽ nó như một khối đen có 2 biến trở chỉnh gain: đầu vào IN lấy từ Cọc Đỏ (+), đầu ra chân 02 về PA2, cụm giữa bỏ trống.
3. **Tụ gốm 104 của PA0/PA1 phải vẽ SÁT chân vi điều khiển** — đây là vị trí hàn thật trên mạch, vẽ xa là mất ý nghĩa lọc nhiễu.
