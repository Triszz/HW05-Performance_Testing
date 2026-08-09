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

_Lập luận lý do tại sao ghép cặp 3 loại test này với 3 endpoint tương ứng:_

- **Read-heavy (`GET /api/products/:id`) ➔ Load Test:** `[Giải thích lý do]`
- **Auth-heavy (`POST /api/register`) ➔ Spike Test:** `[Giải thích lý do]`
- **Transactional (`POST /api/products`) ➔ Stress Test:** `[Giải thích lý do]`

### 1.2. Human Review & Script Fixes

_Phân tích những lỗi AI đã mắc phải khi sinh kịch bản và cách khắc phục:_

- **What AI got wrong or missed:** `[Đặc biệt nhấn mạnh vào lỗi tạo dữ liệu tĩnh gây văng lỗi HTTP 409 Conflict ở API Register, hoặc các thông số Ramp-up/Think-time phi lý]`
- **Why AI missed them:** `[Giải thích do AI thiếu ngữ cảnh hệ thống, không hiểu tính duy nhất của dữ liệu, v.v.]`
- **How I fixed it:** `[Nêu cách dùng hàm ${__time()} hoặc các cấu hình đã tự điều chỉnh trong JMeter]`

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
