# Group Exercises — Bài tập nhóm thảo luận

> **Nhóm**: 21
> **Ngày làm**: 26/05/2026
> **Tham chiếu**: `docs/group-exercises.md`, `submissions/test-cases.md`, `submissions/bug-reports.md`
> **Textbook**: Ammann & Offutt, *Introduction to Software Testing*, 2nd Edition

---

## Bài tập 1 — Cuộc chiến của những chiếc hộp (The "Box" Debate)

### 1.1. Bảng 6 giá trị test cho tính năng "Mượn sách"

| # | Nguồn gốc | Test Value (Mô tả) | Dữ liệu cụ thể |
|---|---|---|---|
| 1 | **SRS (Black-box)** | Thành viên đã mượn đủ 3 sách thử mượn cuốn thứ 4 — SRS REQ-04 ghi rõ "Tối đa 3 sách / thành viên" | `MEM006` đã mượn `BOOK013, BOOK001, BOOK002` → thử mượn `BOOK004` |
| 2 | **SRS (Black-box)** | Thành viên có trạng thái "Tạm ngưng" thử mượn sách — SRS REQ-04 yêu cầu thông báo lỗi **đúng lý do** (tạm ngưng ≠ hết hạn) | `MEM004` (Tạm ngưng) thử mượn `BOOK001` |
| 3 | **SRS (Black-box)** | Mượn sách đang có trạng thái "Đã mượn" — SRS quy định mỗi đầu sách chỉ có 1 bản | `MEM003` thử mượn `BOOK003` (đang được `MEM002` mượn) |
| 4 | **Code (White-box)** | `memberId` không tồn tại trong hệ thống — code có `if (member == null) return ...` mà **SRS không nói rõ** | `memberId = "MEM999"` (không tồn tại trong DB) |
| 5 | **Code (White-box)** | `bookId` không tồn tại — code có `if (book == null) return ...` riêng | `bookId = "BOOK999"` (không tồn tại trong DB) |
| 6 | **Code (White-box)** | Test **thứ tự ưu tiên** thông báo lỗi: thành viên vừa Hết hạn vừa đã mượn 3 sách → code check `expired` trước nên trả về "hết hạn" (không phải "vượt giới hạn") | `MEM005` (Hết hạn, giả lập đã mượn 3 sách) → thử mượn cuốn 4 |

### 1.2. Câu hỏi thảo luận

**a. Test từ SRS và Code có khác nhau không? Trùng nhau không?**

- **Trùng nhau**: Cả hai cùng test 3 điều kiện reject chính (sách đã mượn, thành viên tạm ngưng/hết hạn, vượt giới hạn 3 sách). Đó là vì code được implement theo SRS.
- **Khác nhau**:
  - Code thêm `null check` cho member và book — **SRS không hề đề cập** "nếu memberId/bookId không tồn tại". Nhóm test theo SRS sẽ bỏ sót case này.
  - Code cho biết **thứ tự** kiểm tra: `expired` → `suspended` → `book null` → `book not available` → `count >= 3`. Nếu một thành viên vi phạm nhiều điều kiện cùng lúc, **lý do đầu tiên trong code** mới được hiển thị. SRS không nói rõ thứ tự ưu tiên.

**b. Tại sao hỏi "Test là Black-box hay White-box?" là câu hỏi sai?**

- Vì cả SRS lẫn code đều là **model** của hệ thống, chỉ khác **mức trừu tượng**:
  - SRS = model **mức cao** (mô tả nghiệp vụ, không quan tâm triển khai).
  - Code = model **mức thấp** (mô tả chính xác cách máy chạy).
- Câu hỏi đúng phải là: ***"Test này được thiết kế từ model nào, ở mức trừu tượng nào?"***
- Ví dụ test BVA cho giới hạn 3 sách có thể xuất phát từ **cả SRS** (đề cập số 3) **lẫn code** (constant `maxBooksPerMember = 3`). Hỏi nó "black hay white" là vô nghĩa.

**c. Kết luận**: Test tốt nên dùng **nhiều model** kết hợp — SRS cho coverage nghiệp vụ, code cho coverage chi tiết edge case (như null input).

---

## Bài tập 2 — Phá án cùng RIPR (The RIPR Detective)

