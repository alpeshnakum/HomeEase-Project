# HomeEase – Project Scope

Back to: [MASTER.md](../MASTER.md) · Related: [FREE_TECH_STACK.md](FREE_TECH_STACK.md) · [PROJECT_ARCHITECTURE.md](PROJECT_ARCHITECTURE.md)

---

## 1. Purpose of this document

This document defines the scope, the users, the full page list, the **business rules** and the timeline for the HomeEase website. Phase files refer back to the business rules here (IDs such as `BR-B-03`) instead of repeating them. If a phase file and this document disagree, **this document wins**. Report the mismatch to the owner.

## 2. Project objectives (measurable)

| # | Objective | How we measure it |
|---|---|---|
| O1 | Customers can book a service online without calling | A new customer completes a booking with a test payment in under 5 minutes on mobile |
| O2 | Clear pricing | Every active service page shows a price, a price type (fixed / visit charge) and an estimated duration |
| O3 | Recurring income through memberships | Customers can buy, view and renew Basic / Standard / Premium packages, monthly or yearly |
| O4 | Fewer manual tasks for staff | Staff can confirm, assign, reschedule and complete a booking from one admin screen |
| O5 | Zero software cost | Every installed theme and plugin is free on WordPress.org; no paid licence keys are entered anywhere |
| O6 | Trust and safety | HTTPS, policy pages, privacy controls, backups and security plugin in place before launch |

## 3. In scope

- A public marketing website with 13 pages (section 6.1).
- A service catalogue: 7 categories and about 25 sample services.
- A booking system with service areas, PIN code check, date, 2-hour time slots, slot capacity and holidays.
- Booking status workflow: Pending → Confirmed → Assigned → In Progress → Completed, plus Cancelled.
- Prepaid maintenance memberships: 3 tiers × 2 durations, fixed validity, manual renewal, renewal reminders, cancellation requests.
- Checkout, online payment (Razorpay, test mode), coupons, member discounts and offer banners.
- Customer accounts: registration, login, forgot password, dashboard, bookings, packages, payment history, profile, settings.
- An admin area: dashboard summary, services, categories, prices, bookings, customers, memberships, coupons, offers, payments, technicians, service areas and content.
- Simple technician management: profiles, skills, areas, availability, assignment and job lists.
- Email notifications for every important event (section 8 of [PROJECT_ARCHITECTURE.md](PROJECT_ARCHITECTURE.md)).
- Basic SEO, security hardening, backups and performance tuning.
- Testing, bug fixing, an owner user guide and handover.

## 4. Out of scope (not in this project)

| Item | Reason / alternative |
|---|---|
| Automatic recurring card or UPI-AutoPay billing | Needs the paid *WooCommerce Subscriptions* extension (see [FREE_TECH_STACK.md](FREE_TECH_STACK.md)). We use prepaid fixed-term memberships with manual renewal instead. |
| SMS or WhatsApp API notifications | SMS gateways and the WhatsApp Business API are paid. We use email notifications and a free **click-to-chat** WhatsApp link (`https://wa.me/<number>`). |
| Technician login or mobile app | Not needed for a small team. Technicians get job details by email, and office staff update statuses. |
| Live GPS tracking of technicians | Needs paid map APIs and an app |
| Online payment for on-site extra work and spare parts | Final repair quotes are given on site and collected directly. Staff can note them on the booking. |
| Multi-city or multi-branch support | Single city with zones. You can add more zones later without code. |
| Multi-language website | English only. A free translation plugin can be added later if needed. |
| GST invoice PDF generation | WooCommerce order emails act as payment receipts. If the business is GST registered, the owner's accountant decides on invoicing (see Phase 6, Known limitations). |
| Customer reviews and ratings system | Testimonials are managed by admin as content (Phase 2) |
| Login with OTP / social login | SMS OTP costs money. Email and password login is used. |

## 5. Users and roles (summary)

The full permission matrix is in [PROJECT_ARCHITECTURE.md](PROJECT_ARCHITECTURE.md), section 7.

