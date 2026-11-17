# Phase 4 – Booking and Scheduling System

| Item | Detail |
|---|---|
| Phase | 4 of 10 |
| Estimated effort | 12 working days (fresher). **This is the hardest phase.** Build it in the order of section 12.1. |
| Depends on | [Phase 1](PHASE_01_WORDPRESS_SETUP.md) (checkout + Razorpay test), [Phase 3](PHASE_03_SERVICES_MANAGEMENT.md) (services, areas, allowed slots, category capacity) |
| Unlocks | Phase 5 (member benefits on bookings), Phase 7 (My Bookings), Phase 8 (technician assignment) |
| Business rules used | BR-B-01 to BR-B-16, BR-P-02, BR-P-03 |

Back to: [MASTER.md](../MASTER.md) · Previous: [Phase 3](PHASE_03_SERVICES_MANAGEMENT.md) · Next: [Phase 5](PHASE_05_MAINTENANCE_PACKAGES.md)

---

## 1. Phase title

Booking and Scheduling System: customers choose a service area, PIN code, date and 2-hour time slot, enter their address and instructions, and pay. The system creates a booking with a status that staff manage from a Bookings screen in the admin.

## 2. Phase objective

- A customer can go from a service page to a paid booking in **under 5 minutes on mobile**, without calling.
- The system never offers a date or slot the business can't serve.
- Staff can see every booking in one list and move it through the status workflow, with emails sent to the customer at each step.

## 3. Business context

Today, bookings arrive by phone and are tracked on paper. Double bookings and forgotten jobs damage trust. The booking system:

1. captures complete, correct job details up front (address, PIN code, instructions),
2. limits bookings to the team's real capacity per category and slot,
3. gives the customer and staff a shared, visible status for every job.

Because there's no free booking plugin with Razorpay, coupons and a customer panel (see [FREE_TECH_STACK.md](../00-Project-Planning/FREE_TECH_STACK.md) §3.7), the booking module is custom code on top of WooCommerce. **WooCommerce handles the money; HomeEase Core handles the time.**

## 4. Scope of work

**In scope**

- Booking settings (slots, working days, holidays, window, lead times, cancellation cutoff)
- Booking form on service pages
- Date strip and AJAX slot availability
- Area and PIN code validation
- Address and instruction capture
- Cart and checkout display
- Re-validation at checkout
- Booking record creation after payment
- Slot conflict flag
- Booking statuses and allowed transitions
- Admin Bookings list and edit screen
- Order ↔ booking sync
- Booking emails (N5, N6, N9, N10, N11)
- Thank-you page booking summary
- Core functions for customer cancellation (the button UI comes in Phase 7)

**Out of scope**

- Member price, waivers, priority and same-day logic (Phase 5, which plugs into the filters defined here)
- Coupons and payment failure UI polish (Phase 6)
- My Bookings and Booking Details pages (Phase 7)
- Technician assignment and the schedule view (Phase 8)
- Manual phone bookings (Phase 8)

## 5. Detailed functional requirements

### 5.1 Booking settings (HomeEase → Settings → "Booking" section, Administrator only)

| ID | Requirement | Priority |
|---|---|---|
| FR-04-01 | The admin must be able to manage up to **5 time slots** (IDs `s1`–`s5`), each with a start time, end time and Active switch. Defaults: BR-B-02. A slot's end time must be later than its start time. | MUST |
| FR-04-02 | The admin must be able to tick the **working days** (default Mon–Sat). | MUST |
| FR-04-03 | The admin must be able to enter **holidays** (one date per line, format DD/MM/YYYY, with an optional label, for example `26/01/2027 Republic Day`). Invalid lines show an error and aren't saved. | MUST |
| FR-04-04 | The admin must be able to set: booking window (default **30** days), same-day minimum lead time (default **3** hours, used for Premium in Phase 5), customer cancellation cutoff (default **12** hours), default slot capacity (default **2**, used when a category has none), and service city name (default "*Sample City*"). | MUST |

### 5.2 Booking form (service detail page, section "Book this service", anchor `#book`)

