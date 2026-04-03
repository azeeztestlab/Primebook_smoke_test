# PrimBooks — Regression & Smoke Test Report (v2)

**Document Type:** Comprehensive Regression + New Module Smoke Test
**Product:** PrimBooks — Cloud-Based Financial & Business Management Platform
**Testing Date:** April 2, 2026
**Prepared For:** Stakeholder Meeting (April 3, 2026)
**QA Lead:** Azeez Test Lab
**Prior Report Baseline:** March 28–31, 2026 (v4.0 Master Report — 22 bugs)
**Note:** PM confirmed Liabilities module was NOT in the PRD — LIAB-001 removed from bug list.

---

## 1. Executive Summary

This regression test was conducted against the latest PrimBooks build on `localhost:3000` to verify fixes for 21 previously reported bugs, test new/expanded modules for the first time, and assess overall platform stability after developer updates.

### High-Level Result

| Area | Outcome |
|:---|:---|
| **Previously Reported Bugs Fixed** | **0 of 6 Critical bugs fixed** |
| **New Modules Tested** | Production, Purchase, Inventory, Assets — all structurally sound |
| **New Bugs Found** | 5 new issues identified |
| **Platform Stability** | ⚠️ Persistent server errors (`fetchDashboardTotalCounts`) |
| **Audit Trail** | ✅ Fully functional — records all actions |
| **Reports Module** | ✅ Comprehensive — Financial, Sales, Purchase, Inventory reports available |

### Overall Verdict
> **PLATFORM EXPANDING RAPIDLY — BUT CRITICAL BUGS FROM ROUND 1 REMAIN UNFIXED**
>
> The dev team has added significant new functionality (Production, Purchase, expanded Payroll/Assets/CRM), but the 6 critical bugs identified in the March 28–31 test cycle remain unresolved. This must be addressed before any pilot deployment.

---

## 2. Regression Test Results — Previously Reported Bugs

### 🔴 Critical Bugs (Status After This Test)

| Bug ID | Description | Previous Status | Current Status | Verdict |
|:---|:---|:---:|:---:|:---:|
| **PAY-001** | Payroll "Create Payroll" returns 0 employees | Open | **✅ FIXED (Apr 3)** | Payroll functional, Create Payroll works |
| **HRM-001** | Average Salary is manual text input | Open | **❌ NOT FIXED** | Still manual |
| **SEC-001** | XSS payloads render raw in Record table | Open | **✅ FIXED (Apr 3)** | Script tags now render as plain text in Record, Journal, and Audit Trail |
| **LIAB-001** | Liabilities module missing | Open | **✅ REMOVED** | PM confirmed not in PRD |
| **DATA-003** | Large numbers (20+ digits) corrupt to 0 | Open | **⚠️ NOT RETESTED** | Server instability prevented test |
| **RECON-001** | Status column shows "null null" | Open | **⚠️ UNTESTABLE** | Bank Recon page now throws API error |

### 🟠 High Severity Bugs

| Bug ID | Description | Current Status | Verdict |
|:---|:---|:---:|:---:|
| **DASH-001** | Revenue KPI hardcoded "+₦51.6k" | **⚠️ CHANGED** | Now shows ₦0.00 — but still not synced to data |
| **DASH-002** | Expense KPI static placeholder | **⚠️ CHANGED** | Now shows ₦0.00 — but still not synced to data |
| **DASH-003** | Employee Headcount shows "0" | **❌ WORSE** | KPI card completely removed from dashboard |
| **HRM-002** | Dashboard ↔ HRM employee sync | **❌ NOT FIXED** | No employee count visible on dashboard |
| **PAY-002** | No error message when payroll empty | **❌ NOT FIXED** | Shows "No employee added" but no guidance |

### 🟡 Medium / Low Bugs

| Bug ID | Description | Current Status |
|:---|:---|:---:|
| **PH1-002** | Company Name not in Settings | **⚠️ PARTIAL** | Settings shows "Joe Exchange" but needs deeper check |
| **RECON-002** | Reconcile buttons off-screen | **⚠️ UNTESTABLE** | Bank Recon page broken |
| **UX-001** | Date picker defaults Jan 1 | **Not retested** |
| **CRM-002** | Invoice generation incomplete | **✅ FIXED (Apr 3)** | Full CRM pipeline now works: Customer → Order → Invoice → Quotation → Credit Notes |

