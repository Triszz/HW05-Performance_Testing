# BÁO CÁO HW#05 - Performance Testing

**Thông tin sinh viên:**

- **Họ và tên:** Trần Thanh Trí
- **MSSV:** 23127503
- **Lớp:** 23KTPM2
- **Môn học:** Kiểm thử phần mềm
- **Hệ thống (SUT):** EShop
- **Public GitHub Repository:** https://github.com/Triszz/HW05-Performance_Testing

---

## 1. Self-Assessment

| **No.** | **Criteria**                                                                      | **Grade** | **Self-Assessed Grade** |
| ------- | --------------------------------------------------------------------------------- | --------- | ----------------------- |
| **1**   | Task 1 — Load testing                                                             | 20        | **20**                  |
| **2**   | Task 1 — Stress testing                                                           | 20        | **20**                  |
| **3**   | Task 1 — Spike testing                                                            | 20        | **20**                  |
| **4**   | Task 2 — AI analysis + misinterpretation hunt (with correct values from raw logs) | 20        | **20**                  |
| **5**   | Task 3 — Continuous Performance Testing proposal (G9.6)                           | 10        | **10**                  |
| **6**   | Agent Skills                                                                      | 10        | **10**                  |
|         | **Total**                                                                         | **100**   | **100**                 |

_(Lưu ý: Em đã chủ động điều chỉnh khung điểm Task 2 thành 20 để tổng điểm đạt mốc 100 chuẩn tuyệt đối)._

---

## 2. Test Summary Report

Báo cáo dưới đây tổng hợp kết quả thực thi kiểm thử hiệu năng (Performance Testing) bằng Apache JMeter, áp dụng phương pháp đối chiếu chéo giữa log thực thi thô (raw `.jtl`) và tài nguyên phần cứng (Resource Monitoring) để tìm ra điểm nghẽn hệ thống (Bottleneck) cũng như các lỗ hổng bảo mật tiềm ẩn dưới Backend.

### 2.1. Execution Metrics & Scenarios

- **Scenarios Run:** 3 kịch bản ép tải chuyên sâu.
- **Endpoint Groups Covered:**
  - **Read-heavy (Load Test):** `GET /api/products/:id` (50 Threads / 10 Phút).
  - **Auth-heavy (Spike Test):** `POST /api/register` (150 Threads / 1 Giây).
  - **Transactional (Stress Test):** `POST /api/products` (100 Threads / 5 Phút).
- **Performance Overview:** Hệ thống xử lý xuất sắc các tác vụ với **0% Error Rate** trên tổng số hơn 60,000 requests. Không ghi nhận bất kỳ lỗi 500/502/503 (Server Overload) nào. Tuy nhiên, hiệu năng bị ảnh hưởng nặng bởi phần cứng và kiến trúc Database.

### 2.2. Endurance Threshold & Bottlenecks

Dựa trên dữ liệu giám sát hệ thống qua Task Manager và JMeter:

- **Maximum Stable RPS (Throughput):** **`~24.0 req/sec`** (Ghi nhận ở bài Load Test. Hệ thống duy trì cực kỳ ổn định với thời gian phản hồi trung bình chỉ 2.33ms).
- **Memory Ceiling (Điểm nghẽn RAM):** RAM chạm đỉnh **`14.4 GB`** (Chiếm tới **92%** công suất máy) trong khi CPU chỉ hoạt động 14%. Điều này chỉ ra hệ thống đang bị giới hạn bởi bộ nhớ (Memory-bound). Nếu tiếp tục tăng tải, hệ thống sẽ rủi ro bị crash do Out of Memory trước khi bị nghẽn CPU.
- **Disk I/O Impact:** Tại kịch bản Stress Test, áp lực ghi liên tục xuống file SQLite đã khiến tiến trình ổ cứng (Disk) tăng vọt lên mức **29%**, mở ra bài toán cần phải bật cơ chế WAL (Write-Ahead Logging) cho Database.

### 2.3. Defect Discovery (Bắt Bug qua Performance Metrics)

- **Total Number of Bugs Logged:** 2
- **Severity Breakdown:**
  - **[1] Security/Critical Bug:** Mật khẩu người dùng bị lưu dưới dạng Plain-text. Bằng chứng được phát hiện thông qua sự bất thường ở bài Spike Test: Hệ thống chịu cú sốc 150 reqs/giây nhưng CPU chỉ nhích nhẹ lên **11%**. Truy xuất thẳng vào Database xác nhận Backend đã bỏ qua hoàn toàn công đoạn Hashing mật khẩu, gây sai lệch kỳ vọng về chi phí tính toán CPU.
  - **[1] Major (Functional Bug):** Hệ thống vắng bóng ràng buộc Unique Constraint cho trường Email ở tầng Database, cho phép tạo hàng loạt tài khoản rác trùng lặp thông tin.
- **Summary:**
  Thay vì chỉ nhìn vào báo cáo màu xanh (`200 OK`) của JMeter, sự kết hợp giữa tư duy Performance Testing và Security Testing đã giúp lật tẩy những lỗ hổng nghiêm trọng về kiến trúc phần mềm mà các bài test chức năng thông thường dễ dàng bỏ sót. Chi tiết xem tại `Bug_Report.md`.

---

## 3. Demo Videos

### 3.1. Performance Execution Demo (Task 1)

Video dưới đây demo toàn bộ quá trình thực thi 3 kịch bản kiểm thử (Load, Spike, Stress) theo thời gian thực trên màn hình chia đôi (Split-screen). Video thể hiện rõ sự biến động tương quan giữa lưu lượng Throughput trên JMeter và biểu đồ tài nguyên phần cứng (CPU, RAM, Disk I/O) trên Task Manager, kèm theo quá trình phân tích bắt Bug bảo mật tại Database.

🔗 **YouTube Unlisted Link:** https://youtu.be/d9Kchasc_5c

### 3.2. Agent Skill Demo

Video dưới đây trình diễn khả năng ứng dụng thực tế và tính tái sử dụng (SUT-Agnostic) của **Agent Skill v3.1.0: Performance Testing & Log Analysis Expert**. Video mô phỏng quy trình kiểm duyệt (Human Approval Gate) để sửa lỗi Data Collision từ AI, cũng như kích hoạt giao thức Săn lỗi ảo giác (Misinterpretation Hunt Protocol) để phản biện lại các nhận định sai lệch dựa trên bằng chứng log thô (Raw `.jtl`).

🔗 **YouTube Unlisted Link:** https://youtu.be/9siGDMSLyKg
