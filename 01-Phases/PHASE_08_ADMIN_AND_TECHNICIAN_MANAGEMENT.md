# Phase 8 – Admin Dashboard and Technician Management

| Item | Detail |
|---|---|
| Phase | 8 of 10 |
| Estimated effort | 6 working days (fresher) |
| Depends on | [Phase 4](PHASE_04_BOOKING_SYSTEM.md) (bookings, status engine), [Phase 5](PHASE_05_MAINTENANCE_PACKAGES.md) (memberships), [Phase 6](PHASE_06_PAYMENT_AND_COUPONS.md) (orders, refunds, offers), [Phase 7](PHASE_07_CUSTOMER_DASHBOARD.md) (customer profile data) |
| Unlocks | Phase 10 (end-to-end operations testing, owner training) |
| Business rules used | BR-B-10, BR-B-11, BR-B-14, BR-B-16, BR-M-08; roles in [PROJECT_ARCHITECTURE.md §7](../00-Project-Planning/PROJECT_ARCHITECTURE.md) |

Back to: [MASTER.md](../MASTER.md) · Previous: [Phase 7](PHASE_07_CUSTOMER_DASHBOARD.md) · Next: [Phase 9](PHASE_09_SEO_SECURITY_PERFORMANCE.md)

---

## 1. Phase title

Admin Dashboard and Technician Management: a simple operations console for a small team, covering the "HomeEase Today" dashboard, technician profiles, skills, areas and availability, job assignment with workload checks, a daily schedule view, phone bookings, customer management and confirmed role permissions.

## 2. Phase objective

- Each morning, the office manager can open one screen and know what needs attention today: pending confirmations, unassigned jobs, conflicts, refunds and expiring plans.
- They can assign the right technician to each job in a few clicks.
- Technicians get their job details by email or WhatsApp.
- Each staff role can do exactly what it should, and nothing more.

## 3. Business context

HomeEase starts with about 5–15 technicians and a small office team. A complex dispatch system isn't needed, and a technician app would cost money. The business needs:

1. a clear list of each technician's skills, areas and availability,
2. a quick, safe way to assign jobs without double-booking someone,
3. a daily view of who goes where.

Technicians **don't log in** to the website. The office keeps statuses up to date after phone or WhatsApp check-ins.

## 4. Scope of work

**In scope**

- Technician records (`he_technician`)
- Assignment box on bookings, with the Assigned status
- Reassign and unassign
- Technician job emails and WhatsApp share
- Technician job lists and counts
- Bookings list technician filter and bulk actions
- Schedule view
- Reschedule emails (N8)
- Phone (manual) bookings
- "HomeEase Today" dashboard widgets
- Daily admin digest email
- Customer management additions
- Admin menu structure
- Role and capability verification
- Website content management map
- Removing the Phase 4 dev skip flag

**Out of scope**

- Technician login or app
- Payroll and commissions
- GPS tracking
- Inventory and spare parts

## 5. Detailed functional requirements

### 5.1 Technician management

| ID | Requirement | Priority |
|---|---|---|
| FR-08-01 | Staff must be able to **add** a technician (HomeEase → Technicians → Add New) with the fields in section 11.1. | MUST |
| FR-08-02 | Staff must be able to **edit** any technician field, and set Status to **Available**, **On leave** or **Inactive**. Technicians are never deleted while they have bookings. They are set to Inactive instead, and only the Administrator can trash them. | MUST |
| FR-08-03 | **Skills** = one or more service categories (the 7 categories; Maintenance Packages excluded). **Areas** = one or more service areas. | MUST |
| FR-08-04 | **Availability** = working days (Mon–Sun checkboxes) + leave dates (one date or range per line, `DD/MM/YYYY` or `DD/MM/YYYY - DD/MM/YYYY`) + status. A technician is **available on a date** if status = Available, the weekday is ticked, and the date isn't inside a leave line. Invalid leave lines show an error on save. | MUST |
| FR-08-05 | The technicians list must show: Photo · Name · Phone · Skills · Areas · Status · Jobs today · Upcoming jobs · Completed this month. Filters: status, skill, area. | MUST |
| FR-08-06 | The technician edit screen must show a **Jobs** box with: counts (Assigned/In Progress upcoming · Completed this month · Completed total) and two lists: **Upcoming jobs** (date, slot, booking no. link, service, area, status) and the **last 20 completed jobs**. | MUST |
| FR-08-07 | Technician records are **private**: no public URL, not in search, not in sitemaps. | MUST |

