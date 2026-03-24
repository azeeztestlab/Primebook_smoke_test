# PrimBooks — Smoke Test Report (Part 1 of 2)
## System Accessibility, Core Navigation & Initial Risk Assessment

**Date:** March 23, 2026  
**Lead QA:** Azeez  
**Environment:** localhost:3000 (Dev Server)  
**Account:** gundro.nodes@gmail.com (Admin role)  
**Browser:** Chrome (Latest)  
**OS:** Windows

---

## 1. Objective & Scope

The purpose of this smoke test is to verify PrimBooks' fundamental stability before investing time in deep functional testing. A smoke test answers one critical question:

> **"Is the application stable enough to test?"**

### What was tested:
- ✅ Authentication (Sign Up + Login flow)
- ✅ Negative authentication scenarios (invalid inputs, edge cases)
- ✅ Dashboard first-load integrity
- ✅ All 12 sidebar modules accessibility
- ✅ Sub-page navigation within each module
- ✅ Initial security and session observations
- ✅ UX/Accessibility quick assessment

### What was NOT tested (deferred to Phase 2):
- ❌ CRUD operations (Create, Read, Update, Delete)
- ❌ Financial calculations and data integrity
- ❌ Role-based access control
- ❌ API endpoint testing
- ❌ Cross-browser testing (Safari, Firefox, Mobile)

---

## 2. Authentication — Detailed Analysis

### 2.1 Sign Up Flow

| Step | Action | Expected | Actual | Result |
|------|--------|----------|--------|--------|
| 1 | Navigate to sign up page | Form loads | Form loads with all fields | ✅ Pass |
| 2 | Fill Company Name | Accepts text | Accepts "QA Test Corp" | ✅ Pass |
| 3 | Fill Email | Accepts valid email | Accepts email correctly | ✅ Pass |
| 4 | Fill Password | Accepts secure password | Field accepts input, masked | ✅ Pass |
| 5 | Fill Confirm Password | Matches password | Validates match | ✅ Pass |
| 6 | Check Terms of Service | Required checkbox | Must check to proceed | ✅ Pass |
| 7 | Submit | Confirmation modal | Modal appears for review | ✅ Pass |
| 8 | Confirm creation | Account created | Redirects to login with email pre-filled | ✅ Pass |

![Sign Up Confirmation Modal](./screenshots/03_signup_confirmation.png)

**Positive Finding:** The confirmation modal before account creation is a good UX pattern. It prevents accidental submissions and gives users a chance to review their details.

### 2.2 Login Flow

| Step | Action | Expected | Actual | Result |
|------|--------|----------|--------|--------|
| 1 | Navigate to login page | Form loads | Form loads | ✅ Pass |
| 2 | Enter registered email | Accepted | Accepted | ✅ Pass |
| 3 | Enter correct password | Accepted | Accepted | ✅ Pass |
| 4 | Click Login | Redirect to dashboard | Redirects to `/dashboard` | ✅ Pass |
| 5 | Session persists on refresh | Stay logged in | Stays logged in | ✅ Pass |

### 2.3 Negative Authentication Scenarios

These tests verify how the system handles **invalid or malicious inputs** — a critical area for any fintech application.

| # | Scenario | Input | Expected Behavior | Actual Behavior | Result |
|---|----------|-------|-------------------|-----------------|--------|
| 1 | Empty email + empty password | Both blank | "Email required" error | Needs verification in Phase 2 | ⬜ Deferred |
| 2 | Valid email + wrong password | Wrong password | "Invalid credentials" error | Needs verification in Phase 2 | ⬜ Deferred |
| 3 | SQL injection in email field | `' OR 1=1 --` | Rejected/sanitized | Needs verification in Phase 2 | ⬜ Deferred |
| 4 | XSS in company name | `<script>alert('xss')</script>` | Sanitized/escaped | Needs verification in Phase 2 | ⬜ Deferred |
| 5 | Duplicate email registration | Same email twice | "Email already exists" error | Needs verification in Phase 2 | ⬜ Deferred |
| 6 | Password without uppercase | `lowercase123` | Password policy enforced | Needs verification in Phase 2 | ⬜ Deferred |
| 7 | Password with <6 characters | `123` | "Password too short" | Needs verification in Phase 2 | ⬜ Deferred |

> **⚠️ Risk Note:** These negative scenarios are flagged for immediate Phase 2 priority. For a fintech application handling financial data, authentication security is non-negotiable. SQL injection and XSS attacks in the login/signup forms could compromise all user data.

