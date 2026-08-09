# Bug & Performance Issues Report

## Issue 1: `[Tên lỗi, VD: HTTP 500 Internal Server Error khi Stress Test API tạo sản phẩm]`

- **Endpoint:** `POST /api/products`
- **Type:** Performance Issue / Functional Bug
- **Description:** `[Mô tả: Khi mức tải đạt 100 threads, server không phản hồi và văng lỗi 500]`
- **GitHub Issue Link:** `[Link issue trên repo]`
- **Evidence (Screenshot):**
  `[Chèn ảnh lỗi trong View Results Tree của JMeter vào đây (nếu có)]`

---

## Issue 2: [Data Collision] HTTP 409 Conflict khi chạy Spike Test chức năng Đăng ký

- **Endpoint:** `POST /api/register`
- **Type:** Test Script Defect / Functional Limitation
- **Description:** Khi thực thi kịch bản Spike Test với 150 Virtual Users dội tải vào hệ thống trong 1 giây, cơ sở dữ liệu gặp hiện tượng đụng độ dữ liệu (Data Collision) nghiêm trọng. Do script ban đầu do AI sinh ra chỉ sử dụng dữ liệu tĩnh từ CSV, các threads đồng thời gửi cùng một địa chỉ email lên server. Hệ thống lập tức từ chối và trả về mã lỗi `409 Conflict` (Email đã tồn tại trong hệ thống). Lỗi này làm thất bại toàn bộ kịch bản đo lường hiệu năng.
- **Resolution (Khắc phục):** Đã khắc phục trực tiếp trong kịch bản JMeter bằng cách áp dụng hàm `${__time()}` vào đuôi chuỗi email trong payload JSON để đảm bảo tính duy nhất (Unique constraint) của mỗi request. Kịch bản sau đó đã Pass 100%.
- **GitHub Issue Link:** `[Tạo 1 issue trên Github Repo của bạn mô tả lỗi này và dán link vào đây]`
- **Evidence (Screenshot):**
  `[Chèn ảnh màn hình View Results Tree báo lỗi đỏ HTTP 409 vào đây]`
