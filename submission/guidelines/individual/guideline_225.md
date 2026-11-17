# Guideline gán nhãn — v0.1 — Cá nhân 03225

> Bản guideline cá nhân phát triển từ `guideline_v0_starter.md`, hoàn thiện định nghĩa định lượng và giải quyết triệt để các ca biên sau khi khảo sát toàn bộ 40 ảnh đường phố thuộc bộ dữ liệu. Đã bổ sung chính thức hai lớp `tuk_tuk` (bao gồm toàn bộ xe cơ giới 3 bánh) và `bicycle` (bao gồm xe đạp và xe xích lô).

- Phiên bản: v0.1 (Bản cá nhân trước phiên họp thống nhất Pha 2)
- Ngày: 16/11/2026
- Người viết: 03225
- Công cụ áp dụng: CVAT, xuất dữ liệu chuẩn COCO 1.0

---

## 1. Mục tiêu và phạm vi sử dụng

- **Mục đích mô hình:** Bộ dữ liệu được gán nhãn 2D Bounding Box để huấn luyện mô hình thị giác máy tính phát hiện đối tượng giao thông đường phố (người đi bộ và các loại phương tiện lưu thông) phục vụ hệ thống camera giám sát đô thị thông minh và hệ thống trợ lái xe tự hành (ADAS/Autonomous Driving) trong điều kiện giao thông phức tạp tại châu Á.
- **Rủi ro sai lệch:**
  - *False Negative (Bỏ sót vật):* Cực kỳ nguy hiểm đối với các đối tượng dễ bị tổn thương như người đi bộ, người đi xe đạp, xích lô, xe máy hoặc xe tải lớn đang lưu thông; có thể gây ra tai nạn hoặc va chạm trực tiếp.
  - *False Positive (Nhận diện nhầm):* Gây cảnh báo ảo hoặc phanh gấp nguy hiểm nếu mô hình nhận nhầm hình in 2D trên biển quảng cáo, tượng đài điêu khắc, ma-nơ-canh hoặc bóng xe phản chiếu trong gương chiếu hậu là chướng ngại vật thật trên lòng đường.
- **Tiêu chuẩn chất lượng:** Bounding box 2D phải bao trọn và ôm khít toàn bộ phần nhìn thấy (visible pixels) của đối tượng thực tế; không phỏng đoán phần khuất không có căn cứ; không bao thừa vùng không gian nền xung quanh.

---

## 2. Lớp và thuộc tính

### 2.1. Danh mục lớp đối tượng (Taxonomy đề xuất)

Để giải quyết triệt để các ca biên phổ biến trên đường phố mà taxonomy v0 bỏ sót, danh mục lớp được chuẩn hóa với **7 lớp đối tượng** và **1 lớp vùng bỏ qua**:

