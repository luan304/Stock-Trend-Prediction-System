# Dự báo Xu hướng Giá Cổ phiếu (VNM, FPT, VIC) - Machine Learning Pipeline

Dự án này tập trung vào việc xây dựng một hệ thống học máy hoàn chỉnh để dự báo xu hướng giá cổ phiếu tại thị trường Việt Nam. Thay vì chỉ sử dụng các mô hình cơ bản, hệ thống đã được tối ưu hóa thông qua Feature Engineering chuyên sâu và kỹ thuật xử lý dữ liệu chuỗi thời gian khắt khe.

## Quy trình thực hiện (Pipeline)

### Bước 1: Thu thập dữ liệu (Data Ingestion)
Sử dụng thư viện `vnstock` để lấy dữ liệu OHLCV từ năm 2020 đến nay. Để tăng cường độ chính xác, mô hình còn tích hợp thêm các biến số vĩ mô như chỉ số VNINDEX và tỷ giá USD/VND nhằm phản ánh bức tranh toàn cảnh của thị trường.

### Bước 2: Tiền xử lý (Preprocessing)
* **Ghép nối (Merge):** Hợp nhất các nguồn dữ liệu thô theo trục thời gian (Date).
* **Xử lý khuyết thiếu:** Sử dụng kỹ thuật `ffill` (điền tiến) và `bfill` (điền lùi) để đảm bảo tính liên tục của chuỗi dữ liệu mà không làm mất thông tin quan trọng.
* **Chuẩn hóa:** Áp dụng `MinMaxScaler`. **Lưu ý quan trọng:** Quá trình `fit` chỉ thực hiện trên tập Train để ngăn chặn triệt để hiện tượng rò rỉ dữ liệu (Data Leakage).

### Bước 3: Kỹ thuật đặc trưng (Feature Engineering)
Đây là phần cốt lõi nâng cao hiệu suất của mô hình với 3 nhóm đặc trưng:
* **Chỉ báo kỹ thuật:** SMA, EMA, RSI, MACD và Bollinger Bands (đo lường độ biến động).
* **Đặc trưng động lực:** Tỷ suất sinh lời (Percentage Return) và Biến động khối lượng giao dịch.
* **Đặc trưng tâm lý:** Phân tích xu hướng theo Ngày trong tuần (Day of Week) và khoảng cách giá so với các đường trung bình động.

### Bước 4: Huấn luyện & Tối ưu (Model Training)
Hệ thống tiến hành so sánh và đánh giá 3 thuật toán phổ biến: **Logistic Regression, Random Forest, và XGBoost**.
* Sử dụng `GridSearchCV` để dò tìm bộ tham số tối ưu.
* Áp dụng `TimeSeriesSplit` (5-fold) để đảm bảo việc kiểm định chéo tuân thủ nghiêm ngặt tính chất thời gian (không dùng dữ liệu tương lai để dạy quá khứ).

### Bước 5: Đánh giá (Evaluation)
Mô hình tập trung tối ưu điểm **F1-Macro** nhằm đảm bảo khả năng dự báo tốt ở cả hai chiều Tăng và Giảm (tránh sai lệch do mất cân bằng nhãn). Các báo cáo xuất ra bao gồm:
* **Confusion Matrix:** Ma trận nhầm lẫn để kiểm tra tỷ lệ đoán đúng/sai.
* **Learning Curve:** Đánh giá hiện tượng Overfitting/Underfitting.
* **Biểu đồ tín hiệu:** Trực quan hóa các điểm AI dự báo trực tiếp trên đồ thị giá thực tế.

### Bước 6: Dự báo thực tế (Inference)
Xây dựng pipeline tự động hóa: Nạp dữ liệu mới nhất -> Xử lý đặc trưng -> Đưa vào mô hình "nhà vô địch" -> Xuất khuyến nghị **MUA/BÁN** cho ngày giao dịch tiếp theo.

## Kết quả đạt được
Mô hình cho thấy khả năng thích nghi tốt với thị trường thực tế với điểm F1-Score ổn định trong khoảng **55% - 60%** trên dữ liệu kiểm thử năm 2025. Trong lĩnh vực tài chính, đây là ngưỡng hiệu suất có giá trị ứng dụng cao và vượt trội hơn so với các phương pháp dự báo ngẫu nhiên.

## Hướng dẫn chạy dự án
1. **Cài đặt thư viện:**
   ```bash
   pip install pandas numpy scikit-learn xgboost matplotlib seaborn vnstock yfinance
2. **Thực thi:** Chạy lần lượt các file Notebook trong thư mục `notebooks/` theo thứ tự từ 01 đến 06.

3. **Kiểm tra kết quả:** Các biểu đồ phân tích và file mô hình sẽ được tự động lưu vào thư mục `outputs/` và `models/`.