| ID | Requirement | Priority |
|---|---|---|
| FR-04-05 | The form must appear on every published service with Booking enabled = Yes, at least one active area and at least one allowed active slot. Otherwise the unavailable notice from Phase 3 is shown. | MUST |
| FR-04-06 | **Step 1 – Service area:** a dropdown of the active areas assigned to this service, sorted by name. Required. | MUST |
| FR-04-07 | **Step 1 – PIN code:** a 6-digit numeric field (`inputmode="numeric"`, `maxlength="6"`). When the customer leaves the field, it is checked against the selected area's PIN codes. If it doesn't match: "Sorry, we don't serve this PIN code in {area} yet. Please check the area or call us." The rest of the form stays disabled until the PIN code is valid (BR-B-07). | MUST |
| FR-04-08 | **Step 2 – Date:** a horizontally scrollable **date strip** of chips covering the booking window (BR-B-05), each showing the weekday and date ("Tue 18 Nov"). Non-working days and holidays are shown greyed out with a tooltip ("Closed – Sunday", "Holiday – Diwali") and can't be selected. **Today** is shown only when same-day booking is allowed for this customer (Phase 5 filter `he_allow_same_day`, default false). A "Next 7 days ›" arrow scrolls the strip on desktop. | MUST |
| FR-04-09 | **Step 3 – Time slot:** after a date is picked, the slot list loads by AJAX within 2 seconds, with a loading spinner. Each slot shows its label ("10:00 AM – 12:00 PM") and one state: **Available**, **Only 1 left** (remaining = 1), or **Full** (disabled). Slots not allowed for this service aren't shown. On today's date, slots that start within the lead time are shown as "Too soon" (disabled). If no slot is available: "All slots on this date are full. Please pick another date." | MUST |
| FR-04-10 | **Step 4 – Address and contact:** House/Flat no. & building (required, max 100), Street / locality (required, max 100), Landmark (optional, max 100), City (read-only, from settings), Contact name (required, pre-filled from the account if logged in), Contact mobile (required, 10 digits starting 6–9, pre-filled), Instructions for the technician (optional, max 500 characters, with a live character counter). Placeholder for instructions: "e.g. Kitchen sink leaking under the cabinet; parking available in basement". | MUST |
| FR-04-11 | **Price summary:** below the fields, show "Service price: ₹X" (and, from Phase 5, the member price line), plus the note "Final amount, including any coupon, is shown at checkout." | MUST |
| FR-04-12 | **Submit button:** "Add booking & continue". Disabled until all required fields are valid. On click, the data is validated again **on the server** (FR-04-14). On success, the customer goes to the **Cart**. | MUST |
| FR-04-13 | Logged-out customers can fill in the form and reach the cart. Login or registration is required at checkout (BR-A-01). The booking details must survive the login step. | MUST |
| FR-04-14 | **Server-side validation** when the booking is added to the cart: the area is active and assigned to the service, the PIN code belongs to the area, the date is inside the window and is a working, non-holiday day, the slot is active and allowed for the service, the slot still has capacity, and all required fields are present and valid. Every failure shows a WooCommerce error notice naming the problem, and the item isn't added. | MUST |
| FR-04-15 | Each "Add booking" creates a **separate cart line**, even for the same service (for example two bathroom cleanings on different dates). Quantity is always 1 and isn't editable in the cart. | MUST |
| FR-04-16 | Adding the **same service, date and slot** twice to one cart must be blocked with "This booking is already in your cart." | SHOULD |

### 5.3 Cart and checkout

| ID | Requirement | Priority |
|---|---|---|
| FR-04-17 | In the cart, the checkout review and order emails, each booking line must show: Service date (DD/MM/YYYY, weekday), Time slot, Area, PIN code, Address (one line) and Instructions (if any). | MUST |
| FR-04-18 | The cart must have an **Edit** link per booking line. It removes the line and returns the customer to the service page, with the previous values pre-filled where possible. COULD: pre-fill. MUST: remove + return. | SHOULD |
| FR-04-19 | At checkout (on "Place order"), every booking line must be **re-validated** (date not past, not a holiday, slot active, capacity available). If a line fails, the order isn't placed and the customer sees: "Sorry, the {slot} slot on {date} for {service} has just been booked by someone else. Please remove it and choose another slot." (BR-B-09) | MUST |
| FR-04-20 | The checkout billing form must ask only for the fields needed: first name, last name, mobile, email. Remove company, country (India fixed), address 2 and state where WooCommerce allows; the service address is already on each booking. The billing address fields may stay if Razorpay requires them; check during testing and record the result. | SHOULD |
| FR-04-21 | The checkout page must show the text: "By placing this order you agree to our Terms and Conditions and Cancellation and Refund Policy", with links, plus the WooCommerce terms checkbox. | MUST |

### 5.4 Booking creation