### 2.1. Sơ đồ RIPR cho kịch bản

```
┌──────────────┐   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐
│ Reachability │ → │  Infection   │ → │ Propagation  │ → │ Revealability│
│  Tester chạm │   │ State nhiễm  │   │ State lỗi    │   │ Tester nhận  │
│  tới chức    │   │  lỗi (list   │   │ propagate ra │   │ ra qua so    │
│  năng search │   │  cũ vẫn lưu) │   │ UI (hiển thị)│   │ sánh expect  │
└──────────────┘   └──────────────┘   └──────────────┘   └──────────────┘
       ✅                 ✅                 ✅                 ❌ ← GÃY!
```

### 2.2. Câu hỏi thảo luận

**a. Bước nào bị gãy?**

→ **Revealability** (Bộ lộ). Bug đã đi đầy đủ 3 bước đầu (tester đến chức năng → state nội bộ bị nhiễm → UI hiển thị sai), nhưng tester vẫn ghi PASS. Nguyên nhân: **Test Oracle (kết quả mong đợi) quá yếu** — chỉ ghi "không có lỗi" → tester không có tiêu chí cụ thể để so sánh.

**b. Viết lại kết quả mong đợi**

| | Trước (yếu) | Sau (mạnh) |
|---|---|---|
| KQ mong đợi | "Không có lỗi" | "Khung kết quả tìm kiếm hiển thị **CHÍNH XÁC 0 dòng sách**. Hiển thị thông báo **"Không tìm thấy sách"**. **Bất kỳ sách nào** xuất hiện trong khung kết quả (kể cả sách từ lần tìm trước) đều coi là FAIL." |

→ Oracle mới mô tả **chính xác** số lượng (0) và đặt **điều kiện loại trừ** (không có sách nào hiện) → tester buộc phải kiểm tra trực quan và sẽ phát hiện bug.

**c. Liên hệ với automation**

- Trong automation, dòng `assert page.locator(".book-row").count() == 0` chính là **Test Oracle** — cài bằng code.
- Trong manual testing, "Kết quả mong đợi" trong TC = Test Oracle, do **mắt người** so sánh.
- Bài học: **Oracle mạnh** là yếu tố quyết định chất lượng test, bất kể manual hay automation.

---

## Bài tập 3 — Ai bảo vệ phần mềm? (Test Suite vs SRS)

### 3.1. Câu hỏi thảo luận

**a. Nếu xóa SRS, dev mới có dùng TC để code lại được không?**

→ **Có thể code khung sườn, nhưng không đủ**. Ví dụ chỉ nhìn TC-08 → TC-10:

| Dev biết được | Dev KHÔNG biết |
|---|---|
| Có khái niệm sách + thành viên + phiếu mượn | Tại sao giới hạn lại là 3 (TC chỉ test boundary, không giải thích) |
| Có nút "Mượn" và "Trả" trên UI | Thời hạn mượn = 14 ngày (TC không nói số ngày) |
| Phải hiển thị thông báo lỗi khi không hợp lệ | Phải phân biệt "tạm ngưng" vs "hết hạn" — nếu chỉ test happy path thì miss luôn |
| Phải cập nhật trạng thái sách | Trình tự ưu tiên kiểm tra điều kiện |

→ Dev có thể **đoán** ngược ra phần lớn, nhưng sẽ bỏ sót **lý do nghiệp vụ** (vì sao thiết kế thế).

**b. Thông tin nghiệp vụ rút ra từ TC-08 (Mượn sách)**

| # | Thông tin (đọc được từ test) | Nguồn |
|---|---|---|
| 1 | Hệ thống có 2 thực thể: Sách (Book) và Thành viên (Member) | "Thành viên MEM006 mượn BOOK001" |
| 2 | Mượn thành công sẽ tạo ra Phiếu mượn (Borrow Record) | "Tạo phiếu mượn mới" |
| 3 | Mỗi sách có thuộc tính trạng thái thay đổi khi mượn | "Sách chuyển sang 'Đã mượn'" |
| 4 | Có giới hạn số lượng sách mỗi thành viên được mượn | TC-04, TC-05 thể hiện boundary 2 → 3 → 4 |
| 5 | Có khái niệm "ngày hết hạn" (dueDate) | "Hạn trả 14 ngày" trong expected result |
| 6 | Có 3 trạng thái thành viên khác nhau ảnh hưởng quyền mượn | TC-06, TC-07 dùng "Tạm ngưng", "Hết hạn" |

