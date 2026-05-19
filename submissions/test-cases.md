# Test Cases — Bảng trường hợp kiểm thử

> **Hướng dẫn**: Viết tối thiểu **20 TC** phủ đủ các chức năng chính (REQ-01 → REQ-08).
> Xem [examples/sample-test-case.md](../examples/sample-test-case.md) để hiểu cách viết TC tốt.
> Tự tổ chức và phân nhóm test case theo cách hợp lý nhất.

| Thông tin | |
|---|---|
| **Nhóm** | Nhóm 21 |
| **Ngày tạo** | 19/05/2026 |
| **Hệ thống** | https://stqa.rbc.vn |
| **Tham chiếu** | SRS v1.0 |

---

## Bước 1: Mô hình hóa miền đầu vào — Input Domain Modeling (IDM)

> 📖 **Textbook:** Chương 6 — *Input Domain Modeling*, Paul Ammann & Jeff Offutt.
>
> **Trước khi viết Test Case**, nhóm **phải** phân tích miền đầu vào bằng bảng IDM bên dưới.
> Mỗi chức năng cần xác định: **Đặc tính (Characteristic)**, **Phân vùng (Block/Partition)**, và **Giá trị đại diện (Value)**.

### IDM — Đăng nhập (REQ-01)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Email có tồn tại trong DB? | Có | `librarian@library.com` | Đăng nhập thành công |
| | Không | `noone@email.com` | Thông báo lỗi |
| Mật khẩu có đúng? | Đúng | `admin123` | Đăng nhập thành công |
| | Sai | `wrongpass` | Thông báo lỗi |
| Ô nhập có rỗng? | Không rỗng | (giá trị bất kỳ) | Xử lý bình thường |
| | Rỗng | `""` | Thông báo "Vui lòng nhập..." |

### IDM — Tìm kiếm sách (REQ-03)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Từ khóa có tồn tại trong DB? | Có (tên sách) | `"Flutter"` | Hiển thị sách chứa "Flutter" |
| | Có (tên tác giả) | `"Nguyễn"` | Hiển thị sách của tác giả Nguyễn |
| | Không | `"XYZ123"` | Danh sách rỗng |
| Phân biệt HOA/thường? | Chữ thường | `"flutter"` | Kết quả giống "Flutter" |
| | Chữ HOA | `"FLUTTER"` | Kết quả giống "Flutter" |

### IDM — Mượn sách (REQ-04, REQ-05)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Trạng thái sách? | Có sẵn | BOOK001 | Cho phép mượn |
| | Đang mượn | BOOK003 | Không cho phép |
| | Thất lạc | BOOK007 | Không cho phép |
| Trạng thái thành viên? | Hoạt động | MEM002 | Cho phép mượn |
| | Tạm ngưng | MEM004 | Từ chối, thông báo lỗi |
| | Hết hạn | MEM005 | Từ chối, thông báo lỗi |
| Số sách đang mượn? | < 3 (BVA: 0, 1, 2) | MEM006 (0 sách) | Cho phép mượn |
| | = 3 (BVA: giới hạn) | MEM đã mượn 3 sách | Từ chối, thông báo vượt giới hạn |

### IDM — Trả sách (REQ-05)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Sách có nằm trong danh sách đang mượn của thành viên? | Có | BR001 (MEM002 đang mượn BOOK003) | Cho phép trả, sách chuyển về "Có sẵn" |
| | Không | Phiếu của người khác | Không cho phép trả |
| Thời điểm trả so với `dueDate`? | Trước hạn (BVA: dueDate − 1) | Trả sớm 1 ngày | Trả thành công, không cảnh báo |
| | Đúng hạn (BVA: dueDate) | Trả đúng ngày hết hạn | Trả thành công, không cảnh báo |
| | Quá hạn (BVA: dueDate + 1) | BR001 (quá hạn thực tế) | Trả thành công + **cảnh báo quá hạn** |

### IDM — Xử lý quá hạn (REQ-06)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Vai trò người dùng? | Thủ thư | LIB001 | Thấy nút "Kiểm tra quá hạn", kích hoạt được |
| | Thành viên | MEM002 | Không thấy / không truy cập được nút |
| `dueDate` so với ngày hiện tại? | dueDate < hôm nay | BR001 (15/09/2024) | Đánh dấu "Quá hạn" |
| | dueDate = hôm nay (BVA) | Phiếu có dueDate trùng hôm nay | Đánh dấu "Quá hạn" (theo SRS: ≤ ngày hiện tại) |
| | dueDate > hôm nay | BR003 (15/10/2024) | Giữ trạng thái "Đang mượn" |
| Trạng thái hiện tại của phiếu? | Đang mượn | BR001 | Có thể chuyển thành "Quá hạn" |
| | Đã trả | BR002 | Không thay đổi (đã đóng) |

