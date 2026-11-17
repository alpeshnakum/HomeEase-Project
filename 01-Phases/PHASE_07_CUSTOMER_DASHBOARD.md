# Phase 7 – Customer Registration, Login and Dashboard

| Item | Detail |
|---|---|
| Phase | 7 of 10 |
| Estimated effort | 6 working days (fresher) |
| Depends on | [Phase 1](PHASE_01_WORDPRESS_SETUP.md) (My Account page and settings), [Phase 4](PHASE_04_BOOKING_SYSTEM.md) (booking data and cancel functions), [Phase 5](PHASE_05_MAINTENANCE_PACKAGES.md) (membership data and functions), [Phase 6](PHASE_06_PAYMENT_AND_COUPONS.md) (orders and retry) |
| Unlocks | Phase 10 end-to-end customer tests |
| Business rules used | BR-A-01 to BR-A-06, BR-B-12, BR-B-13, BR-M-10, BR-M-11 |

Back to: [MASTER.md](../MASTER.md) · Previous: [Phase 6](PHASE_06_PAYMENT_AND_COUPONS.md) · Next: [Phase 8](PHASE_08_ADMIN_AND_TECHNICIAN_MANAGEMENT.md)

---

## 1. Phase title

Customer Registration, Login and Dashboard: the HomeEase customer account area, covering registration, login, password reset, dashboard, profile, bookings, booking details with online cancellation, maintenance packages with renewal and cancellation requests, payment history and account settings. Each customer sees **only their own data**.

## 2. Phase objective

A customer can create an account in under 1 minute and, from one place, see everything about their relationship with HomeEase:

- upcoming and past bookings and their live status,
- their plan's validity and benefits,
- every payment.

They can act on these (cancel a booking, renew or request cancellation of a plan, retry a payment, update details) without calling support.

## 3. Business context

Self-service reduces calls to the office, and visible status builds trust ("Technician assigned" removes "is anyone coming?" anxiety). Privacy matters because the account holds home addresses and phone numbers. One customer must never be able to see another customer's data, even by changing a number in the URL.

The account area is built on **WooCommerce My Account (free)**, with custom tabs from HomeEase Core.

## 4. Scope of work

**In scope**

- Registration form fields and validation
- Login and redirects
- Lost and reset password
- Account menu (labels, order, icons)
- Custom Dashboard content
- My Profile (with default service address)
- My Bookings (tabs, list)
- Booking Details (timeline, technician, payment, cancel)
- My Maintenance Packages (current, upcoming, history, renew, request cancellation)
- Payment History (WooCommerce orders relabelled, with retry)
- Account Settings (details, password, privacy requests)
- Booking form pre-fill from the profile
- Access control and ownership checks
- Privacy data exporters

**Out of scope**

- Social login, OTP login (paid SMS)
- Technician-facing accounts
- Admin screens (Phase 8)

## 5. Detailed functional requirements

### 5.1 Registration

| ID | Requirement | Priority |
|---|---|---|
| FR-07-01 | The Register form on `/my-account/` must have: First name*, Last name*, Email*, Mobile number*, Password*, a checkbox "I agree to the Terms and Conditions and Privacy Policy*" (both linked), and a **Create account** button (BR-A-02). | MUST |
| FR-07-02 | Validation (shown next to each field, with the form values kept): names 2–40 letters (spaces, `.`, `'` and `-` allowed); email valid and **not already registered** ("An account is already registered with this email. Please log in or reset your password."); mobile exactly 10 digits starting 6–9; password meets WooCommerce's strength meter (minimum **medium**); terms ticked. | MUST |
| FR-07-03 | On success: the customer is logged in, lands on the Dashboard with the notice "Welcome to HomeEase, {first name}!", and gets the WooCommerce "Your account has been created" email (N1). | MUST |
| FR-07-04 | First and last name are saved to the user profile **and** to the WooCommerce billing first/last name. Mobile is saved to `billing_phone`, so checkout is pre-filled. The terms acceptance time is saved (`he_terms_accepted_at`). | MUST |
| FR-07-05 | The same registration fields and rules apply when an account is created **during checkout** (Phase 1 setting). The terms checkbox there is WooCommerce's checkout terms checkbox. | MUST |
| FR-07-06 | Registration must be protected against bots by Wordfence's login security / reCAPTCHA option (Phase 9) or a honeypot field. At minimum, a hidden honeypot field that rejects submissions when filled. | SHOULD |