| Role | Who | Access summary |
|---|---|---|
| Visitor | Anyone browsing | Public pages; can start a booking but must log in or register at checkout |
| Customer | Registered user | Own account area only; no access to the WordPress admin |
| Shop Manager | HomeEase office staff | Bookings, orders, coupons, offers, services, packages, technicians, memberships, customers; **no** plugin, theme, user-role or payment-key settings |
| Content Editor (WordPress "Editor") | Marketing / blog person (optional) | Pages, blog posts and media only |
| Administrator | Business owner | Everything |
| Technician | Field worker | **No website login.** Gets assignment emails. |

## 6. Page inventory

### 6.1 Public website (built in Phase 2 unless noted)

| # | Page | URL (slug) | Dynamic content added in |
|---|---|---|---|
| P1 | Home | `/` | Phase 3 (services), Phase 5 (packages), Phase 6 (offers) |
| P2 | About Us | `/about-us/` | – |
| P3 | Services | `/services/` | Phase 3 |
| P4 | Individual Service Details | `/service/<service-name>/` | Phase 3 (details), Phase 4 (booking form) |
| P5 | Pricing & Maintenance Packages | `/pricing/` | Phase 5 |
| P6 | Offers & Discounts | `/offers/` | Phase 6 |
| P7 | How It Works | `/how-it-works/` | – |
| P8 | Contact Us | `/contact-us/` | – |
| P9 | FAQs | `/faqs/` | – |
| P10 | Blog (list + single post) | `/blog/` | – |
| P11 | Privacy Policy | `/privacy-policy/` | – |
| P12 | Terms & Conditions | `/terms-and-conditions/` | – |
| P13 | Cancellation & Refund Policy | `/cancellation-refund-policy/` | – |
| P14 | Service category page (supports P3) | `/service-category/<category>/` | Phase 3 |
| P15 | Cart and Checkout (supports booking and payment) | `/cart/`, `/checkout/` | Phase 4 / Phase 6 |

### 6.2 Customer website (built in Phase 7 unless noted)

| # | Page | URL | Notes |
|---|---|---|---|
| C1 | Registration | `/my-account/` (Register form) | WooCommerce account page plus our extra fields |
| C2 | Login | `/my-account/` (Login form) | Same page as registration, shown side by side on desktop and stacked on mobile |
| C3 | Forgot Password | `/my-account/lost-password/` | WooCommerce built-in |
| C4 | Customer Dashboard | `/my-account/` (logged in) | Custom summary |
| C5 | My Profile | `/my-account/profile/` | Custom endpoint |
| C6 | My Bookings | `/my-account/bookings/` | Custom endpoint (Phase 4 data) |
| C7 | Booking Details | `/my-account/view-booking/<id>/` | Custom endpoint |
| C8 | My Maintenance Packages | `/my-account/packages/` | Custom endpoint (Phase 5 data) |
| C9 | Payment History | `/my-account/orders/` | WooCommerce "Orders", relabelled |
| C10 | Account Settings | `/my-account/edit-account/` | WooCommerce "Account details" plus a privacy request button |

### 6.3 Admin management (inside WordPress admin)

| # | Admin screen | Location in admin | Built in |
|---|---|---|---|
| A1 | Admin Dashboard | Dashboard → "HomeEase Today" widgets | Phase 8 |
| A2 | Service Management | Products (labelled **Services**) | Phase 3 |
| A3 | Service Category Management | Products → Categories | Phase 3 |
| A4 | Booking Management | HomeEase → Bookings | Phase 4 |
| A5 | Customer Management | Users (Customer role) + WooCommerce → Customers | Phase 8 |
| A6 | Subscription / Membership Management | HomeEase → Memberships | Phase 5 |
| A7 | Pricing Management | Product price fields, bulk edit, package benefit fields | Phase 3 / Phase 5 |
| A8 | Coupon Management | Marketing → Coupons | Phase 6 |
| A9 | Offers Management | HomeEase → Offers | Phase 6 |
| A10 | Payment Management | WooCommerce → Orders + Razorpay dashboard | Phase 6 |
| A11 | Technician Management | HomeEase → Technicians | Phase 8 |
| A12 | Service Area Management | Products → Service Areas | Phase 3 |
| A13 | Website Content Management | Pages, Posts, Media, Appearance → Menus, Customizer, HomeEase → Settings | Phase 2 / Phase 8 |

