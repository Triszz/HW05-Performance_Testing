# Bug & Performance Issues Report

## Issue 1: `[Tên lỗi, VD: HTTP 500 Internal Server Error khi Stress Test API tạo sản phẩm]`

- **Endpoint:** `POST /api/products`
- **Type:** Performance Issue / Functional Bug
- **Description:** `[Mô tả: Khi mức tải đạt 100 threads, server không phản hồi và văng lỗi 500]`
- **GitHub Issue Link:** `[Link issue trên repo]`
- **Evidence (Screenshot):**
  `[Chèn ảnh lỗi trong View Results Tree của JMeter vào đây (nếu có)]`

---

## Issue 2: [Functional Bug] Hệ thống cho phép đăng ký hàng loạt tài khoản trùng Email

- **Endpoint:** `POST /api/register`

- **Type:** Functional Bug / Security Issue

- **Description:** Trong quá trình chạy Spike Test với 150 Threads sử dụng cùng một địa chỉ email tĩnh (do kịch bản AI sinh ra), toàn bộ 150 requests đều trả về `200 OK - User registered successfully`. Kiểm tra lại Database cho thấy hệ thống đã thực sự tạo ra 150 user khác nhau (ID khác nhau) nhưng dùng chung một địa chỉ Email. Hệ thống đã thiếu sót hoàn toàn việc kiểm tra Unique Constraint ở tầng Database và Validation ở Backend, gây rủi ro lớn về bảo mật và quản lý định danh.

- **GitHub Issue Link:** `[Tạo 1 issue trên Github Repo của bạn mô tả lỗi này và dán link vào đây]`

- **Evidence (Screenshot):**

  ![alt text](images/image.png)

  ![alt text](images/image-1.png)
