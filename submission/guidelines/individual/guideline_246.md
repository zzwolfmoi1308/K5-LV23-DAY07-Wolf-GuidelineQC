# Guideline gán nhãn — bản cá nhân (Pha 1)

> **Đây là bản do giảng viên viết nhanh. Nó CHƯA đủ dùng.**
> Đừng sửa file này. Hãy copy sang `submission/guidelines/individual/` rồi sửa bản của bạn
> (xem `GUIDE.md`, Pha 1).

- Mã thành viên: `03246` (đổi thành mã 3 chữ số của bạn, đổi tên file thành `guideline_<mã>.md`)
- Phiên bản: v0 → v0.1-cá-nhân
- Loại nhãn: bounding box (hộp chữ nhật) trên ảnh đường phố
- Công cụ: CVAT, xuất COCO 1.0
- Tập ảnh: `img_001.jpg` … `img_040.jpg`

---

## 1. Mục tiêu mô hình 2D

Nhãn dùng để huấn luyện mô hình **phát hiện vật 2D** cho người và phương tiện trên ảnh đường phố (camera giao thông, xe tự lái).

- Mô hình chỉ cần trả lời: *"Có vật thật thuộc lớp X ở vị trí này không?"*
- **Nhãn chỉ gán cho vật thật đang tồn tại ngoài đời**, không gán cho tượng, ma-nơ-canh, đồ chơi, ảnh in, phản chiếu.
- Box phục vụ phát hiện, không phục vụ đếm hàng hóa hay phân loại chi tiết (hãng xe, biển số).
- **Phép thử lớp vs thuộc tính:** nếu mô hình 2D *phải đoán* thông tin đó khi suy luận → là **lớp**. Nếu chỉ dùng để lọc mẫu khó / kiểm QC → là **thuộc tính**.

---

## 2. Bảng lớp (mỗi vật thật thuộc đúng một lớp)

| Lớp | Định nghĩa | Ví dụ (ảnh) | Phản ví dụ — KHÔNG thuộc lớp này |
|---|---|---|---|
| `car` | Ô tô con 4 bánh chở người: sedan, SUV, hatchback, taxi. | Ô tô con đang chạy/đỗ; **ô tô con nằm trên sàn xe chở ô tô** (`img_002`) | Bán tải (→ `truck`); tượng/mô hình xe (`img_027`, `img_038`) |
| `truck` | Xe tải, xe bán tải (cabin + thùng), xe đầu kéo, xe chở xe. | Xe tải thùng; **xe tải chở 8 ô tô** (`img_002`); bán tải kể cả nắp thùng cao (`img_016`, `img_037` — *đề xuất, xem R-09*) | Xe buýt (→ `bus`); xe thùng kín cỡ nhỏ (→ `van`) |
| `van` | Xe thùng kín cỡ nhỏ, một khối thân liền, chở hàng/người (minivan, xe tải nhẹ thùng kín). | Xe van giao hàng | Bán tải có nắp thùng (tách riêng cabin → `truck`, R-09) |
| `bus` | Xe buýt / xe khách cỡ lớn, thân dài, nhiều cửa sổ. | `img_009`, `img_031`, `img_037`, `img_040` | Xe van chở khách cỡ nhỏ (→ `van`) |
| `tuk_tuk` | Xe ba bánh có động cơ, có mui hoặc thùng chở người/hàng. | `img_004`, `img_016`, `img_035` | Xe máy bình thường (→ `motorcycle`); xích lô đạp (không gán xe) |
| `motorcycle` | Xe máy, mô tô hai bánh có động cơ (không gồm người ngồi). | Xe máy đang chạy/đỗ | Xe đạp, xe đạp điện không bàn đạp thấy rõ — xem R-05 |
| `person` | Người thật (đi bộ, ngồi xe, đứng). | Người đi bộ; người lái và người ngồi sau xe máy | Tượng (`img_011`, `img_017`, `img_027`), ma-nơ-canh (`img_033`, `img_038`), ảnh chân dung trên biển quảng cáo (`img_018`), người trong phản chiếu (`img_020`) |
| `ignore_region` | Vùng không gán từng vật: đám đông/cụm vật dày đặc không tách được, hoặc cụm vật nhỏ cao < 20 px. | Đám đông xa | Vật lớn rõ ràng — luôn gán box riêng |

**Lớp không có trong danh sách (xe đạp, xích lô, xe lăn, động vật…)**: **không vẽ box**. Xem R-05.

---

## 3. Thuộc tính (đo được)

Tính theo **tỷ lệ diện tích của vật bị che / cắt so với diện tích vật nếu nhìn thấy đủ** (ước lượng bằng mắt, làm tròn theo ngưỡng).

### `occluded_level` (bắt buộc cho mọi box; không dùng cờ Q của CVAT)