| Lớp | Định nghĩa chuẩn 2D | Ví dụ cụ thể trong 40 ảnh | Phản ví dụ / Dễ nhầm với |
|---|---|---|---|
| `car` | Ô tô con từ 4 đến 7 chỗ ngồi (sedan, SUV, hatchback, coupe, wagon) dùng chở người. | 8 xe ô tô con được vận chuyển trên xe lồng (`img_002.jpg`), xe taxi (`img_040.jpg`). | Xe tải nhẹ thùng lửng (`img_001.jpg`), xe tải nhỏ đầu bằng (`img_036.jpg`), xe mô hình đồ chơi trong tủ kính (`img_038.jpg`), xe diễu hành mô hình (`img_019.jpg`), bia mộ đá hình xe (`img_027.jpg`), ảnh xe trong gương chiếu hậu (`img_020.jpg`). |
| `truck` | Xe tải chở hàng, xe tải thùng kín/lửng/bạt, xe bồn, xe đầu kéo rơ-moóc, có cabin riêng biệt với khoang chở hàng. | Xe tải nhẹ thùng lửng chở kim loại (`img_001.jpg`), xe tải lồng chuyên dụng chở ô tô (`img_002.jpg`), xe tải thùng kín trong kho giao hàng (`img_007.jpg`), xe tải nhẹ đầu bằng (`img_036.jpg`), xe bán tải chở rau củ (`img_012.jpg`), xe bán tải gắn nắp thùng cao chuyên dụng (`img_037.jpg`). | Xe buýt lớn chở khách (`img_009.jpg`, `img_032.jpg`), xe van nhỏ khoang liền (`img_031.jpg`), tượng đài xe tải bằng đá (`img_027.jpg`). |
| `van` | Xe thùng kín cỡ vừa/nhỏ, thân xe liền khối giữa khoang lái và khoang chở hàng/chở khách (minivan, microvan). | Xe van bưu phẩm/chở hàng đô thị đỗ ven đường (`img_031.jpg`). | Xe buýt công cộng cỡ lớn (`img_009.jpg`, `img_032.jpg`), xe tải có thùng rời (`img_001.jpg`, `img_036.jpg`), xe ô tô con 7 chỗ (`car`). |
| `motorcycle` | Phương tiện hai bánh có gắn động cơ (xe máy số, xe tay ga, mô tô phân khối lớn hai bánh). | Đoàn xe máy (`img_005.jpg`, `img_030.jpg`), xe máy đỗ chợ đêm (`img_008.jpg`), dàn xe mô tô độ (`img_011.jpg`), cặp đôi đi xe máy trong hầm (`img_026.jpg`), xe máy chở hàng cồng kềnh (`img_029.jpg`). | Xe mô tô 3 bánh có thùng phụ sidecar (`tuk_tuk`), xe đạp không động cơ (`bicycle`), xe xích lô (`bicycle`). |
| `tuk_tuk` | Xe cơ giới ba bánh có động cơ, bao gồm xe tuk-tuk chở khách, xe auto-rickshaw, xe lam 3 bánh và xe mô tô 3 bánh có thùng phụ bên hông (sidecar). | 2 xe tuk-tuk màu xanh đỗ vỉa hè (`img_004.jpg`), xe mô tô 3 bánh sidecar cổ điển (`img_022.jpg`), xe auto-rickshaw chở hàng ở Hyderabad (`img_035.jpg`), xe tuk-tuk chạy ở giao lộ (`img_040.jpg`). | Xe xích lô đạp chân 3 bánh (`bicycle`), xe máy 2 bánh (`motorcycle`), xe ô tô con 4 bánh (`car`). |
| `bicycle` | Phương tiện thô sơ không có động cơ, di chuyển nhờ sức người đạp, bao gồm xe đạp hai bánh thông thường, xe đạp chở hàng rong và xe xích lô ba bánh chở khách/chở hàng. | Xe đạp (`img_003.jpg`), đoàn xe xích lô chở khách du lịch (`img_006.jpg`, `img_009.jpg`, `img_023.jpg`, `img_034.jpg`, `img_039.jpg`), xe đạp (`img_010.jpg`), xe đạp và xích lô tham gia giao thông hỗn hợp (`img_021.jpg`). | Xe máy có động cơ (`motorcycle`), xe cơ giới 3 bánh (`tuk_tuk`). |
| `person` | Con người thật đang đứng, đi bộ, ngồi, hoặc đang điều khiển phương tiện (người lái xe, người đạp xe, người ngồi sau, hành khách). | Cô gái đứng và khách ăn uống (`img_001.jpg`), người bán mít (`img_003.jpg`), người đi bộ cầm ô trong mưa (`img_014.jpg`), người đi xe máy (`img_005.jpg`, `img_026.jpg`), người đạp xích lô và khách du lịch (`img_006.jpg`, `img_009.jpg`), người lái và hành khách trên xe sidecar (`img_022.jpg`), người dạo biển (`img_025.jpg`), hành khách chờ xe buýt (`img_032.jpg`). | Tượng quái vật viễn tưởng bằng kim loại (`img_011.jpg`), tượng danh nhân cưỡi ngựa bằng đồng (`img_017.jpg`), ảnh chân dung chính trị gia trên biển quảng cáo (`img_018.jpg`), ma-nơ-canh váy đỏ trong cửa hàng (`img_033.jpg`), búp bê đồ chơi (`img_038.jpg`). |
| `ignore_region` | Vùng đánh dấu không bóc tách từng vật thể riêng lẻ; dùng cho đám đông quá dày đặc, cụm vật quá xa (< 20px), hoặc vùng nhòe mờ nghiêm trọng. | Vùng người và xe bị nhòe vệt chuyển động/nước mưa nặng (`img_014.jpg`), dải người đi bộ ở rất xa trên bờ biển (`img_025.jpg`), cụm đồ chơi/búp bê dày đặc trong tủ kính (`img_038.jpg`). | Các đối tượng đứng đơn lẻ có chiều cao >= 20px nhận diện rõ hình thể. |

