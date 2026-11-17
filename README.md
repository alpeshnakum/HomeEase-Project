# HomeEase Project Documentation – Read Me First

This folder holds the complete requirements for the HomeEase website. It describes **what must be built** and **how the business expects it to work**. It is not a general WordPress tutorial.

---

## 1. Who this is for

| Reader | What to read |
|---|---|
| Business owner | [MASTER.md](MASTER.md) and [PROJECT_SCOPE.md](00-Project-Planning/PROJECT_SCOPE.md) |
| WordPress developer | Everything, in the order below |
| Tester / reviewer | [PROJECT_SCOPE.md](00-Project-Planning/PROJECT_SCOPE.md), then the "Acceptance criteria" and "Testing scenarios" sections of each phase, then [Phase 10](01-Phases/PHASE_10_TESTING_AND_HANDOVER.md) |

## 2. Reading order for the developer

1. [MASTER.md](MASTER.md): the business in 3 minutes.
2. [00-Project-Planning/PROJECT_SCOPE.md](00-Project-Planning/PROJECT_SCOPE.md): what is in scope, the **business rules** and the timeline.
3. [00-Project-Planning/FREE_TECH_STACK.md](00-Project-Planning/FREE_TECH_STACK.md): which free tools to install and which ones not to use.
4. [00-Project-Planning/PROJECT_ARCHITECTURE.md](00-Project-Planning/PROJECT_ARCHITECTURE.md): how the parts connect, including the data model, statuses and flows.
5. The phase files in `01-Phases/`, **one at a time, in order**. Don't start a phase until the previous phase's acceptance criteria pass, unless the phase file says otherwise.

## 3. Folder structure

```
HomeEase-Project/
├── MASTER.md                      Short business overview
├── README.md                      This file
├── 00-Project-Planning/
│   ├── PROJECT_SCOPE.md           Scope, business rules, page list, timeline, risks
│   ├── FREE_TECH_STACK.md         Verified free tools + what is NOT free
│   └── PROJECT_ARCHITECTURE.md    Data model, statuses, flows, roles, custom plugin layout
└── 01-Phases/
    ├── PHASE_01_WORDPRESS_SETUP.md
    ├── PHASE_02_WEBSITE_DESIGN.md
    ├── PHASE_03_SERVICES_MANAGEMENT.md
    ├── PHASE_04_BOOKING_SYSTEM.md
    ├── PHASE_05_MAINTENANCE_PACKAGES.md
    ├── PHASE_06_PAYMENT_AND_COUPONS.md
    ├── PHASE_07_CUSTOMER_DASHBOARD.md
    ├── PHASE_08_ADMIN_AND_TECHNICIAN_MANAGEMENT.md
    ├── PHASE_09_SEO_SECURITY_PERFORMANCE.md
    └── PHASE_10_TESTING_AND_HANDOVER.md
```

## 4. How each phase file is organised

Every phase file has the same 18 sections, so you always know where to look:

1. Title · 2. Objective · 3. Business context · 4. Scope of work · 5. Functional requirements · 6. Page-wise requirements · 7. Page sections · 8. UI components · 9. Customer interactions · 10. Administrator interactions · 11. Data fields · 12. WordPress implementation expectations · 13. Free plugins / custom development · 14. Dependencies · 15. Deliverables · 16. Acceptance criteria · 17. Testing scenarios · 18. Known limitations

If a section doesn't apply to a phase, it says so and points to the phase that covers it.

## 5. Conventions used in all documents

| Convention | Meaning |
|---|---|
| **MUST** | Required for launch. The phase is not complete without it. |
| **SHOULD** | Expected. Skip it only with the owner's written approval. |
| **COULD** | Nice to have. Do it only if time remains. |
| `FR-04-07` | Functional requirement 7 of Phase 4 |
| `AC-04-03` | Acceptance criterion 3 of Phase 4 |
| `TC-04-05` | Test case 5 of Phase 4. Phase 10 collects all test cases into one test sheet. |
| `BR-B-03` | Business rule from [PROJECT_SCOPE.md](00-Project-Planning/PROJECT_SCOPE.md) (B = booking, M = membership, C = coupon, P = payment, A = account) |
| `he_` | Prefix for everything custom (post types, fields, functions, options). It stands for **H**ome**E**ase. |
| ₹ | All prices are in Indian Rupees and include any taxes unless stated otherwise |
| *Sample* | Example data (prices, areas, coupon dates). The owner confirms the real values before launch. |

