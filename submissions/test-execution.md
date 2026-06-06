# Test Execution — Kết quả thực thi kiểm thử

> **Hướng dẫn**: Chạy từng TC trên hệ thống https://stqa.rbc.vn, ghi lại kết quả thực tế.
> Kết luận: **Pass** (kết quả đúng), **Fail** (kết quả sai → tạo bug report), **Blocked** (không thực hiện được vì lỗi khác chặn), **Not Run** (chưa chạy).

| Thông tin | |
|---|---|
| **Nhóm** | Nhóm 21 |
| **Ngày thực thi** | 21/05/2026 |
| **Trình duyệt** | Chrome 124 |
| **Hệ điều hành** | Window 11 |

---

## Detailed Results

| TC ID | Functional Group | Expected Result (Summary) | Actual Result | Conclusion | Evidence | Bug |
|-------|------------------|---------------------------|----------------|------------|-----------|-----|
| TC-01 | Login | Login succeeds and displays "Nguyễn Thủ Thư" with correct role | Successfully redirected to home page with correct name and role displayed | **Pass** | `tc01.png` | |
| TC-02 | Login | Login blocked with message "Member not found" | Login blocked and message "Member not found" displayed | **Pass** | `tc02.png` | |
| TC-03 | Login | Login blocked with message **"Incorrect password"** | Login blocked and message "Incorrect password" displayed | **Pass** | `tc03.png` | |
| TC-04 | Login | Display "Please enter email and password" and stay on login page | System required user to enter email and password | **Pass** | `tc04.png` | |
| TC-05 | Book List | Member can view all 20 books with 5 information columns | All 20 books and 5 information columns displayed correctly with borrow buttons | **Pass** | `tc05.png` | |
| TC-06 | Book List | Librarian can view all 20 books with 5 information columns | Same book list displayed as Member view, but without borrow buttons | **Pass** | `tc06.png` | |
| TC-07 | Book List | Guest is denied access and redirected to Login page | Access denied and automatically redirected to Login page | **Pass** | `tc07.png` | |
| TC-08 | Book List | BOOK001 status updates to "Borrowed" in real-time without refresh | Status automatically changed to "Borrowed" without F5 refresh | **Pass** | `tc08.png` | |
| TC-09 | Search | Only BOOK001 containing "Flutter" is displayed | Correctly displayed "Flutter Programming Basics" | **Pass** | `tc09.png` | |
| TC-10 | Search | Display BOOK001 and BOOK009 by author Nguyễn Minh Đức | Correctly displayed both books by Nguyễn Minh Đức | **Pass** | `tc10.png` | |
| TC-11 | Search | Empty result list with "No books found" message | Empty interface displayed with message "No books found" | **Pass** | `tc11.png` | |
| TC-12 | Search | Same result as TC-09 (case-insensitive search) | Flutter book was still found successfully | **Pass** | `tc12.png` | |
| TC-13 | Search | Display only 8 Technology books: BOOK001, BOOK002, BOOK003, BOOK005, BOOK008, BOOK009, BOOK010, BOOK011 | No books were displayed | **Fail** | `tc13.png` | BUG-01 |
| TC-14 | Borrow Book | Borrow succeeds, status changes to "Borrowed", due date set to +14 days | Borrow succeeded and book was added to borrow records | **Pass** | `tc14.png` | |
| TC-15 | Borrow Book | Borrow button disabled OR display "Book already borrowed" | Borrow button (+) was completely hidden | **Pass** | `tc15.png` | |
| TC-16 | Borrow Book | Borrow rejected with correct reason "Suspended" | Incorrect rejection reason displayed ("Expired" instead of "Suspended") | **Fail** | `tc16.png` | BUG-02 |
| TC-17 | Borrow Book | Borrow rejected with correct reason "Expired" | Borrow blocked with message "Member account expired" | **Pass** | `tc17.png` | |
| TC-18 | Borrow Book | Borrow rejected with message "Borrowing limit reached (3 books)" | System did NOT block borrowing and allowed 4th book successfully | **Fail** | `tc18.png` | BUG-03 |
| TC-19 | Return Book | Return succeeds before due date, status changes to "Available", NO overdue warning | Return succeeded without any overdue warning | **Pass** | `tc19.png` | |
| TC-20 | Return Book | Return succeeds on due date with NO overdue warning | Book returned successfully on due date without warning | **Pass** | `tc20.png` | |
| TC-21 | Return Book | Return succeeds and overdue warning is displayed | Return succeeded but NO overdue warning was displayed | **Fail** | `tc21.png` | BUG-04 |
| TC-22 | Return Book | "Return Book" button hidden OR display "You did not borrow this book" | Security issue: system still allowed returning another member’s book | **Fail** | `tc22.png` | BUG-05 |
| TC-23 | Overdue Processing | BR006 status changes to "Overdue" | BR006 remained in "Borrowing" status | **Fail** | `tc23.png` | BUG-06 |
| TC-24 | Overdue Processing | Record due tomorrow remains "Borrowing" | Non-overdue record correctly remained unchanged | **Pass** | `tc24.png` | |
| TC-25 | Overdue Processing | "Check Overdue" button hidden for Member | No overdue-check button found in Member interface | **Pass** | `tc25.png` | |
| TC-26 | Member Management | Member added successfully with confirmation and appears in list | System incorrectly displayed "Invalid email" even though email was valid | **Fail** | `tc26.png` | BUG-07 |
| TC-27 | Member Management | System blocks creation and shows invalid email format error | Member creation succeeded despite invalid email format (missing `.`) | **Fail** | `tc27.png` | BUG-07 |
| TC-28 | Member Management | System blocks duplicate email and shows error message | System correctly blocked duplicate email and displayed error | **Pass** | `tc28.png` | |
| TC-29 | Member Management | System blocks creation and requires Name field | Red warning message displayed requiring Name input | **Pass** | `tc29.png` | |
| TC-30 | Borrow Record Lookup | Librarian can view ALL 5 records with complete information | All borrow records from BR001 to BR005 displayed correctly | **Pass** | `tc30.png` | |
| TC-31 | Borrow Record Lookup | MEM002 can ONLY view own records and cannot view others | Regular member was able to access other members’ borrow records (data leak) | **Fail** | `tc31.png` | BUG-05 |
| TC-32 | Borrow Record Lookup | Empty list displayed ("You do not have any borrow records") without system crash | Blocked: Could not test because new member creation failed in TC-26 | **Blocked** | `tc32.png` | |

---

## Summary of Test Execution Results

| Metric | Value |
|--------|--------|
| Total Test Cases | `32` |
| Pass | `22` |
| Fail | `9` |
| Blocked | `1` |
| Not Run | `0` |
| **Pass Rate** | `68.75%` |

### Functional Group Results

| Functional Group | Total TC | Pass | Fail | Blocked | Pass Rate |
|------------------|----------|------|------|----------|------------|
| Login | 4 | 4 | 0 | 0 | 100% |
| View Book List | 4 | 4 | 0 | 0 | 100% |
| Search & Filter Books | 5 | 4 | 1 | 0 | 80% |
| Borrow Books | 5 | 3 | 2 | 0 | 60% |
| Return Books | 4 | 2 | 2 | 0 | 50% |
| Overdue Processing | 3 | 2 | 1 | 0 | 66.7% |
| Member Management | 4 | 2 | 2 | 0 | 50% |
| Borrow Record Lookup | 3 | 1 | 1 | 1 | 33.3% |