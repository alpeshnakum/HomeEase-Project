# HomeEase – Project Architecture

Back to: [MASTER.md](../MASTER.md) · Related: [PROJECT_SCOPE.md](PROJECT_SCOPE.md) · [FREE_TECH_STACK.md](FREE_TECH_STACK.md)

---

## 1. Purpose

This document shows how the parts of the HomeEase website fit together: which tool does what, where data is stored, how statuses change, and how the main business flows work. The **full field lists** are in the phase that builds each feature. This document gives the overview and the relationships.

## 2. System overview

```
                         ┌────────────────────────────────────────────┐
  Visitor / Customer ──► │  Kadence theme + HomeEase child theme      │  (design, header, footer)
  (mobile / desktop)     │  Block editor pages + Kadence Blocks       │  (public pages)
                         └───────────────┬────────────────────────────┘
                                         │
         ┌───────────────────────────────┼───────────────────────────────────┐
         ▼                               ▼                                   ▼
┌──────────────────┐        ┌──────────────────────────┐         ┌──────────────────────┐
│ WooCommerce      │◄──────►│ HomeEase Core (custom)   │◄───────►│ Secure Custom Fields │
│ products, cart,  │ hooks  │ booking, slots, statuses │ fields  │ field groups, Offer, │
│ checkout, orders,│        │ memberships, reminders,  │         │ Technician, Service  │
│ coupons, My Acct │        │ technicians, dashboard,  │         │ Area structures      │
└────────┬─────────┘        │ shortcodes, emails       │         └──────────────────────┘
         │                  └────────────┬─────────────┘
         ▼                               ▼
┌──────────────────┐        ┌──────────────────────────┐
│ Razorpay for     │        │ FluentSMTP → mailbox     │──► Customer / Admin / Technician emails
│ WooCommerce      │──────► │ (all wp_mail emails)     │
│ (test → live)    │ API    └──────────────────────────┘
└──────────────────┘
   Razorpay servers

 Support plugins: Contact Form 7 + Flamingo · Yoast SEO · Wordfence · UpdraftPlus · cache plugin
```

**Principle: WooCommerce handles money, HomeEase Core handles time.** WooCommerce owns products, prices, cart, coupons, orders, payments and refunds. HomeEase Core owns dates, slots, bookings, memberships, technicians and the related screens. They are linked by IDs stored on both sides.

## 3. What each part is responsible for

| Responsibility | Handled by | Built in phase |
|---|---|---|
| Header, footer, colours, fonts, layout | Kadence theme + child theme CSS | 2 |
| Public page content | Block editor + Kadence Blocks | 2 |
| Services and prices | WooCommerce products (Simple, Virtual) | 3 |
| Service categories | WooCommerce product categories | 3 |
| Service details (duration, price type, allowed slots…) | SCF field group on products | 3 |
| Service areas + PIN codes | Custom taxonomy `he_service_area` (registered with SCF), with SCF term fields | 3 |
| Booking form, slots, capacity | HomeEase Core | 4 |
| Booking records and statuses | HomeEase Core: custom post type `he_booking` | 4 |
| Packages (6 products) | WooCommerce products in the category "Maintenance Packages" | 5 |
| Membership records, expiry, reminders | HomeEase Core: custom post type `he_membership` + WP-Cron | 5 |
| Member discount and visit-charge waivers | HomeEase Core (per-line member price via the WooCommerce cart calculation hook) | 5 |
| Checkout and payment | WooCommerce classic checkout + Razorpay | 1 (setup) / 6 |
| Coupons | WooCommerce coupons | 6 |
| Offers and banners | Custom post type `he_offer` (registered with SCF) + HomeEase Core shortcode and top bar | 6 |
| Registration, login, password reset | WooCommerce My Account + HomeEase Core extra fields | 7 |
| Customer dashboard tabs | HomeEase Core custom My Account endpoints | 7 |
| Technicians | Custom post type `he_technician` (registered with SCF) + HomeEase Core assignment logic | 8 |
| Admin dashboard widgets, schedule view | HomeEase Core | 8 |
| Business settings (phone, slots, holidays…) | HomeEase Core settings page (option `he_settings`) | 1 (skeleton) / 4 |

## 4. Data model

### 4.1 Entities and relationships

