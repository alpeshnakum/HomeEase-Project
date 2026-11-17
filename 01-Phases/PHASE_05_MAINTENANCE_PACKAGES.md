# Phase 5 – Maintenance Packages and Membership Management

| Item | Detail |
|---|---|
| Phase | 5 of 10 |
| Estimated effort | 8 working days (fresher) |
| Depends on | [Phase 1](PHASE_01_WORDPRESS_SETUP.md) (checkout), [Phase 3](PHASE_03_SERVICES_MANAGEMENT.md) (product setup, item type field), [Phase 4](PHASE_04_BOOKING_SYSTEM.md) (booking hooks for benefits) |
| Unlocks | Phase 6 (ANNUAL15 coupon targets yearly packages), Phase 7 (My Maintenance Packages tab) |
| Business rules used | BR-M-01 to BR-M-13, BR-C-07, BR-B-05, BR-B-08 |

Back to: [MASTER.md](../MASTER.md) · Previous: [Phase 4](PHASE_04_BOOKING_SYSTEM.md) · Next: [Phase 6](PHASE_06_PAYMENT_AND_COUPONS.md)

---

## 1. Phase title

Maintenance Packages and Membership Management: Basic, Standard and Premium plans sold as **prepaid, fixed-term memberships** (monthly = 30 days, yearly = 365 days), with automatic member benefits, renewal reminders, manual renewal, cancellation requests and admin management. **No automatic recurring billing.**

## 2. Phase objective

- A customer can compare the 3 plans, buy one (monthly or yearly), and immediately get member prices, visit-charge waivers and priority on their bookings.
- Before expiry, the customer is reminded by email and can renew in a few clicks.
- Staff can see every membership, its validity and usage, and can handle cancellation requests and extensions.

## 3. Business context

Memberships give HomeEase predictable income and repeat customers. Automatic recurring billing would need the paid *WooCommerce Subscriptions* extension (₹26,746/year), which breaks the free-only rule. The business therefore sells **prepaid plans with manual renewal**. That is also simpler for customers: nothing is ever charged without their action.

**Important for every text on the site:**

| Say | Never say |
|---|---|
| "Prepaid plan", "valid for 30 / 365 days", "renew when you want" | "Subscription", "auto-renew", "monthly billing", "cancel anytime to stop charges" |

## 4. Scope of work

**In scope**

- 6 package products and their benefit fields
- `he_membership` records
- Creation on payment (Active or Upcoming)
- Validity and expiry calculation
- Daily cron job (activate, expire, reminders)
- Member price and visit-charge waivers in the cart
- Priority and same-day booking hooks
- Cart rules (packages are bought alone, one active plus one upcoming at most, renewal window)
- Pricing page cards and toggle
- Home featured packages
- Service page member-savings box with real values
- Admin Memberships list and edit screen (cancellation approve/reject with suggested refund, extend, adjust waivers, manual membership)
- Emails N12–N16
- Customer-side helper functions for Phase 7

**Out of scope**

