# Báo cáo QC — Nhóm Wolf

## 1. Mục đích, dữ liệu và phiên bản

- Mục đích và giới hạn sử dụng: Đặc tả và QC bộ nhãn 2D Bounding Box phục vụ bài toán phát hiện đối tượng giao thông đường phố đô thị hỗn hợp (người đi bộ và phương tiện cơ giới/thô sơ); giới hạn trên tập dữ liệu tuyển chọn 40 ảnh cho mục đích thử nghiệm và huấn luyện mô hình cơ sở, độ đại diện cho dữ liệu vận hành thời gian thực chưa được đánh giá đầy đủ.
- Nguồn và phép sử dụng dữ liệu: Toàn bộ 40 ảnh có nguồn gốc minh bạch từ Wikimedia Commons và các kho mở, tuân thủ giấy phép Creative Commons (CC BY-SA 4.0, CC BY 2.0, v.v.), được ghi nhận đầy đủ tác giả, bản quyền và đường link gốc tại data/ATTRIBUTION.csv.
- Manifest và checksum nhãn cuối: batch_manifest.json quản lý hồ sơ toàn vẹn; SHA-256 của nhãn cuối labels_calibrated.json là 9f1d2ebb529fb17855cb9692d8bd07c997dc7388347336edd4f51dffe5fbe4eb.
- Guideline và taxonomy khi gán / khi bàn giao: Khi gán độc lập ở Pha 3 dùng guideline v1.0 và taxonomy v1.0 đã khóa; khi bàn giao dùng guideline v1.1 calibrated và giữ nguyên taxonomy v1.0 (thay đổi loại editorial).

## 2. Phân công và đồng thuận trước calibration

- Lượt hoàn thành / được giao: 80/80 (100% lượt hoàn thành trên 40 ảnh, phân công gán mù đôi K=2 giữa thành viên 225 và 246 theo assignment.csv).
- Số cặp box ghép, IoU TB, đồng thuận lớp và mẫu số: 198 cặp box ghép được (IoU >= 0.5), IoU trung bình đạt 0.792, độ đồng thuận lớp đạt 98.5% trên mẫu số 198 cặp box ghép.
- Box chưa ghép, loại do ignore, bất đồng vùng ignore: 228 box chưa ghép của 225, 72 box chưa ghép của 246; 0 box bỏ qua do ignore; 52 ca bất đồng vùng ignore_region.
- Ca hai người có thể cùng sai sau khi đối chiếu tham chiếu: Ảnh img_017.jpg (tượng nghệ thuật hình người) và img_027.jpg (tượng điêu khắc xe tải), cần đối chiếu điều khoản loại trừ R-05; cả hai thành viên đều nhận diện chính xác và không vẽ box (0 annotation).

## 3. Calibration và kiểm lại

- Ca đã xử lý, rule ID, bản sửa và người xác nhận: Đã xử lý toàn bộ 568 ca bất thường trong calibration_decisions.csv, áp dụng các mã quy tắc R-01 đến R-09; bản sửa được chốt bởi 225 và được 246 kiểm lại, xác nhận 100%.
- Tác động v1.1 lên nhãn và ảnh: Hiệu chuẩn trên 37 ảnh bị gắn cờ; giữ nguyên 100% taxonomy và các quy tắc v1.0; chuẩn hóa các đối tượng bỏ sót của 246 theo nhãn hợp lệ của 225.
- Tham chiếu bằng chứng khi Rework: Vòng 1 đạt chuẩn Release nên không cần mở vòng Rework; toàn bộ bằng chứng calibration được lưu tại submission/qc/calibration_decisions.csv và submission/qc/labels_calibrated_251b3167dbcb205b.json.

## 4. Kết quả duyệt

- Vòng QC: 1
- Người review và ngày: 246, 17/11/2026
- Đã duyệt / cả lô: 40/40
- Ảnh lỗi / ảnh đã duyệt: 0/40 = 0.0%
- Annotation đối tượng hiện có bị lỗi / annotation đối tượng đã duyệt: 0/426 = 0.0%
- Vật bỏ sót, ảnh và vùng: 0 vật bỏ sót trên 40 ảnh đã duyệt
- Ca khó / phần còn lại, lỗi lặp và ví dụ: Đã duyệt 37/37 ảnh ca khó (ảnh lỗi 0/37 = 0.0%) và 3/3 ảnh còn lại (ảnh lỗi 0/3 = 0.0%); không phát hiện lỗi lặp lại; phiếu chi tiết tại submission/qc/reviews/round-01/qc_review.csv.
- Một lỗi máy chặn được và một lỗi cần người đọc ảnh: Lỗi máy chặn được: bbox vượt biên ảnh hoặc thiếu thuộc tính bắt buộc (scripts/validate_labels.py); lỗi cần người đọc ảnh: phân biệt tượng điêu khắc hoặc tranh vẽ 2D để tránh nhận nhầm thành vật thật (R-05).

## 5. Quyết định bàn giao

- Quyết định: Release
- Lý do, người nhận và phạm vi sử dụng: Tỷ lệ ảnh lỗi đạt 0.0% (<= 2.0%), tỷ lệ đối tượng lỗi 0.0%, 0 lỗi lặp lại, 0 cờ can_review còn tồn đọng; bàn giao cho đội ngũ kỹ sư phát triển mô hình thị giác máy tính ADAS/camera giám sát giao thông đô thị.
- Nếu còn việc: Không còn việc tồn đọng; toàn bộ quy trình từ Pha 1 đến Pha 6 đã hoàn tất và kiểm tra đạt 100%.
- Kiểm ngược từ báo cáo tới ảnh, rule và nhãn: Ca C001 (ảnh img_001.jpg): quy tắc R-01 (Visible Pixels Only) và R-04 (Oversized Cargo), box truck [226, 99, 734, 541] ôm sát cabin và thùng xe chở hàng, thuộc tính occluded_level=1, truncated=no, can_review=false; kiểm tra trùng khớp 100% trong labels_calibrated.json với ID annotation #1.
