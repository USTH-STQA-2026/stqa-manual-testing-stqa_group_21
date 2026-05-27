# Bug Reports — Báo cáo lỗi

> **Hướng dẫn**: Tạo 1 mục bug cho mỗi TC có kết quả **Fail**.
> Xem [examples/sample-bug-report.md](../examples/sample-bug-report.md) để hiểu cách viết bug report tốt.
> Mỗi bug cần: tiêu đề mô tả hành vi lỗi, bước tái hiện, expected vs actual, severity + giải thích.

| Thông tin | |
|---|---|
| **Nhóm** | Nhóm 21 |
| **Ngày báo cáo** | 19/05/2026 |

## BUG-01

| Attribute | Detail |
|-----------|---------|
| **Bug ID** | BUG-01 |
| **Related TC** | TC-13 |
| **Related REQ** | REQ-03 |
| **Severity** | Medium |
| **Reported By** | Nguyễn Minh Hiếu |
| **Date Reported** | 19/05/2026 |
| **Status** | Open |

**Title:**
Book genre filter is case-sensitive — entering "công nghệ" (lowercase) returns 0 results

**Environment:**
- Browser: Chrome Version 148.0.7778.168
- Operating System: Windows
- Interface Language: Vietnamese

**Preconditions:**
User is logged in. System has at least 8 books in the "Công nghệ" category.

**Steps to Reproduce:**
1. Enter 'ba.nguyen@email.com' into the 'Email' field
2. Enter 'password123' into the 'Password' field and click 'Login'
3. Enter 'công nghệ' into the 'Filter by category' field

**Expected Result:**
System displays 8 Technology books: BOOK001, BOOK002, BOOK003, BOOK005, BOOK008, BOOK009, BOOK010, BOOK011. The filter must be case-insensitive per REQ-03.

**Actual Result:**
System displays 'No books found.'

**Impact:**
The category filter fails to return any results when the search term is entered in lowercase, even though REQ-03 explicitly requires case-insensitive filtering. Users are unable to find available books through the filter function, directly breaking a core search feature and causing all matching books to appear unavailable when they are not.

**Evidence:**

**Suggested Fix:**
Normalize both the filter input and the stored category values to the same case before performing the comparison so that inputs like `"công nghệ"`, `"Công Nghệ"`, or `"CÔNG NGHỆ"` all return the same results.

---

## BUG-02

| Attribute | Detail |
|-----------|---------|
| **Bug ID** | BUG-02 |
| **Related TC** | TC-16 |
| **Related REQ** | REQ-04 |
| **Severity** | Medium |
| **Reported By** | Hoàng Lê Anh Đức |
| **Date Reported** | 19/05/2026 |
| **Status** | Open |

**Title:**
System displays incorrect rejection reason when a suspended member attempts to borrow a book

**Environment:**
- Browser: Chrome Version 148.0.7778.168
- Operating System: Windows
- Interface Language: Vietnamese

**Preconditions:**
Data has been reset. Member `cu.le@email.com` (MEM004) exists in the system with status "Suspended".

**Steps to Reproduce:**
1. Enter 'cu.le@email.com' into the 'Email' field
2. Enter 'password123' into the 'Password' field and click 'Login'
3. Click the 'plus' icon on any available book

**Expected Result:**
System displays: "Member account is suspended. Cannot borrow book."

**Actual Result:**
System displays: "Member account has expired. Cannot borrow book."

**Impact:**
Incorrect rejection message prevents librarians from distinguishing between suspended and expired accounts, leading to incorrect actions (renewing membership instead of reactivating account, or vice versa).

**Evidence:**
![alt text](image-7.png)
![alt text](image.png)

**Suggested Fix:**
Separate the display condition for each member status and map the correct message accordingly.

---

## BUG-03

| Attribute | Detail |
|-----------|---------|
| **Bug ID** | BUG-03 |
| **Related TC** | TC-18 |
| **Related REQ** | REQ-04 |
| **Severity** | High |
| **Reported By** | Nguyễn Tiến Dũng |
| **Date Reported** | 19/05/2026 |
| **Status** | Open |

**Title:**
System allows a member to borrow a 4th book, exceeding the 3-book limit

**Environment:**
- Browser: Chrome Version 148.0.7778.168
- Operating System: Windows
- Interface Language: Vietnamese

**Preconditions:**
Data has been reset. Member `biet.hoang@email.com` (MEM006) is active and currently has 1 book borrowed (BR003 — BOOK013).

