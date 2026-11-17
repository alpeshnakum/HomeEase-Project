# Phase 6 – Payment Integration, Checkout, Coupons and Offers

| Item | Detail |
|---|---|
| Phase | 6 of 10 |
| Estimated effort | 5 working days (fresher) |
| Depends on | [Phase 1](PHASE_01_WORDPRESS_SETUP.md) (Razorpay test keys), [Phase 4](PHASE_04_BOOKING_SYSTEM.md) (service orders), [Phase 5](PHASE_05_MAINTENANCE_PACKAGES.md) (package products for ANNUAL15) |
| Unlocks | Phase 7 (Payment History tab), Phase 10 (go-live payment switch) |
| Business rules used | BR-P-01 to BR-P-06, BR-C-01 to BR-C-08, BR-B-13, BR-M-12 |

Back to: [MASTER.md](../MASTER.md) · Previous: [Phase 5](PHASE_05_MAINTENANCE_PACKAGES.md) · Next: [Phase 7](PHASE_07_CUSTOMER_DASHBOARD.md)

---

## 1. Phase title

Payment Integration, Checkout, Coupons and Offers: reliable Razorpay payments (test mode) with clear success and failure handling, refunds, order confirmation, the launch coupons (WELCOME20, HOME100, ANNUAL15), and admin-managed offer banners.

## 2. Phase objective

- Every payment outcome (success, failure, abandoned, refund) leaves the customer with a clear message and leaves the business with a correct order status.
- Staff can create and control coupons and offers without a developer.
- Offer banners advertise valid coupons and disappear automatically when they end.

## 3. Business context

Payment is where trust is won or lost. A customer whose payment fails must know whether money was taken and how to retry. Staff must be able to refund cancelled bookings quickly, from one place. Coupons and offers drive first bookings (WELCOME20), basket size (HOME100) and yearly plan sales (ANNUAL15). All of this uses **free** WooCommerce core features and the free official Razorpay plugin. The only cost is Razorpay's per-transaction fee once live (see [FREE_TECH_STACK.md](../00-Project-Planning/FREE_TECH_STACK.md) §3.5).

## 4. Scope of work

**In scope**

- Razorpay settings review
- Webhook (staging)
- Success, failure and abandonment handling
- Automatic cancellation of unpaid orders
- Retry payment
- Order emails text
- Payment reference display
- Refund procedure and booking/membership refund sync
- WooCommerce coupons setup and rules
- Coupon UI on cart and checkout
- `he_offer` post type, offer shortcode, top bar
- Offers page, Home offers section
- Admin offer list warnings
- Payment reporting for staff

**Out of scope**

- Live keys and KYC (owner, at go-live, Phase 10)
- Customer Payment History tab layout (Phase 7)
- Invoicing / GST setup (owner decision D7; see Known limitations)

## 5. Detailed functional requirements

### 5.1 Payment gateway

| ID | Requirement | Priority |
|---|---|---|
| FR-06-01 | Razorpay must be the **only** enabled payment method, in **test mode** with test keys (BR-P-01, BR-P-06). The keys are entered only in WooCommerce → Settings → Payments → Razorpay, never in code, Git or documents. | MUST |
| FR-06-02 | The checkout must show the Razorpay option with the title and description from Phase 1 and a "Secured by Razorpay" note. | MUST |
| FR-06-03 | On **staging**, a Razorpay **webhook** must be set up in the Razorpay dashboard (test mode) pointing to the URL shown in the plugin's settings or documentation, with a webhook secret entered in both places. Enable the events the plugin's official documentation requires. Record the configured events in `CHANGELOG.txt`. | MUST |
| FR-06-04 | Order totals of **₹0** (for example a visit charge fully waived) must complete without opening Razorpay, and the order must go to Processing (bookings created as normal). | MUST |

### 5.2 Payment success