### 2.4 OAuth Observations

| Provider | Button Present | Functional | Notes |
|----------|---------------|------------|-------|
| **Google OAuth** | ✅ Yes | ⬜ Not testable on localhost | Requires production OAuth credentials |
| **Apple OAuth** | ✅ Yes | ⬜ Not testable on localhost | Requires production OAuth credentials |
| **Forgot Password** | ✅ Yes | ⬜ Not tested | Link visible, points to `/reset-password` |

> **Recommendation:** Before launch, OAuth and Forgot Password flows must be verified on a staging environment with real credentials.

---

## 3. Dashboard — First Impression Analysis

**URL:** `/dashboard` — ✅ Loads in < 2 seconds

![Dashboard — Top Half](./screenshots/01_dashboard_top.png)

### 3.1 Layout Components

The dashboard is the first screen users see after login. It contains:

| Component | Position | Type | Status |
|-----------|----------|------|--------|
| Welcome header | Top bar | Text + user avatar | ✅ Displays "Welcome - [Username]" |
| KPI Cards (x4) | Top section | Total Revenue, Total Expenses, Orders, Invoices | ✅ Cards render correctly |
| Cash Flow Chart | Middle | Line chart with FY2026 monthly data | ✅ Chart renders |
| Revenue Analysis | Below Cash Flow | Bar chart (Income vs Expenses) | ✅ Chart renders |
| Invoice Summary | Bottom right | Quick stats table | ✅ Renders |
| Recurrent Expenses | Bottom left | Placeholder | ⬜ "Coming Soon" |
| Banks & Credit Cards | Bottom left | Placeholder | ⬜ "Coming Soon" |

### 3.2 KPI Card Structure

Each KPI card follows a consistent design:
```
┌─────────────────────┐
│  [Icon]  Label      │
│  ₦0.00             │ ← Primary value (correct)
│  ↗ 12.3% +51.634   │ ← Trend indicator (⚠️ suspicious)
│           today     │
└─────────────────────┘
```

> **Observation:** The primary values are correct (₦0.00 and 0 for a fresh account), but the trend indicators below each value appear static. This will be analyzed in depth in Part 2.

### 3.3 Navigation Bar & Session

| Element | Expected | Actual | Result |
|---------|----------|--------|--------|
| User name displayed | Shows logged-in user | Shows "Joe Exchange" / "Admin" | ✅ Pass |
| Notification bell icon | Present and clickable | Present | ✅ Pass |
| User dropdown menu | Shows profile options | Shows dropdown with options | ✅ Pass |
| Sidebar toggle (hamburger) | Collapses sidebar | Toggles sidebar visibility | ✅ Pass |
| Dark/Light mode | Toggle available | Theme switch works | ✅ Pass |

---

## 4. Module Navigation — Comprehensive Breakdown

Every sidebar module was individually clicked and verified. Below is a detailed assessment of each module, including sub-page navigation.

### 4.1 Record Module (`/record`)

| Aspect | Finding |
|--------|---------|
| **Page Title** | "Items" — differs from sidebar label "Record" |
| **Table Columns** | Name, Category, Description, Opening Balance, Unit Price, Opening Stock, Quantity Left, Date Created, Action |
| **Empty State** | Shows "No records found..." with stack icon |
| **Add Button** | "+ Add records" button present and clickable |
| **Search** | Search bar available |
| **Refresh** | Refresh button available |

> **Observation:** The "Record" sidebar label opens an "Items" page. This is an inventory/product management module, which may confuse users expecting financial record-keeping. The naming convention should align with the module's actual purpose.

---

### 4.2 CRM Module (`/crm/order`)

| Sub-Page | URL | Status | Content |
|----------|-----|--------|---------|
| **Order** | `/crm/order` | ✅ Loaded | KPI cards (Total, Completed, Pending, Cancelled), order table |
| **Customers** | `/crm/customers` | ✅ Loaded | Customer list with search and "Add Customer" |
| **Invoice** | `/crm/invoice` | ✅ Loaded | Invoice list with status tracking |
| **Quotation** | `/crm/quotation` | ✅ Loaded | Quotation management table |
| **Credit Note** | `/crm/credit-note` | ✅ Loaded | Credit note tracking |

> **Observation:** CRM has the most sub-pages (5). All load correctly. The Order page has its own KPI cards — this is where revenue data likely feeds into the Dashboard.

---