## 7. Business rules

These rules are the single source of truth. Phase files refer to them by ID.

### 7.1 Booking rules (BR-B)

| ID | Rule |
|---|---|
| BR-B-01 | A booking is for **one service, one date and one time slot** at one address. A customer can book several services in one checkout, and each one becomes a separate booking. |
| BR-B-02 | Time slots (*sample*): 08:00–10:00, 10:00–12:00, 12:00–14:00, 14:00–16:00, 16:00–18:00. The slot is the technician's **arrival window**. |
| BR-B-03 | Each service has an "Allowed slots" setting. Long jobs (for example deep cleaning or painting) are allowed only in morning slots. |
| BR-B-04 | **Slot capacity** is set per service category (default 2). The count includes every booking in that category and slot on that date except Cancelled ones. A full slot shows as "Full" and can't be selected. |
| BR-B-05 | Booking window: earliest **tomorrow**, latest **30 days** from today. **Premium members** may also book **today** if the slot starts at least 3 hours from now. |
| BR-B-06 | Working days (*sample*): Monday–Saturday. Sundays and dates on the admin holiday list can't be selected. |
| BR-B-07 | The customer must choose a **service area** that the service is offered in and enter a **6-digit PIN code** that belongs to that area. Otherwise the booking is refused with the message "Sorry, we don't serve this PIN code yet." |
| BR-B-08 | A booking record is created **only after successful payment**, or immediately when the order total is ₹0. Unpaid or failed orders do not reserve slots. |
| BR-B-09 | Slot capacity is checked three times: when the slot list loads, when the service is added to the cart, and when the order is placed. If a slot fills between payment and booking creation, the booking is still created and flagged **"Slot conflict"** so staff can reschedule within 2 working hours. |
| BR-B-10 | Status flow: **Pending → Confirmed → Assigned → In Progress → Completed**. **Cancelled** is possible from Pending, Confirmed or Assigned. Completed and Cancelled are final. |
| BR-B-11 | **Assigned** requires a technician. Selecting a technician on a Confirmed booking sets it to Assigned automatically. Removing the technician sets it back to Confirmed. |
| BR-B-12 | A customer can cancel online **up to 12 hours before the slot starts**, if the booking is Pending, Confirmed or Assigned. Inside 12 hours, the customer must call support. |
| BR-B-13 | When a customer cancels within the allowed window, they get a **full refund** to the original payment method. Staff issue it from the order within 2 working days. Razorpay usually takes 5–7 working days to return the money. Cancellations by HomeEase are always fully refunded. |
| BR-B-14 | Online rescheduling is not offered to customers. Staff can change the date and slot in admin. The capacity rules still apply, but staff can override them with a warning. |
| BR-B-15 | Price types: **Fixed price** (the full job price is paid online) and **Visit charge** (an inspection fee paid online; the final repair price is quoted on site and paid directly). Spare parts are always extra and paid on site. |
| BR-B-16 | Staff must confirm a Pending booking within **6 working hours**, or within **2 working hours** for Priority (Standard/Premium member) bookings. This is a service target shown on the admin dashboard. It is not enforced by code. |

### 7.2 Membership rules (BR-M)