**c. Giới hạn của góc nhìn "Test là tài liệu"**

| Vấn đề | Giải thích |
|---|---|
| Negative case bị bỏ sót | TC chỉ test những gì tester nghĩ ra. Trường hợp người dùng thao tác **kỳ lạ** (paste text khổng lồ, SQL injection...) không có trong TC → dev không biết phải xử lý. |
| Yêu cầu phi chức năng | TC không nói "phải load trong 2 giây", "phải chịu được 1000 user". Chỉ nghiệp vụ hiện rõ. |
| Tài liệu lệch nhau khi nghiệp vụ thay đổi | Nếu giới hạn đổi từ 3 → 2 nhưng TC chưa update, đọc TC sẽ hiểu sai. |
| Không thể hiện ý đồ thiết kế | "Vì sao có 3 trạng thái thành viên?" — TC không trả lời. Chỉ SRS/BRD mới có context kinh doanh. |

**d. Kết luận nhóm**

→ **Cả SRS và Test Suite phải cùng tồn tại**, đóng vai trò bổ trợ nhau:
- **SRS** = "tại sao" (intent) — định hướng cho dev và tester.
- **Test Suite** = "có thật như vậy không" (verification) — bằng chứng sống rằng system làm đúng SRS.
- Mất SRS → test có thể vẫn chạy nhưng knowledge nghiệp vụ biến mất. Mất Test → SRS không có gì xác nhận đã thực sự được triển khai đúng.

---

## Bài tập 4 — Vòng đời cuốn sách FSM

### 4.1. Test Paths cho Edge Coverage

Mỗi transition (T1–T5) phải được thực hiện ít nhất 1 lần:

| Test Path | Chuỗi trạng thái | Transitions bao phủ |
|-----------|------------------|---------------------|
| **TP1** | S1 → S2 → S1 | T1 (Mượn), T2 (Trả đúng hạn) |
| **TP2** | S1 → S2 → S3 → S1 | T1 (Mượn), T3 (Quá hạn), T4 (Trả trễ) |
| **TP3** | S1 → S2 → S3 → S4 | T1 (Mượn), T3 (Quá hạn), T5 (Đánh dấu thất lạc) |

→ 3 test paths cover toàn bộ 5 transitions.

### 4.2. Ánh xạ với TC nhóm đã viết

| Test Path | TC tương ứng (file `submissions/test-cases.md`) | Đã được cover? |
|-----------|-------------------------------------------------|---------------|
| **TP1** (Mượn → Trả đúng hạn) | TC-09 (mượn BOOK001) + TC-15 (trả BR003 đúng hạn) | ✅ Có |
| **TP2** (Mượn → Quá hạn → Trả trễ) | TC-09/TC-04 (mượn) + TC-17 (Kiểm tra quá hạn) + TC-16 (trả BR001 quá hạn) | ✅ Có |
| **TP3** (Mượn → Quá hạn → Thất lạc) | **❌ KHÔNG CÓ TC nào cover T5** (Mark as lost) | ❌ Thiếu |

### 4.3. Câu hỏi thảo luận

**a. Transition nào chưa có TC cover? Viết tiêu đề TC mới.**

→ **T5 (S3 → S4: Mark as lost)** chưa được cover.

TC mới gợi ý:
- **TC-26**: *"Thủ thư đánh dấu sách "Quá hạn lâu" thành "Thất lạc""*. Setup: phiếu BR001 đã ở trạng thái Quá hạn. Bước: Thủ thư mở phiếu BR001 → nhấn "Đánh dấu thất lạc". Expected: trạng thái sách BOOK003 chuyển sang "Thất lạc", phiếu BR001 vẫn giữ thông tin (không xóa).

**b. BUG-07 (off-by-one ở overdue check) nằm ở transition nào?**

