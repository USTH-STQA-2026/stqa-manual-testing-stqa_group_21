# Test Summary — Báo cáo tổng hợp kiểm thử

> Đây là hoạt động **Quality Assurance** — đánh giá chất lượng tổng thể của hệ thống Thư viện ABC dựa trên kết quả thực thi 32 test case theo SRS v1.0.

---

## 1. Thông tin nhóm

| Mục | Thông tin |
|-----|----------|
| **Nhóm** | Nhóm 21 |
| **Lớp** |  |
| **Ngày báo cáo** | 21/05/2026 |
| **Hệ thống kiểm thử** | https://stqa.rbc.vn — v1.0 |
| **Tham chiếu** | SRS v1.0 (REQ-01 → REQ-08) |

### Thành viên nhóm

| # | MSSV | Họ và tên | Vai trò | Đóng góp chính |
|---|------|-----------|---------|----------------|
| 1 | 23BA14068 | Nguyễn Tiến Dũng | Nhóm trưởng | Điều phối, viết IDM, BUG-03 |
| 2 | 23BA14005 | Lê Đức Anh | Thành viên | TC REQ-07, BUG-07 |
| 3 | 23BA14057 | Hoàng Lê Anh Đức | Thành viên | TC REQ-04/05/08, BUG-02, BUG-05 |
| 4 | 23BA14305 | Nguyễn Cao Tuấn | Thành viên | TC REQ-05, BUG-04 |
| 5 | 23BA14105 | Nguyễn Minh Hiếu | Thành viên | TC REQ-03/06, BUG-01, BUG-06 |

---

## 2. Tổng quan kết quả

| Chỉ số | Giá trị |
|--------|---------|
| Tổng số test case | **32** |
| Pass | **22** |
| Fail | **9** |
| Blocked | **1** |
| Not Run | **0** |
| **Tỷ lệ Pass** | **68.75%** |
| **Số bug phát hiện** | **7** (BUG-01 → BUG-07) |

### 2.1. Phân bổ theo nhóm chức năng

| Nhóm chức năng | REQ | TC | Pass | Fail | Blocked | Pass Rate | Đánh giá |
|----------------|-----|----|------|------|---------|-----------|----------|
| Đăng nhập | REQ-01 | 4 | 4 | 0 | 0 | 100% | ✅ Hoạt động ổn định, đầy đủ thông báo lỗi theo SRS |
| Xem danh sách sách | REQ-02 | 4 | 4 | 0 | 0 | 100% | ✅ Cập nhật real-time tốt, phân quyền đúng |
| Tìm kiếm & lọc sách | REQ-03 | 5 | 4 | 1 | 0 | 80% | ⚠ Filter thể loại không case-insensitive (BUG-01) |
| Mượn sách | REQ-04 | 5 | 3 | 2 | 0 | 60% | ❌ Vi phạm giới hạn 3 sách, sai thông báo Suspended |
| Trả sách | REQ-05 | 4 | 2 | 2 | 0 | 50% | ❌ Thiếu cảnh báo quá hạn, lỗ hổng phân quyền |
| Xử lý quá hạn | REQ-06 | 3 | 2 | 1 | 0 | 66.7% | ⚠ Sai logic biên `dueDate ≤ today` |
| Quản lý thành viên | REQ-07 | 4 | 2 | 2 | 0 | 50% | ❌ Email validation sai 2 chiều |
| Tra cứu phiếu mượn | REQ-08 | 3 | 1 | 1 | 1 | 33.3% | ❌ Lỗ hổng nghiêm trọng: Member xem được phiếu người khác |

### 2.2. Phân bổ bug theo mức độ

| Mức độ | Số lượng | Bug IDs | Đặc điểm |
|--------|---------|---------|----------|
| **High** | 4 | BUG-03, BUG-05, BUG-06, BUG-07 | Vi phạm rule nghiệp vụ cốt lõi hoặc lỗ hổng bảo mật |
| **Medium** | 3 | BUG-01, BUG-02, BUG-04 | Sai thông báo / sai trải nghiệm, không phá vỡ luồng chính |
| **Low** | 0 | — | — |

### 2.3. Bug map ↔ REQ