| ID | Requirement | Priority |
|---|---|---|
| FR-06-05 | After a successful payment the customer lands on the **Order received** page showing: "Payment successful" heading, order number, date, total, payment method, the Phase 4 "Your bookings" box or a Phase 5 "Your plan" box (plan name, valid from–to), and buttons "View my bookings" / "View my plan" and "Back to home". | MUST |
| FR-06-06 | The order must be **Processing** (services) or **Completed** (packages) (BR-P-02), with the **Razorpay payment ID** stored as the order's transaction ID and visible in admin. | MUST |
| FR-06-07 | The customer must receive the WooCommerce order email, with HomeEase-specific text: "Processing order" email heading "Payment received – thank you!", and additional content "Your booking details are below. We'll confirm your booking soon. You can track it any time in My Account → My Bookings." The admin must receive the "New order" email. | MUST |
| FR-06-08 | The order details page (customer and admin) must show "Payment reference: {Razorpay payment ID}". | SHOULD |

### 5.3 Payment failure and abandonment

| ID | Requirement | Priority |
|---|---|---|
| FR-06-09 | If the payment fails or the customer closes the Razorpay window, the customer must stay on (or return to) the checkout or order-pay page with an error notice. Expected text: "Your payment was not completed. No money has been taken. Please try again or use a different payment method." If the Razorpay plugin shows its own message instead, record the exact text in testing. Accept it if it clearly says the payment failed; otherwise add the HomeEase notice with the hook described in section 12. | MUST |
| FR-06-10 | A failed or unpaid order must create **no** booking or membership and use **no** slot capacity or waiver (BR-B-08, BR-P-03). | MUST |
| FR-06-11 | The customer must be able to **retry** payment from My Account → Payment History, using WooCommerce's **Pay** button on Pending payment / Failed orders. A successful retry produces the same result as FR-06-05, after the booking slot is **re-validated** (Phase 4 FR-04-19 logic runs on the order-pay page too). If the slot is no longer available, the customer sees "This slot is no longer available. Please cancel this order and book a new slot." | MUST |
| FR-06-12 | Unpaid "Pending payment" orders must be **cancelled automatically after 60 minutes**. Use WooCommerce's built-in unpaid-order cancellation: enable stock management globally (WooCommerce → Settings → Products → Inventory → "Enable stock management") and set "Hold stock (minutes)" = 60, while services and packages themselves don't track stock. Verify on staging that unpaid orders are cancelled. If it doesn't work, add a HomeEase cron fallback (COULD). | SHOULD |
| FR-06-13 | The admin must receive WooCommerce's "Failed order" email for failed payments. | SHOULD |
| FR-06-14 | If money was debited but the order shows Failed (a bank-side issue), the FAQ and refund policy explain automatic reversal (Phase 2 §6.13 item 6). Staff check the Razorpay dashboard with the payment reference. | MUST |

### 5.4 Refunds

| ID | Requirement | Priority |
|---|---|---|
| FR-06-15 | Staff (Shop Manager and Administrator) must be able to refund **fully or partially** from the WooCommerce order screen ("Refund" → amount → "Refund ₹X via Razorpay"). The refund is sent to Razorpay by the plugin, and the order shows an order note with the refund ID (BR-P-04). | MUST |
| FR-06-16 | For an order with several bookings, staff refund only the cancelled booking's line (line-item refund using the booking's line amount). | MUST |
| FR-06-17 | When a refund is created for a line linked to a cancelled booking, that booking's refund status changes to **Refund done** automatically, with a history entry. The same applies to a cancelled membership's order. Staff can still use "Mark refund done" manually (for example for refunds made in the Razorpay dashboard). | SHOULD |
| FR-06-18 | The customer receives WooCommerce's "Refunded order" (full) or "Partially refunded" email, with additional content: "Refunds usually reach your account in 5–7 working days, depending on your bank." | MUST |
| FR-06-19 | Refunding more than the paid amount must be impossible (WooCommerce prevents it; verify). | MUST |

### 5.5 Coupons

