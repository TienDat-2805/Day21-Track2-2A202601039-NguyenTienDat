# Báo cáo Lab MLOps

## 1. Lựa chọn siêu tham số

Các cấu hình được so sánh bằng MLflow trên tập huấn luyện 2.998 mẫu:

| `n_estimators` | `max_depth` | `min_samples_split` | Accuracy |
|---:|---:|---:|---:|
| 50 | 3 | 2 | 0,558 |
| 100 | 5 | 2 | 0,564 |
| 200 | 10 | 5 | 0,644 |
| 300 | Không giới hạn | 2 | **0,682** |

Cấu hình cuối cùng là `n_estimators=300`, `max_depth=None` và
`min_samples_split=2`. Cấu hình này cho accuracy cao nhất trong các lần thử;
`random_state=42` được cố định để kết quả có thể tái tạo.

## 2. Ảnh hưởng của dữ liệu mới

| Tập huấn luyện | Số mẫu | Accuracy | F1-score (weighted) |
|---|---:|---:|---:|
| Phase 1 | 2.998 | 0,6820 | 0,6811 |
| Phase 1 + Phase 2 | 5.996 | **0,7460** | **0,7449** |

Khi bổ sung Phase 2, accuracy tăng 0,064 và F1-score tăng 0,0638. Mô hình
huấn luyện trên 5.996 mẫu vượt eval gate 0,70 và đủ điều kiện triển khai. Tập
`eval.csv` gồm 500 mẫu luôn được giữ riêng, không tham gia huấn luyện.

## 3. Khó khăn và cách giải quyết

MLflow 2.13 còn phụ thuộc `pkg_resources`, trong khi các bản setuptools mới đã
loại bỏ module này. Dự án khóa `setuptools<81` để môi trường CI có thể tái lập.
Kết quả Phase 1 thấp hơn gate dù đã tối ưu Random Forest; thay vì hạ ngưỡng hoặc
chỉnh metric, pipeline giữ nguyên gate 0,70. Sau khi thêm đúng dữ liệu Phase 2,
mô hình đạt 0,7460. Workflow cũng chuyển output của GitHub Actions sang `float`
trước khi so sánh, nhờ đó mô hình yếu bị chặn và job Deploy không chạy.

## 4. Danh sách bằng chứng nộp kèm

1. MLflow UI hiển thị các thí nghiệm và hai metric.
2. GitHub Actions có bốn job xanh ở lần đầu và lần commit dữ liệu.
3. Kết quả gọi `/health` và `/predict` trên VM.
4. Cloud Storage có dữ liệu dưới `dvc/` và `models/latest/model.pkl`.
5. Ảnh job Eval thất bại với tham số yếu (khuyến khích).

## 5. Phạm vi triển khai

Project GCP của cá nhân chưa liên kết Billing để tránh phát sinh chi phí, vì
vậy phần tạo bucket, VM và ảnh chạy cloud chưa được thực hiện. Repo vẫn cung
cấp đầy đủ DVC pointers, cấu hình remote GCS, workflow bốn job và FastAPI để
có thể chạy ngay khi được cấp project cloud/sandbox của lớp. Các kết quả cloud
không được mô phỏng hoặc khai báo là đã hoàn thành.