#### Vấn đề còn lại cần thảo luận và biểu quyết ở Pha 2:
- **Xe buýt công cộng cỡ lớn (`img_009.jpg`, `img_032.jpg`, `img_040.jpg`):** Xe buýt công cộng có kích thước đồ sộ. Hiện tại ở v0.1 cá nhân tạm gán cờ `can_review` và đề xuất nhóm bổ sung thêm lớp `bus` vào `submission/taxonomy.json` tại Pha 2.
- **Xe diễu hành mô hình hoạt hình (`img_019.jpg`):** Xe mô hình chế tạo trên khung xe cơ giới tham gia lễ hội. Tạm gán `car` kèm tick `can_review`.

---

### 2.2. Thuộc tính và ngưỡng đo lường định lượng

Để loại bỏ hoàn toàn tính mơ hồ cảm tính giữa các thành viên gán nhãn, các thuộc tính được lượng hóa theo tỷ lệ phần trăm điểm ảnh cụ thể:

| Thuộc tính | Giá trị | Định nghĩa định lượng | Minh họa thực tế |
|---|---|---|---|
| `occluded_level` | `0` | **Không bị che khuất (0%):** Nhìn thấy trọn vẹn toàn bộ đường bao của vật thể. | SUV chạy đơn độc giữa đường (`img_015.jpg`), xe tải nhẹ đầu bằng đỗ thoáng (`img_036.jpg`), xích lô đi đầu đoàn (`img_023.jpg`). |
| | `1` | **Che khuất nhẹ (1% – 30%):** Bị chướng ngại vật nhỏ chắn một góc thân xe/chân người. | Xe tải nhẹ thùng lửng bị cô gái đứng che một góc thùng xe (`img_001.jpg`), người đi xe máy có chân bị ghi-đông che (`img_026.jpg`), xe đạp bị giỏ hàng che gióng xe (`img_003.jpg`). |
| | `2` | **Che khuất trung bình (31% – 70%):** Bị phương tiện khác hoặc hàng hóa che chắn từ 1/3 đến hơn nửa thân xe/thân người. | Người ngồi sau xe máy bị người lái che nửa người (`img_026.jpg`), xe máy xếp lớp ở ngã tư (`img_005.jpg`), khách ngồi uống nước bị bàn che nửa thân dưới (`img_001.jpg`), xích lô đi sau bị xe trước che chắn (`img_006.jpg`). |
| | `3` | **Che khuất nặng (> 70%):** Chỉ còn nhìn thấy một phần nhỏ (đầu người nhô lên, mũi xe thò ra), nhưng vẫn nhận dạng chắc chắn nhờ ngữ cảnh. | Người điều khiển xe máy bị đống hàng bìa các-tông khổng lồ trùm kín thân (`img_029.jpg`), người trong đám đông lấp ló (`img_008.jpg`). |
| `truncated` | `no` | Toàn bộ đối tượng nằm 100% bên trong khung hình ảnh. | Xe wagon đỗ trọn vẹn trong ảnh (`img_024.jpg`), xe tuk-tuk đỗ trọn vẹn (`img_004.jpg`). |
| | `yes` | Vật thể bị cắt bởi mép ảnh (ít nhất 1 điểm ảnh của vật tiếp xúc trực tiếp với mép ảnh trên/dưới/trái/phải). | Bánh xe và gầm xe máy bị cắt sát mép dưới bức ảnh (`img_026.jpg`), đầu xe tải bị cắt mép phải (`img_001.jpg`). |
| `can_review` | `false` | (Mặc định) Người gán hoàn toàn chắc chắn về lớp, ranh giới và thuộc tính. | Các xe và người rõ ràng, không có tranh chấp. |
| | `true` | (Tick chọn) Gắn cờ khi gặp ca phân vân, ca ngoài taxonomy, hoặc ranh giới box chưa chốt để đối chiếu tại Pha 4 & 5. | Xe buýt lớn (`img_009.jpg`, `img_032.jpg`), xe diễu hành mô hình (`img_019.jpg`), xe bán tải gắn nắp thùng cao (`img_037.jpg`). |

---

## 3. Quy tắc vẽ Bounding Box