→ **T3 (S2 → S3: Check overdue)**. Bug làm cho điều kiện `dueDate <= today` bị viết thành `dueDate < today` → sách quá hạn **đúng vào ngày dueDate** không chuyển sang S3 → transition T3 bị **kích hoạt sai thời điểm**.

**c. Nếu thêm tính năng "Tìm lại sách" (S4 → S1), FSM thay đổi thế nào?**

- Thêm transition **T6**: Found again (S4 → S1).
- FSM trở nên **strongly connected** hơn — từ mọi state đều có thể quay về S1.
- Cần bổ sung thêm 1 test path mới: TP4 = S1 → S2 → S3 → S4 → S1, đồng thời cần Decision Table cho thao tác "Tìm lại" (chỉ Thủ thư mới được phép?).

---

## Bài tập 5 — Oracle mạnh vs Oracle yếu

### 5.1. Oracle nào phát hiện BUG-06 (lọc thể loại case-sensitive)?

> **Kịch bản**: Nhập `"công nghệ"` (chữ thường) vào ô lọc → hệ thống không trả về sách nào (đúng ra phải trả 8 sách Công nghệ).

| Oracle | Phát hiện BUG-06? | Giải thích |
|--------|-------------------|------------|
| **A (Null)** — "Hệ thống không bị lỗi" | ❌ KHÔNG | Hệ thống chạy bình thường, không crash, không runtime exception. Tester sẽ ghi PASS. |
| **B (Weak)** — "Hiển thị danh sách sách" | ⚠️ Phụ thuộc | Nếu hệ thống hiển thị **danh sách rỗng** (kèm "Không tìm thấy sách") thì kỹ thuật vẫn là "danh sách" → tester có thể ghi PASS. Phần lớn sẽ miss. |
| **C (Strong)** — "Hiển thị 5 sách: BOOK001, BOOK002, BOOK003, BOOK005, BOOK008" | ✅ CÓ | Expected = 5 sách cụ thể. Actual = 0 sách. → Khác → FAIL → tester bắt buộc phát hiện. |

### 5.2. Đánh giá Oracle level của TC nhóm

Nhìn lại `submissions/test-cases.md` của nhóm:

| TC | Expected hiện tại | Oracle level | Cải thiện nếu cần |
|---|---|---|---|
| TC-08 | "Hiển thị ít nhất 20 sách. Dòng BOOK001 có đủ 5 trường: tên..., tác giả..." | **Strong** ✅ | OK |
| TC-14 | "Chỉ hiển thị 3 sách: BOOK004, BOOK012, BOOK013" | **Strong** ✅ | OK |
| TC-09 | "Trạng thái BOOK001 đổi sang 'Đã mượn' ngay lập tức" | **Strong** ✅ | OK |
| TC-19 | "Hiển thị thông báo thành công. Thành viên mới xuất hiện trong danh sách." | **Medium-Weak** ⚠️ | Nên thêm: "Thành viên mới có ID `MEM00X`, hiện đầy đủ Họ tên, Email, SĐT vừa nhập, trạng thái mặc định 'Hoạt động'." |
| TC-25 | "Hiển thị đủ cả 5 phiếu (BR001-BR005)" | **Strong** ✅ | OK |

→ Phần lớn TC của nhóm thuộc **Strong Oracle**. Riêng TC-19 (thêm thành viên) có thể mạnh hơn bằng cách liệt kê các trường cụ thể.

### 5.3. Oracle C nên kiểm gì?

Theo textbook: *"only check things directly relevant to the test purpose"*. Với TC lọc thể loại:

- ✅ **CẦN check**: Số sách hiển thị + danh sách mã sách cụ thể + thể loại của mỗi sách đều = "Công nghệ".
- ❌ **KHÔNG cần check**: Màu nền của hàng, font chữ, animation chuyển trang, vị trí cuộn (scroll position) — những thứ này thuộc về TC giao diện khác.

→ Oracle C phải đủ **chính xác** để bắt bug nhưng đừng **quá rộng** dẫn tới mass false-positive.

---

## Bài tập 6 — Regression Test Selection (giới hạn 3 → 2)

### 6.1. Phân loại 12 Test Cases

> **Thay đổi**: maxBooksPerMember từ **3** xuống **2**.

