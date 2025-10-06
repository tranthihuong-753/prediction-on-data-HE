#Phát hiện gian lận thẻ tín dụng bảo mật dựa trên XGBoost và đặc trưng đồ thị trong môi trường mã hóa FHE – Mô phỏng bằng Concrete ML

#0. Chuẩn bị
# 🧩 Giới thiệu

Trong bài thực hành này, chúng tôi mô phỏng **pipeline inference bảo mật (privacy-preserving inference pipeline)**  
cho bài toán **Credit Card Fraud Detection** bằng cách kết hợp **XGBoost** và **Graph Features**,  
triển khai trong môi trường **Fully Homomorphic Encryption (FHE)** thông qua thư viện **Concrete ML** của *Zama*.

---

## Lưu ý
- Dùng với concrete-ml ver <1.5
- Dùng trong môi trường Linux

## 🎯 Mục tiêu

- Hiểu quy trình huấn luyện mô hình trong dạng **plaintext**, sau đó **biên dịch sang FHE-compatible model**.  
- Mô phỏng 3 vai trò chính trong giao thức 3PervPPML (Lưu ý chỉ là mô phỏng cách hoạt động)
  - 🧠 **Analyst** – huấn luyện mô hình, sinh bộ khóa FHE.  
  - 🧾 **User** – xây dựng đồ thị giao dịch và mã hóa dữ liệu.  
  - ☁️ **CSP (Cloud Service Provider)** – thực thi **encrypted inference**.  

---

## 💾 Dữ liệu

Nguồn dữ liệu: **Credit Card Fraud Detection Dataset** từ Kaggle  
🔗 [https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud)

**Mô tả:**
- 284,807 bản ghi giao dịch (`transactions`)  
- 30 thuộc tính (`features`):  
  - 28 đặc trưng ẩn danh (`V1`–`V28`) sinh bằng PCA  
  - `Amount` – số tiền giao dịch  
  - `Time` – thời điểm tương đối  
  - `Class` – nhãn (0 = hợp lệ, 1 = gian lận)  

---

## ⚙️ Quy trình mô phỏng pipeline

Toàn bộ quy trình được chạy **trên cùng một máy (Colab)**,  
trong đó các vai trò Analyst – User – CSP được mô phỏng bằng các bước tuần tự.

### Setup
#### 🧭 1. Analyst – Huấn luyện & chuẩn bị mô hình
- Đọc dữ liệu, chia `train/test`.  
Nguồn dữ liệu: Credit Card Fraud Detection Dataset từ Kaggle
🔗 https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud
- Huấn luyện mô hình **XGBoostClassifier** trên plaintext.  
- Biên dịch mô hình sang **FHE-compatible model** bằng **Concrete ML**.  
- Sinh khóa và lưu key_eval

#### 👤 2. User – Xử lý & mã hóa dữ liệu
- Load Client
- Load dữ liệu
- Mã hóa đặc trưng bằng `public key (pk)`.
- Lưu lại để chuyển cho CPS

#### ☁️ 3. CSP – Thực thi inference mã hóa
- Load Server từ Analyst
- Load ciphertext từ User.  
- Load evaluation keys
- Thực thi `model.server_run()` trên ciphertext (Concrete ML runtime).  

#### 🔐 4. Analyst – Giải mã & đánh giá
- Load client
- Load result từ CPS
- Giải mã kết quả bằng `secret key (sk)`.  
- Chuyển kết quả decryption sang label

---

## 🧠 Ý nghĩa

Thực hành này giúp:
- Làm quen với **Concrete ML API**: compile, encrypt, decrypt, inference.  
- Nhận thức rõ **trade-off giữa Privacy và Performance**.  
- Tạo nền tảng cho hướng mở rộng: **FHE cho GNN hoặc Federated Learning**.

---
