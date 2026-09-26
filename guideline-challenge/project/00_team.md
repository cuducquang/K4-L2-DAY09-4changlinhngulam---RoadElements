# Team

Điền trước phút 15. Thay mọi placeholder; còn sót thì `make status` báo ở gate G1.

- **Team:** `4changlinhngulam`
- **Nhóm peer test bài của mình:** chưa chốt trong repo này. Blind handoff trong repo cá nhân của Đạt do nhóm `1000`
  (Nguyễn Chí Bằng) label; cần Lab Coach xác nhận cặp chính thức rồi cập nhật dòng này.
- **Nhóm mình test bài của:** chưa chốt (chờ Lab Coach công bố cặp/ring).
- **Problem family:** Traffic light — trạng thái + ego relevance tại giao lộ nhiều đầu đèn ("Đèn nào điều khiển xe mình?")
- **Nguồn ảnh:** `bdd100k` (example/calibration/blind) + `lisa` và `gtsdb` (chỉ example/calibration)

| Thành viên | GitHub | Vai trò chính | File phụ trách |
|---|---|---|---|
| Cù Đức Quang (2A202602188) — nhóm trưởng | [cuducquang](https://github.com/cuducquang) | QA owner | `05_qa_plan.md`, `06_calibration_report.csv`, `07_blind_handoff/` |
| Nguyễn Trọng Thắng (2A202602169) | [trogthang](https://github.com/trogthang) | Spec owner | `01_problem_statement.md`, `02_guideline.md` |
| Đoàn Vĩnh Nguyên (2A202602201) | [everythinggoeson711](https://github.com/everythinggoeson711) | CVAT owner | `03_*`, `sample_pack.csv`, `09_cvat_export_or_task_reference.txt` |
| Hoàng Văn Đạt (2A202602267) | [dathoangdev3-dev](https://github.com/dathoangdev3-dev) | Gold owner | `04_edge_cases/`, `08_revision_log.md` |

Repo cá nhân của từng thành viên: xem [`TEAMMATES.md`](../../TEAMMATES.md).

Gợi ý chia vai (nhóm 2–3 người thì gộp): **spec owner** (`01`, `02`), **CVAT owner** (`03_*`, `sample_pack.csv`,
`09`), **gold owner** (`04_edge_cases/`), **QA owner** (`05`, `06`, `07_blind_handoff/`). Mỗi file một người sửa
chính để tránh xung đột git. Calibration thì mọi người cùng label.