- **R-01 (Visible Pixels Only - Ôm sát phần nhìn thấy):** Bounding box 2D bắt buộc phải ôm sát các điểm ảnh nhìn thấy thực tế (visible boundary). Tuyệt đối không tự ý kéo box phỏng đoán vùng bị che khuất sau tường, sau xe khác hoặc nền đường rỗng. Bóng xe phản chiếu trên mặt đường ướt (`img_015.jpg`) không tính vào box xe.
- **R-02 (Rider & Vehicle Separation - Tách người và phương tiện):**
  - Áp dụng đồng bộ cho toàn bộ các phương tiện 2 bánh và 3 bánh (`motorcycle`, `tuk_tuk`, `bicycle`):
    - **1 box phương tiện:** Bao trọn vẹn khung xe nhìn thấy (bánh xe, thân xe, tay lái, giá chở, gương).
    - **1 box `person` riêng biệt cho từng người:** Bao trọn vẹn người điều khiển hoặc hành khách (tính từ đỉnh mũ bảo hiểm xuống đến chân, bao gồm cả ba lô đeo trên lưng).
  - *Ví dụ kiểm chứng:*
    - Xe máy (`img_005.jpg`, `img_026.jpg`): 1 box `motorcycle` + các box `person` cho người lái và người ngồi sau.
    - Xe xích lô (`img_006.jpg`, `img_009.jpg`, `img_023.jpg`, `img_034.jpg`, `img_039.jpg`): 1 box `bicycle` ôm trọn thân xích lô + 1 box `person` cho người đạp + 1 hoặc 2 box `person` cho hành khách ngồi trên ghế xích lô.
    - Xe đạp chở hàng (`img_003.jpg`, `img_010.jpg`): 1 box `bicycle` + 1 box `person` cho người bán hàng.
    - Xe mô tô 3 bánh sidecar (`img_022.jpg`): 1 box `tuk_tuk` + 1 box `person` cho người lái + 1 box `person` cho người ngồi trong thùng phụ.
- **R-03 (Car Carrier - Xe chở ô tô):**
  - Trên xe tải lồng vận chuyển chuyên dụng (`img_002.jpg`):
    - Vẽ **1 box lớn `truck`** bao toàn bộ xe tải chở hàng (bao gồm cabin đầu kéo và toàn bộ giàn khung rơ-moóc).
    - Vẽ thêm **từng box riêng `car`** cho mỗi chiếc ô tô con thật đang nằm trên các tầng giàn chở hàng (tổng cộng 8 box `car`).
  - Đối với xe chở phụ tùng/vỏ xe cũ (`img_024.jpg`): Chiếc xe ô tô con wagon đang chạy gán `car`. Xác vỏ xe rỉ sét gắn trên nóc được xem là hàng hóa chở theo, **không gán thêm box `car` thứ hai**.
- **R-04 (Oversized Cargo - Hàng hóa quá khổ và chở hàng đặc thù):**
  - Hàng hóa nằm gọn trong lòng thùng xe tiêu chuẩn (`img_001.jpg`, `img_012.jpg`): Box xe bao trọn cả cabin, thùng xe và hàng hóa bên trong thùng.
  - Hàng hóa nhô cao hoặc phình to bất thường vượt quá kích thước vật lý của xe:
    - Xe máy chở núi bìa các-tông cao ngất ngưởng (`img_029.jpg`): Box `motorcycle` chỉ ôm sát khung xe máy nhìn thấy, **không bao trùm đống bìa các-tông** để tránh làm sai lệch kích thước hình học chuẩn của xe máy.
    - Xe tuk-tuk chở núi bao tải phế liệu (`img_035.jpg`): Box `tuk_tuk` chỉ ôm thân xe cơ sở, **không bao trùm đống bao tải phình to phía trên**.
    - Xe bán tải chở tượng ngựa vàng khổng lồ (`img_028.jpg`): Box xe ôm cabin và thùng xe bán tải; tượng ngựa vàng là hàng hóa mỹ nghệ, không gán nhãn sinh vật.