| TC | Mô tả | Sẽ FAIL? | Phải chạy lại? | Lý do |
|----|-------|---------|---------------|-------|
| TC-01 | Đăng nhập thành công | ❌ Không | ✅ Có (smoke) | Smoke test — đảm bảo thay đổi business rule không vô tình phá login |
| TC-02 | Đăng nhập sai mật khẩu | ❌ Không | ❌ Không | Không liên quan tới chức năng mượn sách |
| TC-03 | Đăng nhập bỏ trống | ❌ Không | ❌ Không | Không liên quan |
| TC-04 | Tìm kiếm có kết quả | ❌ Không | ❌ Không | Module search độc lập |
| TC-05 | Tìm kiếm không kết quả | ❌ Không | ❌ Không | Module search độc lập |
| TC-06 | Lọc theo thể loại | ❌ Không | ❌ Không | Module filter độc lập |
| TC-07 | Tìm theo tác giả | ❌ Không | ❌ Không | Module search độc lập |
| TC-08 | Mượn sách (happy path) | ⚠️ **Có thể FAIL** | ✅ **Bắt buộc** | Nếu TC-08 setup "đã mượn 2 sách rồi mượn cuốn 3", expected cũ là **allow** (vì ≤3), expected mới là **reject** (vì >2) |
| TC-09 | Xem sách đang mượn | ❌ Không | ✅ Có (regression) | Module mượn/trả thay đổi → có thể vô tình affect view |
| TC-10 | Trả sách | ❌ Không | ✅ Có (regression) | Phụ thuộc trực tiếp vào module mượn, dữ liệu setup có thể đổi |
| TC-11 | Đăng xuất | ❌ Không | ❌ Không | Không liên quan |
| TC-12 | Chuyển ngôn ngữ | ❌ Không | ❌ Không | Module i18n độc lập |

### 6.2. Câu hỏi thảo luận

**a. TC nào chắc chắn FAIL?**

→ **TC-08** (nếu test data setup là "thành viên đã mượn 2 sách"). Vì:
- Expected cũ: "cho mượn cuốn thứ 3 — vẫn ≤ giới hạn 3" → result = success.
- Expected mới: với giới hạn 2, mượn cuốn thứ 3 phải bị **reject** → kết quả thực tế ≠ kết quả expected ghi trong TC cũ → **FAIL**.
- Để pass: tester phải **update expected result** của TC-08 theo business rule mới (đây là **test maintenance**).

> **Lưu ý**: Trong file nhóm `submissions/test-cases.md`, TC-04 (cho phép mượn ở biên 2→3) sẽ FAIL khi giới hạn = 2.

**b. TC-08 không FAIL nhưng vì sao vẫn phải chạy lại?**

- Vì khi thay đổi code module mượn sách, **các phần code liên quan có thể bị ảnh hưởng dây chuyền** (Ammann: *"small changes to one part of a system often cause problems in distant parts"*).
- Mặc dù logic giới hạn thay đổi, có thể dev vô tình sửa luôn cả validation khác (vd: check thành viên Suspended, check book Available...). Chạy lại TC-08 để **xác minh các điều kiện reject khác vẫn hoạt động**.

**c. Ưu tiên TC nào chạy trước nếu chỉ có 2-3 giờ?**

Nguyên tắc: **Risk-based prioritization**.

1. **Ưu tiên cao** (chạy đầu): TC-08, TC-09, TC-10 — trực tiếp liên quan thay đổi. Nếu fail → block release ngay.
2. **Ưu tiên trung bình**: TC-01 (smoke), TC-04, TC-06 — kiểm soát rủi ro dây chuyền.
3. **Ưu tiên thấp**: TC-02, TC-03, TC-05, TC-07, TC-11, TC-12 — module độc lập, khả năng bị ảnh hưởng thấp. Có thể bỏ qua nếu thời gian không đủ.

---

## Bài tập 7 — Kill the Mutant

### 7.1. Tình huống 1 — Giới hạn mượn sách (`>=` vs `>`)

```
Code đúng:   if (số_sách_đang_mượn >= 3)  → từ chối
Code lỗi:    if (số_sách_đang_mượn > 3)   → cho mượn cuốn thứ 4!
```

