# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe con, ô tô cá nhân chở người từ 4–9 chỗ | Sedan, hatchback, SUV, crossover; có cốp hoặc đuôi thấp |
| `truck` | Xe tải chuyên dụng dùng để chở hàng hóa, vật liệu. | Cabin tách rời với thùng hàng phía sau (thùng kín, thùng lửng) |
| `bus` | Xe buýt công cộng, xe khách cỡ lớn | Thân xe rất dài, trần cao, nhiều hàng cửa sổ khách nối tiếp |
| `van` | Xe tải nhỏ, xe minivan chở hàng hoặc chở khách cỡ nhỏ | Dáng hộp vuông vức, trần cao từ trước ra sau, đuôi đứng, cabin liền thân |

## Quy tắc chung

- Box bám sát vật thể: Vẽ khung bao sát các mép của phương tiện nhìn thấy được, hạn chế lấy phần nền xung quanh.
- Vật thể bị che khuất: Chỉ vẽ phần phương tiện nhìn thấy, không đoán hoặc vẽ phần bị che khuất.
- Vật thể bị cắt bởi rìa ảnh: Kéo box sát đến mép khung ảnh tại vị trí phương tiện bị cắt, không suy đoán phần nằm ngoài ảnh.
- Vật thể rất nhỏ/mơ hồ: Bỏ qua nếu quá mờ hoặc không nhận diện rõ loại xe, không đoán mò.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_024.jpg` | Đường ướt đông đúc; có xe ba bánh tự chế có mái che di chuyển giữa các xe ô tô | Không gán nhãn cho xe ba bánh | Phương tiện thô sơ/ba bánh không thuộc 4 lớp schema (chỉ gán car, truck, bus, van). |
| `drive_025.jpg` | Ô tô ở làn đối diện bị dải phân cách cây xanh che khuất nửa dưới thân xe | Gán `car`, chỉ vẽ box ôm nửa trên nhìn thấy | Xe bị che bởi cây vẫn gán nếu nhận diện rõ xe con; không vẽ trùm qua cây che khuất. |
| `drive_023.jpg` | Xe buýt 2 tầng bên phải bị cắt mép ảnh và có người đứng chờ ở trạm xe | Gán `bus` cho xe buýt; Không gán người đi bộ | Xe buýt 2 tầng thuộc lớp `bus`, box kéo sát mép viền ảnh; người đi bộ không thuộc schema. |
