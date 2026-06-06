# Test Cases — Bảng trường hợp kiểm thử

| Thông tin | |
|---|---|
| **Nhóm** | Nhóm 21 |
| **Ngày tạo** | 19/05/2026 |
| **Hệ thống** | https://stqa.rbc.vn |
| **Tham chiếu** | SRS v1.0 |

---

## Step 1: Input Domain Modeling (IDM)

### IDM — Login (REQ-01)

| Characteristic | Block | Representative Value | Expected Result |
|---|---|---|---|
| Does the email exist in the DB? | Yes | `librarian@library.com` | Login successful |
| | No | `noone@email.com` | Show message "Member not found" |
| Is the password correct? | Correct | `admin123` | Login successful |
| | Incorrect | `wrongpass` | Show message "Incorrect password" |
| Is the input field empty? | Not empty | (any value) | Process normally |
| | Empty | `""` | Show message "Please enter email and password" |

### IDM — View Book List (REQ-02)

| Characteristic | Block | Representative Value | Expected Result |
|---|---|---|---|
| Access permission | Logged in (Librarian) | Account `librarian@library.com` | Can view the full book list |
| | Logged in (Member) | Account `ba.nguyen@email.com` | Can view the full book list |
| | Not logged in (Guest) | N/A | Redirected to the login page / access denied |
| Real-time update | Status changes within the same session | Borrow BOOK001 in the same tab | BOOK001 status immediately changes to "Borrowed" without refresh |
| | No change | Newly opened Books tab | Display normally based on seed data |
| Information display | Book has complete information | BOOK002 (Data Structures and Algorithms) | Display all 5 columns: Title, Author, Category, Year Published, Status |

### IDM — Search Books (REQ-03)

| Characteristic | Block | Representative Value | Expected Result |
|---|---|---|---|
| Does the keyword exist in the DB? | Yes (book title) | `"Flutter"` | Display books containing "Flutter" in the title |
| | Yes (author name) | `"Nguyễn Minh Đức"` | Display books by Nguyễn Minh Đức |
| | No | `"XYZ123"` | Empty list + message "No books found" |
| Case sensitivity | Lowercase | `"flutter"` | Same result as "Flutter" (case-insensitive according to the SRS) |
| | Uppercase | `"FLUTTER"` | Same result as "Flutter" |
| Filter by category | Valid category (according to SRS section 3.4) | `"Management"` | Display only BOOK004, BOOK012, BOOK013 |

### IDM — Borrow Books (REQ-04)

| Characteristic | Block | Representative Value | Expected Result |
|---|---|---|---|
| Book status | Available | BOOK001 | Borrow allowed |
| | Borrowed | BOOK003 | Borrow not allowed |
| | Lost | BOOK007 | Borrow not allowed |
| Member status | Active | MEM002 (ba.nguyen) | Borrow allowed |
| | Suspended | MEM004 (cu.le) | Reject, with the correct reason "suspended" |
| | Expired | MEM005 (binh.pham) | Reject, with the correct reason "expired" |
| Number of books currently borrowed | < 3 (BVA: 0, 1, 2) | MEM006 (0 books) | Borrow allowed |
| | = 3 (BVA: limit) | Member has already borrowed 3 books | Reject, with limit exceeded message |

### IDM — Return Books (REQ-05)

| Characteristic | Block | Representative Value | Expected Result |
|---|---|---|---|
| Ownership status | Borrowed by the current member | BR001 (MEM002 borrowing BOOK003) | System allows the return action |
| | Not borrowed by the current member | BOOK002 (not in the current user’s borrowed list) | Hide the "Return Book" button or show error "You did not borrow this book" |
| Return timing (compared to dueDate) | Return before or on due date (`Return Date ≤ Due Date`) | Return a book 2 days early | Return successful, book changes to "Available", NO warning |
| | Return after due date (`Return Date > Due Date`) | BR001 (dueDate 15/09/2024 — overdue) | Return successful, book changes to "Available", system shows an overdue warning |