### 4.3 Production Module (`/production`)

| Aspect | Finding |
|--------|---------|
| **Primary View** | Raw Materials table |
| **Add Button** | "Add Raw Materials" button present |
| **Empty State** | No raw materials listed |

> **Observation:** This module is straightforward. For SMEs with manufacturing, this connects raw materials to finished goods. Its integration with Inventory will need to be verified in Phase 2.

---

### 4.4 Purchase Module (`/purchase/expenses`)

| Sub-Page | URL | Status | Content |
|----------|-----|--------|---------|
| **Expenses** | `/purchase/expenses` | ✅ Loaded | Expense tracking table |
| **Vendors** | `/purchase/vendors` | ✅ Loaded | Vendor management list |

> **Observation:** This module handles the expense side of the financial engine. Combined with CRM (revenue), these two modules likely form the core data flow into the Dashboard KPIs.

---

### 4.5 Bank Reconciliation (`/bank-reconciliation`)

| Aspect | Finding |
|--------|---------|
| **Upload** | "Upload Statement" button present |
| **Reconcile** | "Reconcile" button present |
| **History** | Transaction history table (empty) |

> **⚠️ Key Module:** This is PrimBooks' #1 selling point per the PRD. It promises automated matching of internal records with bank statements. Thorough testing of this module is critical in Phase 2.

---

### 4.6 Inventory Module (`/inventory/inventorylist`)

| Sub-Page | URL | Status | Content |
|----------|-----|--------|---------|
| **Inventory List** | `/inventory/inventorylist` | ✅ Loaded | Stock tracking table |
| **Inventory Adjustment** | `/inventory/adjustment` | ✅ Loaded | Stock adjustment form |

> **Progress Note:** The PRD previously marked this module as "Not started." It is now built and accessible — showing positive development velocity.

---

### 4.7 Finance Module (`/finance/chart-of-account`)

| Sub-Page | URL | Status | Content |
|----------|-----|--------|---------|
| **Chart of Account** | `/finance/chart-of-account` | ✅ Loaded | Account categories and structure |
| **Journal** | `/finance/journal` | ✅ Loaded | Journal entry management |
| **Banking** | `/finance/banking` | ✅ Loaded | Bank accounts management |
| **Tax** | `/finance/tax` | ✅ Loaded | Tax configuration |

> **Observation:** This is the core accounting engine. Chart of Accounts pre-loads with standard categories (Assets, Liabilities, Revenue, Expenses). The structure appears well-organized. Accuracy of journal entries and tax calculations will be the most critical test in Phase 2.

---

### 4.8 Assets Module (`/asset/list`)

| Sub-Page | URL | Status | Content |
|----------|-----|--------|---------|
| **List of Assets** | `/asset/list` | ✅ Loaded | Asset inventory table |
| **Asset Categories** | `/asset/categories` | ✅ Loaded | Category management |

> **Observation:** For SMEs, asset tracking (equipment, vehicles, property) with depreciation is a compliance requirement. This module addresses that need.

---

### 4.9 Payroll Management (`/hr-payroll/employees`)

| Sub-Page | URL | Status | Content |
|----------|-----|--------|---------|
| **Employees** | `/hr-payroll/employees` | ✅ Loaded | Employee directory |
| **Department** | `/hr-payroll/department` | ✅ Loaded | Department structure |
| **Payroll** | `/hr-payroll/payroll` | ✅ Loaded | Payroll processing |
| **Loan Mgmt.** | `/hr-payroll/loan` | ✅ Loaded | Employee loan tracking |
| **Timesheet Mgmt.** | `/hr-payroll/timesheet` | ✅ Loaded | Time tracking |
| **Leave Mgmt.** | `/hr-payroll/leave` | ✅ Loaded | Leave request/approval |
| **Pension** | `/hr-payroll/pension` | ✅ Loaded | Pension management |

> **Observation:** This is the most feature-rich module with 7 sub-pages. For a fintech platform, payroll accuracy is critical — Nigerian compliance rules (PAYE, Pension, NHF) must be correctly calculated. This is a high-priority Phase 2 test area.

---

### 4.10 Reports Module (`/reports/audit-trail`)

| Aspect | Finding |
|--------|---------|
| **Primary View** | Audit Trail — event log table |
| **Status** | Shows "No activity recorded" on fresh account |

> **Observation:** The Audit Trail is essential for compliance and accountability. Every CRUD operation should generate a log entry. This will be validated during Phase 2 functional testing.

