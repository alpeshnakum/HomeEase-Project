# Phase 1 – WordPress Installation and Configuration

| Item | Detail |
|---|---|
| Phase | 1 of 10 |
| Estimated effort | 3 working days (fresher) |
| Depends on | Nothing. This is the starting phase. |
| Unlocks | All other phases |
| Business rules used | BR-A-01, BR-P-01, BR-P-06 ([PROJECT_SCOPE.md](../00-Project-Planning/PROJECT_SCOPE.md)) |

Back to: [MASTER.md](../MASTER.md) · Next: [Phase 2](PHASE_02_WEBSITE_DESIGN.md)

---

## 1. Phase title

WordPress Installation and Configuration: a clean, correctly configured WordPress + WooCommerce + Razorpay (test mode) foundation, with all approved free plugins installed and the HomeEase Core plugin skeleton in place.

## 2. Phase objective

At the end of this phase, the developer has a working local HomeEase site where:

- the correct free theme, child theme and plugins are installed, and nothing else,
- WordPress and WooCommerce are configured for an Indian home-services business (₹, India timezone, no shipping),
- a test payment of ₹1 can be completed through Razorpay **test mode**,
- all required pages exist (empty) with the correct URLs,
- test user accounts exist for every role,
- a first backup has been taken.

## 3. Business context

Every later feature (booking, packages, coupons, dashboards) depends on these settings. A wrong timezone breaks slot times. A wrong currency breaks prices. Guest checkout left on breaks "customers only see their own data". Getting the base right now prevents hard-to-find bugs later.

**Dependency adjustment (documented reason):** the original plan puts payment integration in Phase 6. We install and configure WooCommerce and Razorpay **test mode** here in Phase 1 because the booking flow (Phase 4) and package purchase (Phase 5) can't be tested end to end without a working checkout. Phase 6 still covers the full payment behaviour (failure handling, refunds, webhooks, coupons, offers).

## 4. Scope of work

**In scope**

- Local development environment
- WordPress install and core settings
- Kadence theme + HomeEase child theme
- Installation of the approved plugins (see [FREE_TECH_STACK.md](../00-Project-Planning/FREE_TECH_STACK.md))
- WooCommerce base configuration
- Razorpay test mode
- Local email capture
- Page creation (empty) with slugs
- HomeEase Core plugin skeleton with a business settings page and contact shortcodes
- Test users
- First backup

**Out of scope**

- Page design (Phase 2)
- Services (Phase 3)
- Security plugin and caching (Phase 9, done on staging/live)
- Live hosting and domain (Phase 9/10)

## 5. Detailed functional requirements

