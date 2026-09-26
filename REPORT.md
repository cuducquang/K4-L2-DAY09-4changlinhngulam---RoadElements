# Báo cáo tiến độ Lab 9 — Guideline Design Challenge (nhóm 4changlinhngulam)

Cập nhật: 26/9/2026. Topic: **"Đèn nào điều khiển xe mình?"**, tức trạng thái đèn và ego relevance tại giao lộ có
nhiều đầu đèn. Bài nhóm nằm trong [`guideline-challenge/project/`](guideline-challenge/project/). Danh sách thành viên
và repo cá nhân: [`TEAMMATES.md`](TEAMMATES.md).

## Bảng gate (`python lab9.py status`)

| Gate | Trạng thái | Việc còn thiếu |
|---|---|---|
| G1 · Topic lock | ✓ | — |
| G2 · CVAT ready | ✓ | — |
| G3 · Calibration done | chưa | Cần ≥ 2 export calibration độc lập (hiện mới có `quang.zip`), rồi `calib`, `06_calibration_report.csv` và guideline v2 |
| G4 · Gold frozen | chưa | Gold owner (Đạt) chạy `freeze` sau khi có v2 |
| G5 · Handoff complete | chưa | `handoff` gửi peer, nhận export của peer, `score`, `clarification_log.csv`, `peer_feedback.md` |
| G6 · Final handoff | chưa | Guideline v3, `gts`, dòng v3 trong `08_revision_log.md` |

## Đã làm

- **Topic, spec, ontology:** `01_problem_statement.md`, `02_guideline.md` (v1), `03_ontology_and_cvat_setup.md`,
  `03_cvat_labels.json`, `sample_pack.csv`. Split gồm 4 example, 7 calibration và 4 blind.
- **Ảnh calibration:** bộ calibration đã được thay bằng 7 ảnh thấy rõ đèn (BDD18, BDD25, LISA30, GTS02, GTS11, GTS14,
  GTS24). Trong đó GTS02, GTS14 và BDD18 cố ý chọn khó: đèn trái xanh còn đèn phải đỏ, LED đỏ cháy sáng thành vàng, và
  đốm xanh xa ban đêm.
- **CVAT:** dùng CVAT 2.75.0 chạy local. Task `4changlinhngulam-calib-quang` (id 32) đã dán guideline v1 vào phần
  Guide. Export CVAT for images 1.1 nằm ở `project/06_calibration_exports/quang.zip`: 22 box `traffic_light` và 7 tag
  `frame`. Chi tiết ở `09_cvat_export_or_task_reference.txt`.
- **Gold và QA:** có sẵn `gold_decisions.csv` (16 dòng), `edge_case_cards.md` (14 card) và `05_qa_plan.md`.

## Quan sát khi label calibration theo v1 (đầu vào cho v2)

| Ảnh | Kết quả theo v1 | Điểm guideline chưa rõ |
|---|---|---|
| GTS02 | Đèn cột trái xanh (ego), đèn cột phải đỏ (ego) → `escalate` theo rule 1 | Hai đèn ở hai phía gắn với biển bắt buộc rẽ trái / rẽ phải. v1 chỉ xét mũi tên trên đèn (5.4c), không xét biển đi kèm. Giàn đèn dưới cầu quay mặt về ego nhưng không ô nào sáng, nên phải chọn `state=unknown`, `relevance=unknown` |
| GTS14 | Cả 3 đầu đèn sáng ô trên, lõi vàng viền đỏ → `red` | Quy tắc "vị trí ô sáng quyết định" mới viết cho ban đêm và chạng vạng; cần mở rộng cho LED cháy sáng ban ngày |
| GTS24 | Mũi tên thẳng xanh (ego) ×2; đầu đèn trên cột phải quay chéo, sáng vàng → `other` | Chưa có ngưỡng cho "quay chéo rõ rệt" |
| BDD18 | 2 đốm xanh xa, `relevance=unknown` → `escalate` theo rule 2 | Đúng thiết kế; cần đối chiếu với các annotator khác |

Các điểm này chỉ là quan sát của một annotator. Chúng sẽ thành dòng trong `06_calibration_report.csv` khi có export
của thành viên khác để đo bất đồng.

## Còn lại (theo người phụ trách trong `00_team.md`)

1. Thắng, Nguyên, Đạt: mỗi người label độc lập 7 ảnh calibration trên CVAT với guideline v1, export CVAT for images
   1.1 và đặt vào `project/06_calibration_exports/<tên>.zip`. Không mở `edge_case_cards.md` trước khi label.
2. QA owner (Quang): chạy `python lab9.py calib project/06_calibration_exports/*.zip`, viết
   `06_calibration_report.csv` (≥ 3 dòng), nâng guideline lên v2 và thêm dòng v2 trong `08_revision_log.md`.
3. Gold owner (Đạt): chạy `python lab9.py freeze`, rồi `git push --follow-tags`.
4. `python lab9.py handoff`, gửi `blind-pack.zip` cho nhóm peer. Nhận export của peer, rồi chạy `score` và `gts`.
5. Guideline v3, thêm dòng v3 trong revision log, chạy `python lab9.py check`, rồi push.
6. Mini lab cá nhân (bước 2–3): Quang, Thắng, Nguyên chưa có submission (xem `TEAMMATES.md`).
