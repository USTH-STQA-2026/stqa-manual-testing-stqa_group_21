# Test Summary — Quality Assurance Report

> This is a Quality Assurance activity — evaluating the overall quality of the ABC Library system based on the execution results of 32 test cases according to SRS v1.0.

---

## 1. Team Information

| Item | Details |
|------|---------|
| Team | Group 21 |
| Class | STQA — USTH |
| Report Date | 21/05/2026 |
| System Under Test | https://stqa.rbc.vn — v1.0 |
| Reference | SRS v1.0 (REQ-01 → REQ-08) |

### Team Members

| # | Student ID | Full Name | Role | Main Contribution |
|---|------------|-----------|------|-------------------|
| 1 | 23BA14068 | Nguyễn Tiến Dũng | Team Leader | Coordination, IDM design, BUG-03 |
| 2 | 23BA14005 | Lê Đức Anh | Member | TC REQ-07, BUG-07 |
| 3 | 23BA14057 | Hoàng Lê Anh Đức | Member | TC REQ-04/05/08, BUG-02, BUG-05 |
| 4 | 23BA14305 | Nguyễn Cao Tuấn | Member | TC REQ-05, BUG-04 |
| 5 | 23BA14105 | Nguyễn Minh Hiếu | Member | TC REQ-03/06, BUG-01, BUG-06 |

---

## 2. Results Overview

| Metric | Value |
|--------|-------|
| Total test cases | 32 |
| Pass | 22 |
| Fail | 9 |
| Blocked | 1 |
| Not Run | 0 |
| Pass Rate | 68.75% |
| Bugs found | 7 (BUG-01 → BUG-07) |

### 2.1. Distribution by Functional Group

| Functional Group | REQ | TC | Pass | Fail | Blocked | Pass Rate | Assessment |
|------------------|-----|----|------|------|---------|-----------|------------|
| Login | REQ-01 | 4 | 4 | 0 | 0 | 100% | ✅ Stable, complete error messages per SRS |
| View Book List | REQ-02 | 4 | 4 | 0 | 0 | 100% | ✅ Good real-time update, correct access control |
| Search & Filter Books | REQ-03 | 5 | 4 | 1 | 0 | 80% | ⚠ Category filter is case-sensitive (BUG-01) |
| Borrow Books | REQ-04 | 5 | 3 | 2 | 0 | 60% | ❌ 3-book limit violation, wrong Suspended message |
| Return Books | REQ-05 | 4 | 2 | 2 | 0 | 50% | ❌ Missing overdue warning, authorization flaw |
| Overdue Processing | REQ-06 | 3 | 2 | 1 | 0 | 66.7% | ⚠ Wrong boundary logic `dueDate ≤ today` |
| Member Management | REQ-07 | 4 | 2 | 2 | 0 | 50% | ❌ Email validation logic inverted |
| Borrow Record Lookup | REQ-08 | 3 | 1 | 1 | 1 | 33.3% | ❌ Critical: Member can view other members' records |

### 2.2. Bug Distribution by Severity

| Severity | Count | Bug IDs | Characteristics |
|----------|-------|---------|-----------------|
| High | 4 | BUG-03, BUG-05, BUG-06, BUG-07 | Core business rule violations or security flaws |
| Medium | 3 | BUG-01, BUG-02, BUG-04 | Wrong messages / wrong UX, main flow not broken |
| Low | 0 | — | — |

### 2.3. Bug Map ↔ REQ

| Bug ID | Related TC | REQ Violated | Bug Type |
|--------|------------|--------------|----------|
| BUG-01 | TC-13 | REQ-03 | Functional — case sensitivity |
| BUG-02 | TC-16 | REQ-04 | Functional — wrong error message |
| BUG-03 | TC-18 | REQ-04 | Business rule violation — exceeded limit |
| BUG-04 | TC-21 | REQ-05 | Missing feature — no overdue warning |
| BUG-05 | TC-22, TC-31 | REQ-05, REQ-08 | Security / Authorization |
| BUG-06 | TC-23 | REQ-06 | Boundary logic — `<` vs `≤` |
| BUG-07 | TC-26, TC-27 | REQ-07 | Validation logic inverted |

---

## 3. Test Design Techniques Used

| Technique | Applied to REQ | TC Count | Explanation |
|-----------|----------------|----------|-------------|
| EP (Equivalence Partitioning) | REQ-01, 02, 03, 05, 06, 07, 08 | ~22 TC | Divided inputs into equivalence classes: existing/non-existing email, role (Librarian/Member/Guest), keyword (exists/not), member status (Active/Suspended/Expired). Selected 1 representative per class. |
| BVA (Boundary Value Analysis) | REQ-04, 05, 06 | ~5 TC | Tested boundary values: books borrowed = 3 (upper limit), `Return Date = dueDate` (on-time boundary), `dueDate = today` (overdue boundary). |
| Decision Table | REQ-04, 05, 08 | ~7 TC | Combined multiple independent conditions (book status × member status × number of borrowed books) to generate all valid rules. See detailed table in `test-cases (1).md` Step 1.5. |

> Input Domain Modeling (IDM) was performed for all 8 REQs before writing TCs — ensuring systematic coverage and no important characteristics were missed.

---