| ID | Rule |
|---|---|
| BR-M-01 | Packages: Basic, Standard and Premium, each **Monthly (30 days)** or **Yearly (365 days)**. That makes 6 purchasable package products. |
| BR-M-02 | Memberships are **prepaid, fixed-term and renewed manually**. There is **no automatic charge**, and this is stated on the Pricing page, at checkout and in the Terms. |
| BR-M-03 | Validity: start date = payment date, or the day after the current membership expires if this is an early renewal. Expiry date = start date + validity days − 1, and the membership ends at 23:59 on the expiry date. |
| BR-M-04 | A customer can hold **only one Active membership** at a time. Buying a package while one is Active creates an **Upcoming** membership that starts the day after the current one expires. Only one Upcoming membership is allowed. |
| BR-M-05 | Statuses: **Upcoming, Active, Expired, Cancellation Requested, Cancelled**. "Expiring soon" is a display label for Active memberships with 7 days or fewer left. It is not a separate status. |
| BR-M-06 | Benefits (*sample*; the owner confirms): see the table in 7.5. The member discount applies only to services marked "Member discount eligible", and only while the membership is Active on the **order date**. |
| BR-M-07 | A visit-charge waiver makes one eligible "Visit charge" service cost ₹0 and uses up one waiver. A cancelled booking returns its waiver if the membership is still Active. Unused waivers expire with the membership and are not carried forward. |
| BR-M-08 | **Priority booking** (Standard and Premium): the booking is flagged "Priority" in admin, listed first and confirmed within 2 working hours. **Same-day booking** is for Premium only (BR-B-05). |
| BR-M-09 | Renewal reminder emails go out **7 days** before expiry, **1 day** before expiry, on the **expiry day** and **3 days after** expiry, unless the customer has already renewed. |
| BR-M-10 | Renewal: the customer can renew from 7 days before expiry onwards, or at any time after expiry. They may pick a different tier or duration when renewing. Mid-term upgrades are handled manually by staff. |
| BR-M-11 | Cancellation: the customer clicks "Request cancellation" and gives a reason. The status becomes **Cancellation Requested** and benefits are paused. Staff approve or reject within 2 working days. |
| BR-M-12 | Refund on approved cancellation (*sample policy*): **Monthly**: full refund if requested within 48 hours of purchase and no benefit was used; otherwise no refund. **Yearly**: full refund within 7 days if no benefit was used. After that, a refund of (yearly price ÷ 12) × each full month remaining, minus the normal price of any visit charges already waived. |
| BR-M-13 | Changing a package price affects **new purchases only**. Existing memberships keep the price and benefits they were bought with, because benefits are copied onto the membership record. |

### 7.3 Coupon and discount rules (BR-C)

| ID | Rule |
|---|---|
| BR-C-01 | Coupons are managed with WooCommerce's built-in coupon system. Codes are not case-sensitive. |
| BR-C-02 | Only **one coupon per order** (the "Individual use only" setting is on for all launch coupons). |
| BR-C-03 | **WELCOME20**: 20% off services only (the Maintenance Packages category is excluded), minimum spend ₹300, **one use per customer**, expiry *sample* 31 March 2027. |
| BR-C-04 | **HOME100**: ₹100 off the cart, services only, minimum spend ₹500, maximum 3 uses per customer, expiry *sample* 31 March 2027. |
| BR-C-05 | **ANNUAL15**: 15% off the **three yearly package products only**, one use per customer, expiry *sample* 31 March 2027. |
| BR-C-06 | Order of calculation: service price → **member price** (the member discount or waiver is applied to each eligible service line, and the cart shows the original price struck through with the label "Standard member price") → coupon on the resulting subtotal → total. A coupon can be used together with a member price. Because every booking line then holds its exact paid amount, refunds per booking are accurate. |
| BR-C-07 | A cart can contain **either** services **or** one maintenance package, not both. This keeps member discounts and coupon rules predictable. |
| BR-C-08 | An offer banner is **display only**. The discount comes from its linked coupon. An offer is hidden automatically after its end date. |

### 7.4 Payment rules (BR-P)

