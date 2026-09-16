# Mini annotation guideline — Ngày 3 (tracking)

> Điền file này **trong lúc gán nhãn**, không phải sau khi xong. Mỗi lần bạn dừng
> lại nghĩ "cái này tính sao nhỉ?" thì đó là một dòng phải ghi vào đây.
>
> Đây là tài liệu mà người gán nhãn tiếp theo sẽ đọc để làm giống bạn. Nếu hai
> người trong nhóm gán khác nhau, gần như luôn là vì file này chưa nói rõ — chứ
> không phải vì ai kém.

Nhóm / tên: `Nguyễn Đăng Huân`
Clip: `clip_01`, `clip_02`

---

## 1. Phạm vi: gán cái gì, không gán cái gì

Một lớp duy nhất: **`vehicle`** — xe bốn bánh (xe con, van, xe buýt, xe tải).

| Gán | Không gán |
| --- | --- |
| xe con, SUV, taxi, xe bán tải | người đi bộ |
| van, minivan | xe đạp |
| xe buýt, minibus | **xe máy / mô tô** |
| xe tải, xe đầu kéo | xe trong ảnh quảng cáo, trong gương, dưới bóng nước |



## 2. Luật ID — phần quan trọng nhất

| Tình huống | Luật của nhóm | Vì sao |
| --- | --- | --- |
| Xe bị che một phần rồi hiện lại | giữ nguyên ID nếu bị che **dưới ... frame** (mặc định của lab: 25 frame = 2 giây @ 12.5 fps) | `...` |
| Xe bị che lâu hơn ngưỡng trên | `Tạo track mới (cấp ID mới)` | `Vì xe đã mất dấu quá lâu, tránh rủi ro nối nhầm sang xe khác` |
| Xe rời khung hình rồi quay lại | mặc định: **track mới** | `...` |
| Hai xe cắt nhau / chồng lên nhau | `Vẫn giữ nguyên ID của từng xe` | `bbox ôm sát phần đang nhìn thấy được của mỗi xe, bật thuộc tính Occluded cho xe bị che` |

## 3. Luật bbox

| Tình huống | Luật của nhóm |
| --- | --- |
| Xe bị cắt bởi rìa ảnh | bbox chạm đúng rìa, không đoán phần ngoài ảnh |
| Xe bị xe khác che một phần | bbox ôm phần **nhìn thấy được** |
| Xe vừa xuất hiện, còn rất nhỏ / rất mờ | bắt đầu track từ frame đầu tiên xác định được là xe bốn bánh; ngưỡng nhóm chọn: `...` |
| Xe đang đỗ, không di chuyển | Vẫn được tính là vehicle và vẫn cần track (giữ nguyên ID và bbox) trong suốt thời gian nó nằm trong khung hình |
| Keyframe đặt dày ở đâu | Nên đặt dày ở những đoạn xe rẽ, phanh, đổi hướng hoặc bị che khuất; đi thẳng đều thì có thể đặt thưa.   |

## 4. Ít nhất ba ca mơ hồ đã gặp thật

Ghi **frame cụ thể** và **ID cụ thể**, không ghi chung chung.

### Ca 1
- Clip / frame / ID: 1 /78/6
- Tình huống: `xe con bị che bởi xe bus và chỉ  hiện mỗi đầu xe
- Quyết định: ở frame 80 t
- Lý do: `xe con đã hiện rõ hơn 1 chút và có thể xác định được phần đầu xe`

### Ca 2
- Clip / frame / ID: `clip 01 / frame 78 / 7`
- Tình huống: `xe con bị che bởi xe bus và chỉ còn hiện mỗi đuôi xe nhưng khá mờ 
- Quyết định: `ở frame 80 `
- Lý do: `đuôi xe đã hiện rõ để có thể phát hiện`

### Ca 3
- Clip / frame / ID: `clip01/ frame 97 / 7`
- Tình huống: `xe con bị xe bus che chỉ hiện 1 phần đuoi xe`
- Quyết định: `vẫn giữ nguyên ID 7`
- Lý do: `vẫn có thể nhận diện đó là xe con `

## 5. Sửa gì sau khi chấm với gold và sau khi kiểm chéo

Với xe bị che khuất một phần, chỉ bắt đầu hoặc tiếp tục gán bbox khi phần còn nhìn thấy đủ rõ để xác định chắc chắn đó là xe bốn bánh. Nếu phần xe lộ ra quá nhỏ hoặc quá mờ, chưa gán cho đến frame có thể nhận diện rõ.
Khi xe đang được track bị xe khác che khuất nhưng vẫn còn nhìn thấy một phần và vẫn có thể nhận diện được là cùng một xe, giữ nguyên ID cũ. Chỉ tạo ID mới nếu xe mất dấu lâu hơn ngưỡng 25 frame hoặc không còn đủ thông tin để xác định đó là cùng một xe.