### IDM — Overdue Processing (REQ-06)

| Characteristic | Block | Representative Value | Expected Result |
|---|---|---|---|
| Compare `dueDate` and `Now` (when Librarian clicks "Check Overdue") | Not yet due (`dueDate > Now`) | A record with dueDate tomorrow | No change, keep status "Borrowed" |
| | Due today (`dueDate = Now`) | A record with dueDate today | Change status to "Overdue" (according to SRS: `dueDate ≤ current date`) |
| | Past due date (`dueDate < Now`) | BR001 (dueDate 15/09/2024) | Change status to "Overdue" |
| Permission to trigger function | Librarian | Login as `librarian@library.com` | Show the "Check Overdue" button and allow it to run |
| | Member | Login as a member account | Hide the "Check Overdue" button or deny access |
| Scope of overdue records display | Librarian viewing the list | Overdue records of MEM002, MEM006, ... | Display the ENTIRE list of overdue records of all members |
| | Member viewing the list | MEM002 account | ONLY display overdue records belonging to MEM002, completely hide other members’ records |

### IDM — Member Management (REQ-07)

| Characteristic | Block | Representative Value | Expected Result |
|---|---|---|---|
| Access permission | Librarian (librarian) | Login with librarian account | Show the "Add Member" form and allow actions |
| | Member (member) | Login with member account | Hide the "Add Member" function or show "Access denied" |
| Email format | Valid (has `@` and `.` in the domain) | `user@domain.com` | Pass validation, continue processing |
| | Invalid (has `@` but missing `.` in the domain) | `user@domain` | Block creation, show invalid email format error |
| | Invalid (missing `@`) | `userdomain.com` | Block creation, show invalid email format error |
| Email uniqueness (against DB) | Email does not exist | `newuser@gmail.com` | Add member successfully, show success message |
| | Email already exists | `librarian@library.com` (already in seed data) | Block creation, show error message "Email already exists" |
| Data completeness (Name, Email, Phone) | Fully filled | Name, Email, Phone all provided | Process normally |
| | Missing required information | Leave Name blank | Show error requesting all required fields |

### IDM — Borrow Record Lookup (REQ-08)

| Characteristic | Block | Representative Value | Expected Result |
|---|---|---|---|
| Role / data access permission | Librarian | Login as librarian | Show all borrow records in the system (including those of all members) |
| | Member | Login as MEM002 | Show only borrow records belonging to MEM002. Absolutely do not show records of MEM003, MEM006, ... |
| Borrow record status display | Borrowed | A record that has not been returned and is not overdue | Status column displays the exact text "Borrowed" |
| | Returned | A completed return record (BR002) | Status column displays the exact text "Returned" |
| | Overdue | A record marked as overdue | Status column displays the exact text "Overdue" |
| Information integrity | Borrow record exists in the database | BR001 | Display all 5 fields: Record ID, Book Title, Borrow Date, Due Date, Status |
| | No borrow records | Newly created user, has never borrowed a book | Display empty list message ("You do not have any borrow records") |

---

## Step 1.5: Decision Table — Borrow Books (REQ-04)

> **Bonus B2**: A complete decision table for the Borrow Book feature. Input combines 3 independent conditions (book status × member status × number of books currently borrowed) → generates all possible rules. This serves as the source for deriving test cases TC-14 → TC-18.

### Conditions / Actions

| # | Condition / Action | R1 | R2 | R3 | R4 | R5 | R6 |
|---|---------------------|----|----|----|----|----|----|
| C1 | Book = "Available"? | Y | N | Y | Y | Y | Y |
| C2 | Member = Active? | Y | – | N (Suspended) | N (Expired) | Y | Y |
| C3 | Number of books currently borrowed < 3? | Y | – | – | – | N (= 3) | N (> 3 — invalid per business rule) |
| A1 | Allow borrowing, set dueDate = today + 14 | ✓ |   |   |   |   |   |
| A2 | Reject — "Book is already borrowed" / Book is Lost |   | ✓ |   |   |   |   |
| A3 | Reject — "Account is suspended" |   |   | ✓ |   |   |   |
| A4 | Reject — "Account has expired" |   |   |   | ✓ |   |   |
| A5 | Reject — "3-book borrowing limit reached" |   |   |   |   | ✓ | ✓ |

