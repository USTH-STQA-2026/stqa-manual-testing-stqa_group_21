# 📋 Test Summary Report — ABC Library System v1.0

> **Quality Assurance Report** | Team 21 | 21/05/2026
> System: https://stqa.rbc.vn — v1.0 | Reference: SRS v1.0 (REQ-01 → REQ-08)

---

##  Team Members

| # | Student ID | Full Name | Role | Contributions |
|---|-----------|-----------|------|---------------|
| 1 | 23BA14068 | Nguyen Tien Dung | Team Lead | Coordination, IDM, BUG-03 |
| 2 | 23BA14005 | Le Duc Anh | Member | TC REQ-07, BUG-07 |
| 3 | 23BA14057 | Hoang Le Anh Duc | Member | TC REQ-04/05/08, BUG-02, BUG-05 |
| 4 | 23BA14305 | Nguyen Cao Tuan | Member | TC REQ-05, BUG-04 |
| 5 | 23BA14105 | Nguyen Minh Hieu | Member | TC REQ-03/06, BUG-01, BUG-06 |

---

##  Results Overview

| Metric | Value |
|--------|-------|
| Total test cases | **32** |
|  Pass | **22** |
|  Fail | **9** |
|  Blocked | **1** |
|  Not Run | **0** |
| Pass Rate | **68.75%** |
| Bugs found | **7** (BUG-01 → BUG-07) |

### 2.1 Distribution by Feature Group

| Feature Group | REQ | TC | Pass | Fail | Blocked | Pass Rate | Status |
|---------------|-----|----|------|------|---------|-----------|--------|
| Login | REQ-01 | 4 | 4 | 0 | 0 | 100% |  Stable |
| View Book List | REQ-02 | 4 | 4 | 0 | 0 | 100% |  Stable |
| Search & Filter | REQ-03 | 5 | 4 | 1 | 0 | 80% |  BUG-01 |
| Borrow Books | REQ-04 | 5 | 3 | 2 | 0 | 60% |  BUG-02, BUG-03 |
| Return Books | REQ-05 | 4 | 2 | 2 | 0 | 50% |  BUG-04, BUG-05 |
| Overdue Processing | REQ-06 | 3 | 2 | 1 | 0 | 66.7% |  BUG-06 |
| Member Management | REQ-07 | 4 | 2 | 2 | 0 | 50% |  BUG-07 |
| Loan Record Lookup | REQ-08 | 3 | 1 | 1 | 1 | 33.3% |  BUG-05 |

### 2.2 Bug Severity

| Severity | Count | Bug IDs |
|----------|-------|---------|
|  High | 4 | BUG-03, BUG-05, BUG-06, BUG-07 |
|  Medium | 3 | BUG-01, BUG-02, BUG-04 |
|  Low | 0 | — |

### 2.3 Bug Map ↔ REQ

| Bug ID | TC | REQ | Type |
|--------|----|-----|------|
| BUG-01 | TC-13 | REQ-03 | Functional — case sensitivity |
| BUG-02 | TC-16 | REQ-04 | Functional — wrong error message |
| BUG-03 | TC-18 | REQ-04 | Business rule violation — limit exceeded |
| BUG-04 | TC-21 | REQ-05 | Missing feature — no overdue warning |
| BUG-05 | TC-22, TC-31 | REQ-05, REQ-08 |  Security / Authorization |
| BUG-06 | TC-23 | REQ-06 | Boundary logic — `<` vs `≤` |
| BUG-07 | TC-26, TC-27 | REQ-07 | Validation logic — wrong in both directions |

---

## 🛠️ Test Design Techniques

| Technique | REQ | # TCs | How Applied |
|-----------|-----|-------|-------------|
| **EP** (Equivalence Partitioning) | REQ-01, 02, 03, 05, 06, 07, 08 | ~22 | Partitioned inputs into equivalence classes: email exists/not, role (Librarian/Member/Guest), member status (Active/Suspended/Expired). One representative per class. |
| **BVA** (Boundary Value Analysis) | REQ-04, 05, 06 | ~5 | Boundary values: books borrowed = 3 (upper limit), `Return Date = dueDate` (on-time), `dueDate = today` (overdue). |
| **Decision Table** | REQ-04, 05, 08 | ~7 | Combined multiple independent conditions (book status × member status × borrow count) to generate full rule coverage. See Step 1.5 in `test-cases.md`. |

