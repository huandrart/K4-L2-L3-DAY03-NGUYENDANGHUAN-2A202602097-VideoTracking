# Báo cáo Ngày 3 — Tracking Annotation

Chép file này thành `reports/REPORT.md` rồi điền. Giữ nguyên các tiêu đề.

Họ tên / nhóm: `Nguyễn Đăng Huân`
Ngày: `2026-09-15`

---

## 1. Quá trình gán nhãn

| Mục | Giá trị |
| --- | --- |
| Công cụ | CVAT / khác: `Cvat` |
| Thời gian gán `clip_02` (warm-up) | `40` phút |
| Thời gian gán `clip_01` | `120` phút |
| Số track đã vẽ trong `clip_01` | `8` |
| Số keyframe trung bình mỗi track | `33` |

Ba tình huống khó nhất khi gán clip này, và bạn xử lý thế nào:

1. `tình huống 1 không rõ về xe con bị che bởi xe buýt và chỉ hiện mỗi đầu xe, ở frame 78`
2. `tình huống 2 không rõ về xe con bị che bởi xe buýt và chỉ hiện mỗi đuôi xe nhưng khá mờ, ở frame 78`
3. `tình huống 3 không rõ về xe con bị che bởi xe buýt và chỉ hiện mỗi đuôi xe nhưng khá mờ, ở frame 97`

## 2. Tự kiểm và kiểm chéo

Ba lượt tua bắt được gì (lượt 1 nhìn ID, lượt 2 frame đầu/cuối, lượt 3 frame giữa):

- Lượt 1: `gán thiếu id cho xe con khi xe bus che khuất`
- Lượt 2: `bbox xe con bị cắt ở cạnh trên khi xe bus che khuất`
- Lượt 3: `ổn định `



Ca nào hai người quyết khác nhau, và luật nào còn thiếu trong `GUIDELINE_MINI.md`?

`...`

## 3. Pre-gold lock và chấm trước/sau rework

| Evidence | Giá trị |
| --- | --- |
| SHA-256 từ `evidence/pre-gold/clip_01/manifest.json` | `...` |
| Thời điểm khóa | `...` |
| Số row / frame / track trước khi mở reference | `...` |

| | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Bản pre-gold | | | | | | | | | | |
| Sau rework | | | | | | | | | | |

Qua cổng (`IDF1 >= 0.80`, `MOTA >= 0.75`, `MOTP >= 0.70`): **có / chưa**

Sau khi đọc danh sách lỗi, bạn đã sửa cụ thể những gì? Ghi theo frame và ID:

| Loại lỗi | Frame | ID | Đã sửa thế nào |
| --- | --- | --- | --- |
| | | | |
| | | | |
| | | | |

## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ `outputs/model_run_config.json`:

| Mục | Giá trị |
| --- | --- |
| Python / ultralytics / torch / lap | `...` |
| weights / hai tracker | `...` |
| conf / IoU / imgsz / classes | `...` |
| device | `...` |

| So sánh | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| bạn vs gold | | | | | | | | | | |
| ByteTrack control vs gold | | | | | | | | | | |
| BoT-SORT + ReID vs gold | | | | | | | | | | |
| ReID vs bạn | | | | | | | | | | |

## 5. Phân tích — năm câu hỏi

**1. MOTA của bạn cao hơn hay thấp hơn IDF1? Nếu MOTA cao mà IDF1 thấp thì điều đó nói gì, và vì sao MOTA không phạt nặng lỗi ID?**

`MOTA: 0.890
IDF1: 0.946
MOTA của tôi thấp hơn IDF1. Điều này cho thấy nhãn của tôi có thể giữ ID tốt nhưng lại gặp vấn đề trong việc phát hiện vật thể (nhiều FP/FN).`

**2. ByteTrack control và BoT-SORT + ReID treatment khác nhau thế nào ở IDF1, AssA và IDSW? Dẫn một frame sequence để giải thích treatment tốt hơn, tệ hơn hoặc không đổi đáng kể. Nhắc rõ đây không cô lập causal effect của ReID vì hai tracker implementation khác.**

`ByteTrack control (so với gold):
  IDF1: 0.875, AssA: 0.776, IDSW: 2
BoT-SORT + ReID treatment (so với gold):
  IDF1: 0.900, AssA: 0.820, IDSW: 2

BoT-SORT + ReID có IDF1 cao hơn ByteTrack, cho thấy khả năng giữ ID tốt hơn.
BoT-SORT + ReID có AssA cao hơn, cho thấy khả năng liên kết đúng vật thể với track tương ứng tốt hơn.
BoT-SORT + ReID có cùng số lượng IDSW với ByteTrack.

ReID có thể giúp phân biệt các vật thể tương tự khi chúng overlap hoặc khi chúng xuất hiện trở lại sau khi bị che khuất.`