### 5.2 Login and password

| ID | Requirement | Priority |
|---|---|---|
| FR-07-07 | The Login form must accept **email or username** + password, with "Remember me" and a "Lost your password?" link. | MUST |
| FR-07-08 | Wrong credentials show one generic message: "The email or password you entered is incorrect." The message must not reveal whether the email exists. | MUST |
| FR-07-09 | After login, the customer goes to: the page they came from if it was the checkout (the cart is kept), otherwise the Dashboard. | MUST |
| FR-07-10 | **Forgot password** (`/my-account/lost-password/`): the customer enters an email or username → message "If an account exists for this email, a reset link has been sent." → the email contains a link valid for **24 hours** → the reset form asks for the new password twice, with the strength meter → success notice and login form. | MUST |
| FR-07-11 | On desktop the Login and Register forms are side by side (two columns). On mobile they are stacked, Login first, with the heading "New to HomeEase? Create an account" above Register. | MUST |
| FR-07-12 | Customers must never see the WordPress admin bar or reach `/wp-admin/` (they are redirected to My Account) (BR-A-05). | MUST |

### 5.3 Account menu

| ID | Requirement | Priority |
|---|---|---|
| FR-07-13 | The My Account menu must contain, in this order: **Dashboard**, **My Bookings**, **My Packages**, **Payment History** (the WooCommerce Orders endpoint, relabelled), **My Profile**, **Account Settings** (the WooCommerce "Account details", relabelled), **Log out**. Downloads and Addresses are removed. | MUST |
| FR-07-14 | On desktop the menu is a left sidebar. On mobile it is a horizontal scrollable tab row (or a dropdown "Go to…") above the content. The active item is highlighted. | MUST |

### 5.4 Dashboard (`/my-account/`)

| ID | Requirement | Priority |
|---|---|---|
| FR-07-15 | Greeting "Hi {first name}" + a log out link. | MUST |
| FR-07-16 | **My plan card:** if in force: tier, period, status badge ("Active" / "Expiring soon"), "Valid till {DD/MM/YYYY} · {n} days left", "{n} of {total} free visit charges left", button "Manage plan". If an Upcoming plan exists, show "Next plan starts {date}". If there's no plan: "Save up to 15% on every service" + "View plans". If expired: "Your plan expired on {date}" + "Renew". | MUST |
| FR-07-17 | **Upcoming bookings:** the next 3 upcoming bookings (number, service, date, slot, status badge, View link), or the empty state "No upcoming bookings. Book a service →". Link "View all bookings". | MUST |
| FR-07-18 | **Quick actions:** Book a service (`/services/`) · Call support (tel link) · WhatsApp us. | MUST |
| FR-07-19 | **Pending payment alert:** if the customer has an order in Pending payment, show the warning "You have an unpaid order #{n}. Complete payment to confirm your booking." + "Pay now" button (Phase 6 retry). | SHOULD |

### 5.5 My Profile (`/my-account/profile/`)

| ID | Requirement | Priority |
|---|---|---|
| FR-07-20 | Shows and lets the customer edit: First name, Last name, Mobile, and a **default service address**: Area (active areas), PIN code (validated against the area), House/Flat & building, Street/locality, Landmark. Email is shown read-only with the link "Change email in Account Settings". | MUST |
| FR-07-21 | Saving validates like the booking form (Phase 4) and shows "Profile updated." Mobile and names also update the WooCommerce billing fields. | MUST |
| FR-07-22 | The booking form (Phase 4) must pre-fill area, PIN, address lines, contact name and mobile from the profile for logged-in customers. The customer can still change them for a single booking. | MUST |

### 5.6 My Bookings (`/my-account/bookings/`)

