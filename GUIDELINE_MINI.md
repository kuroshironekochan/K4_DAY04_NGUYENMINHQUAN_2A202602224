# Mini guideline - nhóm: solo  |  người gán: Nguyễn Minh Quân  |  ngày: 17/9/2026

> Điền file này **trong lúc** gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (phải điền)

| Tình huống | Luật nhóm bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Luôn gán `v = 1`, đặt chấm ước lượng tại vị trí khớp háng trên trục nối từ vai xuống đầu gối. | Khớp vẫn ở trong khung ảnh nhưng bị vải che khuất; nếu để `v = 0` model sẽ học sai rằng người mặc quần thì không có hông. |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Che > 50% diện tích thì gắn `v = 1` và chấm đối xứng ngang hàng đuôi mắt; thấy rõ toàn bộ vành tai thì để `v = 2`. | Đảm bảo tiêu chí thống nhất khi tai bị che khuất cục bộ, tránh tranh cãi giữa nhìn thấy một phần và bị che. |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Các khớp thân dưới (gối, mắt cá) nằm ngoài mép ảnh -> gắn `v = 0` (Outside), không đặt chấm. | Khớp đã ra ngoài giới hạn bức ảnh, không được phép đoán mò ngoài khung hình. |
| Cổ tay nằm sau tay lái / sau thân mình | Gán `v = 1`, đặt chấm ước lượng tại điểm kết thúc của cẳng tay. | Khớp vẫn trong ảnh và xác định được vị trí giải phẫu nhờ hướng đi của xương cẳng tay. |
| Hai người chồng lên nhau | Ước lượng từng người theo đúng cơ thể người đó, khớp bị đối tượng kia che thì để `v = 1`; tuyệt đối không gắn sang cơ thể bên cạnh. | Ngăn chặn lỗi nghiêm trọng "nhầm người" khiến skeleton bị biến dạng và kéo dài bất thường. |
| Người nhỏ đến mức nào thì không gán nữa | Bounding box có chiều cao < 30px hoặc bị mờ nhòe đến mức không nhận diện được đầu - thân - chi thì không gán skeleton. | Độ phân giải quá thấp không đủ căn cứ giải phẫu tin cậy để đặt 17 điểm. |

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_10.jpg`, người thứ `0`, khớp `left_hip` và `right_hip`

- Mơ hồ ở chỗ nào: Người ngồi gập người, mặc áo khoác dài phủ kín toàn bộ vùng thắt lưng và đùi trên, hoàn toàn không thấy bề mặt hông.
- Bạn quyết thế nào: Đặt chấm ước lượng cho cả hai khớp hông dựa trên trục thân trên hạ từ hai vai xuống và hướng của xương đùi, gắn cờ `v = 1`.
- Vì sao: Khớp chắc chắn còn nằm bên trong khung hình vì cả thân trên và gối đều trong ảnh.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu để `v = 0`, model sẽ bị phạt OKS vô lý và học rằng người ngồi mặc áo khoác thì không tồn tại khớp hông.

### Ca 2 - ảnh `train_04.jpg`, người thứ `1`, khớp `left_wrist`

- Mơ hồ ở chỗ nào: Hai người đứng sát cạnh nhau, tay trái của người này chạm gần vào cánh tay của người kia trong vùng ánh sáng phức tạp.
- Bạn quyết thế nào: Dựa theo trục cẳng tay trái của chính người đó để chấm cổ tay trái tại x ~ 0.50 và gán `v = 1`, không chấm theo bàn tay người bên cạnh.
- Vì sao: Tránh bị nhầm lẫn giữa hai cơ thể cạnh nhau.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Model sẽ học thói quen "nhầm người", kéo xương của người này dính vào chi của người khác.

### Ca 3 - ảnh `train_13.jpg`, người thứ `1`, các cặp khớp trái/phải

- Mơ hồ ở chỗ nào: Người đứng đối diện nhìn thẳng vào camera, tay chân hơi giang rộng, dễ gây nhầm lẫn quy ước hướng trái/phải.
- Bạn quyết thế nào: Xác định trái/phải theo cơ thể của người đó (tay trái của họ ở bên phải bức ảnh) và gán nhãn đúng quy chuẩn giải phẫu.
- Vì sao: Quy tắc bất di bất dịch của COCO và bài lab là tính theo cơ thể người, không theo mắt người quan sát.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Model bị lỗi "đảo trái/phải" — lỗi nặng nhất khiến augmentation lật ảnh (fliplr) nhân đôi sai số và làm hỏng hoàn toàn khả năng học pose.

## 4. Sau khi so visibility report với bạn cùng nhóm

- Khớp lệch `%v=1` nhiều nhất: `left_ear` (bạn `69%` / họ `0%`) và `right_ear` (bạn `48%` / họ `0%`)
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**: Guideline chưa rõ về việc xử lý các trường hợp tai bị tóc hoặc nón bảo hiểm che khuất một phần.
- Luật mới bổ sung vào mục 2 sau khi thống nhất: Tai bị che khuất > 50% diện tích bởi tóc hoặc phụ kiện thì bắt buộc gắn `v = 1` và ước lượng vị trí giải phẫu; nếu thấy rõ toàn bộ vành tai thì giữ `v = 2`.
