# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 29 skeleton, trung bình 15.86 khớp có v > 0 mỗi người
- Tổng: v=2 326 | v=1 134 | v=0 33

So sánh với `..\ban_cung_nhom\dataset\labels\train` (0 skeleton).
Cột **lệch** là hiệu số phần trăm v=1 - chỗ nào lệch nhiều nhất là chỗ guideline chưa nói rõ.

| # | Khớp | %v=1 (bạn) | %v=1 (đối chiếu) | lệch |
| ---: | --- | ---: | ---: | ---: |
| 3 | left_ear | 69% | 0% | 69 |
| 4 | right_ear | 48% | 0% | 48 |
| 1 | left_eye | 34% | 0% | 34 |
| 9 | left_wrist | 34% | 0% | 34 |
| 2 | right_eye | 31% | 0% | 31 |
| 10 | right_wrist | 31% | 0% | 31 |
| 13 | left_knee | 28% | 0% | 28 |
| 14 | right_knee | 28% | 0% | 28 |
| 7 | left_elbow | 24% | 0% | 24 |
| 16 | right_ankle | 24% | 0% | 24 |
| 0 | nose | 21% | 0% | 21 |
| 11 | left_hip | 21% | 0% | 21 |
| 5 | left_shoulder | 17% | 0% | 17 |
| 12 | right_hip | 17% | 0% | 17 |
| 15 | left_ankle | 17% | 0% | 17 |
| 8 | right_elbow | 10% | 0% | 10 |
| 6 | right_shoulder | 7% | 0% | 7 |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