**Steps to Reproduce:**
1. Enter 'biet.hoang@email.com' into the 'Email' field
2. Enter 'password123' into the 'Password' field and click 'Login'
3. Click the 'plus' icon on any 3 available books (bringing total to 4)

**Expected Result:**
System displays: "Maximum borrow limit of 3 books reached." and rejects the 4th borrow request.

**Actual Result:**
System allows borrowing the 4th book without any restriction.

**Impact:**
Violates core business rule — the system allows a member to borrow more than the maximum of 3 books simultaneously, causing loss of control over book circulation.

**Evidence:**
![alt text](image-2.png)

**Suggested Fix:**
Check the number of currently borrowed books before allowing a new borrow request. If the count is already 3, reject the request and display the appropriate error message.

---

## BUG-04

| Attribute | Detail |
|-----------|---------|
| **Bug ID** | BUG-04 |
| **Related TC** | TC-21 |
| **Related REQ** | REQ-05 |
| **Severity** | Medium |
| **Reported By** | Nguyễn Cao Tuấn |
| **Date Reported** | 19/05/2026 |
| **Status** | Open |

**Title:**
System does not display overdue warning when a book is returned past its due date

**Environment:**
- Browser: Chrome Version 148.0.7778.168
- Operating System: Windows
- Interface Language: Vietnamese

**Preconditions:**
Data has been reset. Borrow record BR001 (MEM002 — ba.nguyen@email.com) has dueDate = 15/09/2024 — already overdue compared to today's date.

**Steps to Reproduce:**
1. Enter 'ba.nguyen@email.com' into the 'Email' field
2. Enter 'password123' into the 'Password' field and click 'Login'
3. Click the 'Borrow/Return' button
4. Click the 'Return Book' button on BR001 (BOOK003)

**Expected Result:**
System displays "Returned" along with an overdue warning indicating the number of days late.

**Actual Result:**
Only a green "Returned" badge is shown — no overdue warning is displayed.

**Impact:**
Librarians are unaware that the member returned the book late, making it impossible to handle violations or apply penalties if applicable.

**Evidence:**
![alt text](image-8.png)

**Suggested Fix:**
When a member returns a book, the system should compare the actual return date with the due date:
- If return date ≤ due date → display "Returned", no warning
- If return date > due date → display "Returned" along with an overdue warning and the number of days late

---

## BUG-05

| Attribute | Detail |
|-----------|---------|
| **Bug ID** | BUG-05 |
| **Related TC** | TC-22, TC-31 |
| **Related REQ** | REQ-05, REQ-08 |
| **Severity** | High |
| **Reported By** | Hoàng Lê Anh Đức |
| **Date Reported** | 19/05/2026 |
| **Status** | Open |

**Title:**
Member is able to view and return borrow records belonging to another member

**Environment:**
- Browser: Chrome Version 148.0.7778.168
- Operating System: Windows
- Interface Language: Vietnamese

**Preconditions:**
Logged in as Regular Member `ba.nguyen@email.com` (MEM002). MEM006 has at least one active borrow record (BR003) in the system.

**Steps to Reproduce:**
1. Enter 'ba.nguyen@email.com' into the 'Email' field
2. Enter 'password123' into the 'Password' field and click 'Login'
3. Click the 'Borrow/Return' button
4. Click 'Borrow Record Lookup'
5. Enter 'MEM006' into the 'Enter Member ID' field and click 'Lookup'
6. Click 'Return Book' on any displayed record

**Expected Result:**
- Step 5: System REJECTS the lookup request and does not display any records belonging to MEM006
- Step 6: Return action is not available or is rejected with error: "You are not authorized to return this book."

**Actual Result:**
- Step 5: System displays MEM006's full borrow records (member name, book title, borrow date, due date)
- Step 6: System displays 'Return Successful' and updates the record status to 'Returned'

**Impact:**

**Security / Authorization:**
Regular members can look up and view borrow records of any other member by entering their member ID, violating access control rules defined in REQ-08. Members can also perform return actions on behalf of others without any ownership verification, violating REQ-05.

**Data Integrity:**
Borrow records are incorrectly updated — the "Returned" status does not reflect reality as the actual borrower has not returned the book. This can be exploited to fraudulently clear another member's overdue or penalty records.

**Business Operations:**
Librarians lose accurate control over book borrow status. Books may be marked as "Returned" in the system while still missing in reality.

**Evidence:**
![alt text](image-9.png)
![alt text](image-10.png)

