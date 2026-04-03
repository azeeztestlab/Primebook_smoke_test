# PrimBooks Smoke Test: Phase 2 Detailed Findings

## 🏦 Finance & Bank Reconciliation
- **Objective:** Verify transactional integrity, CSV parsing, and UI reporting.
- **Status:** Functional but requires UI/Logic remediation.

### 🔍 Key Findings
1. **[BUG] Status Display Logic:**
   - In the "Reconciliation History" table, the "Status" column renders as `null null` for all entries.
   - **Severity:** Medium (Visual/Reporting).
2. **[UX] Table Padding & Scrolling:**
   - On standard laptop resolutions (13-15"), the "Reconcile Now" buttons are pushed off-screen due to excessive column padding.
3. **[DATA] CSV Import Verification:**
   - Successfully uploaded `SAMPLE_BANK_STATEMENT.csv`. The system parsed the dates and amounts correctly, proving the backend importer is functional.

## 👥 HR & Payroll Management
- **Objective:** Validate calculation accuracy and employee data synchronization.
- **Status:** **CRITICAL BLOCKERS DETECTED**

### 🔍 Key Findings
1. **[CRITICAL] Payroll Workforce Fetching:**
   - **Scenario:** Attempts to "Create New Payroll" result in an empty "Employee Selection" table.
   - **Evidence:** Active employees exist in the HRM module with full profiles and salaries, but the Payroll module fails to query them.
   - **Severity:** Blocker (Prevents payroll processing).
2. **[BUG] Average Salary Formula Error:**
   - **Scenario:** Viewing the HR Dashboard analytics.
   - **Evidence:** The "Average Salary" KPI card shows exactly 50% of the true average. (e.g., If average is ₦200,000, card displays ₦100,000).
   - **Severity:** High (Data Accuracy).
3. **[KPI Sync]:**
   - Headcount KPI on the main dashboard shows "0" even after adding 3 employees.

---
*Date: March 31, 2026*
*Prepared by: Azeez Test Lab*