| Giá trị | Ngưỡng | Cách hiểu |
|---|---|---|
| `0` | 0 % bị che | Thấy trọn vật. |
| `1` | 1 – 30 % | Bị che ít. |
| `2` | 31 – 70 % | Bị che nhiều. |
| `3` | > 70 %, **nhưng vẫn nhận dạng chắc chắn lớp** | Chỉ còn lộ phần nhỏ. Nếu *không nhận dạng chắc* → không vẽ box, ghi `image_issues.csv`. |

Ca đứng giữa hai mức → chọn mức **cao hơn** và tick `can_review` nếu chênh lệch < 5 %.

### `truncated`

| Giá trị | Ngưỡng |
|---|---|
| `no` | Vật nằm trọn trong khung ảnh. |
| `yes` | Một phần vật nằm **ngoài mép ảnh** (≥ 1 % thân vật bị cắt). Box chỉ ôm phần nhìn thấy trong ảnh, không vẽ ra ngoài biên. |

Phân biệt: *bị cắt bởi mép ảnh* → `truncated`; *bị vật khác che* → `occluded_level`. Một vật có thể có cả hai.

### `can_review`

Tick khi: (a) chưa chắc lớp, (b) chưa chắc ngưỡng `occluded_level`, (c) ca thuộc bảng ca khó mà quy tắc chưa phủ, (d) nhòe mờ khiến khó ôm box. **Không tick** để "cho chắc" với vật rõ ràng. Mọi ca tick phải được xóa cờ ở Pha 5 sau khi có quyết định.

---

## 4. Quy tắc vẽ box (khóa bằng mã R-xx)

**Nguyên tắc chung**
- **R-01 — Box ôm sát phần nhìn thấy.** Cạnh box chạm vào pixel ngoài cùng của phần nhìn thấy của vật, lệch tối đa 2–3 px. Không "đoán" phần bị che. Không vẽ vượt biên ảnh.
- **R-02 — Chỉ gán vật thật.** Tượng, ma-nơ-canh, đồ chơi, hình in/quảng cáo, màn hình, **phản chiếu** (gương chiếu hậu, kính tủ, vũng nước) → **không gán**. Ảnh chỉ có các vật này vẫn giữ nguyên trong Task, xuất ra 0 annotation.
- **R-03 — Không gán "xe chủ"/vật không thấy.** Chỉ gán khi thấy ít nhất một phần đặc trưng của vật (và `occluded_level` ≤ 3 vẫn nhận dạng chắc).
- **R-04 — Một vật = một box = một lớp.** Không chia một vật thành nhiều box; không gộp hai vật thành một box.

**Người, xe máy và các phương tiện đặc biệt**
- **R-05 — Người đi xe máy:** vẽ **2 hoặc nhiều box chồng nhau**: 1 box `motorcycle` ôm thân xe (bánh trước → bánh sau, tay lái → yên, **không** kéo lên đầu người), và 1 box `person` cho **mỗi người** (lái + từng người ngồi sau) ôm từ đỉnh đầu/mũ đến chân/phần thấy được. Hai box được phép chồng nhau. Hàng hóa cồng kềnh sau xe: không bao vào box xe.
  - **Xe đạp, xích lô đạp, xe đẩy:** **không vẽ box xe**, nhưng **vẫn vẽ `person`** cho người lái và người ngồi (`img_006`, `img_029`, `img_039`).
- **R-06 — Xe chở xe (`img_002`):** 1 box `truck` ôm thân xe tải/đầu kéo + sàn (không kéo lên bao trùm tất cả xe con); **thêm từng box `car`** cho mỗi ô tô nhìn thấy trên các sàn, `occluded_level` tính theo phần bị sàn/xe khác che. Xe nào thấy < 20 px cao → gộp vào `ignore_region` chỉ khi cả cụm không tách được.
- **R-07 — Hàng hóa quá khổ / chất cao (`img_035`):** box chỉ ôm **thân phương tiện**, **không** bao bao tải/hàng chất cao phía trên. Người ngồi trên hàng vẫn gán `person` riêng.
- **R-08 — Đồ gắn ngoài thân xe (đồ trên nóc, vỏ xe/ván/thùng buộc nóc `img_024`):** không bao vào box xe; box ôm thân xe.
- **R-09 — Ca lai giữa `truck`/`van`/`car` (bán tải nắp thùng cao `img_016`, `img_037`):** đề xuất thống nhất → **`truck`** nếu còn nhận ra cabin riêng + thùng phía sau (kể cả có nắp); **`van`** chỉ khi toàn thân là một khối kín liền không cabin tách; tick `can_review` tới khi nhóm khóa ở Pha 2.