| Số sách đang mượn | Code đúng (`>=`) | Code lỗi (`>`) | Kill mutant? |
|-------------------|-----------------|----------------|-------------|
| **2** | false → Cho mượn ✅ | false → Cho mượn ✅ | ❌ Không — cùng KQ |
| **3** | true → **Từ chối** ❌ | false → **Cho mượn** ✅ | ✅ **CÓ — Kill!** |
| **4** | true → Từ chối ❌ | true → Từ chối ❌ | ❌ Không — cùng KQ |

→ Giá trị giết mutant: **số sách = 3** (chính là **biên** của điều kiện).

### 7.2. Tình huống 2 — Kiểm tra quá hạn (BUG-07 logic)

```
Code đúng:   if (hôm_nay >= ngày_hẹn)  → đánh dấu quá hạn
Code lỗi:    if (hôm_nay > ngày_hẹn)   → bỏ qua đúng ngày dueDate
```

Sách mượn 01/09, hạn trả 15/09:

| Ngày kiểm tra | Code đúng (`>=`) | Code lỗi (`>`) | Kill? |
|--------------|----------|---------|-------|
| **14/09** | false → Chưa quá hạn ✅ | false → Chưa quá hạn ✅ | ❌ Không |
| **15/09** ⭐ | true → **Quá hạn** ❌ | false → **Chưa quá hạn** ✅ | ✅ **CÓ — Kill!** |
| **16/09** | true → Quá hạn ❌ | true → Quá hạn ❌ | ❌ Không |

→ Giá trị giết mutant: **ngày kiểm tra = dueDate** (boundary).

### 7.3. Câu hỏi tổng kết

**a. Giá trị test giết mutant nằm ở đâu?**

→ **Ngay tại biên (boundary value)**. Cụ thể:
- ROR mutant `>=` ↔ `>` chỉ phân biệt nhau **đúng tại điểm bằng nhau** (`x == limit`).
- Bên trong miền (`x < limit` hoặc `x > limit`) cả 2 toán tử cho cùng kết quả → không giết được mutant.

**b. Vì sao "thiết kế test data tốt bằng BVA tự động giết hầu hết ROR mutants"?**

→ **BVA buộc phải test 3 điểm**: trước biên, **đúng biên**, sau biên. Mà **đúng biên** là chính là điểm phân biệt mọi ROR mutant (`<` vs `<=`, `>` vs `>=`, `==` vs `!=`). Do đó:
- Thiết kế test BVA = "tự động" cover các ROR mutant test cases.
- Mutation testing với ROR và BVA về bản chất **đo cùng một thứ** — sức mạnh của test data tại boundary.
- Đây là lý do textbook nói: BVA + Mutation Testing là **hai mặt của cùng một đồng xu**.

---

## Bài tập 8 — Chiếc bẫy Logic (MC/DC)

### 8.1. Bước 1 — Predicate Coverage (PC)

| TC | A | B | C | P = A ∧ B ∧ C | Kết quả |
|-----|---|---|---|---|---|
| TC-α | T | T | T | **True** | Cho mượn ✅ |
| TC-β | F | F | F | **False** | Từ chối ❌ |

**Bug không phát hiện được với chỉ PC**:
- **BUG-04**: Thành viên "Tạm ngưng" nhận thông báo sai "đã hết hạn".
- Tại sao? Cả TC-α và TC-β đều **không phân biệt** clause C (thành viên active) với clause khác. TC-β có cả A, B, C = F nên không biết hệ thống phản ứng đúng với riêng C = F (suspended) hay không.
- Tổng quát: PC **không biết clause nào** thực sự gây ra kết quả False → bug ở clause C cụ thể có thể ẩn dưới sự che chắn của A, B cũng False.

### 8.2. Bước 2 — Active Clause Coverage (ACC) Truth Table

| TC | A | B | C | P | Major clause | Giải thích |
|----|---|---|---|-----|----------------------|-----------|
| 1 | **T** | T | T | **True** | A True → P True | Lật A → P thay đổi |
| 2 | **F** | T | T | **False** | A False → P False | Cặp với TC1 |
| 3 | T | **T** | T | **True** | B True → P True | (Trùng TC1) |
| 4 | T | **F** | T | **False** | B False → P False | Lật B, A và C giữ T |
| 5 | T | T | **T** | **True** | C True → P True | (Trùng TC1 và TC3) |
| 6 | T | T | **F** | **False** | C False → P False | Lật C, A và B giữ T |