- My Maintenance Packages account tab UI (Phase 7, which uses this phase's functions)
- ANNUAL15 coupon (Phase 6)
- Automatic recurring payments (not in the project)

## 5. Detailed functional requirements

### 5.1 Package products

| ID | Requirement | Priority |
|---|---|---|
| FR-05-01 | There must be **6** WooCommerce Simple, Virtual products in the category **Maintenance Packages**: Basic Monthly, Basic Yearly, Standard Monthly, Standard Yearly, Premium Monthly, Premium Yearly. Item type = **Package**. Prices from [PROJECT_SCOPE.md §7.5](../00-Project-Planning/PROJECT_SCOPE.md). | MUST |
| FR-05-02 | Package products must have Catalog visibility **Hidden** (they never appear in service lists or search) and "Sold individually" **on**. | MUST |
| FR-05-03 | Each package product must have the benefit fields in section 11.1, editable by staff. These values drive the Pricing page cards **and** the benefits given at purchase. | MUST |
| FR-05-04 | Changing a package's price or benefits affects **only new purchases**. Existing memberships keep the values copied onto them at purchase (BR-M-13). | MUST |
| FR-05-05 | A package can be withdrawn from sale by setting it to Draft. Its card disappears from the Pricing page, and existing memberships of that package continue unchanged. | MUST |

### 5.2 Pricing page and package display

| ID | Requirement | Priority |
|---|---|---|
| FR-05-06 | The shortcode `[he_packages default="yearly"]` must show a **Monthly / Yearly toggle** and the 3 published package cards for the selected period (Basic, Standard, Premium, in that order). Switching the toggle changes cards instantly, without reloading the page. | MUST |
| FR-05-07 | Each card shows: tier name, price and period ("₹5,999 / year"), for yearly the saving versus 12 monthly payments ("Save ₹1,189 vs monthly"), the benefit lines (section 11.1, `he_pkg_card_lines`), the note "Prepaid · No auto-renewal", and the button **Buy {Tier}**. The card with "Highlight" on shows the "Most popular" ribbon. | MUST |
| FR-05-08 | The **Buy** button adds the package to the cart and takes the customer **straight to checkout**. | MUST |
| FR-05-09 | For a logged-in customer, the cards reflect their status: if they have an Active plan with more than 7 days left, every Buy button is replaced by "Your {Tier} plan is active until {DD/MM/YYYY}. Renewal opens on {expiry − 7 days}." with a link "Need to upgrade? Contact us". In the renewal window, buttons read **Renew with {Tier}**. If they already have an Upcoming plan: "Your next plan starts on {date}." and no Buy buttons. | MUST |
| FR-05-10 | The Home page "Featured maintenance packages" placeholder is replaced by `[he_packages layout="compact" default="yearly"]` (no toggle, button "Compare all plans"). | MUST |
| FR-05-11 | The comparison table on the Pricing page must match the current package fields. It may stay a static table, but its values must be checked against the products whenever prices change (owner guide note), or it may be generated by `[he_package_compare]` (COULD). | SHOULD |
| FR-05-12 | The service page member-savings box (Phase 3, section 7.1 item 3) must read the discount percentages from the published monthly package products. The hard-coded sample text is removed. | MUST |

### 5.3 Cart and purchase rules

| ID | Requirement | Priority |
|---|---|---|
| FR-05-13 | A cart may contain **either** service bookings **or** one package (BR-C-07). Adding a package to a cart with services, or a service to a cart with a package, is blocked with: "Maintenance plans are purchased separately from service bookings. Please complete or empty your cart first." | MUST |
| FR-05-14 | Purchase eligibility (checked at add-to-cart when logged in, and **always** at checkout): **allowed** when the customer has no Active or Upcoming plan, or has an Active plan with ≤ 7 days left and no Upcoming plan. **Blocked** otherwise, with the matching message from FR-05-09. | MUST |
| FR-05-15 | The checkout must show, above the payment button, for package orders: "This is a one-time prepaid payment for {30/365} days of {Tier} benefits. It will **not** renew or charge automatically." | MUST |
| FR-05-16 | Package-only orders must move to **Completed** automatically once paid (BR-P-02). | MUST |

### 5.4 Membership creation and validity

| ID | Requirement | Priority |
|---|---|---|
| FR-05-17 | When a package order is paid, the system creates exactly **one** `he_membership` record (idempotent, like bookings), numbered `HE-M-000001` onwards. | MUST |
| FR-05-18 | **No current plan or the current plan has expired:** status **Active**, start = payment date, expiry = start + validity − 1 day (BR-M-03). Example: monthly bought 17/11/2026 → valid 17/11/2026 to **16/12/2026** inclusive. Yearly bought 17/11/2026 → valid to **16/11/2027**. | MUST |
| FR-05-19 | **Active plan exists (renewal window):** status **Upcoming**, start = current expiry + 1 day, expiry = start + validity − 1 (BR-M-04). The new record stores `he_renewal_of` = the current membership ID. | MUST |
| FR-05-20 | The membership copies from the package at purchase: tier, period, validity days, discount %, waiver count, priority flag, same-day flag, support text, and the amount paid (after any coupon). | MUST |
| FR-05-21 | A membership counts as **in force** on a date when its status is Active (or Cancellation Requested; see FR-05-31) **and** start ≤ date ≤ expiry. Benefit checks must use the dates, not only the stored status, so a plan expiring at 23:59 stops giving benefits immediately, even before the 08:00 cron runs. | MUST |

### 5.5 Member benefits

| ID | Requirement | Priority |
|---|---|---|
| FR-05-22 | **Member price:** for a logged-in customer with an in-force membership (status Active), every cart line for a service with "Member discount eligible" = Yes is priced at the current price × (1 − discount%), **rounded to the nearest rupee**. The cart shows the original price struck through and "{Tier} member price" (BR-C-06). | MUST |
| FR-05-23 | **Visit-charge waiver:** for a service with price type Visit and "Waiver eligible" = Yes, if the membership has waivers remaining, the line price becomes **₹0** with the label "Free visit charge – {Tier} member benefit ({n} of {total} left after this booking)". Waivers are used in cart order, and the remaining count accounts for other lines in the same cart. When a waiver applies, the member discount isn't also applied to that line. | MUST |
| FR-05-24 | A waiver is **recorded** (used count + 1, booking linked to the membership) only when the booking is created after payment. If the customer abandons checkout, no waiver is used. | MUST |
| FR-05-25 | If a booking that used a waiver is cancelled while the membership is still in force, the waiver is **returned** (used count − 1, history note) (BR-M-07). | MUST |
| FR-05-26 | **Priority:** bookings by customers with an in-force Standard or Premium membership are flagged **Priority** (filter `he_booking_is_priority`). They show the amber "Priority" pill in admin and appear first when the Bookings list is sorted by date (BR-M-08). | MUST |
| FR-05-27 | **Same-day booking:** customers with an in-force Premium membership see **today** in the date strip, with slots that start at least the same-day lead time (default 3 hours) from now (filter `he_allow_same_day`) (BR-B-05). | MUST |
| FR-05-28 | If the cart contents change in a way that breaks a benefit (for example the membership expired between adding to cart and checkout), the prices are recalculated at checkout and the customer sees the updated total before paying. | MUST |
| FR-05-29 | The booking form's price summary (Phase 4 FR-04-11) must show the member price or "Free visit charge" line for logged-in members. | SHOULD |

### 5.6 Expiry, reminders and renewal

| ID | Requirement | Priority |
|---|---|---|
| FR-05-30 | A daily job (`he_daily_membership_job`, 08:00 site time) must: (1) set **Upcoming → Active** where start ≤ today; (2) set **Active → Expired** where expiry < today; (3) send reminders (FR-05-32). Each change is recorded in the membership history as "System". | MUST |
| FR-05-31 | A membership in **Cancellation Requested** status gives **no** benefits while the request is open (BR-M-11). If the request is rejected, it returns to Active with the same dates. | MUST |
| FR-05-32 | Reminder emails (N13) go out on: **expiry − 7 days**, **expiry − 1 day**, **expiry day** ("expires today") and **expiry + 3 days** ("has expired – renew now") (BR-M-09). Each is sent **once only** (recorded in `he_reminders_sent`). They are skipped if the customer already has an Upcoming or newer Active membership. | MUST |
| FR-05-33 | Every reminder contains: plan name, expiry date, days left, waivers left, a **Renew {Tier} {period}** button (adds the same package to the cart and goes to checkout), and a link "See all plans" (`/pricing/`). | MUST |
| FR-05-34 | If the job didn't run on a day (for example the server was down), the next run must catch up: send any reminder whose date has passed but which wasn't sent, **except** don't send more than one reminder email per membership per run (send the latest applicable one). | SHOULD |

### 5.7 Cancellation requests

| ID | Requirement | Priority |
|---|---|---|
| FR-05-35 | Function `he_request_membership_cancellation( $membership_id, $reason, $by )`: allowed only for Active memberships owned by the customer (or by staff on behalf). It sets **Cancellation Requested**, stores the reason and date, and sends N15 to the customer (acknowledgement) and the admin. Phase 7 adds the customer button; in this phase staff can record a request from the admin screen ("Record cancellation request"). | MUST |
| FR-05-36 | The admin edit screen must show a **suggested refund amount**, calculated with BR-M-12, with the working shown ("Yearly ₹5,999 · 9 full months left · 2 waivers used × ₹199 · Suggested refund ₹4,101"). | MUST |
| FR-05-37 | **Approve:** status **Cancelled**, refund amount (editable, prefilled with the suggestion) saved, refund status "Refund required" with a link to the order, and N16 sent with the refund amount. Staff then refund in WooCommerce (Phase 6) and click "Mark refund done". | MUST |
| FR-05-38 | **Reject:** a reason is required. Status goes back to **Active**, and N16 (rejected) is sent with the reason. | MUST |
| FR-05-39 | Cancelling an Active membership that has an Upcoming renewal must warn staff: "This customer also has an Upcoming plan {HE-M-…}. Cancel it separately if required." | SHOULD |

### 5.8 Admin membership management

| ID | Requirement | Priority |
|---|---|---|
| FR-05-40 | **HomeEase → Memberships** must list memberships with columns: Membership no. · Customer · Plan (tier + period) · Start · Expiry · Days left · Status (badge; "Expiring soon" label when ≤ 7 days) · Waivers (used / total) · Amount · Order. | MUST |
| FR-05-41 | Filters: status, tier, period, "Expiring in next 7 days", "Expired in last 30 days" (renewal follow-up list). Search by membership no., customer name, email, mobile. | MUST |
| FR-05-42 | The edit screen must have boxes for: Summary (read-only purchase data), Status & actions (approve/reject cancellation, record request), **Extend expiry** (new date + required note, for example "Compensation for delayed service"), **Adjust waivers** (+/− with a required note), Internal notes, History. | MUST |
| FR-05-43 | Staff can add a membership **manually** (for example for cash payment at the office): choose customer, package, start date, payment note (method and reference) and amount. The record is created with the package benefits and marked "Manual – no online order". | SHOULD |
| FR-05-44 | Staff can't delete memberships. Only the Administrator can move one to Trash. | MUST |
| FR-05-45 | Customer data access: functions `he_get_customer_memberships( $user_id )` (all, newest first) and `he_get_active_membership( $user_id, $date )` must return only that user's records. Phase 7 uses them. | MUST |

## 6. Page-wise requirements

| Page | What this phase adds |
|---|---|
| Pricing (`/pricing/`) | Toggle + cards (`[he_packages]`), status-aware buttons, checked comparison table |
| Home | Compact package cards |
| Service details | Real member-savings box; member price in the booking summary |
| Cart / Checkout | Member price and waiver lines, package-only rule, prepaid notice |
| Admin: HomeEase → Memberships | List + edit + manual add |
| Admin: Services & Packages | Package fields |

## 7. Required page sections

### 7.1 Pricing page package area (adds to Phase 2 §7.5)

| # | Section | Content |
|---|---|---|
| 2 | Toggle | "Monthly · **Yearly** (save up to 2 months)" with `role="tablist"` / buttons, keyboard accessible |
| 3 | Cards | 3 cards. Desktop: 3 columns. Tablet: 3 narrow columns. Mobile: stacked in the order Basic → Standard → Premium, with Standard highlighted. |
| 3a | Logged-in status strip | Above the cards for logged-in customers with a plan: "Your plan: Standard Yearly · Active until 16/11/2027 · 20 of 24 free visit charges left" + "Manage my plan" link (active from Phase 7) |

### 7.2 Cart line (member)

```
AC Service – Split AC
Service date: Wed, 19/11/2026 · 10:00 AM – 12:00 PM
~~₹599~~  ₹539   Standard member price
```

```
Plumber Visit & Inspection
~~₹199~~  ₹0     Free visit charge – Standard member benefit (1 of 2 left after this booking)
```

## 8. UI components

| Component | Specification |
|---|---|
| Period toggle | Two pill buttons in a rounded container. The active one has a primary background. Switches cards with no page reload. |
| Package card | Phase 2 §8.5 + status-aware button (FR-05-09) |
| Savings tag | Small accent pill "Save ₹1,189" on yearly cards |
| Member price line | Struck original price in muted colour, new price bold, label in success colour |
| Prepaid notice | Info notice style, with an icon, above the checkout payment button |
| Membership status badges | Phase 2 §8.7 |
| Admin "Expiring soon" | Amber pill in the list |
| Suggested refund box | Grey panel showing the calculation lines and an editable final amount |

## 9. Customer interactions

| # | Interaction | Expected behaviour |
|---|---|---|
| CI-1 | Customer opens Pricing and switches to Monthly | Monthly prices shown instantly |
| CI-2 | Customer clicks **Buy Standard** (yearly) | Package in cart → checkout page with the prepaid notice |
| CI-3 | Customer pays with Razorpay test | Order Completed. Membership HE-M-… Active, valid for 365 days. N12 email. |
| CI-4 | The member books "AC Service – Split AC" (₹599) | Cart shows ₹539 (Standard 10%) |
| CI-5 | The member books "Plumber Visit & Inspection" | Line ₹0. After payment, waivers used 1 of 24. |
| CI-6 | The member (more than 7 days left) opens Pricing | Buy buttons replaced by the "active until" message |
| CI-7 | The member receives the 7-day reminder and clicks **Renew** | Package in cart → checkout → after payment an **Upcoming** plan starting the day after expiry |
| CI-8 | Customer with services in the cart clicks Buy Basic | Blocked with the FR-05-13 message |
| CI-9 | Premium member opens a service page before midday | Today is selectable, with only slots ≥ 3 hours ahead |

## 10. Administrator interactions

| # | Interaction | Expected behaviour |
|---|---|---|
| AI-1 | Staff open HomeEase → Memberships → filter "Expiring in next 7 days" | Shows the renewal follow-up list with mobile numbers to call |
| AI-2 | Staff update the Standard Yearly price from ₹5,999 to ₹6,499 (Scenario 16) | New price on the Pricing page and for new purchases. Existing Standard Yearly memberships unchanged. |
| AI-3 | Staff change Premium discount from 15% to 20% | New Premium purchases get 20%. Existing Premium members keep 15%. |
| AI-4 | Staff record a cancellation request by phone | Status Cancellation Requested, benefits paused, N15 sent |
| AI-5 | Staff approve the cancellation with the suggested refund | Status Cancelled, refund required, N16 sent |
| AI-6 | Staff reject a request with a reason | Status Active, N16 (rejected) sent |
| AI-7 | Staff extend a membership by 7 days with a note | Expiry updated, history entry, reminders recalculated against the new date |
| AI-8 | Staff add a manual Basic Monthly membership for a cash customer | Record created with Basic benefits, start date as chosen, "Manual" label |

## 11. Required data fields

### 11.1 Package product fields (SCF, shown when item type = Package)

| Label | Key | Type | Sample (Standard Yearly) |
|---|---|---|---|
| Tier | `he_pkg_tier` | Select: basic / standard / premium | standard |
| Period | `he_pkg_period` | Select: monthly / yearly | yearly |
| Validity (days) | `he_pkg_validity_days` | Number (30 or 365; editable) | 365 |
| Service discount (%) | `he_pkg_discount` | Number 0–50 | 10 |
| Visit-charge waivers | `he_pkg_waivers` | Number 0–100 | 24 |
| Priority booking | `he_pkg_priority` | True/False | Yes |
| Same-day booking | `he_pkg_same_day` | True/False | No |
| Support level text | `he_pkg_support` | Text | Email + phone, reply within 12 working hours |
| Card benefit lines | `he_pkg_card_lines` | Textarea (one per line, max 6) | 10% off all eligible services · 24 free visit charges · Priority booking · Phone support |
| Highlight ("Most popular") | `he_pkg_highlight` | True/False | Yes |
| Card order | `he_display_order` | Number | 20 |

Standard WooCommerce fields used: name, price, category "Maintenance Packages", visibility Hidden, Sold individually, Virtual.

### 11.2 Membership record (`he_membership` post meta)

| Key | Type | Notes |
|---|---|---|
| `he_membership_number` | `HE-M-000045` | Sequential, never reused |
| `he_status` | `upcoming` / `active` / `expired` / `cancel_requested` / `cancelled` | |
| `he_customer_id` | User ID | Ownership check |
| `he_package_id` | Product ID | |
| `he_tier`, `he_period`, `he_validity_days` | Snapshot | |
| `he_discount`, `he_waivers_total`, `he_priority`, `he_same_day`, `he_support` | Snapshot | |
| `he_waivers_used` | Integer | |
| `he_start_date`, `he_expiry_date` | `Y-m-d` | |
| `he_order_id` | Order ID or empty | Empty for manual |
| `he_amount_paid` | Decimal | After coupon |
| `he_source` | `online` / `manual` | |
| `he_manual_payment_note` | Text | Manual only |
| `he_renewal_of` | Membership ID | When created as a renewal |
| `he_reminders_sent` | Array, for example `["-7","-1"]` | |
| `he_cancel_reason`, `he_cancel_requested_at` | Text, datetime | |
| `he_cancel_decision`, `he_cancel_decision_note`, `he_cancel_decided_by`, `he_cancel_decided_at` | approved/rejected, text, user, datetime | |
| `he_refund_amount`, `he_refund_status` | Decimal, `not_required`/`required`/`done` | |
| `he_history` | Array of `{time, by, action, note}` | |
| `he_admin_notes` | Text | Private |

### 11.3 Email texts

| Email | Subject | Key content |
|---|---|---|
| N12 Active | "Your {Tier} plan is active – {membership no.}" | Validity dates, benefits list, "How to use your benefits", link to book a service |
| N12 Upcoming | "Your {Tier} plan renewal is confirmed" | "Starts on {start}, valid until {expiry}" |
| N13 −7 | "Your {Tier} plan expires in 7 days" | Expiry date, waivers left, **Renew** button |
| N13 −1 | "Your {Tier} plan expires tomorrow" | Same |
| N13 0 | "Your {Tier} plan expires today" | Same |
| N13 +3 | "Your {Tier} plan has expired – renew to keep your benefits" | Same + "Your member prices and free visit charges have stopped" |
| N15 | "We received your cancellation request – {membership no.}" | "We'll respond within 2 working days. Your benefits are paused meanwhile." |
| N16 Approved | "Your plan cancellation is approved" | Refund amount and timeline |
| N16 Rejected | "Update on your plan cancellation request" | Reason, "Your plan remains active until {expiry}" |

## 12. WordPress implementation expectations

1. **Register `he_membership`** in `post-types.php` (same pattern as `he_booking`: not public, own capability type, Add New allowed only through the manual-membership form).
2. **Creation:** `he_create_membership_for_order( $order_id )` on the same three payment hooks as bookings, with the order meta flag `_he_membership_created`. Use `woocommerce_payment_complete_order_status` to return `completed` for package-only orders.
3. **Member price and waivers:**
   - Hook `woocommerce_before_calculate_totals` (priority 20; skip if `is_admin() && ! wp_doing_ajax()`; guard against running twice with `did_action()`).
   - For each service line, start from the **product's own current price** (`wc_get_product( $id )->get_price()`), never the already-changed cart price, so the discount isn't applied twice.
   - Set it with `$cart_item['data']->set_price( $new )`. Store `he_original_price` and `he_benefit` (`discount`/`waiver`) in the cart item for display.
   - Display via the `woocommerce_cart_item_price` and `woocommerce_cart_item_subtotal` filters.
4. **Recording benefits:** on `he_booking_created` (Phase 4), if the order line had `he_benefit = waiver`, increment `he_waivers_used`, set the booking's `he_membership_id` and `he_waiver_used = yes`. Set `he_is_priority` through the `he_booking_is_priority` filter. On `he_booking_cancelled`, return the waiver (FR-05-25).
5. **Cart rules:** `woocommerce_add_to_cart_validation` (mix rule + eligibility), `woocommerce_check_cart_items` + `woocommerce_after_checkout_validation` (eligibility again after login). Use `woocommerce_add_to_cart_redirect` to send package additions to `wc_get_checkout_url()`.
6. **Cron:** schedule `he_daily_membership_job` in the activation hook with `wp_schedule_event( $next_8am_timestamp, 'daily', 'he_daily_membership_job' )`, and clear it on deactivation. The job uses `WP_Query` with meta queries on dates (stored as `Y-m-d`, so string comparison works with `'type' => 'DATE'`).
7. **Pricing cards:** render all 6 cards in HTML and show or hide by period with a small script (no AJAX needed). Use `$product->get_price()` and `wc_price()`. The Buy link is `esc_url( add_query_arg( 'add-to-cart', $product_id, wc_get_checkout_url() ) )`, or the cart URL with the redirect filter.
8. **Refund suggestion** helper `he_membership_suggested_refund( $membership_id )` returns the amount and the explanation lines (BR-M-12). Full months remaining = whole calendar months between the request date and the expiry date. The waiver value = the current price of the waived bookings' services (from the booking snapshot `he_amount` before the waiver; store `he_original_price` on the booking for this).
9. **Testing time-based logic:** add a developer-only admin button "Run membership job now" (visible only when `WP_DEBUG` is true), and edit dates on test memberships to simulate expiry. Don't change the computer clock.
10. **Fresher notes:**
    - `woocommerce_before_calculate_totals` runs many times per page. Always calculate from the original product price.
    - Test benefits with three test customers: customer1 (no plan), customer2 (Standard Yearly), a new customer4 (Premium Monthly).

## 13. Free plugins or custom development requirements

| Need | Solution |
|---|---|
| Selling packages, checkout, payment, refunds | WooCommerce + Razorpay (free) |
| Package benefit fields | Secure Custom Fields (free) |
| Membership records, validity, cron, reminders, benefits, admin, cards | **Custom**: HomeEase Core `memberships.php`, `memberships-admin.php` |
| Recurring billing | **Not used.** It would need the paid WooCommerce Subscriptions. |

## 14. Dependencies

| Needs | From |
|---|---|
| Item type field, product setup | Phase 3 |
| Booking hooks (`he_booking_created`, `he_booking_cancelled`, filters) | Phase 4 |
| Final package prices and benefits | Owner (decision D3) |
| Refund policy wording | Owner (decision D4) |
| My Packages tab | Phase 7 |
| ANNUAL15 coupon | Phase 6 |

## 15. Expected deliverables

1. 6 package products with benefit fields.
2. Pricing page cards with toggle and status-aware buttons. Home compact cards.
3. Membership creation (Active/Upcoming), validity calculation, idempotency.
4. Member price, waivers, priority, same-day integration.
5. Cart and purchase rules and the prepaid notice.
6. Daily job (activation, expiry, 4 reminders, catch-up).
7. Admin Memberships list, edit (cancellation approve/reject with suggested refund, extend, adjust waivers), manual add.
8. Emails N12, N13, N15 and N16.
9. Helper functions for Phase 7.

## 16. Acceptance criteria

| ID | Criterion |
|---|---|
| AC-05-01 | Buying Standard Yearly on 17/11/2026 creates an Active membership valid 17/11/2026–16/11/2027 with 24 waivers and 10% discount, and the order is Completed. |
| AC-05-02 | Renewing in the last 7 days creates an Upcoming membership starting the day after the current expiry. The daily job activates it on that date. |
| AC-05-03 | Buying while more than 7 days remain, or while an Upcoming plan exists, is blocked on the Pricing page **and** at checkout. |
| AC-05-04 | A Standard member sees ₹539 for a ₹599 eligible service. A non-member sees ₹599. A service marked "not eligible" stays at full price for members. |
| AC-05-05 | Waivers make eligible visit-charge lines ₹0, never exceed the remaining count (including several lines in one cart), are recorded only after payment and are returned when the booking is cancelled. |
| AC-05-06 | Standard/Premium member bookings are flagged Priority. Only Premium members can select today. |
| AC-05-07 | The daily job sends each reminder exactly once at −7, −1, 0 and +3 days and expires plans after the expiry date. No reminder is sent when a renewal already exists. |
| AC-05-08 | Package and service items can't be in the same cart. |
| AC-05-09 | Changing package price or benefits doesn't change existing memberships (Scenario 16). |
| AC-05-10 | Approving a cancellation shows the BR-M-12 suggested refund with the working, sets Cancelled and Refund required, and emails the customer. Rejecting it restores Active. |
| AC-05-11 | No page, email or checkout text uses the words "subscription", "auto-renew" or "automatic billing" for HomeEase plans. |
| AC-05-12 | During Cancellation Requested status, no member price or waiver is given. |

## 17. Testing scenarios

| ID | Scenario | Steps | Expected result |
|---|---|---|---|
| TC-05-01 | Customer purchases a maintenance package (Scenario 7) | customer2 → Pricing → Yearly → Buy Standard → pay (test) | Order Completed. HE-M-… Active 365 days. N12. |
| TC-05-02 | Member price | customer2 books "AC Service – Split AC" | Cart line ₹539 with label |
| TC-05-03 | Waiver | customer2 books "Plumber Visit & Inspection" → pay | Line ₹0. Order total ₹0 (no Razorpay step). Booking created. Waivers 1/24. |
| TC-05-04 | Waiver return | Cancel that booking in admin | Waivers 0/24, history note |
| TC-05-05 | Customer views package validity (Scenario 12, data side) | Check the admin Memberships list for customer2 | Start, expiry, days left and waivers correct |
| TC-05-06 | Early purchase blocked | customer2 opens Pricing | "Active until …" message, no Buy buttons |
| TC-05-07 | Renewal window | Edit customer2's expiry to today + 5 → Pricing | Renew buttons shown. Renew → Upcoming from expiry + 1. |
| TC-05-08 | Reminders | Set expiry = today + 7 → "Run membership job now" → repeat with +1, 0, −3 | One email each time, never repeated on a second run |
| TC-05-09 | Expiry | Set expiry = yesterday → run job | Status Expired. Member price no longer applied. |
| TC-05-10 | Same-day | customer4 with Premium Monthly opens a service page at 09:00 | Today selectable. Slots before 12:00 show "Too soon". |
| TC-05-11 | Mixed cart blocked | Add a service, then Buy Basic | Error message, package not added |
| TC-05-12 | Admin updates package pricing (Scenario 16) | Change Standard Yearly to ₹6,499 | Pricing shows ₹6,499. customer2's membership still shows amount ₹5,999 and the same benefits. |
| TC-05-13 | Cancellation (admin-recorded) | Record request for customer2 → check benefits → approve | Benefits paused while requested. Approval: Cancelled, suggested refund shown, N16. |
| TC-05-14 | Cancellation rejected | New test membership → request → reject with reason | Active again, N16 rejected |
| TC-05-15 | Manual membership | Add Basic Monthly manually for customer1, start today | Record "Manual", benefits active immediately |

## 18. Known limitations

- **No automatic recurring payments.** Customers must renew manually. Renewal rates depend on reminder emails and staff follow-up calls (use the "Expiring in next 7 days" filter).
- Mid-term upgrades or downgrades aren't automated. Staff handle them (for example cancel with a pro-rata refund and sell the new plan, or extend manually).
- Reminder timing depends on the daily job. On live hosting, a real server cron is required (Phase 9). Without it, reminders may be late on low-traffic days.
- Only one Upcoming plan can be queued.
- Unused waivers aren't carried forward and have no cash value.
- The suggested refund is a calculation aid. Staff can edit the final amount, and the refund itself is done manually in WooCommerce.
