# Báo cáo Day 08 — 2A202603198-TranPhamHoangPhong

> Bản tổng hợp CP6 theo dữ liệu đã cung cấp trong phiên làm việc. Mọi tỷ lệ đều kèm tử số/mẫu số. Những số liệu chưa có file nguồn đối chiếu được ghi rõ là chưa được cung cấp, không tự suy diễn.

## 1. Model và recipe (Part 2–3)

| Lớp | Model + cỡ | Ngưỡng | Vì sao |
|---|---|---|---|
| `person`, `bicycle`, `motorbike`, `car`, `bus`, `truck` | YOLO26; kích cỡ model đã dùng trong batch cần đối chiếu `model_log.csv` | Chưa có số liệu `threshold_sweep.csv` trong dữ liệu hiện tại | Chưa đủ run_id và kết quả threshold sweep để ghi lại từng ngưỡng một cách đáng tin cậy. |
| `three_wheeler` | YOLOE-26s / prompt lớp `three_wheeler` theo recipe lab | Chưa có số liệu `threshold_sweep.csv` trong dữ liệu hiện tại | YOLOE được dùng cho lớp xe ba bánh địa phương; quyết định cuối vẫn do người duyệt. |

- Lệnh `merge_prelabels.py` đã dùng: chưa có nguyên văn lệnh trong log được cung cấp.
- **Phiên bản SAM được chọn:** SAM 2.1 Base (`sam2.1_b.pt`). Người làm bài đánh giá trực quan SAM 2.1 Base cho mask tốt hơn MobileSAM trong thử nghiệm đã thực hiện.
- Lỗi không sửa được chỉ bằng đổi ngưỡng: nhầm lớp theo ngữ nghĩa, ví dụ xe tải (`truck`) ở `img_003.jpg` bị gán nhầm thành `three_wheeler` hoặc nhầm phương tiện với `person`; cần sửa ánh xạ/nhãn bằng quy tắc lớp, không chỉ tăng/giảm confidence.
- Box điểm cao mà sai / điểm thấp mà đúng: chưa có danh sách ví dụ được trích từ `model_log.csv` hoặc `review_stats.md`.
- **Giới hạn:** ngưỡng được chọn dựa trên tập gold 4 ảnh; số đo trên gold có thể lạc quan vì cùng tập dữ liệu được dùng để chọn và đánh giá ngưỡng.

## 2. AI error profile (từ `qc/review_stats.md`)

| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---:|---:|---|
| accepted | 392 | 90.5% | 433 box máy |
| edited | 14 | 3.2% | 433 box máy |
| deleted | 22 | 5.1% | 433 box máy |
| added | 13 | 3.1% | 419 đối tượng sau duyệt |

- Lớp máy tệ nhất: chưa có bảng phân rã theo lớp trong `review_stats.md` để kết luận.
- Precision / recall của máy so với gold: chưa có số liệu đối chiếu trong log được cung cấp.
- **Điểm cần đối chiếu:** accepted + edited + deleted = 392 + 14 + 22 = 428/433 box máy; còn 5/433 box (1.2%) chưa được giải thích trong ba trạng thái trên. Cần kiểm tra định nghĩa và file `review_stats.md` trước khi chốt diễn giải.

## 3. Thiên kiến tự động hóa và ảnh bẫy

- Bắt bẫy: 11/12 = 91.7%; có 1/12 bẫy chưa bắt được. Loại bẫy lọt cụ thể chưa có trong nội dung báo cáo được cung cấp.
- Kết quả bắt bẫy vượt ngưỡng tối thiểu 80% (tương đương ít nhất 10/12), nhưng vẫn cho thấy cần duy trì quy trình kiểm tra độc lập thay vì chấp nhận box theo confidence.
- Ba hành động giảm thiên kiến được áp dụng/đề xuất kiểm chứng: (1) quét toàn ảnh trước khi nhìn box máy; (2) kiểm tra riêng bỏ sót, thừa, sai lớp và box lỏng; (3) không dùng confidence làm tiêu chí duy nhất để chấp nhận annotation.