| ID | Requirement | Priority |
|---|---|---|
| FR-06-20 | The coupons **WELCOME20**, **HOME100** and **ANNUAL15** must be created in Marketing → Coupons with exactly the settings in section 11.1 (BR-C-03 to BR-C-05). | MUST |
| FR-06-21 | Coupons must be usable on the Cart page ("Coupon code" box + **Apply**) and on the classic Checkout page ("Have a coupon? Click here to enter your code"). | MUST |
| FR-06-22 | A **valid** coupon must show a success notice ("Coupon code applied successfully."), a discount line "Coupon: WELCOME20 −₹X" and the new total, all without a full page reload. A "[Remove]" link must remove it and restore the total. | MUST |
| FR-06-23 | An **invalid** coupon must show WooCommerce's specific error notice and change nothing. Cases that must be tested: code doesn't exist · expired · below minimum spend · cart contains no eligible item (for example ANNUAL15 on a service, WELCOME20 on a package) · usage limit per customer reached · a second coupon when "Individual use only" is on. | MUST |
| FR-06-24 | Per-customer usage limits must be enforced at checkout using the logged-in customer and billing email (WooCommerce standard). | MUST |
| FR-06-25 | Coupon discounts are calculated **after** member prices (BR-C-06). Example: Standard member, AC Service ₹599 → member price ₹539 → WELCOME20 → −₹108 (20% of ₹539, rounded by WooCommerce to whole rupees) → total ₹431. | MUST |
| FR-06-26 | Staff must be able to create, edit, disable (set to Draft) and delete coupons, and view coupon usage in **WooCommerce → Analytics → Coupons**. | MUST |
| FR-06-27 | Coupon codes must not be case-sensitive (WooCommerce standard: "welcome20" works). | MUST |

### 5.6 Offers

| ID | Requirement | Priority |
|---|---|---|
| FR-06-28 | Staff must be able to manage **offers** in HomeEase → Offers (custom post type `he_offer`) with the fields in section 11.2. | MUST |
| FR-06-29 | An offer is **Live** when Active = Yes and today is between the start and end dates (inclusive). Otherwise it is **Scheduled** (future) or **Ended**. The admin list shows this status column. | MUST |
| FR-06-30 | `[he_offers placement="offers_page"]` on the Offers page shows all Live offers with that placement, by display order, as offer cards (Phase 2 §8.6). If there are none, it shows the empty-state text. | MUST |
| FR-06-31 | `[he_offers placement="home" limit="3"]` on the Home page shows up to 3 Live home offers. If none are Live, the **whole Home "Special offers" section is hidden**: put the section heading inside the shortcode output, not in the page. | MUST |
| FR-06-32 | **Top bar:** if an offer with placement "Top bar" is Live, a thin bar (40 px, primary-dark background, white text) appears above the header on all public pages: "{headline} – use code {CODE}" + CTA link + a close ✕. A closed bar stays hidden for that visitor for that offer (browser storage), but reappears for a different offer. Hidden on Cart and Checkout. | SHOULD |
| FR-06-33 | **Copy code** button on offer cards copies the coupon code and shows "Copied!" for 2 seconds. It falls back to selecting the text if the clipboard isn't available. | MUST |
| FR-06-34 | The admin offer list must warn (red text) when an offer's coupon code doesn't exist, is in Draft, or expires **before** the offer's end date: "Coupon HOME100 expires 31/03/2027 – before this offer ends." (BR-C-08) | SHOULD |
| FR-06-35 | Offers must be display-only. Turning an offer off **doesn't** disable the coupon, and staff are told this in the field help text. | MUST |

### 5.7 Payment management (staff)

| ID | Requirement | Priority |
|---|---|---|
| FR-06-36 | Staff must be able to see all payments in **WooCommerce → Orders**, filter by status and date, search by order number, customer name, email or phone, and open order details showing items, booking meta, coupon, totals, payment reference and notes. | MUST |
| FR-06-37 | Staff must be able to see revenue reports in **WooCommerce → Analytics** (Revenue, Orders, Coupons) for any date range. | MUST |
| FR-06-38 | The owner reconciles settlements in the **Razorpay dashboard** (payments, refunds, settlements, fees). The owner guide (Phase 10) explains where to find each. | MUST |