**Suggested Fix:**
When a member performs a borrow record lookup or a return action, the system must verify the logged-in user's identity before returning results or processing the request:
- If logged-in user is a **Librarian** → allow lookup and return actions for any member
- If logged-in user is a **Regular Member** → only allow viewing and acting on their own records; if they attempt to access another member's records → reject and display: "You do not have permission to view another member's borrow records"

---

## BUG-06

| Attribute | Detail |
|-----------|---------|
| **Bug ID** | BUG-06 |
| **Related TC** | TC-23 |
| **Related REQ** | REQ-06 |
| **Severity** | High |
| **Reported By** | Nguyễn Minh Hiếu |
| **Date Reported** | 19/05/2026 |
| **Status** | Open |

**Title:**
System does not mark borrow record as "Overdue" on the exact due date

**Environment:**
- Browser: Chrome Version 148.0.7778.168
- Operating System: Windows
- Interface Language: Vietnamese

**Preconditions:**
- Member `ba.nguyen@email.com` has an active borrow record BR001 with dueDate = borrow date + 14 days and status "Borrowing"
- Librarian account (librarian@library.com / admin123) is available
- System date has been changed to match the borrow record's due date

**Steps to Reproduce:**
1. Enter 'librarian@library.com' into the 'Email' field
2. Enter 'admin123' into the 'Password' field and click 'Login'
3. Click the 'Borrow/Return' button
4. Click 'Check Overdue Books'

**Expected Result:**
Borrow record BR001 with dueDate = today is marked as **"Overdue"**. Per REQ-06: any record where `dueDate ≤ today` must be flagged as overdue, including the exact due date itself.

**Actual Result:**
Borrow record BR001 remains in **"Borrowing"** status — not flagged as overdue.

**Impact:**
Violates the core overdue detection rule defined in REQ-06 (`dueDate ≤ today`). Books due today are not flagged, causing librarians to miss overdue records during the first check. The record is only detected from the following day onward, resulting in a one-day blind spot in overdue management and delayed penalty enforcement.

**Evidence:**
*(Cần bổ sung ảnh chụp màn hình)*

**Suggested Fix:**
Fix the overdue check condition from `dueDate < today` to `dueDate <= today` so that records due on the current date are correctly flagged as overdue, consistent with REQ-06.

---

## BUG-07 

| Attribute | Detail |
|-----------|---------|
| **Bug ID** | BUG-07 |
| **Related TC** | TC-26, TC-27 |
| **Related REQ** | REQ-07 |
| **Severity** | High |
| **Reported By** | Lê Đức Anh |
| **Date Reported** | 19/05/2026 |
| **Status** | Open |

**Title:**
Email validation logic is incorrect — valid emails are rejected and invalid emails are accepted

**Environment:**
- Browser: Chrome Version 148.0.7778.168
- Operating System: Windows
- Interface Language: Vietnamese

**Preconditions:**
Logged in as Librarian (librarian@library.com / admin123). Add Member form is accessible.

**Steps to Reproduce (Case 1 — Valid email rejected):**
1. Enter 'librarian@library.com' / 'admin123' and click 'Login'
2. Click the 'Add Member' icon
3. Enter 'Nguyễn Văn A', email 'newuser@gmail.com', phone '0901234567'
4. Click 'Add Member' button

**Expected Result (Case 1):**
 System displays "Member added successfully! ID: MEM007" 

**Actual Result (Case 1):**
 System displays "Invalid email format." 

---
**Steps to Reproduce (Case 2 — Invalid email accepted):**
1. Enter 'librarian@library.com' / 'admin123' and click 'Login'
2. Click the 'Add Member' icon
3. Enter 'Nguyễn A', email `user@domain`, phone '0901234567'
4. Click 'Add Member'

**Expected Result (Case 2):**
 System displays "Invalid email format." and rejects the request.

**Actual Result (Case 2):**
 System displays "Member added successfully! ID: MEM007" 

**Impact:**
The email validation logic is broken in both directions — it rejects valid emails and accepts invalid ones. This prevents librarians from adding legitimate members while allowing corrupt data to be stored in the system, compromising data integrity and member contact capability as defined in REQ-07.

**Evidence:**
![alt text](image-4.png)
![alt text](image-5.png)

**Suggested Fix:**
Review and correct the email validation logic to enforce exactly 2 conditions:
- Must contain '@'
- Domain part after '@' must contain at least one dot (e.g., domain.com)

Emails such as `newuser@gmail.com` must be **accepted**. Emails such as `user@domain` must be **rejected**.