### IDM — Quản lý thành viên — Thêm thành viên (REQ-07)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Vai trò người dùng? | Thủ thư | LIB001 | Truy cập được form Thêm thành viên |
| | Thành viên | MEM002 | Không thấy tab/form |
| Định dạng email? | Có `@` và có `.` trong domain | `new.user@email.com` | Hợp lệ |
| | Có `@` nhưng KHÔNG có `.` trong domain | `new.user@email` | Không hợp lệ → thông báo lỗi |
| | Không có `@` | `new.user.email.com` | Không hợp lệ → thông báo lỗi |
| Email đã tồn tại? | Đã tồn tại | `librarian@library.com` | Báo lỗi trùng email |
| | Chưa tồn tại | `new.user@email.com` | Tạo thành công |
| Họ tên? | Có giá trị | `Nguyễn Văn A` | Hợp lệ |
| | Rỗng | `""` | Báo lỗi yêu cầu nhập họ tên |

### IDM — Tra cứu phiếu mượn (REQ-08)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Vai trò người dùng? | Thủ thư | LIB001 | Xem được phiếu của **mọi** thành viên |
| | Thành viên | MEM002 | Chỉ xem được phiếu của **chính mình** |
| Mã thành viên tra cứu? | Của chính mình | MEM002 tra cứu MEM002 | Hiển thị phiếu mượn |
| | Của thành viên khác | MEM002 tra cứu MEM003 | **Từ chối** (vi phạm quyền) |
| | Mã không tồn tại | `MEM999` | Thông báo không tìm thấy |

> 💡 **Gợi ý kỹ thuật**: Sử dụng **Phân lớp tương đương (EP)** cho các phân vùng rời rạc, **Phân tích giá trị biên (BVA)** cho các phân vùng số (ví dụ: giới hạn 3 sách). Xem textbook §6.1–6.3.

---

## Bước 2: Test Cases

<!-- Tự tổ chức bảng test case: có thể chia nhóm theo chức năng, theo REQ, hoặc theo luồng nghiệp vụ — tùy nhóm quyết định. -->
<!-- Mỗi TC phải ánh xạ ngược về ít nhất 1 dòng trong bảng IDM ở Bước 1. -->

### Nhóm 1 — Đăng nhập (REQ-01)

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-01 | Đăng nhập thành công với tài khoản Thủ thư hợp lệ | Đã truy cập https://stqa.rbc.vn; dữ liệu seed nguyên trạng | 1. Nhập email vào ô Email. 2. Nhập mật khẩu vào ô Mật khẩu. 3. Nhấn nút **Đăng nhập**. | Email: `librarian@library.com`; Mật khẩu: `admin123` | Hệ thống chuyển sang trang chủ. AppBar hiển thị tên "Nguyễn Thủ Thư" và vai trò "Thủ thư". | REQ-01 | EP (lớp email tồn tại + mật khẩu đúng) |
| TC-02 | Từ chối đăng nhập khi email không tồn tại | Đã truy cập trang đăng nhập | 1. Nhập email. 2. Nhập mật khẩu. 3. Nhấn **Đăng nhập**. | Email: `nobody@test.com`; Mật khẩu: `anything` | Hiển thị thông báo "Không tìm thấy thành viên". Không chuyển trang. | REQ-01 | EP (lớp email không tồn tại) |
| TC-03 | Từ chối đăng nhập khi bỏ trống cả 2 ô | Đã truy cập trang đăng nhập | 1. Để trống ô Email. 2. Để trống ô Mật khẩu. 3. Nhấn **Đăng nhập**. | Email: `""`; Mật khẩu: `""` | Hiển thị thông báo "Vui lòng nhập email và mật khẩu". Không chuyển trang. | REQ-01 | EP (lớp input rỗng) |