**3. DetA, FP và FN đổi thế nào? Lỗi còn lại là detector hay association?**

`
ByteTrack control (so với gold):
  DetA: 0.649, FP: 88, FN: 54
BoT-SORT + ReID treatment (so với gold):
  DetA: 0.711, FP: 91, FN: 26

Phân tích DetA, FP và FN:
DetA phản ánh mức độ chính xác của việc phát hiện vật thể. Mức tăng hoặc giảm của DetA có thể do sự thay đổi trong cách tracker xử lý các box điểm thấp (như ByteTrack).
FP (False Positives) và FN (False Negatives) trực tiếp liên quan đến hiệu suất của detector. Nếu FP và FN tăng/giảm đáng kể, nó có thể chỉ ra rằng phần lớn lỗi vẫn đến từ bước phát hiện chứ không phải chỉ riêng association`

**4. Một chỗ bạn đúng và ReID sai (frame, ID, vì sao):**

`Trong frame reid có 'máy bán hàng' cao , có thể ReID đã tạo ra bbox thừa (FP) mà tôi không gán. Điều này có thể xảy ra khi model nhầm lẫn vật thể tĩnh hoặc nhiễu thành đối tượng cần theo dõi.
`

**5. Một chỗ ReID làm bạn xem lại annotation (frame, ID, vì sao), hoặc lý do evidence cho thấy model sai:**

`Trong frame 91 tôi có 2 track đã gán trước so với file gold khi file gold tận frame 107 mới phát hiện 

## 6. Nếu phải gán thêm 10 clip nữa

Làm rõ quy tắc gán ID khi vật thể bị che khuất/tái xuất hiện: Nhấn mạnh việc giữ cùng một ID cho một vật thể ngay cả khi nó biến mất và xuất hiện lại, đặc biệt nếu thời gian bị che khuất ngắn. Có thể thêm hướng dẫn về khoảng thời gian tối đa được phép giữa hai lần xuất hiện để coi là cùng một track.
Quy định chặt chẽ về bbox điểm thấp và vật thể tĩnh: Hướng dẫn người gán không gán bbox cho các vật thể tĩnh hoặc những thứ có thể bị nhầm lẫn với phương tiện (ví dụ: hình ảnh xe trên biển quảng cáo, các vật thể lớn không di chuyển). Chỉ gán những phương tiện di chuyển hoặc có khả năng di chuyển.
Chi tiết hơn về bbox khi xe overlap hoặc chuyển làn: Cung cấp ví dụ cụ thể về cách gán bbox và ID khi hai xe đi sát nhau, cắt ngang nhau, hoặc đổi làn để tránh lỗi ID Switch và Tách Track.
Tập trung vào tính nhất quán của bbox: Nhấn mạnh tầm quan trọng của việc duy trì kích thước và vị trí bbox nhất quán theo thời gian, tránh lỗi BBOX LỆCH do gán không đều qua các keyframe. Có thể khuyến nghị thêm keyframe khi có sự thay đổi đáng kể về kích thước hoặc hướng di chuyển của vật thể.
Hướng dẫn xử lý các đối tượng nhỏ hoặc ở rìa khung hình: Đưa ra ngưỡng rõ ràng về kích thước tối thiểu của một đối tượng được gán hoặc cách xử lý các đối tượng chỉ xuất hiện một phần trong khung hình để giảm thiểu lỗi BBOX THỪA hoặc THIẾU ĐOẠN.
Kiểm tra chéo định kỳ: Khuyến khích người gán tự kiểm tra lại các track ID của mình sau một vài frame, hoặc kiểm tra ngẫu nhiên các đoạn video để đảm bảo tính nhất quán.
Những thay đổi này nhằm mục đích cải thiện cả DetA (giảm FP/FN) và AssA (giảm IDSW), dẫn đến điểm HOTA và IDF1 tốt hơn cho các lần gán nhãn trong tương lai.
`...`

## 7. Tệp đã nộp

- [ ] `annotations/clip_01/gt.txt`
- [ ] `annotations/clip_02/gt.txt`
- [ ] `evidence/pre-gold/clip_01/gt.txt` và `manifest.json`
- [ ] `GUIDELINE_MINI.md` đã điền
- [ ] `outputs/eval_vs_gold.json`
- [ ] `outputs/model_bytetrack_clip_01.txt`
- [ ] `outputs/model_reid_clip_01.txt`
- [ ] `outputs/model_run_config.json`
- [ ] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json`
- [ ] `reports/REPORT.md` (file này)
