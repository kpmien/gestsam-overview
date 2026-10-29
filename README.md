# 🏪 GestSam : Business Management System

> A full-featured web application for running a trading business, built with **Laravel 11**. Live in production and used daily by the company's staff.

![Status](https://img.shields.io/badge/status-production-brightgreen) ![Laravel](https://img.shields.io/badge/Laravel-11-red) ![PHP](https://img.shields.io/badge/PHP-8.2-blue) ![Tests](https://img.shields.io/badge/tests-PHPUnit-informational)

🇫🇷 [Version française](README.fr.md)

---

## 📌 Overview

**GestSam** is an internal management application covering the full life cycle of a trading business: purchasing from suppliers, stock valuation, customer orders and partial payments, PDF invoicing, operating expenses, and a period-based profit report.

The project was built for **NAO Holding** (Bobo-Dioulasso, Burkina Faso) and is **running in production**, with two user roles: **admin** (full access) and **accountant** (sales only, costs and profit hidden).

The 2026 release reworked the accounting core: weighted average cost of stock, cost of goods frozen at the time of sale, and a clean split between stock purchases and operating expenses. Profit is now computed as **sales − COGS − operating expenses**, by month or by year.

---

## ✨ Core Features

### 🛒 Sales & Orders
- Multi-product order creation with searchable product picker (Select2)
- Stock availability check before confirmation
- Automatic total, amount paid and remaining balance
- **Partial payments (versements)**: numbered installments per order, with the paid amount reconciled from the installments
- Order statuses: **Pending**, **Settled**, **Cancelled**
- Cancellation restores stock and recomputes the client's debt; cancelled orders are excluded from every report
- Full order editing with stock and debt synchronisation (editing a cancelled order is refused)

### 🧾 Customer Invoices (PDF)
- Generated with **FPDF**, company header and footer, client name and phone
- Formatted invoice number (`FAC-00001`)
- Line items, total, amount paid and outstanding balance

### 👥 Clients
- Automatic client creation on first order
- Debt computed dynamically from unsettled orders
- Manual update of name, phone and address

### 📦 Products & Stock
- Product catalogue with on-the-fly supplier creation
- **Weighted average cost (CUMP)**: the unit cost is recomputed at every stock entry
- **Cost frozen at sale**: each order line stores the purchase cost of the moment, so historical margins never drift
- Purchase cost is read-only on the product form (driven by purchase invoices); quantity stays editable as an inventory adjustment
- Negative stock guard enforced in the model's `booted()` hook
- Quick product creation from the purchase invoice form

### 🏭 Suppliers & Purchases (admin only)
- Supplier registry with edit form and on-the-fly creation
- **Purchase invoices**: received goods plus customs, transit and other fees, **allocated to the products and capitalised** into their cost (no immediate expense)
- **Supplier payments** (bank or cash): numbered, tracked as cash flow, no impact on profit; invoice status unpaid / partial / paid
- **Supplier debts** view: open invoices and total amount due
- Invoice cancellation restores stock and cost, refused if a payment exists or the goods were already sold
- Purchase invoice **PDF** (DomPDF)

### 💸 Expenses
- Expense categories: operating charge, goods purchase, customs/transit, supplier payment. **Only operating charges reduce profit**
- Operating charges split into **fixed** (rent, salaries) and **variable** (electricity, transport)
- Create, edit and delete expenses

### 📊 Dashboard & Report
- Default view on the **current month**, with a month / year selector (or whole year)
- Period figures: sales, COGS, fixed and variable charges, **net profit**, purchases of the period
- Current balances: stock value, client debts, supplier debts
- Admin report with **Chart.js**: top 20 products, top clients, 12-month sales trend
- Indicator cards on the dashboard

### 👛 Personal Expenses
- Monthly salary and personal expense log by category (food, transport, family, health, leisure, other)
- Remaining balance, percentage consumed, end-of-month projection and daily budget for the current month
- Comparison with the previous month and 6-month history

### 🔐 Roles & Security
- Two roles stored on the user: **admin** and **comptable** (accountant)
- `EnsureAdmin` middleware guards the report, products, suppliers and purchase modules; the sidebar and cost columns adapt to the role
- Login throttled to 5 attempts per minute, show/hide password toggle
- Seeded accounts driven by `.env`, no plain-text passwords in the repository
- Critical operations (orders, cancellations, purchase invoices, payments) wrapped in **DB transactions**

---

## 📸 Screenshots

### Home Page
<p align="center">
  <img src="screenshots/dashboard.png" width="880" alt="Home Page"/>
</p>

### Dashboard
<p align="center">
  <img src="screenshots/rapport.png" width="880" alt="Dashboard"/>
</p>

### Create Order
<p align="center">
  <img src="screenshots/creer_commande.png" width="880" alt="Create Order"/>
</p>

### Create Product
<p align="center">
  <img src="screenshots/creer_produit.png" width="880" alt="Create Product"/>
</p>

### Purchase Invoice
<p align="center">
  <img src="screenshots/facture_achat.png" width="880" alt="Purchase Invoice"/>
</p>

### Create Purchase Invoice
<p align="center">
  <img src="screenshots/creer_facture_achat.png" width="880" alt="Create Purchase Invoice"/>
</p>

### Customer Invoice
<p align="center">
  <img src="screenshots/facture.png" width="440" alt="Customer Invoice"/>
</p>

---

## 🗄️ Data Model

```
Supplier ──< PurchaseInvoice ──< PurchaseInvoiceLine >── Product
                 │                                         │
                 └──< SupplierPayment                      │
                                                           │
Client ──< Order ──< OrderLine (qty, sale_price, purchase_cost) ──┘
            │
            └──< Installment (versement)

Expense (category, fixed/variable)        PersonalExpense, MonthlySalary
User (role: admin | comptable)
```

Key points:
- An **OrderLine** stores both the sale price and the **purchase cost at the time of sale**
- A **PurchaseInvoiceLine** stores the supplier unit price and the **landed unit cost** (with fees)
- **Installments** and **SupplierPayments** are numbered per order / per invoice

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|------------|
| Backend | **Laravel 11** (PHP 8.2) |
| Database | **MySQL** (production) / SQLite (tests) |
| PDF | **FPDF** (`codedge/laravel-fpdf`) for customer invoices, **DomPDF** (`barryvdh/laravel-dompdf`) for purchase invoices |
| Frontend | **Blade** + Bootstrap 5 + Select2 + DataTables + Chart.js, fonts served locally, assets built with **Vite** |
| Auth | Laravel Auth (sessions), role middleware, `ClientPolicy` |
| Tests | **PHPUnit** feature tests (accounting report, period dashboard, purchase invoices, installments, roles, suppliers) |

---

## 🚀 Operations

The private repository also ships:
- A consolidated **changes and deployment runbook** (test database and test site before production)
- **SQL scripts** to reclassify historical expenses, tag fixed/variable charges and repair distorted historical costs, plus diagnostic queries
- An **opening balance** procedure for the initial stock, and a one-page sheet for the merchant (inventory, unit costs, supplier debts)

---

## 💡 What This Project Demonstrates

- Solid command of **Laravel** (routing, Eloquent, Blade, middleware, policies, form requests, seeders, feature tests)
- **Real accounting logic**: weighted average cost, frozen COGS, capitalised purchase fees, split between cash flow and profit
- Handling **cascading business rules**: cancellations, stock restoration, debt recomputation, guard rails on destructive actions
- Custom **PDF documents** with two different engines
- **Role-based access** with a UI that adapts to the user
- A real **production deployment** with a documented runbook and data migration scripts
- Ability to evolve a live application without breaking historical data

---

## ⚖️ License

© 2024-2026 Kounabé Paulin MIEN. Built for a private client.
This repository is shared for demonstration purposes only. The source code lives in a private repository.
