# PrimeBook — Phase 1 Smoke Test Findings

**Date:** March 19, 2026  
**Lead QA:** Azeez  
**Environment:** localhost:3000 (XAMPP dev server)  
**Account:** qa.tester@primebook.test (Admin role)

---

## 1. Authentication

| Test | Result | Notes |
|------|--------|-------|
| **Sign Up** | ✅ Pass | Fields: Company Name, Email, Password, Confirm Password, Terms checkbox. Confirmation modal appears before creation. Redirects to login with email pre-filled. |
| **Login** | ✅ Pass | Email + password login works. Redirects to `/dashboard`. |
| **Google OAuth** | ⬜ Not Tested | Button visible but not tested on localhost. |
| **Apple OAuth** | ⬜ Not Tested | Button visible but not tested on localhost. |
| **Forgot Password** | ⬜ Not Tested | Link visible, points to `/reset-password`. |

### Sign Up Flow

The sign up form asks for Company Name, Email, Password, and Confirm Password. After filling in the fields and agreeing to the Terms of Service, a confirmation modal appears:

![Sign Up Confirmation Modal](./screenshots/03_signup_confirmation.png)

**Observation:** Sign up works correctly. The confirmation popup is a nice UX touch. However, the Company Name ("QA Test Corp") entered here does not carry over to the user profile (see Issue #5 below).

---

## 2. Dashboard

**URL:** `/dashboard` — ✅ Loaded

After login, the dashboard shows the following layout:

![Dashboard — Top Half](./screenshots/01_dashboard_top.png)

### 🔴 Issue #1: Hardcoded KPI Data

Look at the 4 white KPI cards at the top: **Total Revenue**, **Total Expenses**, **Orders**, and **Invoices**.

The main values are correct (₦0.00 and 0 — this is a fresh account). **BUT** every single card shows the exact same green text underneath:

> **↗ 12.3%  +51.634 today**

This is impossible. All four metrics are zero, so there can't be a 12.3% increase or +51.634 anything. These numbers are **hardcoded placeholder values** that the developer left in and forgot to connect to real data.

---

### 🔴 Issue #2: Cash Flow Chart Shows Fake Data

Still on the dashboard, look at the **"Cash Flow"** chart. It shows a blue line going up and down from January to December, peaking around August at ~1000.

This account was just created — there are **zero transactions**. The chart should be flat at zero or show "No data available."

Also, on the right side of the chart it displays:
- **Incoming: ₦124,358,098.54**
- **Outgoing: ₦124,358,098.54**

**₦124 MILLION** on a brand new account? This is clearly fake placeholder/demo data.

---

### Bottom Half of Dashboard

![Dashboard — Bottom Half (Full View)](./screenshots/02_dashboard_full.png)

### 🔴 Issue #3: Revenue Analysis Chart Also Shows Fake Data

The **"Revenue Analysis"** bar chart displays green (Income) and yellow (Expenses) bars for each month, with values going up to ₦100,000.

**Same problem as above** — we have zero transactions on this account. This data should not exist.

---

### ℹ️ Issue #4: "Coming Soon" Placeholders

At the very bottom of the dashboard, two sections show placeholder text:
- **"Recurrent expenses data coming soon"**
- **"Banks and credit cards data coming soon"**

These are honest "not built yet" indicators. Not a bug, but worth documenting for status tracking.

---

## 3. Settings — Profile Issue

![Settings Page](./screenshots/04_settings_page.png)

### 🟡 Issue #5: Company Name From Sign Up Not Saved to Profile

During sign up, we entered **"QA Test Corp"** as the Company Name. But in the Settings page, the profile shows:
- **"Add Your Name"** ← This should say "QA Test Corp"
- **PrimeBook Tester** (appears to be a default name)
- **qa.tester@primebook.test** ✅ Correct
- **Admin** ✅ Correct
- **Created on 3/19/2026** ✅ Correct

The company name entered during registration did not carry over to the user profile.

---

### 🟡 Issue #6: "3 Issues" Badge

During navigation, a small red badge reading **"3 Issues"** appeared in the bottom-left corner of the app (visible in several screenshots). There is no explanation of what the 3 issues refer to. This may be a developer tool or debugging widget that was not removed.

---

## 4. Module Navigation — All Modules Load ✅

Every sidebar module was clicked and verified. All loaded without errors.

| # | Module | URL | Status | What's Inside |
|---|--------|-----|--------|---------------|
| 1 | **Dashboard** | `/dashboard` | ✅ Loaded | KPI cards, Cash Flow chart, Revenue Analysis, Invoice summary |
| 2 | **Record** | `/record` | ✅ Loaded | Items table (empty), Search, "Add records" button |
| 3 | **CRM** | `/crm/order` | ✅ Loaded | **Sub-pages:** Order, Customers, Invoice, Quotation, Credit Note |
| 4 | **Production** | `/production` | ✅ Loaded | Raw materials table, Add Raw Materials button |
| 5 | **Purchase** | `/purchase/expenses` | ✅ Loaded | **Sub-pages:** Expenses, Vendors |
| 6 | **Bank Reconciliation** | `/bank-reconciliation` | ✅ Loaded | Upload Statement, Reconcile buttons, empty history table |
| 7 | **Inventory** | `/inventory/inventorylist` | ✅ Loaded | **Sub-pages:** Inventory List, Inventory Adjustment |
| 8 | **Finance** | `/finance/chart-of-account` | ✅ Loaded | **Sub-pages:** Chart of Account, Journal, Banking, Tax |
| 9 | **Assets** | `/asset/list` | ✅ Loaded | **Sub-pages:** List of Assets, Asset Categories |
| 10 | **Payroll Mgmt.** | `/hr-payroll/employees` | ✅ Loaded | **Sub-pages:** Employees, Department, Payroll, Loan Mgmt., Timesheet Mgmt., Leave Mgmt., Pension |
| 11 | **Reports** | `/reports/audit-trail` | ✅ Loaded | Audit Trail table (empty — "No activity recorded") |
| 12 | **Settings** | `/settings` | ✅ Loaded | My Profile, Notification, Organization, Currency, Manage Team, Subscription, Customization |

---

## 5. PRD Cross-Reference

Comparing the sidebar modules against what was listed in the Product Requirements Document (PRD):

| PRD Module | Sidebar Label | Status | Notes |
|------------|--------------|--------|-------|
| Dashboard | Dashboard | ✅ Present | Has placeholder data issues |
| Records | **Record** | ✅ Present | Label uses singular, PRD uses plural |
| CRM | CRM | ✅ Present | Full submenu works |
| Production | Production | ✅ Present | Functional |
| Purchases | **Purchase** | ✅ Present | Label uses singular, PRD uses plural |
| Finance | Finance | ✅ Present | Full submenu works |
| HR & Payroll | **Payroll Mgmt.** | ✅ Present | Different label from PRD |
| Inventory | Inventory | ✅ Present | PRD said "Not started" — now built! |
| Bank Reconciliation | Bank Reconciliation | ✅ Present | #1 selling point |
| Audit Trail | Under **Reports** | ✅ Present | Not standalone — is a sub-item of Reports |
| Reports | Reports | ✅ Present | Contains Audit Trail |
| Asset Management | **Assets** | ✅ Present | Shorter label than PRD |
| Settings | Settings | ✅ Present | Full submenu works |

---

## 6. Issue Summary

| # | Issue | Severity | Module | Screenshot |
|---|-------|----------|--------|------------|
| 1 | Dashboard KPIs show hardcoded "+51.634 today" and "12.3%" on a fresh account with zero data | **Medium** | Dashboard | `01_dashboard_top.png` |
| 2 | Cash Flow chart shows non-zero line graph and ₦124M incoming/outgoing on empty account | **Medium** | Dashboard | `01_dashboard_top.png` |
| 3 | Revenue Analysis bar chart shows up to ₦100K values on account with no transactions | **Medium** | Dashboard | `02_dashboard_full.png` |
| 4 | "Recurrent expenses data coming soon" and "Banks and credit cards data coming soon" placeholders | **Informational** | Dashboard | `02_dashboard_full.png` |
| 5 | Company name from sign up ("QA Test Corp") not saved to user profile — shows "Add Your Name" | **Low** | Settings | `04_settings_page.png` |
| 6 | "3 Issues" red badge in bottom-left corner — unclear what it refers to | **Low** | Global | Multiple screenshots |

---

## 7. Next Steps Checklist

My immediate action items for following testing phases:

### 🛠️ Phase 2: Functional & Data Integrity Testing
- [ ] **Record Module:** Verify "Add Record" functionality and ensure data persistence in the database.
- [ ] **CRM Workflow:** Test the full Order → Quotation → Invoice → Credit Note lifecycle.
- [ ] **Bank Reconciliation:** Upload sample statements and verify the auto-matching logic and UI feedback.
- [ ] **Payroll Run:** Execute a sample payroll and verify the calculations for loans and pensions.

### 🛡️ Phase 3: Permissions & Security
- [ ] **Role Validation:** Log in as Accountant, Auditor, and HR to verify module access restrictions.
- [ ] **Audit Trail Integrity:** Verify that every record creation/deletion is correctly logged with a timestamp.

### 🐛 Bug Management
- [ ] **Bug registry:** File formal tickets for Issues #1 through #6 identified in Phase 1.
- [ ] **Console Logging:** Review browser console for hidden errors during complex transactions.