| ID | Requirement | Priority |
|---|---|---|
| FR-01-01 | The site must run locally on WordPress 7.0+ and PHP 8.0+ (8.2+ preferred), using LocalWP or XAMPP. | MUST |
| FR-01-02 | Site Title must be "HomeEase" and the Tagline "Trusted Home Services & Maintenance Plans". | MUST |
| FR-01-03 | Timezone must be **Asia/Kolkata** (choose the city "Kolkata", not a UTC offset). Date format `d/m/Y` (for example 17/11/2026), time format `g:i A` (for example 2:30 PM), week starts on **Monday**. | MUST |
| FR-01-04 | Site language must be English (India), or English (United States) if English (India) isn't available. | SHOULD |
| FR-01-05 | Permalinks must be the custom structure `/blog/%postname%/`, so blog posts live under `/blog/`. Pages must keep clean URLs (for example `/about-us/`). | MUST |
| FR-01-06 | Comments must be **disabled** site-wide for new posts and pages (Settings → Discussion: uncheck "Allow people to submit comments on new posts"). | MUST |
| FR-01-07 | Default content must be removed: "Hello world!" post, "Sample Page", the default comment, and the **Hello Dolly** and **Akismet** plugins. Akismet is paid for commercial sites. | MUST |
| FR-01-08 | The **Kadence** theme must be installed, and a child theme named **HomeEase Child** (folder `homeease-child`) must be created and **activated**. | MUST |
| FR-01-09 | These plugins must be installed **and activated**: WooCommerce, Razorpay for WooCommerce, Kadence Blocks, Secure Custom Fields, Contact Form 7, Flamingo, FluentSMTP, Yoast SEO, UpdraftPlus. Wordfence and the caching plugin are installed in Phase 9. | MUST |
| FR-01-10 | When the WooCommerce setup wizard appears, decline every optional extra (marketing tools, Jetpack, extensions, paid themes). Choose "India", currency **Indian rupee (₹)**, and say the business sells **services / virtual products**. | MUST |
| FR-01-11 | WooCommerce → Settings → General: store address = business address (*sample* if unknown). Selling location: **sell to specific countries → India**. Shipping location: **disable shipping & shipping calculations**. Currency INR, currency position "Left", thousand separator `,`, decimal separator `.`, number of decimals **0**. Taxes: **disabled** (owner decision D7). | MUST |
| FR-01-12 | WooCommerce → Settings → Accounts & Privacy: **guest checkout off** (BR-A-01). "Allow customers to create an account on the My Account page" **on**. "During checkout" **on**. Automatic password generation **off**, so customers choose their own password (BR-A-02). Link the Privacy Policy page. | MUST |
| FR-01-13 | WooCommerce pages: **Cart** page contains only the classic `[woocommerce_cart]` shortcode block. **Checkout** page contains only the classic `[woocommerce_checkout]` shortcode block. **My Account** page contains `[woocommerce_my_account]`. The Terms and Conditions page is selected as the checkout terms page. *Reason:* the classic checkout supports the WooCommerce PHP hooks our custom booking code uses (see section 12). | MUST |
| FR-01-14 | WooCommerce → Settings → Advanced → Account endpoints: clear the **Downloads** endpoint field (this hides the tab). Leave the others at their defaults. Phase 7 relabels them. | MUST |
| FR-01-15 | WooCommerce → Settings → Emails: "From" name **HomeEase**, "From" address = business email (*sample* `bookings@homeease.example`), base colour = brand primary `#1A56DB`, footer text "HomeEase – Trusted Home Services · {site_url}". | MUST |
| FR-01-16 | WooCommerce → Settings → Payments: enable **Razorpay** only. Disable Direct bank transfer, Cheque and Cash on delivery (decision D6). | MUST |
| FR-01-17 | Razorpay must be configured with **test** Key ID and Key Secret from the Razorpay dashboard (Test Mode). Title: "Pay online – UPI, Cards, Net Banking, Wallets". Description: "You will be redirected to Razorpay's secure payment window." Payment action: capture automatically (Authorize and Capture). | MUST |
| FR-01-18 | WooCommerce "Site visibility" must stay on **Coming soon** on any online copy (staging/live) until go-live. Local can be "Live". | MUST |
| FR-01-19 | Local emails must be captured and viewable (LocalWP includes Mailpit; on XAMPP, configure FluentSMTP to a test Gmail account). Every test email must be readable during development. | MUST |
| FR-01-20 | All pages listed in section 6 must exist (draft content allowed), with the exact slugs given. **Home** must be the static front page and **Blog** the posts page. | MUST |
| FR-01-21 | The **HomeEase Core** plugin skeleton must exist and be active, with the file structure from [PROJECT_ARCHITECTURE.md](../00-Project-Planning/PROJECT_ARCHITECTURE.md) section 10 (empty module files are fine) and the HPOS compatibility declaration. | MUST |
| FR-01-22 | HomeEase Core must add an admin menu **HomeEase** with a **Settings** sub-page (Administrator only) holding the business details from section 11. It must provide shortcodes `[he_phone]`, `[he_whatsapp_link]`, `[he_email]`, `[he_address]`, `[he_hours]` that print those values. Phase 2 uses them in the header, footer and Contact page. | MUST |
| FR-01-23 | Test users must exist for each role (section 11). | MUST |
| FR-01-24 | A first full backup (files + database) must be created with UpdraftPlus and downloaded to the developer's computer. | MUST |
| FR-01-25 | A `CHANGELOG.txt` must be started in the HomeEase Core folder. | SHOULD |
| FR-01-26 | If Git is used, the repository must contain only `wp-content/themes/homeease-child/` and `wp-content/plugins/homeease-core/`. Never commit WordPress core, other plugins or `wp-config.php`. | COULD |

## 6. Page-wise requirements

Create these pages now with the title and slug shown. Content is added in later phases.

| Page title | Slug | Notes |
|---|---|---|
| Home | `home` | Settings → Reading → static front page |
| About Us | `about-us` | |
| Services | `services` | |
| Pricing & Maintenance Packages | `pricing` | |
| Offers & Discounts | `offers` | |
| How It Works | `how-it-works` | |
| Contact Us | `contact-us` | |
| FAQs | `faqs` | |
| Blog | `blog` | Settings → Reading → posts page |
| Privacy Policy | `privacy-policy` | Settings → Privacy → select this page |
| Terms and Conditions | `terms-and-conditions` | WooCommerce checkout terms page |
| Cancellation and Refund Policy | `cancellation-refund-policy` | |
| Cart | `cart` | Classic cart shortcode |
| Checkout | `checkout` | Classic checkout shortcode |
| My Account | `my-account` | My Account shortcode |

Delete any duplicate pages that the WooCommerce wizard created (for example a second "Shop" page). **Delete the "Shop" page** and leave WooCommerce → Settings → Products → Shop page **empty** (see [PROJECT_ARCHITECTURE.md](../00-Project-Planning/PROJECT_ARCHITECTURE.md) section 12).

