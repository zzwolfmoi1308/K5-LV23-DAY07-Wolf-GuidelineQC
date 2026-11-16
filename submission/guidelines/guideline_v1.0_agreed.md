# Guideline gán nhãn — v1.0 — Nhóm Wolf

> **Đặc tả gán nhãn 2D chính thức đã thống nhất giữa các thành viên nhóm Wolf (03225 & 03246) và KHÓA CỨNG cho lượt gán độc lập ở Pha 3.**

- Phiên bản: `v1.0` (Bản thống nhất nhóm đã khóa)
- Ngày chốt: 16/11/2026
- Nhóm: Wolf (Mã thành viên: `03225`, `03246`)
- Người duyệt: Cả nhóm (`03225, 03246`)
- Bộ Taxonomy tương ứng: `submission/taxonomy.json` (`version: v1.0`)
- Công cụ áp dụng: CVAT, xuất dữ liệu chuẩn COCO 1.0 trên tập 40 ảnh (`img_001.jpg` – `img_040.jpg`)

---

## 1. Mục tiêu và phạm vi sử dụng mô hình 2D

- **Mục đích mô hình:** Bộ dữ liệu được gán nhãn 2D Bounding Box để huấn luyện mô hình thị giác máy tính phát hiện đối tượng giao thông đường phố (người đi bộ và các loại phương tiện lưu thông phổ biến) phục vụ hệ thống camera giám sát đô thị thông minh và hệ thống trợ lái xe tự hành (ADAS/Autonomous Driving) trong điều kiện giao thông hỗn hợp.
- **Rủi ro sai lệch:**
  - *False Negative (Bỏ sót vật):* Cực kỳ nguy hiểm đối với các đối tượng dễ bị tổn thương như người đi bộ, người đi xe đạp, xích lô, xe máy hoặc xe tải lớn đang lưu thông; có thể gây ra tai nạn hoặc va chạm trực tiếp.
  - *False Positive (Nhận diện nhầm):* Gây cảnh báo ảo hoặc phanh gấp nguy hiểm nếu mô hình nhận nhầm hình in 2D trên biển quảng cáo, tượng điêu khắc nghệ thuật, ma-nơ-canh hoặc bóng xe phản chiếu trong gương chiếu hậu là chướng ngại vật thật trên lòng đường.
- **Tiêu chuẩn chất lượng:** Bounding box 2D phải bao trọn và ôm khít toàn bộ phần nhìn thấy (visible pixels) của đối tượng thực tế; không phỏng đoán phần khuất không có căn cứ; không bao thừa vùng không gian nền xung quanh.

---

## 2. Lớp và thuộc tính

### 2.1. Danh mục lớp đối tượng chính thức (8 lớp đối tượng + 1 vùng bỏ qua)

Áp dụng phép thử: *"Mô hình phát hiện vật 2D có phải đoán ra loại phương tiện này khi suy luận thực tế không?"* ➔ Nhóm thống nhất mở rộng taxonomy thành 8 lớp đối tượng cụ thể:

