# Problem statement + downstream contract

## Bài toán

**"Đèn nào điều khiển xe mình?"** Mục tiêu là xác định trạng thái (đỏ/vàng/xanh) của các đầu đèn giao thông điều khiển
làn ego tại giao lộ kế tiếp, trong ảnh đô thị có nhiều đầu đèn. Cái khó là lọc đúng những đầu đèn này khỏi các nguồn dễ
nhầm: đèn hướng cắt ngang, đèn quay lưng, đèn đi bộ, đèn của giao lộ phía sau, mũi tên của làn rẽ, bóng phản chiếu và
cảnh ban đêm/mưa.

## Downstream contract

1. **Downstream task / model / user là ai?** Module traffic-light perception của xe tự hành: detector và classifier
   trạng thái, cộng với bước gán đèn cho làn ego. Output của module đi thẳng vào planner để quyết định dừng hay đi ở
   vạch dừng kế tiếp. Người dùng dữ liệu là nhóm train/eval model này.
2. **Output annotation nào thực sự cần?** Output gồm ba phần:
   - Một box cho mỗi đầu đèn xe cơ giới có mặt đèn quay về phía ego.
   - Hai attribute trên box đó: `state` và `relevance` (ego / other / unknown).
   - Một tag cấp ảnh `frame.ego_signal` tóm tắt kết luận cho planner: visible / out_of_view / none / escalate.
3. **Failure nào gây hậu quả lớn nhất?** Sai trạng thái hoặc relevance của đèn điều khiển ego:
   - Đèn đi bộ bàn tay đỏ bị gán thành đèn ego đỏ. Hệ quả là phanh gấp vô cớ (phantom braking), dễ bị tông đuôi.
   - Đèn đỏ ở giao lộ phía sau bị gán cho ego trong khi đèn gần đang xanh.
   - Đèn ego bị bỏ sót, hoặc bị gán `other`. Hệ quả là planner không có tín hiệu, hoặc lấy nhầm tín hiệu.
4. **Khi ambiguity không resolve được, ai / ở đâu là escalation path?** Annotator gán `relevance=unknown` hoặc
   `state=unknown` trên box, rồi đặt tag `frame [ego_signal=escalate]`. QA owner review 100% ảnh escalate trong 24h và
   ghi quyết định vào `clarification_log`. Nếu case lặp lại ≥ 2 lần thì thêm rule mới vào guideline.

## Scope

- **Trong scope (bắt buộc label):** mỗi đầu đèn tín hiệu cho xe cơ giới (đèn tròn hoặc mũi tên) thỏa đủ hai điều kiện:
  - thấy được mặt ô đèn (lens), không phải chỉ thấy vỏ;
  - thuộc giao lộ đầu tiên phía trước ego, tức không nhỏ hơn 1/3 đầu đèn lớn nhất đang quay về ego.
  
  Ngoài ra mỗi ảnh có đúng 1 tag `frame`.
- **Ngoài scope (ignore):**
  - đèn đi bộ (bàn tay/người, đếm ngược);
  - vỏ đèn quay ngang hoặc quay lưng;
  - đầu đèn nhỏ hơn 1/3 đầu đèn lớn nhất;
  - đèn đường sắt, tram, bus-only; đèn vàng nhấp nháy cảnh báo;
  - đèn xe, đèn đường, biển hiệu;
  - bóng phản chiếu trên capo, kính hoặc mặt đường ướt.
- **Geometry tolerance:** box ôm sát vỏ đầu đèn nhìn thấy (visible, không amodal), không lấy backplate, cần treo hay
  quầng sáng. Mỗi cạnh được lệch tối đa max(2 px, 15% chiều cao box).

## Output chấm được

Cả bốn loại quyết định đều hiện trong file export CVAT:

| Quyết định | Thể hiện trong CVAT |
|---|---|
| LABEL | box `traffic_light` có `state` và `relevance` |
| IGNORE | không có box. Kiểm được bằng số box / `peer_evidence` |
| UNKNOWN | `state=unknown` và/hoặc `relevance=unknown` |
| ESCALATE | tag `frame [ego_signal=escalate]` |

Tag `frame` còn cho biết kết luận cấp ảnh (`visible` / `out_of_view` / `none`). Blind test sẽ chấm các decision sau:
- số box và giá trị attribute của đèn ego;
- đèn đi bộ, đèn xa, bóng phản chiếu phải bị bỏ qua;
- giá trị tag `frame`;
- 2 decision geometry.

## Dữ liệu và giới hạn

- Nguồn là BDD100K, LISA và GTSDB. BDD là dashcam tổng quát: phần lớn trong 26 ảnh không có đèn hoặc đèn chỉ vài
  pixel, nên chỉ vài ảnh có đèn rõ. LISA là 1 clip 30 frame ở cùng một giao lộ lúc chạng vạng, có đèn mũi tên.
  GTSDB (phố Đức, 1360×800) có vài ảnh đầu đèn to, rõ và nhiều đầu đèn trong một cảnh.
- Dùng 15 ảnh: example 4 (BDD, LISA), calibration 7 (BDD 2, LISA 1, GTSDB 4), blind 4 (toàn BDD — cảnh chưa thấy).
- Ảnh BDD/LISA là giao lộ Mỹ; ảnh GTSDB là giao lộ Đức. Cả hai theo thứ tự ô dọc đỏ trên, vàng giữa, xanh dưới.
  Đức có thêm đèn nhắc lại gắn thấp trên cột và pha đỏ+vàng trước khi xanh — schema v1 chưa có giá trị riêng cho
  pha này; calibration sẽ quyết có cần thêm hay không.
- Ảnh tĩnh, không suy ra được đèn nhấp nháy hay đèn tắt: dùng `unknown`, không có giá trị `off`.
- Chỉ có 1 camera trước và không có HD map. Làn ego được suy ra từ vị trí camera, và mặc định ego đi thẳng.