---

## 3. New Module Testing — First-Time Assessment

### 3.1 Production Module ✅ FUNCTIONAL
- **Status:** Structurally sound with real validation logic
- **Features Found:**
  - KPI cards: Total Products, Completed Product, Work in Progress
  - Product Assembling History table with filters (All/Finished/WIP)
  - Create New Production form with: Customer Name, Product Name, Assigned Team/Employee, Quantity, Unit, Start/End Date
  - **BOM (Bill of Materials):** Raw Materials Requirements section with "+ Add Materials" button
  - Export and Print buttons
- **Cross-Module Integration:** Customer dropdown pulls from CRM, Employee dropdown pulls from HRM (both work!)
- **Issue:** Cannot complete production without inventory items in the raw materials dropdown
- **Rating:** ⭐⭐⭐⭐ — Well-built, just needs inventory data

### 3.2 Purchase Module ✅ PARTIALLY FUNCTIONAL
- **Sub-modules Found:** Expenses, Recurring Expenses, Vendor
- **NOTE:** "Purchase Orders" and "Bills" (mentioned in PRD) are NOT visible in sidebar — may be under development
- **Vendor CRUD:**
  - ✅ Create: "New Vendor" form works — has Title, Full Name, Display Name, Email, Phone, Company, Payment Terms, Debit/Credit accounts
  - ⚠️ **NEW BUG:** Newly created vendor did not appear in vendor list immediately after creation
- **Rating:** ⭐⭐⭐ — Needs Purchase Orders/Bills, vendor sync issue

### 3.3 Inventory Module ⚠️ PARTIALLY FUNCTIONAL
- **Sub-modules Found:** Inventory List, Inv. Adjustment
- **Create Inventory form:**
  - Type selection: Raw Material, Semi-Finished, Finished Product
  - Fields: Product Name, Product Vendor, Date, Unit, Quantity, Cost Price, Selling Price, Opening Balances
- **Issue:** Mandatory "Cost Price" field is obscured at bottom of a scrollable modal — users hit save and get a confusing validation error
- **Issue:** Product Vendor dropdown is empty (newly created vendor not syncing)
- **Rating:** ⭐⭐⭐ — Functional form but UX/sync issues

### 3.4 Assets Module ✅ COMPREHENSIVE
- **Sub-modules Found:** List of Assets, Lease, Lease Return, Dispose, Maintenance, Reserve, Depreciation
- **KPI Cards:** Total Assets (0), Value of Assets (₦0.00), Asset Acquired (0), Sold Assets (0)
- **Categories:** Unregistered Assets, Fixed Assets, Tangible Assets
- **Asset Creation:** 4-step wizard: Assets Details → Financial Info → Manufacturer Details → Maintenance & Contract
- **Depreciation page:** Loads correctly, columns: Date, Asset Name, Asset Type, Accumulated Depreciation, Net Book Value
- **Rating:** ⭐⭐⭐⭐⭐ — Most complete new module

### 3.5 Reports Module ✅ EXCELLENT
- **Financial Reports:** Profit & Loss, Balance Sheet, Equity Statement, General Ledger, Trial Balance, Cashflow, Bank Ledger
- **Other Categories:** Sales, Purchase, Inventory, Production reports
- **Audit Trail:** ✅ **FULLY FUNCTIONAL** — Real-time activity logging with:
  - Date & Time, User, Action (CREATE/UPDATE/VIEW), Description, Device Info, Module
  - Successfully records: bank creation, employee updates, department creation, report views, invoice & credit note creation
- **Rating:** ⭐⭐⭐⭐⭐ — Production-grade

### 3.6 CRM Module — RESTRUCTURED
- **Previous sub-items:** Customers, Orders, Invoices, Quotations
- **Current sub-items:** Order, Invoice, Customer, Quotation, Credit Notes
- **New feature:** Credit Notes module added
- **Add Customer form:** Comprehensive — Title, Full Name, Display Name, Email, Phone, Company, Payment Terms, Opening Balances
- **Rating:** ⭐⭐⭐⭐ — Expanded from previous version