| Bug ID | TC liên quan | REQ vi phạm | Loại lỗi |
|--------|--------------|-------------|----------|
| BUG-01 | TC-13 | REQ-03 | Functional — case sensitivity |
| BUG-02 | TC-16 | REQ-04 | Functional — sai thông báo |
| BUG-03 | TC-18 | REQ-04 | Business rule violation — vượt giới hạn |
| BUG-04 | TC-21 | REQ-05 | Missing feature — thiếu cảnh báo quá hạn |
| BUG-05 | TC-22, TC-31 | REQ-05, REQ-08 | **Security / Authorization** |
| BUG-06 | TC-23 | REQ-06 | Boundary logic — `<` vs `≤` |
| BUG-07 | TC-26, TC-27 | REQ-07 | Validation logic sai 2 chiều |

---

## 3. Kỹ thuật thiết kế đã sử dụng

| Kỹ thuật | Áp dụng cho REQ | Số TC | Giải thích cách áp dụng |
|----------|-----------------|-------|-------------------------|
| **EP (Equivalence Partitioning)** | REQ-01, 02, 03, 05, 06, 07, 08 | ~22 TC | Chia input thành các lớp tương đương: email tồn tại / không tồn tại, role (Librarian / Member / Guest), keyword (có / không), trạng thái thành viên (Active / Suspended / Expired). Mỗi lớp chọn 1 đại diện để test. |
| **BVA (Boundary Value Analysis)** | REQ-04, 05, 06 | ~5 TC | Test giá trị biên: số sách mượn = 3 (giới hạn trên), `Return Date = dueDate` (biên đúng hạn), `dueDate = today` (biên quá hạn). |
| **Decision Table** | REQ-04, 05, 08 | ~7 TC | Kết hợp nhiều điều kiện độc lập (trạng thái sách × trạng thái member × số sách đang mượn) để sinh đầy đủ rule. Xem bảng chi tiết trong `test-cases (1).md` mục Step 1.5. |

> **Bước Input Domain Modeling (IDM)** đã được thực hiện cho cả 8 REQ trước khi viết TC — giúp đảm bảo độ phủ hệ thống và không bỏ sót characteristic quan trọng.

---

## 4. Phân tích chất lượng phần mềm

### 4.1. Điểm mạnh

- **REQ-01 (Đăng nhập)**: Pass 100%, thông báo lỗi đầy đủ 3 trường hợp (email sai / password sai / bỏ trống) đúng theo SRS.
- **REQ-02 (Xem sách)**: Hiển thị real-time chính xác, phân quyền Guest/Member/Librarian đúng.
- **REQ-03 (Search theo tên/tác giả)**: Search có hỗ trợ case-insensitive (TC-12 Pass).
- **Phân quyền cấp cao**: Member không thấy nút "Kiểm tra quá hạn" (TC-25 Pass), không vào được tab Thành viên.
- **Validation chống trùng email**: Block email đã tồn tại đúng yêu cầu (TC-28 Pass).

### 4.2. Điểm yếu

- **Bảo mật phân quyền cấp dữ liệu (REQ-05 + REQ-08)**: Lỗ hổng **nghiêm trọng nhất** — member có thể tra cứu phiếu mượn của người khác và thậm chí trả sách thay (BUG-05). Đây là rủi ro Confidentiality + Integrity.
- **Business rule không được enforce (REQ-04)**: Cho phép mượn vượt giới hạn 3 sách (BUG-03) — vi phạm trực tiếp tham số hệ thống mục 3.4 SRS.
- **Email validation logic sai 2 chiều (REQ-07)**: Vừa reject email hợp lệ vừa accept email không hợp lệ (BUG-07) — chứng tỏ logic validate bị đảo điều kiện hoặc dùng sai regex.
- **Sai thông báo phân biệt trạng thái (REQ-04)**: Suspended ≠ Expired nhưng hệ thống trả cùng message (BUG-02) — vi phạm requirement "phải mô tả đúng lý do từ chối".
- **Logic biên ngày tháng (REQ-06)**: SRS yêu cầu `dueDate ≤ today` đánh dấu Overdue, hệ thống đang dùng `<` → bỏ lọt 1 ngày (BUG-06).
- **Thiếu cảnh báo quá hạn khi trả sách (REQ-05)**: Không hiển thị warning khi `Return Date > dueDate` (BUG-04).
- **Filter case-sensitive (REQ-03)**: Filter thể loại không tuân theo quy tắc case-insensitive (BUG-01) trong khi search lại đúng → inconsistent giữa các tính năng cùng module.

---

## 5. Đề xuất ưu tiên sửa lỗi

> **Tiêu chí ưu tiên**: kết hợp **Severity** (mức độ nghiêm trọng kỹ thuật) và **Priority nghiệp vụ** (tác động đến bảo mật, dữ liệu, business rule cốt lõi).