| ID | Requirement | Priority |
|---|---|---|
| FR-04-22 | When an order containing service lines is **paid** (status Processing), or has a ₹0 total, the system must create **one booking per service line** with status **Pending**, and link it to the order and the line item (BR-B-08). | MUST |
| FR-04-23 | Booking creation must be **idempotent**: refreshing the thank-you page, a Razorpay webhook and the status hook firing together must never create duplicates. | MUST |
| FR-04-24 | Each booking gets a sequential number `HE-B-000001`, `HE-B-000002`, …, which never repeats, even if a booking is deleted. | MUST |
| FR-04-25 | The booking stores a **snapshot** of the service name, category, slot label, area name and amount at the time of booking. Later edits to the service or settings don't change past bookings. | MUST |
| FR-04-26 | If, at creation time, the slot is already at or over capacity (a race between two payments), the booking is still created as Pending but marked **Slot conflict = Yes**. An urgent admin email (N11) is sent, and the booking shows a red "Slot conflict" label in admin (BR-B-09). | MUST |
| FR-04-27 | Unpaid, failed or cancelled-before-payment orders must **not** create bookings or use capacity. | MUST |

### 5.5 Status management

| ID | Requirement | Priority |
|---|---|---|
| FR-04-28 | Booking statuses and transitions must follow BR-B-10 exactly. The status dropdown on the admin edit screen shows **only the allowed next statuses** for the current status. | MUST |
| FR-04-29 | Every status change records an entry in the booking's **History**: old status, new status, date and time, staff user (or "Customer" / "System"), and an optional note. | MUST |
| FR-04-30 | When staff change the status, a "Notify customer by email" checkbox (ticked by default) controls whether the status email is sent. Pending → Confirmed sends N6, In Progress → Completed sends N9, → Cancelled sends N10. | MUST |
| FR-04-31 | The **Assigned** status can't be chosen from the dropdown. It is set only by assigning a technician (Phase 8, BR-B-11). Until Phase 8, the status flow can be tested as Pending → Confirmed → Cancelled / Completed with a temporary "skip" (see section 12). | MUST |
| FR-04-32 | When **every** booking in an order is Completed, the order is set to **Completed** automatically (BR-P-02). | MUST |
| FR-04-33 | When an order is set to **Cancelled** or **Refunded** (full) by staff, all its non-completed bookings become **Cancelled** (cancelled by "System"), with history entries. | MUST |
| FR-04-34 | When a paid booking is cancelled, its **Refund status** becomes "Refund required", and the admin email N10 says "Refund required: ₹X – open order #N". Staff mark "Refund done" after refunding in the order (Phase 6). A full order refund sets "Refund done" automatically. | MUST |

### 5.6 Customer cancellation (logic only; UI in Phase 7)

| ID | Requirement | Priority |
|---|---|---|
| FR-04-35 | A function `he_customer_can_cancel( $booking_id )` must return true only if: the booking belongs to the current user, its status is Pending, Confirmed or Assigned, and the slot start time is **more than 12 hours** away (setting from FR-04-04) (BR-B-12). | MUST |
| FR-04-36 | A function `he_cancel_booking( $booking_id, $by, $reason )` must set Cancelled, record history, set the refund status, return any waiver (filter for Phase 5), and send N10 to the customer, admin and technician (if assigned). | MUST |

### 5.7 Confirmation and notifications

| ID | Requirement | Priority |
|---|---|---|
| FR-04-37 | The WooCommerce **thank-you page** must show a box "Your bookings" listing each booking number, service, date, slot and the status badge "Pending", followed by "What happens next: we'll confirm your booking within 6 working hours (2 hours for priority members). You'll get an email at each step." and a button "View my bookings" (link active from Phase 7). | MUST |
| FR-04-38 | Emails N5 (customer + admin), N6, N9, N10 and N11 must be sent as defined in [PROJECT_ARCHITECTURE.md](../00-Project-Planning/PROJECT_ARCHITECTURE.md) §8, using the texts in section 11.4. | MUST |

### 5.8 Admin bookings list

| ID | Requirement | Priority |
|---|---|---|
| FR-04-39 | **HomeEase → Bookings** must list bookings with columns: Booking no. · Customer (name + mobile) · Service · Date · Slot · Area · Status (badge) · Technician (Phase 8) · Flags (Priority / Slot conflict) · Amount · Order. | MUST |
| FR-04-40 | The default view is **upcoming bookings** (today and later, not Cancelled or Completed), sorted by date then slot, earliest first. | MUST |
| FR-04-41 | Filters: Status (all 6), Date (Today, Tomorrow, This week, a specific date), Category, Area. Filters can be combined. | MUST |
| FR-04-42 | Search works for booking number, customer name, mobile number and email. | MUST |
| FR-04-43 | Status count links appear at the top: "Pending (4) · Confirmed (6) · Assigned (3) · In Progress (1) · Completed · Cancelled". | SHOULD |
| FR-04-44 | Staff can't create a booking from "Add New" on this screen (the button is hidden). Manual bookings come in Phase 8. Staff can't delete bookings. Only the Administrator can move a booking to Trash. | MUST |
| FR-04-45 | Bookings can be exported as CSV for the current filter (columns as in FR-04-39). | COULD |