| ID | Rule |
|---|---|
| BR-P-01 | The only online payment method is Razorpay (UPI, cards, net banking, wallets). It stays in **test mode** until the owner approves go-live. |
| BR-P-02 | An order becomes "Processing" when payment succeeds. Package orders are then auto-set to "Completed". Service orders become "Completed" automatically when all their bookings are Completed. |
| BR-P-03 | If payment fails or is abandoned, the order stays "Pending payment" or "Failed". The customer can retry with the **Pay** button in Payment History. No booking or membership is created. |
| BR-P-04 | All refunds are made by staff from the WooCommerce order screen (the Razorpay plugin sends the refund to Razorpay). There are no cash refunds for online payments. |
| BR-P-05 | All prices are shown in ₹ including any applicable taxes. Tax setup is the owner's decision (Phase 6). |
| BR-P-06 | Only Razorpay test credentials may be used during development. Live keys are entered by the owner at go-live. |

### 7.5 Sample package benefit table (owner to confirm)

| Benefit | Basic | Standard | Premium |
|---|---|---|---|
| Monthly price | ₹299 | ₹599 | ₹999 |
| Yearly price | ₹2,999 | ₹5,999 | ₹9,999 |
| Discount on eligible services | 5% | 10% | 15% |
| Visit-charge waivers – monthly plan | 1 | 2 | 4 |
| Visit-charge waivers – yearly plan | 12 | 24 | 48 |
| Priority booking | – | Yes | Yes |
| Same-day booking | – | – | Yes |
| Support | Email, reply within 24 working hours | Email + phone, reply within 12 working hours | Email + phone + WhatsApp, reply within 4 working hours |

### 7.6 Account rules (BR-A)

| ID | Rule |
|---|---|
| BR-A-01 | Guest checkout is **disabled**. A customer must log in or register to complete a booking or purchase. |
| BR-A-02 | Registration requires first name, last name, email, a 10-digit Indian mobile number (starting 6–9), a password, and acceptance of the Terms and Privacy Policy. |
| BR-A-03 | Email address is the unique login ID. A mobile number may be shared by more than one account (family use). |
| BR-A-04 | A customer can see **only their own** bookings, memberships, orders and profile. Opening another customer's booking URL shows a "Booking not found" page. |
| BR-A-05 | Customers never see the WordPress admin area or admin toolbar. |
| BR-A-06 | A customer may ask for their account and personal data to be deleted. Staff process the request using the WordPress privacy tools. Order records may be kept where tax or accounting law requires. |

## 8. Assumptions

1. The business operates in one city, divided into zones (*sample*: Zone A–E). The owner supplies the real zones and PIN codes.
2. About 5–15 technicians and fewer than 50 bookings a day at launch. The design is sized for a small business.
3. The owner opens a free Razorpay account. Test keys are available immediately. Live activation needs business KYC and a website with visible contact details, pricing and policy pages. Razorpay sets the exact KYC list; check it in the Razorpay dashboard.
4. A business email address exists, or a free Gmail account is used to send site emails through FluentSMTP.
5. Free stock photos (for example Unsplash or Pexels, used under their licences) are acceptable until real photos are available.
6. The developer has basic PHP knowledge (variables, functions, arrays, `if`/`foreach`) and can follow WordPress hook examples. The custom plugin is deliberately simple, with no frameworks and no build tools.

## 9. Constraints

- **Zero software budget**: see [FREE_TECH_STACK.md](FREE_TECH_STACK.md).
- WordPress 7.0+ and PHP 8.0+ (WooCommerce 11.x requires WordPress 7.0+ and recommends PHP 8.0+).
- The site must work on the latest Chrome, Edge, Firefox and Safari, and on Android Chrome and iOS Safari, at screen widths from **360 px** upwards.

## 10. Timeline estimate

These estimates assume **one self-taught fresher developer**, full time (about 6 productive hours a day), with the owner replying to questions within 1 working day.

