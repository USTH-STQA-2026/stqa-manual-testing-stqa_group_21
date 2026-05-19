# Test Cases — Bảng trường hợp kiểm thử

| Thông tin | |
|---|---|
| **Nhóm** | `group 21` |
| **Ngày tạo** | `19/05/2026` |
| **Hệ thống** | https://stqa.rbc.vn |
| **Tham chiếu** | SRS v1.0 |

---

## Bước 1: Mô hình hóa miền đầu vào — Input Domain Modeling (IDM)

### IDM — Đăng nhập (REQ-01)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Email có tồn tại trong DB? | Có | `librarian@library.com` | Đăng nhập thành công |
| | Không | `noone@email.com` | Thông báo lỗi |
| Mật khẩu có đúng? | Đúng | `admin123` | Đăng nhập thành công |
| | Sai | `wrongpass` | Thông báo lỗi |
| Ô nhập có rỗng? | Không rỗng | (giá trị bất kỳ) | Xử lý bình thường |
| | Rỗng | `""` | Thông báo "Vui lòng nhập..." |

### IDM — xem danh sách sách (REQ-02)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Quyền truy cập | đã đăng nhập(thủ thư) | tài khoản admin | xem được toàn bộ danh sách |
|| đã đăng nhập (Thành viên) | tài khoản user | xem được toàn bộ danh sách |
|| chưa đăng nhập(guest) | N/A | Bị điều hướng đăng nhập/ từ chối truy cập |
| Cập nhập Real_time | có thay đổi trạng thái | User khác vừa mượn sách ID BOOK001 | Trạng thái BOOK001 trên màn hình lập tức đổi thành "đã mượn" |
|| Không có thay đổi | Dữ liệu tĩnh | Hiển thị bình thường |
| Hiển thị thông tin | Sách đầy đủ thông tin | Sách BOOK002 có đủ tên, tác giả, thể loại,... | Hiển thị đủ các cột thông tin theo yêu cầu |

### IDM — Tìm kiếm sách (REQ-03)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Từ khóa có tồn tại trong DB? | Có (tên sách) | `"Flutter"` | Hiển thị sách chứa "Flutter" |
| | Có (tên tác giả) | `"Nguyễn"` | Hiển thị sách của tác giả Nguyễn |
| | Không | `"XYZ123"` | Danh sách rỗng |
| Phân biệt HOA/thường? | Chữ thường | `"flutter"` | Kết quả giống "Flutter" |
| | Chữ HOA | `"FLUTTER"` | Kết quả giống "Flutter" |

### IDM — Mượn sách (REQ-04)

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
| Tình trạng sở hữu | Đang được mượn bởi chính thành viên | Chọn sách ID BOOK001(đang nằm trong danh sách mượn của tài khoản) | Hệ thống cho phép thực hiện thao tác trả sách. |
|| Không được mượn bởi thành viên(Sách của người khác hoặc đang "Có sẵn") | Chọn sách ID BOOK002(không nằm trong danh sách mượn) | Không hiện thị nút "Trả sách" hoặc báo lỗi "Bạn không mượn sách này" |
| Thời điểm trả sách(So với ngày hết hạn-DUE DATE) | Trả trước hoặc đúng hạn(Ngày trả <= Ngày hết hạn) | Trả sách trước hạn 2 ngày | Trả thành công, sách chuyển về trạng thái "có sẵn",KHÔNG có cảnh báo |
|| Trả quá hạn(Ngày trả > Ngày hết hạn) | Trả sách trễ 3 ngày so với hạn | Trả thành công, sách chuyển về trạng thái "có sẵn", hệ thống hiển thị cảnh báo quá hạn. |

