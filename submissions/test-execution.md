# Test Execution — Kết quả thực thi kiểm thử

> **Hướng dẫn**: Chạy từng TC trên hệ thống https://stqa.rbc.vn, ghi lại kết quả thực tế.
> Kết luận: **Pass** (kết quả đúng), **Fail** (kết quả sai → tạo bug report), **Blocked** (không thực hiện được vì lỗi khác chặn), **Not Run** (chưa chạy).

| Thông tin | |
|---|---|
| **Nhóm** | Nhóm 21 |
| **Ngày thực thi** | `<!-- DD/MM/YYYY — điền ngày nhóm chạy test thật -->` |
| **Trình duyệt** | Chrome `<!-- ví dụ: 126.0.6478.127 — vào chrome://version để xem -->` |
| **Hệ điều hành** | Windows 10 |
| **Người thực thi** | `<!-- Họ tên thành viên chạy test (có thể nhiều người) -->` |

---

## ⚠️ Trạng thái hiện tại của file

> Khung 25 dòng dưới đây đã được pre-fill các cột **tĩnh** (Mã TC, Nhóm, Kết quả mong đợi). Các cột còn lại (Kết quả thực tế, Kết luận, Minh chứng, Bug) đang được đánh dấu **⏳ Not Run** — nhóm cần truy cập https://stqa.rbc.vn, chạy từng TC theo `submissions/test-cases.md` rồi điền vào.

### Quy tắc điền cột "Kết luận"

| Ký hiệu | Khi nào dùng |
|---|---|
| **Pass** | Kết quả thực tế **trùng khớp** kết quả mong đợi |
| **Fail** | Kết quả thực tế **khác** mong đợi → bắt buộc tạo bug trong `bug-reports.md` |
| **Blocked** | Không thể chạy vì lỗi/môi trường khác chặn (vd: hệ thống sập) |
| **Not Run** | Chưa chạy |

---

## Kết quả chi tiết

| Mã TC | Nhóm chức năng | Kết quả mong đợi (tóm tắt) | Kết quả thực tế | Kết luận | Minh chứng | Bug |
|-------|---------------|---------------------------|-----------------|---------|-----------|----|
| TC-01 | Đăng nhập | Vào trang chủ, AppBar hiện "Nguyễn Thủ Thư — Thủ thư" | ⏳ | Not Run | | |
| TC-02 | Đăng nhập | Thông báo "Không tìm thấy thành viên" | ⏳ | Not Run | | |
| TC-03 | Đăng nhập | Thông báo "Vui lòng nhập email và mật khẩu" | ⏳ | Not Run | | |
| TC-04 | Mượn sách | Mượn được BOOK001, BOOK002 (đạt đủ 3 sách cùng BOOK013) | ⏳ | Not Run | | |
| TC-05 | Mượn sách | Từ chối mượn BOOK004 vì vượt giới hạn 3 sách | ⏳ | Not Run | | |
| TC-06 | Mượn sách | Từ chối, thông báo **đúng lý do "tạm ngưng"** | ⏳ | Not Run | | |
| TC-07 | Mượn sách | Từ chối, thông báo **đúng lý do "hết hạn"** | ⏳ | Not Run | | |
| TC-08 | Xem danh sách | Hiện ≥ 20 sách; BOOK001 có đủ 5 trường thông tin | ⏳ | Not Run | | |
| TC-09 | Xem danh sách | BOOK001 đổi "Đã mượn" tức thì, không cần refresh | ⏳ | Not Run | | |
| TC-10 | Tìm kiếm & lọc | Tìm "Flutter" → chỉ ra BOOK001 | ⏳ | Not Run | | |
| TC-11 | Tìm kiếm & lọc | Tìm "FLUTTER" → ra BOOK001 (case-insensitive) | ⏳ | Not Run | | |
| TC-12 | Tìm kiếm & lọc | Tìm "Nguyễn Minh Đức" → ra BOOK001 + BOOK009 | ⏳ | Not Run | | |
| TC-13 | Tìm kiếm & lọc | Tìm "XYZ123" → thông báo "Không tìm thấy sách" | ⏳ | Not Run | | |
| TC-14 | Tìm kiếm & lọc | Lọc "Quản trị" → ra BOOK004, BOOK012, BOOK013 | ⏳ | Not Run | | |
| TC-15 | Trả sách | BR003 chuyển "Đã trả", BOOK013 chuyển "Có sẵn" | ⏳ | Not Run | | |
| TC-16 | Trả sách | Trả BR001 thành công + hiển thị cảnh báo quá hạn | ⏳ | Not Run | | |
| TC-17 | Quá hạn | BR001 và BR003 chuyển trạng thái "Quá hạn" | ⏳ | Not Run | | |
| TC-18 | Quá hạn | Thành viên MEM002 không thấy nút "Kiểm tra quá hạn" | ⏳ | Not Run | | |
| TC-19 | Thêm thành viên | Thêm "Nguyễn Văn Thử" thành công, xuất hiện trong danh sách | ⏳ | Not Run | | |
| TC-20 | Thêm thành viên | Từ chối email "thunv@email" (thiếu `.` trong domain) | ⏳ | Not Run | | |
| TC-21 | Thêm thành viên | Từ chối email trùng `librarian@library.com` | ⏳ | Not Run | | |
| TC-22 | Thêm thành viên | Từ chối khi họ tên rỗng | ⏳ | Not Run | | |
| TC-23 | Tra cứu phiếu | MEM002 thấy đúng 2 phiếu BR001 + BR004 | ⏳ | Not Run | | |
| TC-24 | Tra cứu phiếu | MEM002 KHÔNG xem được phiếu của MEM006 | ⏳ | Not Run | | |
| TC-25 | Tra cứu phiếu | Thủ thư thấy đủ 5 phiếu BR001 → BR005 | ⏳ | Not Run | | |

