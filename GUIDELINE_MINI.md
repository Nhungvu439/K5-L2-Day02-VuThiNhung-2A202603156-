# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` |Xe ô tô con, gồm cả xe bán tải | Sedan, SUV, Ford Ranger|
| `truck` |Xe tải chuyên chở hàng hóa | Xe tải thùng, xe đầu kéo|
| `bus` |Xe chở nhiều hành khách |Xe buýt, xe khách, minibus |
| `van` | Xe thân dạng hộp, chở người hoặc hàng| Xe van, minivan|

## Quy tắc chung

- Box bám sát vật thể nghĩa là: Vẽ bounding box ôm sát phần xe nhìn thấy, không lấy dư nền, mỗi xe một box riêng.
- Vật thể bị che khuất: Vẫn gán nhãn nếu nhận diện được loại xe; box ôm sát phần nhìn thấy, không tự suy đoán phần bị che.
- Vật thể bị cắt bởi rìa ảnh: Nếu nhận diện được xe, vẫn gán nhãn; box không vượt ra ngoài ranh giới ảnh.   
- Vật thể rất nhỏ/mơ hồ: Chỉ gán nhãn khi đủ thông tin xác định lớp. Nếu không chắc chắn, ghi lại để kiểm tra, không tự đoán.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_010.jpg` |Xe ở xa bên phải gần ngã tư, ảnh ban đêm nên khó quan sát. | Gán car (0) nếu phóng to đủ rõ để nhận diện. | Không đoán lớp khi ảnh quá mờ. |
| `drive_035.jpg` | Xe buýt đỏ gần trạm, có thể bị che một phần. | Gán bus (2), box ôm sát phần nhìn thấy. | Thân xe dài, nhiều cửa sổ, phù hợp lớp bus. |
| `drive_040.jpg` | Ô tô đen góc dưới bên phải bị cắt bởi mép ảnh. | Gán car (0), box không vượt khỏi ảnh. | Vẫn nhận diện được xe con dù bị cắt khung. |