| Lớp | Định nghĩa chuẩn 2D | Ví dụ cụ thể trong 40 ảnh | Phản ví dụ — KHÔNG thuộc lớp này |
|---|---|---|---|
| `car` | Ô tô con từ 4 đến 7 chỗ ngồi (sedan, SUV, hatchback, coupe, wagon, taxi) dùng chở người. | Ô tô con SUV trong mưa (`img_015.jpg`), ô tô con wagon (`img_024.jpg`), các ô tô con được vận chuyển trên giàn xe tải (`img_002.jpg`), ô tô con sedan và taxi (`img_016.jpg`, `img_040.jpg`). | Xe tải nhẹ chở hàng (`img_001.jpg`, `img_036.jpg`), mô hình đồ chơi trong tủ trưng bày (`img_038.jpg` — bỏ qua không gán), xe mô hình diễu hành (`img_019.jpg`), tác phẩm điêu khắc hình xe (`img_027.jpg`), hình ảnh xe phản chiếu qua gương chiếu hậu (`img_020.jpg`). |
| `truck` | Xe tải chở hàng, xe tải thùng kín/lửng/bạt, xe bồn, xe đầu kéo rơ-moóc, xe bán tải cabin riêng thùng chở hàng. | Xe tải nhẹ chở hàng (`img_001.jpg`), xe tải chuyên dụng chở ô tô (`img_002.jpg`), xe tải thùng kín (`img_007.jpg`), xe tải nhẹ đầu bằng (`img_036.jpg`), xe bán tải chở hàng (`img_012.jpg`), xe bán tải gắn nắp thùng cao (`img_037.jpg`). | Xe buýt chở khách (→ `bus`), xe van thân liền khối (→ `van`), tác phẩm điêu khắc hình xe tải (`img_027.jpg`). |
| `van` | Xe thùng kín cỡ vừa/nhỏ, thân xe liền khối một khối giữa khoang lái và khoang chở hàng/chở khách (minivan, microvan). | Xe van chở hàng đỗ ven đường (`img_031.jpg`). | Xe buýt cỡ lớn (→ `bus`), xe tải thùng rời (→ `truck`), xe ô tô con (→ `car`). |
| `bus` | Xe buýt công cộng, xe khách cỡ lớn nhiều cửa sổ chở đông người. | Xe buýt lưu thông trên đường (`img_009.jpg`), xe buýt tại trạm dừng (`img_032.jpg`), xe buýt tại nút giao thông (`img_040.jpg`). | Xe van chở khách (→ `van`), xe tải thùng kín (→ `truck`). |
| `tuk_tuk` | Xe cơ giới ba bánh có động cơ, bao gồm xe ba bánh chở khách, xe ba bánh chở hàng, xe lam và xe mô tô ba bánh có thùng phụ bên hông (sidecar). | Xe cơ giới ba bánh chở khách (`img_004.jpg`, `img_040.jpg`), xe mô tô ba bánh có thùng phụ sidecar (`img_022.jpg`), xe cơ giới ba bánh chở hàng (`img_035.jpg`). | Xe xích lô đạp chân 3 bánh (→ `bicycle`), xe máy hai bánh (→ `motorcycle`), xe ô tô con bốn bánh (→ `car`). |
| `motorcycle` | Phương tiện hai bánh có gắn động cơ (xe máy số, xe tay ga, mô tô phân khối lớn hai bánh). | Xe máy lưu thông hoặc dừng đỗ tại nút giao (`img_005.jpg`, `img_030.jpg`), xe máy dừng đỗ ven đường (`img_008.jpg`, `img_011.jpg`), xe máy đang di chuyển (`img_026.jpg`), xe máy chở hàng hóa (`img_029.jpg`). | Xe mô tô ba bánh có thùng phụ sidecar (→ `tuk_tuk`), xe đạp không động cơ (→ `bicycle`), xe xích lô (→ `bicycle`). |
| `bicycle` | Phương tiện thô sơ không có động cơ, di chuyển nhờ sức người đạp, bao gồm xe đạp hai bánh thông thường, xe đạp chở hàng và xe xích lô ba bánh chở khách/chở hàng. | Xe đạp chở hàng (`img_003.jpg`, `img_010.jpg`), xe xích lô ba bánh chở người (`img_006.jpg`, `img_009.jpg`, `img_023.jpg`, `img_034.jpg`, `img_039.jpg`), xe đạp và xe xích lô lưu thông trên đường (`img_021.jpg`). | Xe máy có động cơ (→ `motorcycle`), xe cơ giới ba bánh (→ `tuk_tuk`). |
| `person` | Con người thật đang đứng, đi bộ, ngồi, hoặc đang điều khiển phương tiện (người lái xe, người đạp xe, người ngồi sau, hành khách). | Người đứng hoặc ngồi ven đường (`img_001.jpg`), người điều khiển xe đạp (`img_003.jpg`), người đi bộ (`img_014.jpg`, `img_025.jpg`), người điều khiển và người ngồi sau xe máy (`img_005.jpg`, `img_026.jpg`), người đạp xe và hành khách trên xe xích lô (`img_006.jpg`, `img_009.jpg`), người điều khiển và hành khách trên xe ba bánh (`img_022.jpg`), người chờ đón xe buýt (`img_032.jpg`). | Tượng điêu khắc nghệ thuật/tượng trang trí kim loại (`img_011.jpg`, `img_017.jpg`), hình ảnh người in 2D trên biển quảng cáo (`img_018.jpg`), ma-nơ-canh trong cửa hàng (`img_033.jpg`), mô hình đồ chơi/búp bê (`img_038.jpg` — bỏ qua không gán). |
| `ignore_region` | Vùng đánh dấu không bóc tách từng vật thể riêng lẻ; dùng cho đám đông quá dày đặc không thể tách rời, cụm vật quá xa (< 20px), hoặc vùng nhòe mờ nghiêm trọng. | Vùng người và phương tiện bị nhòe mờ nghiêm trọng do mưa hoặc chuyển động nhanh (`img_014.jpg`), cụm người ở cự ly rất xa dưới ngưỡng nhận diện (`img_025.jpg`). | Các đối tượng đứng đơn lẻ có kích thước >= 20px nhận diện rõ hình thể; các đối tượng là mô hình đồ chơi (bỏ qua hoàn toàn, không gán `ignore_region`). |

