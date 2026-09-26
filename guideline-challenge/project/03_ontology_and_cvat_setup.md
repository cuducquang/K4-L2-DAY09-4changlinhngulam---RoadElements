# Ontology + CVAT setup

Bảng ontology là **source of truth** cho schema CVAT: `03_cvat_labels.json` phải khớp từng dòng ở đây.

## Ontology table

| Name | Geometry | Type (class / attribute) | Allowed values | Default | Mutable? | Rationale |
|---|---|---|---|---|---|---|
| `traffic_light` | rectangle | class | — | — | — | Mỗi đầu đèn xe cơ giới có mặt ô đèn quay về ego, thuộc giao lộ đầu tiên. Đây là object mà detector cần học |
| `state` | — | attribute của `traffic_light` | `red`, `yellow`, `green`, `unknown` | `__undefined__` | Yes | Planner cần màu. `unknown` thay cho đoán khi bị che/loá/nhấp nháy. Bỏ `off` vì ảnh tĩnh không phân biệt được đèn tắt với LED nhấp nháy. Mutable chỉ để tương thích video |
| `relevance` | — | attribute của `traffic_light` | `ego`, `other`, `unknown` | `__undefined__` | No | Đây là trọng tâm pain point: đèn nào điều khiển làn ego. Một đầu đèn không đổi vai trò giữa các frame |
| `frame` | tag (cả ảnh) | class (image-level tag) | — | — | — | Kết luận cấp ảnh cho planner. Giúp IGNORE và ESCALATE hiện ra trong export, không chỉ được suy từ việc thiếu box |
| `ego_signal` | — | attribute của `frame` | `visible`, `out_of_view`, `none`, `escalate` | `__undefined__` | No | `out_of_view` (có giao lộ đèn nhưng không thấy đèn ego) khác `none` (không có đèn) về hành vi an toàn của planner |

## Class hay attribute

- **`traffic_light` là class duy nhất có geometry.** Mọi đầu đèn có cùng hình dạng, cùng geometry rule và cùng QA rule.
  Màu và vai trò chỉ là thuộc tính của cùng một vật thể. Nếu tách thành class `red_ego_light`, `green_other_light`…
  thì số tổ hợp nổ thành 12 class.
- **`state` là attribute** vì nó đổi theo thời gian trên cùng một đầu đèn; ví dụ LISA01 → LISA30 từ đỏ chuyển xanh.
- **`relevance` là attribute** vì phụ thuộc ngữ cảnh ego chứ không phụ thuộc hình dạng.
- **Đèn đi bộ không phải class**, mà là exclusion rule (IGNORE). Downstream của bài này không dùng đèn đi bộ. Nếu thêm
  class này sẽ tốn công label mà không phục vụ contract. Rủi ro nhầm vẫn được chặn bằng rule 5.1 và bằng tag
  `out_of_view`.
- **`frame` là tag** vì đây là quyết định cho cả ảnh, không gắn với một object nào. Đây cũng là nơi duy nhất thể hiện
  được ESCALATE và việc "không có đèn ego".
- **Không thêm `pictogram` hay `direction`.** Mũi tên được xử lý qua `relevance` (rule 5.4c). Đèn quay ngang/lưng bị
  IGNORE (rule 5.2), nên không cần attribute `direction`. Mỗi attribute thêm vào là thêm một chỗ bất đồng trong blind
  test.
- **Bias của default.** Cả 3 attribute có default `__undefined__`, nên annotator buộc phải chọn. Nếu default là `red`
  hay `ego`, người quên đổi sẽ tạo ra lỗi critical mà không ai thấy. Self-QC: trước khi Save, lọc các object còn
  `__undefined__`.

## CVAT

- **Phiên bản CVAT** (`make cvat-status`): TODO (điền khi mở CVAT; lớp dùng v2.74.1)
- **Tên task calibration:** TODO, ví dụ `teamXX-calib-v1`
- **Guide của task đã dán `02_guideline.md`?** TODO (có / chưa)
- **Nhóm dùng Track hay Shape, vì sao:** dùng **Shape**. Ảnh tĩnh không liên tiếp; frame LISA cũng được label độc lập,
  không nối track.

## Setup test

Một thành viên **chưa tham gia setup** mở task và trả lời bốn câu: label gì, dùng tool nào, gán attribute nào, khi nào
escalate. Ghi lại ai test và chỗ họ vấp.

TODO