| ID | Requirement | Priority |
|---|---|---|
| FR-07-23 | Three tabs: **Upcoming** (Pending, Confirmed, Assigned or In Progress, with date ≥ today; earliest first), **Past** (Completed, or any non-cancelled booking with a past date; newest first), **Cancelled** (newest first). Each tab shows its count, for example "Upcoming (2)". | MUST |
| FR-07-24 | Each row shows: booking number, service name, date + slot, status badge, amount, a **View** button. On mobile, each booking is a card with the same information. | MUST |
| FR-07-25 | Pagination: 10 per page. | SHOULD |
| FR-07-26 | Only bookings with `he_customer_id` = current user are listed (BR-A-04). | MUST |

### 5.7 Booking Details (`/my-account/view-booking/<id>/`)

| ID | Requirement | Priority |
|---|---|---|
| FR-07-27 | Opening a booking that doesn't belong to the current user, or doesn't exist, shows "Booking not found." with a link back to My Bookings. It must not show any data, and must not reveal whether the ID exists (BR-A-04). | MUST |
| FR-07-28 | **Header:** booking number, status badge, "Booked on {date}". | MUST |
| FR-07-29 | **Status timeline:** the 5 normal steps (Pending → Confirmed → Assigned → In Progress → Completed), with completed steps ticked and dated from the history. A cancelled booking shows the steps reached, then "Cancelled on {date}". Internal notes and staff names are **never** shown (shown as "HomeEase team"). | MUST |
| FR-07-30 | **Service & schedule:** service name (link), category, price type explanation, date, slot (arrival window). | MUST |
| FR-07-31 | **Address & contact:** the full service address, contact name and mobile, and the customer's instructions. | MUST |
| FR-07-32 | **Technician:** from Assigned onwards, show the technician's first name and photo (if any) with "Your technician will call before arriving. Need to reach them? Call us on [he_phone]." The technician's own phone number is **not** shown. | MUST |
| FR-07-33 | **Payment:** amount paid, member benefit applied (if any), coupon (if any), order number (link to the order view), payment status. For cancelled paid bookings: refund status "Refund in progress" / "Refunded". | MUST |
| FR-07-34 | **Cancel booking:** shown only when `he_customer_can_cancel()` is true (Phase 4). Clicking it opens an **inline confirmation panel** (not a browser `confirm()` popup) with a reason dropdown (Change of plans · Booked by mistake · Found another provider · Other, plus text) and the policy line "You'll get a full refund to your original payment method within 5–7 working days." Buttons "Yes, cancel booking" / "Keep booking". Confirming calls `he_cancel_booking( id, 'customer', reason )`, reloads the page with the notice "Your booking has been cancelled." and shows the Cancelled status. | MUST |
| FR-07-35 | When cancellation isn't allowed because of the cutoff, show: "This booking starts in less than 12 hours and can't be cancelled online. Please call [he_phone]." For In Progress, Completed or Cancelled bookings, show no cancel option. | MUST |
| FR-07-36 | **Book again** button (for Completed bookings) → the service page. **Need help?** box with phone and WhatsApp (pre-filled message "Hi, I need help with booking {number}"). | SHOULD |

### 5.8 My Maintenance Packages (`/my-account/packages/`)

| ID | Requirement | Priority |
|---|---|---|
| FR-07-37 | **Current plan card:** tier, period, status badge, membership number, start and expiry dates, days left with a progress bar (days used / validity), free visit charges left, discount %, priority and same-day flags, support level, amount paid and order link. | MUST |
| FR-07-38 | **Renew:** shown in the renewal window (BR-M-10) and after expiry. Button "Renew {Tier} {Period}" (same package to checkout) + link "Choose a different plan" (`/pricing/`). Outside the window: "Renewal opens on {expiry − 7 days}." | MUST |
| FR-07-39 | **Request cancellation:** shown for Active plans only. It opens an inline panel with a summary of the refund rules (BR-M-12, link to the full policy), a required reason (dropdown + text) and "Submit request" / "Keep my plan". Submitting calls `he_request_membership_cancellation()` (Phase 5), shows "Your cancellation request has been received. We'll respond within 2 working days. Your plan benefits are paused meanwhile." and the status badge "Cancellation Requested". | MUST |
| FR-07-40 | **Upcoming plan card** (if any): plan, starts on, valid until. | MUST |
| FR-07-41 | **Plan history table:** every membership (newest first) with number, plan, validity (from–to), status, amount, order link. | MUST |
| FR-07-42 | **No plan:** an empty state with the 3 compact package cards (`[he_packages layout="compact"]`). | MUST |
| FR-07-43 | **Benefit usage list:** the bookings that used a visit-charge waiver from this plan (booking number, date, service). | COULD |