## 7. Required page sections

Not applicable in this phase. Page sections are defined in [Phase 2](PHASE_02_WEBSITE_DESIGN.md).

## 8. UI components

| Component | Requirement |
|---|---|
| HomeEase → Settings admin page | One form with labelled fields (section 11), a "Save settings" button and a success notice "Settings saved." |
| Contact shortcodes | Plain text output, except `[he_whatsapp_link]`, which outputs a link `https://wa.me/91XXXXXXXXXX` with the text "Chat on WhatsApp" (the text can be changed with the attribute `text="…"`) |

## 9. Customer interactions

No customer-facing features yet. The developer checks that:

- `/my-account/` shows the Login and Register forms,
- a test customer can register with an email and password and lands on the My Account dashboard.

## 10. Administrator interactions

- The Administrator can open **HomeEase → Settings**, change the business phone number, save, and see the new number wherever `[he_phone]` is used.
- A Shop Manager account **can't** see HomeEase → Settings. Opening it by URL shows WordPress's "Sorry, you are not allowed to access this page."

## 11. Required data fields

### 11.1 HomeEase → Settings, Business details section (option `he_settings`)

| Field label | Key | Type | Validation | Sample value |
|---|---|---|---|---|
| Business name | `business_name` | Text | Required, max 60 characters | HomeEase |
| Support phone | `phone` | Text | Required, 10 digits, starts 6–9 | 9876543210 |
| WhatsApp number | `whatsapp` | Text | 10 digits; stored with a 91 prefix for the link | 9876543210 |
| Support email | `email` | Email | Valid email | support@homeease.example |
| Bookings notification email | `booking_email` | Email | Valid email; staff receive admin notifications here | bookings@homeease.example |
| Office address | `address` | Textarea | Max 250 characters | *Sample address* |
| Working hours | `hours` | Text | Max 80 characters | Mon–Sat, 8:00 AM – 7:00 PM |

The **Booking settings** (slots, capacity, holidays) section is added to this same page in Phase 4.

### 11.2 Test users

| Username | Role | Email (use fake / test inboxes) | Purpose |
|---|---|---|---|
| `owner` | Administrator | owner@homeease.test | Full access |
| `staff1` | Shop Manager | staff1@homeease.test | Office staff tests |
| `editor1` | Editor | editor1@homeease.test | Blog/content tests |
| `customer1` | Customer | customer1@homeease.test | Customer tests |
| `customer2` | Customer | customer2@homeease.test | Privacy (cross-account) tests |

Use strong passwords even locally. **Never** use `admin` as a username.

## 12. WordPress implementation expectations

1. **Child theme:** `homeease-child/style.css` with header `Template: kadence`, and `functions.php` that enqueues the parent and child styles. All custom CSS from Phase 2 goes here (or in Customizer → Additional CSS while experimenting, moved into the child theme before Phase 2 ends).
2. **HomeEase Core main file** (`homeease-core.php`) must include:
   - a standard plugin header (Plugin Name: HomeEase Core, Version: 0.1.0, Text Domain: homeease, Requires Plugins: woocommerce),
   - constants `HE_VERSION`, `HE_PATH`, `HE_URL`,
   - `require_once` for each file in `includes/`,
   - HPOS declaration on the `before_woocommerce_init` hook:
     ```php
     add_action( 'before_woocommerce_init', function () {
         if ( class_exists( \Automattic\WooCommerce\Utilities\FeaturesUtil::class ) ) {
             \Automattic\WooCommerce\Utilities\FeaturesUtil::declare_compatibility( 'custom_order_tables', __FILE__, true );
         }
     } );
     ```
   - `register_activation_hook` and `register_deactivation_hook` functions (empty for now; Phases 4, 5 and 8 add roles and cron).
3. **Settings page:** use the WordPress Settings API (`add_menu_page`, `register_setting`, `add_settings_field`) with capability `manage_options`. Provide a helper `he_get_setting( $key, $default = '' )` that every module uses.
4. **Shortcodes:** `add_shortcode()`; always escape the output (`esc_html`, `esc_url`).
5. **Classic checkout:** WooCommerce's block checkout ignores several classic PHP hooks our booking code relies on (for example `woocommerce_after_checkout_validation`). Using the classic shortcodes keeps the custom code simple for this project. Razorpay supports both.
6. **Fresher notes:**
   - After changing permalinks or adding custom endpoints, always go to **Settings → Permalinks → Save Changes** to flush rewrite rules. Many "404 page not found" errors come from forgetting this.
   - Turn on `WP_DEBUG` and `WP_DEBUG_LOG` in `wp-config.php` locally. Check `wp-content/debug.log` after every feature. **Never** leave `WP_DEBUG_DISPLAY` on for an online site.

## 13. Free plugins or custom development requirements

