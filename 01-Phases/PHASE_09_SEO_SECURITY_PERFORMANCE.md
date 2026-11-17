# Phase 9 – SEO, Security, Backup and Performance

| Item | Detail |
|---|---|
| Phase | 9 of 10 |
| Estimated effort | 3 working days (fresher) + waiting time for hosting/DNS |
| Depends on | Phases 1–8 complete locally. **Staging hosting and domain purchased by the owner** (non-software cost, see [FREE_TECH_STACK.md §5](../00-Project-Planning/FREE_TECH_STACK.md)). |
| Unlocks | Phase 10 (testing on staging, go-live) |
| Business rules used | BR-A-04, BR-A-06, BR-P-06 |

Back to: [MASTER.md](../MASTER.md) · Previous: [Phase 8](PHASE_08_ADMIN_AND_TECHNICIAN_MANAGEMENT.md) · Next: [Phase 10](PHASE_10_TESTING_AND_HANDOVER.md)

---

## 1. Phase title

SEO, Security, Backup and Performance: making the HomeEase site findable on Google, safe from common attacks, recoverable from mistakes, and fast on mobile, using only free tools on a staging copy that will become the live site.

## 2. Phase objective

- The site is moved from local to a **staging** server with HTTPS.
- It has correct basic SEO.
- It is protected by Wordfence and hardened settings, with 2FA for all staff.
- It backs up automatically to the owner's Google Drive, with a tested restore.
- It runs scheduled jobs on a real server cron.
- It delivers emails reliably.
- It meets the performance targets in section 5.5.

## 3. Business context

Customers in our city search Google for "AC service near me" or "plumber in {city}". Basic SEO helps them find us. The site stores home addresses and phone numbers and takes payments, so a security breach would damage the business badly. Backups protect against mistakes and failed updates. Most customers use mid-range Android phones on mobile data, so speed directly affects bookings.

## 4. Scope of work

**In scope**

- Staging setup and migration
- Server cron
- SMTP delivery and DNS email records
- Razorpay webhook (if deferred from Phase 6)
- Yoast SEO configuration
- Meta titles and descriptions
- Sitemaps and noindex rules
- Breadcrumbs
- Local business details
- Search Console preparation
- Wordfence setup, 2FA and hardening
- UpdraftPlus schedules and a restore drill
- Caching plugin and exclusions
- Image and front-end performance checks
- Update policy

**Out of scope**

- Paid SEO tools, paid CDN, paid security services
- Content marketing beyond the 3 blog posts
- Go-live switch (Phase 10)

## 5. Detailed functional requirements

### 5.1 Staging and server

