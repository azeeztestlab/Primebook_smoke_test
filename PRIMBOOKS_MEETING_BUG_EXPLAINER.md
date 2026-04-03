# PrimBooks — Meeting Bug Explainer & Proof Guide

### Your walk-through companion for the April 3, 2026 stakeholder meeting
### Aligned with: PRD-Aligned Full Regression & CRUD Test Report

> **How to use this document:** Each bug has a plain-English explanation, why it matters to the business, and a "What to say" script you can read or paraphrase. Bugs are grouped by severity. Start the meeting with the ✅ Wins section, then walk through the blockers.

---

## QUICK-REFERENCE CARD

| Stat | Count |
|:---|:---:|
| **Total Bugs** | 24 |
| **Critical (🔴)** | 12 |
| **High (🟠)** | 6 |
| **Medium (🟡)** | 4 |
| **Low (🟢)** | 2 |
| **Bugs Fixed Since Last Test** | 3 (PAY-001 ✅, SEC-001 ✅, LIAB-001 removed ✅) |
| **Modules Fully Passing** | Finance, Payroll, Inventory, Assets, Settings, Reports |
| **Modules Blocked** | CRM (all 5 sub-modules), Production, Purchase |

---

## ✅ START HERE — WINS TO PRESENT FIRST

These are your good-news talking points. Lead with these before getting into the bugs.

### 1. Payroll Is Working (PAY-001 — FIXED ✅)

**Before:** Payroll showed 0 employees — nobody could get paid. This was the #1 blocker from the last test.

**Now:** Payroll shows 1 employee, Total Payouts of ₦790,000, and the "Create Payroll" button works.

**What to say:** *"The biggest blocker from our last test was payroll. It's now fixed. The system correctly shows employees and payout totals. Salary processing is unblocked."*

---

### 2. XSS in Record Table Fixed (SEC-001 — FIXED ✅)

**Before:** If you typed code like `<script>alert('hack')</script>` into a form field, it appeared as raw code in the Records table — a security risk.

**Now:** Script tags are rendered as harmless plain text. The system no longer treats user input as executable code in the Record module.

**What to say:** *"The cross-site scripting vulnerability we found in the Record module has been fixed. User input is now properly escaped in that table."*

---

### 3. All 13 PRD Modules Exist in the Sidebar

Every module specified in the PRD is present in the navigation. The architecture is complete. The issues are within the modules, not missing modules.

**What to say:** *"From an architecture standpoint, the platform is complete. All 13 modules from the PRD are present. The work now is fixing the bugs inside them."*

---

### 4. Finance Module — Fully Functional

Chart of Account, Banking, Taxation, and Journal all work. You can create tax records, view accounts, and manage bank info.

---

### 5. Assets Module — Comprehensive (7 sub-modules)

List of Assets, Depreciation, Maintenance, Reserve, Lease, Lease Return, Dispose — all accessible and structurally sound.

---

### 6. Audit Trail — Working

Real-time activity logging. Every CREATE, UPDATE, DELETE action is tracked with timestamps, user info, device info, and module name. Critical for compliance.

---

### 7. Settings — Working

Profile (shows "Musa Mohammed"), Notification, Organization ("Joe Exchange"), Currency, Manage Team, Subscription, Customization — all present and functional.

---

## 🔴 CRITICAL BUGS — 12 Issues (Must Fix Before Pilot)

These are business-blocking. Walk through each one with the stakeholders.

---

### STB-001: Server Error on Every Page

**Where:** Everywhere — a red "1 Issue" badge appears on every page

**What happens:** The system constantly tries to call an API endpoint called `fetchDashboardTotalCounts` and it fails every single time. This creates a persistent red error badge at the top of every page. It's not just cosmetic — it means the backend has a broken API that the frontend keeps calling.

**Why it matters:** Every user sees this error badge on every page. It makes the entire platform look unstable. It may also be the root cause of other data-loading failures throughout the app.

**Proof:** Navigate to any page — the red "1 Issue" badge is visible immediately.

