# 🛒 Walmart Weekly Sales Forecasting
### Dự báo doanh thu tuần Walmart để tối ưu kế hoạch tồn kho

---

## 📌 Giới thiệu

Dự án xây dựng hệ thống dự báo doanh thu tuần cho **Top 3 Department có doanh thu cao nhất** (Dept 92, 95, 38) tại hệ thống siêu thị Walmart, dựa trên dữ liệu lịch sử từ 2010 đến 2012. Kết quả dự báo được ứng dụng trực tiếp vào việc **phân loại mức nhu cầu** và **lập kế hoạch tồn kho** cho 39 tuần tiếp theo.

> 📚 Đồ án môn học: Phân tích Dữ liệu Nâng cao

---

## 🎯 Mục tiêu

- Phân tích chuỗi thời gian doanh thu bán lẻ theo tuần
- Xây dựng và so sánh 3 mô hình dự báo: **Holt-Winters**, **ARIMA**, **XGBoost**
- Lựa chọn mô hình tốt nhất dựa trên MAE, RMSE, MAPE
- Phân loại mức nhu cầu (Low / Normal / High) để hỗ trợ quyết định tồn kho

---

## 📁 Cấu trúc dự án
```
walmart-sales-forecasting/
│
├── data/                        # Dữ liệu (tải từ Kaggle)
│   ├── train.csv
│   ├── test.csv
│   ├── features.csv
│   └── stores.csv
│
├── notebooks/
│   └── walmart_forecasting.ipynb   # Notebook chính (đầy đủ pipeline)
│
├── reports/
│   └── walmart_forecasting_report.pdf   # Báo cáo đồ án
│
└── README.md
```

---

## 🔄 Pipeline
```
Thu thập dữ liệu (Kaggle)
        ↓
Khám phá dữ liệu (EDA)
        ↓
Tiền xử lý & Aggregate theo tuần
        ↓
Phân tích Time Series (STL, ACF/PACF, ADF/KPSS)
        ↓
Feature Engineering (25 đặc trưng)
        ↓
Huấn luyện & So sánh mô hình
  ├── Holt-Winters
  ├── ARIMA (Grid Search AIC)
  └── XGBoost
        ↓
Tinh chỉnh Hyperparameter
        ↓
Retrain + Recursive Forecast (39 tuần)
        ↓
Phân loại nhu cầu & Kế hoạch tồn kho
```

---

## 📊 Dữ liệu

**Nguồn:** [Walmart Sales Forecast — Kaggle](https://www.kaggle.com/datasets/aslanahmedov/walmart-sales-forecast)

| File | Mô tả | Kích thước |
|---|---|---|
| `train.csv` | Doanh thu lịch sử (2/2010 – 10/2012) | 421,570 dòng |
| `test.csv` | 39 tuần cần dự báo | 115,064 dòng |
| `features.csv` | Biến ngoại sinh (nhiệt độ, CPI, MarkDown...) | 8,190 dòng |
| `stores.csv` | Thông tin 45 cửa hàng | 45 dòng |

---

## 🧪 Kết quả mô hình

### So sánh sau tinh chỉnh Hyperparameter

| Department | Holt-Winters MAPE | ARIMA MAPE | XGBoost MAPE |
|---|---|---|---|
| Dept 92 | 2.71% | 4.85% | **2.89%** |
| Dept 95 | 2.71% | 4.06% | **1.71%** |
| Dept 38 | 5.63% | 6.50% | **3.61%** |

> ✅ **XGBoost được chọn** làm mô hình triển khai cho cả 3 Department dựa trên chỉ số RMSE.

### Tham số XGBoost tốt nhất

| Department | n_estimators | learning_rate | max_depth | RMSE |
|---|---|---|---|---|
| Dept 92 | 500 | 0.05 | 5 | 122,190 |
| Dept 95 | 500 | 0.05 | 5 | 90,132 |
| Dept 38 | 700 | 0.02 | 6 | 120,531 |

---

## 💡 Kết quả chính

- **Dept 92:** Nhu cầu cao tập trung tháng 11–2, đỉnh tuần 21/12 (+30% vs baseline)
- **Dept 95:** Nhu cầu cao vào mùa hè (tháng 6–7), thấp vào cuối năm
- **Dept 38:** Nhu cầu cao tháng 1–4, chỉ 2 tuần Low Demand (23/11 và 28/12)
- Phát hiện cơ hội **tái phân bổ vốn tồn kho nội bộ** từ Dept 92 sang Dept 95 từ tháng 3–5