---

### 2.2. Thuộc tính và ngưỡng đo lường định lượng

Để đảm bảo độ đồng thuận cao giữa hai người gán nhãn ở Pha 3 và đo IAA ở Pha 4, toàn bộ thuộc tính được chuẩn hóa theo ngưỡng phần trăm định lượng đo được bằng mắt:

| Thuộc tính | Giá trị | Định nghĩa định lượng | Minh họa thực tế |
|---|---|---|---|
| `occluded_level` | `0` | **Không bị che khuất (0%):** Nhìn thấy trọn vẹn toàn bộ đường bao của vật thể. | Xe ô tô con di chuyển trên đường (`img_015.jpg`), xe tải đỗ thoáng ven đường (`img_036.jpg`), xe xích lô đi đầu không bị che (`img_023.jpg`). |
| | `1` | **Che khuất nhẹ (1% – 30% diện tích):** Bị chướng ngại vật nhỏ chắn một góc thân xe/chân người. | Xe tải bị người đứng che một góc thùng xe (`img_001.jpg`), người điều khiển xe máy bị tay lái che một phần chân (`img_026.jpg`), xe đạp bị hàng hóa che một phần khung xe (`img_003.jpg`). |
| | `2` | **Che khuất trung bình (31% – 70% diện tích):** Bị phương tiện khác hoặc hàng hóa che chắn từ 1/3 đến hơn nửa thân xe/thân người. | Người ngồi sau xe máy bị người lái che nửa người (`img_026.jpg`), xe máy xếp lớp tại nút giao (`img_005.jpg`), người ngồi bị vật chắn che nửa thân dưới (`img_001.jpg`), xe xích lô bị phương tiện phía trước che chắn (`img_006.jpg`). |
| | `3` | **Che khuất nặng (> 70% diện tích):** Chỉ còn nhìn thấy một phần nhỏ (đầu người nhô lên, mũi xe thò ra), nhưng vẫn nhận dạng chắc chắn nhờ ngữ cảnh. | Người điều khiển xe máy bị khối hàng hóa cồng kềnh trùm kín phần lớn cơ thể (`img_029.jpg`), người bị che khuất nặng trong đám đông (`img_008.jpg`). |
| `truncated` | `no` | Toàn bộ đối tượng nằm 100% bên trong khung hình ảnh. | Xe ô tô con đỗ trọn vẹn trong ảnh (`img_024.jpg`), xe ba bánh đỗ trọn vẹn (`img_004.jpg`). |
| | `yes` | Vật thể bị cắt bởi mép ảnh (ít nhất 1 điểm ảnh của vật tiếp xúc trực tiếp với mép ảnh trên/dưới/trái/phải). | Bánh xe và gầm xe máy bị cắt sát mép dưới bức ảnh (`img_026.jpg`), đầu xe tải bị cắt mép ảnh (`img_001.jpg`). |
| `can_review` | `false` | (Mặc định) Người gán hoàn toàn chắc chắn về lớp, ranh giới và thuộc tính. | Các xe và người rõ ràng, không có tranh chấp. |
| | `true` | (Tick chọn) Gắn cờ khi gặp ca phân vân, hình ảnh nhòe mờ khó ôm box hoặc ranh giới box chưa chốt để đối chiếu trong phiên Calibration (Pha 5). | Phương tiện mô hình tham gia diễu hành (`img_019.jpg`), phương tiện mờ nhòe trong mưa (`img_014.jpg`). |

---

## 3. Quy tắc vẽ Bounding Box (Khóa bằng mã R-xx)