| Item | Free plugin / custom | Notes |
|---|---|---|
| Theme and design base | Kadence + child theme | [FREE_TECH_STACK.md](../00-Project-Planning/FREE_TECH_STACK.md) §3.2 |
| Ecommerce and checkout | WooCommerce | §3.4 |
| Payment | Razorpay for WooCommerce, **test keys** | §3.5 |
| Fields | Secure Custom Fields (configured in Phase 3) | §3.6 |
| Forms | Contact Form 7 + Flamingo (configured in Phase 2) | §3.9 |
| Email | FluentSMTP (fully configured on staging) | §3.10 |
| SEO | Yoast SEO (configured in Phase 9; skip its setup wizard for now) | §3.11 |
| Backup | UpdraftPlus | §3.13 |
| Business settings and shortcodes | **Custom**: HomeEase Core | Section 12 |

## 14. Dependencies

| Needs | From |
|---|---|
| Razorpay test account (Key ID and Secret) | Owner (free sign-up at razorpay.com, then switch the dashboard to Test Mode) |
| Business contact details (samples allowed) | Owner |
| Nothing from other phases | – |

## 15. Expected deliverables

1. A working local site, with a screenshot of the Plugins page showing only the approved plugins.
2. Child theme folder `homeease-child` (active).
3. HomeEase Core plugin skeleton (active) with the Settings page and shortcodes.
4. All pages from section 6 created with the correct slugs.
5. A successful Razorpay **test** payment for a ₹1 temporary product. Delete the product afterwards.
6. UpdraftPlus backup files downloaded.
7. A short note (5–10 lines) listing the versions installed: WordPress, PHP, every plugin.

## 16. Acceptance criteria

| ID | Criterion |
|---|---|
| AC-01-01 | Settings → General shows timezone "Kolkata". A new draft post shows the current Indian time. |
| AC-01-02 | The Plugins page lists **exactly** the approved plugins plus HomeEase Core. Hello Dolly and Akismet are gone. No plugin shows "Activate licence", "Upgrade to Pro required" or a trial counter for a feature we use. |
| AC-01-03 | The active theme is **HomeEase Child** (parent Kadence). |
| AC-01-04 | WooCommerce shows prices like `₹299` (rupee symbol, no decimals). |
| AC-01-05 | Checkout as a logged-out visitor asks the visitor to log in or create an account. There is no guest option. |
| AC-01-06 | `/my-account/` shows Login + Register, and the account menu has **no** "Downloads" tab. |
| AC-01-07 | A ₹1 test order can be paid with a Razorpay **test** card or UPI test ID and reaches the status **Processing**. The Razorpay test dashboard shows the payment. |
| AC-01-08 | Every page in section 6 opens at its slug without a 404. |
| AC-01-09 | `[he_phone]` placed on a draft page shows the phone number saved in HomeEase → Settings, and updates after the setting changes. |
| AC-01-10 | Logged in as `staff1`, the HomeEase → Settings page can't be accessed. |
| AC-01-11 | An UpdraftPlus backup set (database, plugins, themes, uploads, others) exists and has been downloaded. |
| AC-01-12 | `debug.log` contains no PHP errors or warnings from HomeEase Core or the child theme. |

## 17. Testing scenarios

| ID | Scenario | Steps | Expected result |
|---|---|---|---|
| TC-01-01 | Timezone check | Create a draft post and look at the "Publish immediately" time | Matches current India time |
| TC-01-02 | Guest checkout blocked | Log out → add the ₹1 test product → go to checkout | Login / register prompt; no guest form |
| TC-01-03 | Customer registration | Register `customer3@homeease.test` on `/my-account/` | Account created, dashboard shown, "new account" email visible in Mailpit |
| TC-01-04 | Test payment | Buy the ₹1 product as customer1 with a Razorpay test card | Order "Processing", payment visible in the Razorpay test dashboard |
| TC-01-05 | Payment cancelled | Start payment, then close the Razorpay window | Order stays "Pending payment" and an error/notice is shown |
| TC-01-06 | Settings permission | Open `wp-admin/admin.php?page=he-settings` as staff1 | Access denied |
| TC-01-07 | Shortcode output | Put `[he_whatsapp_link]` on a draft page and click it | Opens `https://wa.me/91<number>` |
| TC-01-08 | Slugs | Visit each URL in section 6 | Page loads (blank content is fine) |

## 18. Known limitations

- Razorpay **webhooks can't reach a localhost site**. Payment success still works locally, because the plugin verifies the payment when the customer returns from Razorpay. Webhooks are configured and tested on staging (Phase 9).
- Local email is captured, not delivered. Real delivery through FluentSMTP is verified on staging.
- WP-Cron on a local site runs only while you browse the site. That's fine for development.
- Live Razorpay keys aren't available until the owner completes Razorpay KYC. They are not needed until go-live.
