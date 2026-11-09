# Báo cáo: Thiết kế Quy trình Troubleshooting Nội bộ & Chuẩn hóa Bug Report

## 1. Bối cảnh & Vấn đề (Problem Statement)
Trong quá trình vận hành nhóm Startup, các thành viên thường xuyên gặp lỗi môi trường cục bộ (Docker container crash, Port conflict `EADDRINUSE`, sai lệch biến `.env`). 
- **Thực trạng:** Thành viên nhắn tin cầu cứu rời rạc, thiếu thông tin ngữ cảnh vào nhóm chat chung.
- **Hậu quả:** Làm trôi tin nhắn quan trọng, gây gián đoạn sự tập trung (Context Switching) của các thành viên khác và tốn thời gian giải đáp lặp đi lặp lại cùng một lỗi.

---

## 2. Thiết kế kiến trúc: Mẫu báo cáo lỗi 4 mảnh ghép (Bug Report Template)

Mọi câu hỏi kỹ thuật gửi lên kênh hỗ trợ chung bắt buộc phải tuân thủ cấu trúc **4 mảnh ghép (Context - Expected - Actual - Log)**:

| Mảnh ghép | Nội dung bắt buộc | Mục đích kỹ thuật |
| :--- | :--- | :--- |
| **1. Context** | Hệ điều hành, phiên bản Docker/Runtime, nhánh Git, câu lệnh vừa chạy. | Giúp người hỗ trợ nắm bắt ngay môi trường mà không cần hỏi lại. |
| **2. Expected** | Trạng thái đúng của hệ thống theo thiết kế ban đầu. | Làm rõ mục tiêu của tác vụ đang thực hiện. |
| **3. Actual** | Hiện tượng lỗi thực tế xảy ra (mã thoát, treo cổng, từ chối kết nối). | Khoanh vùng phạm vi sự cố. |
| **4. Log** | Đoạn mã lỗi dạng văn bản (Raw Text) đặt trong khối code + Các bước đã tự thử. | Cung cấp dữ liệu chính xác để tra cứu và tránh tư vấn trùng lặp. |

### 📋 Mẫu Template áp dụng thực tế (Copy & Paste)
```markdown
### 🚨 [BUG/ENV] <Tiêu lỗi tóm tắt đề>

- **1. Context (Bối cảnh):**
  - OS & Tool: Windows 11 (WSL2) / Docker Desktop 4.x
  - Branch / Service: `main` - Service `api-gateway`
  - Command: `docker compose up -d`
- **2. Expected (Kỳ vọng):** Container khởi chạy thành công tại cổng `3000`.
- **3. Actual (Thực tế):** Quá trình khởi tạo thất bại, báo lỗi trùng cổng mạng.
- **4. Log & Self-Check (Nhật ký & Tự xử lý):**
  ```text
  Error: listen EADDRINUSE: address already in use :::3000
  ```
  - **Các bước đã thử:** Đã tìm PID bằng `netstat -ano | findstr :3000` và tắt tiến trình Node cũ nhưng khởi chạy lại vẫn báo lỗi.
```

---

## 3. Sản phẩm hoàn chỉnh: Quy trình 3 bước tra cứu bắt buộc (3-Step Troubleshooting SOP)

Trước khi gửi Bug Report lên nhóm, thành viên bắt buộc thực hiện quy trình