**Sau khi dedup (loại trùng)**, ACC cần **tối thiểu 4 TC** cho `A AND B AND C`:

| TC dedup | A | B | C | P |
|---|---|---|---|---|
| TC1 | T | T | T | True |
| TC2 | F | T | T | False |
| TC4 | T | F | T | False |
| TC6 | T | T | F | False |

> **Lý do trùng**: TC1, TC3, TC5 đều là (T, T, T) — vì để A/B/C ở vị trí "determines" thì các clause khác phải True. Ba TC này gộp thành 1.

### 8.3. Bước 3 — Phát hiện BUG-04

**BUG-04**: Suspended → thông báo sai "Expired" (lỗi trong nhánh xử lý clause C = False).

| TC | Phát hiện BUG-04? | Giải thích |
|---|---|---|
| **PC: TC-α (TTT)** | ❌ Không | C = True → không kích hoạt nhánh suspended |
| **PC: TC-β (FFF)** | ⚠️ Có thể không | C = False nhưng A và B cũng False → không rõ thông báo đến từ A, B hay C |
| **ACC: TC6 (TTF)** | ✅ **CÓ** | A, B đều OK → bug nếu xuất hiện phải đến từ clause C → thông báo "Expired" sai bản chất sẽ được phơi bày |

→ **ACC phát hiện BUG-04, PC không (hoặc che lấp)**.

### 8.4. Câu hỏi thảo luận

**a. PC cần 2 TCs, ACC cần 4 TCs. Tại sao tăng lên xứng đáng?**

- Tăng 2x số TC, nhưng đổi lại có khả năng phát hiện bug **ở từng clause cụ thể**.
- Trong hệ thống nghiệp vụ phức tạp (Mượn sách có 3 clauses), bug có thể nằm ở **bất kỳ clause nào** — chỉ PC sẽ miss.
- Chi phí 2 TC thêm rẻ hơn nhiều so với chi phí khi 1 BUG-04 lọt ra production (thông báo sai → librarian xử lý sai → mất uy tín thư viện).

**b. Vì sao chỉ dùng PC cho phần mềm hàng không là nguy hiểm?**

- Phần mềm điều khiển bay có biểu thức logic phức tạp (vd: `(altitude > MIN AND speed < MAX AND fuel >= RESERVE)`).
- 1 clause sai (vd: dấu so sánh fuel) → có thể gây tai nạn chết người.
- PC chỉ cần True/False của toàn biểu thức → bỏ sót lỗi trong clause cụ thể.
- Vì vậy **FAA bắt buộc MC/DC** (≈ ACC) cho phần mềm có hậu quả nghiêm trọng (DO-178C).

**c. Liên kết BT7 — AND → OR là loại mutant nào?**

- AND → OR là **LOR (Logical Operator Replacement)**, **KHÔNG phải ROR** (vốn dành cho `<, >, ==, ...`).
- Test giết LOR: cần input mà `AND` và `OR` cho kết quả khác nhau.
  - Vd: (T, F, T): `A AND B AND C = F`, `A OR B OR C = T` → khác → **kill**!
  - Vd: (T, F, F): cùng kết quả → không kill.
- Nhận xét: **ACC TC4 (T, F, T)** trong bảng ở trên đồng thời giết LOR mutant (AND → OR). ACC mạnh đến mức "miễn phí" cover nhiều loại mutant khác.

---

## Bài tập 9 — Flaky Tests (Exit Ticket)

### 9.1. Thí nghiệm tưởng tượng

Thay `wait_for_flutter(page, text="Đăng xuất")` bằng `time.sleep(0.1)`:

| Lần chạy | `wait_for_flutter` (Smart Wait) | `time.sleep(0.1)` (Hard Sleep) |
|----------|-------------------------------|-------------------------------|
| 1 | ✅ PASS | ✅ PASS (mạng nhanh, Flutter render kịp) |
| 2 | ✅ PASS | ❌ FAIL (Flutter chưa render xong, locator không tìm thấy) |
| 3 | ✅ PASS | ✅ PASS (may mắn) |
| 4 | ✅ PASS | ❌ FAIL |
| ... | ✅ PASS | Lúc PASS, lúc FAIL — **Flaky** |
| 10 | ✅ PASS | Tổng kết: ~5/10 FAIL ngẫu nhiên |

