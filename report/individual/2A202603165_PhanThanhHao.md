# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Phan Thanh Hào
- MSSV: 2A202603165
- Email: phanthanhhao06122005@gmail.com
- Lớp: 2A
- Link repo bài làm: https://github.com/phanthanhhao0612/K5-L2A-Day02-PhanThanhHao-2A202603165-ObjectDetection.git
- Link Colab đã chạy: https://colab.research.google.com/drive/1NidcfV5biEeMdUcAF2go2vA-sOGgr9yR?usp=sharing

---

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md).

### Tóm tắt định nghĩa 4 lớp:
- **`car` (class 0):** Ô tô con, xe cá nhân từ 4–9 chỗ (sedan, hatchback, SUV, crossover...).
- **`truck` (class 1):** Xe tải chở hàng hóa, vật liệu, cabin tách rời thùng hàng.
- **`bus` (class 2):** Xe buýt công cộng, xe khách lớn, thân dài và nhiều hàng cửa sổ.
- **`van` (class 3):** Xe minivan, xe tải nhỏ chở hàng/khách cỡ nhỏ, dáng hộp vuông vức, cabin liền thân.

### Tóm tắt ba ca mơ hồ đã quyết định:
1. **`drive_024.jpg`:** Đường ướt đông đúc, có xe ba bánh tự chế có mái che di chuyển giữa các ô tô $\rightarrow$ **Quyết định:** Không gán nhãn cho xe ba bánh vì không thuộc 4 lớp phương tiện trong schema (chỉ nhận `car`, `truck`, `bus`, `van`).
2. **`drive_025.jpg`:** Ô tô ở làn đối diện bị dải phân cách cây xanh che khuất nửa dưới thân xe $\rightarrow$ **Quyết định:** Gán nhãn `car`, chỉ vẽ bounding box ôm sát nửa trên thân xe nhìn thấy được, không vẽ trùm qua phần cây che khuất.
3. **`drive_023.jpg`:** Xe buýt 2 tầng bên phải bị cắt mép ảnh và có người đứng chờ ở trạm xe $\rightarrow$ **Quyết định:** Gán nhãn `bus` cho xe buýt 2 tầng, kéo box sát mép viền ảnh; không gán nhãn người đi bộ vì không thuộc schema.

### Phần cần giảng viên làm rõ:
- Ranh giới phân loại cụ thể giữa dòng xe bán tải cỡ lớn (pickup truck) và SUV (`car`) hoặc xe tải nhỏ dạng thùng kín (`van`).
- Tiêu chí kích thước pixel tối thiểu để bỏ qua các phương tiện ở hậu cảnh quá xa và mờ.

---

## 3. Annotation và self-QC

- **Số file nhãn train:** 50 file `.txt` trong `dataset/labels/train/` (tương ứng từ `drive_001.txt` đến `drive_050.txt`).
- **Kết quả checker và lỗi đã xử lý:** Chạy công cụ kiểm tra tự động `python tools/check_yolo_labels.py`:
  ```text
  ĐẠT cấu trúc: 50/50 file nhãn hợp lệ. Chờ ground truth để chấm chất lượng.
  ```
  Không có lỗi tọa độ ngoài khoảng [0, 1] hay sai cú pháp 5 cột.
- **Danh sách 10 ảnh đã rà bằng mắt:** `drive_001.jpg`, `drive_005.jpg`, `drive_010.jpg`, `drive_015.jpg`, `drive_020.jpg`, `drive_025.jpg`, `drive_030.jpg`, `drive_035.jpg`, `drive_040.jpg`, `drive_045.jpg`.
- **Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa:** Phát hiện một số xe ở rìa ảnh bị kéo box thừa ra ngoài vùng ảnh, hoặc box bao cả phần cây/dải phân cách che khuất; đã chỉnh lại mép box bám sát phần nhìn thấy của vật thể và bổ sung một số xe con ở xa làn đối diện.
- **Commit khóa nhãn ban đầu:** `3f6797b` (*feat: hoan thanh CP1 mini guideline va CP2-CP4 khoa bo nhan ban dau 50 anh train*).

---

## 4. Gold QC và rework

Dẫn tới file kết quả: [outputs/annotation_evaluation.json](../../outputs/annotation_evaluation.json).

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 795 | - |
| FP | 166 | - |
| FN | 163 | - |
| Precision | 0.8273 | - |
| Recall | 0.8299 | - |
| F1 | **0.8286** | - |
| mean_iou_of_matches | **1.0000** | - |
| passed | **true** | - |

### Ghi chép rework:
- Kết quả đối soát với `gold_train_labels` đạt **$F1 = 0.8286 \ge 0.80$** và **$mean\_iou = 1.0000 \ge 0.70$**, hệ thống xác nhận **ĐẠT** (`passed: true`) ngay lần chạy đầu tiên.
- Không cần rework lớn về nhãn. Các trường hợp False Positive (FP) và False Negative (FN) xuất hiện chủ yếu ở các xe rất nhỏ ở hậu cảnh xa hoặc xe bị che khuất nặng trong các ảnh có mật độ phương tiện dày đặc (như `drive_026` có 36 xe, `drive_002` có 28 xe).