| ID | Requirement | Priority |
|---|---|---|
| FR-09-01 | The owner's hosting must meet [FREE_TECH_STACK.md §6](../00-Project-Planning/FREE_TECH_STACK.md): PHP 8.0+ (8.2+ preferred), MySQL/MariaDB, free SSL, cron jobs, SMTP allowed, at least 256 MB PHP memory. | MUST |
| FR-09-02 | A **staging** site (for example `staging.<domain>`) must be created by migrating the local site with UpdraftPlus (backup → upload → restore with URL search-and-replace) or the host's migration tool. | MUST |
| FR-09-03 | Staging must be protected from the public and from Google: (a) host directory password **or** WooCommerce "Coming soon" mode, **and** (b) Settings → Reading → "Discourage search engines from indexing this site" **ticked**. | MUST |
| FR-09-04 | HTTPS must be active with a valid certificate (free Let's Encrypt from the host). All HTTP URLs must redirect (301) to HTTPS. Settings → General URLs must use `https://`. There must be no mixed-content warnings in the browser console. | MUST |
| FR-09-05 | WP-Cron must be replaced by a **server cron** every 15 minutes: add `define( 'DISABLE_WP_CRON', true );` to `wp-config.php` and a host cron job such as `wget -q -O - "https://<domain>/wp-cron.php?doing_wp_cron" >/dev/null 2>&1` (or the host's recommended PHP command). Check with WooCommerce → Status → Scheduled Actions that past-due actions aren't piling up. | MUST |
| FR-09-06 | `WP_DEBUG` false, `WP_DEBUG_DISPLAY` false on staging and live. `DISALLOW_FILE_EDIT` true (prevents editing plugin and theme files from the admin). | MUST |
| FR-09-07 | The Razorpay webhook must be configured on staging and verified (Phase 6 FR-06-03), if it was deferred. | MUST |

### 5.2 Email delivery

| ID | Requirement | Priority |
|---|---|---|
| FR-09-08 | FluentSMTP must be connected to the business mailbox (Gmail / Google Workspace via OAuth, Brevo free plan, or the host's SMTP). The "From" email must match the connected mailbox or domain. | MUST |
| FR-09-09 | If a domain email is used, the domain DNS must have **SPF** and **DKIM** records from the mail provider, and a basic **DMARC** record (`v=DMARC1; p=none; rua=mailto:<owner email>`). | SHOULD |
| FR-09-10 | FluentSMTP email logging must be **on**, with failure alerts sent to the owner's email (or another free channel FluentSMTP offers). Test emails must reach Gmail and Outlook inboxes (not spam) for: new account, booking received, booking confirmed, renewal reminder, password reset. | MUST |

### 5.3 SEO (Yoast SEO free)

| ID | Requirement | Priority |
|---|---|---|
| FR-09-11 | Yoast site representation: Organization "HomeEase", logo, and social profile URLs (if any). | MUST |
| FR-09-12 | Title templates: Pages and posts `%%title%% %%sep%% HomeEase`. Services (products) `%%title%% in {City} – Book Online %%sep%% HomeEase`. Service categories `%%term_title%% Services in {City} %%sep%% HomeEase`. Home: "HomeEase – Home Cleaning, Plumbing, AC & Repair Services in {City}". Separator "–" or "\|". | MUST |
| FR-09-13 | A unique **meta description** (120–155 characters, including a benefit + call to action) must be written for: the 13 public pages, the 7 category pages and the 10 most important services. Example (AC Service – Split AC): "Book split AC service in {City} at a fixed ₹599. Verified technicians, 2-hour arrival slots, secure online payment. Book in 60 seconds." | MUST |
| FR-09-14 | **Noindex** (Yoast advanced setting per page, or post type setting): Cart, Checkout, My Account, the 6 package products (they are hidden products; the Pricing page is the indexed page for plans), the order-received page and the blog author archives. The "Maintenance Packages" category must also be noindex. | MUST |
| FR-09-15 | **XML sitemap** (Yoast) enabled, including pages, posts, services (products) and service categories, and **excluding** the package products, the Maintenance Packages category, users/authors, tags, and all HomeEase private post types. Check `/sitemap_index.xml`. | MUST |
| FR-09-16 | **Breadcrumbs** enabled (Yoast or Kadence, not both) on service, category and blog pages, with the format from Phase 3 §7.1. | MUST |
| FR-09-17 | Every public page: one H1, logical H2/H3, descriptive alt text on images (Phase 2 rules), human-readable slug, at least 2 internal links (for example service → category, blog post → related service). | MUST |
| FR-09-18 | A **LocalBusiness** structured data block (JSON-LD, type `HomeAndConstructionBusiness`) must be output on the Home and Contact pages with name, logo, phone, email, address, opening hours, area served and URL, all from HomeEase → Settings. *Reason:* the Yoast Local SEO add-on is paid, so a small custom snippet is used. Validate it with Google's Rich Results Test or the Schema.org validator. | SHOULD |
| FR-09-19 | Open Graph metadata enabled in Yoast (Settings → Social / site features), with a default share image (1200 × 630) showing the logo and tagline. | SHOULD |
| FR-09-20 | `robots.txt` must not block CSS, JS or images, and must reference the sitemap (Yoast handles this). Staging stays blocked (FR-09-03). | MUST |
| FR-09-21 | Owner checklist for launch day (performed in Phase 10): verify the domain in **Google Search Console** (free), submit the sitemap, and create or claim the **Google Business Profile** (free) with the same name, address and phone (NAP) as the website. | MUST |

### 5.4 Security

| ID | Requirement | Priority |
|---|---|---|
| FR-09-22 | **Wordfence** installed and activated on staging. Run the firewall in "Learning mode" for 3–7 days of normal use (including test bookings), then switch to **Enabled and Protecting**. Enable Extended Protection (the setup wizard gives the server configuration). | MUST |
| FR-09-23 | Wordfence **brute-force protection**: lock out after **5** failed logins within **4 hours** for **4 hours**. Don't enable "immediately lock out invalid usernames", because customers log in with email addresses and typos would lock them out. Enforce strong passwords for all staff roles. | MUST |
| FR-09-24 | **Two-factor authentication** (Wordfence Login Security, free) **required** for Administrator and Shop Manager roles. Each staff member sets up an authenticator app (free: Google Authenticator / Microsoft Authenticator) and saves their recovery codes. | MUST |
| FR-09-25 | Wordfence Login Security **CAPTCHA** (reCAPTCHA v3, free tier) enabled on the login and registration forms, including the WooCommerce forms, **if** available in the installed version. Otherwise, rely on the honeypot (Phase 7) and brute-force limits. Record which was used. | SHOULD |
| FR-09-26 | Wordfence **scan** enabled with email alerts to the owner for critical and high issues. Fix every critical or high issue before launch. | MUST |
| FR-09-27 | **Hardening:** no user named `admin`; at most 2 Administrator accounts; remove unused themes (keep Kadence, HomeEase Child and one current default WordPress theme as an emergency fallback) and unused plugins; file permissions 755 for folders, 644 for files, 640 (or 600) for `wp-config.php`; XML-RPC disabled if no app uses it (Wordfence option or host setting). | MUST |
| FR-09-28 | **Update policy:** WordPress minor/security releases auto-update. Plugins and themes are updated **weekly**: back up → update on staging → quick test (booking, payment, account) → update live. Wordfence-reported security updates are applied within 48 hours. | MUST |
| FR-09-29 | **Privacy:** only staff who need it have admin accounts. Each staff member has their own account (no shared logins). Customer data exports and deletions follow Phase 7. The Privacy Policy is linked in the footer, at registration and at checkout. | MUST |
| FR-09-30 | **Secrets:** Razorpay keys, SMTP credentials and Google Drive authorisation are stored only in plugin settings, never in code, Git, documents or chat messages. Credentials are handed over to the owner securely in Phase 10. | MUST |

### 5.5 Backup

| ID | Requirement | Priority |
|---|---|---|
| FR-09-31 | UpdraftPlus schedule: **database daily**, keep **14**. **Files weekly**, keep **4**. Remote storage: the owner's **Google Drive** (one remote destination; multiple destinations are a Premium feature). Email report to the owner. | MUST |
| FR-09-32 | Exclude cache folders and old backups from file backups (UpdraftPlus default exclusions + the cache plugin's folder). | SHOULD |
| FR-09-33 | Before every update session and before go-live, take a manual "Backup Now". | MUST |
| FR-09-34 | **Restore drill:** restore the latest backup to a second test location (for example a new local site, or a temporary subfolder/subdomain) and confirm the site, a booking and a membership record work. Record the date and result. Repeat **every 3 months** after launch (owner guide). | MUST |
| FR-09-35 | If the host offers free daily backups, keep them on as a second layer, and note their retention in the handover document. | SHOULD |

### 5.6 Performance

| ID | Requirement | Priority |
|---|---|---|
| FR-09-36 | Install **one** caching plugin according to [FREE_TECH_STACK.md §3.14](../00-Project-Planning/FREE_TECH_STACK.md): LiteSpeed Cache if the host runs LiteSpeed, otherwise WP Super Cache. **Don't** connect QUIC.cloud services. | MUST |
| FR-09-37 | **Never cache** for logged-in users, and never cache `/cart/`, `/checkout/`, `/my-account/` (all endpoints), URLs with `add-to-cart`, `wc-ajax` and `admin-ajax.php` requests (slot loading). Verify: two different customers never see each other's name or cart, and slot availability updates without clearing the cache. | MUST |
| FR-09-38 | Cache lifetime: 8–24 hours for public pages. After changing prices, offers or content, clear the cache (the owner guide explains how). Offers with date limits must disappear on time: use a cache lifetime of 24 hours at most, **or** clear the cache on offer save. | MUST |
| FR-09-39 | Use the caching plugin's free settings for: browser caching, GZIP/Brotli (if the server doesn't already do it), CSS/JS minification (LiteSpeed only; test the booking form and checkout after enabling, and exclude `he-booking.js` and Razorpay scripts if anything breaks), lazy-loading images (WordPress native lazy loading also works). | SHOULD |
| FR-09-40 | All images used meet Phase 2 size rules. Run a Media Library check and re-compress anything over the limit with Squoosh. | MUST |
| FR-09-41 | Load fonts efficiently: only Poppins (600, 700) and Inter (400, 500, 600). Use Kadence's option to load Google Fonts locally if the installed version offers it free. | SHOULD |
| FR-09-42 | **Targets on staging** (Google PageSpeed Insights, which is free): Home, Services, one service page and Pricing must score **≥ 70 mobile** and **≥ 90 desktop** for Performance. Largest Contentful Paint (lab) ≤ 3.0 s on mobile. Total Home page weight ≤ 1.5 MB. Accessibility ≥ 90 and Best Practices ≥ 90. Record the scores. | MUST |
| FR-09-43 | The slot AJAX response must return in **≤ 1 second** on staging (measure in the browser Network tab, average of 5 tries). | SHOULD |
| FR-09-44 | Keep the plugin count at the [FREE_TECH_STACK.md](../00-Project-Planning/FREE_TECH_STACK.md) list. Don't add "speed" plugins beyond the one cache plugin. | MUST |
| FR-09-45 | Free uptime monitoring (for example UptimeRobot's free plan) checking the home page every 5 minutes, alerting the owner by email. | COULD |

## 6. Page-wise requirements

| Page / item | SEO title (template result) | Index? | Cache? |
|---|---|---|---|
| Home | HomeEase – Home Cleaning, Plumbing, AC & Repair Services in {City} | Yes | Yes |
| About, How It Works, FAQs, Contact, Offers, Pricing | {Title} – HomeEase | Yes | Yes |
| Services | Home Services in {City} – HomeEase | Yes | Yes |
| Service category | {Category} Services in {City} – HomeEase | Yes | Yes |
| Service detail | {Service} in {City} – Book Online – HomeEase | Yes | Yes |
| Blog list / posts | Blog – HomeEase / {Post title} – HomeEase | Yes | Yes |
| Policies | {Policy} – HomeEase | Yes | Yes |
| Package products, Maintenance Packages category | – | **No** | Yes |
| Cart, Checkout, My Account, order received | – | **No** | **No** |

## 7. Required page sections

No new visible sections. This phase adds metadata (titles, descriptions, Open Graph, JSON-LD) and the breadcrumbs defined in earlier phases.

## 8. UI components

| Component | Requirement |
|---|---|
| Breadcrumbs | Small muted text above the H1 on service, category and blog pages, with links on all but the last item |
| Cookie notice | **Not required** at launch (no advertising or analytics cookies). If Google Analytics or ads are added later, add a free consent banner first. |
| Staff 2FA screen | Wordfence Login Security setup screen (QR code) for each staff user |

## 9. Customer interactions

| # | Interaction | Expected behaviour |
|---|---|---|
| CI-1 | Customer shares a service page on WhatsApp | The preview shows the page title, description and image (Open Graph) |
| CI-2 | Customer mistypes their password 5 times | Locked out for 4 hours with Wordfence's message. They can reset their password by email. |
| CI-3 | Customer opens the site on 4G | Home becomes usable within about 3 seconds |
| CI-4 | Logged-in customer browses cached pages | Their header shows "My Account" (not a cached "Login") and their own cart |

## 10. Administrator interactions

| # | Interaction | Expected behaviour |
|---|---|---|
| AI-1 | Staff log in | Password + 2FA code required |
| AI-2 | Owner gets the weekly UpdraftPlus email | Backup succeeded; files are in Google Drive |
| AI-3 | Owner changes a service price | After clearing the cache (or the next cache expiry), the new price shows to visitors |
| AI-4 | Owner receives a Wordfence alert | Forwards it to the developer, who acts within 48 hours |
| AI-5 | Staff check email delivery | FluentSMTP → Email Logs show each email's status, and failed ones can be resent |

## 11. Required data fields

| Setting group | Values to record in the handover sheet |
|---|---|
| Hosting | Provider, plan, PHP version, server type (LiteSpeed/Apache/Nginx), control panel URL |
| Domain/DNS | Registrar, nameservers, SPF/DKIM/DMARC records added (yes/no) |
| Cron | Cron command and interval |
| Email | FluentSMTP connection type, sender address, daily sending limit of the provider |
| SEO | Meta descriptions list (page → description), noindex list, sitemap URL |
| Security | Wordfence mode, 2FA enabled users, lockout rules, CAPTCHA used (yes/no) |
| Backup | Schedule, retention, remote destination (owner's Google account), last restore drill date and result |
| Performance | Cache plugin, excluded URLs, PageSpeed scores (mobile/desktop) per page |

## 12. WordPress implementation expectations

1. **Migration:**
   - Back up locally.
   - Create the staging WordPress (or empty database) on the host, then install UpdraftPlus there.
   - Upload the backup set and restore. UpdraftPlus free performs the URL search-and-replace during restore of a migrated backup, so confirm the new URL when asked.
   - Then: Settings → Permalinks → Save. Reconnect FluentSMTP and the UpdraftPlus Google Drive link (authorisations don't transfer).
2. **LocalBusiness JSON-LD:** in HomeEase Core, on `wp_head` for `is_front_page() || is_page( 'contact-us' )`, print `<script type="application/ld+json">` built with `wp_json_encode()` from settings values. Opening hours use the format `Mo-Sa 08:00-19:00`.
3. **Cache exclusions:** WooCommerce marks cart, checkout and account pages as non-cacheable, but still add the URL exclusions in the cache plugin settings, and enable "Don't cache pages for known users" (WP Super Cache) / "Cache Logged-in Users: OFF" (LiteSpeed).
4. **Cache clear on offer save** (if using a long cache): on `save_post_he_offer`, call the cache plugin's purge function if it exists (`function_exists( 'wp_cache_clear_cache' )` for WP Super Cache; `do_action( 'litespeed_purge_all' )` for LiteSpeed). Wrap the calls in checks, so the code doesn't break when the plugin is missing.
5. **Measure before and after:** record PageSpeed scores before installing the cache plugin and after configuring it, and keep both in the handover sheet.
6. **Fresher notes:**
   - Make every change on staging first. Never "try a setting" on live.
   - If the booking form or checkout breaks after enabling minify/combine, turn those options off first. They are the usual cause.
   - Save Wordfence 2FA recovery codes somewhere safe (owner's password manager), or staff can get locked out of their own site.

## 13. Free plugins or custom development requirements

| Need | Solution | Free? |
|---|---|---|
| SEO titles, descriptions, sitemaps, breadcrumbs, noindex, Open Graph | Yoast SEO (free features only; [FREE_TECH_STACK.md §3.11](../00-Project-Planning/FREE_TECH_STACK.md)) | ✅ |
| LocalBusiness schema | **Custom** snippet (Yoast Local SEO is paid) | ✅ |
| Firewall, scan, brute force, 2FA, CAPTCHA | Wordfence free (rules delayed 30 days; country blocking unavailable) | ✅ |
| Backups + Google Drive | UpdraftPlus free (one remote destination) | ✅ |
| Page cache, browser cache, minify | LiteSpeed Cache (no QUIC.cloud) **or** WP Super Cache | ✅ |
| SMTP | FluentSMTP + free mailbox/provider tier | ✅ |
| Search Console, Business Profile, PageSpeed Insights, Rich Results Test | Google (free) | ✅ |
| SSL | Host's free Let's Encrypt | ✅ |
| Hosting and domain | **Paid by the owner** (not software) | ❌ unavoidable |

## 14. Dependencies

| Needs | From |
|---|---|
| Completed local site | Phases 1–8 |
| Hosting, domain, DNS access | Owner (purchase) |
| Business mailbox or Gmail account for SMTP | Owner |
| Owner's Google account for backups | Owner |
| Meta description approval | Owner (optional review) |

If hosting isn't ready, the developer can do the SEO settings, meta descriptions, Wordfence basic settings and backup schedule locally, and record the staging-only items (FR-09-02 to FR-09-10, FR-09-42) as **blocked – waiting for hosting**.

## 15. Expected deliverables

1. Staging site on HTTPS, protected and noindexed.
2. Server cron active.
3. SMTP working with logs; DNS records added (if a domain email is used).
4. Razorpay webhook verified.
5. Yoast configured: titles, 30+ meta descriptions, noindex list, sitemap, breadcrumbs, Open Graph.
6. LocalBusiness JSON-LD validated.
7. Wordfence enabled, 2FA for staff, scan clean.
8. Hardening checklist completed.
9. UpdraftPlus schedule + restore drill record.
10. Cache plugin configured with exclusions; PageSpeed scores recorded.
11. Handover sheet section from §11 filled in.

## 16. Acceptance criteria

| ID | Criterion |
|---|---|
| AC-09-01 | `http://` redirects to `https://` on every URL tested. No mixed-content warnings. Valid certificate. |
| AC-09-02 | Staging isn't publicly accessible (password or coming-soon) and has "Discourage search engines" on. |
| AC-09-03 | With WP-Cron disabled, the membership job runs daily (history entries show "System" at around 08:00) and WooCommerce Scheduled Actions has no past-due backlog. |
| AC-09-04 | The 5 test emails in FR-09-10 arrive in Gmail and Outlook inboxes (not spam) and are logged in FluentSMTP. |
| AC-09-05 | `/sitemap_index.xml` lists pages, posts, services and categories only. Package products, the Maintenance Packages category and private types are absent. |
| AC-09-06 | Cart, Checkout, My Account and package product pages output `noindex`. Every indexed page has a unique title and meta description (spot-check 10). |
| AC-09-07 | The LocalBusiness JSON-LD passes validation with no errors. |
| AC-09-08 | Staff login requires 2FA. 5 wrong passwords lock out the IP for 4 hours. |
| AC-09-09 | The Wordfence scan shows no critical or high issues. The firewall is "Enabled and Protecting". |
| AC-09-10 | Backups appear in the owner's Google Drive. The restore drill succeeded and is recorded with the date. |
| AC-09-11 | Two customers logged in on two browsers never see each other's name, cart or account data. Slot availability is always live (not cached). |
| AC-09-12 | PageSpeed targets in FR-09-42 are met on all 4 pages. If not met, the gaps and reasons are documented and accepted by the owner. |
| AC-09-13 | The booking flow, coupon application, Razorpay test payment and account pages still work after the cache, minify and Wordfence settings (regression run of TC-04-02, TC-06-01 and TC-07-04). |

## 17. Testing scenarios

| ID | Scenario | Steps | Expected result |
|---|---|---|---|
| TC-09-01 | HTTPS | Open `http://staging…/services/` | Redirects to HTTPS |
| TC-09-02 | Cron | Set a test membership to expire yesterday → wait for the next 08:00 run, or trigger the server cron command manually | Status Expired, history "System" |
| TC-09-03 | Email deliverability | Trigger the 5 emails to a Gmail and an Outlook address | Inbox, not spam |
| TC-09-04 | Noindex | View source of Cart, Checkout, My Account and a package product | `noindex` present |
| TC-09-05 | Sitemap | Open the sitemap index and each sub-sitemap | Only the allowed content |
| TC-09-06 | Structured data | Run Home through the Rich Results Test / Schema validator | LocalBusiness detected, no errors |
| TC-09-07 | Brute force | 5 wrong logins with a test account | Lockout message |
| TC-09-08 | 2FA | Log in as staff1 | 2FA code requested |
| TC-09-09 | Cache privacy | customer1 in Chrome, customer2 in Firefox, both browse Home → Services → Cart | Each sees only their own name and cart |
| TC-09-10 | Slot freshness | customer1 books the last place in a slot. customer2 (logged out) reloads that service page and picks the date | Slot shows Full |
| TC-09-11 | Offer expiry with cache | Create an offer ending today. Check tomorrow (or after clearing the cache) | Gone |
| TC-09-12 | Backup restore drill | Restore the latest backup to a test location | Site, booking and membership data intact |
| TC-09-13 | PageSpeed | Test 4 pages on mobile and desktop | Targets met / documented |
| TC-09-14 | Regression | Run TC-04-02, TC-06-01, TC-07-04 on staging | All pass |

## 18. Known limitations

- **Wordfence free firewall rules and malware signatures are delayed by 30 days** compared with Premium, and **country blocking isn't available**. Prompt updates (FR-09-28) reduce the risk.
- **UpdraftPlus free stores backups in one remote location** and doesn't do incremental backups. The host's own backups (if any) give a second copy.
- **Yoast free** doesn't include the redirect manager. If a page URL changes, add a 301 redirect at the host (`.htaccess`/control panel), or avoid changing slugs after launch.
- Without a CDN (paid), visitors far from the server load more slowly. HomeEase serves one city, so this matters little.
- PageSpeed scores depend on the hosting quality. Very cheap shared hosting may not reach the mobile target even with every free optimisation. That's a hosting cost decision for the owner, not a software one.
- Email sending limits of free mailbox/provider tiers apply. At launch volumes (under 50 bookings a day, a few hundred emails) they are enough, but they should be reviewed if volume grows.