### 5.9 Admin booking edit screen

| ID | Requirement | Priority |
|---|---|---|
| FR-04-46 | **Summary box (read-only):** booking number, created date, customer (link to user profile), service (link), category, price type, amount paid, order number (link) and order payment status. | MUST |
| FR-04-47 | **Status box:** current status badge, a "Change to" dropdown (allowed transitions only), a note field, the "Notify customer" checkbox and an **Update** button. | MUST |
| FR-04-48 | **Schedule box:** date and slot. Staff can **reschedule** to another date and slot. The system checks capacity and shows "This slot is full (2/2). Tick 'Override capacity' to save anyway." Saving a change records history and sends email N8 (Phase 8 adds the technician copy) (BR-B-14). | MUST |
| FR-04-49 | **Address & contact box:** all address fields and the contact mobile are editable by staff. Changes are recorded in history ("Address updated"). | MUST |
| FR-04-50 | **History box:** a timeline of all changes, newest first. | MUST |
| FR-04-51 | **Internal notes box:** private notes for staff only (never shown to the customer), for example "Customer prefers a call before arrival". | MUST |
| FR-04-52 | **Refund box:** shown when the refund status is "Refund required", with the amount, a link to the order and a "Mark refund done" button. | MUST |

## 6. Page-wise requirements

| Page | What this phase adds |
|---|---|
| Service details (`/service/<slug>/`) | The booking form (section 7.1) replaces the placeholder |
| Cart (`/cart/`) | Booking details per line, Edit link, no quantity box |
| Checkout (`/checkout/`) | Booking details in the order review, re-validation, terms text |
| Order received (thank-you) | "Your bookings" box |
| Admin: HomeEase → Bookings | List and edit screens |
| Admin: HomeEase → Settings | Booking section |

## 7. Required page sections

### 7.1 Booking form ("Book this service")

| # | Section | Content |
|---|---|---|
| 1 | Heading | H2 "Book {service name}" + a small step indicator: ① Location ② Date ③ Time ④ Address |
| 2 | Location | Area dropdown + PIN code with inline validation message |
| 3 | Date | Date strip |
| 4 | Time | Slot cards (2 per row on mobile, 3–5 per row on desktop) |
| 5 | Address & contact | Address fields, contact fields, instructions with a counter |
| 6 | Summary & submit | Price summary, notes, "Add booking & continue" button, trust line "🔒 Secure payment via Razorpay · Cancel free up to 12 hrs before" |

On desktop, the form sits in the main column with the service summary visible above. On mobile it is full width, and every step is visible as you scroll. There is no multi-page wizard, which keeps it simple.

### 7.2 Thank-you page addition

Order details (WooCommerce default) + the "Your bookings" box (FR-04-37) above the order details.

## 8. UI components

| Component | Specification |
|---|---|
| Date chip | 64 × 72 px, weekday (12 px) above day + month (16 px bold). States: default (white, border), selected (primary background, white text), disabled (grey, strikethrough day, `aria-disabled="true"`). Keyboard: arrow keys move, Enter selects. |
| Slot card | Min 44 px tall, radio input visually hidden but accessible, label "10:00 AM – 12:00 PM", sub-label state ("Available" green / "Only 1 left" amber / "Full" grey / "Too soon" grey) |
| Inline validation message | Under the field, danger colour, starts with an icon. Success state for the PIN code: "✓ We serve {PIN} in {area}". |
| Loading spinner | Shown in the slot area while AJAX runs. Text "Checking availability…" for screen readers (`aria-live="polite"`). |
| Booking line meta (cart) | Small muted text lines under the service name |
| Status badge | From Phase 2 §8.7 |
| Admin flags | Red pill "Slot conflict", amber pill "Priority" |
| Admin status dropdown | Shows only allowed statuses. Disabled when Completed or Cancelled ("Final status"). |

## 9. Customer interactions

| # | Interaction | Expected behaviour |
|---|---|---|
| CI-1 | Customer selects area "Zone A" and types PIN `400001` | ✓ success message. Date strip enabled. |
| CI-2 | Customer types PIN `999999` | Error message. Date and slot steps stay disabled. |
| CI-3 | Customer taps a Sunday chip | Nothing is selected. Tooltip / sub-text shows "Closed". |
| CI-4 | Customer taps tomorrow | Slots load. Full slots are disabled. |
| CI-5 | Customer selects 10:00–12:00, fills the address and taps "Add booking & continue" | Lands on the Cart with the booking details shown |
| CI-6 | Customer (logged out) proceeds to checkout | Asked to log in or register. After login, the cart still has the booking with all details. |
| CI-7 | Customer pays with a Razorpay test method | Thank-you page with "Your bookings" (HE-B-…, Pending). Email N5 received. |
| CI-8 | Another customer took the last slot while this customer was in checkout | Checkout error (FR-04-19). Order not placed. |
| CI-9 | Customer receives the "Booking confirmed" email after staff confirm | Email N6 with booking number, date, slot and address |

