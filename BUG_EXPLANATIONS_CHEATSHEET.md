# PrimBooks Bug Explanations — Meeting Cheat Sheet
### Read this before your meeting. Every bug explained like you're talking to a human.

---

## 🔴 CRITICAL BUGS (Must fix before launch)

---

### PAY-001: Payroll Shows 0 Employees

**Where:** Payroll Mgmt → Payroll → Create New Payroll

**What happens:** You go to create a new payroll to pay your staff. The system shows you an empty table — zero employees — even though you already added employees in the Employees section right next to it.

**Why it matters:** This means **no one can get paid**. The whole point of a payroll feature is to process salaries. If the payroll can't see the employees, it's completely useless. It's like having a cashier counter with no products to scan.

**What you say in the meeting:** *"The Payroll module cannot see the employees that were added in the HR section. So if a business tries to run payroll, it shows an empty table. This completely blocks salary processing — the most critical feature for any business."*

---

### HRM-001: Average Salary is Manual Instead of Auto-Calculated

**Where:** Payroll Mgmt → Department → "Average Salary" column

**What happens:** When you create a department, there's a field called "Average Salary." Instead of the system automatically calculating this from the actual salaries of employees in that department, it lets you **type any number you want**. You can type ₦300,000 for a department with zero employees and the system says "okay."

**Why it matters:** The whole point of an "Average Salary" figure is to give HR managers accurate analytics. If they can just type whatever they want, the number means nothing. It should automatically look at all the employees in that department, add up their salaries, and divide by the count.

**What you say in the meeting:** *"The Average Salary field on the Department page is a free-text input. You can type any number, even for departments with no employees. It should be auto-calculated from actual employee salary data."*

---

### SEC-001: XSS (Cross-Site Scripting) Vulnerability

**Where:** Record → when viewing any table that shows user-entered data

**What happens:** If someone types computer code (like `<script>alert('XSS')</script>`) into a form field — for example, the Expense Name — the system saves it. When another user (especially an admin) opens the Records table to view entries, that code appears as **raw code in the table**. It's not "cleaned up" or hidden.

**Why it matters:** Right now it just shows ugly code text. But if a hacker types a more advanced script, it could actually **run** in the admin's browser. That means the hacker could steal the admin's login session, redirect them to a fake page, or mess with the data they see. This is a well-known security vulnerability called "XSS."

**What the fix is:** The developers need to add "output encoding" — that means before the system shows any text that a user typed, it converts special characters (like `<` and `>`) into safe versions so they display as plain text and never execute as code.

**What you say in the meeting:** *"If you type code into a form field, the system doesn't clean it before displaying it in tables. A hacker could exploit this to run scripts in other users' browsers. The fix is standard — add output encoding to all user-facing data displays."*

---

### LIAB-001: Liabilities Module is Completely Missing

**Where:** Should be in the sidebar navigation — but it's not there

**What happens:** The Product Requirements Document (PRD) says there should be a "Liabilities" module where businesses track their debts, loans, and money they owe. We looked through the entire sidebar menu — top to bottom — and it doesn't exist. There's no link, no menu item, nothing.

**Why it matters:** Every business has liabilities — loans, vendor credit, outstanding payments. Without this module, the financial picture is incomplete. An accountant can't prepare a proper balance sheet because half of it (what the business owes) has nowhere to be recorded.

**What you say in the meeting:** *"The PRD requires a Liabilities module for tracking debts and loans. It's completely absent from the application — not even a placeholder in the sidebar. This is a feature gap."*

---

### DATA-003: Extremely Large Numbers Cause Data Corruption

**Where:** Any financial input field (Opening Balance, amounts, etc.)

**What happens:** If you type a very large number — 20 digits or more (like 99999999999999999999) — the system stores it as **zero** or a completely wrong number. No error message, no warning.

**Why it matters:** For large businesses or holding companies dealing in billions, their transactions could be wiped to zero without anyone knowing. The system silently corrupts the data instead of telling the user "this number is too big."

**What you say in the meeting:** *"If you enter an extremely large value in a financial field, the system stores it as zero. No error, no warning — just silent data loss. We need input validation and proper number handling."*

---

### RECON-001: Reconciliation Status Shows "null null"

**Where:** Bank Reconciliation → Reconciliation History table → Status column

**What happens:** After you do a bank reconciliation (matching your bank statement entries with your internal records), you go to the history page to see past reconciliations. The "Status" column — which should say "Pending" or "Completed" — instead shows the literal text **`null null`**.

**Why it matters:** An accountant needs to know which reconciliations are finished and which are still pending. When it shows `null null`, they can't tell. It looks unprofessional and makes the feature unreliable. This is PrimBooks' "killer feature" per the PRD — and it has a visible display bug.

**What the problem likely is:** The backend probably stores the status correctly, but the frontend code that reads and displays it is looking for the data in the wrong place or the wrong format. So it gets "nothing" (null) and prints that literally.