### IDM — Xử lý sách quá hạn (REQ-06)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| So sánh ngày hết hạn(DueDate) và Ngày hiện tại(Now) - (được quét khi thủ thư nhấn nút) | chưa đến hạn (dueDate > Now) | Phiếu mượn có duedate là ngày mai | không thay đổi giữ nguyên trạng thái "Đang mượn" |
|| Đúng ngày hết hạn( dueDate = Now ) | Phiếu mượn có dueDate là hôm nay | Chuyển trạng thái "Quá hạn" |
|| Đã qua ngày hết hạn( dueDate < Now ) | Phiếu mượn có dueDate là hôm qua | Chuyển trạng thái sang "quá hạn" |
| Quyền kích hoạt chức năng | Thủ thư | Đăng nhập bằng tài khoản thủ thư | Hiện thị nút "Kiểm tra quá hạn", cho phép ấn và chạy cập nhật |
|| Thành viên | Đăng nhập bằng tài khoản thành viên | Nút "kiểm tra quá hạn" bị ẩn hoặc từ chối quyền truy cập |
| Phạm vi hiển thị phiếu quá hạn | Thủ thư xem danh sách | Phiếu quá hạn của User A, User B, ... | Hiển thị TOÀN BỘ danh sách các phiếu bị quá hạn của mọi thành viên |
|| Thành viên xem danh sách | Tài khoản User A đang xem | CHỉ hiện thị phiếu quá hạn cảu User A, ẩn hoàn toàn phiếu của người khác |

### IDM — Quản lý thành viên (REQ-07)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Quyền truy cập chức năng | Thủ thư(librarian) | đăng nhập bằng tải khoản thủ thư | Hiển thị nút/form "thêm thành viên" và cho phép thao tác |
|| thành viên(member) | đăng nhập bằng tài khoản thành viên | ẩn chức năng "thêm thành viên" hoặc báo lỗi "không có quyền truy cập " |
| Định dạng Email | Hợp lệ(có @ và có . trong domain) | user@domain.com | Qua bước kiểm tra định dạng, tiếp tục xử lý. |
|| Không hợp lệ(có @ nhưng thiếu . trong domain) | user@domain | chặn tạo mới, báo lỗi định dạng email không hợp lệ |
|| Không hợp lệ(thiếu @) | userdomain.com | chặn tạo mới, báo lỗi định dạng email không hợp lệ |
| Tính duy nhất của email(kiểm tra với Database) | email chưa tồn tại | newuser@gmail.com | Thêm thành viên thành công, hiển thị thông báo thành công |
|| email đã tồn tại | admin@library.com(đã có trong database) | chặn tạo mới, hiển thị thông báo lỗi "email đã tồn tại" |
| Tính đầy đủ của dữ liệu( Họ tên, email, sdt) | điền đầy đủ | Name:...,Email:..., Phone:... | Xử lý bình thường |
|| Bỏ trống thông tin | Để trống họ tên( Hoặc SĐT ) | Báo lỗi yêu cầu điền đầy đủ các trường bắt buộc |

### IDM — Tra cứu phiếu mượn (REQ-08)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Vai trò/Quyền truy cập dữ liệu | Thủ thư(librarian) | Đăng nhập tài khoản thủ thư | Hiển thị toàn bộ phiếu mượn của hệ thống( bao gồm của user A, user B,...)|
|| Thành viên(member) | Đăng nhập tài khoản User A | Chỉ hiện thị các phiếu mượn thuộc về User A. Tuyệt đối không hiển thị phiếu của User B |
| Trạng thái phiếu mượn hiển thị | Đang mượn | Phiếu mượn chưa trả và chưa tới hạn | Cột trạng thái hiển thị đúng text "Đang mượn" |
|| Đã trả | phiếu mượn đã hoàn tất trả sách | Cột trạng thái hiển thị đúng text "đã trả" |
|| Quá hạn | Phiếu mượn đã bị đánh dấu quá hạn | Cột trạng thái hiển thị đúng text "Quá hạn" |
| Tính toàn vẹn của thông tin | Có phiếu mượn trong database | Phiếu BR001 | Hiển thị đầy đủ 5 trường: Mã phiếu, Tên sách, Ngày mượn, Ngày hết hạn, Trạng thái. |
|| Không có phiếu mượn nào | User mới tạo, chưa mượn sách bao giờ | Hiển thị danh sách trống. | 




## Bước 2: Test Cases