## 10. Administrator interactions

| # | Interaction | Expected behaviour |
|---|---|---|
| AI-1 | Staff open HomeEase → Bookings in the morning | See today's and upcoming bookings sorted by date and slot. Pending ones are clearly visible. |
| AI-2 | Staff filter Status = Pending | Only Pending bookings |
| AI-3 | Staff open a booking and change Pending → Confirmed with "Notify customer" ticked | Status saved, history entry added, customer email N6 sent |
| AI-4 | Staff reschedule a booking to a full slot without override | Error "This slot is full"; the change isn't saved |
| AI-5 | Staff cancel a paid booking with the note "Customer called to cancel" | Status Cancelled, refund status "Refund required", N10 sent, refund box shown |
| AI-6 | Staff fully refund the order in WooCommerce | Bookings Cancelled (if not already) and refund status "Refund done" |
| AI-7 | Staff try to set a Completed booking back to In Progress | Not possible: the dropdown is disabled ("Final status") |
| AI-8 | Admin adds the holiday `26/01/2027 Republic Day` | That date is greyed out in the date strip |
| AI-9 | Admin deactivates slot `s5` | The 16:00–18:00 slot disappears for new bookings. Existing bookings keep their slot label. |

## 11. Required data fields

### 11.1 Settings (option `he_settings`, booking keys)

| Key | Type | Default |
|---|---|---|
| `slots` | Array of 5: `{id, start "H:i", end "H:i", active}` | BR-B-02 |
| `working_days` | Array of weekday numbers (1 = Mon … 7 = Sun) | `[1,2,3,4,5,6]` |
| `holidays` | Array of `{date "Y-m-d", label}` | `[]` |
| `booking_window_days` | Integer 1–90 | 30 |
| `same_day_lead_hours` | Integer 1–12 | 3 |
| `cancel_cutoff_hours` | Integer 0–72 | 12 |
| `default_capacity` | Integer 1–20 | 2 |
| `city` | Text | *Sample City* |

### 11.2 Booking record (`he_booking` post meta)

| Key | Type | Set by | Notes |
|---|---|---|---|
| `he_booking_number` | Text `HE-B-000123` | System | Also used as the post title prefix |
| `he_status` | `pending` / `confirmed` / `assigned` / `in_progress` / `completed` / `cancelled` | System / staff | |
| `he_customer_id` | User ID | System | Used for the ownership check |
| `he_order_id` | Order ID | System | |
| `he_order_item_id` | Order item ID | System | |
| `he_service_id` | Product ID | System | |
| `he_service_name` | Text | System (snapshot) | |
| `he_category_id` / `he_category_name` | Term ID / text | System (snapshot) | For capacity and display |
| `he_price_type` | `fixed` / `visit` | System (snapshot) | |
| `he_amount` | Decimal | System | Order line total (after member price and coupon) |
| `he_original_price` | Decimal | System | Service price before member price or waiver (used for refund suggestions in Phase 5) |
| `he_date` | `Y-m-d` | Customer / staff | |
| `he_slot_id` / `he_slot_label` | `s2` / "10:00 AM – 12:00 PM" | Customer / staff | Label snapshot |
| `he_area_id` / `he_area_name` | Term ID / text | Customer | |
| `he_pincode` | 6 digits | Customer | |
| `he_address_line1`, `he_address_line2`, `he_landmark`, `he_city` | Text | Customer / staff | |
| `he_contact_name`, `he_contact_phone` | Text | Customer / staff | |
| `he_instructions` | Text ≤ 500 | Customer | |
| `he_slot_conflict` | `yes` / `no` | System | |
| `he_status_history` | Array of `{from, to, by_user_id, by_label, time, note}` | System | |
| `he_cancel_reason`, `he_cancelled_by`, `he_cancelled_at` | Text / `customer`, `staff`, `system` / datetime | System | |
| `he_refund_status` | `not_required` / `required` / `done` | System / staff | |
| `he_admin_notes` | Text | Staff | Private |
| `he_technician_id` | Post ID | Staff (Phase 8) | |
| `he_is_priority`, `he_membership_id`, `he_waiver_used` | yes/no, ID, yes/no | System (Phase 5) | |

### 11.3 Cart item data / order line item meta