**What to say:** *"There's a persistent server error — `fetchDashboardTotalCounts` — that triggers on every page load. Every user sees a red error badge. This needs investigation first because it may be causing cascading failures in other modules."*

---

### CRM-003: Customer Creation — Server Error

**Where:** CRM → Customer → Add Customer → fill all fields → click Save

**What happens:** You fill in every single field on the customer form — Title, Full Name, Display Name, Email, Phone, Company, Payment Terms — and hit Save. The system returns **"Failed! Server error."** Nothing is saved.

**Why it matters:** Customers are the starting point for the entire CRM workflow. If you can't create a customer, you can't create orders. If you can't create orders, you can't create invoices. If you can't create invoices, you can't issue credit notes. **One bug breaks the entire CRM chain.**

**Proof:** Go to CRM → Customer → click "Add Customer" → fill in all fields → click Save → observe "Server error."

**What to say:** *"Customer creation returns a server error even when all fields are correctly filled. This is a cascading blocker — it breaks Orders, Invoices, Quotations, and Credit Notes too, because they all depend on having customers."*

---

### ORD-001: "Error Loading Customer Details" in Order Form

**Where:** CRM → Order → Create Order → select a customer from dropdown

**What happens:** When you try to create an order and select a customer from the dropdown, the system shows **"Error loading customer details"** instead of pulling in the customer's information. The order form can't proceed.

**Why it matters:** Orders are the backbone of CRM. If orders can't load customer data, no orders can be created, which blocks invoice generation downstream.

**Proof:** Go to CRM → Order → click "Create Order" → select any customer → observe the error.

**What to say:** *"When you try to create a sales order and pick a customer, the system can't load their details. The order form breaks at that step."*

---

### ORD-002: "Failed to Load Orders" — API Failure

**Where:** CRM → Order → Orders History table

**What happens:** The Orders History page that should show all past orders displays an error: **"Failed to load orders."** The API endpoint that fetches order data is returning an error.