- **R-05 (Non-real Entities - Loại trừ thực thể phi thực tế):**
  - **Không gán `person`** cho tượng điêu khắc nghệ thuật/tượng đài danh nhân: tượng quái vật viễn tưởng bằng kim loại (`img_011.jpg`), tượng danh nhân cưỡi ngựa bằng đồng (`img_017.jpg`).
  - **Không gán `car` / `truck`** cho công trình kiến trúc / bia mộ: bia tưởng niệm hình xe bán tải bằng đá granite (`img_027.jpg`).
  - **Không gán `person`** cho ma-nơ-canh tĩnh trong tủ kính thời trang (`img_033.jpg`).
  - **Không gán `person`** cho hình ảnh in 2D trên biển hiệu quảng cáo (`img_018.jpg`).
  - **Không gán box** cho bóng phản chiếu ảo trong gương chiếu hậu (`img_020.jpg`).
  - **Không gán box đơn lẻ** cho đồ chơi/mô hình thu nhỏ trong tủ kính trưng bày (`img_038.jpg`).
- **R-06 (Minimum Size - Ngưỡng kích thước tối thiểu):**
  - Vật thể có chiều cao hoặc chiều rộng nhỏ hơn 20 pixels được coi là quá nhỏ, không thể nhận dạng tin cậy ➔ Bỏ qua, không gán box đơn lẻ.
  - Nếu xuất hiện thành dải đông người/phương tiện ở xa chân trời (`img_025.jpg`): Vẽ 1 box bao bằng `ignore_region`.
- **R-07 (Quy tắc gom nhóm xe 3 bánh vào lớp `tuk_tuk`):**
  - Mọi phương tiện 3 bánh gắn động cơ đều được chuẩn hóa vào lớp `tuk_tuk`:
    - Xe tuk-tuk chở khách truyền thống (`img_004.jpg`, `img_040.jpg`).
    - Xe auto-rickshaw chở hàng (`img_035.jpg`).
    - Xe mô tô 3 bánh có thùng phụ bên hông (Sidecar ở `img_022.jpg`): Box `tuk_tuk` bao trọn cả xe máy chính và thùng phụ gắn liền.
- **R-08 (Quy tắc lớp xe đạp và xích lô `bicycle`):**
  - Mọi phương tiện thô sơ đạp chân đều được chuẩn hóa vào lớp `bicycle`:
    - Xe đạp hai bánh thường và xe đạp chở hàng dạo (`img_003.jpg`, `img_010.jpg`, `img_021.jpg`): Box `bicycle` bao khung xe và giá chở hàng gắn liền.
    - Xe xích lô ba bánh (`img_006.jpg`, `img_009.jpg`, `img_023.jpg`, `img_034.jpg`, `img_039.jpg`): Box `bicycle` bao trọn khung xe xích lô (bao gồm cả khoang ghế hành khách phía trước, bánh xe, xích líp và yên đạp phía sau).

---

## 4. Bảng phân tích chi tiết các ca khó qua khảo sát 40 ảnh

Bảng tổng hợp quyết định của cá nhân đối với toàn bộ các tình huống biên trong 40 ảnh:

| Tên ảnh | Tình huống biên phát hiện | Mã quy tắc | Quyết định cá nhân | Cơ sở lập luận & Biện pháp |
|---|---|---|---|---|
| `img_001.jpg` | Xe tải nhẹ thùng lửng chở kim loại, có cô gái đứng cạnh và khách ngồi ăn | `R-01`, `R-04` | 1 box `truck` (`occluded_level=1`) + các box `person` | Xe thùng lửng chở hàng có cabin riêng ➔ `truck` (không phải `car`). Hàng sắt gắn liền thùng tính vào box xe. Khách ngồi uống nước gán `person`. |
| `img_002.jpg` | Xe tải lồng chuyên dụng vận chuyển 8 xe ô tô con trên hai tầng giàn | `R-03` | 1 box `truck` (toàn bộ xe tải) + 8 box `car` riêng biệt | Xe tải chở hàng là phương tiện vận tải; 8 xe ô tô trên giàn là xe thật hoàn chỉnh, mô hình cần phát hiện được chúng. |
| `img_003.jpg` | Người phụ nữ bán mít dạo bằng xe đạp ở Hà Nội | `R-02`, `R-08` | 1 box `bicycle` (xe đạp) + 1 box `person` (người bán) | Áp dụng lớp `bicycle` cho xe đạp thồ hàng. Mít trên xe tính là hàng chở theo. |
| `img_004.jpg` | 2 xe tuk-tuk 3 bánh màu xanh đỗ trên vỉa hè ban đêm | `R-07` | 2 box `tuk_tuk` (`occluded_level=0`, `truncated=no`) | Xe cơ giới 3 bánh chính thức gán vào lớp `tuk_tuk`. |
| `img_005.jpg` | Dòng xe máy đông đúc di chuyển giờ cao điểm tại TP.HCM | `R-02` | Tách mỗi xe thành 1 box `motorcycle` + 1 box `person` | Kiểm chứng quy tắc tách người/xe máy trên góc rộng; người ngồi sau có box `person` riêng. |
| `img_006.jpg` | Đoàn xích lô chở khách du lịch diễu hành tại Huế | `R-02`, `R-08` | Mỗi xích lô: 1 box `bicycle` + các box `person` cho người đạp và khách | Áp dụng lớp `bicycle` cho xích lô; tách riêng người đạp và khách ngồi. |
| `img_007.jpg` | Xe tải chở hàng trong kho bốc dỡ ban đêm | `R-01` | Gán `truck` cho xe tải thùng kín; gán `person` cho công nhân | Điều kiện thiếu sáng; box bám sát phần thân xe nhìn thấy được. |
| `img_008.jpg` | Chợ đêm với người đi xe máy mặc áo phản quang và người đi bộ | `R-02` | Tách người và xe máy; gán `person` cho người đi bộ | Khung cảnh ánh sáng phức tạp; áo phản quang thuộc về box `person`. |
| `img_009.jpg` | Xe buýt lớn chở khách đi phía sau đoàn xích lô | `R-08`, ca ngoài | Xích lô gán `bicycle` + `person`; xe buýt tick `can_review` | Xích lô gán `bicycle`. Xe buýt quá to, đề xuất nhóm thêm lớp `bus` ở Pha 2. |
| `img_010.jpg` | Người bán quả Phật thủ bằng xe đạp ở phố cổ | `R-02`, `R-08` | 1 box `bicycle` (xe đạp) + 1 box `person` (người bán hàng) | Gán `bicycle` cho xe đạp bán hàng rong và `person` cho người bán. |
| `img_011.jpg` | Tượng quái vật viễn tưởng bằng kim loại đứng cạnh dàn xe mô tô độ | `R-05` | KHÔNG gán `person` cho tượng; gán `motorcycle` cho dàn xe | Tượng trang trí làm từ phế liệu kim loại, không phải sinh vật sống/người thật. |
| `img_012.jpg` | Xe bán tải chở thùng xốp và túi rau củ quả đầy ắp | `R-04` | Gán `truck`, box bao trọn cả cabin và thùng xe chở rau | Hàng hóa nằm trong kích thước thùng xe tiêu chuẩn được tính vào box phương tiện. |
| `img_013.jpg` | Phố đêm mưa ướt, chỉ có bàn ghế và ánh đèn | Trống | Không vẽ box nào | Ảnh không có người và phương tiện hợp lệ theo taxonomy. |
| `img_014.jpg` | Người qua đường cầm ô trong mưa lớn; xe cộ bị nhòe nước | `R-01`, `R-06` | Gán `person` người nhìn rõ; vùng nhòe mờ gán `ignore_region` | Chuyển động nhòe và màn mưa làm mất biên dạng vật thể, không gán ép box sai kích thước. |
| `img_015.jpg` | Xe SUV màu bạc chạy giữa trời mưa, mặt đường phản chiếu | `R-01` | Gán 1 box `car`, đáy box chỉ chạm đáy lốp xe | Vệt bóng phản chiếu trên mặt đường ướt KHÔNG được kéo box vào. |
| `img_016.jpg` | Xe taxi sedan và người đi bộ ở phố Tàu Bangkok | `R-01` | Gán `car` cho taxi sedan, `person` cho người đi bộ | Xe taxi là ô tô con 4 chỗ tiêu chuẩn (`car`). |
| `img_017.jpg` | Tượng danh nhân cưỡi ngựa bằng đồng đội chóp nón giao thông | `R-05` | KHÔNG gán `person` cho tượng người; không gán ngựa | Tượng nghệ thuật danh nhân; mô hình tự hành không được nhận diện nhầm để tránh phanh gấp. |
| `img_018.jpg` | Biển quảng cáo chính trị trên tòa nhà có ảnh chân dung cực lớn | `R-05` | KHÔNG gán `person` cho khuôn mặt trên biển | Hình in 2D trên mặt phẳng quảng cáo, không phải người thật đang đứng trên đường. |
| `img_019.jpg` | Xe diễu hành mô hình hoạt hình | Ca biên | Tạm gán `car`, tick `can_review` | Xe mô hình được chế tạo trên khung xe cơ giới tham gia lễ hội đồ chơi. |
| `img_020.jpg` | Gương chiếu hậu ô tô phản chiếu cầu vồng và xe phía sau | `R-05` | KHÔNG gán box cho bất kỳ xe nào trong gương | Hình ảnh phản chiếu qua gương là ảnh ảo 2D, không nằm trong không gian thực tế trước xe. |
| `img_021.jpg` | Hỗn hợp giao thông Việt Nam (người đi bộ, xe máy, xích lô, xe đạp) | `R-02`, `R-08` | Gán `person`, `motorcycle`, và `bicycle` cho xích lô / xe đạp | Bóc tách rõ ràng giữa xe cơ giới (`motorcycle`) và xe thô sơ (`bicycle`). |
| `img_022.jpg` | Xe mô tô 3 bánh có thùng phụ chở người và chó (Sidecar) | `R-02`, `R-07` | 1 box `tuk_tuk` bao cả xe và thùng phụ + 2 box `person` | Gom xe mô tô 3 bánh sidecar vào lớp `tuk_tuk`. Người lái và người ngồi thùng gán `person`. Chó không gán. |
| `img_023.jpg` | Xích lô chở khách đi phía trước | `R-02`, `R-08` | 1 box `bicycle` (xích lô) + các box `person` (người đạp và khách) | Xe xích lô gán `bicycle`, tách riêng người điều khiển và hành khách. |
| `img_024.jpg` | Xe ô tô con wagon chở xác thân xe cũ rỉ sét trên nóc | `R-03` | 1 box `car` cho xe đang chạy; KHÔNG gán box cho xác xe trên nóc | Xác vỏ xe rỉ sét đóng vai trò hành lý/hàng hóa phế liệu, không phải phương tiện đang lưu thông. |
| `img_025.jpg` | Người đi dạo bãi biển ở cự ly rất xa (< 20px) | `R-06` | Bao toàn bộ dải người ở xa bằng `ignore_region` | Kích thước quá nhỏ dưới ngưỡng 20px, không bóc tách từng box đơn lẻ. |
| `img_026.jpg` | Cặp đôi đi xe máy trong hầm Thủ Thiêm góc nhìn cận cảnh | `R-02` | 1 box `motorcycle` + 2 box `person` (người lái và người ngồi sau) | Cắt mép dưới ảnh (`truncated=yes`). Người ngồi sau bị che thân dưới (`occluded_level=2`). |
| `img_027.jpg` | Bia mộ hình xe bán tải chạm khắc bằng đá hoa cương | `R-05` | KHÔNG gán `truck` hay `car` | Tác phẩm điêu khắc nghĩa trang, không có giá trị giao thông thực tế. |
| `img_028.jpg` | Xe bán tải chở tượng ngựa vàng khổng lồ ("yorse") trong thùng | `R-04` | Gán `truck` cho xe bán tải; tượng ngựa là hàng hóa chở theo | Tượng ngựa được tính là hàng hóa, không gán nhãn động vật. |
| `img_029.jpg` | Xe máy chở khối bìa các-tông và xốp khổng lồ che khuất người | `R-04` | Box `motorcycle` chỉ ôm khung xe máy; gán `person` (`occluded_level=3`) | Không kéo box xe máy bao trùm đống bìa để tránh làm phình to kích thước chuẩn của phương tiện. |
| `img_030.jpg` | Xe máy dừng đỗ tại ngã tư TP.HCM | `R-02` | Tách 1 box `person` + 1 box `motorcycle` cho từng xe | Xác định mức độ che khuất xếp lớp (`occluded_level=1, 2`). |
| `img_031.jpg` | Phố Kangding ban đêm có xe van nhỏ và xe máy đỗ | `R-01` | Gán `van` cho xe giao hàng thùng liền; gán `motorcycle`, `person` | Xe minivan khoang liền thân thuộc lớp `van`. |
| `img_032.jpg` | Trạm xe buýt giờ cao điểm có nhiều xe buýt lớn và đám đông | Ca ngoài taxonomy | Xe buýt tick `can_review`; đám đông gán từng `person` | Xe buýt lớn đề xuất lớp `bus`. Hành khách chờ xe gán `person`. |
| `img_033.jpg` | Ma-nơ-canh mặc váy đỏ sau cửa kính thời trang | `R-05` | KHÔNG gán `person` cho ma-nơ-canh | Người nộm bằng nhựa/thạch cao, không phải người thật. |
| `img_034.jpg` | Xích lô qua đường ở ngã ba | `R-02`, `R-08` | Gán 1 box `bicycle` cho xích lô + các box `person` cho người | Xích lô thuộc `bicycle`, tách người đạp và khách ngồi. |
| `img_035.jpg` | Xe tuk-tuk chở núi bao tải phế liệu cao ngất ngưởng | `R-04`, `R-07` | 1 box `tuk_tuk` ôm thân xe cơ sở; KHÔNG bao trùm núi bao tải | Xe thuộc `tuk_tuk`. Quá khổ nghiêm trọng; box xe chỉ ôm thân xe cơ sở theo R-04. |
| `img_036.jpg` | Xe tải nhẹ đầu bằng đỗ bên lề đường | `R-01` | Gán 1 box `truck` (`occluded_level=0`, `truncated=no`) | Xe tải nhẹ chở hàng đô thị có cabin riêng biệt. |
| `img_037.jpg` | Xe bán tải lắp nắp thùng cao chuyên dụng | `R-01` | Gán `truck`, tick `can_review` | Nắp thùng cao biến đổi hình dáng bán tải thành xe thùng kín dịch vụ. |
| `img_038.jpg` | Tủ kính cửa hàng đồ chơi chứa nhiều xe hơi và búp bê mô hình | `R-05` | KHÔNG gán box đơn lẻ (hoặc bao `ignore_region`) | Đồ chơi/mô hình thu nhỏ trong tủ trưng bày. |
| `img_039.jpg` | Tuyến xích lô du lịch nối đuôi nhau | `R-02`, `R-08` | Gán `bicycle` cho từng xe xích lô và `person` cho người trên xe | Gán đầy đủ phương tiện xích lô và hành khách. |
| `img_040.jpg` | Giao lộ Bangkok từ trên cao có taxi sedan, xe buýt, tuk-tuk | `R-01`, `R-07` | Gán `car` cho taxi sedan, `tuk_tuk` cho xe tuk-tuk, `motorcycle` cho xe máy; tick `can_review` cho bus | Tuk-tuk phân loại chính thức vào `tuk_tuk`. Xe buýt đưa vào danh mục thảo luận Pha 2. |