### 5.9 Payment History (`/my-account/orders/`)

| ID | Requirement | Priority |
|---|---|---|
| FR-07-44 | The WooCommerce Orders list, titled **Payment History**, with columns: Order · Date · Status · Total · Actions (View; **Pay** and **Cancel** for unpaid orders, which are WooCommerce defaults). | MUST |
| FR-07-45 | The order view shows the items with booking meta, coupon, total, payment method, **payment reference** (Phase 6), refunds, and links "View booking HE-B-…" or "View plan HE-M-…" for each related record. | MUST |
| FR-07-46 | Status names shown to customers: Pending payment → "Awaiting payment", Processing → "Paid", Completed → "Completed", Failed → "Payment failed", Cancelled → "Cancelled", Refunded → "Refunded". | SHOULD |

### 5.10 Account Settings (`/my-account/edit-account/`)

| ID | Requirement | Priority |
|---|---|---|
| FR-07-47 | WooCommerce account details: first name, last name, display name, email, and the password change block (current password, new password, confirm). | MUST |
| FR-07-48 | Changing the email requires the current password (WooCommerce rule for password changes; for email changes, HomeEase also requires the current password). The new email must be unused. | SHOULD |
| FR-07-49 | A **Privacy** section with two buttons: "Request a copy of my data" and "Request deletion of my account". Each creates a WordPress privacy request (export / erase). WordPress sends a confirmation email, and the screen says "Check your email to confirm your request. Our team will complete it within 30 days." (BR-A-06) | MUST |
| FR-07-50 | The data export must include HomeEase bookings and memberships (personal data exporters), in addition to the WordPress and WooCommerce data. | SHOULD |

## 6. Page-wise requirements

| Customer page | URL | Main content |
|---|---|---|
| C1 Registration | `/my-account/` (logged out) | Register column (5.1) |
| C2 Login | `/my-account/` (logged out) | Login column (5.2) |
| C3 Forgot Password | `/my-account/lost-password/` | Request form → email → reset form |
| C4 Customer Dashboard | `/my-account/` | 5.4 |
| C5 My Profile | `/my-account/profile/` | 5.5 |
| C6 My Bookings | `/my-account/bookings/` | 5.6 |
| C7 Booking Details | `/my-account/view-booking/<id>/` | 5.7 |
| C8 My Maintenance Packages | `/my-account/packages/` | 5.8 |
| C9 Payment History | `/my-account/orders/` (+ `/view-order/<id>/`) | 5.9 |
| C10 Account Settings | `/my-account/edit-account/` | 5.10 |

## 7. Required page sections

### 7.1 Login / Register page (logged out)

| # | Section | Content |
|---|---|---|
| 1 | Page heading | H1 "My Account" + the line "Log in to manage your bookings and plans" |
| 2 | Login card | Email/username, password (show/hide toggle), remember me, Log in button, Lost password link |
| 3 | Register card | 5.1 fields + "Create account" |
| 4 | Trust strip | "🔒 Your details are safe with us" + Privacy Policy link |

### 7.2 Dashboard (logged in)

Menu | Greeting → (pending-payment alert) → My plan card → Upcoming bookings → Quick actions.

### 7.3 Booking Details

Header → Status timeline → Service & schedule → Address & contact → Technician → Payment → Actions (Cancel / Book again) → Need help?

### 7.4 My Packages

Current plan card (with Renew / Request cancellation) → Upcoming plan card → Plan history → (No plan: compact package cards).

## 8. UI components