## 6. Page-wise requirements

| Page | What this phase adds |
|---|---|
| Cart | Coupon box, coupon discount line, error and success notices |
| Checkout | Coupon link, Razorpay option, failure notice, prepaid notice (Phase 5) |
| Order received | Success layout (FR-06-05) |
| Order pay (`/checkout/order-pay/…`) | Retry payment with slot re-validation |
| Offers (`/offers/`) | Live offer cards (replaces the placeholder) |
| Home | Special offers section (replaces the placeholder, auto-hidden) |
| All public pages | Optional top bar |
| Admin | Coupons, HomeEase → Offers, Orders, Analytics |

## 7. Required page sections

### 7.1 Order received page

| # | Section | Content |
|---|---|---|
| 1 | Success header | ✓ icon, H1 "Payment successful", "Order #{number} · {date}" |
| 2 | Bookings / plan box | From Phase 4 / Phase 5 |
| 3 | Order summary | WooCommerce order details table (items with booking meta, coupon, total, payment method, payment reference) |
| 4 | Next actions | Buttons "View my bookings" / "View my plan", "Book another service" |

### 7.2 Cart page (adds to WooCommerce default)

Items with booking meta → coupon box → totals box (Subtotal, member savings shown in the lines, Coupon line, **Total**) → "Proceed to checkout" (primary, full width on mobile) → trust line "🔒 Secure payment via Razorpay".

### 7.3 Offer card (on Offers and Home pages)

Banner image (or primary-dark background if none) → headline (H3) → sub-text → coupon code box + Copy button → "Valid till {DD/MM/YYYY}" → CTA button → "Terms apply" link (to the Offers page terms section).

## 8. UI components

| Component | Specification |
|---|---|
| Coupon input | Text field + "Apply" button on one row (stacked on ≤ 360 px). Uppercase display. |
| Coupon discount line | "Coupon: WELCOME20  −₹108  [Remove]" |
| Success / error notices | Phase 2 §8.8 |
| Payment failure notice | Error notice with a "Try again" button that scrolls to the payment section |
| Top bar | 40 px, centred text, ✕ button with `aria-label="Close offer"` |
| Offer status pill (admin) | Live (green) / Scheduled (blue) / Ended (grey) |
| Coupon warning (admin) | Red text below the offer title in the list |

## 9. Customer interactions

| # | Interaction | Expected behaviour |
|---|---|---|
| CI-1 | Customer applies WELCOME20 to a ₹599 service (non-member) | −₹120, total ₹479 |
| CI-2 | Customer applies WELCOME20 a second time on a new order after using it once | Error: usage limit reached for this customer |
| CI-3 | Customer applies HOME100 to a ₹399 cart | Error: minimum spend ₹500 |
| CI-4 | Customer applies ANNUAL15 to Standard Yearly ₹5,999 | −₹900, total ₹5,099 |
| CI-5 | Customer applies ANNUAL15 to Basic Monthly | Error: coupon not applicable to the selected products |
| CI-6 | Customer types "SAVE50" | Error: coupon doesn't exist |
| CI-7 | Customer pays successfully | Order received page, emails |
| CI-8 | Customer closes the Razorpay window | Failure notice, order Pending payment, can retry |
| CI-9 | Customer retries from Payment History → Pay | Razorpay opens again. On success, normal confirmation. |
| CI-10 | Customer copies an offer code on the Offers page | "Copied!" feedback, code in clipboard |
| CI-11 | Customer closes the top bar | Bar stays hidden on the next pages |

## 10. Administrator interactions

