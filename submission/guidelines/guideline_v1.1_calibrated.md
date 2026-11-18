# Label contract — Phố châu Á — BỔ SUNG v1.1 — Tran Pham Hoang Phong

> Chỉ ghi các quyết định bổ sung/làm rõ so với guideline v1.0. Bản gốc v1.0 được giữ nguyên. Các ví dụ dưới đây lấy từ các trường hợp đã ghi nhận trong QC và cần được duy trì đồng bộ với `submission/qc/calibration_decisions.csv`.

## 1. Câu hỏi mở đã chốt (từ mục 6 của v1.0)

| # | Câu hỏi | Quyết định | Ảnh ví dụ | `case_id` |
|---|---|---|---|---|
| 2 | Khối motorbike đỗ dày gần camera: từng xe hay `ignore_region`? | Gán từng xe nếu nhận diện được từng cá thể. Mật độ cao hoặc box chồng lấn không phải lý do tự động gộp. Chỉ dùng `ignore_region` khi không thể phân tách từng cá thể theo quy tắc cụm dày của v1.0. | `img_006.jpg` | `CAL-04` |
| 3 | Hàng hóa nhô ra khỏi xe đạp/ba gác: box có gồm hàng không? | Bộ phận gắn cố định (khung, baga, giỏ) thuộc phương tiện. Hàng hóa rời không thuộc box xe nếu nhô ra khỏi xe hơn khoảng 20% chiều dài xe. | `img_031.jpg` | `CAL-03` |

## 2. Ca khó mới (từ lỗi của máy / lỗi QC)

| Ảnh | Tình huống | Máy làm gì (model nào) | Quyết định của bạn | Quy tắc mới (vào mục nào của v1.0) |
|---|---|---|---|---|
| `img_003.jpg` | Xe tải (annotation ID 18 trong bản nhãn được đối chiếu) bị nhầm với `three_wheeler` | Nhãn được gán là `three_wheeler` trong khi người duyệt xác định phương tiện thực tế là `truck` | Giữ nhãn `truck` cho xe tải; chỉ gán `three_wheeler` khi cấu hình xe ba bánh được xác định rõ. Không suy luận `three_wheeler` chỉ từ thùng/chở hàng; nếu hình ảnh không đủ bằng chứng thì đánh dấu `can_review`. | Làm rõ mục 2 và 4.3: phân biệt theo cấu hình tổng thể của phương tiện, đối chiếu bánh xe và thân xe; không dựa riêng vào hình dáng thùng hàng. |
| `img_035.jpg` | Xe ba bánh và người lái nằm gần nhau, dễ lẫn nhãn | Theo ghi chú QC ban đầu, model có trường hợp gán nhóm `person` cho xe ba bánh | Tách phương tiện và con người thành hai annotation: xe `three_wheeler`; người nhìn thấy đủ điều kiện là `person`. Không dùng box người thay cho box phương tiện. | Làm rõ mục 2 và 4.1.4: người lái là đối tượng riêng, phương tiện là đối tượng riêng. |

## 3. Lịch sử thay đổi

| Phiên bản | Thay đổi | Lý do (ca nào, `case_id`) |
|---|---|---|
| v1.0 | Bản giảng viên | — |
| v1.1 | Làm rõ tránh nhầm xe tải (`truck`) thành `three_wheeler`; tách box người lái và phương tiện; chốt cách xử lý hàng rời nhô khỏi xe; làm rõ điều kiện dùng `ignore_region` cho cụm motorbike gần camera. | QC các ảnh `img_003`, `img_006`, `img_031`, `img_035`; `CAL-01` đến `CAL-04`. |

### Ghi chú áp dụng

- Các quy tắc v1.1 chỉ làm rõ cách áp dụng guideline; chúng không thay thế việc nhìn ảnh và xác minh từng annotation.
- Với đối tượng không đếm được số bánh hoặc không thể xác định chắc lớp, dùng `can_review` thay vì đoán lớp.
- Khi quyết định làm thay đổi annotation, sửa nhãn nguồn, export phần đã sửa vào `qc_fixed.json`, sau đó build release và QC lại theo quy trình lab.