> `Y` = Yes, `N` = No, `–` = Don't care (the rule is already triggered by a prior condition; subsequent conditions need not be checked).

### Rule → Test Case Mapping

| Rule | Test Case | Technique | Notes |
|------|-----------|-----------|-------|
| R1 (Happy path) | **TC-14** | Decision Table | MEM006 (Active, 1 book borrowed) borrows BOOK001 (Available) → allowed |
| R2 (Book unavailable) | **TC-15** | Decision Table | BOOK003 already borrowed by MEM002 in seed data → reject |
| R3 (Member Suspended) | **TC-16** | Decision Table | MEM004 (Le Can Cu) status Suspended → reject with message **"Suspended"** (BUG-02 detects wrong message returned) |
| R4 (Member Expired) | **TC-17** | Decision Table | MEM005 (Pham Trung Binh) status Expired → reject with message **"Expired"** |
| R5 (Boundary = 3) | **TC-18** | BVA + Decision Table | MEM006 has exactly 3 books borrowed → attempting to borrow a 4th must be rejected (BUG-03 detects system still allows borrowing) |
| R6 (Beyond boundary) | (Implicit) | BVA | Theoretically unreachable because R5 already blocks it — only test if a bug is found at R5 |

### Design Analysis

- **Why Decision Table for REQ-04?**
  REQ-04 involves **multiple independent conditions** that interact with one another (book × member × quantity). Equivalence Partitioning alone can only cover each characteristic in isolation and may miss interactions between them. A Decision Table guarantees coverage of **every meaningful combination**.
- **Why include R6 (> 3 books)?**
  This is a rule that "should never be reached if R5 works correctly" — but if R5 has a bug (such as BUG-03), R6 becomes a real reachable state. The Decision Table helps surface this risk.
- **Why are Suspended and Expired treated as two separate rules?**
  Per SRS REQ-04: "The rejection reason must be described **accurately** (suspended ≠ expired)." Merging them into one rule would prevent detection of BUG-02.
---

## Step 2: Test Cases

### Group 1 — Login (REQ-01)

| TC ID | Test Objective | Preconditions | Steps | Input Data | Expected Result | REQ | Technique |
|-------|----------------|---------------|-------|------------|----------------|-----|-----------|
| TC-01 | Verify successful login with a valid account | Login page has been opened; seed data unchanged | 1. Enter email. 2. Enter password. 3. Click **Login**. | Email: `librarian@library.com`; Password: `admin123` | Login successful, redirect to the homepage. AppBar displays "Nguyễn Thủ Thư" + role "Librarian". | REQ-01 | EP (class: existing email + correct password) |
| TC-02 | Verify login with a non-existing email in the DB | Login page has been opened | 1. Enter email. 2. Enter password. 3. Click **Login**. | Email: `random@email.com`; Password: `123` | System blocks login, display message: "Member not found". | REQ-01 | EP (class: non-existing email) |
| TC-03 | Verify login with a correct email but incorrect password | Login page has been opened | 1. Enter an existing email. 2. Enter the wrong password. 3. Click **Login**. | Email: `ba.nguyen@email.com`; Password: `wrongpassword` | System blocks login, display message: **"Incorrect password"** | REQ-01 | EP (class: incorrect password) |
| TC-04 | Reject login when both fields are empty | Login page has been opened | 1. Leave the Email field empty. 2. Leave the Password field empty. 3. Click **Login**. | Email: `""`; Password: `""` | Display message "Please enter email and password". No navigation occurs. | REQ-01 | EP (class: empty input) |

### Group 2 — View Book List (REQ-02)