>  **Input Domain Modeling (IDM)** was performed for all 8 REQs prior to writing test cases.

---

##  Quality Analysis

###  Strengths

- **REQ-01 (Login)** — 100% pass; all 3 error scenarios covered per SRS.
- **REQ-02 (View Books)** — Accurate real-time display; correct Guest/Member/Librarian access.
- **REQ-03 (Search)** — Case-insensitive name/author search works correctly (TC-12 pass).
- **Authorization (high-level)** — Members cannot see "Check Overdue" button (TC-25 pass).
- **Duplicate email check** — Correctly blocks already-registered emails (TC-28 pass).

###  Weaknesses

- **BUG-05** — `[CRITICAL]` Member can view and return books belonging to another member. Violates REQ-05 & REQ-08. **Confidentiality + Integrity risk.**
- **BUG-03** — System allows borrowing beyond the 3-book limit. Direct violation of SRS §3.4.
- **BUG-07** — Email validation logic inverted: rejects valid emails, accepts invalid ones.
- **BUG-02** — `Suspended` and `Expired` statuses return the same error message. Violates SRS rejection-reason requirement.
- **BUG-06** — System uses `<` instead of `≤` for overdue check; misses books due today.
- **BUG-04** — No overdue warning displayed when `Return Date > dueDate`.
- **BUG-01** — Genre filter is case-sensitive; search is not. Inconsistent behavior in same module.

---

##  Fix Priority

| # | Bug | Severity | Priority | Reason |
|---|-----|----------|----------|--------|
| 1 | BUG-05 |  High → Critical | **P0** | Data breach risk. Must fix before release. |
| 2 | BUG-03 |  High | **P0** | Core business rule broken. |
| 3 | BUG-07 |  High | **P1** | Blocks member creation workflow. |
| 4 | BUG-06 |  High | **P1** | Overdue logic off by one day. |
| 5 | BUG-04 |  Medium | **P2** | Missing overdue warning on return. |
| 6 | BUG-02 |  Medium | **P2** | Wrong rejection message shown to librarian. |
| 7 | BUG-01 |  Medium | **P3** | Easy workaround; low urgency. |

### Roadmap

Sprint Hotfix  (1–3 days) │ BUG-05, BUG-03       → Security + business rule
Sprint Next    (1 week)   │ BUG-07, BUG-06, BUG-04 → Validation & logic
Backlog                   │ BUG-02, BUG-01        → UI/UX polish
---

##  Conclusion

> ** ABC Library System v1.0 is NOT READY for production release.**

### Reasons

1. Pass rate **68.75%** — below the standard release threshold of ≥ 90%.
2. **4 High-severity bugs**, including 1 security/authorization vulnerability (BUG-05).
3. **3 feature groups with Pass Rate ≤ 50%**: Return Books (50%), Member Management (50%), Loan Record Lookup (33.3%).
4. **At least 4 REQs directly violated**: REQ-04, REQ-05, REQ-07, REQ-08.

### Release Conditions

- [ ] Fix all High bugs: BUG-03, BUG-05, BUG-06, BUG-07
- [ ] Re-test all 9 failed TCs + TC-32 (Blocked) → 100% pass
- [ ] Regression test all 32 TCs → Pass rate ≥ 95%
- [ ] Security review of authorization model (post BUG-05 fix)

---

##  Lessons Learned

| # | Lesson |
|---|--------|
| 1 | **SRS is the source of truth** — BUG-02 (`<` vs message text) and BUG-06 (`<` vs `≤`) were only caught by line-by-line SRS comparison. |
| 2 | **BVA is high-value** — BUG-06 was only detectable at the exact boundary `dueDate = today`. |
| 3 | **Decision Tables surface status-differentiation bugs** — BUG-02 (Suspended vs Expired) required full rule coverage to expose. |
| 4 | **Test authorization in both directions** — BUG-05 shows UI hiding ≠ backend enforcement. |
| 5 | **Test data setup matters** — TC-18 and TC-23 depend on prior state; "Restore Data" steps were added before each test group. |
| 6 | **Cross-review helps** — Assigning one module per member + cross-review caught TC description inconsistencies. |

---