- **R-01 (Visible Pixels Only - Ôm sát phần nhìn thấy):** Bounding box 2D bắt buộc phải ôm sát các điểm ảnh nhìn thấy thực tế (visible boundary). Cạnh box chạm vào pixel ngoài cùng của vật thể, lệch tối đa 2–3 px. Tuyệt đối không tự ý kéo box phỏng đoán vùng bị che khuất sau tường, sau xe khác hoặc nền đường rỗng. Bóng phương tiện phản chiếu trên mặt đường ướt (`img_015.jpg`) không tính vào box xe.
- **R-02 (Rider & Vehicle Separation - Tách người và phương tiện):**
  - Áp dụng đồng bộ cho toàn bộ các phương tiện 2 bánh và 3 bánh (`motorcycle`, `tuk_tuk`, `bicycle`):
    - **1 box phương tiện:** Bao trọn vẹn khung xe nhìn thấy (bánh xe, thân xe, tay lái, giá chở, gương).
    - **1 box `person` riêng biệt cho từng người:** Bao trọn vẹn người điều khiển hoặc hành khách (tính từ đỉnh mũ bảo hiểm xuống đến chân, bao gồm cả ba lô đeo trên lưng). Box xe và box người được phép chồng lấn tự nhiên.
  - *Ví dụ kiểm chứng:*
    - Xe máy (`img_005.jpg`, `img_026.jpg`): 1 box `motorcycle` + các box `person` cho người điều khiển và người ngồi sau.
    - Xe xích lô (`img_006.jpg`, `img_009.jpg`, `img_023.jpg`, `img_034.jpg`, `img_039.jpg`): 1 box `bicycle` ôm trọn thân xích lô + 1 box `person` cho người điều khiển + các box `person` cho hành khách trên ghế xích lô.
    - Xe đạp chở hàng (`img_003.jpg`, `img_010.jpg`): 1 box `bicycle` + 1 box `person` cho người điều khiển xe.
    - Xe mô tô 3 bánh có thùng phụ (`img_022.jpg`): 1 box `tuk_tuk` + 1 box `person` cho người điều khiển + 1 box `person` cho người ngồi trong thùng phụ.
- **R-03 (Car Carrier - Xe chở ô tô):**
  - Trên xe tải chuyên dụng vận chuyển ô tô (`img_002.jpg`):
    - Vẽ **1 box lớn `truck`** bao toàn bộ xe tải chở hàng (bao gồm cabin đầu kéo và toàn bộ giàn khung rơ-moóc).
    - Vẽ thêm **từng box riêng `car`** cho mỗi chiếc ô tô con thật đang nằm trên các tầng giàn chở hàng.
  - Đối với xe chở theo xác vỏ xe cũ (`img_024.jpg`): Chiếc ô tô con đang lưu thông gán `car`. Xác vỏ xe gắn trên nóc được xem là hàng hóa chở theo, **không gán thêm box `car` thứ hai**.
- **R-04 (Oversized Cargo - Hàng hóa quá khổ và chở hàng đặc thù):**
  - Hàng hóa nằm gọn trong lòng thùng xe tiêu chuẩn (`img_001.jpg`, `img_012.jpg`): Box xe bao trọn cả cabin, thùng xe và hàng hóa bên trong thùng.
  - Hàng hóa nhô cao hoặc phình to bất thường vượt quá kích thước vật lý của xe:
    - Xe máy chở hàng hóa cồng kềnh quá khổ (`img_029.jpg`): Box `motorcycle` chỉ ôm sát khung xe máy nhìn thấy, **không bao trùm hàng hóa cồng kềnh** để tránh làm sai lệch kích thước hình học chuẩn của phương tiện.
    - Xe ba bánh chở hàng hóa cồng kềnh quá khổ (`img_035.jpg`): Box `tuk_tuk` chỉ ôm thân xe cơ sở, **không bao trùm hàng hóa phình to phía trên**.
    - Xe bán tải chở hàng hóa/vật phẩm cồng kềnh trong thùng (`img_028.jpg`): Box xe ôm cabin và thùng xe bán tải; vật phẩm trên thùng là hàng hóa, không gán nhãn đối tượng riêng.
- **R-05 (Non-real Entities - Loại trừ thực thể phi thực tế):**
  - **Không gán `person`** cho tượng điêu khắc nghệ thuật hoặc tượng trang trí: tượng trang trí kim loại (`img_011.jpg`), tượng điêu khắc hình người (`img_017.jpg`).
  - **Không gán `car` / `truck`** cho công trình kiến trúc hoặc tác phẩm điêu khắc: tác phẩm điêu khắc bằng đá hình xe tải (`img_027.jpg`).
  - **Không gán `person`** cho ma-nơ-canh tĩnh trong tủ kính thời trang (`img_033.jpg`).
  - **Không gán `person`** cho hình ảnh người in 2D trên biển hiệu quảng cáo (`img_018.jpg`).
  - **Không gán box** cho bóng phản chiếu ảo trong gương chiếu hậu (`img_020.jpg`).
  - **Bỏ qua hoàn toàn, không gán bất kỳ nhãn nào** cho mô hình đồ chơi thu nhỏ trong tủ trưng bày (`img_038.jpg`).
  - *Lưu ý:* Những ảnh chỉ chứa các thực thể này (`img_017.jpg`, `img_020.jpg`, `img_027.jpg`, `img_033.jpg`, `img_038.jpg`) không gán nhãn nào, vẫn giữ nguyên trong Task trên CVAT và xuất ra 0 annotation.