| Thứ tự | Bug | Severity | Priority | Lý do ưu tiên |
|--------|-----|----------|----------|---------------|
| **1** | BUG-05 | High (đề nghị Critical) | P0 | Lỗ hổng bảo mật + data integrity. Member có thể xem/sửa dữ liệu người khác → vi phạm REQ-05 & REQ-08, rủi ro pháp lý nếu hệ thống chứa dữ liệu thật. **Phải sửa trước khi release.** |
| **2** | BUG-03 | High | P0 | Vi phạm business rule cốt lõi (giới hạn 3 sách). Mất kiểm soát luân chuyển sách, ảnh hưởng vận hành thư viện. |
| **3** | BUG-07 | High | P1 | Email validation sai 2 chiều → không thể thêm member hợp lệ, lại tạo data rác. Chặn workflow tạo thành viên mới. |
| **4** | BUG-06 | High | P1 | Sai logic biên `dueDate ≤ today` → bỏ lọt phiếu đến hạn 1 ngày, ảnh hưởng quản lý quá hạn. |
| **5** | BUG-04 | Medium | P2 | Thiếu cảnh báo quá hạn khi trả sách → không phá luồng nhưng ảnh hưởng nghiệp vụ phạt trễ. |
| **6** | BUG-02 | Medium | P2 | Sai thông báo Suspended/Expired — không phá luồng, nhưng gây xử lý sai cho thủ thư. |
| **7** | BUG-01 | Medium | P3 | Filter thể loại case-sensitive — workaround dễ (nhập đúng chữ in hoa đầu), không cấp thiết. |

### Lộ trình sửa đề xuất

- **Sprint hotfix (1–3 ngày)**: BUG-05, BUG-03 — bảo mật + business rule.
- **Sprint kế tiếp (1 tuần)**: BUG-07, BUG-06, BUG-04 — validation & logic.
- **Backlog**: BUG-02, BUG-01 — UI/UX polish.

---

## 6. Kết luận

**Hệ thống Thư viện ABC v1.0 CHƯA SẴN SÀNG để phát hành production.**

### Lý do

1. **Tỷ lệ Pass 68.75%** thấp hơn ngưỡng chấp nhận thông thường (≥ 90% cho release candidate).
2. **4 bug High** trong đó **1 lỗ hổng bảo mật/authorization** (BUG-05) — release sẽ tạo rủi ro lộ dữ liệu.
3. **2 nhóm chức năng có Pass Rate ≤ 50%**: Trả sách (50%), Quản lý thành viên (50%), Tra cứu phiếu mượn (33.3%) — chất lượng không đồng đều.
4. **Vi phạm trực tiếp ít nhất 4 REQ** trong SRS: REQ-04 (giới hạn 3 sách), REQ-05 (cảnh báo quá hạn), REQ-07 (email validation), REQ-08 (phân quyền dữ liệu).

### Điều kiện để release

- Sửa **tất cả bug High** (BUG-03, 05, 06, 07).
- Re-test 9 TC Fail + TC-32 Blocked → đạt 100% Pass cho các TC này.
- Regression test toàn bộ 32 TC → tỷ lệ Pass ≥ 95%.
- Bổ sung security review cho authorization model (sau khi sửa BUG-05).

---

## 7. Bài học rút ra

- **SRS là nguồn sự thật**: Nhiều bug phát hiện được nhờ đối chiếu sát từng dòng SRS (đặc biệt mục Quy tắc, Thông báo lỗi). Nếu chỉ test theo "cảm tính" sẽ bỏ sót BUG-02 (sai 1 từ trong message), BUG-06 (sai 1 ký tự `<` vs `≤`).
- **Boundary value analysis cực kỳ giá trị**: BUG-06 chỉ phát hiện được khi test giá trị biên `dueDate = today`. Test các giá trị "rõ ràng quá hạn" sẽ Pass và bỏ sót lỗi.
- **Decision Table giúp phát hiện sai phân biệt trạng thái**: BUG-02 (Suspended vs Expired) lộ ra khi test đủ các rule trong bảng decision.
- **Phân quyền cần test cả 2 chiều**: BUG-05 (đọc + ghi dữ liệu người khác) cho thấy authorization check phải áp dụng cho cả query và action, không chỉ ẩn nút trên UI.
- **Test data setup quan trọng**: Một số TC (TC-18, TC-23) phụ thuộc state trước đó → nhóm đã rút kinh nghiệm dùng "Khôi phục dữ liệu" trước mỗi nhóm test.
- **Phối hợp nhóm**: Mỗi thành viên phụ trách 1 module + cross-review giúp phát hiện inconsistency trong mô tả TC.