| Internal cart key | Order item meta label (visible) | Example |
|---|---|---|
| `he_date` | Service date | Tue, 18/11/2026 |
| `he_slot_id` | Time slot | 10:00 AM – 12:00 PM |
| `he_area_id` | Area | Zone A – City Centre |
| `he_pincode` | PIN code | 400001 |
| `he_address_*` | Service address | Flat 302, Sunrise Apts, MG Road, near City Mall |
| `he_contact_*` | Contact | Riya Shah, 98XXXXXX10 |
| `he_instructions` | Instructions | Kitchen sink leaking |
| – | `_he_booking_id` (hidden) | 1234 |

Order meta: `_he_bookings_created` = `yes` (idempotency flag).

### 11.4 Email texts (all start "Hi {first name}," and end with the support phone and email)

| Email | Subject | Key content |
|---|---|---|
| N5 Customer | "Booking received – {booking no.}" | Service, date, slot, address, amount paid, "We'll confirm within 6 working hours.", link to My Bookings |
| N5 Admin | "[New booking] {booking no.} – {service} – {date} {slot}" (prefix "[PRIORITY]" when flagged) | All booking fields + link to the admin edit screen |
| N6 | "Booking confirmed – {booking no.}" | Date, slot, address, "A technician will be assigned shortly." |
| N9 | "Job completed – {booking no.}" | Thanks, a line on the warranty (if any), "Book again" link, "Save more with a maintenance plan" link |
| N10 Customer | "Booking cancelled – {booking no.}" | Reason (if given by staff), refund line "₹X will be refunded to your original payment method within 5–7 working days" (if paid) |
| N10 Admin | "[Cancelled] {booking no.} – Refund required: ₹X" | Who cancelled, reason, order link |
| N11 Admin | "[URGENT] Slot conflict – {booking no.}" | Booking details + "Reschedule and inform the customer within 2 working hours" |

## 12. WordPress implementation expectations

### 12.1 Build order (do it in this order and test each step before the next)

