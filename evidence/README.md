# Bằng chứng nộp bài

Các ảnh trong thư mục này phải được đọc theo đúng thứ tự yêu cầu tại mục 6.2
của `TODO.md`. Chỉ lưu ảnh lấy từ hệ thống chạy thật; không dùng ảnh mô phỏng.

## 01 — MLflow experiments

- File: `01-mlflow-experiments.png`
- SHA-256: `c693f349236be9ce8f4972e0a7db2af7278e133fcfc02c926b38e2a36d5d6613`
- Nguồn: MLflow 2.13.0 tại `http://127.0.0.1:5000`, backend
  `sqlite:///mlflow.db`.
- Ảnh hiển thị 7 runs, các cột `accuracy`, `f1_score`, `max_depth`,
  `min_samples_split`, `n_estimators`.
- Run tốt nhất: `44fcb3f0669d4840821a063d95931a4b`, accuracy `0.746`,
  F1-score `0.7449481408322124`, huấn luyện trên 5.996 mẫu.

Có thể kiểm chứng lại bằng:

```bash
export MLFLOW_TRACKING_URI=sqlite:///mlflow.db
.venv/bin/python - <<'PY'
from mlflow.tracking import MlflowClient
client = MlflowClient()
for run in client.search_runs(["0"], order_by=["metrics.accuracy DESC"]):
    print(run.info.run_id, run.data.params, run.data.metrics)
PY

sha256sum evidence/01-mlflow-experiments.png
```

## Các ảnh phải bổ sung sau khi cấu hình cloud

2. Hai lần GitHub Actions có bốn job xanh: lần đầu và commit dữ liệu.
3. Terminal gọi thành công `/health` và `/predict` trên VM.
4. Cloud Storage Console hiển thị `dvc/` và
   `models/latest/model.pkl`.
5. Job Eval thất bại với tham số yếu (khuyến khích).

Các ảnh 2–4 chưa thể chụp khi repo chưa có DVC remote, GitHub Secrets, bucket
GCS và VM đang chạy.