| TC ID | Test Objective | Preconditions | Steps | Input Data | Expected Result | REQ | Technique |
|-------|----------------|---------------|-------|------------|----------------|-----|-----------|
| TC-05 | View the book list successfully as a Member | Logged in as a Member; seed data available | 1. In the menu bar, click the **Books** tab. | Account: MEM002 | The system displays 20 books. Each book displays complete information: Title, Author, Category, Published Year, Status (Available / Borrowed / Lost). | REQ-02 | EP (role class: logged-in Member) |
| TC-06 | View the book list successfully as a Librarian | Logged in as a Librarian; seed data available | 1. In the menu bar, click the **Books** tab. | Account: LIB001 | The system displays 20 books with complete information, similar to the Member view. | REQ-02 | EP (role class: logged-in Librarian) |
| TC-07 | Block access to the book list page when not logged in (Guest) | Browser is in a not-logged-in state (clear cookies/cache) | 1. Enter the system URL directly into the address bar. 2. Press **Enter**. | URL: `https://stqa.rbc.vn` (plus internal path if any) | The system automatically redirects to the Login page and does not display the book list. | REQ-02 | EP (role class: Guest) |
| TC-08 | Verify real-time book status update within the same session | Logged in as MEM006 (currently borrowing 1 book - BOOK013); Books tab is open; BOOK001 is currently "Available" | 1. On the **Books** tab, note that BOOK001 is "Available". 2. Click **Borrow** on BOOK001 and confirm. 3. Without refreshing the page, return to the **Books** tab. | Book: BOOK001 | BOOK001 status on the Books tab immediately changes from "Available" to "Borrowed" without pressing F5. | REQ-02 | EP (data class: status change event within the session) |

### Group 3 — Search & Filter Books (REQ-03)

| TC ID | Test Objective | Preconditions | Steps | Input Data | Expected Result | REQ | Technique |
|-------|----------------|---------------|-------|------------|----------------|-----|-----------|
| TC-09 | Successfully search by Book Title (basic flow) | Logged in; seed data includes BOOK001 "Lập trình Flutter cơ bản" | 1. Enter a keyword into the search box. 2. Press **Enter** (or click the Search button). | Keyword: `Flutter` | The list only displays BOOK001 (a title containing "Flutter"). | REQ-03 | EP (keyword matches book title class) |
| TC-10 | Search by Author Name | Logged in; seed data has BOOK001 and BOOK009 by author "Nguyễn Minh Đức" | 1. Enter a keyword into the search box. 2. Press **Enter**. | Keyword: `Nguyễn Minh Đức` | The list displays BOOK001 and BOOK009 (both by Nguyễn Minh Đức). | REQ-03 | EP (keyword matches author name class) |
| TC-11 | Search with a keyword that does not exist in the system | Logged in; no book or author contains the string "XYZ123" | 1. Enter a keyword into the search box. 2. Press **Enter**. | Keyword: `XYZ123` | The list is empty. The message "No books found" appears. | REQ-03 | EP (non-existing keyword class) |
| TC-12 | Search without case sensitivity (uppercase/lowercase) | Logged in; seed data includes BOOK001 "Lập trình Flutter cơ bản" | 1. Enter an uppercase keyword into the search box. 2. Press **Enter**. | Keyword: `FLUTTER` | Same result as TC-09: BOOK001 is still displayed (case-insensitive according to the SRS). | REQ-03 | EP (letter case class: uppercase — expected to match lowercase) |
| TC-13 | Filter books by category — lowercase input | Logged in. Books tab open | 1. In the filter, enter `"công nghệ"`. 2. Observe the results. | Category: `"công nghệ"` | Display **8 Technology books**: BOOK001, BOOK002, BOOK003, BOOK005, BOOK008, BOOK009, BOOK010, BOOK011. The filter is **case-insensitive**. | REQ-03 | EP |

### Group 4 — Borrow Books (REQ-04)