### Nhóm 1 — Đăng nhập (REQ-01)

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-01 | kiểm tra đăng nhập thành công với tài khoản hợp lệ | Tài khoản: librarian@library.com có tồn tại trong DB, mật khẩu: admin123 | 1. Nhập email 2. Nhập mật khẩu 3. Nhấn "đăng nhập" | Email: librarian@library.com pass: admin123 | Đăng nhập thành công chuyển sang trang chủ. Thanh AppBar hiển thị 'Tên người dùng + vai trò' | REQ-01 | EP |
| TC-02 | Kiểm tra đăng nhập với email không tồn tại trong DB | Hệ thống hoạt động bình thường ở trang đăng nhập | 1. Nhập email 2. Nhập mật khẩu 3. Nhấn "đăng nhập" | Email: random@email.com Pass: 123 | Hệ thống chặn đang nhập và hiển thị thông báo lỗi: "không tìm thấy thành viên" | REQ-01| EP |
| TC-03 | Từ chối đăng nhập khi bỏ trống cả 2 ô | Đã truy cập trang đăng nhập | 1. Để trống ô Email. 2. Để trống ô Mật khẩu. 3. Nhấn **Đăng nhập**. | Email: `""`; Mật khẩu: `""` | Hiển thị thông báo "Vui lòng nhập email và mật khẩu". Không chuyển trang. | REQ-01 | EP |

### Nhóm 2 — xem danh sách sách (REQ-02)

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-04 | Xem danh sách sách thành công với tài khoản Thành viên (Luồng cơ sở) | Đăng nhập thành công với tài khoản Thành viên; hệ thống có sẵn dữ liệu sách `BOOK002` | 1. Trên thanh menu, nhấn chọn tab **Danh sách sách**. | N/A | Hệ thống hiển thị danh sách tất cả các sách. Mỗi sách hiển thị đầy đủ thông tin: Tên sách, tác giả, thể loại, năm xuất bản, trạng thái (Có sẵn / Đã mượn). | REQ-02 | BCC (Base: Thành viên + Dữ liệu tĩnh + Đầy đủ thông tin) |
| TC-05 | Xem danh sách sách thành công với tài khoản Thủ thư | Đăng nhập thành công với tài khoản Thủ thư; hệ thống có sẵn dữ liệu sách | 1. Trên thanh menu, nhấn chọn tab **Danh sách sách**. | N/A | Hệ thống hiển thị danh sách tất cả các sách với thông tin đầy đủ, tương tự như góc nhìn của Thành viên. | REQ-02 | BCC (Biến thể quyền: Thủ thư) |
| TC-06 | Chặn truy cập trang danh sách sách khi chưa đăng nhập (Guest) | Trình duyệt ở trạng thái chưa đăng nhập (Clear cache/cookies) | 1. Nhập trực tiếp URL trang danh sách sách vào thanh địa chỉ trình duyệt. 2. Nhấn **Enter**. | URL: `https://stqa.rbc.vn/books` | Hệ thống từ chối truy cập và tự động điều hướng người dùng quay về trang Đăng nhập. | REQ-02 | BCC (Biến thể quyền: Khách chưa đăng nhập) |
| TC-07 | Kiểm tra tính năng cập nhật Real-time khi sách bị mượn | Mở 2 trình duyệt: Trình duyệt 1 (User A) đang ở trang Danh sách sách. Trình duyệt 2 (User B) đã đăng nhập. Sách `BOOK001` đang "Có sẵn" | 1. Tại Trình duyệt 2 (User B), thực hiện thao tác mượn sách `BOOK001`. 2. Quan sát màn hình Trình duyệt 1 (User A) **không** tải lại trang (F5). | Sách ID: `BOOK001` | Trạng thái của sách `BOOK001` trên màn hình của User A lập tức tự động đổi từ "Có sẵn" sang "Đã mượn". | REQ-02 | BCC (Biến thể dữ liệu: Có thay đổi trạng thái realtime) |