### Nhóm 2 — Mượn sách (REQ-04) — minh họa BVA & Decision Table

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-04 | Cho phép mượn khi đã mượn 2 sách (dưới giới hạn) | Đăng nhập MEM006 (đang mượn BOOK013 = 1 sách); dữ liệu seed | 1. Mở tab Sách. 2. Chọn BOOK001 → nhấn **Mượn**. 3. Quay lại tab Sách, chọn BOOK002 → nhấn **Mượn**. | Người mượn: MEM006; Sách: BOOK001, BOOK002 | Cả 2 thao tác mượn đều thành công. Sau TC, MEM006 có 3 sách (đạt giới hạn). | REQ-04 | BVA (biên dưới: 2 → 3, vẫn ≤ 3) |
| TC-05 | Từ chối mượn cuốn thứ 4 khi đã đạt giới hạn 3 sách | Đăng nhập MEM006, đã mượn đủ 3 sách (BOOK013, BOOK001, BOOK002) — tiếp nối TC-04 | 1. Mở tab Sách. 2. Chọn BOOK004 → nhấn **Mượn**. | Người mượn: MEM006; Sách: BOOK004 | Hiển thị thông báo lỗi vượt giới hạn 3 sách. Không tạo phiếu mượn mới. | REQ-04 | BVA (biên trên: 3 → 4, vượt giới hạn) |
| TC-06 | Từ chối mượn cho thành viên Tạm ngưng | Đăng nhập Thủ thư; dữ liệu seed | 1. Vào chức năng Mượn sách. 2. Chọn thành viên MEM004 (Lê Cần Cù — Tạm ngưng). 3. Chọn sách BOOK001. 4. Xác nhận mượn. | Thành viên: MEM004 (Tạm ngưng); Sách: BOOK001 (Có sẵn) | Hệ thống từ chối, hiển thị thông báo **đúng lý do** "tạm ngưng" (không phải "hết hạn"). | REQ-04 | Decision Table (Sách=Có sẵn × TV=Tạm ngưng → Từ chối) |
| TC-07 | Từ chối mượn cho thành viên Hết hạn | Đăng nhập Thủ thư; dữ liệu seed | 1. Vào chức năng Mượn sách. 2. Chọn MEM005 (Phạm Trung Bình — Hết hạn). 3. Chọn BOOK001. 4. Xác nhận. | Thành viên: MEM005 (Hết hạn); Sách: BOOK001 | Từ chối với thông báo **đúng lý do** "hết hạn". | REQ-04 | Decision Table (Sách=Có sẵn × TV=Hết hạn → Từ chối) |

> 📌 **Giải thích cho TC-06 + TC-07**: SRS yêu cầu thông báo lỗi phải **đúng lý do** (tạm ngưng ≠ hết hạn). Đây là lý do phải tách 2 TC riêng — nếu hệ thống chỉ trả về 1 thông báo chung "Không thể mượn" thì cả 2 TC đều Fail.

### Nhóm 3 — Xem danh sách sách (REQ-02)

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-08 | Hiển thị đầy đủ thông tin sách theo SRS | Đã đăng nhập MEM002; dữ liệu seed nguyên trạng | 1. Mở tab **Sách**. 2. Quan sát danh sách hiển thị. 3. Kiểm tra dòng BOOK001. | Người dùng: MEM002 (Thành viên) | Hiển thị ít nhất 20 sách. Dòng BOOK001 có đủ 5 trường: tên "Lập trình Flutter cơ bản", tác giả "Nguyễn Minh Đức", thể loại "Công nghệ", năm 2023, trạng thái "Có sẵn". | REQ-02 | EP |
| TC-09 | Cập nhật trạng thái sách real-time khi mượn | Đăng nhập MEM006 (đang mượn 1 sách); BOOK001 đang "Có sẵn" | 1. Mở tab **Sách**, ghi nhận BOOK001 đang "Có sẵn". 2. Nhấn **Mượn** BOOK001 và xác nhận. 3. Không refresh, quay lại tab **Sách**. | Sách: BOOK001 | Trạng thái BOOK001 đổi sang "Đã mượn" **ngay lập tức** (không cần refresh). | REQ-02 | EP |