- **R-06 (Minimum Size - Ngưỡng kích thước tối thiểu):**
  - Vật thể có chiều cao hoặc chiều rộng nhỏ hơn 20 pixels được coi là quá nhỏ, không thể nhận dạng tin cậy ➔ Bỏ qua, không gán box đơn lẻ.
  - Nếu xuất hiện thành cụm/dải đông người hoặc phương tiện ở rất xa dưới ngưỡng nhận diện (`img_025.jpg`): Vẽ 1 box bao bằng `ignore_region`.
- **R-07 (Quy tắc gom nhóm xe 3 bánh vào lớp `tuk_tuk`):**
  - Mọi phương tiện 3 bánh gắn động cơ đều được chuẩn hóa vào lớp `tuk_tuk`:
    - Xe cơ giới ba bánh chở khách (`img_004.jpg`, `img_040.jpg`).
    - Xe cơ giới ba bánh chở hàng (`img_035.jpg`).
    - Xe mô tô 3 bánh có thùng phụ bên hông (sidecar ở `img_022.jpg`): Box `tuk_tuk` bao trọn cả xe máy chính và thùng phụ gắn liền.
- **R-08 (Quy tắc lớp xe đạp và xích lô `bicycle`):**
  - Mọi phương tiện thô sơ đạp chân đều được chuẩn hóa vào lớp `bicycle`:
    - Xe đạp hai bánh và xe đạp chở hàng (`img_003.jpg`, `img_010.jpg`, `img_021.jpg`): Box `bicycle` bao khung xe và giá chở hàng gắn liền.
    - Xe xích lô ba bánh (`img_006.jpg`, `img_009.jpg`, `img_023.jpg`, `img_034.jpg`, `img_039.jpg`): Box `bicycle` bao trọn khung xe xích lô (bao gồm cả khoang ghế hành khách phía trước, bánh xe, xích líp và yên đạp phía sau).
- **R-09 (Quy tắc xe bán tải nắp thùng cao):**
  - Xe bán tải gắn nắp thùng cao chuyên dụng (`img_037.jpg`): Thống nhất phân loại là **`truck`** do xe sở hữu kết cấu cabin điều khiển tách biệt hoàn toàn với khoang thùng hàng phía sau.

---

## 4. Bảng phân tích chi tiết các ca khó qua khảo sát 40 ảnh

Bảng tổng hợp quyết định chính thức đã thống nhất của nhóm đối với 40 bức ảnh:

| Tên ảnh | Tình huống biên phát hiện | Mã quy tắc | Quyết định thống nhất nhóm | Cơ sở lập luận & Biện pháp |
|---|---|---|---|---|
| `img_001.jpg` | Xe tải nhẹ chở hàng, có người đứng cạnh và người ngồi | `R-01`, `R-04` | 1 box `truck` (`occluded_level=1`) + các box `person` | Xe tải chở hàng có cabin riêng biệt ➔ gán `truck`. Hàng hóa trong thùng tính vào box xe. Người đứng và người ngồi gán `person`. |
| `img_002.jpg` | Xe tải chuyên dụng vận chuyển các xe ô tô con trên giàn chở | `R-03` | 1 box `truck` (toàn bộ xe tải) + các box `car` riêng biệt cho từng ô tô con | Xe tải là phương tiện vận chuyển (gán `truck`); các ô tô con trên giàn là xe thật hoàn chỉnh (gán `car` cho từng xe). |
| `img_003.jpg` | Người điều khiển xe đạp chở hàng | `R-02`, `R-08` | 1 box `bicycle` (xe đạp) + 1 box `person` (người điều khiển) | Gán `bicycle` cho xe đạp chở hàng và `person` cho người điều khiển. Hàng hóa trên xe tính vào box xe đạp theo R-04. |
| `img_004.jpg` | Xe cơ giới ba bánh đỗ trên vỉa hè ban đêm | `R-07` | 2 box `tuk_tuk` (`occluded_level=0`, `truncated=no`) | Xe cơ giới ba bánh gán vào lớp `tuk_tuk` theo R-07. |
| `img_005.jpg` | Dòng xe máy và người điều khiển di chuyển trên đường | `R-02` | Tách mỗi xe thành 1 box `motorcycle` + box `person` tương ứng | Tách riêng phương tiện (`motorcycle`) và người điều khiển/người ngồi sau (`person`) theo R-02. |
| `img_006.jpg` | Đoàn xe xích lô chở người di chuyển trên đường | `R-02`, `R-08` | Mỗi xe xích lô: 1 box `bicycle` + các box `person` cho người đạp và hành khách | Áp dụng lớp `bicycle` cho xe xích lô; tách riêng người điều khiển và hành khách theo R-02, R-08. |
| `img_007.jpg` | Xe tải và người trong khu vực giao nhận hàng ban đêm | `R-01` | Gán `truck` cho xe tải; gán `person` cho người | Điều kiện thiếu sáng; box bám sát phần nhìn thấy của xe và người theo R-01. |
| `img_008.jpg` | Người điều khiển xe máy và người đi bộ ban đêm | `R-02` | Tách người và xe máy; gán `person` cho người đi bộ | Khung cảnh ban đêm; tách riêng người (`person`) và phương tiện (`motorcycle`) theo R-02. |
| `img_009.jpg` | Xe buýt và xe xích lô cùng lưu thông trên đường | `R-08`, `bus` | Xe buýt gán `bus`; xe xích lô gán `bicycle` + `person` | Xe buýt phân loại lớp `bus`; xe xích lô phân loại lớp `bicycle` và tách box `person` theo R-02, R-08. |
| `img_010.jpg` | Người điều khiển xe đạp chở hàng dừng đỗ bên đường | `R-02`, `R-08` | 1 box `bicycle` (xe đạp) + 1 box `person` (người điều khiển) | Gán `bicycle` cho xe đạp chở hàng và `person` cho người điều khiển theo R-02, R-08. |
| `img_011.jpg` | Tượng trang trí kim loại và các xe máy dừng đỗ | `R-05` | KHÔNG gán `person` cho tượng; gán `motorcycle` cho các xe máy | Tượng trang trí không phải người thật theo R-05; các xe máy gán `motorcycle`. |
| `img_012.jpg` | Xe bán tải chở hàng hóa trong thùng xe | `R-04` | Gán `truck`, box bao trọn cả cabin và thùng xe chở hàng | Hàng hóa nằm trong phạm vi thùng xe tiêu chuẩn được tính vào box xe theo R-04. |
| `img_013.jpg` | Khung cảnh ban đêm không có đối tượng | Trống | Không vẽ box nào (0 annotation) | Ảnh không có người hoặc phương tiện thuộc taxonomy. |
| `img_014.jpg` | Người đi bộ và phương tiện trong mưa lớn nhòe mờ | `R-01`, `R-06` | Gán `person` cho người nhìn rõ; vùng nhòe mờ gán `ignore_region` | Mưa lớn làm mất biên dạng đối tượng; vùng nhòe mờ không thể nhận diện rõ gán `ignore_region` theo R-06. |
| `img_015.jpg` | Xe ô tô con di chuyển trong mưa, mặt đường phản chiếu | `R-01` | Gán 1 box `car`, đáy box chạm đáy lốp xe | Bóng phản chiếu trên mặt đường ướt không tính vào box xe theo R-01. |
| `img_016.jpg` | Xe ô tô con (taxi) và người đi bộ trên đường | `R-01` | Gán `car` cho ô tô con (taxi), gán `person` cho người đi bộ | Xe taxi là ô tô con chở người thuộc lớp `car` theo R-01. |
| `img_017.jpg` | Tượng điêu khắc nghệ thuật hình người | `R-05` | KHÔNG gán nhãn nào (0 annotation) | Tác phẩm điêu khắc nghệ thuật, không phải người thật theo R-05. |
| `img_018.jpg` | Biển quảng cáo có hình ảnh người in 2D | `R-05` | KHÔNG gán nhãn nào (0 annotation) | Hình ảnh in 2D trên biển quảng cáo, không phải thực thể người thật theo R-05. |
| `img_019.jpg` | Phương tiện mô hình tham gia diễu hành | Ca biên | Tạm gán `car`, tick `can_review` | Phương tiện mô hình chế tạo trên khung xe cơ giới; gắn cờ `can_review` để đối chiếu hiệu chuẩn theo R-01. |
| `img_020.jpg` | Hình ảnh phương tiện phản chiếu trong gương chiếu hậu | `R-05` | KHÔNG gán nhãn nào (0 annotation) | Hình ảnh ảo phản chiếu qua gương chiếu hậu, không phải phương tiện thực tế trước đầu xe theo R-05. |
| `img_021.jpg` | Giao thông hỗn hợp (người đi bộ, xe máy, xe xích lô, xe đạp) | `R-02`, `R-08` | Gán `person`, `motorcycle`, và `bicycle` (cho xe đạp và xích lô) | Phân tách rõ ràng giữa xe cơ giới (`motorcycle`), xe thô sơ (`bicycle`) và người (`person`) theo R-02, R-08. |
| `img_022.jpg` | Xe mô tô ba bánh có thùng phụ (sidecar) chở người | `R-02`, `R-07` | 1 box `tuk_tuk` bao cả thân xe và thùng phụ + các box `person` tương ứng | Xe mô tô 3 bánh có thùng phụ gán vào lớp `tuk_tuk` theo R-07; tách riêng các box `person` cho người theo R-02; động vật không gán. |
| `img_023.jpg` | Xe xích lô chở người lưu thông trên đường | `R-02`, `R-08` | 1 box `bicycle` (xích lô) + các box `person` (người điều khiển và hành khách) | Xe xích lô gán `bicycle`, tách riêng người điều khiển và hành khách theo R-02, R-08. |
| `img_024.jpg` | Xe ô tô con chở xác vỏ xe cũ trên nóc | `R-03` | 1 box `car` cho xe đang chạy; KHÔNG gán box cho vỏ xe trên nóc | Vỏ xe cũ trên nóc được tính là hàng hóa chở theo, không gán nhãn phương tiện thứ hai theo R-03. |
| `img_025.jpg` | Cụm người ở cự ly rất xa dưới ngưỡng nhận diện (< 20px) | `R-06` | Bao toàn bộ cụm người ở xa bằng `ignore_region` | Kích thước quá nhỏ dưới ngưỡng 20px, không tách từng box đơn lẻ mà gán `ignore_region` theo R-06. |
| `img_026.jpg` | Người điều khiển và người ngồi sau xe máy góc nhìn cận cảnh | `R-02` | 1 box `motorcycle` + các box `person` (người lái và người ngồi sau) | Phương tiện cắt mép ảnh (`truncated=yes`); người ngồi sau bị che thân dưới (`occluded_level=2`) theo R-02. |
| `img_027.jpg` | Tác phẩm điêu khắc bằng đá hình xe tải | `R-05` | KHÔNG gán nhãn nào (0 annotation) | Tác phẩm điêu khắc kiến trúc, không phải phương tiện giao thông thực tế theo R-05. |
| `img_028.jpg` | Xe bán tải chở hàng hóa/vật phẩm cồng kềnh trong thùng | `R-04` | Gán `truck` cho xe bán tải; vật phẩm trong thùng là hàng hóa chở theo | Vật phẩm nằm trên thùng xe được tính là hàng hóa, không gán nhãn đối tượng riêng theo R-04. |
| `img_029.jpg` | Xe máy chở hàng hóa cồng kềnh quá khổ che khuất người điều khiển | `R-04` | Box `motorcycle` chỉ ôm khung xe máy; gán `person` (`occluded_level=3`) | Không kéo box xe máy bao trùm hàng hóa quá khổ để tránh làm sai lệch kích thước chuẩn theo R-04; người điều khiển bị che khuất nặng. |
| `img_030.jpg` | Dòng xe máy và người điều khiển dừng đỗ tại nút giao | `R-02` | Tách 1 box `person` + 1 box `motorcycle` cho từng xe | Tách riêng người và phương tiện theo R-02; xác định mức độ che khuất xếp lớp (`occluded_level=1, 2`). |
| `img_031.jpg` | Xe van và xe máy dừng đỗ bên đường ban đêm | `R-01` | Gán `van` cho xe thùng liền khối; gán `motorcycle` và `person` tương ứng | Xe thân liền khối khoang lái và khoang chở hàng thuộc lớp `van` theo taxonomy. |
| `img_032.jpg` | Các xe buýt và người tập trung tại trạm đón trả khách | `bus` | Gán `bus` cho các xe buýt; gán `person` cho từng người | Xe buýt công cộng gán lớp `bus`; người đi bộ/chờ xe gán lớp `person`. |
| `img_033.jpg` | Ma-nơ-canh trưng bày sau cửa kính cửa hàng | `R-05` | KHÔNG gán nhãn nào (0 annotation) | Ma-nơ-canh trưng bày, không phải người thật theo R-05. |
| `img_034.jpg` | Xe xích lô chở người qua nút giao | `R-02`, `R-08` | Gán 1 box `bicycle` cho xích lô + các box `person` cho người | Xe xích lô thuộc lớp `bicycle`; tách riêng người đạp và hành khách theo R-02, R-08. |
| `img_035.jpg` | Xe cơ giới ba bánh chở hàng hóa cồng kềnh quá khổ | `R-04`, `R-07` | 1 box `tuk_tuk` ôm thân xe cơ sở; KHÔNG bao trùm hàng hóa cồng kềnh phía trên | Xe cơ giới ba bánh thuộc lớp `tuk_tuk`; box xe chỉ ôm thân xe cơ sở theo R-04. |
| `img_036.jpg` | Xe tải nhẹ đỗ bên lề đường | `R-01` | Gán 1 box `truck` (`occluded_level=0`, `truncated=no`) | Xe tải chở hàng có cabin riêng biệt thuộc lớp `truck`. |
| `img_037.jpg` | Xe bán tải gắn nắp thùng cao | `R-09` | Gán `truck` (`occluded_level=0`, `truncated=no`) | Xe bán tải có cabin riêng biệt và nắp thùng xếp vào lớp `truck` theo R-09. |
| `img_038.jpg` | Mô hình đồ chơi thu nhỏ trong tủ trưng bày | `R-05` | Bỏ qua hoàn toàn, không gán nhãn nào (0 annotation) | Mô hình đồ chơi thu nhỏ trưng bày, không phải người và phương tiện thật tham gia giao thông; bỏ qua hoàn toàn, không gán bất kỳ nhãn nào theo R-05. |
| `img_039.jpg` | Các xe xích lô chở người di chuyển nối đuôi nhau | `R-02`, `R-08` | Gán `bicycle` cho từng xe xích lô và `person` cho người trên xe | Gán lớp `bicycle` cho từng xe xích lô; tách riêng người điều khiển và hành khách theo R-02, R-08. |
| `img_040.jpg` | Giao lộ nhìn từ trên cao có nhiều loại phương tiện lưu thông | `R-01`, `R-07` | Gán `car` cho ô tô con, `tuk_tuk` cho xe ba bánh, `bus` cho xe buýt, `motorcycle` cho xe máy | Phân loại phương tiện theo đúng định nghĩa các lớp trong taxonomy v1.0. |

