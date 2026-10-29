# 🏪 GestSam — Business Management System

> A full-featured web application for managing commercial operations, built with **Laravel 11** — currently live in production and used daily by merchants.

![Status](https://img.shields.io/badge/status-production-brightgreen) ![Laravel](https://img.shields.io/badge/Laravel-11-red) ![PHP](https://img.shields.io/badge/PHP-8.x-blue) ![Hosting](https://img.shields.io/badge/hosting-Hostinger-orange)

🇫🇷 [Version française](README.fr.md)

---

## 📌 Overview

**GestSam** is an internal management application designed for the day-to-day operations of a trading business. It covers the full sales cycle — from product stock management to client invoicing — including order tracking, expense recording, and supplier management.

The project was built for **NAO Holding** (Burkina Faso / Bobo-Dioulasso) and is **currently running in production**, hosted on **Hostinger**, actively used by the company's sales teams.

---

## ✨ Core Features

### 📦 Product & Stock Management
- Add, edit, and delete products
- Real-time stock level tracking
- Automatic supplier association (created on the fly if not existing)
- Negative stock protection enforced at the model level
- Global stock valuation view (quantity × purchase price)

### 🛒 Order Management
- Multi-product order creation with dynamic product selection
- Stock availability check before order confirmation
- Automatic calculation of total, amount paid, and remaining balance
- Order statuses: **Pending**, **Settled**, **Cancelled**
- Order cancellation with automatic stock restoration and client debt recalculation
- Full order editing with stock and debt synchronization

### 🧾 PDF Invoice Generation
- Invoice generation via **FPDF**
- Custom company header and footer with branding images
- Formatted invoice number (`FAC-00001`)
- Line items, totals, amount paid, and outstanding balance

### 👥 Client Management
- Automatic client creation on first order
- Per-client debt balance tracking
- Manual client profile updates

### 🏭 Supplier Management
- Supplier registration (name, phone)
- On-the-fly creation when adding a new product

### 💸 Expense Tracking
- Log operational expenses (label, amount, date)
- Integrated into the global profit calculation

### 📊 Dashboard
- At-a-glance summary: total sales, total cost, expenses, **net profit**
- Remaining stock value
- Order line history and expense log

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
  <img src="screenshots/creer_commande.png" width="880" alt="Create Commande"/>
</p>

### Create Produit
<p align="center">
  <img src="screenshots/creer_produit.png" width="880" alt="Create Produit"/>
</p>

### Purchase Invoice
<p align="center">
  <img src="screenshots/facture_achat.png" width="880" alt="Purchase Invoice"/>
</p>

### Create Purchase Invoice
<p align="center">
  <img src="screenshots/creer_facture_achat.png" width="880" alt="Create Purchase Invoice"/>
</p>

### Invoice
<p align="center">
  <img src="screenshots/facture.png" width="440" alt="Invoice"/>
</p>

---

## 🗄️ Data Model

```
Supplier  ──< Product >──< OrderLine >── Order >── Client
                                            │
                                    (qty, sale_price)

Expense  (standalone, aggregated in dashboard)
```

Key relationships:
- A **Client** has many **Orders**
- An **Order** contains many **Products** through the `commande_produits` pivot table (storing `quantity` and `sale_price` at the time of sale)
- A **Product** belongs to a **Supplier**

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|------------|
| Backend | **Laravel 11** (PHP 8.x) |
| Database | **MySQL** (production) / SQLite (dev) |
| Hosting | **Hostinger** (shared hosting) |
| PDF | **FPDF** via `codedge/laravel-fpdf` |
| Frontend | **Blade** + Bootstrap + TinyMCE |
| Auth | Laravel Auth (sessions) + Policies |
| ORM | Eloquent (with DB transactions) |

---

## 🔐 Security & Reliability

- All routes protected by the `auth` middleware
- Critical operations (create / cancel / edit orders) wrapped in **DB transactions** with rollback on failure
- Request validation via `FormRequest` classes and inline rules
- Negative stock guard enforced in the model's `booted()` hook
- Authorization via `ClientPolicy`


---

## 💡 What This Project Demonstrates

- Strong command of **Laravel** (routing, Eloquent ORM, Blade, middleware, policies, form requests)
- Handling **complex business logic**: stock management, client debt tracking, cascading cancellation effects
- Generating **custom PDF documents** (print-ready invoices)
- Using **database transactions** to guarantee data integrity
- Clean MVC architecture with proper separation of concerns
- **Real-world deployment** on shared hosting (Hostinger) with Laravel
- Ability to deliver a complete, production-grade application solving an actual business need

---

## ⚖️ License

© 2024 Kounabé Paulin MIEN. Built for private consumer.  
This repository is shared for demonstration purposes only.  