## 4. Máy có đáng dùng không?

- Thời gian gán tay và thời gian duyệt pre-label: chưa có số liệu trong `time_log.csv` được cung cấp, nên chưa thể so sánh phút/ảnh.
- **Kết luận:** có thể dùng AI làm công cụ pre-label để giảm thao tác khởi tạo, nhưng không được tự động phát hành nhãn. Cần người duyệt vì QC phát hiện 7/89 đối tượng lỗi (7.9%) trong mẫu và 5/5 ảnh có ít nhất một lỗi (100%).
- Ba quy tắc giảm thiên kiến:
  1. Quét ảnh trước, sau đó mới bật/đối chiếu overlay box máy.
  2. Kiểm tra lần lượt vật bị bỏ sót, box thừa, sai lớp, rồi mới kiểm tra vị trí/kích thước.
  3. Với xe ba bánh, người lái và hàng hóa, đối chiếu định nghĩa lớp và quy tắc hình học; không suy luận chỉ từ confidence.

## 5. Mask SAM cho 3 vật (Part 5)

| Ảnh | SAM (phiên bản) | Số click | Mask gồm người lái / hàng hóa? | Lỗi quan sát / ghi chú | File trong `masks/` |
|---|---|---:|---|---|---|
| `img_004.jpg` | SAM 2.1 Base (`sam2.1_b.pt`) | 3 (2 positive, 1 negative) | Cần xác minh trực tiếp mask cuối; lần chạy ban đầu có prompt chưa đặt đúng mục tiêu | Kết quả log: 33,936 px (4.9% ảnh), 1 polygon. Đã phát hiện click positive/negative ban đầu đặt lệch; cần dùng bản prompt đã chỉnh để chốt chất lượng. | `tuktuk_img004.jpg` |
| `img_013.jpg` | MobileSAM (`mobile_sam.pt`) | Đã thử point prompts; số click cần lấy từ lệnh chạy cuối | Chưa có thống kê mask cuối trong dữ liệu hiện tại | Người làm bài đánh giá kết quả SAM 2.1 tốt hơn MobileSAM trong thử nghiệm so sánh. | `mobile_sam_img013.jpg` (đã nêu trong lệnh chạy) |
| `img_013.jpg` | SAM 2.1 Base (`sam2.1_b.pt`) | Đã thử point prompts; số click cần lấy từ lệnh chạy cuối | Chưa có thống kê mask cuối trong dữ liệu hiện tại | Cần xác nhận file đầu ra sau lần chạy Colab/CPU cuối cùng trước khi nộp. | `sam21b_img013.jpg` (đã nêu trong lệnh chạy) |

- Đối chiếu guideline mục 4.2: polygon bám phần biên nhìn thấy; bóng đổ không thuộc vật; hàng hóa không mặc nhiên thuộc mask xe. Cần đặt positive point bên trong đúng phương tiện và negative point bên trong người lái/vùng cần loại. Prompt đặt sai có thể làm mask bám nhầm đối tượng, dù checkpoint đúng.
- Interactor SAM trong CVAT: chưa có kết quả thử nghiệm được ghi lại.

## 6. Calibration (Part 6)

- Số quyết định: 4 (`submission/qc/calibration_decisions.csv`), gồm 2 ca `loi_may` và 2 ca `cau_hoi_mo`.
- Quyết định quan trọng: tránh gán xe tải (`truck`) thành `three_wheeler` ở `img_003.jpg` (annotation ID 18; `CAL-01`); chỉ dùng `three_wheeler` khi cấu hình xe ba bánh được xác định rõ; tách người lái và phương tiện thành hai annotation (`CAL-02`).
- Các câu hỏi mở đã chốt trong v1.1: cách xử lý hàng rời nhô khỏi xe (`CAL-03`) và cụm motorbike dày gần camera (`CAL-04`).
- Nguyên nhân gốc: có cả lỗi model phân loại và vùng diễn giải chưa đủ cụ thể trong guideline; không quy mọi lỗi cho một nguyên nhân duy nhất.

