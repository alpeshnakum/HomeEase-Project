# Phase 10 – Testing, Bug Fixes and Final Handover

| Item | Detail |
|---|---|
| Phase | 10 of 10 |
| Estimated effort | 6 working days (fresher): about 3 days full testing, 2 days fixes and retest, 1 day handover and training |
| Depends on | [Phases 1–9](../MASTER.md) complete on **staging** |
| Unlocks | Go-live and business operation |
| Business rules used | All ([PROJECT_SCOPE.md §7](../00-Project-Planning/PROJECT_SCOPE.md)) |

Back to: [MASTER.md](../MASTER.md) · Previous: [Phase 9](PHASE_09_SEO_SECURITY_PERFORMANCE.md)

---

## 1. Phase title

Testing, Bug Fixes and Final Handover: a complete, recorded test of every business workflow on staging, fixing and retesting defects, owner acceptance testing, the free-only compliance audit, the go-live switch, and handover of documentation, credentials and training.

## 2. Phase objective

The owner receives a working website they can trust and run, with:

- every requirement tested and the result recorded (no feature counted as done without a passed test),
- no open critical or high bugs,
- a written user guide and trained staff,
- all credentials and accounts in the owner's control,
- a confirmed zero-software-cost stack.

## 3. Business context

A booking or payment bug on a live site costs money and reputation immediately. A business owner who can't manage the site alone stays dependent on the developer. This phase closes both risks: proof through recorded tests, and independence through the guide, training and credentials.

## 4. Scope of work

**In scope**

- Test preparation (data, devices, accounts)
- Execution of the master test sheet (section 17.1) and all phase test cases
- Cross-device and browser testing
- Accessibility spot checks
- Security and privacy checks
- Free-only compliance audit
- Bug logging, fixing and regression
- Owner acceptance testing (UAT)
- Test-data clean-up
- Go-live checklist
- Handover package
- Training
- 2-week post-launch support

**Out of scope**

- New features. Any request found during UAT that isn't in these documents is logged as a **change request** for after launch.

## 5. Detailed functional requirements

