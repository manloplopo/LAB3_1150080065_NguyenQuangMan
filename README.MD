# LAB 3: MÃ HÓA KHÓA CÔNG KHAI (PUBLIC KEY CRYPTOGRAPHY)

- **Sinh viên thực hiện**: Nguyễn Quang Mẫn
- **MSSV**: 1150080065
- **Học phần**: An toàn và Bảo mật Thông tin


---

## 📌 Trạng thái thực hiện

Dựa trên quá trình triển khai và kiểm thử thực tế trên project `LAB3_MaHoaKhoaCongKhai` (tại `D:\BaoMat\LAB3_MaHoaKhoaCongKhai`)[cite: 56]:

- [x] **Bài 3.1**: Mật mã RSA – Chương trình Console (`RSADemo.java`, `RSACipher.java`)[cite: 56]
- [x] **Bài 3.2**: Mật mã RSA với giao diện Swing (`frm_RSA.java`)[cite: 56]
- [x] **Bài 3.3**: Mật mã đường cong Elliptic – ECC (`frm_ECC.java`, `ECCCipher.java`)[cite: 30, 56]
- [x] **Bài 3.4**: Hệ mã lai kết hợp RSA + AES-GCM (`frm_RSA_AES.java`, `RSA_AES_Cipher.java`)[cite: 38, 56]
- [x] **Bài 3.5**: Chữ ký số ECDSA (`frm_ECC_Signature.java`, `ECCSignature.java`)[cite: 45, 56]
- [x] **Lớp dùng chung & DAO**: Đã tạo `CryptoSupport.java`, `FileTextIO.java`, `DbConfig.java`, `HistoryDao.java`, `frm_KetNoiSQL.java`, `frm_LichSu.java`[cite: 56]
- [ ] **Kết nối SQL Server & Menu chính**: Trong các ảnh kiểm thử hiện tại đang chạy độc lập từng form (`Shift + F6`) ở trạng thái chưa kết nối SQL Server; chưa có minh chứng chạy `frm_Menu` và bảng `dbo.History` trên SSMS[cite: 56].

---

## 📊 Kết quả thực nghiệm & Kiểm thử

### 1. Bài 3.1 – Đo hiệu năng & Giới hạn RSA (Console)
Kết quả chạy thực tế trên máy thông qua `RSADemo.java` với bản rõ `HELLO RSA`[cite: 56]:

| Kích thước khóa | Thời gian tạo khóa (ms) | Độ dài khóa công khai (Base64) | Độ dài bản mã (Base64) | Giới hạn bản rõ | Kết quả khôi phục |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **2048 bit** | `209 ms`[cite: 56] | `392` ký tự[cite: 56] | `344` ký tự[cite: 56] | `190` byte[cite: 56] | `true`[cite: 56] |
| **3072 bit** | `348 ms`[cite: 56] | `564` ký tự[cite: 56] | `512` ký tự[cite: 56] | `318` byte[cite: 56] | `true`[cite: 56] |

### 2. Bài 3.2 – Mật mã RSA Swing (`frm_RSA`)
- **Kiểm tra ràng buộc**: Bấm `Encrypt` khi chưa nhập dữ liệu hiển thị cảnh báo *"Hãy nhập Plaintext trước khi mã hóa."*[cite: 56]
- **Mã hóa & Giải mã**: Tạo khóa `2048` bit, mã hóa `HELLO RSA` ra chuỗi Base64 dài `344` ký tự[cite: 56]. Bấm `Encrypt` nhiều lần cho ra các bản mã khác nhau (do đệm ngẫu nhiên OAEP-SHA256) nhưng đều giải mã về đúng `HELLO RSA`[cite: 22, 28, 56].

### 3. Bài 3.3 – Mật mã ECC (`frm_ECC`)
- Sử dụng đường cong `secp256r1`[cite: 56].
- **Mã hóa & Giải mã**: Mã hóa `HELLO ECC` ra chuỗi Base64 dài `192` ký tự và giải mã thành công về bản rõ ban đầu[cite: 56].
- **Kiểm thử lỗi**: Khi nạp dữ liệu sai định dạng/độ dài vào `Ciphertext` (dán nhầm chuỗi Private key), hệ thống chặn giải mã và báo lỗi `IllegalArgumentException: Bản mã quá ngắn hoặc bị hỏng.`[cite: 56] *(Lưu ý: Để đúng kịch bản kiểm thử khóa sai của Lab 3, cần bấm `Generate Keys` để tạo cặp khóa mới rồi giải mã lại bản mã cũ để nhận lỗi `AEADBadTagException: Tag mismatch`)*[cite: 35].

