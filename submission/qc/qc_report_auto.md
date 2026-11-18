# Báo cáo QC tự động (nháp)

> **Gợi ý** do script tính; bạn xác nhận. Script chỉ đếm theo phiếu kiểm bằng mắt người.

## Mẫu
- 5 ảnh (0 ca khó + 5 ngẫu nhiên), 89 đối tượng.
- taxonomy v1.0

## Số đo
| Chỉ số | Giá trị | Mẫu số |
|---|---|---|
| Lỗi **theo đối tượng** | 7/89 = 7.9% | 89 đối tượng trong mẫu |
| **Ảnh có lỗi** (dùng cho ngưỡng) | 5/5 = 100.0% | 5 ảnh trong mẫu |
| Máy: accepted / edited / deleted | 392/433 = 90.5% / 14/433 = 3.2% / 22/433 = 5.1% | 433 box máy |
| Máy: added | 13/419 = 3.1% | 419 đối tượng sau duyệt |
| Bắt lỗi bẫy | 11/12 = 91.7% | 12 bẫy |

## Top 3 loại lỗi
| Loại lỗi | Số lỗi | Ảnh ví dụ |
|---|---|---|
| sai_lop | 2 | img_003.jpg, img_035.jpg |
| sai_vi_tri_kich_thuoc | 2 | img_003.jpg, img_031.jpg |
| thua | 2 | img_006.jpg, img_014.jpg |

## Giới hạn thống kê
- 5 ảnh lỗi trong 5 ảnh mẫu: cận trên 95% (Wilson) của tỉ lệ ảnh lỗi thật là **100%**. Ngưỡng 2% không thể được **chứng minh** bằng mẫu nhỏ như vậy: hãy nói rõ giới hạn này trong báo cáo.

## Gợi ý quyết định
**REWORK**

- ảnh có lỗi 100.0% > 2%

Cây quyết định: Hold (guideline chưa rõ / dữ liệu hỏng) -> Rework (> ngưỡng, lỗi lặp, bắt bẫy / nhất quán thấp) -> Release.