### Nhóm 3 — Tìm kiếm sách (REQ-03)

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-08 | Tìm kiếm thành công với từ khóa là Tên sách (Luồng cơ sở) | Đã đăng nhập; hệ thống có dữ liệu sách tên "Flutter Cơ Bản" | 1. Nhập từ khóa vào ô tìm kiếm. 2. Nhấn **Enter** (hoặc nút Tìm kiếm). | Từ khóa: `Flutter` | Danh sách hiển thị các sách có chứa từ "Flutter" trong tên sách. | REQ-03 | BCC (Base: Tồn tại tên sách) |
| TC-09 | Tìm kiếm với từ khóa không tồn tại trong hệ thống | Đã đăng nhập; hệ thống không có sách hoặc tác giả nào chứa chuỗi "XYZ123" | 1. Nhập từ khóa vào ô tìm kiếm. 2. Nhấn **Enter** (hoặc nút Tìm kiếm). | Từ khóa: `XYZ123` | Danh sách hiển thị rỗng. Xuất hiện thông báo: "Không tìm thấy sách". | REQ-03 | BCC (Biến thể dữ liệu: Không tồn tại) / EP |
| TC-10 | Kiểm tra tìm kiếm không phân biệt chữ hoa/thường (Nhập chữ thường) | Đã đăng nhập; hệ thống có dữ liệu sách tên "Flutter Cơ Bản" | 1. Nhập từ khóa in thường vào ô tìm kiếm. 2. Nhấn **Enter**. | Từ khóa: `flutter` | Kết quả hiển thị sách "Flutter Cơ Bản" (giống hệt như khi nhập "Flutter"). | REQ-03 | EP (Phân vùng: Chữ thường) |
| TC-11 | Lọc sách theo thể loại | Đã đăng nhập; hệ thống có các sách thuộc nhiều thể loại khác nhau (VD: "IT", "Kinh tế") | 1. Nhấn vào Dropdown/Bộ lọc Thể loại. 2. Chọn thể loại "IT". | Thể loại: `IT` | Danh sách chỉ hiển thị những cuốn sách thuộc thể loại "IT", ẩn các sách thuộc thể loại khác. | REQ-03 | EP (Phân vùng: Lọc theo Category) |

### Nhóm 4 — Mượn sách (REQ-04)

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-12 | Mượn sách thành công (Luồng cơ sở / Happy path) | Thành viên `MEM006` đang Hoạt động, chưa mượn sách nào (0 cuốn). Sách `BOOK001` trạng thái "Có sẵn". | 1. Chọn sách `BOOK001`. 2. Nhấn nút **Mượn sách**. | Tài khoản: `MEM006`, Sách: `BOOK001` | Mượn thành công. Trạng thái sách cập nhật thành "Đã mượn". Hệ thống ghi nhận hạn trả là 14 ngày kể từ ngày hiện tại. | REQ-04 | BCC (Base: Hoạt động + Có sẵn + < 3 sách) / BVA (Biên 0) |
| TC-13 | Mượn sách khi sách không ở trạng thái "Có sẵn" (Đang mượn) | Thành viên Hoạt động, < 3 sách. Sách `BOOK003` trạng thái "Đang mượn". | 1. Truy cập chi tiết sách `BOOK003`. 2. Thực hiện thao tác mượn sách. | Sách: `BOOK003` | Không thể mượn. Nút mượn bị vô hiệu hóa (disabled) HOẶC hiển thị thông báo lỗi "Sách đã được mượn". | REQ-04 | BCC (Biến thể Book Status: Đang mượn) |
| TC-14 | Mượn sách khi tài khoản thành viên bị "Tạm ngưng" | Thành viên `MEM004` trạng thái "Tạm ngưng", < 3 sách. Sách trạng thái "Có sẵn". | 1. Chọn sách "Có sẵn". 2. Nhấn nút **Mượn sách**. | Tài khoản: `MEM004` | Từ chối mượn. Hiển thị chính xác thông báo lỗi: "Tài khoản đang bị tạm ngưng". | REQ-04 | BCC (Biến thể Member Status: Tạm ngưng) |
| TC-15 | Từ chối mượn sách khi đã đạt giới hạn 3 sách | Thành viên Hoạt động. Đang mượn chính xác 3 cuốn sách. Sách mượn mới trạng thái "Có sẵn". | 1. Chọn sách "Có sẵn". 2. Nhấn nút **Mượn sách**. | Số lượng đang mượn: `3` | Từ chối mượn. Hệ thống hiển thị thông báo lỗi: "Thành viên đã đạt giới hạn 3 sách". | REQ-04 | BVA (Tại biên giới hạn tối đa) |