| # | Interaction | Expected behaviour |
|---|---|---|
| AI-1 | Staff create a coupon "MONSOON10" (10% off Pest Control, valid 1–31 July, 1 use per customer) | Works only in that range and only on Pest Control services |
| AI-2 | Staff create an offer "Monsoon Pest Control 10% Off" with code MONSOON10, home + offers page, 1–31 July (Scenario 15) | Appears on Home and Offers on 1 July, disappears after 31 July |
| AI-3 | Staff create an offer with a mistyped code | Red warning in the admin list |
| AI-4 | Staff refund one cancelled booking (₹399) from a 2-booking order | Partial refund via Razorpay, order note, booking refund status "Refund done", customer partial-refund email |
| AI-5 | Staff look up a customer's failed payment | Orders → filter Failed → order note with the Razorpay error |
| AI-6 | Owner checks this month's revenue and coupon usage | Analytics → Revenue / Coupons with the date range |

## 11. Required data fields

### 11.1 Launch coupons (WooCommerce coupon settings; *sample* dates)

| Setting | WELCOME20 | HOME100 | ANNUAL15 |
|---|---|---|---|
| Discount type | Percentage discount | Fixed cart discount | Percentage discount |
| Amount | 20 | 100 | 15 |
| Coupon expiry date | 31/03/2027 | 31/03/2027 | 31/03/2027 |
| Minimum spend | ₹300 | ₹500 | – |
| Maximum spend | – (owner may set, see Known limitations) | – | – |
| Individual use only | Yes | Yes | Yes |
| Exclude sale items | No | No | No |
| Products | – | – | Basic Yearly, Standard Yearly, Premium Yearly |
| Product categories | – | – | – |
| Exclude categories | Maintenance Packages | Maintenance Packages | – |
| Usage limit per coupon | – (unlimited) | – | – |
| Usage limit per user | 1 | 3 | 1 |
| Description (internal) | "Welcome offer – services only" | "Flat ₹100 off ₹500+" | "15% off yearly plans" |

### 11.2 Offer fields (`he_offer`, registered with SCF, not public, Shop Manager can edit)

| Label | Key | Type | Rules |
|---|---|---|---|
| Internal title | (post title) | Text | Required |
| Headline | `he_offer_headline` | Text, max 60 | Required |
| Sub-text | `he_offer_subtext` | Text, max 140 | Optional |
| Banner image | `he_offer_image` | Image, 1200 × 600, ≤ 120 KB | Optional |
| Coupon code | `he_offer_coupon` | Text (uppercase) | Optional; checked against coupons (FR-06-34) |
| CTA label | `he_offer_cta_label` | Text, max 24 | Default "Book now" |
| CTA link | `he_offer_cta_url` | URL | Default `/services/` |
| Start date | `he_offer_start` | Date picker (d/m/Y display, Ymd stored) | Required |
| End date | `he_offer_end` | Date picker | Required, ≥ start |
| Placements | `he_offer_placements` | Checkbox: Offers page / Home / Top bar | At least one |
| Active | `he_offer_active` | True/False | Default true |
| Display order | `he_display_order` | Number | Default 10 |

### 11.3 Payment data shown to staff and customers (from WooCommerce)

Order number, date, status, items (with booking meta), subtotal, coupon code and amount, total, payment method, transaction ID (Razorpay payment ID), refunds (amount, date, reason, Razorpay refund ID in the order note), customer name, email, mobile.

## 12. WordPress implementation expectations