1. Settings section (slots, days, holidays, numbers) and the helpers `he_get_slots()`, `he_is_bookable_date( $date, $user_id )`, `he_slot_start_timestamp( $date, $slot_id )`.
2. Register `he_booking` (custom post type, not public, `show_ui` true, `show_in_menu` 'homeease', `supports` ['title'], `capability_type` ['he_booking','he_bookings'], `map_meta_cap` true). Grant the capabilities on plugin activation (see Architecture §7).
3. Capacity function `he_get_slot_usage( $category_id, $date, $slot_id )`: counts `he_booking` posts with matching category, date and slot, excluding `cancelled` (meta query). `he_get_slot_capacity( $category_id )` reads the category field or the default.
4. AJAX endpoint `he_get_slots` (hooks `wp_ajax_he_get_slots` + `wp_ajax_nopriv_he_get_slots`). Inputs: product ID, date, nonce. Output: JSON list of `{id, label, state, remaining}`. **Never trust the browser.** The endpoint recalculates everything.
5. Booking form output on `woocommerce_single_product_summary` (or after it) for services. The JS file `he-booking.js` handles the PIN check (area PIN lists printed into the page as JSON; they aren't secret), date strip, slot loading, the character counter and enabling the button.
6. Cart integration:
   - `woocommerce_add_to_cart_validation`: full server validation (FR-04-14).
   - `woocommerce_add_cart_item_data`: store the sanitised booking fields + a unique key so each booking is its own line.
   - `woocommerce_get_item_data`: show the fields in cart and checkout.
   - `woocommerce_cart_item_quantity`: show "1" (no input).
   - `woocommerce_checkout_create_order_line_item`: save the visible labelled meta on the order item.
7. Checkout re-validation: `woocommerce_after_checkout_validation` (adds errors to `$errors`) **and** `woocommerce_check_cart_items` (shows errors on the cart page).
8. Booking creation: a function `he_create_bookings_for_order( $order_id )` hooked to `woocommerce_payment_complete`, `woocommerce_order_status_processing` and `woocommerce_order_status_completed`. It first checks `$order->get_meta( '_he_bookings_created' )`, then loops service items, creates posts with `wp_insert_post`, saves meta, writes `_he_booking_id` on each item, sets the flag and calls `$order->save()`.
9. Status engine: `he_allowed_transitions( $status )` returns an array; `he_set_booking_status( $id, $new, $by, $note, $notify )` validates, saves, logs history, fires `do_action( 'he_booking_status_changed', $id, $old, $new )` and sends emails. **All status changes in every phase must go through this one function.**
10. Order sync: `woocommerce_order_status_cancelled`, `woocommerce_order_status_refunded` → cancel bookings. `he_booking_status_changed` → if all of the order's bookings are completed, `$order->update_status( 'completed' )`.
11. Admin list: `manage_he_booking_posts_columns`, `manage_he_booking_posts_custom_column`, `restrict_manage_posts` (filter dropdowns), `pre_get_posts` (apply filters, default upcoming view, meta sorting), `posts_search` or a `pre_get_posts` meta search for number/phone. Hide "Add New" by registering the post type with `'capabilities' => [ 'create_posts' => 'do_not_allow' ]` (keep `map_meta_cap`).
12. Edit screen: `add_meta_boxes` for the boxes in 5.9, saving on `save_post_he_booking` with a nonce and `current_user_can( 'edit_he_booking', $post_id )`. Remove the default "Publish" box confusion by keeping post status `publish` and using `he_status` for the business status.
13. Emails: `he_send_email( $to, $subject, $heading, $body_html )` wraps content with `WC()->mailer()->wrap_message( $heading, $body_html )` and sends with `WC()->mailer()->send( … )`, so all emails match the WooCommerce template.
14. Thank-you box: `woocommerce_thankyou` action (`$order_id`). If bookings aren't created yet (rare timing), show "Your booking is being confirmed – refresh in a moment."

### 12.2 Extension points for later phases (create them now as filters/actions)

| Hook | Default | Used by |
|---|---|---|
| `apply_filters( 'he_allow_same_day', false, $user_id )` | false | Phase 5 (Premium) |
| `apply_filters( 'he_booking_is_priority', false, $user_id )` | false | Phase 5 |
| `do_action( 'he_booking_created', $booking_id, $order )` | – | Phase 5 (record waiver use), Phase 8 |
| `do_action( 'he_booking_cancelled', $booking_id )` | – | Phase 5 (return waiver), Phase 8 (notify technician) |
| `do_action( 'he_booking_status_changed', $id, $old, $new )` | – | Order sync, Phase 8 |

### 12.3 Temporary testing aid until Phase 8

Allow `confirmed → in_progress` in `he_allowed_transitions()` **only** while the constant `HE_DEV_SKIP_ASSIGN` is true (defined in the local `wp-config.php`). Remove the constant when Phase 8 is complete.

### 12.4 Fresher notes

- Store every date as `Y-m-d` and compare times with timestamps from `he_slot_start_timestamp()`, which uses `wp_timezone()`. Never use PHP `date()` with the server timezone.
- Test the race condition by setting a category's capacity to 1 and paying for the same slot in two browsers (Chrome + Firefox incognito) at the same time.
- Put `error_log()` lines in the booking creation function while developing, and remove them afterwards.
- Classic checkout (Phase 1) is what makes `woocommerce_after_checkout_validation` work. Don't switch to the block checkout.

## 13. Free plugins or custom development requirements

| Need | Solution |
|---|---|
| Cart, checkout, order, payment, refunds | WooCommerce + Razorpay (free) |
| Booking settings, form, slots, capacity, records, statuses, admin screens, emails | **Custom**: HomeEase Core (`settings.php`, `booking-form.php`, `booking-create.php`, `booking-admin.php`, `emails.php`, `he-booking.js`) |
| Email delivery | FluentSMTP (free) |
| JavaScript library | None needed. Plain JavaScript and `fetch()` for AJAX. No paid date-picker library. |

## 14. Dependencies

| Needs | From |
|---|---|
| Classic checkout, Razorpay test mode, guest checkout off | Phase 1 |
| Services with price type, allowed slots, areas, category capacity | Phase 3 |
| Real slot times, working days, holidays, capacity per category | Owner (decisions D1, D2) |
| Same-day / priority / member price | Phase 5 (uses the extension points) |
| Technician assignment and the Assigned status | Phase 8 |

## 15. Expected deliverables

1. Booking settings section.
2. Booking form with date strip and AJAX slots on all bookable services.
3. Cart, checkout and email display of booking details.
4. Checkout re-validation.
5. Booking creation from paid orders (idempotent, numbered, conflict flag).
6. Status engine with history.
7. Admin Bookings list (columns, filters, search, counts) and edit screen (all boxes).
8. Order ↔ booking sync.
9. Emails N5, N6, N9, N10, N11.
10. Thank-you page bookings box.
11. Cancellation functions for Phase 7.
12. `CHANGELOG.txt` entries.

## 16. Acceptance criteria

| ID | Criterion |
|---|---|
| AC-04-01 | A customer can book "Pipe Leakage Repair" on mobile (390 px) from the service page to the thank-you page in under 5 minutes. |
| AC-04-02 | Sundays, holidays, past dates and dates beyond 30 days can't be selected, and a request forced through the browser tools is rejected by the server. |
| AC-04-03 | With a category capacity of 2 and 2 non-cancelled bookings in a slot, that slot shows **Full** for every service in that category. Cancelling one booking makes it **Only 1 left**. |
| AC-04-04 | A PIN code outside the selected area blocks the booking, both in the browser and on the server. |
| AC-04-05 | A paid order with 2 service lines creates exactly **2** bookings (status Pending, numbered sequentially), even after refreshing the thank-you page 5 times. |
| AC-04-06 | A failed or abandoned payment creates **no** booking and doesn't use capacity. |
| AC-04-07 | Booking details (date, slot, area, address) appear in the cart, checkout review, WooCommerce order screen and customer emails. |
| AC-04-08 | The status dropdown offers only the allowed transitions. Completed and Cancelled are final. |
| AC-04-09 | Every status change appears in History with user and time, and sends the right email when "Notify customer" is ticked. |
| AC-04-10 | When all bookings of an order are Completed, the order becomes Completed. A full order refund cancels its bookings. |
| AC-04-11 | Staff (Shop Manager) can view and manage bookings but can't delete them. Customers can't access the admin Bookings screen. |
| AC-04-12 | The slot conflict scenario (capacity 1, two simultaneous payments) gives one normal booking and one booking flagged "Slot conflict", plus an N11 email. |
| AC-04-13 | No PHP errors, warnings or JavaScript console errors during the full booking flow. |

## 17. Testing scenarios

| ID | Scenario | Steps | Expected result |
|---|---|---|---|
| TC-04-01 | Choose date and time (Scenario 3) | Open "Tap / Mixer Repair" → Zone A → PIN 400001 → tomorrow → 10:00–12:00 | Date selected, slots loaded, slot selected, submit enabled after the address |
| TC-04-02 | Submit a booking (Scenario 4) | Complete TC-04-01 → Add booking → Checkout as customer1 → pay with a Razorpay test card | Thank-you page shows HE-B-… Pending. N5 to customer and admin. Order Processing. |
| TC-04-03 | Admin receives and manages the booking (Scenario 5) | As staff1, open Bookings → find the new booking → Confirm with a note | Status Confirmed, history entry, N6 email |
| TC-04-04 | Admin manages a booking (Scenario 17) | Reschedule the booking to the day after, 14:00–16:00 → then Cancel with a reason | Reschedule saved with history (and N8 once Phase 8 adds it). Cancel gives refund status "Refund required" and N10 emails. |
| TC-04-05 | PIN not served | Enter PIN 999999 | Error message, steps disabled |
| TC-04-06 | Holiday | Add tomorrow as a holiday → reload the service page | Tomorrow's chip is disabled |
| TC-04-07 | Capacity full | Set Plumbing capacity 1 → book 10:00–12:00 tomorrow → as customer2, open another Plumbing service | 10:00–12:00 shows Full |
| TC-04-08 | Race condition | Capacity 1. customer1 and customer2 add the same slot, then both pay quickly in two browsers. | Second checkout is blocked (FR-04-19), or if both pass, one booking is flagged Slot conflict + N11 |
| TC-04-09 | Payment failure | Use a Razorpay test failure method or close the popup | No booking. Order Pending payment/Failed. Slot remains available. |
| TC-04-10 | Logged-out booking | Fill in the form logged out → checkout → register a new account | Cart keeps the booking. Order placed under the new account. |
| TC-04-11 | Two services, one order | Add "Bathroom Deep Cleaning" for two different dates → pay | 2 bookings, each with its own date |
| TC-04-12 | Completed sync | Move both bookings from TC-04-11 to Completed (using the dev skip flag) | Order Completed automatically. N9 emails sent. |
| TC-04-13 | Final status lock | Open a Completed booking | Status dropdown disabled |
| TC-04-14 | Server tampering | Use DevTools to enable a Full slot and submit | Server error "This slot is full", item not added |
| TC-04-15 | Mobile booking flow (Scenario 18, part 1) | Complete TC-04-02 on a real Android phone and an iPhone (or the DevTools emulator if no device) | All steps usable, no zoom on input focus, no horizontal scroll |

## 18. Known limitations

- **Capacity is a number per category and slot**, not a live technician calendar. Staff must keep category capacities in line with the number of available technicians (for example, lower the capacity when someone is on leave). Phase 8 shows technician workload to help.
- A slot isn't **held** while the customer pays. In rare cases two customers can pay for the last place, which is handled by the Slot conflict flag and a reschedule (BR-B-09).
- Long jobs (4–6 hours) count as a single slot booking. The "Allowed slots" setting keeps them to morning starts, but they don't block the following slots automatically.
- Customers can't reschedule online (BR-B-14). They cancel within the allowed window and rebook, or call support.
- Arrival windows are a promise to the customer, not something the system tracks. There's no live tracking.