### 9.2. Vì sao `wait_for_flutter` ổn định?

- **Smart Wait** chờ **điều kiện cụ thể** (element xuất hiện trên Semantics Tree). Khi điều kiện thỏa → tiếp tục ngay. Khi không → throw timeout sau 10s.
- → Kết quả **xác định** (deterministic): chỉ phụ thuộc trạng thái thật của UI, không phụ thuộc thời gian wall-clock.

**Hard Sleep** không ổn định vì:
- 100ms là một **phỏng đoán cứng** — không có gì đảm bảo Flutter render kịp trong 100ms.
- Mỗi lần chạy, thời gian Flutter render dao động (do CPU, GC, network, browser caching) → kết quả **không xác định** (non-deterministic).
- → **Race condition** giữa test code và Flutter rendering pipeline.

### 9.3. Đánh đổi Good vs Fast

Nếu thay bằng `time.sleep(5)` để "an toàn":

- 12 TC × 10 bước × 5s = **600 giây = 10 phút** cho 1 lần chạy CI.
- Trong TDD/CI, dev push code 20-50 lần/ngày → cần feedback < 5 phút.
- 10 phút sleep + thời gian test logic = **mất immediate verification**.
- Dev mất kiên nhẫn → **bỏ qua CI** → quay lại trạng thái "không có Guardian".

**Bài học**: Test phải vừa **Good** (deterministic, bắt được bug) vừa **Fast** (đủ nhanh để dev tin và chạy thường xuyên). Smart Wait giải quyết được cả hai: chính xác **và** thoát sớm khi điều kiện thỏa.

### 9.4. Câu hỏi trắc nghiệm

> Hàm `wait_for_flutter` thuộc thành phần nào trong kiến trúc test?

- (a) Test Oracle — ❌ không phải. Oracle là phần so sánh actual vs expected.
- (b) Test Driver — ❌ Driver là phần điều khiển hệ thống dưới test (vd: `page.click()`).
- ✅ **(c) Test Harness infrastructure** — Đây là **hạ tầng chung** mà nhiều test sử dụng để giải quyết vấn đề kỹ thuật (chờ Flutter render). Không thuộc về logic test cụ thể nào.
- (d) Test Data — ❌ Data là input cho test (vd: email, password).

→ **Đáp án: (c) Test Harness infrastructure**.

---

## Phụ lục — Tổng kết kiến thức đã áp dụng

| Bài tập | Khái niệm chính | Liên kết với công việc nhóm |
|---|---|---|
| BT1 — Box Debate | MDTD, Level of Abstraction | Nhóm đã viết TC từ SRS (high-level). Bài này mở rộng thêm góc nhìn code |
| BT2 — RIPR Detective | RIPR Model, Test Oracle | Củng cố tại sao "Kết quả mong đợi" phải cụ thể trong TC nhóm |
| BT3 — Test as Guardian | De facto Specification | Đánh giá vai trò của bộ test 25 TC nhóm |
| BT4 — Book Lifecycle FSM | State/Transition Coverage | Phát hiện TC nhóm còn thiếu transition T5 (Mark as lost) |
| BT5 — Oracle Strength | Strong vs Weak Oracle | Đánh giá oracle của TC nhóm — đa phần Strong, riêng TC-19 cần mạnh hơn |
| BT6 — Regression Selection | Risk-based prioritization | Nếu giới hạn đổi 3→2, TC-04, TC-05 phải update |
| BT7 — Kill the Mutant | Mutation Testing, BVA | Lý giải vì sao BVA của nhóm (giới hạn 3 sách) hữu ích |
| BT8 — The Logic Trap | MC/DC | BUG-04 (Suspended/Expired) chỉ phát hiện khi áp dụng ACC, không phải PC |
| BT9 — Flaky Tests | Test Harness | Quan trọng nếu nhóm chuyển sang automation testing |