**What you say in the meeting:** *"The Bank Reconciliation history table shows 'null null' in the Status column instead of 'Pending' or 'Completed.' This is a front-end display bug — the status data isn't being read properly."*

---

## 🟠 HIGH SEVERITY BUGS

---

### DASH-001: Revenue KPI Shows Fake Number "+₦51.6k"

**Where:** Main Dashboard → Revenue KPI card (top of page)

**What happens:** The big revenue card on the dashboard always shows "+₦51.6k" (or similar) no matter what. Even if you add revenue entries in the Finance module, this number never changes. It's a **hardcoded demo number**.

**Why it matters:** A business owner logs into PrimBooks, sees their dashboard, and trusts those numbers. If the number says "+₦51.6k" but their actual revenue is ₦2 million, they're making decisions based on fake data.

**What you say in the meeting:** *"The revenue figure on the dashboard is a hardcoded placeholder. It doesn't connect to actual financial data. Same goes for the expense card."*

---

### DASH-002: Expense KPI Shows Static Data

**Where:** Main Dashboard → Expense KPI card

**What happens:** Same as DASH-001 but for expenses. The number displayed never updates no matter what expenses you record.

---

### DASH-003: Employee Headcount Shows "0"

**Where:** Main Dashboard → Employee Headcount KPI card

**What happens:** Even after adding employees in the HR section (you added Jane Smith and John Doe — 2 employees), the dashboard still shows "0 Employees."

**Why it matters:** Management can't see their workforce size at a glance from the main dashboard. The HR data and the dashboard aren't connected.

**What you say in the meeting:** *"We added 2 employees in HRM. The dashboard still shows '0 Employees'. The dashboard KPIs are not wired to real data."*

---

### HRM-002: Dashboard vs HRM Employee Count Mismatch

**Where:** Dashboard shows "0" employees, but Payroll Mgmt → Employees shows 2

**What happens:** This is the data-sync version of DASH-003. The HRM module correctly shows "Total Employees: 2" (you can see it in your screenshot). But the main Dashboard has no idea those employees exist.

**Why it matters:** Two parts of the same application are showing different numbers. That's a trust issue — users won't trust either number if they conflict.

---

### PAY-002: No Error Message When Payroll Table is Empty

**Where:** Payroll Mgmt → Payroll → Create New Payroll

**What happens:** When the payroll table comes up empty (with 0 employees), the system just shows... nothing. No message like "No employees found — please add employees first." Just a silent empty table.

**Why it matters:** A new user would think they forgot to add employees. They'd go back to the Employees section, see their staff is there, and be confused. The system should tell them something is wrong instead of showing a blank.

**What you say in the meeting:** *"When payroll can't find employees, it just shows a blank table. No error, no guidance. Users will be confused about what went wrong."*

---

### DATA-002: 15-Digit Numbers Get Truncated

**Where:** Any financial input field

**What happens:** If you type a number with 15+ digits (like ₦9,999,999,999,999,999), the system either chops off the end digits or converts it to something like "9.99999e+15" (scientific notation). The actual precise number is lost.

**Why it matters:** This is a technical limitation. JavaScript (the programming language most web apps use) can only handle numbers up to about 15 digits precisely. For a financial system, this means very large transactions can't be recorded accurately. The developers need to use a special number type (called BigDecimal or similar) for financial values.

**What you say in the meeting:** *"Numbers beyond 15 digits get silently rounded or truncated. For a fintech platform handling large transactions, this is a data integrity risk. The team should use precision number handling."*

---

## 🟡 MEDIUM SEVERITY BUGS

---

### PH1-002: Company Name from Registration Doesn't Appear in Settings

**Where:** Settings → Profile / Company page

**What happens:** When you first sign up for PrimBooks, you enter your company name. But when you go to the Settings page later, that company name isn't there. It's blank or shows something different.

**Why it matters:** Minor annoyance — the user has to type their company name again in settings. But it shows data isn't flowing properly between the registration process and the rest of the app.

---

### RECON-002: "Reconcile Now" Button is Off-Screen

**Where:** Bank Reconciliation → History table → far right side

**What happens:** On a standard laptop screen (13 to 15 inches), the "Reconcile Now" action button is pushed off the right edge of the table. You have to scroll horizontally to find it.

**Why it matters:** An accountant doing daily reconciliation has to scroll right every single time to click the button. It should be visible without scrolling. The table columns have too much padding (spacing) which pushes the button out.

**What you say in the meeting:** *"On a normal laptop screen, you can't see the Reconcile button without scrolling sideways. The table layout needs adjustment."*

---

### RECON-003: Auto-Match Accuracy Not Verified at Scale

**Where:** Bank Reconciliation → auto-matching feature

**What happens:** We uploaded a small sample CSV bank statement and the system matched some transactions. But we couldn't test with hundreds or thousands of transactions to see if it stays accurate at real-world volume.