---

## 5. Kết quả YOLO

- **Runtime, GPU/CPU, phiên bản Ultralytics và Torch:**
  - Môi trường: Google Colab, GPU Tesla T4 (14,913 MiB, CUDA 0).
  - Python: 3.13.16.
  - Ultralytics: 8.4.208.
  - PyTorch: 2.14.1+cu130.
- **Các tham số thay đổi so với notebook nếu có và lý do:** Giữ nguyên toàn bộ cấu hình gốc của notebook (`epochs=50`, `imgsz=640`, `seed=20260909`, `batch=-1` tự động tính tối ưu batch size = 45).
- **Đường dẫn best checkpoint trong phiên chạy:** `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`

### Bảng chỉ số trên 10 ảnh eval:

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.3004 (30.04%) |
| Recall | 0.2832 (28.32%) |
| mAP50 | **0.2711 (27.11%)** |
| mAP50–95 | **0.2003 (20.03%)** |

#### Chi tiết theo từng lớp phương tiện:
- **`car`** (94 instances): Precision = 0.631, Recall = 0.766, mAP50 = 0.741, mAP50–95 = 0.527
- **`truck`** (5 instances): Precision = 0.389, Recall = 0.200, mAP50 = 0.239, mAP50–95 = 0.206
- **`bus`** (2 instances): Precision = 0.000, Recall = 0.000, mAP50 = 0.0149, mAP50–95 = 0.0048
- **`van`** (12 instances): Precision = 0.182, Recall = 0.167, mAP50 = 0.0889, mAP50–95 = 0.0635

### Dẫn link tới thư mục kết quả trong repo:
- Thư mục 10 ảnh dự đoán: [assets/eval_predictions/visuals/](assets/eval_predictions/visuals/)
- Thư mục log và biểu đồ huấn luyện: [assets/yolo26_runs/](assets/yolo26_runs/) (chứa [results.csv](assets/yolo26_runs/vehicle_annotation/results.csv), [confusion_matrix.png](assets/yolo26_runs/vehicle_annotation/confusion_matrix.png), [results.png](assets/yolo26_runs/vehicle_annotation/results.png))

### Phân tích trực quan trên ảnh cụ thể:
- **[drive_056.jpg](assets/eval_predictions/visuals/drive_056.jpg):** Mô hình phát hiện chính xác hầu hết các xe con (`car`) ở cự ly gần và trung bình với bounding box sát mép. Tuy nhiên, ở các làn đường xa, mô hình có xu hướng dự đoán nhầm xe `van` thành `car` do đặc điểm hình dạng bị mờ.
- **[drive_053.jpg](assets/eval_predictions/visuals/drive_053.jpg):** Ảnh có phương tiện xe buýt (`bus`) và xe tải, nhưng mô hình không nhận diện được xe buýt do số lượng mẫu `bus` trong tập train quá ít (class imbalance).

---

### Trả lời bốn câu hỏi cuối notebook:

1. **Lớp nào recall thấp nhất? Ảnh nào minh họa?**
   - Lớp có recall thấp nhất là **`bus`** ($Recall = 0.000$, tức 0%), tiếp đến là **`van`** ($Recall = 0.167$) và **`truck`** ($Recall = 0.200$).
   - Minh họa: Trong ảnh [drive_053.jpg](assets/eval_predictions/visuals/drive_053.jpg) và [drive_052.jpg](assets/eval_predictions/visuals/drive_052.jpg), xe buýt xuất hiện nhưng mô hình bị miss hoàn toàn (không tạo được box dự đoán đạt ngưỡng tự tin). Nguyên nhân chính là tập dữ liệu huấn luyện 50 ảnh bị mất cân bằng dữ liệu nghiêm trọng (chủ yếu là `car`, rất ít xe `bus`).

2. **Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?**
   - Cần kiểm tra độ sát và độ chuẩn xác của các cạnh bounding box (localization accuracy).
   - Chỉ số mAP50 chỉ yêu cầu mức độ trùng khớp tương đối ($IoU \ge 0.50$). Trong khi đó, mAP50–95 tính trung bình trên các ngưỡng IoU khắt khe từ $0.50$ đến $0.95$. Nếu các bounding box khi gán nhãn bị vẽ lỏng lẻo, ôm thừa nền xung quanh hoặc cắt phạm vào thân xe thì khi kiểm tra ở ngưỡng IoU cao ($\ge 0.75, 0.90$), box dự đoán sẽ không đạt chuẩn match, làm giảm mạnh chỉ số mAP50–95.