### Nhóm 5 — Trả sách (REQ-05)

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-16 | Trả sách thành công trước ngày hết hạn (Luồng cơ sở) | Đã đăng nhập. Thành viên đang mượn sách `BOOK001`. Ngày hiện tại **nhỏ hơn** ngày hết hạn (trước hạn). | 1. Vào danh sách sách đang mượn. 2. Chọn sách `BOOK001`. 3. Nhấn nút **Trả sách**. | Sách ID: `BOOK001` | Trả sách thành công. Trạng thái sách cập nhật thành "Có sẵn". KHÔNG hiển thị cảnh báo quá hạn. | REQ-05 | BCC (Base: Đang mượn + Trước hạn) / EP |
| TC-17 | Trả sách thành công đúng ngày hết hạn (Kiểm tra biên) | Đã đăng nhập. Thành viên đang mượn sách `BOOK001`. Ngày hiện tại **bằng đúng** ngày hết hạn (Due Date). | 1. Vào danh sách sách đang mượn. 2. Chọn sách `BOOK001`. 3. Nhấn nút **Trả sách**. | Sách ID: `BOOK001` | Trả sách thành công. Trạng thái sách cập nhật thành "Có sẵn". KHÔNG hiển thị cảnh báo quá hạn. | REQ-05 | BVA (Tại ranh giới `<=`) |
| TC-18 | Trả sách khi đã quá hạn | Đã đăng nhập. Thành viên đang mượn sách `BOOK001`. Ngày hiện tại **lớn hơn** ngày hết hạn (trễ hạn). | 1. Vào danh sách sách đang mượn. 2. Chọn sách `BOOK001`. 3. Nhấn nút **Trả sách**. | Sách ID: `BOOK001` | Trả sách thành công. Trạng thái sách cập nhật thành "Có sẵn". Hệ thống **hiển thị cảnh báo quá hạn**. | REQ-05 | EP (Phân vùng: Quá hạn) / BVA (Vượt biên `>`) |
| TC-19 | Từ chối trả sách không thuộc quyền sở hữu (Sách người khác mượn hoặc Có sẵn) | Đã đăng nhập. Thành viên KHÔNG mượn sách `BOOK002` (sách này đang "Có sẵn" hoặc do người khác mượn). | 1. Truy cập vào chi tiết sách `BOOK002`. 2. Tìm cách thực hiện thao tác trả sách. | Sách ID: `BOOK002` | Nút "Trả sách" bị ẩn (không hiển thị) HOẶC nếu cố gọi API/thao tác, hệ thống báo lỗi: "Bạn không mượn sách này". | REQ-05 | BCC (Biến thể quyền sở hữu: Không mượn) |

### Nhóm 6 — Xử lý sách quá hạn (REQ-06)

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-20 | Đánh dấu quá hạn đúng ngày hết hạn (Kiểm tra biên `=` Now) | Đăng nhập Thủ thư. Có phiếu mượn `PM001` với `dueDate` là **hôm nay**. | 1. Vào trang Quản lý mượn trả. 2. Nhấn nút **Kiểm tra quá hạn**. | Click button | Hệ thống quét thành công, trạng thái phiếu `PM001` cập nhật thành "Quá hạn". | REQ-06 | BVA (Tại biên `dueDate = Now`) |
| TC-21 | Không đánh dấu quá hạn khi chưa đến hạn (`>` Now) | Đăng nhập Thủ thư. Có phiếu mượn `PM003` với `dueDate` là **ngày mai**. | 1. Nhấn nút **Kiểm tra quá hạn**. | Click button | Trạng thái phiếu `PM003` không thay đổi, vẫn giữ là "Đang mượn". | REQ-06 | EP / BVA (Biên ngoài `dueDate > Now`) |
| TC-22 | Kiểm tra phạm vi hiển thị danh sách quá hạn của Thành viên | Đăng nhập tài khoản `User A`. Trong DB có phiếu quá hạn của `User A` và `User B`. | 1. Mở trang xem phiếu mượn của tôi. 2. Lọc/xem các phiếu quá hạn. | N/A | Danh sách CHỈ hiển thị phiếu quá hạn của `User A`. Ẩn hoàn toàn (không xem được) phiếu của `User B`. | REQ-06 | EP (Vai trò xem: Thành viên) |

