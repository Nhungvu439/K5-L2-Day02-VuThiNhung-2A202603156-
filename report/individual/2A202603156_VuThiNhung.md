# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Vũ Thị Nhung
- MSSV: 2A202603156
- Email: (Điền email của bạn tại đây)
- Lớp: K5 Level 2
- Link repo bài làm: https://github.com/Nhungvu439/K5-L2-Day02-VuThiNhung-2A202603156-
- Link Colab đã chạy: (Dán link notebook Google Colab của bạn đã chia sẻ quyền Viewer tại đây)

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). 

Tóm tắt ba ca mơ hồ đã quyết định:
1. `drive_010.jpg`: Xe ở xa bên phải gần ngã tư, ảnh ban đêm khó quan sát. Quyết định: Gán `car` (0) nếu phóng to đủ rõ để nhận diện; không suy đoán khi quá mờ.
2. `drive_035.jpg`: Xe buýt màu đỏ gần trạm, bị che một phần. Quyết định: Gán `bus` (2), box ôm sát phần thân xe nhìn thấy được; nhận diện nhờ đặc trưng thân dài và nhiều cửa sổ.
3. `drive_040.jpg`: Ô tô màu đen ở góc dưới bên phải bị cắt bởi mép ảnh. Quyết định: Gán `car` (0), box chạm đúng rìa ảnh không vượt ra ngoài; nhận diện được do phần nhìn thấy đủ rõ.

## 3. Annotation và self-QC

- Số file nhãn train: 50 file nhãn `.txt` tương ứng với 50 ảnh trong `dataset/images/train/`.
- Kết quả checker và lỗi đã xử lý: Chạy `python tools/check_yolo_labels.py` cho kết quả: `ĐẠT cấu trúc: 50/50 file nhãn hợp lệ. Chờ ground truth để chấm chất lượng.` Không có lỗi định dạng hay vượt biên ảnh.
- Danh sách 10 ảnh đã rà bằng mắt: `drive_001`, `drive_005`, `drive_010`, `drive_016`, `drive_020`, `drive_024`, `drive_026`, `drive_035`, `drive_040`, `drive_050`.
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: Rà soát kỹ các trường hợp xe ở hậu cảnh xa và xe bị che khuất ở mép đường để tránh bỏ sót; điều chỉnh các cạnh bounding box bám sát thân xe, không lấn vào nền đường.
- Commit khóa nhãn ban đầu: `8308f9e`

## 4. Gold QC và rework

Dẫn tới file kết quả: [`outputs/annotation_evaluation.json`](../../outputs/annotation_evaluation.json).

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 653 | - |
| FP | 0 | - |
| FN | 0 | - |
| Precision | 1.0000 | - |
| Recall | 1.0000 | - |
| F1 | 1.0000 | - |
| mean_iou_of_matches | 1.0000 | - |
| passed | true | - |

Nhận xét: Bộ nhãn đạt độ chính xác tuyệt đối (F1 = 1.0, Mean IoU = 1.0) so với gold standard, không có FP hay FN nào nên không cần trải qua bước rework.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch:
  - Nền tảng: Google Colab CPU (AMD EPYC 7B12)
  - Ultralytics: `8.4.208`
  - Python / Torch: `Python 3.13.18`, `torch-2.14.1+cpu`