### 3.7 Payroll Mgmt. — EXPANDED BUT CORE BUG REMAINS
- **Previous sub-items:** Employees, Department, Payroll
- **Current sub-items:** Employees, Salary, Payroll, Loan Management, Leave Mgmt., Pension, Department, Timesheet Mgmt.
- **New features:** Loan Management, Leave Mgmt., Pension, Timesheet
- **BLOCKER:** PAY-001 still active — payroll shows 0 employees
- **NEW BUG:** "Add Attendance" button in Timesheet Mgmt. is unresponsive

---

## 4. New Bugs Found in This Test

| Bug ID | Module | Description | Severity |
|:---|:---|:---|:---:|
| **STB-001** | Global | Persistent `fetchDashboardTotalCounts` server error — red "1 Issue" badge across all pages | 🔴 Critical |
| **RECON-004** | Bank Recon | Entire Bank Reconciliation page fails to load: "Failed to load bank reconciliations" | 🔴 Critical |
| **TIME-001** | Timesheet | "Add Attendance" button is unresponsive — no modal or form appears on click | 🟠 High |
| **VEND-001** | Purchase | Newly created vendor does not appear in Vendor list or Inventory vendor dropdown | 🟠 High |
| **INV-001** | Inventory | Mandatory "Cost Price" field hidden at bottom of scrollable modal — confusing validation failure | 🟡 Medium |

---

## 5. Sidebar Navigation — Full Module Map (Current Build)

```
Dashboard
Record
CRM → Order, Invoice, Customer, Quotation, Credit Notes
Production
Purchase → Expenses, Recurring Expenses, Vendor
Bank Reconciliation
Inventory → Inventory List, Inv. Adjustment
Finance → Chart of Account, Banking, Taxation, Journal
Assets → List of Assets, Lease, Lease Return, Dispose, Maintenance, Reserve, Depreciation
Payroll Mgmt. → Employees, Salary, Payroll, Loan Management, Leave Mgmt., Pension, Department, Timesheet Mgmt.
Reports → Audit Trail, Reports
Settings
Log out
```

**Notable Changes from Previous Test:**
- ✅ **NEW:** Purchase module (Expenses, Recurring Expenses, Vendor)
- ✅ **NEW:** CRM now includes Credit Notes
- ✅ **NEW:** Payroll Mgmt. expanded (Loan Mgmt, Leave Mgmt, Pension, Timesheet)
- ✅ **NEW:** Assets expanded (Lease, Lease Return, Dispose, Maintenance, Reserve)
- ❌ **MISSING:** Revenue and Expense links removed from Finance sidebar (previously accessible)
- ❌ **MISSING:** Employee Headcount KPI card removed from Dashboard
- ❌ **MISSING:** Liabilities (confirmed NOT in PRD — removed from bug list)

---

## 6. What Has Been Achieved — Meeting Walkthrough

### ✅ Good News for the Meeting
1. **New Module Architecture:** Production, Purchase, Inventory, and Assets modules are structurally sound and well-designed
2. **Audit Trail:** Fully functional activity logging — critical for compliance and accountability
3. **Reports Dashboard:** Comprehensive financial reporting framework (P&L, Balance Sheet, etc.)
4. **CRM Expansion:** Credit Notes feature added
5. **Payroll Expansion:** Loan Management, Leave Management, Pension, and Timesheet modules added
6. **Asset Management:** Most complete new module — 4-step creation wizard, depreciation tracking, lease management
7. **Dashboard Cleanup:** Hardcoded placeholder values (+₦51.6k) appear to be removed (now shows ₦0.00)

### ❌ Still Outstanding — Critical Items
1. **PAY-001:** Payroll STILL cannot see employees — salary processing completely blocked
2. **SEC-001:** XSS vulnerability STILL present — script tags visible in Record table
3. **HRM-001:** Average Salary STILL manual input — fake data reporting possible
4. **RECON-004 (NEW):** Bank Reconciliation page completely broken — API failure
5. **STB-001 (NEW):** Global server error affecting dashboard and potentially other modules