---

## 5. Tiêu chuẩn QC và Lịch sử phiên bản

- **Chỉ tiêu kiểm soát chất lượng (QC Targets cho bài thực hành):**
  - Độ phủ dữ liệu: $100\%$ số ảnh được phân công theo `assignment.csv` phải được gán và export hợp lệ.
  - Chỉ số đồng thuận liên gán nhãn (IAA) trước hiệu chuẩn: $\text{mIoU} \ge 0.85$, $\text{Class Agreement} \ge 95\%$ trên các cặp box ghép được.
  - Tỷ lệ lỗi nghiệm thu toàn lô (Defect Rate): $\le 2.0\%$ tính theo số ảnh có lỗi.
- **Quy trình xử lý ca mơ hồ:** Luôn tick `can_review` khi phân vân; không tự ý phỏng đoán để phiên calibration Pha 5 chốt bằng biểu quyết nhóm.
- **Lịch sử cập nhật:**

| Phiên bản | Thay đổi chính | Tình huống áp dụng / Căn cứ thực tế |
|---|---|---|
| `v0` | Khung khởi đầu của giảng viên | 5 lớp sơ sài, thuộc tính định tính mơ hồ, chưa có xử lý ca biên. |
| `v0.1` | Bản cá nhân hoàn thiện (Annotator 225) | Khảo sát trọn vẹn 40 ảnh: chuẩn hóa mô tả các loại xe (loại bỏ toàn bộ tên hãng/tên thương mại riêng), lượng hóa thuộc tính theo %, định nghĩa chi tiết 8 quy tắc vẽ box (`R-01` đến `R-08`), bổ sung chính thức 2 lớp `tuk_tuk` (gộp xe tuk-tuk và mô tô 3 bánh sidecar) và `bicycle` (xe đạp và xe xích lô), và lập bảng ma trận phân tích toàn diện 40 ảnh. |