---

## 5. Tiêu chuẩn QC và Lịch sử phiên bản

- **Chỉ tiêu kiểm soát chất lượng (QC Targets):**
  - Độ phủ dữ liệu: $100\%$ số ảnh được phân công theo `assignment.csv` phải được gán và export COCO hợp lệ.
  - Chỉ số đồng thuận liên gán nhãn (IAA) trước hiệu chuẩn: $\text{mIoU} \ge 0.85$, $\text{Class Agreement} \ge 95\%$ trên các cặp box ghép được.
  - Tỷ lệ lỗi nghiệm thu toàn lô (Defect Rate): $\le 2.0\%$ tính theo số ảnh có lỗi.
- **Quy trình xử lý ca mơ hồ:** Luôn tick `can_review` khi phân vân; không tự ý phỏng đoán để phiên calibration Pha 5 chốt bằng biểu quyết nhóm.
- **Lịch sử cập nhật:**

| Phiên bản | Thay đổi chính | Tình huống áp dụng / Căn cứ thực tế |
|---|---|---|
| `v0` | Khung khởi đầu của giảng viên | 5 lớp sơ sài, thuộc tính định tính mơ hồ, chưa có xử lý ca biên. |
| `v1.0` | Bản thống nhất chính thức của nhóm Wolf (03225 & 03246) | **Khóa đặc tả cho Pha 3**: Mở rộng taxonomy thành 8 lớp đối tượng (thêm chính thức `bus`, `tuk_tuk`, `bicycle`), chuẩn hóa thang đo `occluded_level` theo %, chốt 9 quy tắc vẽ box `R-01` đến `R-09` và phân tích thống nhất 40 ảnh. Chốt quy tắc bỏ qua hoàn toàn đối với mô hình đồ chơi (`img_038.jpg`). |
