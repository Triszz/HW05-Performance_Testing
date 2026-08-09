# HW05: Performance Testing Portfolio

**Author:** Trần Thanh Trí (23127503)
**Public GitHub Repository:** `[Dán link GitHub chứa test plans và CSV data vào đây]`

---

## 1. Self-Assessment

| **No.** | **Criteria**                                                                      | **Grade** | **Self-Assessed Grade** |
| ------- | --------------------------------------------------------------------------------- | --------- | ----------------------- |
| **1**   | Task 1 — Load testing                                                             | 20        |                         |
| **2**   | Task 1 — Stress testing                                                           | 20        |                         |
| **3**   | Task 1 — Spike testing                                                            | 20        |                         |
| **4**   | Task 2 — AI analysis + misinterpretation hunt (with correct values from raw logs) | 10        |                         |
| **5**   | Task 3 — Continuous Performance Testing proposal (G9.6)                           | 10        |                         |
| **6**   | Agent Skills                                                                      | 10        |                         |
|         | **Total**                                                                         | **100**   |                         |

---

## 2. Test Summary Report

- **Scenarios Run:** 3 scenarios (Load Test, Spike Test, Stress Test).
- **Endpoint Groups Covered:**
  - Read-heavy: `GET /api/products/:id`
  - Auth-heavy: `POST /api/register`
  - Transactional: `POST /api/products`
- **Endurance Threshold:**
  - _Maximum Stable RPS:_ `[Điền số RPS, VD: 150 req/sec]`
  - _Memory Ceiling:_ `[Điền RAM tiêu thụ tối đa, VD: 450 MB]`
- **Defects Discovered:**
  - _Number of Functional Bugs:_ `[Điền số]`
  - _Number of Performance Issues:_ `[Điền số]`
- **Demo Video:**
  - 🔗 **YouTube Unlisted Link:** `[Dán link YouTube vào đây]`