## 7. QC và quyết định (Part 6)

- Mẫu: 5/24 ảnh batch, gồm 0 ảnh `can_review` + 5 ảnh ngẫu nhiên, seed 42; 89 đối tượng. Người kiểm: học viên; hạn chế là người làm bài đồng thời là người duyệt.
- Lỗi theo **đối tượng**: 7/89 = 7.9%; theo **ảnh**: 5/5 = 100.0% (ngưỡng Release ≤ 2%).
- Top lỗi: `sai_lop` 2 trường hợp (ví dụ `img_003.jpg` (ID 18: truck bị gán `three_wheeler`), `img_035.jpg`); `sai_vi_tri_kich_thuoc` 2 trường hợp (`img_003.jpg`, `img_031.jpg`); `thua` 2 trường hợp (`img_006.jpg`, `img_014.jpg`); còn 1 trường hợp `bo_sot` (`img_003.jpg`) theo ghi chú lỗi ban đầu, cần giữ đồng bộ với CSV phiếu kiểm.
- Giới hạn mẫu nhỏ: cận trên Wilson 95% của tỷ lệ ảnh lỗi là 100%. Mẫu 5 ảnh không đủ để chứng minh tỷ lệ lỗi thật của toàn bộ batch thấp hơn 2%; kết quả mẫu chỉ là tín hiệu QC, không phải ước lượng chắc chắn cho cả 24 ảnh.
- Script kiểm được cấu trúc/taxonomy, tọa độ box và thống kê từ phiếu kiểm; script không thể tự đánh giá box có ôm sát vật thật, phân loại đúng theo ngữ cảnh, vật thể cả người lẫn máy cùng bỏ sót, hoặc việc người duyệt có thực sự làm theo checklist.

**Quyết định: REWORK**, ngày 2026-11-19. Lý do: mẫu có lỗi ở 5/5 ảnh (100.0%, mẫu số 5 ảnh), vượt ngưỡng 2%; lỗi theo đối tượng là 7/89 (7.9%). Cận trên Wilson 95% là 100%, do đó không có cơ sở phát hành ở vòng hiện tại.

Việc cần làm tiếp: sửa các annotation có lỗi đã xác minh, export thay đổi vào `qc_fixed.json`, cập nhật release, chạy lại `build_release.py`, lấy mẫu mới bằng `qc_sample.py --force`, duyệt và chạy lại `qc_report.py`. Chỉ xem xét Release khi bằng chứng QC vòng sau đáp ứng ngưỡng và các lỗi đã được xử lý.

## 8. Ghi chú cá nhân

- **Dùng AI để làm bài:** có dùng ChatGPT để giải thích quy trình, hỗ trợ phân tích log.
- Gold có bị nhiễm không: chưa có bằng chứng ghi trong log hiện tại để xác nhận độc lập rằng pre-label không được xem trước khi hoàn thành gold; học viên cần tự xác nhận theo quá trình thực tế.
- Máy / GPU / CVAT: Windows 11 với Ubuntu WSL2; laptop Intel Core i5-1155G7, RAM 16 GB, không có GPU rời; đã sử dụng Google Colab GPU T4 cho tác vụ model.
- Lỗi / bug script đã gặp: một số thao tác path khi chạy script trong WSL; cần truyền basename ảnh khi script tự thêm `data/images/`. Ngoài ra đã gặp nhầm đường dẫn khi cố thực thi trực tiếp file Markdown/ZIP thay vì dùng `cat` hoặc mở bằng File Explorer.
- Một điều làm khác lần sau: lưu lại đầy đủ run_id, threshold sweep, time log, ID lỗi QC và kết quả mask để mọi kết luận trong báo cáo truy ngược được về nguồn.