## 4. Software Quality Analysis

### 4.1. Strengths

- REQ-01 (Login): 100% Pass, complete error messages for all 3 scenarios (wrong email / wrong password / empty fields) as specified in SRS.
- REQ-02 (View Books): Accurate real-time display, correct Guest/Member/Librarian access control.
- REQ-03 (Search by name/author): Search supports case-insensitive matching (TC-12 Pass).
- High-level access control: Members cannot see the "Check Overdue" button (TC-25 Pass) and cannot access the Members tab.
- Duplicate email validation: Correctly blocks already-existing emails (TC-28 Pass).

### 4.2. Weaknesses

- Data-level authorization (REQ-05 + REQ-08): The most critical flaw — members can look up other members' borrow records and even return books on their behalf (BUG-05). This is a Confidentiality + Integrity risk.
- Business rule not enforced (REQ-04): Allows borrowing beyond the 3-book limit (BUG-03) — directly violates system parameter in SRS section 3.4.
- Email validation logic inverted (REQ-07): Rejects valid emails while accepting invalid ones (BUG-07) — indicates reversed condition or incorrect regex.
- Wrong status differentiation message (REQ-04): Suspended ≠ Expired but the system returns the same message (BUG-02) — violates the requirement to "describe the correct rejection reason".
- Date boundary logic (REQ-06): SRS requires `dueDate ≤ today` to mark Overdue, but the system uses `<` — missing 1 day (BUG-06).
- Missing overdue warning on return (REQ-05): No warning displayed when `Return Date > dueDate` (BUG-04).
- Case-sensitive filter (REQ-03): Category filter does not follow case-insensitive rule (BUG-01) while search does — inconsistent behavior within the same module.

---

## 5. Bug Fix Priority Recommendations

> Priority criteria: Combination of Severity (technical impact) and Business Priority (impact on security, data integrity, core business rules).

| Order | Bug | Severity | Priority | Rationale |
|-------|-----|----------|----------|-----------|
| 1 | BUG-05 | High (recommend Critical) | P0 | Security flaw + data integrity. Members can view/modify other members' data → violates REQ-05 & REQ-08, legal risk if the system contains real data. Must fix before release. |
| 2 | BUG-03 | High | P0 | Core business rule violation (3-book limit). Loss of control over book circulation, impacts library operations. |
| 3 | BUG-07 | High | P1 | Email validation inverted → cannot add valid members, creates junk data. Blocks new member creation workflow. |
| 4 | BUG-06 | High | P1 | Wrong boundary logic `dueDate ≤ today` → misses records due by 1 day, affects overdue management. |
| 5 | BUG-04 | Medium | P2 | Missing overdue warning on book return → does not break flow but affects late penalty management. |
| 6 | BUG-02 | Medium | P2 | Wrong Suspended/Expired message — does not break flow, but causes incorrect handling by librarians. |
| 7 | BUG-01 | Medium | P3 | Case-sensitive category filter — easy workaround (type correct capitalization), not urgent. |

### Suggested Fix Roadmap

- Hotfix sprint (1–3 days): BUG-05, BUG-03 — security + business rule.
- Next sprint (1 week): BUG-07, BUG-06, BUG-04 — validation & logic.
- Backlog: BUG-02, BUG-01 — UI/UX polish.

---

## 6. Conclusion

ABC Library System v1.0 is NOT READY for production release.

### Reasons

1. Pass rate of 68.75% is below the typical acceptance threshold (≥ 90% for release candidates).
2. 4 High-severity bugs including 1 security/authorization vulnerability (BUG-05) — release would create data exposure risk.
3. 3 functional groups with Pass Rate ≤ 50%: Return Books (50%), Member Management (50%), Borrow Record Lookup (33.3%) — uneven quality.
4. Direct violations of at least 4 REQs in SRS: REQ-04 (3-book limit), REQ-05 (overdue warning), REQ-07 (email validation), REQ-08 (data access control).

### Conditions for Release

- Fix all High-severity bugs (BUG-03, 05, 06, 07).
- Re-test 9 Failed TCs + TC-32 Blocked → achieve 100% Pass for these TCs.
- Regression test all 32 TCs → Pass rate ≥ 95%.
- Additional security review for the authorization model (after fixing BUG-05).

---

## 7. Lessons Learned

- SRS is the source of truth: Many bugs were discovered by closely cross-referencing each line of the SRS (especially Rules and Error Messages sections). Testing by "intuition" alone would miss BUG-02 (1 wrong word in message) and BUG-06 (1 wrong operator `<` vs `≤`).
- Boundary value analysis is extremely valuable: BUG-06 was only discovered when testing the boundary value `dueDate = today`. Testing "obviously overdue" values would Pass and miss the defect.
- Decision Table helps detect status differentiation errors: BUG-02 (Suspended vs Expired) was exposed when testing all rules in the decision table.
- Authorization needs bidirectional testing: BUG-05 (reading + writing other members' data) shows that authorization checks must apply to both queries and actions, not just hiding UI buttons.
- Test data setup matters: Some TCs (TC-18, TC-23) depend on prior state → the team learned to use "Reset Data" before each test group.
- Team coordination: Each member owned 1 module + cross-review helped detect inconsistencies in TC descriptions.