---

## 7. What's Next — Agenda & Priorities

### For Tomorrow's Meeting (April 3, 2026):
1. **Present this report** — Clear picture of progress vs outstanding items
2. **Priority discussion:** Which of the 6 unfixed critical bugs gets resolved first?
3. **Recommended priority order:**
   - P0: STB-001 (server error) — may be causing cascading failures
   - P0: PAY-001 (payroll blocked) — core business function
   - P0: RECON-004 (bank recon broken) — core selling point per PRD
   - P1: SEC-001 (XSS) — security risk
   - P1: HRM-001 (average salary) — data integrity

### Post-Meeting Action Items:
- [ ] Dev team to investigate and fix `fetchDashboardTotalCounts` API error
- [ ] Dev team to fix payroll-employee data sync
- [ ] Dev team to fix Bank Reconciliation API
- [ ] QA to conduct full CRUD testing on Purchase Orders/Bills once implemented
- [ ] QA to stress-test Production module with inventory data
- [ ] QA to verify Dashboard KPI wiring after fixes

---

## 8. Complete Updated Bug Registry

| Bug ID | Module | Description | Severity | Status |
|:---|:---|:---|:---:|:---:|
| **PAY-001** | Payroll | Payroll returns 0 employees — blocked | 🔴 Critical | ✅ FIXED (Apr 3) |
| **HRM-001** | HRM | Average Salary is manual text field | 🔴 Critical | ❌ Open |
| **SEC-001** | Security | XSS rendering in Record table | 🔴 Critical | ✅ FIXED (Apr 3) |
| **DATA-003** | Integrity | Large numbers corrupt to 0 | 🔴 Critical | ⚠️ Untested |
| **STB-001** | Global | `fetchDashboardTotalCounts` server error | 🔴 Critical | ✅ FIXED (Apr 3) |
| **RECON-004** | Bank Recon | Bank Reconciliation page API failure | 🔴 Critical | 🆕 Open |
| **DASH-003** | Dashboard | Employee Headcount KPI removed entirely | 🟠 High | ❌ Worse |
| **HRM-002** | HRM | Dashboard ↔ HRM employee sync broken | 🟠 High | ❌ Open |
| **PAY-002** | Payroll | No error/guidance when payroll table empty | 🟠 High | ❌ Open |
| **TIME-001** | Timesheet | "Add Attendance" button unresponsive | 🟠 High | 🆕 Open |
| **VEND-001** | Purchase | New vendor not syncing to list/dropdown | 🟠 High | 🆕 Open |
| **RECON-001** | Bank Recon | Status shows "null null" | 🟠 High | ⚠️ Untestable |
| **RECON-002** | Bank Recon | Reconcile buttons off-screen | 🟡 Medium | ⚠️ Untestable |
| **INV-001** | Inventory | Cost Price field hidden in modal | 🟡 Medium | 🆕 Open |
| **PH1-002** | Settings | Company Name propagation | 🟡 Medium | ⚠️ Partial |
| **UX-001** | Global | Date picker defaults Jan 1 | 🟡 Medium | ⚠️ Not retested |
| **CRM-002** | CRM | Invoice generation incomplete | 🟡 Medium | ✅ FIXED (Apr 3) |
| **UX-002** | Global | Generic error messages | 🟡 Medium | ❌ Open |
| **CRM-001** | CRM | "Add Item" button — now has ➕ Add Item | 🟢 Low | ✅ FIXED (Apr 3) |
| **UX-003** | Global | No loading spinners | 🟢 Low | ❌ Open |
| **UX-004** | Payroll | Empty table no "No data" message | 🟢 Low | ❌ Open |

**Total: 21 bugs — 5 now FIXED (Apr 3 re-test), 16 remaining (3 Critical, 6 High, 4 Medium, 2 Low)**

---

*Report generated April 2, 2026 — Azeez Test Lab*
*Updated April 3, 2026 (01:30–02:25 AM EDT) — PAY-001, SEC-001, STB-001, CRM-001, CRM-002 verified FIXED*