---

## 📝 Ví dụ cách điền 1 hàng khi chạy thật

> 2 ví dụ dưới đây là **mẫu minh họa cách điền** sau khi chạy test. KHÔNG copy vào bảng trên — bảng trên giữ trạng thái Not Run cho đến khi nhóm chạy thật.

### Ví dụ Pass

| Mã TC | Nhóm chức năng | Kết quả mong đợi (tóm tắt) | Kết quả thực tế | Kết luận | Minh chứng | Bug |
|-------|---------------|---------------------------|-----------------|---------|-----------|----|
| TC-01 | Đăng nhập | Vào trang chủ, AppBar hiện "Nguyễn Thủ Thư — Thủ thư" | Hệ thống chuyển sang trang chủ. AppBar hiển thị "Nguyễn Thủ Thư" và badge "Thủ thư" như mong đợi. | **Pass** | `evidence/tc01-pass.png` | — |

### Ví dụ Fail

| Mã TC | Nhóm chức năng | Kết quả mong đợi (tóm tắt) | Kết quả thực tế | Kết luận | Minh chứng | Bug |
|-------|---------------|---------------------------|-----------------|---------|-----------|----|
| TC-20 | Thêm thành viên | Từ chối email "thunv@email" (thiếu `.` trong domain) | Hệ thống **tạo thành công** thành viên với email `thunv@email`. Không có thông báo lỗi định dạng. Vi phạm SRS REQ-07 (email phải có `@` VÀ có `.`). | **Fail** | `evidence/tc20-fail.png` | BUG-01 |

**Lưu ý quan trọng khi điền cột "Kết quả thực tế":**
- Mô tả **hành vi thật của hệ thống** (không lặp lại Expected).
- Nếu Fail: phải nói rõ **khác Expected ở đâu** + dẫn chiếu REQ bị vi phạm.
- Nếu có ảnh chụp → đặt vào thư mục `evidence/` (tạo mới nếu chưa có) và ghi đường dẫn vào cột Minh chứng.
- Mỗi Fail → tạo 1 bug ID (BUG-01, BUG-02, ...) trong `bug-reports.md` và đối chiếu sang cột "Bug".

---

## Tổng hợp kết quả

> Cập nhật sau khi chạy hết 25 TC.

| Chỉ số | Giá trị |
|--------|---------|
| Tổng số test case | 25 |
| Pass | `<!-- điền số -->` |
| Fail | `<!-- điền số -->` |
| Blocked | `<!-- điền số -->` |
| Not Run | `<!-- điền số (lý tưởng = 0 khi nộp bài) -->` |
| **Tỷ lệ Pass** | `<!-- (Pass / Tổng) × 100% -->` |

### Kết quả theo nhóm chức năng

| Nhóm | Tổng TC | Pass | Fail | Tỷ lệ Pass |
|------|---------|------|------|------------|
| Đăng nhập (REQ-01) | 3 | `<!-- -->` | `<!-- -->` | `<!-- % -->` |
| Mượn sách (REQ-04) | 4 | `<!-- -->` | `<!-- -->` | `<!-- % -->` |
| Xem danh sách (REQ-02) | 2 | `<!-- -->` | `<!-- -->` | `<!-- % -->` |
| Tìm kiếm & lọc (REQ-03) | 5 | `<!-- -->` | `<!-- -->` | `<!-- % -->` |
| Trả sách (REQ-05) | 2 | `<!-- -->` | `<!-- -->` | `<!-- % -->` |
| Xử lý quá hạn (REQ-06) | 2 | `<!-- -->` | `<!-- -->` | `<!-- % -->` |
| Thêm thành viên (REQ-07) | 4 | `<!-- -->` | `<!-- -->` | `<!-- % -->` |
| Tra cứu phiếu (REQ-08) | 3 | `<!-- -->` | `<!-- -->` | `<!-- % -->` |
| **Tổng** | **25** | | | |

---

## 🔧 Quy trình chạy test (gợi ý — để team thực thi nhanh)

1. **Chuẩn bị môi trường**:
   - Mở Chrome bản mới nhất, vào `chrome://version` để biết version → điền vào ô "Trình duyệt" ở đầu file.
   - Mở https://stqa.rbc.vn ở 1 tab. Để file `test-cases.md` mở song song trong cửa sổ khác.
   - Mở thư mục `evidence/` (tạo nếu chưa có) để lưu ảnh chụp.

2. **Reset dữ liệu** trước mỗi nhóm TC: F5 (refresh) HOẶC đăng nhập Thủ thư → nhấn 🔄 → xác nhận "Đặt lại".

3. **Chạy theo thứ tự nhóm**, không nhảy cóc — nhóm trước có thể tạo state cho nhóm sau (vd: TC-04 phải chạy trước TC-05).

4. **Ghi nhận**:
   - **Pass** → điền vắn tắt vào "Kết quả thực tế" (vd: "Đúng như mong đợi").
   - **Fail** → mô tả chi tiết hành vi sai + chụp màn hình (Win+Shift+S) lưu vào `evidence/tcXX-fail.png`.
   - Cập nhật cột **Kết luận** + **Minh chứng** + **Bug** ngay sau khi chạy xong TC đó.

5. **Sau khi chạy hết 25 TC**:
   - Đếm lại Pass/Fail/Blocked/Not Run, điền vào bảng tổng hợp.
   - Mở `bug-reports.md` viết bug report chi tiết cho mỗi Fail.
   - Mở `summary.md` viết báo cáo tổng hợp.
