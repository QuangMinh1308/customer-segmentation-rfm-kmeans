# 🛍️ Ứng dụng K-Means và RFM: Chiến Lược Phân Khúc Khách Hàng Hiệu Quả

*Applying K-Means and RFM for Effective Customer Segmentation Strategy*

[![Python](https://img.shields.io/badge/Python-3.x-blue?style=for-the-badge)](https://www.python.org/)
[![scikit-learn](https://img.shields.io/badge/Model-K--Means-orange?style=for-the-badge)](https://scikit-learn.org/)
[![RFM](https://img.shields.io/badge/Model-RFM-green?style=for-the-badge)]()
[![Jupyter](https://img.shields.io/badge/Made%20with-Jupyter-F37626?style=for-the-badge&logo=jupyter)](https://jupyter.org/)

---

## 📌 Tổng quan

*(Overview)*

Dự án phân khúc khách hàng dựa trên hành vi mua sắm bằng cách kết hợp **mô hình RFM (Recency – Frequency – Monetary)** với thuật toán phân cụm **K-Means**.

Dự án xây dựng một quy trình đầy đủ: từ làm sạch dữ liệu giao dịch thô, tính toán các chỉ số RFM, xử lý ngoại lệ, chuẩn hóa dữ liệu, xác định số cụm tối ưu, phân cụm khách hàng, cho đến đề xuất chiến lược tiếp thị cụ thể cho từng nhóm khách hàng.

---

## 🎯 Mục tiêu

*(Objectives)*

- Tính toán và chuẩn hóa các chỉ số RFM từ dữ liệu giao dịch thực tế.
- Áp dụng thuật toán K-Means để phân chia khách hàng thành các cụm hành vi tương đồng.
- Sử dụng các chỉ số **Elbow, Silhouette Score, Calinski-Harabasz Index, Davies-Bouldin Index** để xác định số cụm tối ưu và đánh giá chất lượng phân cụm.
- Phát hiện và phân tích riêng nhóm khách hàng ngoại lai (outliers) có giá trị đặc biệt.
- Đề xuất chiến lược tiếp thị/chăm sóc phù hợp cho từng phân khúc khách hàng.

---

## 📂 Dữ liệu

*(Dataset)*

- Nguồn: **[UCI Machine Learning Repository](https://archive.ics.uci.edu/) — Online Retail Dataset**
- Giao dịch của một doanh nghiệp bán lẻ tại Anh, từ **12/2010 đến 12/2011**
- **541,909 dòng** giao dịch, **8 cột**: `InvoiceNo`, `StockCode`, `Description`, `Quantity`, `InvoiceDate`, `UnitPrice`, `CustomerID`, `Country`

| File | Mô tả |
|---|---|
| `Online Retail.xlsx` | Dữ liệu giao dịch gốc |
| `Online Retail Cleaned Data.csv` | Dữ liệu sau khi làm sạch |

---

## 🧹 Quy trình xử lý dữ liệu

*(Data Pipeline)*

```
Thu thập dữ liệu → Tiền xử lý & làm sạch → Tính chỉ số RFM →
Phát hiện & xử lý outliers → Chuẩn hóa (Z-score) →
Chọn số cụm tối ưu → Phân cụm K-Means → Đánh giá & gán nhãn chiến lược
```

### 1. Làm sạch dữ liệu (`cleandata.ipynb`)

- Loại bỏ hóa đơn hủy (mã bắt đầu bằng `C`) và hóa đơn không hợp lệ → còn **22,061 hóa đơn hợp lệ**
- Loại bỏ mã sản phẩm không phản ánh hành vi tiêu dùng (`POST`, `DOT`, `BANK CHARGES`,...) → còn **4,029 mã sản phẩm**
- Loại bỏ dòng thiếu `CustomerID`, `Quantity ≤ 0`, `UnitPrice ≤ 0`
- Kết quả: **396,240 dòng dữ liệu / 4,334 khách hàng** (tỷ lệ giữ lại 73.14%)

### 2. Tính toán chỉ số RFM (`RFM_Analysis.ipynb`)

| Chỉ số | Ý nghĩa | Cách tính |
|---|---|---|
| **Recency (R)** | Số ngày kể từ lần mua gần nhất | Ngày phân tích − ngày mua cuối |
| **Frequency (F)** | Số lần mua hàng | Số hóa đơn duy nhất |
| **Monetary (M)** | Tổng giá trị chi tiêu | `Σ (Quantity × UnitPrice)` |

### 3. Xử lý ngoại lệ (Outliers)

- Áp dụng phương pháp **IQR (Interquartile Range)** trên `Monetary` và `Frequency`
- Phát hiện **425 khách hàng** ngoại lệ theo Monetary và **278 khách hàng** ngoại lệ theo Frequency
- Sau xử lý: còn lại **3,863 khách hàng hợp lệ** (~89.1%) cho tập phân cụm chính, phần ngoại lệ được phân tích và gán nhãn riêng

### 4. Chuẩn hóa dữ liệu

- Chuẩn hóa Z-score (`StandardScaler`) cho ba biến RFM, đưa dữ liệu về trung bình ≈ 0, độ lệch chuẩn ≈ 1

---

## 🧠 Mô hình hóa (`k_means_TN_3.ipynb`)

### Xác định số cụm tối ưu

Kết hợp 4 phương pháp đánh giá để chọn K:

| Phương pháp | Kết quả tối ưu |
|---|---|
| Elbow Method | K = 3–4 |
| Silhouette Score | K = 3 (~0.46) |
| Calinski-Harabasz Index | K = 3 |
| Davies-Bouldin Index | K = 3 |

➡️ **Số cụm được chọn: K = 3**, với **Silhouette Score trung bình đạt ~0.45**

### Trực quan hóa

- Scatter plot 3D và **t-SNE** (3 & 4 cụm) để quan sát cấu trúc phân cụm
- Biểu đồ **Violin** so sánh phân phối Recency / Frequency / Monetary giữa các cụm
- Biểu đồ kết hợp (bar + line) thể hiện quy mô và đặc trưng trung bình từng nhóm

---

## 🏷️ Kết quả phân khúc khách hàng

### Nhóm khách hàng chính

| Cụm | Nhãn | Đặc điểm | Số lượng | Chiến lược đề xuất |
|---|---|---|---|---|
| 0 | **NURTURE (NUR)** | Monetary & Frequency thấp, Recency gần | 2,047 | Ưu đãi khuyến khích mua lại, remarketing qua email |
| 1 | **RE-ENGAGE (RE)** | Recency rất cao (lâu không quay lại) | 980 | Chiến dịch kích hoạt lại, ưu đãi rủi ro thấp |
| 2 | **REWARD (REW)** | Frequency & Monetary cao, Recency thấp | 836 | Chương trình khách hàng thân thiết, tri ân |

### Nhóm khách hàng ngoại lai (Outliers)

| Nhãn | Đặc điểm | Số lượng | Chiến lược đề xuất |
|---|---|---|---|
| **PAMPER (PAM)** | VIP — Monetary vượt trội | 232 | Chăm sóc cá nhân hóa, hotline riêng |
| **DELIGHT (DEL)** | Trung thành, chi tiêu thấp hơn | 193 | Nâng cao trải nghiệm, ưu đãi mềm |
| **UPSELL (UPS)** | Khách mới, Recency rất thấp | 46 | Cross-sell / upsell sản phẩm liên quan |

---

## 🛠️ Công nghệ sử dụng

- **Ngôn ngữ:** Python
- **Thư viện chính:** `pandas`, `numpy`, `scikit-learn` (K-Means, StandardScaler, Silhouette Score, Calinski-Harabasz, Davies-Bouldin), `matplotlib`, `seaborn`, `t-SNE`
- **Môi trường:** Jupyter Notebook

---

## 📁 Cấu trúc repository

```
DACS/
├── Online Retail.xlsx                 # Dữ liệu giao dịch gốc
├── Online Retail Cleaned Data.csv     # Dữ liệu sau làm sạch
├── cleandata.ipynb                    # Notebook làm sạch & tiền xử lý dữ liệu
├── RFM_Analysis.ipynb                 # Notebook tính toán chỉ số RFM
├── k_means_TN_3.ipynb                 # Notebook phân cụm K-Means & đánh giá
├── Thử nghiệm/                        # Các thử nghiệm bổ sung
└── README.md
```

---

## 📊 Đánh giá & Hạn chế

**Đạt được:**
- Silhouette Score trung bình ~0.45 cho thấy cấu trúc phân cụm tương đối rõ ràng
- Quy trình đầy đủ, có thể áp dụng trực tiếp cho các bài toán ecommerce/retail thực tế

**Hạn chế:**
- Dữ liệu chỉ trong 1 năm, chưa phản ánh yếu tố mùa vụ
- Chưa tích hợp dữ liệu nhân khẩu học hoặc tâm lý khách hàng
- Chưa so sánh với các thuật toán phân cụm khác (DBSCAN, Hierarchical Clustering)

**Hướng phát triển:**
- Áp dụng RFM động theo thời gian
- Kết hợp thêm DBSCAN / Hierarchical Clustering để đối chiếu kết quả
- Tích hợp dự đoán Customer Lifetime Value (CLV/LTV)

---