| ID | Requirement | Priority |
|---|---|---|
| FR-10-01 | All testing is done on **staging** with Razorpay **test** keys, using the test users from Phase 1 plus customers created during testing. | MUST |
| FR-10-02 | The **master test sheet** (section 17.1) must be executed in full. Each row records Actual result, Status (Pass / Fail / Blocked), Tester, Date and Bug ID (if failed). | MUST |
| FR-10-03 | Every phase test case (TC-01-xx to TC-09-xx) must be executed once more on staging, or its earlier result confirmed still valid after the final code. Record the result in the regression sheet (section 17.2). | MUST |
| FR-10-04 | The traceability table (section 11.3) must show at least one passed test for every module. A requirement without a passed test is **not complete**. | MUST |
| FR-10-05 | Device and browser matrix (section 11.2) must be covered for the booking flow, package purchase and account area. | MUST |
| FR-10-06 | Every defect is logged in the bug log (section 11.4) with severity, steps, expected/actual and a screenshot. Fixed bugs are retested and closed only by the tester (or the owner), not by the person who fixed them, where possible. | MUST |
| FR-10-07 | **Exit criteria for go-live:** 0 open Critical, 0 open High, at most 5 open Medium (each accepted in writing by the owner, with a fix date), any number of Low. | MUST |
| FR-10-08 | The **free-only compliance audit** (section 12.2) must be completed and signed by the developer and the owner. | MUST |
| FR-10-09 | The owner (or their delegate) must run the **UAT script** (section 12.3) personally on a phone and give written approval. | MUST |
| FR-10-10 | Before go-live, all **test data must be removed** from the live site: test orders, bookings, memberships, customers, coupon usages and Flamingo messages (section 12.4). | MUST |
| FR-10-11 | The **go-live checklist** (section 12.5) must be completed in order, with a tick and date for each item. | MUST |
| FR-10-12 | The **handover package** (section 15) must be delivered. Credentials must be transferred securely (in person or through the owner's password manager), **never** by plain email or chat. | MUST |
| FR-10-13 | A **training session** (about 2 hours, section 12.6) must be held with the owner and office staff, and the attendance noted. | MUST |
| FR-10-14 | For **2 weeks after go-live**, the developer checks daily: the Wordfence alert email, FluentSMTP failed emails, PHP error log, the HomeEase Today dashboard (slot conflicts), backup emails and Razorpay webhook delivery. Issues found are fixed under the same bug process. | SHOULD |

## 6. Page-wise requirements

Every page in [PROJECT_SCOPE.md §6](../00-Project-Planning/PROJECT_SCOPE.md) (13 public, 10 customer, 13 admin) must be opened at least once during testing on mobile **and** desktop. Use this checklist for each page:

- [ ] Loads without errors (no PHP notice, no console error)
- [ ] All sections from its phase document are present
- [ ] Links and buttons work
- [ ] Works at 360 px without horizontal scrolling
- [ ] One H1. Images have alt text.
- [ ] Correct access (public / customer only / staff only)

Record the result per page in the page checklist tab of the test sheet.

## 7. Required page sections

Not applicable. This phase verifies the sections defined in [Phase 2](PHASE_02_WEBSITE_DESIGN.md), [Phase 3](PHASE_03_SERVICES_MANAGEMENT.md), [Phase 5](PHASE_05_MAINTENANCE_PACKAGES.md), [Phase 6](PHASE_06_PAYMENT_AND_COUPONS.md) and [Phase 7](PHASE_07_CUSTOMER_DASHBOARD.md).

## 8. UI components

Verify the shared components against [Phase 2 §8](PHASE_02_WEBSITE_DESIGN.md) once each: buttons (all states), form inputs and errors, service card, package card, offer card, status badges (all 11 states), notices, date chips, slot cards, account menu, timeline, inline confirmation panels.

## 9. Customer interactions

All customer interactions are covered by the master test sheet (17.1), scenarios 1–4, 7–13 and 18.

## 10. Administrator interactions

All administrator interactions are covered by the master test sheet (17.1), scenarios 5, 6 and 14–17, plus the permission matrix test TC-08-10.

## 11. Required data fields

### 11.1 Test data set (create on staging before testing)

| Data | Values |
|---|---|
| Services | Full sample catalogue (Phase 3 §11.4), all published |
| Areas | Zones A–E. Zone A includes PIN codes `400001`, `400002`. `999999` is in no zone. |
| Technicians | Ravi, Suresh, Anil (Phase 8 TC-08-01) + 1 Inactive |
| Customers | customer1 (no plan), customer2 (Standard Yearly), customer4 (Premium Monthly), new customers created in tests |
| Coupons | WELCOME20, HOME100, ANNUAL15 (expiry in the future), plus `EXPIRED10` (expired yesterday) for testing |
| Offers | One Live (Home + Offers + Top bar), one Scheduled (future), one Ended |
| Holidays | One holiday within the next 7 days |
| Capacity | Plumbing category capacity 1 (for race and full-slot tests), others 2 |

### 11.2 Device and browser matrix

| Device class | Browser | Flows to test |
|---|---|---|
| Android phone (real device, mid-range) | Chrome | Booking, package purchase, coupon, payment, account, cancellation |
| iPhone (real device, or Safari on Mac responsive mode if no device) | Safari | Booking, payment, account |
| Tablet (or DevTools 768 px) | Chrome | Home, Services, Pricing, Account |
| Desktop Windows | Chrome, Edge, Firefox | All flows + admin |
| Desktop macOS (if available) | Safari | Home, booking, checkout |
| Widths | 360, 390, 768, 1024, 1440 px | Every public page |

### 11.3 Traceability (module → requirements → tests)

| Module | Requirements | Tests | Master scenario |
|---|---|---|---|
| Setup | FR-01-01…26 | TC-01-01…08 | – |
| Design & public pages | FR-02-01…24 | TC-02-01…08 | 1 |
| Services | FR-03-01…33 | TC-03-01…10 | 1, 2, 14 |
| Booking | FR-04-01…52 | TC-04-01…15 | 3, 4, 5, 17, 18 |
| Memberships | FR-05-01…45 | TC-05-01…15 | 7, 12, 13, 16 |
| Payments, coupons, offers | FR-06-01…38 | TC-06-01…14 | 8, 9, 10, 15 |
| Customer account | FR-07-01…50 | TC-07-01…14 | 11, 12, 13, 18 |
| Admin & technicians | FR-08-01…39 | TC-08-01…12 | 5, 6, 17 |
| SEO, security, performance | FR-09-01…45 | TC-09-01…14 | – |

### 11.4 Bug log template

| Bug ID | Date | Found in (TC / scenario) | Page / screen | Severity | Steps to reproduce | Expected | Actual | Screenshot | Assigned to | Status (Open / Fixed / Retest / Closed / Won't fix) | Fixed in (date, CHANGELOG ref) | Retested by / date |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| BUG-001 | | | | | | | | | | | | |

**Severity definitions**

| Severity | Meaning | Example |
|---|---|---|
| Critical | Money, data loss, security or a privacy breach; a core flow is impossible | Payment succeeds but no booking created; customer sees another's booking |
| High | A core flow works only with a workaround, or gives wrong business results | Wrong member discount; reminder emails not sent |
| Medium | A non-core feature is broken, or there's a clear UI defect on a main page | Schedule grid misaligned; wrong badge colour |
| Low | Cosmetic, text typo, minor spacing | Extra space in the footer |

## 12. WordPress implementation expectations

### 12.1 Testing approach

1. Freeze code: no new features during testing. Only bug fixes, each recorded in `CHANGELOG.txt` with the bug ID.
2. Test in this order: master scenarios → regression of phase test cases → devices → security/privacy → performance recheck.
3. After every fix batch, rerun the scenarios touching that module (traceability table).
4. Use Mailpit locally or the FluentSMTP email log on staging to verify every email in [PROJECT_ARCHITECTURE.md §8](../00-Project-Planning/PROJECT_ARCHITECTURE.md) at least once (N1–N18). Tick each one in the email checklist tab.

### 12.2 Free-only compliance audit

| # | Check | Result | Initials |
|---|---|---|---|
| 1 | The Plugins list matches [FREE_TECH_STACK.md §2](../00-Project-Planning/FREE_TECH_STACK.md) exactly (screenshot attached) | | |
| 2 | Every third-party plugin and theme was installed from WordPress.org (Plugins → Add New / Themes) | | |
| 3 | No licence key fields are filled in. No "trial" or "days remaining" notices anywhere. | | |
| 4 | No Pro-only Kadence block or setting in use (spot-check Home, Pricing, Contact in the editor) | | |
| 5 | WooCommerce → Extensions: no paid extensions installed | | |
| 6 | No external API with billing is used (Maps embed only, no Maps API key; no SMS; no paid email plan) | | |
| 7 | Razorpay: only the free plugin; the fee schedule was explained to the owner | | |
| 8 | Fonts (Google Fonts), images (owner / Unsplash / Pexels with licence) are free to use | | |
| 9 | Recurring billing not enabled anywhere; all texts say "prepaid / no auto-renewal" | | |
| 10 | The non-software costs (domain, hosting, Razorpay fees) were explained to and accepted by the owner | | |

### 12.3 Owner UAT script (about 45 minutes, on the owner's phone)

1. Open the website and read Home → tap a service category → open a service.
2. Book a plumbing service for tomorrow with a test payment.
3. Open My Account → My Bookings → the new booking.
4. On a laptop, log in as staff → Dashboard → confirm the booking → assign a technician → check the technician email.
5. Buy a Standard Yearly plan with `ANNUAL15` → open My Packages.
6. Book an AC service as that member and see the member price.
7. Create an offer for HOME100 and see it on Home.
8. Change a package price and check the Pricing page.
9. Cancel a booking as the customer, then refund it as staff, and see the refund in the Razorpay test dashboard.
10. Owner signs: "I have tested the above and approve go-live" + date.

### 12.4 Test data clean-up (on live, before opening to the public)

**Option A (recommended):** build live as a **fresh migration from a "clean" staging backup** taken before test transactions. Then re-create only the real content (services, areas, packages, coupons, offers, technicians).

**Option B:** delete the test data manually:

- WooCommerce orders (and their refunds)
- `he_booking`, `he_membership` records
- test customers (Users)
- Flamingo messages
- coupon usage counts. Recreate the 3 coupons so usage starts at 0.

Then:

- Set the counters `he_booking_counter` and `he_membership_counter` back to 0, so the first real booking is `HE-B-000001`.
- Check WooCommerce → Reports/Analytics shows no test revenue. If needed, use the Analytics import "Delete previously imported data" and re-import.

### 12.5 Go-live checklist (complete in order)

| # | Task | Owner / Dev | Done (date) |
|---|---|---|---|
| 1 | Final full backup of staging (UpdraftPlus) | Dev | |
| 2 | Owner's Razorpay account activated for live (KYC approved) | Owner | |
| 3 | Live site created (fresh migration, Option A) on the main domain with HTTPS | Dev | |
| 4 | Real content checked: services, prices, areas, PIN codes, packages, slots, holidays, policies (owner-approved), real testimonials and statistics (or sections hidden) | Owner + Dev | |
| 5 | Razorpay **live** Key ID and Secret entered by the owner. Test mode **off**. Live webhook created with a new secret. | Owner (Dev assists) | |
| 6 | One real ₹1 test: a temporary private product bought with the owner's own UPI → check the order, then refund it and delete the product | Owner + Dev | |
| 7 | FluentSMTP connected on live. Test email received. | Dev | |
| 8 | Server cron on live. `DISABLE_WP_CRON` true. | Dev | |
| 9 | Wordfence enabled on live. 2FA set up for every staff member on live. | Dev + staff | |
| 10 | UpdraftPlus schedule on live → owner's Google Drive. First backup completed. | Dev | |
| 11 | Cache plugin configured on live with exclusions | Dev | |
| 12 | Settings → Reading: "Discourage search engines" **unticked**. WooCommerce Site visibility = **Live**. | Dev | |
| 13 | Google Search Console verified, sitemap submitted. Google Business Profile with matching NAP. | Owner (Dev assists) | |
| 14 | Coupons: real expiry dates and limits | Owner | |
| 15 | Developer admin account downgraded or removed (the owner decides). Owner changes all passwords received in the handover. | Owner | |
| 16 | Staging kept password-protected for future testing, **with Razorpay test keys only** | Dev | |
| 17 | Announce launch | Owner | |

### 12.6 Training agenda (about 2 hours)

| Time | Topic | Uses guide section |
|---|---|---|
| 15 min | Logging in with 2FA. The HomeEase Today dashboard. | 1, 2 |
| 30 min | Daily booking routine: confirm, assign, WhatsApp the job, statuses, reschedule, cancel, phone bookings, schedule view | 3 |
| 15 min | Technicians: add, leave, deactivate | 4 |
| 15 min | Memberships: renewal follow-up list, cancellation requests, extend, manual membership | 5 |
| 15 min | Payments and refunds (WooCommerce + Razorpay dashboard) | 6 |
| 15 min | Services, prices, coupons, offers, page text, clearing the cache | 7, 8 |
| 15 min | Backups, updates, what to do when something breaks | 9, 10 |

## 13. Free plugins or custom development requirements

No new plugins. Testing tools used (all free): browser DevTools, PageSpeed Insights, Rich Results Test, Razorpay test dashboard, FluentSMTP logs, Mailpit (local), WordPress Site Health (Tools → Site Health), a spreadsheet for the test sheet (Google Sheets / LibreOffice Calc).

## 14. Dependencies

| Needs | From |
|---|---|
| Staging with all features | Phases 1–9 |
| Owner availability for UAT (about 1 hour) and training (2 hours) | Owner |
| Razorpay live activation (KYC) | Owner (start KYC by week 8, see [PROJECT_SCOPE.md §11](../00-Project-Planning/PROJECT_SCOPE.md)) |
| Real content (prices, areas, technicians, testimonials, approved policies) | Owner |

## 15. Expected deliverables (handover package)

| # | Deliverable | Format |
|---|---|---|
| 1 | Completed **test sheet**: master scenarios, regression, pages, emails, devices, permissions | Spreadsheet (+ PDF export) |
| 2 | **Bug log**, all closed or accepted | Spreadsheet |
| 3 | Signed **UAT approval** and **free-only compliance audit** | PDF / photo |
| 4 | **Owner user guide** (10–20 pages, screenshots) with sections: 1 Logging in & 2FA · 2 Dashboard · 3 Bookings · 4 Technicians · 5 Memberships · 6 Payments & refunds · 7 Services, prices & packages · 8 Coupons, offers & website content · 9 Backups & updates · 10 Troubleshooting & who to call | PDF or Google Doc |
| 5 | **HomeEase Core technical README** (in the plugin folder): modules and files, settings keys, post types and meta keys, hooks and filters provided, cron jobs, emails list, how to add a slot/area/package, known limitations, update procedure | `homeease-core/README.md` |
| 6 | `CHANGELOG.txt` (plugin) and child theme notes | Text |
| 7 | **Handover sheet** (Phase 9 §11 values) | Spreadsheet / document |
| 8 | **Credentials list**: WordPress admin, hosting panel, domain registrar, DNS, Razorpay, SMTP mailbox, Google account (backups, Search Console, Business Profile). Transferred securely. | Password manager entry / sealed handover |
| 9 | Final **backup set** (files + database) of the live site at launch | Owner's Google Drive + one download |
| 10 | These requirement documents, updated with any approved changes | This folder |

## 16. Acceptance criteria

| ID | Criterion |
|---|---|
| AC-10-01 | All 18 master scenarios in 17.1 are **Pass** on staging (and scenarios 1–4 and 10 also on live with the real ₹1 test). |
| AC-10-02 | Every module in 11.3 has passed tests recorded. |
| AC-10-03 | Exit criteria FR-10-07 are met. |
| AC-10-04 | Every email N1–N18 was received and checked at least once. |
| AC-10-05 | The free-only audit is signed with all 10 checks passed. |
| AC-10-06 | The owner's UAT approval is signed. |
| AC-10-07 | The go-live checklist is fully ticked and dated. |
| AC-10-08 | The handover package (15) is delivered and the training session held. |
| AC-10-09 | The owner can, unaided, confirm and assign a booking, change a price and create an offer (observed during training). |

## 17. Testing scenarios

### 17.1 Master test sheet (required business scenarios)

Fill in the **Actual result** and **Status** columns during testing. Status = Pass / Fail / Blocked.

| # | Scenario | Preconditions | Steps | Expected result | Actual result | Status | Tester / Date |
|---|---|---|---|---|---|---|---|
| 1 | Customer opens the website and browses services | Logged out, mobile | Open Home → scroll → Services → open 2 categories | All sections render. Service cards show price, duration and Book Now. No layout issues at 360–390 px. | | | |
| 2 | Customer selects a plumbing service | – | Services → Plumbing → "Pipe Leakage Repair" → Book Now | Service page with price, price type, duration, areas, included list. Scrolls to the booking form. | | | |
| 3 | Customer chooses a date and time | On the service page | Zone A → PIN 400001 → tomorrow → 10:00–12:00 | Only valid dates selectable (holiday and Sunday disabled). Slots load ≤ 2 s with correct states. | | | |
| 4 | Customer submits a booking | Scenario 3 done | Fill the address → Add booking → log in as customer1 → pay (Razorpay test) | Thank-you page with HE-B-… **Pending**. N5 emails to customer and admin. Order Processing. | | | |
| 5 | Administrator receives and manages the booking | Scenario 4 done | staff1 → Dashboard (Awaiting confirmation +1) → Bookings → open → Confirm | Counter correct. Status Confirmed, history entry, N6 email. | | | |
| 6 | Administrator assigns a technician | Booking Confirmed | Technician box → Ravi (Recommended) → Save → Send via WhatsApp | Status **Assigned**. N7 to customer (first name) and Ravi. WhatsApp opens with job text. Customer Booking Details shows Ravi. | | | |
| 7 | Customer purchases a maintenance package | customer1 has no plan, empty cart | Pricing → Yearly → Buy Standard → pay | Order Completed. HE-M-… **Active** 365 days, 24 waivers, 10%. N12. Shown in My Packages. | | | |
| 8 | Customer applies a valid coupon | New customer3, ₹599 service in cart | Apply `WELCOME20` | −₹120, total ₹479, success notice. Usage recorded after payment. | | | |
| 9 | Customer enters an invalid coupon | Cart with a ₹399 service | Apply `SAVE50`, then `HOME100`, then `EXPIRED10`, then `ANNUAL15` | 4 specific error notices (doesn't exist / minimum spend / expired / not applicable). Total unchanged. | | | |
| 10 | Customer completes a test payment | Any cart | Pay with a Razorpay test success method, then repeat with a failure method | Success: order paid, transaction ID stored, confirmation page and emails. Failure: clear message, no booking, retry from Payment History works. | | | |
| 11 | Customer views their booking history | customer1 with upcoming, completed and cancelled bookings | My Account → My Bookings → each tab → View | Correct tabs, counts and details. Timeline accurate. No internal notes. | | | |
| 12 | Customer views package validity | Scenario 7 done | Dashboard plan card → My Packages | Start, expiry, days left, waivers left match admin. Renew hidden until the last 7 days. | | | |
| 13 | Customer requests package cancellation | Active plan | My Packages → Request cancellation → reason → submit; staff approve | Status Cancellation Requested + N15. Benefits paused. On approval: Cancelled, suggested refund shown, N16. | | | |
| 14 | Administrator creates a new service | staff1 | Add "Geyser Installation" (Electrical, ₹499, fixed, 60 min, Zones A–C) → publish | Visible on Services, Electrical page and its URL. Bookable with correct slots and areas. | | | |
| 15 | Administrator creates a new offer | Coupon HOME100 exists | HomeEase → Offers → add (Home + Offers + Top bar, today → +7) | Shows on Home, Offers and the top bar. Copy code works. Hidden after the end date. | | | |
| 16 | Administrator updates package pricing | Scenario 7 done | Change Standard Yearly to ₹6,499 | Pricing shows ₹6,499. Existing membership unchanged (amount and benefits). New purchase charged ₹6,499. | | | |
| 17 | Administrator manages a booking | Assigned booking | In Progress → Completed. On another booking: reschedule, then cancel and refund | Correct statuses and emails (N9, N8, N10). Order Completed when all its bookings are done. Refund reaches the Razorpay test dashboard. Booking refund status Done. | | | |
| 18 | Mobile users complete the booking flow | Real Android + iPhone | Scenarios 2→4 and 8 on each phone | All steps usable. No zoom on input focus. No horizontal scroll. Razorpay window works. Confirmation shown. | | | |

### 17.2 Additional required tests

| # | Test | Expected result | Actual result | Status |
|---|---|---|---|---|
| 19 | Privacy: customer2 opens customer1's booking, plan and order URLs | "Not found" / invalid order for all | | |
| 20 | Permission matrix (TC-08-10) for all 4 roles | Matches Architecture §7 | | |
| 21 | Race condition (TC-04-08) | Blocked at checkout, or Slot conflict flag + N11 | | |
| 22 | Renewal reminders −7/−1/0/+3 (TC-05-08) | Each once | | |
| 23 | Membership expiry by cron on staging (TC-09-02) | Expired, benefits stop | | |
| 24 | Member price + waiver + coupon combination (FR-06-25) | Matches the calculation | | |
| 25 | ₹0 order via waiver (AC-06-09) | Completed without Razorpay | | |
| 26 | Cache privacy (TC-09-09) | No data leaks | | |
| 27 | Backup restore drill (TC-09-12) | Restored successfully | | |
| 28 | PageSpeed targets (FR-09-42) | Met / accepted | | |
| 29 | Lighthouse Accessibility ≥ 90 on Home, Services, a service page, Pricing, Checkout | Met | | |
| 30 | All emails N1–N18 received | All ticked | | |

### 17.3 Regression sheet

List every TC-01-xx to TC-09-xx from the phase files in one tab, with the columns: TC ID · Title · Last run date · Status · Bug ID.

## 18. Known limitations

These are the project-wide limitations that the owner accepts at handover (details in each phase):

1. **No automatic recurring billing.** Plans are prepaid and renewed manually (Phase 5).
2. **No SMS or WhatsApp API notifications.** Email only, plus manual WhatsApp click-to-chat (Phases 2 and 8).
3. **No technician login or app.** The office updates statuses (Phase 8).
4. **Capacity is per category and slot**, not a minute-level technician calendar (Phase 4).
5. **No online rescheduling for customers** (Phase 4).
6. **Coupons can't cap percentage discounts** or limit to a first order (Phase 6).
7. **No GST invoice generation** (Phase 6).
8. **Free-tier security and backup limits:** Wordfence rules delayed 30 days; UpdraftPlus single remote destination (Phase 9).
9. **Non-software costs:** domain, hosting and Razorpay transaction fees (2% + GST on standard domestic payments) are paid by the business ([FREE_TECH_STACK.md §5](../00-Project-Planning/FREE_TECH_STACK.md)).
10. Free plugin features can change in future versions. **Before every major plugin update, re-check that the features used are still free** (owner guide, section 9).

**Sign-off**

| Role | Name | Signature | Date |
|---|---|---|---|
| Business owner | | | |
| Developer | | | |
