# Tóm tắt IAA (taxonomy v1.0)

- Ảnh được so sánh (có >=2 người gán): **40**; số người gán: 2
- Lượt gán đủ **80/80**.
- IoU trung bình và % cùng lớp **chỉ tính trên các cặp box đã ghép** (IoU >= 0.5, ghép 1-1 không phân biệt lớp). Box chưa ghép được thống kê riêng ở cột bỏ sót/thừa.
- Mẫu số: 198 cặp box ghép (tổng mọi cặp người gán, ảnh chung).

| Cặp | Ảnh | Cặp box ghép | IoU TB | Cùng lớp | Cùng occluded_level | Cùng truncated | Chưa ghép (A/B) | Bỏ qua do ignore | Đạt IAA |
|---|---|---|---|---|---|---|---|---|---|
| **TỔNG** | 40 | 198 | 0.792 | 98.5% | 47.0% | 86.9% | 228/72 | 0 | CHƯA ĐẠT |
| 225|246 | 40 | 198 | 0.792 | 98.5% | 47.0% | 86.9% | 228/72 | 0 | CHƯA ĐẠT |

Ngưỡng IAA: IoU TB >= 0.85, đồng thuận lớp >= 95%.

Số ca bất thường: 568 ({'bo_sot_thua': 280, 'iou_thap': 117, 'sai_thuoc_tinh': 116, 'bat_dong_ignore': 52, 'sai_lop': 3}), trên 37 ảnh cần calibration.