---

### 4.11 Settings Module (`/settings`)

| Sub-Page | Content |
|----------|---------|
| **My Profile** | User details, avatar |
| **Notification** | Alert preferences |
| **Organization** | Business details |
| **Currency** | Currency configuration |
| **Manage Team** | User roles and permissions |
| **Subscription** | Plan management |
| **Customization** | UI preferences |

![Settings Page](./screenshots/04_settings_page.png)

---

## 5. Security & Session Quick Assessment

| Check | Observation | Risk Level |
|-------|-------------|------------|
| **HTTPS** | Not active (localhost dev) | ⬜ N/A (Expected for dev) |
| **Session Persistence** | User stays logged in after page refresh | ✅ Low |
| **Session Timeout** | No auto-logout observed during 30-min test | ⚠️ **Medium** — Fintech apps should auto-logout inactive sessions |
| **URL Direct Access** | Typing `/dashboard` while logged out redirects to login | ✅ Low — route guard works |
| **Browser Back After Logout** | Not tested | ⬜ Deferred |
| **Multiple Tabs** | Session works across multiple tabs | ✅ Low |
| **Console Errors** | "Compiling..." indicator seen briefly during navigation | ⚠️ Low — Dev server hot reload |

> **⚠️ Risk:** No session timeout was observed during the entire testing session (~30 minutes). For a financial application, OWASP recommends idle session timeout of **15 minutes maximum**. This should be verified and enforced before production.

---

## 6. UX & Accessibility Quick Notes

| Observation | Impact |
|-------------|--------|
| **Responsive Sidebar** | Collapses cleanly on hamburger click | ✅ Good |
| **Dark/Light Mode** | Both themes work without visual glitches | ✅ Good |
| **Loading States** | Pages show loading indicators during transitions | ✅ Good |
| **Empty States** | All modules show clear "No data" messages | ✅ Good |
| **Table Horizontal Scroll** | Action column (Edit/Delete) hidden on default viewport width | ⚠️ UX issue — users may miss functionality |
| **"Compiling" Badge** | Yellow "Compiling..." indicator appears in bottom-left during dev | ℹ️ Dev artifact — should not appear in production |

---

## 7. Risk Matrix

Based on this smoke test, here is the initial risk assessment for Phase 2 testing priority:

| Module | Business Risk | Test Priority | Reason |
|--------|--------------|---------------|--------|
| **Bank Reconciliation** | 🔴 High | P1 | Core USP — must work correctly |
| **Finance (Tax/Journals)** | 🔴 High | P1 | Financial accuracy is non-negotiable |
| **CRM (Orders/Invoices)** | 🔴 High | P1 | Revenue tracking |
| **Payroll** | 🟡 Medium | P2 | Nigerian compliance (PAYE, Pension) |
| **Dashboard** | 🟡 Medium | P2 | Data visualization accuracy |
| **Authentication Security** | 🟡 Medium | P2 | SQL injection, XSS, session mgmt |
| **Records/Inventory** | 🟢 Low | P3 | Supporting module |
| **Production** | 🟢 Low | P3 | Niche use case |
| **Settings** | 🟢 Low | P3 | Configuration only |

---

## 8. Summary & Verdict

| Area | Status |
|------|--------|
| **Can users sign up?** | ✅ Yes |
| **Can users log in?** | ✅ Yes |
| **Does the dashboard load?** | ✅ Yes |
| **Are all 12 modules accessible?** | ✅ Yes |
| **Do sub-pages within modules load?** | ✅ Yes (30+ sub-pages verified) |
| **Are there any blocking errors?** | ✅ No — zero crash or error screens |

### 🟢 Verdict: **Application is STABLE and READY for functional testing.**

The system passes the smoke test. All core features are accessible. No crashes, no broken routes, no 404 errors. The application architecture is well-structured with clear module separation.

**Areas of concern** (to be detailed in Part 2):
- Dashboard data integrity
- Session security hardening
- Naming consistency between sidebar and page titles

---

## 9. What's Coming in Part 2

The next report will cover:
- 🔍 Detailed analysis of Dashboard data issues
- 📊 PRD vs. Implementation cross-reference table
- 🐛 Specific bugs cataloged with severity ratings
- 💡 Prioritized recommendations for the development team

---

**Lead QA:** Azeez  
**Company:** Abvakon Mobile Solutions  
**Report Status:** Part 1 of 2 — Phase 1 Smoke Test