```
 WordPress User (role: customer)
   │ 1
   ├──< many  WooCommerce Order ──< many Order Line Items
   │                                     │ 1 (service line item)
   │                                     └── 1  he_booking ──> 1 Service (product)
   │                                                  │        └─> 1 Service Category (product_cat)
   │                                                  ├──> 1 Service Area (he_service_area)
   │                                                  ├──> 0..1 he_technician
   │                                                  └──> 0..1 he_membership (if a benefit was used)
   │ 1
   └──< many  he_membership ──> 1 Package product ──> 1 Order (purchase)

 he_technician ──> many Service Categories (skills), many Service Areas
 he_offer ──> 0..1 WooCommerce coupon (by code)
```

### 4.2 Entity summary

| Entity | WordPress storage | Key fields (full list in…) | Created by |
|---|---|---|---|
| Service | `product` (Simple, Virtual) | price, duration, price type, allowed slots, areas, booking enabled… ([Phase 3](../01-Phases/PHASE_03_SERVICES_MANAGEMENT.md)) | Admin |
| Service Category | `product_cat` term | name, image, icon, description, slot capacity ([Phase 3](../01-Phases/PHASE_03_SERVICES_MANAGEMENT.md)) | Admin |
| Service Area | `he_service_area` term | name, PIN codes, active ([Phase 3](../01-Phases/PHASE_03_SERVICES_MANAGEMENT.md)) | Admin |
| Booking | `he_booking` post | booking number, customer, service, date, slot, address, status, technician, history ([Phase 4](../01-Phases/PHASE_04_BOOKING_SYSTEM.md)) | System, on payment |
| Package | `product` in "Maintenance Packages" | tier, period, validity days, benefits ([Phase 5](../01-Phases/PHASE_05_MAINTENANCE_PACKAGES.md)) | Admin |
| Membership | `he_membership` post | membership number, customer, package, start, expiry, status, waivers used ([Phase 5](../01-Phases/PHASE_05_MAINTENANCE_PACKAGES.md)) | System, on payment (or admin manually) |
| Coupon | `shop_coupon` (WooCommerce) | code, type, amount, limits ([Phase 6](../01-Phases/PHASE_06_PAYMENT_AND_COUPONS.md)) | Admin |
| Offer | `he_offer` post | headline, banner, coupon code, dates, placement ([Phase 6](../01-Phases/PHASE_06_PAYMENT_AND_COUPONS.md)) | Admin |
| Customer profile | WordPress user + user meta | name, email, mobile, default address ([Phase 7](../01-Phases/PHASE_07_CUSTOMER_DASHBOARD.md)) | Customer at registration |
| Technician | `he_technician` post (not public) | name, phone, skills, areas, availability ([Phase 8](../01-Phases/PHASE_08_ADMIN_AND_TECHNICIAN_MANAGEMENT.md)) | Admin |
| Settings | option `he_settings` (one array) | business contact details, slots, holidays, capacity default, notification email ([Phase 4](../01-Phases/PHASE_04_BOOKING_SYSTEM.md), [Phase 8](../01-Phases/PHASE_08_ADMIN_AND_TECHNICIAN_MANAGEMENT.md)) | Admin |

### 4.3 Linking rules

- Each **service line item** in an order stores the booking details as visible item meta (Service date, Time slot, Area, Address, Instructions), so they show in WooCommerce emails and order screens without extra code. It also stores a hidden `_he_booking_id` once the booking exists.
- Each **booking** stores `he_order_id` and `he_order_item_id`.
- Each **membership** stores `he_order_id`, and the order stores a hidden `_he_membership_id`.
- Booking and membership **numbers** are human-readable: `HE-B-000001` and `HE-M-000001`. They come from a counter kept in options (`he_booking_counter`, `he_membership_counter`), not from the post ID.

## 5. Status lifecycles

### 5.1 Booking status (BR-B-10, BR-B-11)

```
                ┌──────────── Cancelled (final) ◄───────────┐
                │                  ▲                         │
 (payment OK) ──► Pending ──► Confirmed ──► Assigned ──► In Progress ──► Completed (final)
                                  ▲            │
                                  └────────────┘  (technician removed)
```

