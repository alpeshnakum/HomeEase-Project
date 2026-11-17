# HomeEase – Master Overview

**HomeEase – Home Services Booking & Subscription Management Platform**
Document version 1.0 · Prepared 17 November 2026 · Read time: about 3 minutes

---

## What is HomeEase?

HomeEase is a home maintenance and repair business. Customers book trained technicians for everyday home jobs, either one at a time or through a prepaid maintenance membership.

This project builds the HomeEase **website**: a WordPress site where customers find, book and pay for home services online, and where the business team manages the whole operation.

## The problem it solves

Customers today book home services by phone or WhatsApp. They don't know the price in advance, can't choose a time slot and get no written record. The business, for its part, tracks bookings, technicians and payments by hand.

The HomeEase website replaces this with:

- clear service prices,
- online booking with real time slots,
- online payment,
- one dashboard for customers and one for the business.

## Target customers

- Families and working professionals who live in apartments or independent houses in our service areas.
- Landlords and tenants who need regular, reliable maintenance.
- Customers who want predictable yearly maintenance costs through a membership.

## Services we provide

1. Home Cleaning
2. Plumbing Services
3. Electrical Repairs
4. AC Repair & Maintenance
5. Painting Services
6. Pest Control
7. Appliance Repair

## What customers can do

- Browse services, prices, estimated durations and service areas.
- Book a service: choose an area, enter an address, pick a date and a time slot, and add instructions.
- Buy a **Basic, Standard or Premium** maintenance package (monthly or yearly).
- Apply coupon codes (for example `WELCOME20`, `HOME100`, `ANNUAL15`).
- Pay online through Razorpay (UPI, cards, net banking, wallets).
- Register, log in and reset a forgotten password.
- Track booking status and view package validity, renewals and payment history.

## What administrators can manage

Services and categories, prices, bookings and their status, technicians and job assignment, customers, maintenance memberships, coupons, offers and banners, payments and refunds, service areas, and all website content.

## Technology

| Layer | Tool (all free) |
|---|---|
| CMS | WordPress |
| Theme / design | Kadence theme + Kadence Blocks + WordPress block editor |
| Shop, cart, checkout, coupons, orders | WooCommerce |
| Payments | Razorpay for WooCommerce (test mode during development) |
| Booking, memberships, technicians | A small custom plugin, **HomeEase Core**, plus Secure Custom Fields |
| Forms, email, SEO, security, backup, speed | Contact Form 7, FluentSMTP, Yoast SEO, Wordfence, UpdraftPlus, a caching plugin |

## The free-only rule (mandatory)

- **No paid themes, plugins, extensions, page builders, APIs or SaaS. No free trials. No nulled software.**
- Any feature that would need a paid add-on is built as small custom code instead, or is left out and documented.
- **Memberships are prepaid and fixed-term, not automatic recurring billing.** Automatic billing would need the paid *WooCommerce Subscriptions* extension, so customers renew manually after reminder emails.
- **Some costs are not software.** To go live, the business must pay for a domain name and web hosting, and Razorpay deducts a fee of about 2% + GST from each payment. All development and testing can be done at zero cost on a local computer. See [FREE_TECH_STACK.md](00-Project-Planning/FREE_TECH_STACK.md).

## Development phases

| # | Phase | Estimate (working days)* |
|---|---|---|
| 1 | [WordPress installation & configuration](01-Phases/PHASE_01_WORDPRESS_SETUP.md) | 3 |
| 2 | [Website design & public pages](01-Phases/PHASE_02_WEBSITE_DESIGN.md) | 10 |
| 3 | [Service & category management](01-Phases/PHASE_03_SERVICES_MANAGEMENT.md) | 4 |
| 4 | [Booking & scheduling system](01-Phases/PHASE_04_BOOKING_SYSTEM.md) | 12 |
| 5 | [Maintenance packages & memberships](01-Phases/PHASE_05_MAINTENANCE_PACKAGES.md) | 8 |
| 6 | [Payments, checkout, coupons & offers](01-Phases/PHASE_06_PAYMENT_AND_COUPONS.md) | 5 |
| 7 | [Customer registration, login & dashboard](01-Phases/PHASE_07_CUSTOMER_DASHBOARD.md) | 6 |
| 8 | [Admin dashboard & technician management](01-Phases/PHASE_08_ADMIN_AND_TECHNICIAN_MANAGEMENT.md) | 6 |
| 9 | [SEO, security, backup & performance](01-Phases/PHASE_09_SEO_SECURITY_PERFORMANCE.md) | 3 |
| 10 | [Testing, bug fixes & handover](01-Phases/PHASE_10_TESTING_AND_HANDOVER.md) | 6 |
| | **Total including a 15% learning buffer** | **≈ 72 working days ≈ 14–15 weeks** |

\* These estimates assume one self-taught fresher developer working full time, 5 days a week. On a 6-day week, expect about 12 weeks. Part-time work takes roughly twice as long. The full breakdown is in [PROJECT_SCOPE.md](00-Project-Planning/PROJECT_SCOPE.md).

**Next step for the developer:** read [README.md](README.md), then start with Phase 1.