## 6. Glossary (plain English)

| Term | Meaning in this project |
|---|---|
| **Service** | One bookable job, for example "AC Service – Split AC". In WordPress it is a WooCommerce **product**. |
| **Service category** | One of the 7 service groups, for example "Plumbing Services". In WordPress it is a WooCommerce **product category**. |
| **Booking** | One scheduled visit for one service on one date and time slot. It is stored as a custom post type `he_booking`. |
| **Time slot** | A fixed 2-hour arrival window, for example 10:00–12:00. |
| **Slot capacity** | The maximum number of bookings one service category accepts in one slot. |
| **Service area** | A zone of the city we serve, together with its list of PIN codes. |
| **Maintenance package** | A Basic, Standard or Premium plan, bought monthly or yearly. In WordPress it is a WooCommerce product. |
| **Membership** | The record created when a customer buys a package. It has a start date, an expiry date and a status, and is stored as the custom post type `he_membership`. |
| **Visit-charge waiver** | A membership benefit: the customer doesn't pay the visit/inspection charge for an eligible service. |
| **Order** | A WooCommerce order. It is created at checkout and holds the payment. One order can contain several bookings. |
| **Technician** | A field worker who does the job. Technicians are records, not website users, and they don't log in. |
| **Shop Manager** | WooCommerce's built-in staff role. HomeEase office staff use it. |
| **Test mode** | Razorpay sandbox. No real money moves. Use the official test cards and UPI IDs from Razorpay's documentation. |
| **Custom plugin** | `homeease-core`, our own small plugin for the features no free plugin provides |
| **SCF** | Secure Custom Fields, a free plugin for adding custom fields and post types without code |

## 7. Ground rules for the developer

1. **Free only.** Before you install anything, check that it is listed in [FREE_TECH_STACK.md](00-Project-Planning/FREE_TECH_STACK.md). If a tutorial or video uses a "Pro" feature, don't use it. Ask first.
2. **Never edit plugin or theme files directly.** Put design changes in the child theme and logic in the `homeease-core` plugin. Updates will overwrite anything else.
3. **Work locally first.** Build on your own computer (LocalWP or XAMPP, both free), then move to staging or live.
4. **Back up before big changes.** Use UpdraftPlus, or copy the site folder and database.
5. **Use test data only.** Use Razorpay *test* keys and fake customer details. Never use a real card.
6. **Write down what you did.** Keep a simple `CHANGELOG.txt` inside the custom plugin folder with the date and what changed.
7. **When a requirement is unclear, ask before you build.** Questions are cheaper than rework.
8. **Mark a requirement done only when its test case passes.** Record the result in the Phase 10 test sheet.

## 8. Information the business owner must provide

Collect these before the phase that needs them. Until then, use the *sample* values in the documents.

| Needed item | Needed by |
|---|---|
| Logo, brand colours (if any), business name, address, phone, WhatsApp number, email, working hours | Phase 2 |
| Real photos of the team and work (optional; free stock photos are fine at first) | Phase 2 |
| Final list of services, prices and durations | Phase 3 |
| City zones and PIN codes served | Phase 3 |
| Time slots, slot capacity per category, holidays | Phase 4 |
| Final package prices and benefits | Phase 5 |
| Coupon validity dates and limits | Phase 6 |
| Razorpay account (test keys during development; live keys only after KYC) | Phase 1 (test) / Phase 10 (live) |
| Text for the Privacy, Terms and Cancellation & Refund policies, reviewed by the owner (a legal review is recommended) | Phase 2 |
| Domain name and hosting (paid by the owner; not software) | Before go-live (Phase 9/10) |
