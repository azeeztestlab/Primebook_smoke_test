# PrimBooks Smoke Test: Records & Security Audit (Part 2)

## 🛡️ Security Adversarial Testing
- **Objective:** Stress-test the platform against common web vulnerabilities.
- **Status:** Backend is resilient; Frontend requires output sanitization.

### 1. SQL Injection (SQLi)
- **Payload used:** `' OR 1=1 --` and `admin' #`
- **Modules Tested:** Login, Record Searching, Employee Search.
- **Result:** ✅ **PASS**.
- **Observation:** The application properly sanitizes inputs or uses parameterized queries. No data leakage or unauthorized access was achievable via basic SQLi strings.

### 2. Cross-Site Scripting (XSS)
- **Payload used:** `<script>alert('XSS')</script>`
- **Module:** Records > Expense Name
- **Result:** ⚠️ **FAIL (Frontend Rendering)**.
- **Detail:** While the script does not execute on the server, the **Records Table renders the literal script tag** in the browser.
- **Risk:** If an attacker inputs a more complex malicious script, it will execute in the browser of any user (especially admins) viewing that record. **Recommendation:** Implement front-end encoding for all user-supplied content.

## 📊 Data Integrity & Boundary Testing
- **Objective:** Identify limits of the financial ledger recording system.
- **Status:** Multiple data-entry bugs detected.

### Key Findings
1. **[BUG] Negative Opening Balances:**
   - The system allows users to enter negative values (e.g., `-5000`) for "Opening Balance" during an "Edit" action.
   - **Impact:** Can lead to negative equity or incorrect balance sheets.
2. **[BUG] 15-Digit Truncation:**
   - Entering a number with 15+ digits (e.g., `9,999,999,999,999,999`) results in the database truncating the value or storing it as zero.
   - **Impact:** Limits the system's ability to handle massive corporate transactions.
3. **[UX] Form Validation:**
   - Date pickers default to Jan 1st of the current year, requiring multiple clicks to reach current dates. No "Now" or "Today" shortcut available.

---
*Date: March 31, 2026*
*Prepared by: Azeez Test Lab*