| From → To | Who can do it | Automatic side effects |
|---|---|---|
| (none) → Pending | System, after payment or a ₹0 order | Customer email "Booking received", admin email "New booking" |
| Pending → Confirmed | Staff | Customer email "Booking confirmed" |
| Confirmed → Assigned | Staff (by selecting a technician) | Customer email "Technician assigned", technician email "New job" |
| Assigned → Confirmed | Staff (by removing the technician) | None (internal) |
| Assigned → In Progress | Staff | None (optional customer email: COULD) |
| In Progress → Completed | Staff | Customer email "Job completed". Order set to Completed when all its bookings are Completed. |
| Pending / Confirmed / Assigned → Cancelled | Staff, or the customer (BR-B-12) | Customer and admin emails. Waiver returned (BR-M-07). Staff reminded to refund. |
| Order Cancelled / Refunded (full) → booking Cancelled | System | Same as above, without the refund reminder |

### 5.2 Membership status (BR-M-03 to BR-M-12)

```
 (payment OK, no active membership) ─────────────► Active ──(expiry date passes)──► Expired
 (payment OK, active membership exists) ─► Upcoming ──(start date)──► Active
 Active ──(customer requests)──► Cancellation Requested ──(staff approves)──► Cancelled
                                          └──────────(staff rejects)──► Active
```

### 5.3 Order status mapping (WooCommerce, BR-P-02 and BR-P-03)

| WooCommerce order status | Meaning for HomeEase |
|---|---|
| Pending payment | Checkout started, not paid. Nothing reserved. |
| Failed | Payment failed. The customer can retry from Payment History. |
| Processing | Paid. Bookings created as Pending, and staff now manage them. |
| Completed | Package orders: immediately after payment. Service orders: when all bookings are Completed. |
| Cancelled | Unpaid order cancelled, or cancelled by staff. Linked bookings are Cancelled. |
| Refunded | Fully refunded. Linked bookings or the membership are Cancelled. |

## 6. Key business flows

### 6.1 Book a service (one-time)

1. Customer opens a service page → clicks **Book Now** → the page scrolls to the booking form.
2. Selects a **service area** → enters a **PIN code** (checked immediately).
3. Picks a **date** (only allowed dates are clickable) → the **slot list** loads by AJAX, showing Available or Full.
4. Enters address and instructions → **Add booking to cart** (validated again on the server).
5. Cart shows the booking details, any member discount and the coupon box → **Proceed to checkout**.
6. Logs in or registers (guest checkout off) → reviews the order → **Pay with Razorpay**.
7. Payment succeeds → order "Processing" → HomeEase Core creates one `he_booking` per service item (status Pending) → emails sent.
8. Staff confirm → assign technician → In Progress → Completed.

### 6.2 Buy a maintenance package

1. Customer opens **Pricing** → toggles Monthly / Yearly → clicks **Buy Standard**.
2. The package is added to the cart (any services in the cart are blocked, BR-C-07) → checkout → Razorpay.
3. Payment succeeds → order set to Completed → HomeEase Core creates a `he_membership` (Active, or Upcoming if one is already Active) with dates and copied benefits → "Membership activated" email.

### 6.3 Renew a package (manual)

1. Daily job sends reminders (BR-M-09) with a **Renew now** link.
2. Link → My Packages → **Renew** → same package (or a different one) in cart → checkout → pay.
3. New membership: Upcoming (if the old one is still Active) or Active (if it has expired).

### 6.4 Cancel a booking (customer)

My Bookings → booking → **Cancel booking** (shown only within BR-B-12) → reason → confirm → status Cancelled → staff get "Refund required" in the admin email and dashboard → staff refund from the order.

### 6.5 Cancel a membership (request)

My Packages → **Request cancellation** → reason → status Cancellation Requested → staff approve (Cancelled + refund as per BR-M-12) or reject (Active) → customer emailed.

### 6.6 Payment failure

Razorpay popup closed or payment declined → order stays Pending payment or Failed → customer sees an error message and a **Pay** button in Payment History → no booking, no membership, slot not held.

## 7. Roles and permissions