| Phase | Work | Fresher estimate (days) | Experienced developer (days) | Why it takes this long |
|---|---|---|---|---|
| 1 | Setup & configuration | 3 | 1 | Local setup, plugins, WooCommerce and Razorpay test configuration |
| 2 | Design & 13 public pages | 10 | 4 | Most visible work; many pages and responsive checks |
| 3 | Services, categories, areas | 4 | 1.5 | Mostly configuration, some custom fields and templates |
| 4 | Booking & scheduling | 12 | 5 | **Hardest phase**: custom PHP, AJAX slots, capacity, statuses |
| 5 | Memberships | 8 | 3 | Custom records, expiry logic, reminders, discounts |
| 6 | Payments, coupons, offers | 5 | 2 | Configuration plus edge-case testing |
| 7 | Customer dashboard | 6 | 2.5 | Custom My Account tabs |
| 8 | Admin & technicians | 6 | 2.5 | Admin screens, filters, dashboard widgets |
| 9 | SEO, security, performance | 3 | 1 | Configuration and measurement |
| 10 | Testing & handover | 6 | 2.5 | Full test sheet, fixes, guide, training |
| | **Subtotal** | **63** | **25** | |
| | Learning / rework buffer (15%) | **9** | 3 | Self-taught developers lose time on unfamiliar hooks |
| | **Total** | **≈ 72 working days** | **≈ 28 working days** | |

**In calendar terms:**

- Fresher, 5-day week: **about 14–15 weeks (around 3.5 months).**
- Fresher, 6-day week: **about 12 weeks (3 months).**
- Fresher, part time (3–4 h/day): **about 6–7 months.**
- Fastest safe path for a fresher: build a **Minimum Launch Version** first (all MUST items only; skip SHOULD/COULD), which takes **about 10–11 weeks** on a 5-day week. Add the rest after launch.

**Suggested milestones for the owner to review:**

| Milestone | End of week (5-day weeks) | Owner reviews |
|---|---|---|
| M1 – Site set up, design approved | Week 3 | Home page and colour/typography look |
| M2 – Services live with booking form | Week 7 | Book a plumbing service with a test payment |
| M3 – Packages, coupons, payments | Week 10 | Buy a package and apply a coupon |
| M4 – Customer and admin dashboards | Week 13 | Manage a booking end to end |
| M5 – Tested and handed over | Week 15 | Test sheet signed off, owner trained |

## 11. Risks and mitigations

| Risk | Impact | Mitigation |
|---|---|---|
| Fresher gets stuck on custom PHP (booking, membership) | Delay | Requirements give exact hook names and data fields. Build the custom code in small steps and test each one. Allow the 15% buffer. |
| A tutorial leads the developer to a "Pro" feature | Hidden cost | The allowed list in [FREE_TECH_STACK.md](FREE_TECH_STACK.md). The owner checks the plugin list at each milestone. |
| Double booking of the last slot | Customer upset | Triple capacity check plus "Slot conflict" flag (BR-B-09) |
| WP-Cron doesn't run on a low-traffic site, so reminders are late | Missed renewals | Use a real server cron on live hosting (Phase 9) |
| Emails go to spam | Customers miss confirmations | FluentSMTP with an authenticated mailbox, SPF/DKIM on the domain (Phase 9) |
| Razorpay live activation delayed by KYC | Launch delay | The owner starts KYC in week 8, well before handover |
| Plugin update breaks custom code | Site errors | Staging copy, backups before updates, no edits to third-party code |

## 12. Owner decisions log

Record each decision here (or in a shared sheet) with the date, so the developer builds the right thing.

| # | Decision needed | Default used in docs | Decided value | Date |
|---|---|---|---|---|
| D1 | Time slots and working days | BR-B-02, BR-B-06 | | |
| D2 | Slot capacity per category | 2 | | |
| D3 | Package prices and benefits | Section 7.5 | | |
| D4 | Refund policy wording | BR-B-13, BR-M-12 | | |
| D5 | Coupon expiry dates and limits | BR-C-03 to BR-C-05 | | |
| D6 | Offer "Cash after service" payment? | **No** (Razorpay only) | | |
| D7 | GST registration and tax display | Prices tax-inclusive, no tax lines | | |
| D8 | Hosting provider (affects the caching plugin choice) | Any host with PHP 8+, SSL and cron | | |