| TC ID | Test Objective | Preconditions | Steps | Input Data | Expected Result | REQ | Technique |
|-------|----------------|---------------|-------|------------|----------------|-----|-----------|
| TC-14 | Borrow a book successfully (basic flow / happy path) | Member MEM006 is Active and currently borrowing BOOK013 (1 book). BOOK001 status is "Available". | 1. Login as MEM006. 2. Open the Books tab. 3. Select BOOK001. 4. Click the **Borrow Book** button. | Account: `MEM006`; Book: `BOOK001` | Borrow successful. Book status updates to "Borrowed". The system records the due date as 14 days from the current date (according to SRS REQ-04). | REQ-04 | Decision Table (Book=Available × Member=Active × <3 books → Allow borrowing) |
| TC-15 | Reject borrowing when the book is not in "Available" status (Borrowed) | Active member, currently borrowing fewer than 3 books. BOOK003 status is "Borrowed" (borrowed by MEM002 in seed). | 1. Login as any Active account (e.g. MEM003). 2. Open BOOK003 details. 3. Attempt to borrow the book. | Book: `BOOK003` | Borrow not allowed. The borrow button is disabled (disabled) **OR** the system displays the error message `"Book already borrowed"`. | REQ-04 | Decision Table (Book=Borrowed → Reject, regardless of member/number of books) |
| TC-16 | Reject borrowing when the member account is "Suspended" | Member MEM004 (Lê Cần Cù) status is "Suspended". Book is available (BOOK001). | 1. Login as MEM004. 2. Select BOOK001 which is "Available". 3. Click the **Borrow Book** button. | Account: `MEM004`; Book: `BOOK001` | Borrow rejected. Display the error message stating the correct reason **"suspended"** (according to the SRS: suspended ≠ expired). | REQ-04 | Decision Table (Member=Suspended → Reject with a separate message) |
| TC-17 | Reject borrowing when the member account is "Expired" | Member MEM005 (Phạm Trung Bình) status is "Expired". Book is available (BOOK001). | 1. Login as MEM005. 2. Select BOOK001 which is "Available". 3. Click the **Borrow Book** button. | Account: `MEM005`; Book: `BOOK001` | Borrow rejected. Display the error message stating the correct reason **"expired"** (the SRS requires it to be distinguished from "suspended"). | REQ-04 | Decision Table (Member=Expired → Reject with a separate message) |
| TC-18 | Reject borrowing when the borrowing limit of 3 books has been reached | Active member currently borrowing exactly 3 books. Newly borrowed book is available. | 1. Login as MEM006. 2. (Setup) Borrow BOOK001, BOOK002 so that together with BOOK013 there are 3 books. 3. Select BOOK004 which is "Available". 4. Click the **Borrow Book** button. | Account: `MEM006` (already borrowed 3 books); 4th book: `BOOK004` | Borrow rejected. The system displays the error message: "Member has reached the limit of 3 books". | REQ-04 | BVA (upper boundary: number of books = 3 → +1 = 4, exceeding the limit) |

### Group 5 — Return Books (REQ-05)