| Capability / screen | Administrator | Shop Manager | Editor | Customer | Visitor |
|---|---|---|---|---|---|
| Install / update plugins and themes | ✅ | ❌ | ❌ | ❌ | ❌ |
| WooCommerce settings and Razorpay keys | ✅ | ❌ | ❌ | ❌ | ❌ |
| HomeEase → Settings (slots, holidays, contact info) | ✅ | ❌ | ❌ | ❌ | ❌ |
| Services, categories, areas, prices | ✅ | ✅ | ❌ | ❌ | ❌ |
| Packages (products) and prices | ✅ | ✅ | ❌ | ❌ | ❌ |
| Bookings: view, status, assign, reschedule | ✅ | ✅ | ❌ | Own only (view, cancel) | ❌ |
| Memberships: view, approve or reject cancellations, extend | ✅ | ✅ | ❌ | Own only (view, renew, request cancel) | ❌ |
| Technicians | ✅ | ✅ | ❌ | ❌ | ❌ |
| Orders, payments, refunds | ✅ | ✅ | ❌ | Own orders only | ❌ |
| Coupons and offers | ✅ | ✅ | ❌ | Apply a coupon | Apply at checkout after login |
| Customers list | ✅ | ✅ (view, edit details) | ❌ | Own profile | ❌ |
| Create or delete staff users / change roles | ✅ | ❌ | ❌ | ❌ | ❌ |
| Pages, posts, media, menus | ✅ | ❌ (menus ❌) | ✅ (pages, posts, media) | ❌ | ❌ |
| Privacy requests (export/erase) | ✅ | ❌ | ❌ | Can submit a request | ❌ |

**Implementation:** the custom post types `he_booking`, `he_membership`, `he_technician` and `he_offer` use their own capability type (for example `edit_he_bookings`). HomeEase Core grants these capabilities to `administrator` and `shop_manager` when the plugin is activated. The settings page requires `manage_options` (Administrator only).

## 8. Notification matrix

All emails are sent with `wp_mail()` through FluentSMTP and use the WooCommerce email template wrapper, so they all look the same. Every email includes the booking or membership number and a link to the customer's account.

| # | Event | Customer | Admin / staff | Technician | Phase |
|---|---|---|---|---|---|
| N1 | Account created | ✅ (WooCommerce) | – | – | 7 |
| N2 | Password reset | ✅ (WooCommerce) | – | – | 7 |
| N3 | Order paid (receipt) | ✅ (WooCommerce "Processing") | ✅ (WooCommerce "New order") | – | 6 |
| N4 | Payment failed | – | ✅ (WooCommerce "Failed order") | – | 6 |
| N5 | Booking received (Pending) | ✅ | ✅ "New booking" (marked PRIORITY if applicable) | – | 4 |
| N6 | Booking confirmed | ✅ | – | – | 4 |
| N7 | Technician assigned | ✅ (technician first name only) | – | ✅ job details | 8 |
| N8 | Booking rescheduled by staff | ✅ | – | ✅ if assigned | 8 |
| N9 | Booking completed | ✅ | – | – | 4 |
| N10 | Booking cancelled | ✅ | ✅ "Refund required" if paid | ✅ if assigned | 4 |
| N11 | Slot conflict detected | – | ✅ urgent | – | 4 |
| N12 | Membership activated / upcoming | ✅ | ✅ | – | 5 |
| N13 | Renewal reminders (−7, −1, 0, +3 days) | ✅ | – | – | 5 |
| N14 | Membership expired | ✅ (the day-0 reminder doubles as this) | – | – | 5 |
| N15 | Membership cancellation requested | ✅ acknowledgement | ✅ | – | 5 |
| N16 | Membership cancellation approved / rejected | ✅ | – | – | 5 |
| N17 | Refund issued | ✅ (WooCommerce "Refunded") | – | – | 6 |
| N18 | Contact form message | ✅ auto-reply | ✅ | – | 2 |

## 9. Scheduled jobs (WP-Cron)

| Hook name | Runs | Does |
|---|---|---|
| `he_daily_membership_job` | Daily at 08:00 (site time Asia/Kolkata) | Turn Upcoming → Active on the start date, Active → Expired after the expiry date, send reminders (N13), record which reminders were sent |
| `he_daily_admin_digest` (SHOULD) | Daily at 07:30 | Email staff today's bookings, unassigned bookings, pending cancellations |

On live hosting, add a **server cron** that calls `wp-cron.php` every 15 minutes and set `DISABLE_WP_CRON` to `true` in `wp-config.php` (Phase 9). On local development, WP-Cron runs when you load pages.

## 10. HomeEase Core plugin structure

Keep it simple: one main file that loads one file per module. No frameworks, no Composer, no build step.