| Component | Specification |
|---|---|
| Account menu item | Icon + label. Active: primary left border (desktop) / primary underline (mobile tabs). |
| Summary card | White, 12 px radius, border, 24 px padding, heading + content + action row |
| Status timeline | Vertical on mobile, horizontal stepper on desktop. Done steps: success colour tick + date. Current: primary ring. Future: grey. |
| Progress bar | 8 px tall, light background, primary fill; label "{n} days left" |
| Tabs (My Bookings) | Underlined tabs with counts. Keyboard accessible (`role="tablist"`). |
| Inline confirmation panel | Expands below the action button. Light danger background for cancellation. Two buttons (danger primary, secondary). Focus moves into the panel when it opens. |
| Empty state | Icon + one line + primary action button |
| Password field | Show/hide eye toggle with `aria-label` |
| Status badges | Phase 2 §8.7 |

## 9. Customer interactions

| # | Interaction | Expected behaviour |
|---|---|---|
| CI-1 | New visitor registers with a 9-digit mobile | Error on mobile. Other values kept. |
| CI-2 | New visitor registers correctly | Logged in, Dashboard, welcome notice, N1 email |
| CI-3 | Customer forgets password | Requests reset → email → sets new password → logs in |
| CI-4 | Customer opens My Bookings | Upcoming / Past / Cancelled tabs with their bookings only |
| CI-5 | Customer opens a booking assigned to a technician | Timeline up to Assigned, technician first name, no technician phone |
| CI-6 | Customer cancels a booking 2 days ahead | Inline panel → confirm → Cancelled, refund message, N10 emails |
| CI-7 | Customer tries to cancel a booking 6 hours ahead | No cancel button; the "call us" message instead |
| CI-8 | Customer changes the booking ID in the URL to another customer's booking | "Booking not found." |
| CI-9 | Customer opens My Packages | Plan validity, days left, waivers left, history |
| CI-10 | Customer requests plan cancellation | Status Cancellation Requested, N15 email, benefits paused |
| CI-11 | Customer with an unpaid order opens the Dashboard | Pending payment alert with Pay now |
| CI-12 | Customer updates their default address in My Profile, then opens a service page | The booking form is pre-filled with the new address |
| CI-13 | Customer requests data deletion | Confirmation email from WordPress, request visible to admin in Tools → Erase Personal Data |

## 10. Administrator interactions

| # | Interaction | Expected behaviour |
|---|---|---|
| AI-1 | Admin opens Tools → Export / Erase Personal Data | Sees confirmed customer requests and can process them. The export includes HomeEase bookings and memberships. |
| AI-2 | Staff edit a customer's profile in Users | Changes (name, phone) show on the customer's My Profile |
| AI-3 | Staff process a cancellation request submitted by the customer | Uses the Phase 5 admin screen |

## 11. Required data fields

### 11.1 Customer (WordPress user + meta)

| Field | Storage key | Source |
|---|---|---|
| First name / Last name | `first_name`, `last_name` + `billing_first_name`, `billing_last_name` | Registration, profile |
| Email | `user_email` (+ `billing_email`) | Registration, account settings |
| Mobile | `billing_phone` | Registration, profile |
| Default area | `he_default_area` (term ID) | Profile |
| Default PIN code | `he_default_pincode` | Profile |
| Default address line 1 / 2 / landmark | `he_default_line1`, `he_default_line2`, `he_default_landmark` | Profile |
| Terms accepted at | `he_terms_accepted_at` (datetime) | Registration |

### 11.2 Endpoints (registered by HomeEase Core)

| Endpoint slug | Menu label | Template |
|---|---|---|
| `bookings` | My Bookings | `templates/account/bookings.php` |
| `view-booking` | (none, a detail page) | `templates/account/view-booking.php` |
| `packages` | My Packages | `templates/account/packages.php` |
| `profile` | My Profile | `templates/account/profile.php` |
| `orders` (WooCommerce) | Payment History | WooCommerce default (styled) |
| `edit-account` (WooCommerce) | Account Settings | WooCommerce default + privacy section |

## 12. WordPress implementation expectations

