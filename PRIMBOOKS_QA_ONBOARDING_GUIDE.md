# PrimBooks QA — Strategy & Kickstart Guide

**Role:** QA Lead / Tester for PrimBooks  
**Prepared:** March 19, 2026  
**Product:** PrimBooks — Cloud-based Financial & Business Management Platform for SMEs

---

## 1. Product Overview (Plain English)

PrimBooks is a comprehensive **Financial & Business Management Platform** designed for small and medium businesses (SMEs). It combines accounting, CRM, HR, and production management into a single web application, replacing manual spreadsheets and notebooks.

### Key Modules:
| Module | Purpose |
|---|---|
| **Dashboard** | Business health overview — sales, expenses, and key metrics. |
| **Records** | Financial transaction entry (Income/Expenses). |
| **CRM** | Customer management, invoicing, quotations, and credit notes. |
| **Production** | Manufacturing tracking — raw materials and assembly. |
| **Purchases** | Vendor management and business spending. |
| **Finance** | Core accounting — banking, taxes, chart of accounts, and journals. |
| **HR & Payroll** | Employee management, attendance, salaries, and leave. |
| **Inventory** | Stock tracking and adjustments. |
| **Bank Reconciliation** | Matching internal records with bank statements (Core USP). |
| **Audit Trail** | Accountability logs of all system actions. |
| **Reports** | Automated financial and operational reporting. |
| **Asset Management** | Tracking company equipment and depreciation. |
| **Settings** | System configuration and team permissions. |

---

## 2. Prerequisites & Requirements (What's Needed)

### 🔴 Critical — Required for Testing
1. **Access to Staging/Dev Environment:** Need the dev server URL to begin testing since the product is pre-launch.
2. **Test Accounts:** Access to accounts with different roles (Admin, Accountant, HR, Auditor) to verify permissions.
3. **Communication Channel:** Confirmation of the primary team channel (Slack, Discord, WhatsApp, etc.).
4. **Bug Reporting Pipeline:** Access to the bug tracking tool (Jira, Linear, GitHub Issues) and the preferred template.

---

### 🟡 Important — Needed for Phase 2
5. **Design Files (Figma):** UI mockups to compare the current build against the intended design.
6. **API Documentation:** Swagger or Postman collections to test backend logic and edge cases.
7. **Development Roadmap:** Clarity on what is currently functional vs. what is still in development.
8. **Seed Data:** Pre-loaded sample data (customers, products) to accelerate testing.

---

### 🟢 Value-Add — Shows Rigor
9. **Sprint Cycle:** Understanding the deployment frequency for new updates.
10. **QA Priority:** Confirmation of which modules are the highest priority for the business.
11. **Browser/Device Scope:** Confirmation of which browsers and mobile devices should be prioritized for PWA testing.
12. **Compliance Requirements:** Any specific Nigerian tax/regulatory rules (FIRS, CBN) to validate in calculations.

---

## 3. QA Strategy & Roadmap

My structured approach to verifying PrimBooks:

### Phase 1: Smoke Test
- System accessibility and load stability.
- Core navigation across all modules.
- Basic authentication flow (Sign up/Login).

### Phase 2: Core Module Testing
Testing in order of business impact:
1. **Authentication** (System entry)
2. **Dashboard** (Initial user experience)
3. **Records** (Data foundation)
4. **CRM** (Revenue generation)
5. **Bank Reconciliation** (Key differentiator)
6. **Finance & Taxation**
7. **HR & Payroll**

### Phase 3: Integration & End-to-End (Week 3+)
- Does creating an order in CRM correctly update the Finance ledger?
- Does payroll correctly reflect in Expense reports?
- Does inventory update after a Production run?

### Phase 4: API & Automation (Future Roadmap)
- **API Testing:** Verify backend endpoints using Postman/Swagger to ensure data integrity and security (Auth tokens).
- **Regression Automation:** Once the UI is stable, I will implement automated scripts (e.g., Playwright or Cypress) for critical flows like Login, Record Creation, and Invoicing to prevent future bugs.
- **Performance Testing:** Verify the system remains fast and stable as transaction volume grows.

---

## 4. Current Observations & Checklist

Items to confirm during development meetings:
- Status of **Inventory List & Adjustments** (PRD marks as "Not started").
- Functionality of **Reporting Engine** (Verify if UI is a shell or live data).
- Status of **Expenses** module (PRD marks as "Frontend pending").
- Status of **Bank Reconciliation** (Verify backend matching logic).

---

## 5. Technical Note on Fintech Testing

Financial accuracy is non-negotiable. Every decimal point and tax calculation matters. My testing focuses on ensuring that PrimBooks provides a reliable, audit-ready financial record for every business using it.
