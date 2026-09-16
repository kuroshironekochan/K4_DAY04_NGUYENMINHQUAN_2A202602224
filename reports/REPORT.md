# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Nguyễn Minh Quân   Nhóm: solo   Ngày: 17/9/2026

> Cách dùng: copy file này thành `reports/REPORT.md`. Điền bằng số liệu do công cụ sinh ra;
> không tự ước lượng hoặc sửa số trong file JSON.

## 1. Nhãn của tôi

<!-- Lấy số từ reports/visibility_report.md hoặc outputs/visibility_report.json sau Chặng 4.
Số ảnh phải là 20; số skeleton là tổng số người trong 20 ảnh. Thời gian trung bình = tổng
thời gian gán / 20. -->

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 326 / 134 / 33 |
| Thời gian trung bình mỗi ảnh | 10 phút |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `left_ear` — 69%
2. `right_ear` — 48%
3. `left_wrist` — 34% (và `left_eye` — 34%)

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

Không hoàn toàn. Các khớp như `left_ear` và `right_ear` có tỉ lệ `%v=1` cao nhất chủ yếu là do đặc điểm thị giác: tai người trong ảnh đời thường rất hay bị che khuất bởi tóc dài, mũ nón hoặc góc nghiêng của đầu (thuộc nhóm "hay bị che"), tuy nhiên vị trí giải phẫu của tai lại tương đối dễ suy đoán nhờ đối xứng với mắt và gò má. Ngược lại, những khớp tôi thấy khó gán nhất lại là hông (`left_hip`, `right_hip`) và cổ tay (`left_wrist`, `right_wrist`) khi đối tượng mặc quần áo thụng hoặc bị che bởi đồ vật/người khác. Khi đó, việc xác định chính xác tâm ổ khớp giải phẫu nằm ở đâu bên dưới lớp vải rất mơ hồ và tốn nhiều thời gian cân nhắc nhất.

## 2. Chấm với gold

<!-- Lấy hai cột từ outputs/eval_vs_gold.json: một lần ngay khi protected release mở và một
lần sau rework. Đếm số phần tử trong từng danh sách lỗi, không tự làm tròn. -->

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.931 | 0.945 |
| OKS@0.50 | 1.000 | 1.000 |
| OKS@0.75 | 1.000 | 1.000 |
| Lỗi `dao_trai_phai` | 1 | 0 |
| Lỗi `nham_nguoi` | 1 | 0 |
| Lỗi `xoa_khop_bi_che` | 2 | 0 |

**Tôi đã sửa gì giữa hai lần chạy** (ghi cụ thể: ảnh nào, người thứ mấy, khớp nào):

- `train_13.jpg`, người #1: Cả skeleton bị đảo trái/phải — Đã hoán đổi lại toàn bộ các cặp keypoint trái và phải (vai, khuỷu, cổ tay, hông, gối, mắt cá) theo đúng hệ quy chiếu giải phẫu của cơ thể người.
- `train_04.jpg`, người #1, khớp `left_wrist`: Chấm cổ tay bị kéo nhầm sang cổ tay của người đứng bên cạnh (tọa độ x ~ 0.59) — Kéo chấm `left_wrist` về đúng vị trí cổ tay trái của người đang gán (tọa độ x ~ 0.50) và đặt cờ `v=1` do bị che khuất một phần.
- `train_10.jpg`, người #0, khớp `left_hip` và `right_hip`: Trước đó để `v=0` (Outside) do thấy bị áo khoác che khuất — Đặt lại chấm ước lượng cho cả 2 khớp hông trên trục thân mình nối từ vai xuống gối và chuyển sang cờ `v=1` (Occluded).

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Ảnh đó dễ hay khó? Nếu là ảnh dễ,
bạn nghĩ vì sao mình vẫn sai?

Lỗi đảo trái/phải xảy ra ở ảnh `train_13.jpg`, người thứ #1. Đây là bức ảnh tương đối dễ vì người đứng đối diện gần ống kính. Nguyên nhân dẫn đến sai sót là do lúc gán nhãn thao tác nhanh, tôi đã nhìn theo thị giác trực diện của bức ảnh (bên trái khung hình) thay vì xét theo hệ quy chiếu giải phẫu của người trong ảnh (tay trái của người đứng đối diện sẽ nằm ở bên phải của khung ảnh). Sau khi chạy `visualize_pose.py` thấy đường nối xương bị chéo và màu xanh (trái) / cam (phải) bị ngược, tôi đã nhận ra và sửa lại.

## 3. Kiểm chéo

Bạn cùng nhóm: solo (đối chiếu bộ dữ liệu mẫu lớp)

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| `left_ear` | 69% | 0% | 69% | Guideline chưa rõ: Tiêu chí xác định cờ che khuất ở tai khi bị tóc hoặc mũ che phủ một phần chưa đồng nhất (một bên gán v=1 khi bị tóc phủ, một bên coi là v=2 hoặc v=0). |
| `right_ear` | 48% | 0% | 48% | Guideline chưa rõ: Chưa có quy định cụ thể về ngưỡng phần trăm diện tích tai bị che thì chuyển từ v=2 sang v=1. |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

- Tai bị tóc hoặc phụ kiện che khuất trên 50% diện tích: Bắt buộc gắn cờ `v=1` và chấm vị trí ước lượng đối xứng giải phẫu ngang hàng đuôi mắt; chỉ gắn `v=2` khi nhìn thấy trọn vẹn vành tai.

## 4. Model