1. **Registration fields:** `woocommerce_register_form_start` / `woocommerce_register_form` (output) → `woocommerce_register_post` (validate; add to `$errors`) → `woocommerce_created_customer` (save). Use the same validation helper as the profile form.
2. **Endpoints:** `add_rewrite_endpoint( 'bookings', EP_ROOT | EP_PAGES )` etc. on `init`. Add them to the menu with the `woocommerce_account_menu_items` filter (also rename and reorder items, and unset `downloads` and `edit-address`). Render with `woocommerce_account_{endpoint}_endpoint` actions. Register the query vars with `woocommerce_get_query_vars` so WooCommerce recognises them. **Flush permalinks** after adding endpoints (on plugin activation, and Settings → Permalinks → Save).
3. **Dashboard content:** use the `woocommerce_account_dashboard` action to output the HomeEase dashboard. Hide the default intro paragraph with a template override `myaccount/dashboard.php` in the child theme (copy from WooCommerce `templates/myaccount/dashboard.php`, keep only the action call), or with CSS if simpler. Note the choice in `CHANGELOG.txt`.
4. **Ownership check helper:** `he_get_customer_booking( $booking_id )` returns the post only if it exists, is `he_booking` and `he_customer_id == get_current_user_id()`. Otherwise null, and the template shows "Booking not found." Use the same pattern for memberships.
5. **Forms in the account area** (cancel booking, request cancellation, profile): POST to the same page with a nonce (`wp_nonce_field` / `check_admin_referer` with a unique action including the ID), handled on `template_redirect` before output. Then redirect (POST → redirect → GET) to avoid double submission on refresh.
6. **Order status labels for customers:** `wc_order_statuses` filter, but only on the front end (`! is_admin()`), so admin labels stay standard.
7. **Privacy:** `wp_create_user_request( $email, 'export_personal_data' | 'remove_personal_data' )` + `wp_send_user_request( $request_id )`. Register exporters with the `wp_privacy_personal_data_exporters` filter (bookings and memberships for the email's user). An eraser (anonymise address and phone on bookings older than the retention period) is COULD.
8. **Password strength:** WooCommerce's strength meter applies on the account and checkout pages. If "strong" is too strict for customers, lower the minimum to medium with the `woocommerce_min_password_strength` filter (returns 2).
9. **Fresher notes:**
   - Test every account page logged in as customer1 **and** customer2 in two different browsers, to catch data leaks.
   - Don't use `$_GET['id']` directly in a query without `absint()` and the ownership helper.
   - After editing endpoints, if you see a 404, flush permalinks.

## 13. Free plugins or custom development requirements

| Need | Solution |
|---|---|
| Accounts, login, lost password, orders list, account details | WooCommerce My Account (free) |
| Privacy requests, export/erase tools | WordPress core (free) |
| Extra registration fields, custom tabs, dashboard, cancel and renew actions, ownership checks, exporters | **Custom**: HomeEase Core `account.php` + `templates/account/*` |
| Bot protection on forms | Wordfence (Phase 9) / honeypot (custom) |

## 14. Dependencies

| Needs | From |
|---|---|
| WooCommerce account settings | Phase 1 |
| Header Login / My Account link | Phase 2 |
| Booking data, `he_customer_can_cancel`, `he_cancel_booking` | Phase 4 |
| Membership data and functions | Phase 5 |
| Retry payment, payment reference | Phase 6 |
| Technician first name and photo | Phase 8 (show "Technician details will appear here" until then) |

## 15. Expected deliverables

1. Registration with extra fields and validation (account page + checkout).
2. Login, lost password and reset password flows styled and working.
3. Account menu with the final labels and order.
4. Dashboard, My Profile, My Bookings, Booking Details (with cancel), My Packages (with renew and request cancellation), Payment History, Account Settings (with privacy requests).
5. Booking form pre-fill.
6. Ownership checks on every custom page.
7. Personal data exporters.

## 16. Acceptance criteria

| ID | Criterion |
|---|---|
| AC-07-01 | A new customer can register on mobile in under 1 minute. Every invalid input shows a clear message without losing the other values. |
| AC-07-02 | Login with a wrong password shows the generic message. Login with the correct details lands on the Dashboard (or back on the checkout when coming from there). |
| AC-07-03 | The password reset email arrives (Mailpit locally, inbox on staging), the link works once, and the new password works. |
| AC-07-04 | The account menu shows exactly: Dashboard, My Bookings, My Packages, Payment History, My Profile, Account Settings, Log out. |
| AC-07-05 | My Bookings shows correct counts and contents in each tab for a customer with at least 1 upcoming, 1 completed and 1 cancelled booking. |
| AC-07-06 | Booking Details shows the timeline, address, technician first name (when assigned) and payment, and never shows internal notes, staff names or the technician's phone. |
| AC-07-07 | Online cancellation works only inside the allowed rule (BR-B-12), and produces Cancelled status, a refund-required flag and emails. |
| AC-07-08 | Changing the ID in `/view-booking/<id>/` to another customer's booking shows "Booking not found." (tested with customer1 and customer2). The same applies to plans and orders (WooCommerce already protects orders; verify). |
| AC-07-09 | My Packages shows validity, days left and waivers left matching the admin record. Renew appears only in the renewal window or after expiry. |
| AC-07-10 | A cancellation request from My Packages sets Cancellation Requested and pauses benefits (a member price is no longer applied in the cart). |
| AC-07-11 | Customers can't open `/wp-admin/` and don't see the admin bar. |
| AC-07-12 | A data export request, once confirmed and processed by the admin, produces a file that includes the customer's bookings and memberships. |
| AC-07-13 | All account pages work at 360 px without horizontal scrolling (wide tables become cards or scroll inside their own box). |

## 17. Testing scenarios

| ID | Scenario | Steps | Expected result |
|---|---|---|---|
| TC-07-01 | Registration validation | Register with an existing email, then a bad mobile, then a weak password | Correct message each time |
| TC-07-02 | Registration success | Register customer5 | Dashboard, welcome notice, N1 |
| TC-07-03 | Forgot password | Request a reset for customer1 → open the email → set a new password → log in | Works. The old link no longer works after use. |
| TC-07-04 | Customer views booking history (Scenario 11) | customer1 (with bookings from Phases 4–6) → My Bookings → each tab → View | Correct lists and details |
| TC-07-05 | Customer views package validity (Scenario 12) | customer2 → Dashboard plan card → My Packages | Dates, days left, waivers match admin |
| TC-07-06 | Customer requests package cancellation (Scenario 13) | customer2 → My Packages → Request cancellation → reason → submit | Status Cancellation Requested, N15 (customer + admin), member price no longer applied in the cart |
| TC-07-07 | Booking cancel allowed | customer1 cancels a booking 2 days ahead | Cancelled, refund message, N10 |
| TC-07-08 | Booking cancel blocked | Make a test booking for tomorrow at 08:00 and open it at 21:00 today (or set the cutoff to 48 h for the test) | No cancel button, call-us message |
| TC-07-09 | Data privacy | As customer2, open customer1's booking URL, plan URL and `view-order` URL | "Not found" / WooCommerce "Invalid order" for all |
| TC-07-10 | Pay pending order from Dashboard | Create an unpaid order → Dashboard → Pay now | Razorpay opens → success → booking created |
| TC-07-11 | Profile pre-fill | Update the default address → open a service page | Form pre-filled |
| TC-07-12 | wp-admin blocked | As customer1, visit `/wp-admin/` | Redirected to My Account |
| TC-07-13 | Mobile account area (Scenario 18, part 2) | At 390 px, open every account tab | Usable menu, cards instead of wide tables, no horizontal scroll |
| TC-07-14 | Privacy export | customer1 → Request a copy of my data → confirm the email → admin processes it | The export file includes bookings and memberships |

## 18. Known limitations

- Login is email/username + password only. OTP and social login need paid SMS or extra services and aren't included.
- Customers can't reschedule online (BR-B-14). The Booking Details page shows "Need to change the time? Call us."
- Account deletion is a **request**, processed by staff with the WordPress privacy tools. It isn't instant, because order records may need to be kept for accounting (BR-A-06).
- The technician's contact number isn't shown to customers, by design (privacy and business control). Customers call the office.