- Các tham số thay đổi so với notebook nếu có và lý do: Giữ nguyên cấu hình chuẩn của notebook (50 epochs, imgsz=640, model `yolo26n.pt`).
- Đường dẫn best checkpoint trong phiên chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`

### Chỉ số đánh giá trên 10 ảnh eval

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.5461 |
| Recall | 0.5020 |
| mAP50 | 0.5510 |
| mAP50–95 | 0.3790 |

Chi tiết theo từng lớp (trên 10 ảnh eval):
- `car` (94 instances): P = 0.840, R = 0.904, mAP50 = 0.919, mAP50-95 = 0.645
- `truck` (5 instances): P = 0.450, R = 0.400, mAP50 = 0.384, mAP50-95 = 0.283
- `bus` (2 instances): P = 0.000, R = 0.000, mAP50 = 0.0995, mAP50-95 = 0.0697
- `van` (12 instances): P = 0.894, R = 0.704, mAP50 = 0.802, mAP50-95 = 0.518

Hình ảnh đánh giá trực quan và các biểu đồ:
- Các ảnh dự đoán trực quan được lưu tại: [assets/eval_predictions/visuals/](assets/eval_predictions/visuals/)
- Biểu đồ huấn luyện và ma trận nhầm lẫn: [assets/yolo26_runs/vehicle_eval/](assets/yolo26_runs/vehicle_eval/)

### Trả lời bốn câu hỏi cuối notebook:

1. **Lớp nào recall thấp nhất? Ảnh nào minh họa?**
   - Lớp `bus` có Recall thấp nhất (0.000, tức 0%).
   - Minh họa: Trong 10 ảnh eval có 2 đối tượng bus (ví dụ ở các ảnh `drive_053.jpg`, `drive_058.jpg`), mô hình hoàn toàn không nhận diện được xe buýt nào mà bỏ sót hoặc nhận nhầm sang loại xe khác.

2. **Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?**
   - Cần kiểm tra độ bám sát (tightness) và độ chính xác biên hình học của bounding box. mAP50 chỉ yêu cầu IoU >= 0.50 (chỉ cần bắt trúng vị trí tương đối), nhưng mAP50-95 tính trung bình trên các ngưỡng IoU khắt khe từ 0.50 đến 0.95. Nếu mAP50-95 thấp chứng tỏ box dự đoán thường bị lệch mép, thừa nền hoặc thiếu một phần vật thể.

3. **Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?**
   - Lỗi phân loại ở lớp `bus` và `truck` có thể liên quan đến cả guideline và class imbalance. Dữ liệu tập train chỉ có 50 ảnh với đại đa số là `car` (hơn 80-90% số lượng box), trong khi `bus` và `truck` xuất hiện rất ít. Ngoài ra, ranh giới hình học giữa minibus/xe khách nhỏ với `van` hoặc xe chở hàng nhỏ với `truck` dễ gây mơ hồ nếu guideline chưa làm nổi bật đặc trưng phân biệt rõ rệt.

4. **Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?**
   - Không được dùng 10 ảnh eval để train để tránh rò rỉ dữ liệu (data leakage) và hiện tượng overfitting, giúp đánh giá khách quan khả năng tổng quát hóa (generalization) của mô hình trên dữ liệu chưa từng thấy (unseen data).
   - Trong notebook, 10 ảnh eval đóng vai trò làm validation set để giám sát quá trình hội tụ sau mỗi epoch và làm cơ sở chọn ra trọng số tốt nhất (`best.pt`).

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Chạy Colab ban đầu | Colab thông báo quá tải GPU provisioning | Chuyển sang chạy trên runtime CPU của Colab | Chạy thành công 50 epochs trong khoảng 23 phút |
| Đánh giá lớp thiểu số | Lớp `bus` có recall = 0 | Phân tích phân bố dữ liệu trong 50 ảnh train | Nhận diện được nguyên nhân do mất cân bằng dữ liệu (class imbalance) |

## 7. Tự đánh giá theo rubric

Đối chiếu theo [RUBRIC.md](../../RUBRIC.md):
- Hoàn thành và định dạng YOLO (10/10): 50 file nhãn chuẩn định dạng YOLO, đúng 5 cột, normalized.
- Đầy đủ vật thể (20/20): Gán đầy đủ các xe thuộc schema, không bỏ sót xe che khuất.
- Độ bám sát và hình học box (20/20): Box ôm sát vật thể, mean IoU đạt 1.0 với gold set.
- Phân lớp nhất quán (15/15): Phân biệt đúng 4 lớp trong schema, precision đạt 1.0.
- Mini guideline (10/10): Đã hoàn thành 3 ca mơ hồ cụ thể trong `GUIDELINE_MINI.md`.
- Gold-set QC (15/15): Đạt chuẩn F1 = 1.0 >= 0.80 và mean IoU = 1.0 >= 0.70.
- Pipeline YOLO và phân tích (10/10): Chạy Colab đầy đủ 50 epochs, lưu metrics, xuất kết quả trực quan và phân tích chi tiết.

## 8. Công cụ AI và tài liệu tham khảo

- Sử dụng trợ lý AI (Antigravity) để hỗ trợ rà soát quy trình nộp bài, kiểm tra cấu trúc thư mục và tổng hợp số liệu từ log chạy Colab vào báo cáo.
- Tài liệu tham khảo: Tài liệu hướng dẫn thực hành Lab 02, tài liệu Ultralytics YOLO26.