**Why it matters:** A small test might work, but when a business uploads 6 months of bank statements with thousands of transactions, we don't know if the matching logic will hold up or start making errors.

**What you say in the meeting:** *"The auto-matching works in small tests, but we haven't stress-tested it with production-volume data yet. That's for the next testing phase."*

---


### UX-001: Date Picker Starts on January 1st (No "Today" Button)

**Where:** Any form with a date picker (expense dates, record dates, etc.)

**What happens:** When you click on a date field, the calendar opens to **January 1st** of the current year instead of today's date. To get to today (March 31), you have to click "next month" three times. There's no "Today" or "Jump to Now" shortcut button.

**Why it matters:** Every single time a user enters a date (which in an accounting app is multiple times per day), they waste clicks navigating months. It should default to today.

---

### CRM-002: Invoice Generation Not Yet Built

**Where:** CRM → Invoices

**What happens:** The PRD says the Invoice feature should let you generate invoices from orders. The UI design is approved but the actual frontend code is still "in progress." So you can access the page but can't fully create invoices yet.

**Why it matters:** Invoicing is a core business function. It's noted as a feature gap, not a bug — the dev team knows it's incomplete.

---

### UX-002: Error Messages Are Generic

**Where:** Various forms throughout the app

**What happens:** When something goes wrong (like a failed save or a validation error), the system shows generic messages like "An error occurred" instead of telling you specifically what went wrong and how to fix it.

---

## 🟢 LOW SEVERITY BUGS

---

### CRM-001: "Add Item" Button Not Always Visible in Orders

**Where:** CRM → Create Order → Add Item button

**What happens:** On certain screen sizes, the "Add Item" button that lets you add products to an order is hard to find or partially hidden.

---

### UX-003: No Loading Spinners on Heavy Pages

**Where:** Tables throughout the app (Records, Employees, etc.)

**What happens:** When a page is loading data, there's no spinning loader or skeleton placeholder. The page just looks empty for a moment before data appears. A user might think there's no data when it's actually still loading.

---

### UX-004: Empty Payroll Table Has No "No Data" Message

**Where:** Payroll → Create New Payroll → empty employee table

**What happens:** Instead of showing "No employees found" or "Please add employees first," it just shows an empty table with column headers and nothing underneath.

---

## ⚠️ PARTIAL RESULTS EXPLAINED

---

### Bank Reconciliation: Transaction Matching = "PARTIAL"

**Where:** Bank Reconciliation → after uploading a CSV bank statement

**What happens:** When you upload a bank statement CSV, the system tries to automatically match the imported transactions with your internal records. In our small test (with a few transactions), it **did match some correctly**. But we marked it "PARTIAL" because:

1. We only tested with a tiny sample (a few transactions)
2. We couldn't verify if it works correctly with hundreds of transactions
3. Some transactions were left unmatched (which is normal — those need manual reconciliation)

**It's not broken — it partially works.** We just can't call it a full PASS until it's tested at real-world scale.

**What you say in the meeting:** *"The auto-matching works for small datasets. We marked it partial because we haven't tested at volume yet. That's scheduled for the next phase."*

---

### Payroll History = "PARTIAL"

**Where:** Payroll Mgmt → Payroll → history view

**What happens:** The history table loads and the interface works — but it's empty because we could never actually process a payroll (since PAY-001 blocks it). So we can confirm the UI works, but we can't confirm the actual payroll processing and history recording works.

**What you say in the meeting:** *"The payroll history page loads fine, but we couldn't test it fully because the payroll employee-fetching bug prevents us from running any payroll at all."*

---

### CRM: Add Item to Order = "PARTIAL"

**Where:** CRM → Create Order → Add Item

**What happens:** The "Add Item" button exists and the order flow works, but the button placement wasn't consistently visible across all screen resolutions. It works — you just have to look for it sometimes.

---

### POS = "LIMITED"

**Where:** Sidebar → POS

**What happens:** The POS (Point of Sale) interface loads and is accessible, but its feature scope is still under active development. We could access the page but couldn't test full transaction processing because the features aren't all built yet.

**What you say in the meeting:** *"POS loads fine but the features are still being developed. Full testing deferred."*

---

## ✅ THINGS THAT FULLY PASSED (Good news to share)

1. **Login/Logout** — Completely secure and functional
2. **Session management** — Can't access dashboard after logout, sessions persist on refresh
3. **SQL Injection protection** — Hackers can't break in through input fields
4. **Revenue module** — Adding and viewing revenue works perfectly
5. **Expense module** — Adding and viewing expenses works perfectly
6. **Fixed Assets** — Stable and functional
7. **Inventory** — Product lists work correctly
8. **Task Manager** — Kanban board works without issues
9. **Visual Design** — Premium 3D styling, dark mode, animations — all excellent
10. **CSV Bank Statement Upload** — Parsing dates and amounts from CSV works correctly

---

*Use this document as your reference during the meeting. Every bug, fail, partial, and critical is explained above in plain English.*
