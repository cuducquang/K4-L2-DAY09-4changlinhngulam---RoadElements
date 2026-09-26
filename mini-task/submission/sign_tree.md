# Traffic sign tree

Họ tên: Cù Đức Quang (2A202602188) · Chế độ: cá nhân · Mã khoá traffic_sign: `A7B4-E16A`

Viết sau mini-task traffic sign (phút ~155). Dựa vào những biển **bạn đã vẽ** trong 7 ảnh core, không chép danh sách
43 class. Đã điền xong, không còn marker để `make check` đếm.

## 1. Cây của bạn

Chỉ liệt kê class có trong ảnh core. Đếm số box của từng class. Class có 1 box trong cả batch là ứng viên "hiếm".

| family | class (`sign_class`) | Số box trong core | Phổ biến / hiếm | Ảnh ví dụ |
|---|---|---|---|---|
| prohibitory | `01 speed limit 30` | 2 | phổ biến | 00026, 00223 |
| prohibitory | `00 speed limit 20` | 1 | hiếm | 00054 |
| prohibitory | `02 speed limit 50` | 1 | hiếm | 00073 (cụm phải) |
| prohibitory | `09 no overtaking` | 1 | hiếm | 00073 (cụm phải) |
| prohibitory | `10 no overtaking (trucks)` | 2 | phổ biến | 00088 (hai bên cao tốc) |
| prohibitory | `unknown` | 4 | — | 00073 cụm trái, 00088 hai biển trên cùng |
| mandatory | `38 keep right` | 2 | phổ biến | 00054, 00206 |
| mandatory | `34 go left` | 1 | hiếm | 00206 |
| mandatory | `33 go right` | 1 | hiếm | 00206 |
| danger | `unknown` | 3 | — | 00054, 00073 hai cụm |
| other | `13 give way` | 2 | phổ biến | 00206 (hai bên ngã tư) |
| other | `12 priority road` | 1 | hiếm | 00054 |
| other | `unknown` | 1 | — | 00026 (biển xanh lối qua đường, ngoài 43 class) |

Tổng 22 box trên 6 ảnh; `00108` không có biển nên để trống. 8 box (36%) có `sign_class=unknown`, trong đó 4 box sai
so với reference vì mình để unknown khi biển vẫn đọc được (xem `comparison_log.csv`).

## 2. Hai quyết định merge/split

Mỗi quyết định: giữ tách hay gộp, vì sao, và cái giá nếu chọn sai (model downstream nhầm gì).

**Quyết định 1 — `09 no overtaking` và `10 no overtaking (trucks)`: tách hay gộp?**
**Giữ tách.** Hai biển khác đối tượng bị cấm: `09` cấm mọi xe vượt, `10` chỉ cấm xe tải > 3,5 t vượt. Ở 00088 cả hai
bên cao tốc đều là `10` (xe tải màu đỏ bên trái). Nếu gộp thành một class, planner xe con sẽ tự cấm vượt trên cả đoạn
cao tốc chỉ cấm xe tải, gây phanh và giữ làn vô lý. Nếu nhầm theo chiều ngược lại thì xe tải được phép vượt ở chỗ cấm.
Hai biển chỉ khác màu của hình xe bên trái (đỏ = xe tải, đen = xe con), nên ở ảnh nhỏ dễ nhầm: khi không thấy rõ màu
và hình xe thì chọn `unknown`, không chọn `09` mặc định.

**Quyết định 2 — nhóm `other` của GTSDB khi dùng ở Việt Nam.**
GTSDB xếp biển hết hạn chế (`06`, `32`, `41`, `42`) vào `other`. QCVN 41:2024/BGTVT xếp biển hết hiệu lực
(`DP.133`–`DP.135`) vào nhóm **biển báo cấm**. Cây của bạn theo cách nào, và cần rule gì để hai người label giống nhau?
Cây của mình theo **GTSDB**, vì schema và reference của bài dùng taxonomy này. Trong 7 ảnh core không có biển hết hạn
chế nào, nên chưa gặp ca này. Rule để hai người label giống nhau: `sign_family` luôn lấy theo bảng GTSDB trong schema,
không theo QCVN. Nếu dữ liệu dùng cho Việt Nam thì map `06/32/41/42` → nhóm cấm bằng một bảng ánh xạ ở bước hậu xử lý.
Người label không tự đổi family, vì khi hai người theo hai chuẩn khác nhau thì cùng một biển sẽ ra hai family.

## 3. Chính sách cho class hiếm và biển không đọc được

Khi gặp biển không có trong 43 class, hoặc quá nhỏ để đọc: bạn chọn `sign_family`, `sign_class`, `readable` thế nào?
Dẫn một box cụ thể (ảnh + vị trí) làm bằng chứng.
- **Quá nhỏ hoặc nhoè:** chọn family theo hình và màu, `sign_class=unknown`, `readable=uncertain` (thấy hình nhưng
  không đọc được nội dung) hoặc `no` (chỉ đoán được là biển). Bằng chứng: 00088 biển tròn viền đỏ ở (412,440)-(436,464)
  cao ~24 px trong sương, không phân biệt được 100 hay 120, nên chọn prohibitory/unknown/uncertain. Reference ghi
  `08 speed limit 120`, nhưng cũng để `readable=uncertain`.
- **Ngoài 43 class:** vẫn box, `sign_family=other`, `sign_class=unknown`, `readable` theo mức đọc được. Bằng chứng:
  00026 biển xanh vuông lối qua đường (292,347)-(360,414). Reference không box biển này, nên rule đang ở trạng thái
  `escalated`.
- **Bài học từ compare:** mình dùng `unknown` hơi rộng. Biển ≥ 30 px như 00054 (1113,436)-(1152,473) đọc được hình
  người đi bộ khi zoom 400%. Ngưỡng thực tế: biển ≥ 30 px thì phải cố đọc class trước khi chọn unknown.

## 4. Dòng decision log tương ứng

Id của dòng trong `decision_log.csv` ghi rule ở mục 2 hoặc 3: `D2` (biển nhỏ, không đọc được) và `D1` (ngoài 43 class).