**Vật bị che, nhỏ, mờ**
- **R-10 — Vật bị che:** vẫn gán nếu nhận dạng chắc lớp (≥ 30 % thấy được hoặc đặc trưng rõ, `occluded_level` ≤ 3). Box ôm phần nhìn thấy (R-01), **không ngoại suy** phần bị che.
- **R-11 — Vật nhỏ (cao < 20 px):** không vẽ box riêng lẻ. Nếu thành cụm khó tách → 1 `ignore_region` ôm cả cụm. Một vật nhỏ đơn lẻ nhưng rõ → vẫn vẽ nếu ≥ 20 px; dưới ngưỡng → bỏ qua.
- **R-12 — Đám đông dày:** khi người chồng lên nhau, không tách được từng người → 1 `ignore_region` cho vùng đó; người ở hàng đầu rõ ràng vẫn gán `person` riêng.
- **R-13 — Mờ chuyển động (`img_014`, `img_015`):** vẫn gán nếu nhận ra lớp; box ôm vùng lõi của vật (không gồm vệt mờ kéo dài); `occluded_level` giữ theo che thật, tick `can_review` nếu không chắc lớp.
- **R-14 — `ignore_region`:** không dùng để "đỡ" box khó. Chỉ dùng theo R-11/R-12. Không đặt ignore chồng lên box mà người khác có thể vẽ.

---

## 5. Bảng ca khó

| Ảnh | Tình huống | Quyết định | Quy tắc / lý do |
|---|---|---|---|
| `img_002.jpg` | Xe tải chở 8 ô tô con trên 2 sàn | 1 `truck` + 8 `car` | R-06: mô hình phải thấy xe con trên sàn là xe thật |
| `img_004.jpg`, `img_035.jpg` | Tuk-tuk 3 bánh; `img_035` chất hàng cao | `tuk_tuk`; box chỉ ôm thân xe, không bao bao tải | R-07; tuk-tuk khác `motorcycle` về hình dạng |
| `img_016.jpg` | Tuk-tuk + bán tải nắp cao | `tuk_tuk` / `truck` (can_review) | R-09 |
| `img_009.jpg`, `img_031.jpg`, `img_037.jpg`, `img_040.jpg` | Xe buýt cỡ lớn | `bus` | Tách khỏi `truck` vì hình dạng và hành vi khác |
| `img_037.jpg` | Bán tải gắn nắp thùng cao (Master 75) | `truck` (tạm), tick `can_review` | R-09 — mang ra họp Pha 2 |
| `img_011.jpg`, `img_017.jpg`, `img_027.jpg` | Tượng đồng/đá (người, xe bán tải) | **Không gán** | R-02 |
| `img_033.jpg`, `img_038.jpg` | Ma-nơ-canh, xe đồ chơi trong tủ kính | **Không gán** | R-02 |
| `img_018.jpg` | Chân dung trên biển quảng cáo | **Không gán** | R-02 |
| `img_020.jpg` | Người/xe phản chiếu trong gương chiếu hậu | **Không gán** | R-02 |
| `img_006.jpg`, `img_029.jpg`, `img_039.jpg` | Xe đạp, xích lô không động cơ | Chỉ gán `person`, không box xe | R-05 |
| `img_024.jpg` | Vỏ xe/đồ chằng trên nóc xe | Box chỉ ôm thân xe | R-08 |
| `img_014.jpg`, `img_015.jpg` | Mờ chuyển động | Gán nếu nhận ra, box lõi; tick `can_review` nếu không chắc | R-13 |
| `img_026.jpg` | Ảnh 784×1024 — dễ vẽ box vượt biên | Cắt box tại mép ảnh, `truncated = yes` | R-01 |
| Ảnh có 0 đối tượng (`img_017`, `img_020`, `img_027`, `img_033`, `img_038`) | Chỉ tượng/ma-nơ-canh/phản chiếu | Giữ ảnh trong Task, xuất 0 annotation | R-02 |

> Thêm các ca khác bạn tự gặp khi duyệt đủ 40 ảnh (ghi tên ảnh, quyết định, lý do).

---

## 6. QC, nhãn nghi vấn và lịch sử phiên bản

- Ca mơ hồ trên box đã vẽ: tick `can_review`.
- Vật chưa vẽ được box: ghi `submission/qc/image_issues.csv` (cột `image,annotator,region,note`).
- **Gán độc lập (Pha 3):** không hỏi đồng đội khi đang gán.
- Mọi thay đổi **làm đổi nhãn** (lớp, box, thuộc tính, ngưỡng) → tăng phiên bản, ghi `kind = semantic` và kiểm lại cả lô 40 ảnh. Sửa câu chữ → `editorial`.
- Số liệu mục tiêu IAA: IoU TB ≥ 0.85, đồng thuận lớp ≥ 95 %; QC toàn lô: ảnh lỗi ≤ 2 % mới Release.

| Phiên bản | Thay đổi |
|---|---|
| v0 | Bản khởi đầu của giảng viên (5 lớp, thuộc tính định tính) |
| v0.1-cá-nhân | Thêm `bus`, `tuk_tuk`; ngưỡng đo được; R-01…R-14; bảng ca khó |