### Nhóm 4 — Tìm kiếm & lọc sách (REQ-03)

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-10 | Tìm sách theo tên — kết quả chính xác | Đăng nhập bất kỳ; dữ liệu seed | 1. Mở tab **Sách**. 2. Nhập từ khóa vào ô tìm kiếm. | Từ khóa: `Flutter` | Danh sách chỉ hiển thị BOOK001 ("Lập trình Flutter cơ bản"). | REQ-03 | EP (lớp từ khóa tồn tại trong tên sách) |
| TC-11 | Tìm sách không phân biệt HOA/thường (case-insensitive) | Đăng nhập bất kỳ; dữ liệu seed | 1. Nhập từ khóa CHỮ HOA vào ô tìm kiếm. | Từ khóa: `FLUTTER` | Kết quả **giống TC-10** — vẫn hiển thị BOOK001. | REQ-03 | EP (so sánh với TC-10) |
| TC-12 | Tìm sách theo tên tác giả | Đăng nhập bất kỳ; dữ liệu seed | 1. Nhập từ khóa vào ô tìm kiếm. | Từ khóa: `Nguyễn Minh Đức` | Hiển thị BOOK001 và BOOK009 (cùng tác giả "Nguyễn Minh Đức"). | REQ-03 | EP (lớp từ khóa = tên tác giả) |
| TC-13 | Tìm sách không có kết quả | Đăng nhập bất kỳ; dữ liệu seed | 1. Nhập từ khóa không tồn tại. | Từ khóa: `XYZ123` | Hiển thị thông báo "Không tìm thấy sách". Danh sách rỗng. | REQ-03 | EP (lớp từ khóa không tồn tại) |
| TC-14 | Lọc sách theo thể loại | Đăng nhập bất kỳ; dữ liệu seed | 1. Mở tab **Sách**. 2. Chọn bộ lọc thể loại = "Quản trị". | Thể loại: `Quản trị` | Chỉ hiển thị 3 sách: BOOK004, BOOK012, BOOK013. | REQ-03 | EP (phân vùng thể loại) |

### Nhóm 5 — Trả sách (REQ-05)

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-15 | Trả sách đang mượn — chuyển về "Có sẵn" | Đăng nhập MEM006 (đang mượn BOOK013 — phiếu BR003) | 1. Mở tab **Mượn/Trả**. 2. Tìm phiếu BR003. 3. Nhấn **Trả sách**. 4. Mở tab **Sách**, kiểm tra BOOK013. | Phiếu: BR003 | Phiếu BR003 chuyển "Đã trả". BOOK013 chuyển "Có sẵn" trong tab Sách. | REQ-05 | EP |
| TC-16 | Cảnh báo khi trả sách quá hạn | Đăng nhập MEM002 (đang mượn BOOK003 — phiếu BR001, dueDate 15/09/2024 đã quá) | 1. Mở tab **Mượn/Trả**. 2. Tìm phiếu BR001. 3. Nhấn **Trả sách**. | Phiếu: BR001 (đã quá hạn) | Trả thành công nhưng hệ thống **phải hiển thị cảnh báo quá hạn** (theo SRS REQ-05). | REQ-05 | BVA (trả sau dueDate) |

### Nhóm 6 — Xử lý sách quá hạn (REQ-06)

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-17 | Thủ thư kích hoạt "Kiểm tra quá hạn" — đánh dấu phiếu quá hạn | Đăng nhập Thủ thư (`librarian@library.com`); dữ liệu seed (BR001 dueDate 15/09/2024, BR003 dueDate 15/10/2024 — cả 2 đều đã qua) | 1. Mở tab **Mượn/Trả**. 2. Nhấn nút **Kiểm tra quá hạn**. 3. Xem lại danh sách phiếu. | - | BR001 và BR003 chuyển trạng thái "Quá hạn". BR002, BR004, BR005 (đã trả) không thay đổi. | REQ-06 | EP (dueDate ≤ hôm nay) |
| TC-18 | Thành viên KHÔNG có quyền truy cập nút "Kiểm tra quá hạn" | Đăng nhập MEM002; dữ liệu seed | 1. Mở tab **Mượn/Trả**. 2. Quan sát thanh công cụ. | Người dùng: MEM002 | Không hiển thị nút "Kiểm tra quá hạn" cho Thành viên (chỉ Thủ thư mới có — theo SRS mục 4.2). | REQ-06 | EP (quyền truy cập) |