| TC ID | Test Objective | Preconditions | Steps | Input Data | Expected Result | REQ | Technique |
|-------|----------------|---------------|-------|------------|----------------|-----|-----------|
| TC-19 | Return a book successfully before the due date (basic flow) | Logged in as MEM006 borrowing BOOK013 (record BR003, dueDate 15/10/2024). (Simulated) The test time must be before 15/10/2024 to ensure `Return Date < dueDate`. | 1. Login as MEM006. 2. Open the **My Borrow/Return** tab. 3. Select record BR003 (BOOK013). 4. Click the **Return Book** button. | Record: `BR003` | Return successful. BOOK013 status updates to "Available". NO overdue warning is displayed. | REQ-05 | EP (class: return before due date — `Return Date < dueDate`) |
| TC-20 | Return a book on the due date (boundary `=` check) | Logged in. Member is borrowing 1 book. Current date = the record’s due date (must be set up or use a suitable record). | 1. (Setup) Borrow 1 book. 2. (Simulate) Wait until the exact due date or use a record with dueDate = today. 3. Open the **My Borrow/Return** tab. 4. Click the **Return Book** button. | Record with dueDate = today | Return successful. Book status updates to "Available". NO overdue warning is displayed (because `Return Date ≤ dueDate`). | REQ-05 | BVA (boundary at `Return Date = dueDate`) |
| TC-21 | Return a book after due date — show overdue warning | Logged in as MEM002 borrowing BOOK003 (record BR001, dueDate 15/09/2024 — overdue compared to today). No need to run "Check Overdue" first. | 1. Login as MEM002. 2. Open the **My Borrow/Return** tab. 3. Find record BR001 (borrowing BOOK003). 4. Click the **Return Book** button directly. | Record: `BR001` (BOOK003 overdue) | Return successful. BOOK003 status updates to "Available". The system automatically detects overdue status and shows an overdue warning (according to SRS REQ-05). | REQ-05 | BVA (beyond boundary `Return Date > dueDate`) |
| TC-22 | Reject returning a book not owned by the member | Logged in as MEM003. BOOK013 is being borrowed by MEM006 (BR003), not by MEM003. | 1. Login as MEM003. 2. Open the **My Borrow/Return** tab. 3. Try to perform the return action for BOOK013. | Book: `BOOK013` (borrowed by MEM006) | The "Return Book" button for BOOK013 does not appear in MEM003’s interface (because it is not in MEM003’s borrowed list) OR if manually triggered, show the error "You did not borrow this book". | REQ-05 | Decision Table (Role=Member × Own record=No → Hide return button) |

### Group 6 — Overdue Processing (REQ-06)

| TC ID | Test Objective | Preconditions | Steps | Input Data | Expected Result | REQ | Technique |
|-------|----------------|---------------|-------|------------|----------------|-----|-----------|
| TC-23 | Check overdue — exact due date (boundary BVA) | Logged in as Librarian. There is a record with **dueDate = today** | 1. Click **"Check Overdue"**. 2. Observe the record with dueDate = today. | dueDate = today | The record **must** be marked as **"Overdue"** (SRS: `dueDate ≤ today` — including the exact due date). | REQ-06 | BVA |
| TC-24 | Do not mark overdue when not yet due (`dueDate > Now`) | Logged in as Librarian. There is a record with **dueDate > today** | 1. Click **"Check Overdue"**. 2. Observe the record with dueDate > today. | Click button | The newly created record’s status does not change; it remains "Borrowed" (because dueDate > Now). | REQ-06 | BVA (outside boundary `dueDate > Now` — does not satisfy the trigger condition) |
| TC-25 | Member cannot trigger "Check Overdue" | Logged in as MEM002 (Member). Seed data available. | 1. Open the **Borrow/Return** tab. 2. Observe the toolbar / interface. | Account: MEM002 | The "Check Overdue" button is NOT displayed for Members (according to SRS section 4.2 — Librarian only). | REQ-06 | EP (role class: Member — no permission) |

### Group 7 — Member Management (REQ-07)

| TC ID | Test Objective | Preconditions | Steps | Input Data | Expected Result | REQ | Technique |
|-------|----------------|---------------|-------|------------|----------------|-----|-----------|
| TC-26 | Add a new member successfully (basic flow) | Logged in as Librarian. Email `newuser@gmail.com` does not exist in the DB. | 1. Open the **Members** tab. 2. Choose "Add Member". 3. Enter valid information. 4. Click **Save / Add**. | Name: `Nguyễn Văn A`<br>Email: `newuser@gmail.com`<br>Phone: `0901234567` | Member added successfully, success message displayed. The new member appears in the list. | REQ-07 | EP (valid data class: valid email + non-existing + complete fields) |
| TC-27 | Add a member with an invalid email format (missing `.` in the domain) | Logged in as Librarian. Open the Add Member form. | 1. Enter the information. 2. Enter an email with `@` but missing `.` in the domain. 3. Click **Add**. | Name: `Nguyễn A`<br>Email: `user@domain`<br>Phone: `0901234567` | Block creation. The system shows an invalid email format error (according to SRS REQ-07: email must contain `@` AND `.` in the domain). | REQ-07 | EP (invalid email format class: missing `.` in domain) |
| TC-28 | Add a member with an email that already exists in the database | Logged in as Librarian. Email `librarian@library.com` already exists (according to seed data). | 1. Enter the information. 2. Enter an existing email. 3. Click **Add**. | Name: `Nguyễn B`<br>Email: `librarian@library.com`<br>Phone: `0807654321` | Block creation. The system shows an error message. | REQ-07 | EP (email uniqueness violation: already exists) |
| TC-29 | Reject adding a member when Name is left blank | Logged in as Librarian. Open the Add Member form. | 1. Leave the Name field blank. 2. Enter valid Email and Phone. 3. Click **Add**. | Name: `""`<br>Email: `valid@email.com`<br>Phone: `0901234567` | Block creation. Display a message asking to enter the Name field (required field). | REQ-07 | EP (empty input class: missing required field) |

