# So sánh traffic_sign

> Các số dưới đây là số của công cụ so sánh, không phải ngưỡng chấm.

Box GTSDB (43 class Đức). GT không có readable, truncated, relevant_to_ego — các attribute này tự đối chiếu bằng decision log.

Trùng từng đỉnh với reference: 0/22 shape (<= 0,5 px).

## 00026.png

- readable, truncated, relevant_to_ego: GT không có, không so — tự đối chiếu bằng decision log
- R1: bạn thiếu biển có trong reference — gợi ý `missing`
- B1: box bạn vẽ không có trong reference — gợi ý `guideline_gap`
- B2: box bạn vẽ không có trong reference — gợi ý `guideline_gap`

## 00054.png

- R2/B3: IoU 0.707
- R1/B2: IoU 0.663
- R1/B2: sign_class: bạn unknown, reference 27 pedestrian crossing — gợi ý `class`
- R3/B1: IoU 0.662
- readable, truncated, relevant_to_ego: GT không có, không so — tự đối chiếu bằng decision log
- R4: bạn thiếu biển có trong reference — gợi ý `missing`
- B4: box bạn vẽ không có trong reference — gợi ý `guideline_gap`

## 00073.png

- R1/B4: IoU 0.681
- R1/B4: sign_class: bạn unknown, reference 23 slippery road — gợi ý `class`
- R5/B2: IoU 0.610
- R5/B2: sign_class: bạn unknown, reference 02 speed limit 50 — gợi ý `class`
- readable, truncated, relevant_to_ego: GT không có, không so — tự đối chiếu bằng decision log
- R2: bạn thiếu biển có trong reference — gợi ý `missing`
- R3: bạn thiếu biển có trong reference — gợi ý `missing`
- R4: bạn thiếu biển có trong reference — gợi ý `missing`
- R6: bạn thiếu biển có trong reference — gợi ý `missing`
- B1: box bạn vẽ không có trong reference — gợi ý `guideline_gap`
- B3: box bạn vẽ không có trong reference — gợi ý `guideline_gap`
- B5: box bạn vẽ không có trong reference — gợi ý `guideline_gap`
- B6: box bạn vẽ không có trong reference — gợi ý `guideline_gap`

## 00088.png

- R2/B2: IoU 0.962
- R3/B1: IoU 0.886
- R3/B1: sign_class: bạn unknown, reference 08 speed limit 120 — gợi ý `class`
- R1/B4: IoU 0.796
- R4/B3: IoU 0.733
- R4/B3: sign_class: bạn unknown, reference 08 speed limit 120 — gợi ý `class`
- readable, truncated, relevant_to_ego: GT không có, không so — tự đối chiếu bằng decision log

## 00206.png

- R2/B4: IoU 0.897
- R4/B1: IoU 0.874
- R5/B3: IoU 0.850
- R1/B2: IoU 0.842
- R3/B5: IoU 0.692
- readable, truncated, relevant_to_ego: GT không có, không so — tự đối chiếu bằng decision log

## 00223.png

- R1/B1: IoU 0.794
- readable, truncated, relevant_to_ego: GT không có, không so — tự đối chiếu bằng decision log

## Dòng gợi ý cho comparison_log.csv

```csv
task,sample,object,difference,error_type,who_is_right,action,note
traffic_sign,00026.png,R1,R1: bạn thiếu biển có trong reference,missing,,,
traffic_sign,00026.png,B1,B1: box bạn vẽ không có trong reference,guideline_gap,,,
traffic_sign,00026.png,B2,B2: box bạn vẽ không có trong reference,guideline_gap,,,
traffic_sign,00054.png,R1/B2,"R1/B2: sign_class: bạn unknown, reference 27 pedestrian crossing",class,,,
traffic_sign,00054.png,R4,R4: bạn thiếu biển có trong reference,missing,,,
traffic_sign,00054.png,B4,B4: box bạn vẽ không có trong reference,guideline_gap,,,
traffic_sign,00073.png,R1/B4,"R1/B4: sign_class: bạn unknown, reference 23 slippery road",class,,,
traffic_sign,00073.png,R5/B2,"R5/B2: sign_class: bạn unknown, reference 02 speed limit 50",class,,,
traffic_sign,00073.png,R2,R2: bạn thiếu biển có trong reference,missing,,,
traffic_sign,00073.png,R3,R3: bạn thiếu biển có trong reference,missing,,,
traffic_sign,00073.png,R4,R4: bạn thiếu biển có trong reference,missing,,,
traffic_sign,00073.png,R6,R6: bạn thiếu biển có trong reference,missing,,,
traffic_sign,00073.png,B1,B1: box bạn vẽ không có trong reference,guideline_gap,,,
traffic_sign,00073.png,B3,B3: box bạn vẽ không có trong reference,guideline_gap,,,
traffic_sign,00073.png,B5,B5: box bạn vẽ không có trong reference,guideline_gap,,,
traffic_sign,00073.png,B6,B6: box bạn vẽ không có trong reference,guideline_gap,,,
traffic_sign,00088.png,R3/B1,"R3/B1: sign_class: bạn unknown, reference 08 speed limit 120",class,,,
traffic_sign,00088.png,R4/B3,"R4/B3: sign_class: bạn unknown, reference 08 speed limit 120",class,,,
```

Hãy điền `who_is_right`, `action`, `note`; loại lỗi chỉ là gợi ý.