### Nhóm 7 — Quản lý thành viên — Thêm thành viên (REQ-07)

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-19 | Thêm thành viên với dữ liệu hợp lệ | Đăng nhập Thủ thư; dữ liệu seed | 1. Mở tab **Thành viên**. 2. Nhấn **Thêm thành viên**. 3. Điền form. 4. Nhấn **Lưu**. | Họ tên: `Nguyễn Văn Thử`; Email: `thunv@email.com`; SĐT: `0901234567` | Hiển thị thông báo thành công. Thành viên mới xuất hiện trong danh sách. | REQ-07 | EP (lớp dữ liệu hợp lệ) |
| TC-20 | Từ chối email không có dấu `.` trong domain | Đăng nhập Thủ thư; mở form thêm thành viên | 1. Điền form với email không có `.` ở domain. 2. Nhấn **Lưu**. | Họ tên: `Nguyễn Văn Thử`; Email: `thunv@email`; SĐT: `0901234567` | Hệ thống hiển thị lỗi định dạng email không hợp lệ. **Không** tạo thành viên. | REQ-07 | EP (biên định dạng email — SRS yêu cầu phải có `@` VÀ có `.` trong domain) |
| TC-21 | Từ chối email trùng với thành viên đã tồn tại | Đăng nhập Thủ thư; mở form thêm thành viên | 1. Điền form với email đã tồn tại. 2. Nhấn **Lưu**. | Họ tên: `Test Trùng`; Email: `librarian@library.com`; SĐT: `0901112233` | Hiển thị thông báo email đã tồn tại. **Không** tạo thành viên. | REQ-07 | EP (lớp email trùng) |
| TC-22 | Từ chối khi họ tên bỏ trống | Đăng nhập Thủ thư; mở form thêm thành viên | 1. Để trống ô Họ tên. 2. Điền email và SĐT hợp lệ. 3. Nhấn **Lưu**. | Họ tên: `""`; Email: `valid@email.com`; SĐT: `0901234567` | Hiển thị thông báo yêu cầu nhập họ tên. **Không** tạo thành viên. | REQ-07 | EP (lớp input rỗng) |

### Nhóm 8 — Tra cứu phiếu mượn (REQ-08)

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-23 | Thành viên xem được phiếu mượn của chính mình | Đăng nhập MEM002 (`ba.nguyen@email.com`); dữ liệu seed (MEM002 có BR001, BR004) | 1. Mở tab **Mượn/Trả**. 2. Quan sát danh sách phiếu cá nhân. | Người dùng: MEM002 | Hiển thị 2 phiếu BR001 (đang mượn BOOK003) và BR004 (đã trả BOOK005) — tổng 2 phiếu của MEM002. | REQ-08 | EP (lớp tra cứu phiếu của mình) |
| TC-24 | Thành viên KHÔNG xem được phiếu của thành viên khác | Đăng nhập MEM002; dữ liệu seed | 1. Mở tab **Mượn/Trả**. 2. Cố gắng nhập mã `MEM006` vào ô tra cứu (nếu UI có). 3. Quan sát kết quả. | Người tra cứu: MEM002; Mã tra cứu: MEM006 | Hệ thống **TỪ CHỐI** hiển thị phiếu của MEM006 (theo SRS: "Thành viên KHÔNG được xem phiếu mượn của thành viên khác"). | REQ-08 | Decision Table (Vai trò=Thành viên × Mã tra cứu=Của người khác → Từ chối) |
| TC-25 | Thủ thư xem được phiếu mượn của tất cả thành viên | Đăng nhập Thủ thư; dữ liệu seed (tổng 5 phiếu BR001-BR005) | 1. Mở tab **Mượn/Trả**. 2. Quan sát danh sách phiếu tổng. 3. Tra cứu lần lượt MEM002, MEM003, MEM006. | Người dùng: LIB001 (Thủ thư) | Hiển thị đủ cả 5 phiếu (BR001-BR005). Tra cứu theo từng MEM ra đúng phiếu của người đó. | REQ-08 | EP (lớp Thủ thư — quyền toàn phần) |

---

## Tổng hợp

| Nhóm chức năng | Số TC | REQ phủ | Kỹ thuật IDM áp dụng |
|----------------|-------|---------|----------------------|
| Đăng nhập | 3 (TC-01 → TC-03) | REQ-01 | EP |
| Mượn sách | 4 (TC-04 → TC-07) | REQ-04 | BVA, Decision Table |
| Xem danh sách sách | 2 (TC-08, TC-09) | REQ-02 | EP |
| Tìm kiếm & lọc | 5 (TC-10 → TC-14) | REQ-03 | EP |
| Trả sách | 2 (TC-15, TC-16) | REQ-05 | EP, BVA |
| Xử lý quá hạn | 2 (TC-17, TC-18) | REQ-06 | EP |
| Thêm thành viên | 4 (TC-19 → TC-22) | REQ-07 | EP |
| Tra cứu phiếu mượn | 3 (TC-23 → TC-25) | REQ-08 | EP, Decision Table |
| **Tổng** | **25** | REQ-01 → REQ-08 (đủ 8/8) | EP + BVA + Decision Table |
