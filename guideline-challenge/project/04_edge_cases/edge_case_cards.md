# Edge-case library

Kho nội bộ của nhóm, **không gửi cho peer**. Rule và ví dụ từ card dùng ảnh example/calibration đã được chép sang
`02_guideline.md` (mục 5, 7, 9). Card dùng ảnh blind (EC09–EC12) chỉ nằm ở đây; decision tương ứng có trong
`gold_decisions.csv`.

Card nào ghi **"dự kiến"** (EC04–EC08, EC13, EC14) thì phải chốt lại sau calibration (120–140') và cập nhật Expected theo consensus.

---

CASE ID: EC01
Sample: BDD02
Scene: Ngã tư đô thị NYC, ban ngày, nhiều đầu đèn gắn trên cột và cần vươn
Observation: 4 đầu đèn xanh quay mặt về ego. Có 2 vỏ vàng nhìn ngang (thấy mép mái che hình răng lược), hộp đèn đi bộ trắng/vàng trên cột phải, và đèn tí hon ở giao lộ sau (~582,245)
Decision: LABEL 4 đầu đèn; IGNORE phần còn lại
Expected: traffic_light [relevance=ego;state=green]×4 · frame [ego_signal=visible]
Rationale: Planner chỉ cần đèn ego. Vỏ quay ngang không cho biết màu với ego; đèn giao lộ sau gây nhiễu (contract Q2)
Common mistake: Box cả vỏ quay ngang (đếm 6 thay vì 4); box đèn tí hon ở giao lộ sau
Diversity: conflict (nhiều nguồn) · small_far

---

CASE ID: EC02
Sample: LISA01
Scene: Giao lộ California lúc chạng vạng; cần vươn mang đầu đèn mũi tên trái và đầu đèn tròn
Observation: Mũi tên trái sáng ở ô trên (đỏ-cam), đèn tròn sáng ô trên, đầu đèn phụ bên phải cũng sáng ô trên. Có 3 đèn thấp ở xa
Decision: LABEL 3 đầu đèn trên cao; IGNORE 3 đèn xa (< 1/3)
Expected: đèn tròn giữa và phải: [relevance=ego;state=red]; mũi tên: [relevance=other;state=red] · frame visible
Rationale: Ego mặc định đi thẳng, nên mũi tên phục vụ làn rẽ. Lúc chạng vạng màu đỏ trông như cam; xác định màu theo vị trí ô sáng
Common mistake: Chọn state=yellow vì màu trông cam; gán mũi tên relevance=ego
Diversity: conflict · low_visibility · ambiguity semantics (mũi tên)

---

CASE ID: EC03
Sample: BDD11
Scene: Ngã tư khu dân cư, ban ngày
Observation: Chỉ có đèn đi bộ bàn tay cam ở góc phải (~1210,260); không thấy đầu đèn xe nào quay về ego
Decision: IGNORE đèn đi bộ; frame = out_of_view
Expected: 0 box traffic_light · frame [ego_signal=out_of_view]
Rationale: Box đèn đi bộ thành đèn ego đỏ gây phantom braking. out_of_view báo cho planner rằng giao lộ có đèn nhưng không thấy đèn ego
Common mistake: Box bàn tay cam thành traffic_light [state=red;relevance=ego]; chọn none thay vì out_of_view
Diversity: **critical-risk** · negative

---

CASE ID: EC04
Sample: BDD18
Scene: Ban đêm, đường đô thị
Observation: Đèn đi bộ bên phải (bàn tay cam + người trắng). Vài đốm xanh nhỏ ở xa (~585,305; ~660,305). Không có đầu đèn lớn nào; không định vị được giao lộ đầu tiên
Decision: LABEL đốm xanh (nhánh "chỉ có đèn xa" của 5.3) với relevance=unknown → ESCALATE (dự kiến)
Expected: traffic_light [relevance=unknown;state=green]×2 · frame [ego_signal=escalate] (dự kiến, chốt sau calibration)
Rationale: Không đủ bằng chứng để khẳng định đèn xanh xa điều khiển ego. Đoán "ego green" là rủi ro chạy qua giao lộ khi chưa rõ tín hiệu
Common mistake: Gán ego green; hoặc box đèn đi bộ; hoặc chọn out_of_view và bỏ đèn xanh
Diversity: **escalation** · low_visibility · small_far

---

CASE ID: EC05
Sample: BDD25
Scene: Đại lộ Midtown lúc chạng vạng, đường ướt, đèn xanh ở nhiều giao lộ liên tiếp
Observation: Đầu đèn xanh ở bên phải (~830,242; ~865,262) và ở giữa xa (~580,255); tất cả đều nhỏ. Đường ướt phản chiếu đèn
Decision: LABEL đèn ở giao lộ gần nhất; đèn xa hơn IGNORE hoặc unknown theo rule 1/3 (dự kiến)
Expected: (dự kiến) 2 đầu đèn bên phải [relevance=ego;state=green]; đèn giữa xa IGNORE nếu < 1/3 · frame visible. Không box phản chiếu trên mặt đường
Rationale: Contract yêu cầu đèn ở vạch dừng kế tiếp, không phải đèn ở các block sau
Common mistake: Box tất cả đèn xanh nhìn thấy và gán ego; box phản chiếu trên mặt đường ướt
Diversity: ambiguity · small_far · low_visibility

---

CASE ID: EC06
Sample: GTS14
Scene: Phố Đức, ban ngày, trời trắng
Observation: Đầu đèn trên cần vươn giữa (~611,262) sáng **ô trên** màu cam-vàng; đầu đèn thấp trên cột phải (~742,403) và đầu đèn trái xa (~283,403) cũng sáng ô trên
Decision: LABEL đầu đèn cần vươn và đầu đèn cột phải; đèn trái xa theo rule 1/3 (dự kiến)
Expected: (dự kiến) [relevance=ego;state=red]×2 · frame visible. Nếu calibration thấy có ô giữa cùng sáng (pha đỏ+vàng của Đức) thì cần rule mới — chốt sau calibration
Rationale: Màu nhìn như vàng nhưng vị trí ô sáng là ô trên = đỏ (mục 4). Schema chưa có giá trị cho pha đỏ+vàng
Common mistake: Chọn state=yellow theo màu nhìn thấy; box cả cần treo
Diversity: ambiguity (state) · edge

---

CASE ID: EC07
Sample: LISA30
Scene: Cùng giao lộ LISA01, 29 frame sau
Observation: Hai đèn tròn đã chuyển xanh; mũi tên trái vẫn đỏ
Decision: LABEL; frame = visible (không escalate)
Expected: đèn tròn [relevance=ego;state=green]×2; mũi tên [relevance=other;state=red] · frame visible
Rationale: Mũi tên là other, nên không tính vào điều kiện "≥ 2 đèn ego khác màu" của bảng frame
Common mistake: Thấy đỏ và xanh cùng lúc rồi chọn escalate; gán mũi tên ego
Diversity: conflict · temporal

---

CASE ID: EC08
Sample: GTS02
Scene: Giao lộ Đức dưới gầm cầu đường sắt, ban ngày
Observation: Cột trái có đầu đèn sáng xanh (~88,270) dưới biển bắt buộc rẽ trái; cột phải có đầu đèn sáng ô trên (~1200,278) dưới biển bắt buộc rẽ phải; 3 đầu đèn trên giá ngang (~505,330; ~638,335; ~733,342) chỉ thấy mặt lưng/tấm nền; đầu đèn treo trên cùng (~822,20) nhìn lưng
Decision: LABEL 2 đầu đèn có ô sáng; IGNORE các đầu đèn chỉ thấy lưng (5.2). Relevance của 2 đầu đèn là chỗ dự kiến bất đồng (dự kiến)
Expected: (dự kiến) xanh trái và đỏ phải đều relevance=ego → ≥ 2 box ego khác màu → frame [ego_signal=escalate]; hoặc một bên là other nếu nhóm chốt rule theo biển hướng đi — chốt sau calibration
Rationale: Ego không biết rẽ hướng nào; hai đầu đèn khác màu gắn với biển hướng khác nhau là xung đột thật, không nên đoán
Common mistake: Box các đầu đèn nhìn lưng trên giá ngang; chọn visible theo đèn xanh và bỏ qua đèn đỏ
Diversity: **conflict** · **escalation** · ambiguity

---

CASE ID: EC09
Sample: BDD26 (blind)
Scene: Ban đêm, đại lộ có dải phân cách
Observation: Đầu đèn gần trên cột dải phân cách trái sáng xanh (~487,71). Ngay dưới là đốm cam hình bàn tay (~437,118). Đèn đỏ/vàng tí hon gần điểm tụ (~632,222; ~667,217)
Decision: LABEL đèn gần; IGNORE đèn đi bộ và đèn xa
Expected: traffic_light [relevance=ego;state=green]; không có box ego nào mang red/yellow · frame visible
Rationale: Critical: nếu lấy đèn đỏ xa hoặc đèn đi bộ làm đèn ego, planner sẽ phanh sai trong khi ego đang có đèn xanh
Common mistake: Box đèn đỏ xa với relevance=ego; box bàn tay cam thành red
Diversity: **critical** · low_visibility · small_far

---

CASE ID: EC10
Sample: BDD12 (blind)
Scene: Ngã tư Queens cạnh trạm xăng, trời âm u
Observation: Đèn đi bộ bàn tay đỏ-cam rất rõ (~1090,140). Cạnh đó là vỏ đèn nhìn ngang/lưng. Không có đầu đèn xe nào quay về ego
Decision: IGNORE tất cả; frame = out_of_view
Expected: 0 box traffic_light · frame [ego_signal=out_of_view]
Rationale: Critical: bàn tay đỏ là tín hiệu nổi bật nhất ảnh, rất dễ bị box thành đèn ego đỏ
Common mistake: Box bàn tay thành traffic_light [state=red;relevance=ego]; chọn frame visible hoặc none
Diversity: **critical** · negative

---

CASE ID: EC11
Sample: BDD21 (blind)
Scene: Parkway ban ngày; bên phải là hàng rào và đường song song
Observation: 1 đầu đèn xanh trên cần vươn (~336,295). Đèn xanh tí hon bên trái (~226,320). Hai khối vàng-cam bên trái (~57/75,345). Vật đỏ sau tán cây bên kia hàng rào (~1150–1210,313)
Decision: LABEL đèn cần vươn; IGNORE đèn tí hon và khối vàng-cam; vật đỏ không được là ego
Expected: traffic_light [relevance=ego;state=green] ×1 duy nhất ở relevance=ego · frame visible
Rationale: Đèn thuộc đường khác (sau hàng rào) là other. Nếu nhầm thì tạo xung đột giả, dẫn tới escalate hoặc phanh sai
Common mistake: Gán vật đỏ bên phải relevance=ego rồi escalate; box khối vàng-cam
Diversity: **conflict** · small_far · occlusion

---

CASE ID: EC12
Sample: BDD07 (blind)
Scene: Brooklyn, ban ngày; capo xe bóng phản chiếu cả cảnh
Observation: Mỗi cần vươn mang 1 đầu đèn quay về ego (xanh) và 1 vỏ quay ngang. Hộp đèn đi bộ bị nhìn nghiêng. Bóng đèn phản chiếu trên capo (~245,625; ~695,600)
Decision: LABEL 2 đầu đèn; IGNORE vỏ quay ngang, đèn đi bộ và phản chiếu
Expected: traffic_light [relevance=ego;state=green]×2 · frame visible · geometry đầu đèn trái ≈ x230–240 y126–158
Rationale: Phản chiếu và vỏ quay ngang tạo false positive cho detector
Common mistake: Box ôm cả 2 vỏ (trước + ngang) vào một box; box phản chiếu
Diversity: normal · reflection

---

CASE ID: EC13
Sample: GTS24
Scene: Phố Đức có đường ray tàu điện, ban ngày
Observation: Đầu đèn treo trên cần vươn (~818,80) sáng **mũi tên đi thẳng** xanh; cột phải có 2 đầu đèn: trên sáng ô vàng nhỏ (~1045,290), dưới sáng mũi tên thẳng xanh (~1055,375); đầu đèn bên trái (~195,170) nhìn nghiêng, không thấy ô
Decision: LABEL đầu đèn mũi tên thẳng (ego đi thẳng → 5.4c thỏa); đầu đèn có ô vàng nhỏ là chỗ dự kiến bất đồng; IGNORE đầu đèn nhìn nghiêng
Expected: (dự kiến) mũi tên thẳng [relevance=ego;state=green]×2 · frame visible — chốt sau calibration
Rationale: Mũi tên chỉ loại relevance=ego khi nó phục vụ làn ego không đi (5.4c); mũi tên thẳng là của ego
Common mistake: Gán mọi mũi tên relevance=other; box đầu đèn nhìn nghiêng
Diversity: ambiguity semantics (mũi tên) · edge

---

CASE ID: EC14
Sample: GTS11
Scene: Đường phố Đức, mùa thu, ban ngày
Observation: Đầu đèn trên cần vươn giữa (~607,215) sáng xanh; đèn nhắc lại thấp trên cột phải (~778,400) cũng sáng xanh; đầu đèn trên cần vươn trái (~345,228) chỉ thấy lưng; đầu đèn thấp bên trái (~180,390) nhỏ
Decision: LABEL 2 đầu đèn xanh; IGNORE đầu đèn nhìn lưng; đầu đèn thấp bên trái theo 5.2/5.3 (dự kiến)
Expected: (dự kiến) [relevance=ego;state=green]×2 · frame visible
Rationale: Đèn nhắc lại cùng màu với đèn chính không phải xung đột; đầu đèn nhìn lưng không cho biết màu
Common mistake: Box đầu đèn nhìn lưng với state=unknown rồi escalate; bỏ đèn nhắc lại thấp
Diversity: conflict (nhiều đầu đèn) · small_far

---
