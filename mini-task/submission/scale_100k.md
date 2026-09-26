# Nếu scale lên 100k frames

Họ tên: Cù Đức Quang (2A202602188) — mini lab đã chọn: traffic_sign

Mỗi mini-task trả lời một câu: **"Nếu scale lên 100k frames, lỗi nào sẽ trở thành systematic defect?"** Viết ngay sau
khi ghi comparison log của task đó. Dựa vào một lỗi bạn **thật sự** gặp hôm nay. Xoá mọi chữ `TODO` khi xong.

Mỗi câu trả lời có 3 phần: lỗi (và bằng chứng: task + sample), vì sao nó lặp lại có hệ thống thay vì ngẫu nhiên,
và cách phát hiện sớm (lát nào cần oversample, tín hiệu QC nào).

## Lane

TODO

## Drivable area

TODO

## Traffic sign

**Lỗi:** biển nhỏ ở xa bị gán `sign_class=unknown` dù vẫn đọc được, hoặc bị box rộng hơn mặt biển vài pixel. Bằng
chứng: 00073 tam giác (723,431)-(752,457) bị để unknown trong khi reference ghi `23 slippery road`; 00054 biển keep
right 20 px bị box rộng 4 px mỗi cạnh nên IoU < 0.6. Trong 22 box của mình có 8 box unknown, và 4/9 dòng khác biệt
trong `comparison_log.csv` là loại này.

**Vì sao thành systematic:** lỗi không ngẫu nhiên mà gắn với **kích thước biển**. Trên 100k frame, mọi biển < 30 px
(biển xa, cao tốc, sương mù) sẽ bị lệch cùng một kiểu: class chuyển sang unknown, box to hơn mặt biển. Model sẽ học
rằng biển nhỏ = unknown, nên chỉ đọc được giới hạn tốc độ khi xe đã tới rất gần. Trên cao tốc như 00088 (biển 120 và
cấm xe tải vượt), đó là lúc quá muộn để giảm tốc êm. Box rộng cũng làm model học lẫn cột và nền vào mặt biển.

**Phát hiện sớm:**
- Chia QC theo lát kích thước box: oversample box < 30 px, cao tốc và sương mù.
- Theo dõi tỉ lệ `unknown` theo từng bin kích thước; bin 30–40 px mà unknown > 20% là dấu hiệu annotator bỏ cuộc sớm.
- Kiểm cặp biển giống nhau hai bên đường (00073, 00088): hai biển cùng cột mốc mà khác class là cờ cần review.
- Thêm rule D2 và D3 vào guideline, kèm ngưỡng "biển ≥ 30 px phải zoom 400% đọc trước khi chọn unknown".

## Traffic light

TODO
