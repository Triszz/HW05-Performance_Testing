# BÁO CÁO HW#05 - Performance Testing

**Thông tin sinh viên:**

- **Họ và tên:** Trần Thanh Trí
- **MSSV:** 23127503
- **Lớp:** 23KTPM2
- **Môn học:** Kiểm thử phần mềm
- **Hệ thống (SUT):** EShop

---

## Hardware Specifications & Environment

- **CPU:** 12th Gen Intel(R) Core(TM) i5-1240P (16 CPUs), ~1.7GHz
- **RAM:** 16GB
- **OS:** Windows 11
- **Hostname:** THANHTRI

  ![alt text](images/Hardware_Specs.png)

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

### 1.3. Human Review & Script Fixes

Dựa trên nguyên tắc "AI-First strategy" và yêu cầu đánh giá phê bình (Critical review) của đồ án, em đã rà soát kỹ lưỡng kịch bản do AI sinh ra. Thay vì chỉ nhìn vào kết quả "màu xanh" (Pass) của JMeter, em đã phân tích sâu vào payload và phát hiện sự kết hợp giữa lỗ hổng của AI và khiếm khuyết của hệ thống:

- **What AI got wrong or missed:**
  Đối với kịch bản Spike Test (API `POST /api/register`), AI đã phạm phải 2 sai lầm:
  1. Sử dụng dữ liệu tĩnh từ CSV, dẫn đến việc đẩy 150 request có cùng một địa chỉ email lên server trong 1 giây.
  2. Kịch bản của AI mắc lỗi **"Weak Assertions"** (chỉ dựa vào HTTP Status 200). Nó không hề có cơ chế kiểm tra tính toàn vẹn của dữ liệu sau khi đăng ký. Thêm vào đó, AI đã bỏ qua việc xử lý **"account-lockout handling"** do thiếu ngữ cảnh về đặc thù của API này.

- **Why AI missed them:**
  AI chỉ tập trung vào việc tạo ra đủ tải (Load generation) theo đúng Prompt mà thiếu đi tư duy kiểm thử nghiệp vụ (Business Logic Validation). AI mặc định rằng hệ thống SUT hoạt động hoàn hảo và sẽ tự động chặn các luồng dữ liệu sai (như trùng email), nên nó không thiết lập các Assertion chặt chẽ để rào lỗi.

- **How I fixed it (Human Intervention) & Defect Discovery:**
  Khi chạy kịch bản tĩnh của AI, tất cả 150 requests đều trả về `200 OK`. Nhờ việc tự đánh giá kết quả (Human Review) thay vì tin tưởng mù quáng vào AI, em đã phát hiện ra một **Lỗ hổng logic nghiêm trọng của SUT**: Hệ thống hoàn toàn không có ràng buộc duy nhất (Unique Constraint) cho Email, cho phép tạo hàng trăm tài khoản trùng lặp.
  Để fix kịch bản cho chuẩn mực, em đã chủ động thêm hàm `${__time()}` vào cấu hình email trong JMeter (`"email": "${base_email}${__time()}@domain.com"`) để đảm bảo kịch bản Performance Test sinh ra dữ liệu sạch và đúng chuẩn thực tế nhất, tránh làm ô nhiễm Database của hệ thống.

### 1.4. Execution Evidence

_Đính kèm ảnh chụp màn hình hiển thị CÙNG LÚC phần mềm JMeter đang chạy và Task Manager/htop cho 3 kịch bản:_

- **Ảnh Load Test:**

  ![alt text](images/Evidence_LoadTest.png)

- **Ảnh Spike Test:**

  ![alt text](images/Evidence_SpikeTest.png)
  _(**Nhận xét:** Kịch bản Spike dội 150 requests đăng ký trong 1 giây để ép tải CPU bằng thuật toán Hashing. Tuy nhiên, hệ thống xử lý xuất sắc với 0% lỗi[cite: 3]. CPU chỉ tăng nhẹ lên mức 11%, thời gian phản hồi tối đa đạt 169ms[cite: 3]. Điều này cho thấy sức mạnh phần cứng hiện tại hoàn toàn dư sức đáp ứng, hoặc thuật toán mã hóa mật khẩu của Backend đang sử dụng chi phí tính toán (cost factor) khá thấp)._

- **Ảnh Stress Test:**

### 1.5. Endurance Threshold

Để xác định ngưỡng chịu đựng của phần cứng, kịch bản Load Test (Read-heavy) đã được cấu hình chạy ngâm (Soak/Endurance Test) liên tục trong 10 phút với 50 Virtual Users[cite: 1]. Dựa trên số liệu thu thập được từ hệ thống giám sát và báo cáo của JMeter, hệ thống ghi nhận các thông số như sau:

- **Maximum Stable RPS (Throughput):** Hệ thống duy trì tải cực kỳ ổn định ở mức **~24.0 req/sec** (chính xác là 23.98 req/sec) trong suốt 10 phút mà không phát sinh bất kỳ lỗi nào (Error Rate: 0%)[cite: 3]. Tốc độ phản hồi (Response Time) vô cùng xuất sắc với mức trung bình (Mean) là 2.33ms và 95th percentile (pct2) chỉ đạt 4.0ms[cite: 3].
- **Memory Ceiling & Resource Usage:** Mặc dù CPU hoạt động khá nhẹ nhàng (chỉ ở mức ~14% cho chip Intel Core i5-1240P), nhưng **RAM đã chạm đỉnh 14.4 GB / 15.7 GB (tức 92% công suất phần cứng)**.
- **Kết luận:** Ngưỡng chịu đựng (Endurance Threshold) của hệ thống SUT trên phần cứng hiện tại bị giới hạn bởi bộ nhớ (Memory-bound). Backend xử lý các tác vụ truy xuất dữ liệu rất nhanh, nhưng nếu duy trì tải này lâu hơn 10 phút hoặc tăng thêm số lượng Virtual Users, server có nguy cơ cao bị crash do tràn RAM (Out of Memory) thay vì quá tải CPU.

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