<!-- Chép số từ outputs/eval_model.json sau Chặng 6. “Chênh” = sau fine-tune trừ baseline;
đây là quan sát trên tập test, không phải chất lượng sản phẩm. -->

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.7250 | 0.7010 | -0.0240 |
| pose_mAP50-95 | 0.4520 | 0.4208 | -0.0312 |
| pose_precision | 0.6840 | 0.6520 | -0.0320 |
| pose_recall | 0.6410 | 0.6230 | -0.0180 |
| box_mAP50-95 | 0.6120 | 0.6100 | -0.0020 |

### Trả lời năm câu hỏi ở cuối notebook

> Mỗi câu cần trỏ tới ảnh/chỉ số cụ thể. Một con số thấp không tự chứng minh nhãn sai;
> kiểm lại bằng bằng chứng thị giác và kết quả gold.

1. `pose_mAP50-95` thay đổi bao nhiêu? Nếu nó giảm, 20 ảnh của bạn dạy được model
   điều gì mà COCO chưa dạy, và nó làm hỏng điều gì?
   Chỉ số `pose_mAP50-95` giảm 0.0312 (từ 0.4520 xuống 0.4208). Bộ 20 ảnh train của tôi dạy model tuân thủ nghiêm ngặt quy tắc gắn cờ `v=1` và ước lượng các khớp bị che (như hông mặc quần thụng hay tai bị tóc che), điều mà nhãn COCO gốc thường thả lỏng (để `v=0` hoặc không gán). Tuy nhiên, do kích thước tập train quá nhỏ (chỉ 20 ảnh) so với tập dữ liệu phong phú của COCO, model đã bị hiện tượng phân phối hẹp (overfitting cục bộ), làm giảm độ ổn định tổng thể khi đánh giá trên tập test với các góc chụp và tư thế đa dạng khác.

2. `box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm *người* dễ hơn hay tìm
   *khớp* dễ hơn? Vì sao?
   Sau khi fine-tune, `box_mAP50-95` đạt 0.6100 trong khi `pose_mAP50-95` đạt 0.4208, chênh lệch 0.1892. Model tìm *người* dễ hơn rất nhiều so với tìm *khớp*. Hộp bao quanh (bounding box) chỉ cần xác định vùng không gian bao bọc cơ thể người dựa trên các đặc trưng diện tích lớn (thân mình, trang phục), dung sai IoU khá thoải mái. Trong khi đó, việc xác định 17 keypoint đòi hỏi độ chính xác đến từng pixel của từng khớp giải phẫu cụ thể, vốn thường xuyên bị che khuất, bị trùng lấp giữa nhiều người và dễ bị nhầm lẫn trái/phải.

3. Một ảnh test model đoán sai - gọi tên lỗi theo bốn loại của slide 43
   (lệch nhẹ / đảo trái/phải / nhầm người / trượt hẳn):
   Ở ảnh `test_07.jpg`, model mắc lỗi **nhầm người**: Khớp cổ tay và khuỷu tay của người đứng phía trước bị model nối nhầm sang thân người đứng sát phía sau. Lỗi này xảy ra do hai cơ thể đứng chồng lấn ở cự ly quá gần, khiến mô hình không phân tách rõ ràng được ranh giới các chi.

4. Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng, và bạn dựa vào đâu?
   Ảnh có OKS thấp nhất giữa nhãn gán và model là `train_12.jpg` (OKS đạt ~0.42). Trong tình huống này, **nhãn của tôi đúng**. Người trong ảnh đang ở tư thế cúi gập người và quay lưng; model bị trượt hẳn khớp vai và hông vào nếp gấp balo/áo khoác, đồng thời bị đảo trái/phải. Khi đối chiếu trực tiếp với giải phẫu cơ thể trên ảnh gốc, nhãn của tôi định vị đúng các điểm xoay tự nhiên của vai và hông.

5. Ảnh bạn gán tệ nhất có *cũng* là ảnh model đoán tệ nhất không? Nếu có, điều đó
   nói gì về bức ảnh đó?
   Không trùng nhau. Ảnh tôi gán tệ nhất trước rework là `train_13.jpg` (do bất cẩn thao tác dẫn đến đảo trái/phải), trong khi ảnh model đoán tệ nhất là `train_12.jpg` và `train_18.jpg` (nơi có nhiều người che khuất lẫn nhau và tư thế phức tạp). Điều này cho thấy lỗi của người gán chủ yếu là lỗi quy ước hệ quy chiếu trong thao tác, còn lỗi của model là do hạn chế về khả năng suy luận thị giác trước các ca che khuất và chồng lấp nặng.

## 5. Một rule evidence bạn đã dùng

Chọn một keypoint trong ảnh core mà bạn phải quyết định giữa `v=1` và `v=0`. Nêu ảnh, người,
khớp, bằng chứng nhìn thấy và lý do chọn trạng thái đó trong 3-5 câu.

Trong ảnh `train_10.jpg`, ở người thứ #0 (người ngồi tựa), tôi phải quyết định chọn cờ cho khớp hông trái `left_hip`. Khi quan sát, toàn bộ vùng xương chậu và hông bị áo khoác dài cùng tư thế ngồi gập che khuất hoàn toàn, không lộ bề mặt da hay đường viền xương. Tuy nhiên, bằng chứng thị giác rõ ràng là cả phần thân trên (vai trái, ngực) và phần chân (đầu gối trái) đều nằm hoàn toàn bên trong khung ảnh. Điều này chứng minh chắc chắn khớp hông trái vẫn nằm trong giới hạn không gian của bức ảnh chứ không bị cắt ra ngoài mép. Do đó, theo đúng quy tắc evidence của bài học, tôi đã định vị khớp bằng phương pháp ngoại suy trục vai - gối và gán cờ `v=1` (Occluded) thay vì `v=0` (Outside).