**Why it matters:** Even if you could create orders (which you can't, see ORD-001), you wouldn't be able to view them afterwards. Both creating and reading orders are broken.

**Proof:** Navigate to CRM → Order → observe the error message where the orders table should be.

**What to say:** *"The Orders History page can't load any data. The API is returning an error. So both creating and viewing orders are completely blocked."*

---

### INV-002: Invoice Creation Blocked by Order Dependency

**Where:** CRM → Invoice → Create Invoice

**What happens:** The invoice creation form requires an Order ID to link the invoice to. But since orders can't be created (ORD-001/ORD-002), there are no Order IDs available. The invoice feature is dead in the water.

**Why it matters:** Invoicing is how businesses get paid. This is blocked not by its own bug, but by the cascading failure from the Order module.

**What to say:** *"Invoices require Order IDs, but orders can't be created. So invoicing is blocked by a dependency chain, not by its own code. Fix orders → invoices should unlock."*

---

### QUOT-001: Quotation Form Missing "Add Item" Capability

**Where:** CRM → Quotation → Create Quotation

**What happens:** The form to create a quotation loads, but there's no way to add line items to it. There's no "Add Item" button or section where you can specify products, quantities, and prices. You can't build the actual quote content.

**Why it matters:** A quotation without line items is just a blank document. The whole point is to itemize what you're offering the customer.

**Proof:** Go to CRM → Quotation → click "Create Quotation" → look for an "Add Item" button — it doesn't exist.

**What to say:** *"The quotation form opens, but you can't add products or line items to it. There's no 'Add Item' button. You end up with an empty quotation."*

---

### CN-001: "Failed to Load Credit Notes" — API Failure

**Where:** CRM → Credit Notes

**What happens:** The Credit Notes listing page shows **"Failed to load credit notes"** — the API call to fetch credit notes is failing.

**Why it matters:** Credit notes are how businesses handle refunds and adjustments. The module loads but can't display or manage any data.

**What to say:** *"The Credit Notes page has the same API failure pattern — 'Failed to load credit notes.' The listing doesn't work."*

---

### PROD-001: Production Dropdowns All Empty

**Where:** Production → Create New Production

**What happens:** When you try to create a production order, the dropdown menus for **Customer**, **Employee**, and **Raw Materials** all show **"No results found."** Even though customers exist in CRM and employees exist in HRM, the Production module can't see them.

**Why it matters:** Production can't function if it can't reference customers to produce for, employees to assign work to, or raw materials to use. This is a cross-module data sync failure.

**Proof:** Go to Production → click "Create" → click any dropdown → observe "No results found."

**What to say:** *"The Production module can't see data from other modules. Customer, Employee, and Raw Material dropdowns are all empty. This is a data-sync issue between modules."*

---

### EXP-001: Expense Creation Blocked — Vendor Dropdown Empty

**Where:** Purchase → Expenses → Create Expense

**What happens:** The expense form has a mandatory "Vendor" dropdown. But it's completely empty — no vendors appear. Since Vendor is required, you can't save the expense.

**Why it matters:** This is directly caused by VEND-002 (vendors can't be created). No vendors exist → no vendors in dropdown → no expenses can be created.

**What to say:** *"Expense creation requires a vendor, but the vendor dropdown is empty because vendor creation itself is broken. Fix vendor save → expenses unlock."*

---

### VEND-002: Vendor "Save" Button Doesn't Work — ROOT CAUSE BLOCKER

**Where:** Purchase → Vendor → Add New Vendor → fill all fields → click Save

**What happens:** You fill in the entire vendor form — Title, Full Name, Display Name, Email, Phone Number, Company Name, Payment Terms, Debit/Credit accounts — and click "Save." **Nothing happens.** No error message, no loading spinner, no response at all. The button appears clickable but does absolutely nothing.

**Why it matters:** **This is the root cause of the entire Purchase module failure.** If vendors can't be saved, they don't appear in the vendor list, they don't appear in the expense form dropdown, and the entire purchase workflow is blocked. Fix this one bug and multiple others may resolve.

**Proof:** Go to Purchase → Vendor → click "Add New Vendor" → fill everything → click Save → nothing happens.

**What to say:** *"The vendor Save button is completely unresponsive. We filled every field, clicked Save — nothing. No error, no feedback. This is the root cause blocker for the entire Purchase module. Fix this one thing and it may unlock expenses too."*

---

### SEC-002: XSS Vulnerability in Finance → Journal

**Where:** Finance → Journal → description fields in journal entries list

**What happens:** When viewing the Journal entries list, script tags (like `<script>alert('xss')</script>`) that were entered via form fields are visible as raw code in the description column. The same vulnerability that was fixed in the Record module still exists here.

**Why it matters:** This is the same security risk as the original SEC-001, just in a different module. If a malicious user enters a script through any form that feeds into journal entries, it could execute in another user's browser.

**Proof:** View Finance → Journal → observe script tags visible in description fields.

**What to say:** *"The XSS fix that was applied to the Record module hasn't been applied to the Journal module yet. The same vulnerability exists there — script tags render as raw code."*

---

### SEC-003: XSS Vulnerability in Audit Trail

**Where:** Reports → Audit Trail → description column

**What happens:** Same issue as SEC-002. The Audit Trail logs activities across the platform. If any of those logged activities contain script tags in their descriptions, those tags appear as raw code in the Audit Trail table.

**Why it matters:** The Audit Trail is specifically viewed by admins and compliance officers — the highest-privilege users. XSS in the audit trail could target exactly the people with the most access.

**What to say:** *"XSS also appears in the Audit Trail. Since the Audit Trail is viewed by admins and compliance personnel, this is a higher-risk instance of the same vulnerability."*

---

## 🟠 HIGH SEVERITY BUGS — 6 Issues

---

### DASH-004: Dashboard KPIs Show ₦0.00 Despite Real Data

**Where:** Dashboard → KPI cards (Revenue, Expenses, Orders, Invoices)

**What happens:** All KPI cards show ₦0.00 or 0 — but we know real data exists. Payroll alone shows ₦790,000 in total payouts. The dashboard isn't pulling from actual module data.

**What to say:** *"Dashboard KPIs are disconnected from real data. Payroll has ₦790,000 in payouts, but the dashboard shows ₦0.00 revenue. The wiring between modules and dashboard isn't complete."*

---

### REC-001: Record Search Bar Doesn't Work

**Where:** Record → search bar at top of records table

**What happens:** You type a search term and press Enter. Nothing happens. The records don't filter. The search bar is purely cosmetic.

**Proof:** Go to Record → type any product name in search → press Enter → table doesn't change.

**What to say:** *"The search bar on the Records page doesn't filter anything. You type, press Enter, and the table stays the same."*

---

### CN-002: Credit Note Creation Blocked by Invoice Dependency

**Where:** CRM → Credit Notes → Create

**What happens:** To create a credit note, you need an existing invoice to reference. But invoices can't be created (blocked by orders, which are blocked by customer errors). So credit notes are blocked three levels deep.

**What to say:** *"Credit notes need invoices, invoices need orders, orders need customers — and customer creation has a server error. It's a three-level dependency chain."*

---

### SET-001: "Deactivate Account" Missing from Settings

**Where:** Settings menu

**What happens:** The PRD (§6.12) specifically requires a "Deactivate Account" option in Settings. It's not there. Users have no self-service way to deactivate their account.

**Proof:** Go to Settings → scroll through all options → "Deactivate Account" is absent.

**What to say:** *"The PRD requires a 'Deactivate Account' option in Settings. It's not there. This is a missing feature, not a bug."*

---

### HRM-001: Average Salary Still Manual Text Input

**Where:** Payroll Mgmt → Department → "Average Salary" field

**What happens:** When viewing a department, the "Average Salary" column shows a number — but it's whatever someone typed in manually, not calculated from actual employee salaries. You can type ₦500,000 for a department with zero employees.

**What to say:** *"Average Salary per department is a free-text field. You can type any number. It should auto-calculate from actual employee salary data."*

---

### HRM-002: Dashboard ↔ HRM Employee Sync Broken

**Where:** Dashboard vs. Payroll Mgmt → Employees

**What happens:** HRM shows employees exist (3 employees). The dashboard shows nothing about employees — no headcount card at all. The two modules don't talk to each other.

**What to say:** *"HRM knows we have 3 employees. The dashboard has no employee widget anymore — it was removed entirely in this build."*

---

## 🟡 MEDIUM SEVERITY BUGS — 4 Issues

---

### REC-002: Selling Price Renders as Dropdown

**Where:** Record → Create/Edit Record → Selling Price field

**What happens:** The Selling Price input field appears as a searchable dropdown instead of a normal number input. You can still enter a price, but the UI is wrong — it looks like you're choosing from a list instead of typing a price.

**What to say:** *"Minor UI issue — the Selling Price field shows as a dropdown instead of a number input. Usable but confusing."*

---

### REC-003: Price Calculation Mismatch (₦1000 → ₦998)

**Where:** Record → after creating a record with a specific price

**What happens:** You enter ₦1,000 as the price for a product. When you look at the records list, it shows ₦998. The system seems to apply some undocumented calculation — possibly a tax deduction — but doesn't tell you.

**What to say:** *"There's a hidden calculation. You enter ₦1,000 and the system stores ₦998. If there's a tax logic, it should be transparent to the user."*

---

### CRM-004: Company Name Required Even for Individuals

**Where:** CRM → Customer → Add Customer → select "Individual" type

**What happens:** When creating a customer and selecting type "Individual" (not a business), the "Company Name" field is still marked as required (red asterisk). An individual person shouldn't need a company name.

**What to say:** *"If you set customer type to 'Individual,' the form still requires Company Name. That field should be optional for non-business customers."*

---

### CRM-005: Phone Validation Too Strict

**Where:** CRM → Customer → Add Customer → Phone Number field

**What happens:** The phone number field requires a specific format (like +234...) but doesn't show what format it expects. If you type "08012345678" (a common Nigerian format), it rejects it silently.

**What to say:** *"The phone field requires +234 international format but doesn't tell you that. Regular numbers get rejected with no guidance."*

---

## 🟢 LOW SEVERITY BUGS — 2 Issues

---

### UX-003: No Loading Spinners

**Where:** All pages with data tables

**What happens:** When a page loads and fetches data from the server, there's no loading indicator. The page looks empty until data appears. Users might think there's no data when it's actually just loading.

**What to say:** *"There are no loading spinners. Pages look empty while data is being fetched. Minor, but it confuses users."*

---

### UX-004: Empty Tables Have No "No Data" Message

**Where:** Various modules (Payroll, CRM, etc.)

**What happens:** When a table genuinely has no data, it just shows empty rows with column headers. There's no message saying "No records found" or "Create your first…". Users can't tell if the table is empty or still loading.

**What to say:** *"Empty tables show nothing — not even a 'No data yet' message. Users don't know if it's loading or truly empty."*

---

## 🔗 THE CRM DEPENDENCY CHAIN — Visual Explainer

Use this to explain why 5 CRM modules are all broken:

```
Customer creation (CRM-003 — Server Error)
    ↓ blocks
  Order creation (ORD-001 — Can't load customer details)
    ↓ blocks
    Invoice creation (INV-002 — Needs Order ID)
      ↓ blocks
      Credit Note creation (CN-002 — Needs Invoice)

  Quotation (QUOT-001 — Separate bug: missing "Add Item")
```

**What to say:** *"Think of it as a chain. Customers feed into Orders, Orders feed into Invoices, Invoices feed into Credit Notes. The chain broke at the very first link — customer creation has a server error. Fix that one bug and the downstream modules may start working."*

---

## 🔗 THE PURCHASE DEPENDENCY CHAIN

```
Vendor Save (VEND-002 — Button unresponsive)
    ↓ blocks
  Vendor list (no vendors exist)
    ↓ blocks
    Expense creation (EXP-001 — Vendor dropdown empty)
```

**What to say:** *"Same pattern in Purchase. Vendor Save doesn't work → no vendors exist → Expense dropdown is empty → can't create expenses. One root fix unlocks the chain."*

---

## 📋 RECOMMENDED PRIORITY ORDER FOR DEV TEAM

Present this as the action plan:

| Priority | Bug ID | Module | Fix This → Unlocks |
|:---:|:---|:---|:---|
| **P0** | VEND-002 | Purchase/Vendor | → EXP-001 (Expenses) |
| **P0** | CRM-003 | CRM/Customer | → ORD-001, ORD-002, INV-002, CN-001, CN-002 |
| **P0** | STB-001 | Global | → May resolve cascading API failures |
| **P0** | PROD-001 | Production | → Full Production workflow |
| **P1** | SEC-002, SEC-003 | Journal, Audit Trail | → Close XSS vulnerability |
| **P1** | QUOT-001 | CRM/Quotation | → Quotation workflow |
| **P2** | REC-001 | Record | → Search functionality |
| **P2** | SET-001 | Settings | → PRD compliance |
| **P2** | DASH-004 | Dashboard | → Accurate KPIs |

---

## 📊 OVERALL PLATFORM HEALTH SUMMARY

```
PASSING (6 modules):    Finance ✅  Payroll ✅  Inventory ✅  Assets ✅  Reports ✅  Settings ✅
PARTIAL (3 modules):    Dashboard ⚠️  Record ⚠️  Audit Trail ⚠️
BLOCKED (3 modules):    CRM ❌  Production ❌  Purchase ❌
SKIPPED (1 module):     Bank Reconciliation ⏭️
```

**What to say:** *"Six out of thirteen modules are fully functional. Three have minor issues. Three are completely blocked by create-operation failures. One was skipped because the dev team is actively working on it."*

---

*Keep this document open during the meeting. Scroll to any bug ID for the full explanation and what to say.*
*Report date: April 3, 2026 — Azeez Test Lab*