### 5.2 Job assignment (booking edit screen, "Technician" box)

| ID | Requirement | Priority |
|---|---|---|
| FR-08-08 | Assignment is possible only when the booking is **Confirmed** (first assignment) or **Assigned** (reassignment). For Pending bookings the box says "Confirm the booking before assigning a technician." | MUST |
| FR-08-09 | The dropdown must list technicians in two groups: **Recommended** (skill matches the booking's category, area matches, and available on the booking date) and **Other active technicians** (status Available but not matching skill, area or day, each labelled with the reason, for example "– not in Zone B", "– on leave"). Inactive technicians aren't listed. | MUST |
| FR-08-10 | Each option shows the technician's load for that date: "Ravi Kumar – 2 jobs on 19/11 (0 in this slot)". | MUST |
| FR-08-11 | If the chosen technician already has a non-cancelled booking in the **same date and slot**, show a warning naming that booking and require a tick on "Assign anyway" before saving. | MUST |
| FR-08-12 | Saving a technician on a Confirmed booking sets the status to **Assigned** via the Phase 4 status engine (BR-B-11), records history ("Assigned to Ravi Kumar"), and sends **N7** to the customer (technician's first name) and to the technician. | MUST |
| FR-08-13 | **Reassign:** choosing a different technician records history ("Reassigned from Ravi Kumar to Suresh P."), sends N7 to the new technician and the customer, and sends the previous technician a short "Job {number} has been reassigned – no action needed" email. | MUST |
| FR-08-14 | **Unassign** ("— No technician —") sets the status back to **Confirmed** and records history. | MUST |
| FR-08-15 | A **Send job via WhatsApp** button opens `https://wa.me/91{technician phone}?text={job details}` in a new tab, pre-filled with: booking number, service, date, slot, customer first name, address, landmark, customer mobile, instructions and the office number. It uses no paid API. | SHOULD |
| FR-08-16 | A **Print job sheet** button opens a clean printable page with the same details and a signature line. | COULD |
| FR-08-17 | Technicians with no email address: N7 isn't sent to them, and staff see the notice "This technician has no email – send the job via WhatsApp or phone." | MUST |
| FR-08-18 | When a booking is **cancelled or rescheduled** after assignment, the assigned technician gets an email (N10 / N8) if they have an email address. | MUST |
| FR-08-19 | After this phase, `HE_DEV_SKIP_ASSIGN` (Phase 4 §12.3) must be removed, so Confirmed → In Progress is no longer possible without a technician. | MUST |

### 5.3 Booking operations

| ID | Requirement | Priority |
|---|---|---|
| FR-08-20 | The Bookings list (Phase 4) gains a **Technician** column and filter (including "Unassigned"). | MUST |
| FR-08-21 | **Bulk actions** on the Bookings list: "Mark Confirmed" (from Pending), "Mark In Progress" (from Assigned), "Mark Completed" (from In Progress). Each booking goes through the status engine. Bookings where the transition isn't allowed are skipped, and the result notice says "5 updated, 2 skipped (not allowed from their current status)." Customer emails are sent as normal. | SHOULD |
| FR-08-22 | **Rescheduling** (Phase 4 FR-04-48) sends **N8** to the customer ("Your booking has been moved to {date}, {slot}") and to the assigned technician. If the technician isn't available on the new date or already has a job in that slot, staff see the FR-08-11 warning and may reassign. | MUST |
| FR-08-23 | **Schedule view** (HomeEase → Schedule): pick a date (default today, with Previous/Next day buttons). It shows the day's non-cancelled bookings **grouped by slot**, each with booking no., service, area, customer first name, status badge, technician (or a red "Unassigned"), Priority and Slot conflict flags, and a link to edit. At the top: totals per status and "Unassigned: n". | MUST |
| FR-08-24 | The schedule view also has a **technician grid** tab: rows = active technicians available that day, columns = slots, cells = booking numbers (links). Empty cells show "–". Technicians on leave are listed greyed with "On leave". | SHOULD |
| FR-08-25 | **Phone booking** (HomeEase → Bookings → "Add phone booking"): staff select an existing customer (search by name, email or mobile) **or** create one (first name, last name, email, mobile; the customer gets the WooCommerce new-account email with a password-setup link). Then: service, area, PIN, date, slot (capacity check with override), address, contact, instructions, amount, and a payment note (for example "Collect ₹399 on site" or "Paid via UPI ref 1234"). The booking is created with status **Confirmed**, source "Phone", and no WooCommerce order. N6 (confirmed) is sent to the customer. Member benefits aren't applied automatically. Staff adjust the amount manually. | SHOULD |
| FR-08-26 | Phone bookings appear in the customer's My Bookings like online ones, with "Payment: {payment note}" instead of an order link. | SHOULD |

### 5.4 Admin dashboard ("HomeEase Today")

| ID | Requirement | Priority |
|---|---|---|
| FR-08-27 | The WordPress Dashboard must show a **HomeEase Today** widget at the top for Administrators and Shop Managers, with counters that **link to the matching filtered list**: | MUST |
| | a. **Today's bookings** by status (Pending / Confirmed / Assigned / In Progress / Completed) | |
| | b. **Awaiting confirmation**: count, how many are Priority, and the age of the oldest ("oldest: 5 h"). Red when the oldest exceeds the BR-B-16 target (6 h normal / 2 h priority). | |
| | c. **Unassigned (today + tomorrow)**: Confirmed bookings with no technician | |
| | d. **Slot conflicts**: count (red if > 0) | |
| | e. **Refunds required**: bookings + memberships with refund status "Refund required" | |
| | f. **Plan cancellation requests** waiting for a decision | |
| | g. **Plans expiring in 7 days** (renewal follow-up) | |
| | h. **Revenue**: paid today and this month (orders Processing + Completed, minus refunds), with a link to WooCommerce Analytics | |
| FR-08-28 | A second widget **Next 7 days** shows bookings per day (a simple table: date, total, unassigned). | SHOULD |
| FR-08-29 | WordPress's default dashboard widgets (WordPress Events and News, Quick Draft, the welcome panel) and WooCommerce marketing widgets must be hidden for Shop Managers, to keep the screen focused. | SHOULD |
| FR-08-30 | A **daily digest email** at 07:30 to the bookings notification email: today's bookings by slot (with technician), unassigned count, pending confirmations, refunds required, plans expiring in 7 days (`he_daily_admin_digest`). | SHOULD |

### 5.5 Customer management

| ID | Requirement | Priority |
|---|---|---|
| FR-08-31 | Staff can list customers in **Users** (filtered by role Customer) and in **WooCommerce → Customers** (orders count, total spend, last active). | MUST |
| FR-08-32 | The Users list gains columns **Mobile** and **Plan** (current tier + expiry, or "–"). | SHOULD |
| FR-08-33 | The user edit screen gains a **HomeEase** section: mobile, default address, current plan (link), booking counts (upcoming / completed / cancelled) with a link "View bookings" (the Bookings list filtered by this customer), and orders link. | MUST |
| FR-08-34 | The Bookings list supports filtering by customer (`?he_customer=<user id>`), used by the link above. | MUST |
| FR-08-35 | Shop Managers can edit customer details but **can't** edit Administrator or Shop Manager accounts or change roles (WooCommerce default behaviour; verify). | MUST |

### 5.6 Admin structure and permissions

| ID | Requirement | Priority |
|---|---|---|
| FR-08-36 | The admin menu must have a top-level **HomeEase** menu with: Bookings, Add phone booking, Schedule, Memberships, Technicians, Offers, Settings (Administrator only). Icon: a house dashicon. Pending booking count shown as a bubble on "Bookings". | MUST |
| FR-08-37 | Permissions must match [PROJECT_ARCHITECTURE.md §7](../00-Project-Planning/PROJECT_ARCHITECTURE.md) exactly. Every row of that table is tested with the test users (TC-08-10). | MUST |
| FR-08-38 | The **Editor** role (content) can edit pages, posts and media, but sees **no** HomeEase, WooCommerce or Users menus and can't open their URLs. | MUST |

### 5.7 Website content management (admin page A13)

| ID | Requirement | Priority |
|---|---|---|
| FR-08-39 | Staff can update all common content without code, using the places listed in section 7.3. Each place is described in the owner guide (Phase 10). | MUST |

## 6. Page-wise requirements

| Admin page | Purpose |
|---|---|
| Dashboard → HomeEase Today | Morning overview with action links (5.4) |
| HomeEase → Bookings | List (Phase 4) + technician column/filter, bulk actions, customer filter |
| HomeEase → Bookings → Edit | + Technician box, WhatsApp button, print sheet |
| HomeEase → Add phone booking | Manual booking form (FR-08-25) |
| HomeEase → Schedule | Day view by slot + technician grid |
| HomeEase → Technicians | List + edit with jobs box |
| Users / WooCommerce → Customers | Customer management additions |

## 7. Required page sections

### 7.1 HomeEase Today widget layout

```
┌ HomeEase Today – Wed 19/11/2026 ─────────────────────────────────────────┐
│ Today: 9 bookings  [Pending 2] [Confirmed 1] [Assigned 4] [In Prog 1] [Done 1] │
│ ⚠ Awaiting confirmation: 3 (1 priority) · oldest 7 h      → View          │
│ ⚠ Unassigned today + tomorrow: 4                           → View          │
│ ● Slot conflicts: 0                                                       │
│ ₹ Refunds required: 1 · Plan cancellation requests: 1      → View          │
│ ↻ Plans expiring in 7 days: 6                               → Call list     │
│ Revenue: today ₹4,317 · this month ₹86,950                 → Analytics     │
└───────────────────────────────────────────────────────────────────────────┘
```

### 7.2 Technician box on the booking edit screen

Status line ("Not assigned" / "Assigned to Ravi Kumar on 18/11 10:42 by staff1") → dropdown (grouped, with load) → conflict warning + "Assign anyway" → **Save technician** → buttons: Send via WhatsApp · Print job sheet.

### 7.3 Content management map (for the owner guide)

| Content | Where to edit | Who |
|---|---|---|
| Page text and images (Home, About, How It Works, FAQs, policies…) | Pages → Edit (block editor) | Admin, Editor |
| Contact CTA, Book CTA, Packages CTA, Blog CTA | Appearance → Editor → Patterns (synced) or any page using them → "Edit original" | Admin, Editor |
| Blog posts | Posts | Admin, Editor |
| Menus | Appearance → Menus | Admin |
| Header, footer, colours, fonts, logo | Appearance → Customize (Kadence) | Admin |
| Phone, WhatsApp, email, address, hours | HomeEase → Settings | Admin |
| Slots, working days, holidays, capacity default | HomeEase → Settings | Admin |
| Services, prices, categories, areas | Services & Packages | Admin, Shop Manager |
| Packages, prices, benefits | Services & Packages (Maintenance Packages category) | Admin, Shop Manager |
| Coupons | Marketing → Coupons | Admin, Shop Manager |
| Offers and banners | HomeEase → Offers | Admin, Shop Manager |
| Testimonials | Home page → Testimonials block | Admin, Editor |
| Contact form messages | Flamingo → Inbound Messages | Admin |
| Email texts (WooCommerce) | WooCommerce → Settings → Emails | Admin |

## 8. UI components

| Component | Specification |
|---|---|
| Dashboard counter row | Large number + label + link. Red/amber state when above target. |
| Grouped technician dropdown | `<optgroup>` "Recommended" / "Other active technicians". Option text includes the load and reason labels. |
| Conflict warning | Amber admin notice inside the box + "Assign anyway" checkbox |
| Schedule slot group | Slot heading ("10:00 AM – 12:00 PM · 3 bookings"), then rows |
| Technician grid | HTML table with sticky first column; cells are links; "On leave" rows greyed |
| Bulk action result | Standard WordPress admin notice with counts |
| Menu bubble | WordPress `awaiting-mod` count bubble on Bookings |

## 9. Customer interactions

| # | Interaction | Expected behaviour |
|---|---|---|
| CI-1 | Customer's booking is assigned | Customer receives N7 ("Your technician Ravi will arrive between 10:00 AM – 12:00 PM"). Booking Details shows the technician's first name and photo. |
| CI-2 | Customer's booking is rescheduled by staff | N8 email. My Bookings shows the new date and slot. |
| CI-3 | Phone booking made by staff for a new customer | The customer gets an account email (password set-up) and a booking confirmed email, and sees the booking after logging in |

## 10. Administrator interactions

| # | Interaction | Expected behaviour |
|---|---|---|
| AI-1 | Staff add technician "Ravi Kumar", skills Plumbing + Electrical, Zones A and B, Mon–Sat, Available | Appears in the list and as Recommended on Plumbing/Electrical bookings in Zones A and B |
| AI-2 | Staff assign Ravi to a Confirmed plumbing booking | Status Assigned. N7 to the customer and Ravi. History entry. |
| AI-3 | Staff try to assign Ravi to a second booking in the same slot | Warning. Save only after ticking "Assign anyway". |
| AI-4 | Staff mark Ravi On leave 20/11–22/11 | On bookings for those dates Ravi is listed under "Other" with "– on leave" |
| AI-5 | Staff open the Schedule for tomorrow | Bookings by slot. Unassigned ones in red. |
| AI-6 | Staff bulk-mark 4 Assigned bookings as In Progress | 4 updated |
| AI-7 | Staff create a phone booking for an existing customer | Booking Confirmed, source Phone, visible to the customer |
| AI-8 | Owner opens the Dashboard | HomeEase Today with correct counts. Clicking a counter opens the filtered list. |
| AI-9 | Staff open a customer in Users | HomeEase section with plan, booking counts and a View bookings link |
| AI-10 | Editor tries to open `wp-admin/edit.php?post_type=he_booking` | "Sorry, you are not allowed to access this page." |

## 11. Required data fields

### 11.1 Technician (`he_technician`, registered with SCF; not public; capability type `he_technician`)

| Label | Key | Type | Rules |
|---|---|---|---|
| Full name | (post title) | Text | Required |
| Photo | (featured image) | Image, square, ≤ 80 KB | Optional (shown to customers as first name + photo) |
| Mobile | `he_tech_phone` | Text | Required, 10 digits, starts 6–9 |
| Email | `he_tech_email` | Email | Optional (needed for N7 emails) |
| Skills | `he_tech_skills` | Taxonomy field → `product_cat` (multi, checkbox) | At least one |
| Areas | `he_tech_areas` | Taxonomy field → `he_service_area` (multi) | At least one |
| Working days | `he_tech_days` | Checkbox Mon–Sun | Default Mon–Sat |
| Status | `he_tech_status` | Select: available / on_leave / inactive | Default available |
| Leave dates | `he_tech_leave` | Textarea (lines `DD/MM/YYYY` or `DD/MM/YYYY - DD/MM/YYYY`) | Validated on save |
| ID verified | `he_tech_verified` | True/False + date `he_tech_verified_on` | Business record |
| Joining date | `he_tech_joined` | Date | Optional |
| Internal notes | `he_tech_notes` | Textarea | Private |

### 11.2 Booking fields added in this phase

| Key | Type | Notes |
|---|---|---|
| `he_technician_id` | Post ID | Set by the Technician box |
| `he_assigned_at`, `he_assigned_by` | Datetime, user ID | |
| `he_source` | `online` / `phone` | Default `online` |
| `he_offline_payment_note` | Text | Phone bookings |

### 11.3 Email texts

| Email | Subject | Key content |
|---|---|---|
| N7 Customer | "Technician assigned – {booking no.}" | Technician first name, date, slot, "They'll call you before arriving." |
| N7 Technician | "New job {booking no.} – {date} {slot} – {area}" | Service, customer first name, full address + landmark, customer mobile, instructions, office phone. Footer: "Customer details are confidential and only for this job." |
| Reassigned (old technician) | "Job {booking no.} reassigned" | "No action needed." |
| N8 | "Booking rescheduled – {booking no.}" | Old → new date/slot, reason note if given |
| Daily digest | "HomeEase today – {date}: {n} bookings, {u} unassigned" | FR-08-30 content |

## 12. WordPress implementation expectations

1. **Technician post type** via SCF (Post Types), with `public` false, `show_ui` true, `show_in_menu` 'homeease', `exclude_from_search` true, capability type `he_technician`, and the field group from 11.1. Grant the capabilities to administrator and shop_manager on activation (`roles.php`).
2. **Availability helper** `he_technician_is_available( $tech_id, $date )` and **load helper** `he_technician_load( $tech_id, $date, $slot_id = null )` (counts non-cancelled bookings). They are used by the dropdown, warnings, grid and lists. Parse the leave lines once per request.
3. **Technician box** = a meta box on `he_booking`, saved through the same `save_post_he_booking` handler. The status change must call `he_set_booking_status()`, never update `he_status` directly.
4. **WhatsApp link:** build the text with `rawurlencode()`. Line breaks as `%0A`. Keep it under about 1,000 characters.
5. **Bulk actions:** `bulk_actions-edit-he_booking` (add) + `handle_bulk_actions-edit-he_booking` (process) + an `admin_notices` result message.
6. **Schedule page:** `add_submenu_page( 'homeease', … , 'edit_he_bookings', … )`. Query by `he_date` and sort by slot ID. Use GET parameters for the date with `sanitize_text_field` + date validation.
7. **Phone booking page:** a custom admin form (nonce + capability). Reuse the Phase 4 validation and creation functions with `$source = 'phone'`. Create customers with `wc_create_new_customer( $email, '', '', [ 'first_name' => …, 'last_name' => … ] )`. WooCommerce sends the account email; the password is generated, and the setup link lets the customer choose one. Save the mobile as `billing_phone`.
8. **Dashboard widgets:** `wp_add_dashboard_widget()` on `wp_dashboard_setup`. Remove default widgets with `remove_meta_box()` for users without `manage_options`. Revenue: `wc_get_orders( [ 'status' => [ 'processing', 'completed' ], 'date_paid' => '>=' . $start_timestamp, 'limit' => -1, 'return' => 'ids' ] )`, then sum `get_total() - get_total_refunded()`. Cache the result in a transient for 10 minutes.
9. **Menu bubble:** count Pending bookings (cached 5 minutes) and add `<span class="awaiting-mod">n</span>` to the menu label.
10. **User screen additions:** `manage_users_columns` / `manage_users_custom_column`, and `show_user_profile` + `edit_user_profile` for the HomeEase section (read-only, so nothing needs saving).
11. **Daily digest:** a second cron event scheduled on activation (07:30), cleared on deactivation.
12. **Fresher notes:**
    - Build the availability helper first and test it with 3 technicians and a few leave lines before building the dropdown.
    - For each role, log in as the test user (Phase 1) and click every menu. Permission bugs are the most common security problem in custom admin code.

## 13. Free plugins or custom development requirements

| Need | Solution |
|---|---|
| Technician fields and post type | Secure Custom Fields (free) |
| Assignment, workload, schedule, dashboard widgets, digest, phone booking, user columns, bulk actions | **Custom**: HomeEase Core `technicians.php`, `dashboard.php`, `booking-admin.php` |
| Customer list with spend | WooCommerce → Customers (free) |
| Revenue reports | WooCommerce Analytics (free) |
| WhatsApp job sharing | `wa.me` link (free, no API) |
| Roles | WordPress roles + WooCommerce Shop Manager (free) |

## 14. Dependencies

| Needs | From |
|---|---|
| Status engine, booking screens, `HE_DEV_SKIP_ASSIGN` | Phase 4 |
| Membership counters (expiring, cancellation requests, refunds) | Phase 5 |
| Orders, refunds, offers | Phase 6 |
| Customer profile fields (mobile, default address) | Phase 7 |
| Real technician list (names, phones, skills, areas, days) | Owner |

## 15. Expected deliverables

1. Technician post type, fields and list with filters and counts.
2. Technician box with recommendation, load, conflict warning, assign/reassign/unassign, emails, WhatsApp share (and print sheet if time allows).
3. Bookings list technician column/filter, bulk actions, customer filter.
4. Schedule view (slot list + technician grid).
5. Phone booking form.
6. HomeEase Today and Next 7 days widgets. Cleaned dashboard for Shop Managers.
7. Daily digest email.
8. Customer management additions.
9. HomeEase admin menu with pending bubble.
10. Permission verification results (TC-08-10 table filled in).
11. `HE_DEV_SKIP_ASSIGN` removed.

## 16. Acceptance criteria

| ID | Criterion |
|---|---|
| AC-08-01 | Staff can add, edit and deactivate technicians. Inactive technicians never appear in assignment lists. |
| AC-08-02 | On a Plumbing booking in Zone A on a Wednesday, only technicians with the Plumbing skill, Zone A, Wednesday ticked and not on leave appear as Recommended. |
| AC-08-03 | Assigning sets Assigned and sends both N7 emails. Unassigning returns to Confirmed. Reassigning notifies the new and old technician. |
| AC-08-04 | A same-slot double assignment can't be saved without "Assign anyway". |
| AC-08-05 | Confirmed → In Progress isn't possible without a technician (dev flag removed). |
| AC-08-06 | The technician Jobs box and list counts match the bookings data. |
| AC-08-07 | The Schedule view for a date shows every non-cancelled booking for that date, grouped by slot, with unassigned ones highlighted. |
| AC-08-08 | Every HomeEase Today counter is correct against the lists it links to (spot-check 3). |
| AC-08-09 | A phone booking creates a Confirmed booking that appears in the customer's My Bookings, and counts toward slot capacity. |
| AC-08-10 | The role table in [PROJECT_ARCHITECTURE.md §7](../00-Project-Planning/PROJECT_ARCHITECTURE.md) is verified row by row for Administrator, Shop Manager, Editor and Customer, with no deviations. |
| AC-08-11 | Technician records have no public URL (visiting a guessed URL gives 404) and aren't in the sitemap. |

## 17. Testing scenarios

| ID | Scenario | Steps | Expected result |
|---|---|---|---|
| TC-08-01 | Add technicians | Add 3: Ravi (Plumbing, Electrical; A, B; Mon–Sat), Suresh (AC; A–E; Mon–Fri), Anil (Cleaning; C; Mon–Sat, on leave tomorrow) | All listed with correct columns |
| TC-08-02 | Administrator assigns a technician (Scenario 6) | Confirm a Plumbing booking in Zone A → Technician box → Ravi (Recommended) → Save | Assigned, N7 to the customer and Ravi, history entry, Ravi's Jobs box lists it |
| TC-08-03 | Recommendation rules | Open a Cleaning booking in Zone C for tomorrow | Anil listed under Other with "– on leave". No recommended technician shown, with the message "No recommended technician available". |
| TC-08-04 | Double assignment warning | Assign Ravi to a second booking in the same slot | Warning; saves only with "Assign anyway" |
| TC-08-05 | Reassign and unassign | Reassign to Suresh (allowed with the "not skilled" label), then unassign | History entries, emails, final status Confirmed |
| TC-08-06 | Administrator manages a booking (Scenario 17, operations part) | Assigned → In Progress → Completed; then reschedule another assigned booking | Statuses and emails correct. N8 to the customer and technician. |
| TC-08-07 | Schedule view | Open tomorrow's schedule and the technician grid | Matches the bookings list for tomorrow |
| TC-08-08 | Phone booking | Create one for a new customer (customer6) | Account email + N6, booking visible to customer6, capacity reduced |
| TC-08-09 | Dashboard counters | Create 1 pending priority booking aged > 2 h (edit the created time in the test database or wait) | "Awaiting confirmation" shows red with priority 1 |
| TC-08-10 | Permission matrix | For each row of Architecture §7, try the action as each role | Results match the table. Record pass/fail per row. |
| TC-08-11 | Bulk action | Select 3 Assigned + 1 Pending → "Mark In Progress" | 3 updated, 1 skipped |
| TC-08-12 | Customer profile section | Users → customer2 → HomeEase section → View bookings | Bookings list filtered to customer2 |

## 18. Known limitations

- Technicians don't log in, so status updates depend on office staff after phone or WhatsApp contact. A technician portal could be added later (custom, free) if the team grows.
- Workload is counted in bookings per slot. Job duration isn't scheduled minute by minute.
- WhatsApp job sharing opens WhatsApp for staff to press Send. It can't send automatically (that would need the paid WhatsApp Business API).
- Phone bookings have no WooCommerce order, so they don't appear in WooCommerce revenue reports or Payment History. Offline payments are tracked by the payment note only.
- The revenue figure on the dashboard is a quick indicator. WooCommerce Analytics and the Razorpay dashboard are the sources for accounting.
