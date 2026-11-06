# Báo cáo cá nhân – Day 01: Lọc tin nhắn rác bằng ML, DL, Embedding và LLM

- **MSSV:** 2A202603584
- **Họ và tên:** Phạm Tuấn Cường
- **SEED:** 9887

## 1. Bằng chứng

| Tệp | Có? |
| --- | --- |
| [`day01_lab.ipynb`](day01_lab.ipynb) – notebook đã chạy, còn output | [x] |
| [`lab1_report.json`](lab1_report.json) – do ô "Báo cáo cuối" sinh ra | [x] |
| [`hand_calc.jpg`](hand_calc.jpg) – ảnh tính tay có các bước | [x] |

## 2. Kết quả chính

| Thế hệ AI / Mô hình | F1 (test) | F1 (SUB 40) | p95 ms/tin | USD / 1k tin |
| :--- | :---: | :---: | :---: | :---: |
| **1. BoW + LogReg (tự code)** | 0.886 | 0.750 | 0.118 | $0.000 |
| **2. BoW + MLP (deep learning)** | 0.921 | 0.824 | 2.490 | $0.000 |
| **3a. Encoder (MiniLM) + kNN** | 0.919 | 0.974 | 126.693 | $0.000 |
| **3b. Encoder (MiniLM) + LogReg** | 0.947 | 0.974 | 41.653 | $0.000 |
| **3c. Encoder (MiniLM) + MLP head** | 0.961 | 0.947 | 42.918 | $0.000 |
| **4a. LLM nhỏ zero-shot** | 0.923 | 0.923 | 1522.224 | $0.020 |
| **4b. LLM lớn zero-shot** | 0.000 | 0.000 | 0.000 | $0.042 |
| **4c. LLM nhỏ few-shot** | 0.895 | 0.895 | 1280.482 | $0.049 |

## 3. Dự đoán và thực tế

1. **Thí nghiệm 4.B (Few-shot prompt):**
   - *Đoán gì:* Dự đoán đưa thêm 4 ví dụ mẫu vào prompt sẽ giúp LLM định hình ranh giới tốt hơn và tăng điểm F1.
   - *Ra gì:* F1 không tăng mà giảm nhẹ từ 0.923 xuống 0.895, trong khi chi phí USD tăng gấp 2.5 lần ($0.020 lên $0.049 / 1k tin).
   - *Vì sao lệch:* Số lượng ví dụ ít không bao quát hết các dạng tin nhắn, nhưng việc "cõng" thêm token ví dụ trong mỗi request làm phình to chi phí đầu vào mà không đem lại giá trị phân loại rõ rệt.

2. **Thí nghiệm 4.A (LLM lớn vs LLM nhỏ):**
   - *Đoán gì:* Dự đoán model lớn (gemini-3.8-flash) sẽ có khả năng suy luận vượt trội và F1 cao hơn model nhỏ.
   - *Ra gì:* F1 = 0.000, độ trễ = 0.00 ms, 40/40 tin nhắn bị báo lỗi `invalid`.
   - *Vì sao lệch:* Máy chủ API từ chối request ngay lập tức (lỗi quyền truy cập hoặc phiên bản model), khối lệnh try-catch trả về rỗng. Điều này phản ánh rủi ro phụ thuộc hạ tầng mạng và tính ổn định của API bên thứ ba.

3. **Thí nghiệm 4.D / Stress test (LLM phòng thủ):**
   - *Đoán gì:* Dự đoán prompt phòng thủ sẽ giúp LLM hoàn thiện khả năng chống đỡ mà không ảnh hưởng chất lượng chung.
   - *Ra gì:* Phòng thủ giúp triệt tiêu Prompt Injection nhưng lại làm sụt giảm khả năng nhận diện ở nhóm "Né từ khoá" (độ chính xác tụt từ 100% xuống 62%).