3. **Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?**
   - Lỗi nhầm lẫn giữa **`car`** và **`van`**, hoặc giữa **`van`** và **`truck` nhỏ**.
   - Dữ kiện hỗ trợ: Trên ma trận nhầm lẫn ([confusion_matrix.png](assets/yolo26_runs/vehicle_eval/confusion_matrix.png)), các xe `van` bị phân loại nhầm thành `car` hoặc bị bỏ sót vào background chiếm tỷ lệ lớn. Trong thực tế, ranh giới giữa xe minivan (dáng hộp liền thân) và các dòng SUV/crossover cỡ lớn rất dễ gây nhầm lẫn nếu guideline chưa có quy định định lượng rõ ràng, dẫn đến dữ liệu gán nhãn có độ nhiễu và mô hình khó học đặc trưng phân tách.

4. **Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?**
   - Không dùng 10 ảnh eval để train để tránh hiện tượng rò rỉ dữ liệu (data leakage) và học vẹt (overfitting). Nếu đưa eval vào train, các chỉ số đánh giá sẽ bị thổi phồng, mất tính khách quan và không đo lường được khả năng tổng quát hóa (generalization) của mô hình trên dữ liệu mới trong thực tế.
   - Vai trò của 10 ảnh eval trong notebook: Đóng vai trò là tập Validation độc lập trong quá trình huấn luyện, cho phép theo dõi loss và mAP qua từng epoch để đánh giá mức độ hội tụ, đồng thời làm căn cứ để lưu checkpoint tốt nhất (`best.pt`).

---

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Chấm nhãn với Ground Truth (CP5) | Lệnh `evaluate_annotations.py` thiếu đường dẫn sau cờ `--gold-labels` gây lỗi cú pháp | Sao chép thư mục nhãn chuẩn vào `gold_train_labels/` và truyền đúng tham số `--gold-labels gold_train_labels --output outputs/annotation_evaluation.json` | Lệnh thực thi thành công, đạt $F1 = 0.8286$ và $IoU = 1.000$, tạo file JSON chuẩn xác |
| Xử lý các xe ở hậu cảnh xa và bị che khuất | Khó phân biệt các xe kích thước nhỏ hoặc bị dải phân cách/cây cối che khuất một phần | Áp dụng triệt để quy tắc trong `GUIDELINE_MINI.md`: chỉ vẽ phần thân xe nhìn thấy, không đoán phần bị che khuất | Giữ được độ nhất quán cao trong bộ nhãn, đạt tiêu chuẩn kiểm định |
| Đánh giá mô hình trên Colab (CP6) | Mất cân bằng lớp (class imbalance) khiến model ít nhận diện được `bus`/`truck` | Giữ nguyên split chuẩn theo yêu cầu, tập trung phân tích nguyên nhân từ độ phủ dữ liệu thay vì sửa nhãn | Thu được đầy đủ các artifact và trả lời thấu đáo 4 câu hỏi phân tích |

---

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md):
- **CP1 (Schema & Guideline):** Đạt. Đã hoàn thiện định nghĩa 4 lớp và 3 ca mơ hồ cụ thể trong `GUIDELINE_MINI.md`.
- **CP2 (Gán nhãn 50 ảnh train):** Đạt. Đủ 50 file nhãn định dạng YOLO trong `dataset/labels/train/`.
- **CP3 (Self-QC):** Đạt. Checker thông qua 50/50 file, đã rà mắt kiểm tra 10 ảnh mẫu.
- **CP4 (Khóa nhãn ban đầu):** Đạt. Có commit khóa nhãn ban đầu (`3f6797b`) trên Git trước khi nhận gold.
- **CP5 (Gold QC):** Đạt. $F1 = 0.8286 \ge 0.80$, $mean\_iou = 1.0000 \ge 0.70$, có file kết quả `outputs/annotation_evaluation.json`.
- **CP6 (Train YOLO và phân tích):** Đạt. Chạy đầy đủ notebook trên Colab, giải nén đúng các folder `eval_predictions` và `yolo26_runs` vào assets, trả lời đầy đủ và sâu sắc 4 câu hỏi.
- **Đánh giá tổng thể:** Hoàn thành tốt 100% các tiêu chí yêu cầu của bài lab.

---

## 8. Công cụ AI và tài liệu tham khảo

- **Công cụ AI sử dụng:**
  - Sử dụng trợ lý AI Antigravity để hỗ trợ rà soát cấu trúc thư mục repo theo chuẩn nộp, hướng dẫn cú pháp lệnh kiểm tra annotation và tổng hợp dữ liệu từ các file log/output kết quả huấn luyện Colab.
  - Tự kiểm tra, đối soát lại toàn bộ số liệu thực tế, ảnh dự đoán và tự viết phần phân tích bản chất mô hình.
- **Tài liệu tham khảo:**
  - Hướng dẫn bài lab: `GUIDE.md`, `CHECKPOINTS.md`, `SUBMISSION.md`, `RUBRIC.md`.
  - Tài liệu chính thức của Ultralytics YOLO: https://docs.ultralytics.com/
