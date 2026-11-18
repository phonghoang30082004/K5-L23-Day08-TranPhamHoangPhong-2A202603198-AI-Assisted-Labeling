# Thông tin học viên

- Họ và tên: Tran Pham Hoang Phong
- MSHV: 2A202603198
- Lớp (L2 / L3 / khác): L2
- Tài khoản GitHub: phonghoang30082004
- Repo bài nộp (URL): https://github.com/phonghoang30082004/K5-L23-Day08-TranPhamHoangPhong-2A202603198-AI-Assisted-Labeling
- Commit chốt bài (hash): Chưa chốt; cập nhật sau commit cuối cùng.
- Máy: Windows 11 + Ubuntu WSL2; Intel Core i5-1155G7, RAM 16 GB, không có GPU rời; dùng Google Colab T4 cho tác vụ model.
- CVAT (Docker): Đã sử dụng CVAT local.

## Kết quả tóm tắt

- Máy: accepted 392/433 = 90.5%; edited 14/433 = 3.2%; deleted 22/433 = 5.1% (mẫu số: 433 box máy); cộng ba trạng thái được 428/433, còn 5 box cần đối chiếu trạng thái. Added: 13/419 = 3.1% (mẫu số: 419 đối tượng sau duyệt).
- So với gold: precision / recall của máy chưa có số liệu trong bản tổng hợp hiện tại; bổ sung từ `review_stats.md` nếu có.
- Bắt bẫy: 11/12 = 91.7%.
- Thời gian: gán tay và duyệt có pre-label chưa có số liệu đối chiếu từ `time_log.csv`.
- Lỗi QC: 100.0% theo ảnh (5/5 ảnh), 7.9% theo đối tượng (7/89 đối tượng).
- Ca sai lớp cần lưu ý: `img_003.jpg`, annotation ID 18 — xe tải thực tế (`truck`) bị gán nhãn `three_wheeler`; ghi nhận trong `CAL-01`.
- **Quyết định:** REWORK — mẫu QC có lỗi ở 5/5 ảnh, vượt ngưỡng ≤ 2%; cần sửa nhãn, cập nhật guideline và chạy vòng QC mới trước khi phát hành.