```
wp-content/plugins/homeease-core/
├── homeease-core.php          Plugin header, constants, includes, activation/deactivation hooks, HPOS declaration
├── CHANGELOG.txt              Date + what changed (developer log)
├── includes/
│   ├── settings.php           HomeEase → Settings page (Settings API), helper he_get_setting()
│   ├── roles.php              Capabilities for CPTs, granted on activation
│   ├── post-types.php         Register he_booking and he_membership (code) – he_technician/he_offer via SCF
│   ├── helpers.php            Formatting, booking numbers, date helpers, status labels and colours
│   ├── services.php           Service fields output on product page, [he_services] shortcode
│   ├── booking-form.php       Booking form on product page, AJAX slots, cart item data
│   ├── booking-create.php     Create bookings from paid orders, capacity re-check, conflict flag
│   ├── booking-admin.php      Admin list columns, filters, edit screen meta boxes, status changes
│   ├── memberships.php        Create memberships, benefits, discount fee, waivers, cron job
│   ├── memberships-admin.php  Admin list, cancellation approve/reject, manual membership
│   ├── offers.php             [he_offers] shortcode, top bar
│   ├── account.php            Registration fields, My Account endpoints and templates
│   ├── technicians.php        Assignment dropdown, job lists, availability checks
│   ├── dashboard.php          Dashboard widgets, schedule page
│   └── emails.php             he_send_email() wrapper + all custom email texts
├── templates/                 HTML templates for My Account tabs and emails (overridable)
├── assets/
│   ├── css/he-front.css       Booking form, badges, account tabs
│   ├── css/he-admin.css
│   └── js/he-booking.js       Date limits, slot loading, PIN check (vanilla JS, no jQuery needed)
└── acf-json/                  SCF field group JSON (auto-saved here – see Phase 3)
```

### 10.1 Coding rules (must follow)

1. **Prefix everything** with `he_` (functions, options, meta keys, hooks, CSS classes `he-`).
2. **Security on every form and AJAX call:** check a nonce (`wp_verify_nonce` / `check_ajax_referer`), check permissions (`current_user_can`), **sanitize input** (`sanitize_text_field`, `absint`, `sanitize_email`, `sanitize_textarea_field`) and **escape output** (`esc_html`, `esc_attr`, `esc_url`).
3. **Ownership check:** before showing a booking or membership to a customer, confirm `he_customer_id === get_current_user_id()` (BR-A-04).
4. **Orders:** use `wc_get_order()`, `$order->get_meta()` and `$order->update_meta_data()` + `$order->save()`, never `get_post_meta()` on an order ID (HPOS).
5. **Idempotency:** code that runs on payment must check a flag (for example order meta `_he_bookings_created = yes`) so bookings are never created twice when several hooks fire.
6. **Dates:** store dates as `Y-m-d` and times as `H:i` in site time (Asia/Kolkata). Use `wp_date()` and `current_time()`. Never use the PHP server timezone.
7. **Text shown to users** goes through `__()` / `esc_html__()` with the text domain `homeease`. This makes future translation possible.
8. **No editing of WooCommerce, Kadence or other plugin files.** Use hooks only.

## 11. Environments

| Environment | Where | Purpose | Razorpay keys |
|---|---|---|---|
| Local | Developer's computer (LocalWP / XAMPP) | Daily development | Test |
| Staging | A sub-domain or password-protected copy on the live host (for example `staging.homeease.in`) | Owner review, webhook testing, Phase 10 tests | Test |
| Live | `https://www.<domain>` | Real customers | Live (entered by owner at go-live) |

Move from local to staging/live with UpdraftPlus (free migration: back up, download, upload, restore, with search-and-replace) or the host's migration tool.

## 12. URL map

| URL | What it is |
|---|---|
| `/` | Home |
| `/about-us/`, `/how-it-works/`, `/contact-us/`, `/faqs/`, `/offers/`, `/pricing/` | Public pages |
| `/services/` | Services overview page (all categories) |
| `/service-category/<slug>/` | Category page (WooCommerce product category base set to `service-category`) |
| `/service/<slug>/` | Service detail page (WooCommerce product base set to `service`) |
| `/blog/`, `/blog/<post-slug>/` | Blog list and posts (posts page = Blog; post permalink `/blog/%postname%/`) |
| `/privacy-policy/`, `/terms-and-conditions/`, `/cancellation-refund-policy/` | Policies |
| `/cart/`, `/checkout/` | WooCommerce cart and checkout (classic shortcodes) |
| `/my-account/` and its endpoints | Customer area (see [PROJECT_SCOPE.md](PROJECT_SCOPE.md), section 6.2) |
| `/shop/` | **Not used.** WooCommerce "Shop page" setting left empty; `/shop/` redirects to `/services/` (Phase 3). |
