# BÁO CÁO HW#05 - Performance Testing

**Thông tin sinh viên:**

- **Họ và tên:** Trần Thanh Trí
- **MSSV:** 23127503
- **Lớp:** 23KTPM2
- **Môn học:** Kiểm thử phần mềm
- **Hệ thống (SUT):** EShop

---

## Hardware Specifications & Environment

- **CPU:** `[VD: Intel Core i7-12700H]`
- **RAM:** `[VD: 16GB DDR4]`
- **OS:** `[VD: Windows 11 / Ubuntu 22.04]`
- **Hostname:** `[Hostname khớp với HW04]`
- _(Đính kèm ảnh screenshot dxdiag hoặc screenfetch tại đây)_

---

## Task 1: AI-assisted Test Design and Execution

### 1.1. Scenario Pairing & Justification

Dựa trên đặc thù của hệ thống SUT, em đã phân tích và thiết lập sự ánh xạ 1:1 giữa các nhóm API và các loại hình kiểm thử hiệu năng như sau:

- **Read-heavy (`GET /api/products/:id`) ➔ Load Test:** Kịch bản này vô cùng phù hợp để đánh giá hiệu năng hệ thống khi có một lượng lớn người dùng duy trì việc lướt xem chi tiết các sản phẩm khác nhau. Hệ thống chủ yếu thực hiện các lệnh `SELECT` từ Database và trả về JSON.
- **Auth-heavy (`POST /api/register`) ➔ Spike Test:** Chức năng đăng ký yêu cầu Backend phải thực hiện thuật toán băm (hashing) mật khẩu - một tác vụ cực kỳ ngốn CPU. Kịch bản Spike mô phỏng một đợt tăng vọt người dùng đột ngột (ví dụ: mở cổng đăng ký săn sale lúc 0h), giúp kiểm tra xem CPU có bị "thắt cổ chai" (bottleneck) khi xử lý đồng loạt hàng trăm phép băm hay không.
- **Transactional (`POST /api/products`) ➔ Stress Test:** Chức năng thêm sản phẩm yêu cầu hệ thống phải xác thực JSON payload, kiểm tra khóa ngoại (category_id) và thực hiện lệnh `INSERT` vào Database. Sử dụng Stress Test cho API này giúp ép hệ thống thực hiện liên tục các giao dịch Write-heavy, qua đó tìm ra "điểm gãy" (breaking point) của Database dưới áp lực cao.

### 1.2. AI-Suggested Parameters

Thông qua việc prompt AI hỗ trợ cấu hình, các thông số thực tế (realistic parameters) được thiết lập cho 3 Thread Groups nhằm phản ánh đúng hành vi người dùng và mục tiêu kiểm thử:

- **Load Test (Read):** 50 Threads, Ramp-up 50s, Duration 10 phút. Áp dụng `Uniform Random Timer` (1000ms - 3000ms) để mô phỏng think-time của người dùng thực tế đang đọc mô tả sản phẩm.
- **Spike Test (Auth):** 150 Threads, Ramp-up 1s, Loop 1. Không sử dụng think-time để tạo ra một cú shock lưu lượng (burst traffic) tức thì vào hệ thống.
- **Stress Test (Transactional):** 100 Threads, Ramp-up 100s, Duration 5 phút. Áp dụng `Gaussian Random Timer` (~500ms) để giả lập tốc độ thao tác nhập liệu liên tục của Admin.

### 1.3. Execution Evidence

_Đính kèm ảnh chụp màn hình hiển thị CÙNG LÚC phần mềm JMeter đang chạy và Task Manager/htop cho 3 kịch bản:_

- **Ảnh Load Test:**
- **Ảnh Spike Test:**
- **Ảnh Stress Test:**

### 1.4. Endurance Threshold

- Kết quả chạy Soak/Endurance test trong 10-15 phút: `[Điền phân tích và thông số phần cứng khi duy trì tải lâu dài]`

---

## Task 2: AI Analysis and Misinterpretation Hunt

### 2.1. AI-suggested Performance Thresholds

`[Dán kết quả AI phân tích file .jtl vào đây]`

### 2.2. Misinterpretation Hunt (Bắt lỗi AI)

`[Chỉ ra điểm AI đọc sai số liệu. Trích dẫn giá trị ĐÚNG từ file raw .jtl của bạn để phản biện]`

### 2.3. AI's Recommendations Evaluation

`[Liệt kê các giải pháp tối ưu AI đề xuất và phân loại xem chúng là Feasible (Khả thi) hay Hallucinated (Ảo giác), kèm lý do]`

---

## Task 3: Continuous Performance Testing Proposal (Disrupt)

### 3.1. Pipeline Model (Flow Chart)

`[Chèn hình ảnh sơ đồ luồng CI/CD chạy Performance Test tự động tại đây]`

### 3.2. Trade-offs Discussion

`[Thảo luận về sự đánh đổi: Chi phí duy trì server, rủi ro báo động giả (false alarms) khi theo dõi chỉ số p95]`
