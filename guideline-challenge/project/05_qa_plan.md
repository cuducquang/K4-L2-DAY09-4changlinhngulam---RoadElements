# QA plan + quality gates

## Flow

Guideline → Calibration → Production → Self-QC → Review → Rework → Quality Gate. Cụ thể cho project này:

- **Ai review, review bao nhiêu:** QA owner review, người review phải khác người label.
  - Review **100%** các ảnh có tag rủi ro:
    - ảnh có ≥ 1 box `relevance=ego`;
    - ảnh `frame` = `escalate` hoặc `out_of_view`;
    - ảnh có tag low_visibility (đêm, chạng vạng, mưa).
  - Review **20% ngẫu nhiên** các ảnh còn lại (`none`, chỉ có `other`), tối thiểu 5 ảnh cho mỗi annotator.
- **Chọn sample theo rule nào:** ưu tiên theo rủi ro như trên.
  - Annotator mới: review 100% trong 50 ảnh đầu.
  - Mỗi lô đều chạy **auto-check** trước khi review bằng mắt:
    - (a) mỗi ảnh có đúng 1 tag `frame`, không còn `__undefined__`;
    - (b) `frame=visible` ⇔ có ≥ 1 box `ego` mang màu rõ;
    - (c) `frame=out_of_view`/`none` ⇒ không có box `ego`;
    - (d) có ≥ 2 box `ego` khác màu ⇒ `frame=escalate`.
- **Issue được ghi ở đâu, đóng thế nào:**
  - Mỗi lỗi là một dòng issue (sample_id, object, severity, rule vi phạm), ghi trong issue tracker của repo hoặc CVAT
    Issue.
  - Annotator sửa, reviewer xác nhận rồi mới đóng.
  - Câu hỏi thì vào `clarification_log`.
- **Khi phát hiện guideline gap thì update và version ra sao:**
  - Gap thì sửa `02_guideline.md` và tăng version (v2 → v3…).
  - Ghi một dòng vào `08_revision_log.md` kèm bằng chứng (sample_id).
  - Dán lại Guide vào CVAT, rồi re-review các ảnh đã label trước đó có liên quan tới rule vừa đổi.

## Defect severity

| Severity | Định nghĩa cho project này | Ví dụ | Action mặc định |
|---|---|---|---|
| Critical | Sai kết luận về đèn điều khiển ego: sai `state` của box ego; sai `relevance` ego ↔ other/không box với đèn ego; box đèn đi bộ, đèn giao lộ sau hoặc phản chiếu với `relevance=ego`; sai `frame` giữa visible và out_of_view/none | Bàn tay cam ở BDD11 bị box `[ego;red]`; đèn đỏ xa ở cảnh kiểu BDD26 bị gán ego | Reject ảnh. Rework toàn bộ ảnh cùng annotator có cùng pattern. Nếu nguyên nhân là guideline gap thì dừng lô và sửa rule |
| Major | Sai decision nhưng không đổi kết luận của ego: thiếu hoặc thừa box `other`; box vỏ quay ngang hay đèn xa không mang relevance=ego; sai `state` của box `other`; dùng `unknown` hoặc `escalate` khi rule đã quyết được | Box vỏ quay ngang ở BDD02; mũi tên LISA01 bị chọn `unknown` | Rework ảnh; coaching nếu một annotator lặp ≥ 3 lần |
| Minor | Geometry vượt tolerance nhưng đúng object; box ôm cả backplate hoặc quầng sáng | Box đầu đèn lệch 6 px | Sửa khi rework; ghi thống kê |
| Question | Case không có rule hoặc rule mâu thuẫn | Đèn tạm trên giá di động ở công trường | Ghi `clarification_log`. QA owner trả lời trong 24h. Lặp ≥ 2 lần thì thêm rule |

## Metrics

| Metric | Cách tính | Vì sao phù hợp với bài toán |
|---|---|---|
| Ego-decision accuracy | Số ảnh có (`frame` đúng và mọi box ego đúng state + relevance) / số ảnh được review | Đây chính là tín hiệu planner dùng |
| Critical defect escape rate | Lỗi critical tìm thấy ở vòng audit sau Quality Gate / tổng ảnh | Đo rủi ro còn lọt vào dataset |
| Box precision / recall (đèn trong scope) | So với box của reviewer, match khi IoU ≥ 0.5 | Detector cần đủ box và không có box rác (đèn đi bộ, phản chiếu) |
| Geometry compliance | % box có mọi cạnh trong tolerance max(2 px, 15% h) | Đèn nhỏ, nên lệch vài px đã làm hỏng crop cho classifier |
| Relevance agreement | % đồng thuận giữa 2 annotator trên ảnh calibration hoặc mẫu double-label 10% | Relevance là attribute chủ quan nhất |
| Unknown/escalate rate | % box `unknown` và % ảnh `escalate` | Quá cao nghĩa là guideline thiếu hoặc annotator né tránh; bằng 0 ở ảnh đêm/mưa thì đáng ngờ là đang đoán |

Metric high-risk tách riêng: **critical defect escape rate** và **ego-decision accuracy trên subset low_visibility**
(đêm, chạng vạng, mưa), báo cáo riêng khỏi tập ban ngày.

## Quality gate

Threshold là đề xuất của nhóm, không phải chuẩn ngành.

```text
PASS if:
  0 critical defect trong mẫu review
  AND ego-decision accuracy ≥ 98%
  AND major defect ≤ 5% số box
  AND geometry compliance ≥ 90%
  AND auto-check (a)–(d) pass 100%
  AND escalate rate ≤ 15% ảnh
REWORK if: major > 5% OR geometry < 90% OR auto-check fail OR unknown rate > 25% box
REJECT / ESCALATE if: ≥ 1 critical defect do guideline gap, OR ≥ 2 critical defect do execution trong cùng lô
  → dừng lô, sửa guideline, calibration lại trước khi label tiếp
```

Trade-off:
- Review 100% ảnh có đèn ego thì tốn công. Nhưng lỗi ở đây trực tiếp làm sai quyết định dừng/đi, nên đáng chi phí.
- Ảnh negative rủi ro thấp nên chỉ lấy mẫu 20%.
- Ngưỡng escalate ≤ 15% chấp nhận tốn thêm thời gian của reviewer, để đổi lấy việc không có nhãn đoán mò ở đèn ego.