### Group 8 — Borrow Record Lookup (REQ-08)

| TC ID | Test Objective | Preconditions | Steps | Input Data | Expected Result | REQ | Technique |
|-------|----------------|---------------|-------|------------|----------------|-----|-----------|
| TC-30 | Librarian can view all borrow records with complete information | Logged in as Librarian. DB contains 5 records BR001-BR005 in multiple statuses (Borrowed, Returned, Overdue). | 1. Login as `librarian@library.com`. 2. Open the **Borrow/Return** tab (Lookup section). | Account: LIB001 | Display ALL 5 borrow records in the system. Each record shows all 5 fields: Record ID, Book Title, Borrow Date, Due Date, Status (the status text is displayed correctly). | REQ-08 | EP (role class: Librarian — full access) |
| TC-31 | Ensure Members cannot look up another member’s borrow records | Logged in as MEM002. The system contains borrow records of MEM006 in the DB. | 1. Login as MEM002. 2. Open the **Borrow Record Lookup** tab. 3. Enter another member’s ID / information (`MEM006`) into the lookup field. 4. Click **Search / Lookup**. | Lookup member: `MEM006` | The system denies access to MEM006’s data. No borrow records of MEM006 are displayed and a suitable message is shown (for example: `"Access denied"`). | REQ-08 | Decision Table (Role=Member × Lookup another member → Denied) |
| TC-32 | Check display when a Member has no borrow records (Empty state) | Logged in with a newly created Member account made by the Librarian during this session (e.g. created by TC-26). This member has never borrowed any book. | 1. Log out the Librarian, then log in with the newly created member account. 2. Open the **Borrow/Return** tab. | Newly created member account | The screen shows an empty list (e.g. "You do not have any borrow records"). The system does not crash when data is missing. | REQ-08 | EP (data class: no records — empty state) |


---

## Summary

| Functional Group | No. of TC | Covered REQ | Applied Techniques |
|----------------|-------|---------|----------------------|
| Group 1 — Login | 4 (TC-01 → TC-04) | REQ-01 | EP |
| Group 2 — View Book List | 4 (TC-05 → TC-08) | REQ-02 | EP |
| Group 3 — Search & Filter Books | 5 (TC-09 → TC-13) | REQ-03 | EP |
| Group 4 — Borrow Books | 5 (TC-14 → TC-18) | REQ-04 | Decision Table, BVA |
| Group 5 — Return Books | 4 (TC-19 → TC-22) | REQ-05 | EP, BVA, Decision Table |
| Group 6 — Overdue Processing | 3 (TC-23 → TC-25) | REQ-06 | Decision Table, BVA, EP |
| Group 7 — Member Management | 4 (TC-26 → TC-29) | REQ-07 | EP |
| Group 8 — Borrow Record Lookup | 3 (TC-30 → TC-32) | REQ-08 | EP, Decision Table |
| **Total** | **32** | **REQ-01 → REQ-08 (8/8 covered)** | **EP + BVA + Decision Table** |