### 4. Bài 3.4 – Hệ mã lai RSA + AES-GCM (`frm_RSA_AES`)
- **Văn bản dài**: Mã hóa thành công đoạn văn bản dài hơn 1.000 ký tự sang chuỗi Base64 dài `2544` ký tự và giải mã khôi phục nguyên vẹn toàn văn[cite: 43, 56].
- **Kiểm tra tính toàn vẹn (Integrity)**: Nhấn nút **Sửa 1 ký tự bản mã** (hệ thống tự động thay đổi ký tự tại vị trí `1272`), sau đó bấm `Decrypt` $\rightarrow$ hệ thống phát hiện dữ liệu bị can thiệp và báo lỗi `AEADBadTagException: Tag mismatch`[cite: 56].

### 5. Bài 3.5 – Chữ ký số ECDSA (`frm_ECC_Signature`)
- **Trường hợp hợp lệ**: Ký thông điệp `THONG DIEP A` bằng Private Key $\rightarrow$ bấm `Verify` hiển thị **`VALID`** (màu xanh lá)[cite: 56].
- **Trường hợp giả mạo nội dung**: Sửa thông điệp thành `THONG DIEP B` và giữ nguyên chữ ký cũ $\rightarrow$ bấm `Verify` hiển thị **`INVALID`** (màu đỏ)[cite: 56].
- **Trường hợp sai người ký**: Khôi phục thông điệp về `THONG DIEP A` nhưng bấm `Generate Keys` để đổi cặp khóa mới $\rightarrow$ bấm `Verify` hiển thị **`INVALID`** (màu đỏ)[cite: 49, 56].

---

## 🚀 Hướng dẫn sử dụng chương trình

### Bước 1: Chuẩn bị cơ sở dữ liệu (Nếu muốn lưu lịch sử)
1. Mở **SQL Server Configuration Manager**, bật **TCP/IP** và cấu hình cổng `14333` tại mục `IPAll`, sau đó Restart dịch vụ SQL Server[cite: 12, 53].
2. Mở **SSMS**, chạy file `01_Tao_CSDL.sql` để tạo database `LAB3_MaHoaKhoaCongKhai` và bảng `dbo.History`[cite: 13].
3. Chạy file `02_Tao_TaiKhoan_LAB.sql` để tạo user `lab3_user` (mật khẩu: `LabUser@2026`)[cite: 13, 14].

### Bước 2: Chạy ứng dụng trên NetBeans
1. Mở project `LAB3_MaHoaKhoaCongKhai` trong NetBeans[cite: 3, 56].
2. **Chạy từng bài độc lập**[cite: 4, 8]:
   - Chuột phải vào file tương ứng trong package `week_03` (`RSADemo.java`, `frm_RSA.java`, `frm_ECC.java`, `frm_RSA_AES.java`, `frm_ECC_Signature.java`) $\rightarrow$ chọn **Run File** (`Shift + F6`)[cite: 8, 56].
3. **Chạy toàn bộ hệ thống (sau khi hoàn thiện `frm_Menu.java`)**[cite: 4]:
   - Nhấn **`F6` (Run Project)** để mở cửa sổ điều hướng `frm_Menu`[cite: 4].
   - Chọn **Kết nối SQL Server** $\rightarrow$ nhập mật khẩu `LabUser@2026` $\rightarrow$ nhấn **Test Connection**[cite: 19, 21].
   - Thực hiện mã hóa/ký số ở các chức năng, sau đó mở **Xem lịch sử** (`frm_LichSu`) để kiểm tra nhật ký ghi nhận trong database[cite: 20, 52].

---

## 🔧 Ghi chú cần bổ sung để hoàn thiện hồ sơ nộp bài
1. **Bài 3.3 (`frm_ECC`)**: Bổ sung ảnh chụp thao tác `Save Keys` / `Load Keys` (che dòng `PRIVATE=...`) và ảnh báo lỗi `Tag mismatch` khi đổi cặp khóa mới[cite: 54].
2. **SQL Server & Menu**: Tạo form `frm_Menu.java` (nếu chưa có)[cite: 50, 56], kết nối SQL Server qua `frm_KetNoiSQL.java`, thực hiện các thao tác để sinh dữ liệu vào `dbo.History` và chụp ảnh `frm_LichSu` cùng kết quả truy vấn từ `03_KiemTra_LichSu.sql` trên SSMS[cite: 52, 54].