1. **Coupons are configuration only.** No code is needed for FR-06-20 to FR-06-27.
2. **Failure notice (only if needed after testing, FR-06-09):** check the order status on the `woocommerce_before_checkout_form` or order-pay page. If the Razorpay plugin returns with a failed status and no clear notice is shown, add `wc_add_notice( $text, 'error' )`. Don't modify Razorpay plugin files.
3. **Slot re-validation on retry (FR-06-11):** hook `woocommerce_before_pay_action` (runs when the customer submits payment on the order-pay page). For each service line, run the Phase 4 validation with the item meta. On failure, add an error notice and stop.
4. **Refund sync (FR-06-17):** `woocommerce_order_refunded` ( `$order_id`, `$refund_id` ). Load the refund with `wc_get_order( $refund_id )`, loop `get_items()`, map refunded line items to `_he_booking_id`, and set the refund status with the Phase 4 helper. For package orders, update the membership refund status.
5. **Payment reference (FR-06-08):** `$order->get_transaction_id()`. Show it with `woocommerce_order_details_after_order_table` (customer) and `woocommerce_admin_order_data_after_billing_address` (admin).
6. **Offers:**
   - Register the post type with SCF (key `he_offer`, not public, show UI under the HomeEase menu, capability type `he_offer`).
   - The shortcode in `offers.php` queries Live offers with a `meta_query` on start ≤ today ≤ end, uses `Ymd` comparison and checks placements with `LIKE`, or filters in PHP for simplicity.
   - Top bar output on the `wp_body_open` hook (Kadence calls it), unless `is_cart() || is_checkout()`.
   - Close button state saved in `localStorage` key `he_offer_closed_{ID}`. Wrap it in `try/catch`, so the bar simply shows again if storage is blocked.
7. **Coupon validity check (FR-06-34):** in the admin column callback, use `new WC_Coupon( $code )`. If `! $coupon->get_id()` it doesn't exist. Compare `$coupon->get_date_expires()` with the offer end date.
8. **Webhook:** configure on staging only (it needs a public HTTPS URL). Test by making a test payment and checking the Razorpay dashboard webhook delivery log shows HTTP 200.
9. **Fresher notes:**
   - Razorpay test mode has official test cards and UPI IDs for success and failure. Use the ones listed in Razorpay's documentation, and never a real card.
   - Coupon minimum spend is checked against the cart subtotal **after** member prices, so a member's ₹539 line counts as ₹539.

## 13. Free plugins or custom development requirements

| Need | Solution |
|---|---|
| Payment gateway, test mode, refunds, webhooks | Razorpay for WooCommerce (free plugin; transaction fees on live payments only) |
| Orders, statuses, emails, coupons, coupon restrictions, analytics | WooCommerce core (free) |
| Offer fields and post type | Secure Custom Fields (free) |
| Offer shortcode, top bar, coupon warnings, refund sync, retry re-validation, payment reference | **Custom**: HomeEase Core `offers.php` + small additions to `booking-create.php` |

## 14. Dependencies

| Needs | From |
|---|---|
| Razorpay test keys | Owner (Phase 1) |
| Staging site with HTTPS (for the webhook) | Owner's hosting (can be done in Phase 9 if staging isn't ready; record that it's deferred) |
| Service orders and bookings | Phase 4 |
| Package products and membership orders | Phase 5 |
| Final coupon values and dates | Owner (decision D5) |

## 15. Expected deliverables

1. Razorpay fully configured (test). Webhook configured on staging (or deferred with a note).
2. Success, failure and retry flows working, with notices.
3. Automatic cancellation of unpaid orders (verified or fallback).
4. HomeEase text in the WooCommerce emails (processing, completed, refunded, partially refunded).
5. Payment reference displayed.
6. Refund procedure working, with booking and membership refund sync.
7. 3 launch coupons.
8. `he_offer` post type, `[he_offers]` shortcode, top bar, admin warnings.
9. Offers page and Home offers section live.
10. One sample offer per coupon.

## 16. Acceptance criteria

| ID | Criterion |
|---|---|
| AC-06-01 | A successful test payment produces the correct order status, transaction ID, order received page and both emails. |
| AC-06-02 | A failed or cancelled test payment shows a clear failure message, leaves the order Pending payment / Failed, creates no booking or membership, and can be retried successfully from Payment History. |
| AC-06-03 | Retrying an old unpaid order whose slot is now full is blocked with the FR-06-11 message. |
| AC-06-04 | All 3 coupons give exactly the discounts in section 9 (CI-1, CI-4) and reject every invalid case in FR-06-23 with an error notice. |
| AC-06-05 | Coupon + member price calculation matches FR-06-25. |
| AC-06-06 | A partial refund of one booking line is sent to Razorpay (visible in the Razorpay test dashboard), noted on the order, emailed to the customer and marks that booking's refund status done. |
| AC-06-07 | An offer appears only between its start and end dates, in its chosen placements, and the Home offers section is fully hidden when no home offer is Live. |
| AC-06-08 | The admin offer list warns about a missing or early-expiring coupon. |
| AC-06-09 | A ₹0 order (full waiver) completes without Razorpay and creates its booking. |
| AC-06-10 | No Razorpay key appears in any file in the child theme, the custom plugin or Git. |