### Nhóm 7 — Quản lý thành viên (REQ-07)

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-23 | Thêm thành viên mới thành công (Luồng cơ sở) | Đăng nhập Thủ thư. Email `newuser@gmail.com` chưa tồn tại trong DB. | 1. Mở trang Quản lý thành viên. 2. Chọn "Thêm thành viên". 3. Nhập đầy đủ thông tin hợp lệ. 4. Nhấn **Lưu/Thêm**. | Họ tên: `Nguyễn Văn A`<br>Email: `newuser@gmail.com`<br>SĐT: `0901234567` | Thêm thành viên thành công, hiển thị thông báo thành công. Thành viên mới xuất hiện trong danh sách. | REQ-07 | BCC (Base: Thủ thư + Hợp lệ + Chưa tồn tại + Điền đủ) |
| TC-24 | Thêm thành viên với định dạng email không hợp lệ (Thiếu dấu `.` trong domain) | Đăng nhập Thủ thư. Mở form Thêm thành viên. | 1. Nhập thông tin. 2. Nhập email có `@` nhưng thiếu dấu `.`. 3. Nhấn **Thêm**. | Họ tên: `Nguyễn A`<br>Email: `user@domain`<br>SĐT: `0901234567` | Chặn tạo mới. Hệ thống báo lỗi định dạng email không hợp lệ. | REQ-07 | BCC (Biến thể Email: Sai định dạng) / EP |
| TC-25 | Thêm thành viên với email đã tồn tại trong cơ sở dữ liệu | Đăng nhập Thủ thư. Email `admin@library.com` đã tồn tại. | 1. Nhập thông tin. 2. Nhập email đã tồn tại. 3. Nhấn **Thêm**. | Họ tên: `Nguyễn B`<br>Email: `admin@library.com`<br>SĐT: `0807654321` | Chặn tạo mới. Hệ thống hiển thị thông báo lỗi: "Email đã tồn tại". | REQ-07 | BCC (Biến thể Uniqueness: Đã tồn tại) / EP |

### Nhóm 8 — Tra cứu phiếu mượn (REQ-08)

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-26 | Thủ thư xem toàn bộ danh sách phiếu mượn với đầy đủ thông tin | Đăng nhập tài khoản Thủ thư. DB có phiếu mượn của `User A`, `User B` ở nhiều trạng thái (Đang mượn, Đã trả, Quá hạn). | 1. Mở trang Tra cứu phiếu mượn. | N/A | Hiển thị TOÀN BỘ phiếu mượn của hệ thống. Mỗi phiếu hiển thị đầy đủ 5 trường: Mã phiếu, Tên sách, Ngày mượn, Ngày hết hạn, Trạng thái (text trạng thái hiển thị chuẩn xác). | REQ-08 | BCC (Base: Thủ thư + Có dữ liệu) / EP |
| TC-27 | Kiểm tra quyền Data Isolation của Thành viên (Chỉ xem phiếu của mình) | Đăng nhập tài khoản `User A`. DB có phiếu mượn `BR001` của `User A` và `BR002` của `User B`. | 1. Mở trang Tra cứu phiếu mượn (Phiếu mượn của tôi). | N/A | Danh sách CHỈ hiển thị phiếu `BR001` thuộc về `User A` với đủ 5 trường thông tin. TUYỆT ĐỐI KHÔNG hiển thị phiếu `BR002` của `User B`. | REQ-08 | EP (Phân vùng: Thành viên) |
| TC-28 | Kiểm tra hiển thị khi Thành viên chưa có phiếu mượn nào (Empty state) | Đăng nhập tài khoản Thành viên mới tạo. Thành viên này chưa từng mượn cuốn sách nào (DB trống đối với user này). | 1. Mở trang Tra cứu phiếu mượn. | N/A | Màn hình hiển thị danh sách trống (VD: "Bạn chưa có phiếu mượn nào"). Hệ thống không bị lỗi (crash) khi thiếu dữ liệu. | REQ-08 | BCC (Biến thể Data: Không có phiếu) |

## Tổng hợp

| Nhóm chức năng | Số TC | REQ phủ | Kỹ thuật IDM áp dụng |
|----------------|-------|---------|----------------------|
| Nhóm 1 — Đăng nhập | 3 | REQ-01 | EP |
| Nhóm 2 — Xem danh sách sách | 4 | REQ-02 | BCC |
| Nhóm 3 — Tìm kiếm sách | 4 | REQ-03 | BCC, EP |
| Nhóm 4 — Mượn sách | 4 | REQ-04 | BCC, BVA |
| Nhóm 5 — Trả sách | 4 | REQ-05 | BCC, EP, BVA |
| Nhóm 6 — Xử lý sách quá hạn | 3 | REQ-06 | EP, BVA |
| Nhóm 7 — Quản lý thành viên | 3 | REQ-07 | BCC, EP |
| Nhóm 8 — Tra cứu phiếu mượn | 3 | REQ-08 | BCC, EP |
| **Tổng** | **28** | **REQ-01 → REQ-08** | **EP, BVA, BCC** |