## 17. Testing scenarios

| ID | Scenario | Steps | Expected result |
|---|---|---|---|
| TC-06-01 | Valid coupon (Scenario 8) | customer3 (new) books AC Service ₹599 → apply `welcome20` | −₹120, total ₹479, success notice |
| TC-06-02 | Invalid coupon (Scenario 9) | Apply `SAVE50`; then `HOME100` on a ₹399 cart; then `ANNUAL15` on a service | Three different error notices, totals unchanged |
| TC-06-03 | Expired coupon | Set WELCOME20 expiry to yesterday → apply | "This coupon has expired." (WooCommerce standard text) |
| TC-06-04 | Per-user limit | customer3 completes an order with WELCOME20 → new booking → apply WELCOME20 | Usage limit error |
| TC-06-05 | ANNUAL15 on a yearly plan | Buy Standard Yearly with ANNUAL15 | Total ₹5,099. Membership amount paid ₹5,099. |
| TC-06-06 | Complete a test payment (Scenario 10) | Pay any booking with a Razorpay test card | Success flow (AC-06-01) |
| TC-06-07 | Payment failure | Use a Razorpay test failure method | Failure flow (AC-06-02) |
| TC-06-08 | Abandoned payment | Close the Razorpay window → wait 60+ min (or shorten "Hold stock" to 5 min in testing) | Order auto-cancelled |
| TC-06-09 | Retry payment | Open Payment History → Pay on a pending order (within 60 min) | Payment succeeds, booking created |
| TC-06-10 | Partial refund | 2-booking order → cancel one booking → refund its line | AC-06-06 |
| TC-06-11 | Admin creates a new offer (Scenario 15) | Create an offer for HOME100, Home + Offers page + Top bar, today → +7 days | Visible on Home, Offers and the top bar. Copy code works. |
| TC-06-12 | Offer ends | Set the end date to yesterday | Gone from all placements. The Home section is hidden if it was the only one. |
| TC-06-13 | Offer coupon warning | Offer with code `HOME1000` | Red warning in the list |
| TC-06-14 | Webhook (staging) | Make a test payment on staging | Razorpay webhook log shows delivered (200) |

## 18. Known limitations

- **WooCommerce core coupons can't cap a percentage discount** (for example "20% up to ₹300"). With WELCOME20 a ₹3,499 deep clean gets ₹700 off. If the owner wants a cap, the options are: a "Maximum spend" (the coupon then stops working above that cart value) or a fixed-amount welcome coupon instead.
- **"First order only" isn't a core coupon rule.** WELCOME20 is limited to one use per customer account/email, which is close but allows existing customers who never used it.
- **Recurring payments aren't supported** (see Phase 5). Every renewal is a new payment.
- **Razorpay transaction fees** (2% + GST on standard domestic payments) apply to live payments. Confirm with Razorpay whether fees are returned on refunds.
- **GST invoices** aren't generated. WooCommerce order emails serve as payment receipts. If the business is GST-registered, the owner's accountant decides the invoicing method. A free invoice plugin may be evaluated later and must pass the free-only check in [FREE_TECH_STACK.md](../00-Project-Planning/FREE_TECH_STACK.md) first.
- Cash or offline payments for bookings aren't taken online (decision D6). Staff can record offline membership payments manually (Phase 5).
- Webhooks can't be tested on localhost.
