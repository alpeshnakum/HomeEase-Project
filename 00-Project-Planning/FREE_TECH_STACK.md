# HomeEase – Free Technology Stack

Back to: [MASTER.md](../MASTER.md) · Related: [PROJECT_SCOPE.md](PROJECT_SCOPE.md) · [PROJECT_ARCHITECTURE.md](PROJECT_ARCHITECTURE.md)

---

## 1. The free-only rule

Every tool in this project must be **free to use for a commercial business website, with no paid licence, paid add-on, trial or subscription**. This document lists the **only** themes and plugins allowed. Installing anything else needs the owner's written approval and an entry in this file.

**How the tools were verified.** Each plugin and theme below was checked on its official WordPress.org listing (and the vendor's site where needed) on **17 November 2026**. The versions noted are the ones listed on that date. Free and paid features change over time, so **re-check the official page before installing**. If a feature listed here as free has become paid, stop and tell the owner.

## 2. Final plugin and theme list (install exactly these)

| # | Type | Name | WordPress.org slug | Required? |
|---|---|---|---|---|
| 1 | Theme | Kadence | `kadence` | MUST |
| 2 | Child theme | HomeEase Child (we create it) | – | MUST |
| 3 | Plugin | WooCommerce | `woocommerce` | MUST |
| 4 | Plugin | Razorpay for WooCommerce | `woo-razorpay` | MUST |
| 5 | Plugin | Kadence Blocks | `kadence-blocks` | MUST |
| 6 | Plugin | Secure Custom Fields | `secure-custom-fields` | MUST |
| 7 | Plugin | Contact Form 7 | `contact-form-7` | MUST |
| 8 | Plugin | Flamingo (stores Contact Form 7 messages) | `flamingo` | SHOULD |
| 9 | Plugin | FluentSMTP | `fluent-smtp` | MUST |
| 10 | Plugin | Yoast SEO | `wordpress-seo` | MUST |
| 11 | Plugin | Wordfence Security | `wordfence` | MUST |
| 12 | Plugin | UpdraftPlus | `updraftplus` | MUST |
| 13 | Plugin | **One** caching plugin: LiteSpeed Cache (LiteSpeed hosting only) **or** WP Super Cache (any other host) | `litespeed-cache` / `wp-super-cache` | MUST on live site |
| 14 | Plugin | HomeEase Core (our custom plugin) | – | MUST |

That is 11 third-party plugins plus our own, which is a minimal stack. **Don't** add sliders, extra page builders, extra form plugins or "all-in-one" toolkits.

## 3. Tool-by-tool details

### 3.1 WordPress (core)

| Field | Description |
|---|---|
| Tool name | WordPress |
| Official URL | https://wordpress.org/download/ |
| Purpose | The CMS: pages, blog posts, media, users, menus, the block editor (Gutenberg), roles, privacy tools, WP-Cron |
| Free features | Everything. Open source (GPL). |
| Limitations | WP-Cron runs only when someone visits the site, so a server cron is needed on live hosting (Phase 9) |
| Paid dependency | None |
| Custom development | None |
| Compatibility | WooCommerce 11.3 requires **WordPress 7.0+**. Use the latest stable WordPress. |

### 3.2 Theme: Kadence (+ HomeEase child theme)

| Field | Description |
|---|---|
| Tool name | Kadence (theme) by Kadence WP. Version 1.6.0 checked. |
| Official URL | https://wordpress.org/themes/kadence/ |
| Purpose | Site design framework: header and footer, global colours and fonts, layouts, WooCommerce page styling |
| Free features | Drag-and-drop **header and footer builder**, global colour palette and typography, starter template library, e-commerce styling. 500,000+ active installs. Requires WordPress 6.6+ and PHP 7.4+. |
| Limitations | Kadence sells paid upgrades ("Kadence Pro"). Any customizer option or template marked **Pro** is **not allowed**. If you use a starter template, use only ones without a Pro badge. |
| Paid dependency | None for this project |
| Custom development | A child theme (`homeease-child`) for custom CSS and any template overrides |
| Compatibility | Built for the block editor and Kadence Blocks; supports WooCommerce |

**Why Kadence:** it has a free header/footer builder and global design settings, and it pairs with Kadence Blocks, so the whole site can be designed without any premium page builder.

**Why not Elementor, Divi or another builder:** Divi is paid. Many Elementor widgets used in popular tutorials (forms, pricing tables, loop grids, popups) are Pro-only. A fresher following videos could easily depend on a paid feature without noticing. The block editor plus Kadence Blocks avoids that risk.

### 3.3 Page building: block editor + Kadence Blocks

| Field | Description |
|---|---|
| Tool name | WordPress block editor (core) + Kadence Blocks. Kadence Blocks version 3.9.1.1 checked. |
| Official URL | https://wordpress.org/plugins/kadence-blocks/ |
| Purpose | Building every page from blocks: hero sections, cards, pricing cards, accordions, testimonials, icons and buttons |
| Free features | **Free blocks listed on WordPress.org:** Row Layout, Section, Advanced Text (heading), Advanced Button, Info Box, Icon, Icon List, Accordion, Tabs, Testimonials, Advanced Gallery, Posts, Count Up, Countdown, Progress Bar, Spacer/Divider, Table of Contents, Show More, Lottie Animation, Advanced Form |
| Limitations | **Pro-only (don't use):** Advanced Query Loop, Advanced Slider, Image Overlay, Modal, Post Grid/Carousel, Product Carousel, Repeater, Split Content, Video Popup, Animate on Scroll, Custom Icons, Custom Fonts, Dynamic Content, the 800+ pattern Design Library. Countdown "evergreen campaigns" are also Pro-only. |
| Paid dependency | None. Use only the free blocks above and core WordPress blocks. |
| Custom development | Dynamic lists (service cards, package cards, offers) are rendered by **HomeEase Core shortcodes**, because the dynamic-content features are Pro-only |
| Compatibility | Works with the Kadence theme and any block theme |

### 3.4 Ecommerce: WooCommerce

| Field | Description |
|---|---|
| Tool name | WooCommerce. Version 11.3.0 checked (released 11 Nov 2026). |
| Official URL | https://wordpress.org/plugins/woocommerce/ |
| Purpose | Services and packages as products, categories, cart, checkout, **orders**, **coupons**, customer accounts (My Account), order emails, refunds, analytics |
| Free features | The core platform is free and open source. Includes products and categories, cart and checkout (block and classic shortcode versions), My Account with registration and lost password, **coupons** (percentage / fixed cart / fixed product, expiry date, minimum and maximum spend, per-coupon and per-user usage limits, product and category include/exclude, individual-use), orders with notes and refunds, built-in Analytics reports, HPOS order storage, transactional emails |
| Limitations | No booking calendar, no time slots, no recurring subscriptions and no memberships in core (those are paid extensions). Core coupons can't cap the maximum discount on a percentage coupon, and can't restrict a coupon to a "first order only" by themselves (see Phase 6). |
| Paid dependency | None. **Don't install** paid WooCommerce.com extensions (Bookings, Subscriptions, Memberships, etc.). |
| Custom development | Booking fields, slot logic, membership records and member discounts are built in HomeEase Core using WooCommerce hooks |
| Compatibility | Requires WordPress 7.0+, PHP 7.4+ (8.0+ recommended), MySQL 5.5.5+ or MariaDB 10.1+. New stores use **HPOS** (orders in custom tables), so custom code must use the WooCommerce order API (`wc_get_order()`, `$order->get_meta()`), **not** `get_post_meta()` on orders. |

### 3.5 Payment gateway: Razorpay for WooCommerce

| Field | Description |
|---|---|
| Tool name | Razorpay for WooCommerce (official, by Razorpay). Version 4.8.9 checked. |
| Official URL | https://wordpress.org/plugins/woo-razorpay/ · Pricing: https://razorpay.com/pricing/ |
| Purpose | Taking online payments (UPI, cards, net banking, wallets) at WooCommerce checkout |
| Free features | Plugin is free (GPLv2). **Test (sandbox) mode** driven by test API keys, webhooks, **refunds from inside WooCommerce**, HPOS compatible (since 4.5.4), **Checkout Blocks** support (4.6.1) and Cart Blocks support (4.7.7) |
| Limitations | **Recurring payments are not available for free.** Razorpay's FAQ says subscriptions need the **WooCommerce Subscriptions** extension plus the Razorpay Subscriptions plugin. WooCommerce Subscriptions is a **paid annual extension (₹26,746 per year** on WooCommerce.com, checked 17 Nov 2026), so we **don't** use it. Webhooks can't reach a site on `localhost`; they are tested on staging or live. |
| Paid dependency | **None for the plugin.** Razorpay charges a **transaction fee** on live payments: a 2% platform fee on standard domestic payments (cards, UPI, net banking, wallets, EMI), 2.15% on corporate cards and up to 3% on international cards, **plus 18% GST on the fee**. There is **no setup fee, no annual maintenance charge and no refund processing fee** (razorpay.com/pricing, checked 17 Nov 2026). The fee is deducted from each payment received, so nothing is paid upfront. Test mode is free. |
| Custom development | None for payments. HomeEase Core reacts to "payment complete" to create bookings and memberships. |
| Compatibility | Requires WooCommerce 4.0+, tested up to WordPress 7.1. Account requirement: a free Razorpay account gives test keys. **Live** keys need business KYC and website review. Razorpay sets the exact document list. |

### 3.6 Custom fields and post types: Secure Custom Fields (SCF)

| Field | Description |
|---|---|
| Tool name | Secure Custom Fields. Version 6.9.9 checked. Maintained by WordPress.org and the community. |
| Official URL | https://wordpress.org/plugins/secure-custom-fields/ |
| Purpose | Adding custom fields (duration, price type, allowed slots, package benefits, technician details, offer details) with a simple admin UI, and registering the **Technician**, **Offer** and **Service Area** structures without writing code |
| Free features | Field groups on posts, products, taxonomy terms and options pages. Registering custom post types and taxonomies from the SCF interface. Repeater, Flexible Content, Gallery and Clone fields are present (all mentioned in the official changelog). No paid tier is offered. |
| Limitations | It is a fields tool only, with no business logic. The booking and membership logic is in HomeEase Core. |
| Paid dependency | None |
| Custom development | Code reads fields with `get_field()`. SCF field groups are **exported to PHP or JSON** and kept in the custom plugin, so they are version-controlled (Phase 3). |
| Compatibility | Uses the ACF-compatible API (`get_field`, `update_field`). **Don't** install "ACF PRO" as well, and don't install a second copy of ACF. |

### 3.7 Booking solution: custom (HomeEase Core)

**Decision: build booking as a small custom module on top of WooCommerce.** Every free booking plugin checked either locks the features we need behind payment or isn't available.

| Option checked | Free version limits (from the official WordPress.org listing, 17 Nov 2026) | Verdict |
|---|---|---|
| **Bookly** (free) | Up to **5 services** only. Online payments, WooCommerce compatibility, coupons and customer account cabinet are **paid** Pro/add-on features. | ❌ We need about 25 services, online payment and coupons |
| **Amelia Lite** | Only **Square** payments are free. PayPal, Stripe, **WooCommerce**, **Razorpay**, **discount coupons**, **customer panel**, **service packages** and **multiple employees** are **Premium**. | ❌ No free Razorpay, coupons or customer panel |
| **BookingPress** | **Closed on WordPress.org since 1 February 2025** ("Guideline Violation"); not available for download | ❌ Not available |
| **WooCommerce Bookings** | Paid extension on WooCommerce.com | ❌ Paid |
| **Custom module in HomeEase Core** | Uses WooCommerce (free) for cart, checkout, payment and coupons, and adds date, slot, area and status logic | ✅ Chosen |

| Field | Description |
|---|---|
| Tool name | HomeEase Core – Booking module (custom) |
| Official URL | Internal (`wp-content/plugins/homeease-core/`) |
| Purpose | Booking fields on service pages, available-slot lookup, capacity rules, booking records, statuses, technician assignment, customer booking pages, admin booking list |
| Free features | Everything, because it's our own code |
| Limitations | Must be maintained by the business's developer. Kept deliberately small (see [PROJECT_ARCHITECTURE.md](PROJECT_ARCHITECTURE.md)). |
| Paid dependency | None |
| Custom development | Yes. Phases 4, 5, 7 and 8. |
| Compatibility | Built with WordPress and WooCommerce public hooks only, and declares HPOS compatibility |

### 3.8 Membership / package management: custom (HomeEase Core)

| Option checked | Status | Verdict |
|---|---|---|
| WooCommerce Subscriptions | Paid (₹26,746/year) | ❌ |
| WooCommerce Memberships | Paid extension on WooCommerce.com | ❌ |
| **Prepaid fixed-term memberships in HomeEase Core** | WooCommerce products for the 6 packages, plus a custom `he_membership` record with dates, status, benefits, reminders and cancellation requests | ✅ Chosen |

**Prepaid membership vs automatic recurring subscription:**

| | Prepaid membership (this project) | Automatic recurring subscription (not used) |
|---|---|---|
| Payment | Customer pays once for 30 or 365 days | Card or UPI mandate charged automatically each period |
| Renewal | Customer clicks **Renew** after reminder emails and pays again | Charged automatically |
| Missed renewal | Membership becomes **Expired**; benefits stop | Retry and dunning logic |
| Cost | ₹0 software | Paid extension + more complex setup |
| Customer control | Full: nothing is charged without the customer's action | Customer must cancel to stop charges |

### 3.9 Contact form: Contact Form 7 + Flamingo

| Field | Description |
|---|---|
| Tool name | Contact Form 7 (version 6.2.5 checked) + Flamingo (message storage add-on by the same author) |
| Official URL | https://wordpress.org/plugins/contact-form-7/ · https://wordpress.org/plugins/flamingo/ |
| Purpose | Contact Us form and an optional "Request a quote" form (painting) |
| Free features | Unlimited forms, email to admin, auto-reply to the sender, file attachments, CAPTCHA integrations. Flamingo saves every submission in the admin, so a lost email doesn't mean a lost lead. |
| Limitations | Contact Form 7 doesn't store submissions by itself (that's why Flamingo is used). Styling is plain and is done with CSS in the child theme. |
| Paid dependency | None. For spam protection, use the CAPTCHA integration offered in your installed version under **Contact → Integration** (Google reCAPTCHA v3 or Cloudflare Turnstile, both with free tiers). **Don't use Akismet**: Akismet requires a paid plan for commercial sites. |
| Custom development | None |
| Compatibility | Works with any theme. Exclude the form page from caching only if the cache plugin causes "invalid nonce" errors. |

*Why not the Kadence Blocks "Advanced Form" block?* It would work for simple forms, but Contact Form 7 + Flamingo gives stored submissions, which the owner needs for follow-up.

### 3.10 Email delivery: FluentSMTP

| Field | Description |
|---|---|
| Tool name | FluentSMTP. Version 2.4.5 checked. |
| Official URL | https://wordpress.org/plugins/fluent-smtp/ |
| Purpose | Sends all site emails (orders, bookings, reminders, password resets) through an authenticated mailbox so they don't land in spam |
| Free features | The listing says it is **"100% free and open source, and it always will be"**. Gmail / Google Workspace (OAuth), Brevo, Amazon SES and generic SMTP. **Email log with resend**, failure alerts and a daily health check. |
| Limitations | Sending limits come from the mail provider you connect. A free Gmail account and Brevo's free plan both have daily caps (check the provider's current limits). At under 50 bookings a day, the HomeEase volume fits a free plan. |
| Paid dependency | None. Use a free mailbox or free provider plan. |
| Custom development | None |
| Compatibility | Works with all WordPress emails (`wp_mail`) |

### 3.11 SEO: Yoast SEO (free)

| Field | Description |
|---|---|
| Tool name | Yoast SEO. Version 28.8 checked. |
| Official URL | https://wordpress.org/plugins/wordpress-seo/ |
| Purpose | Meta titles and descriptions, XML sitemaps, breadcrumbs, basic schema, noindex for private pages |
| Free features | XML sitemaps, title and meta description templates, breadcrumbs, basic schema (Organization / WebSite / WebPage / Article), FAQ block, SEO and readability analysis for one keyphrase |
| Limitations | **Premium-only:** redirect manager, multiple keyphrases (up to 5), internal linking suggestions, social previews for Facebook and X, AI-generated titles and descriptions, extra schema types |
| Paid dependency | None. Redirects, if ever needed, are added by the host or in `.htaccess`. |
| Custom development | None |
| Compatibility | Works with WooCommerce for basic product SEO. The paid "Yoast WooCommerce SEO" add-on is **not** used. |

### 3.12 Security: Wordfence (free)

| Field | Description |
|---|---|
| Tool name | Wordfence Security. Version 9.1.1 checked. |
| Official URL | https://wordpress.org/plugins/wordfence/ |
| Purpose | Firewall, malware scan, brute-force login protection, two-factor authentication for admins |
| Free features | Web application firewall, malware scanner, login security with **2FA** (passkeys available on free installs since 9.0.0), rate limiting and brute-force protection, login attempt limits |
| Limitations | **Free firewall rules and malware signatures arrive 30 days after Premium** users get them ("free version is delayed by 30 days"). **Country blocking is Premium-only.** |
| Paid dependency | None. The 30-day delay is acceptable for a small business when combined with prompt plugin updates. |
| Custom development | None |
| Compatibility | Works on shared hosting. The firewall's "Extended Protection" mode may need a server configuration step (Phase 9). |

### 3.13 Backup: UpdraftPlus (free)

| Field | Description |
|---|---|
| Tool name | UpdraftPlus. Version 1.26.14 checked. |
| Official URL | https://wordpress.org/plugins/updraftplus/ |
| Purpose | Scheduled backups of files and database, plus one-click restore |
| Free features | Scheduled backups, restore, remote storage including **Google Drive** and **Dropbox**, and basic migration (download the backup, upload it to the new site, search-and-replace included) |
| Limitations | **Premium-only:** incremental backups, backing up to **more than one** remote location, database encryption, direct site-to-site migration |
| Paid dependency | None. One remote location (the owner's Google Drive, free 15 GB) is enough. |
| Custom development | None |
| Compatibility | Large sites may hit shared-host time limits; HomeEase is small |

### 3.14 Performance: LiteSpeed Cache **or** WP Super Cache

Pick **one**, based on the live host. Never install both.

| Field | LiteSpeed Cache | WP Super Cache |
|---|---|---|
| Official URL | https://wordpress.org/plugins/litespeed-cache/ | https://wordpress.org/plugins/wp-super-cache/ |
| Version checked | 7.9.3 | 3.1.5 |
| Use when | The host runs **LiteSpeed / OpenLiteSpeed** server (the host's plan page says so) | Any other host (Apache / Nginx) |
| Free features | Page caching (LiteSpeed server required). CSS/JS minify, lazy-load, browser cache and database cleanup work on any server. WooCommerce supported. | Static page cache, "don't cache for known (logged-in) users", cache timeout and garbage collection settings |
| Limitations | **QUIC.cloud** services (CDN, image optimisation and so on) "require payment at certain usage levels", so **don't connect QUIC.cloud**. Use only the local plugin features. | No minification or image optimisation. WooCommerce pages must not be cached (WooCommerce marks cart, checkout and account pages as no-cache; verify in Phase 9). |
| Paid dependency | None if QUIC.cloud isn't used | None |
| Custom development | Exclude `/cart/`, `/checkout/`, `/my-account/` and the slot AJAX call from cache (Phase 9) | Same |

**Image optimisation without a plugin:** compress and resize every image **before uploading** with the free browser tool Squoosh (https://squoosh.app), to WebP or JPEG at 80% quality. WordPress supports WebP uploads natively.

### 3.15 Development and support tools (all free)

| Tool | Official URL | Purpose |
|---|---|---|
| Local (LocalWP) **or** XAMPP | https://localwp.com · https://www.apachefriends.org | Run WordPress on your own computer |
| Visual Studio Code | https://code.visualstudio.com | Code editor |
| Git (optional, recommended) | https://git-scm.com | Track changes to the custom plugin and child theme |
| Google Fonts | https://fonts.google.com | Free fonts (Poppins, Inter), open licence |
| Unsplash / Pexels | https://unsplash.com · https://www.pexels.com | Free stock photos under their own licences |
| Squoosh | https://squoosh.app | Image compression |
| PageSpeed Insights | https://pagespeed.web.dev | Speed testing |
| Google Search Console | https://search.google.com/search-console | Submit sitemap, monitor indexing |
| Google Business Profile | https://business.google.com | Local listing (outside the website) |
| WhatsApp click-to-chat | `https://wa.me/<countrycode><number>` | Free WhatsApp contact link; no API |
| Google Maps embed | Google Maps → Share → Embed a map | Free iframe map on Contact page; no API key needed |

## 4. Not allowed (common traps)

| Don't install / enable | Why |
|---|---|
| Kadence Pro, Kadence Blocks Pro, "Pro" starter templates | Paid |
| Elementor Pro, Divi, WPBakery, Astra Pro, any premium theme | Paid |
| WooCommerce Bookings / Subscriptions / Memberships / Product Add-Ons (WooCommerce.com) | Paid extensions |
| Bookly Pro or add-ons, Amelia Pro | Paid |
| Yoast SEO Premium, Wordfence Premium, UpdraftPlus Premium | Paid |
| QUIC.cloud paid services, any CDN subscription | Paid usage tiers |
| Akismet on this site | Paid for commercial sites |
| SMS gateways, WhatsApp Business API, Google Maps JavaScript/Places API | Paid or billing account required |
| "Free trial" plugins or themes, "GPL club" or nulled downloads | Not allowed. Nulled software often carries malware. |
| Any plugin that asks for a licence key for a feature we need | Paid dependency |

## 5. Non-software costs (be honest with the owner)

Software for this project costs **₹0**. These items are **not software** and can't be avoided for a live business website:

| Item | When needed | Approximate cost | Can it be zero? |
|---|---|---|---|
| Development on a local computer | Phases 1–10 | ₹0 | ✅ Yes |
| Razorpay test mode | Phases 1–10 | ₹0 | ✅ Yes |
| Domain name (for example `homeease.in`) | Go-live | About ₹700–1,200 per year (varies by registrar and extension) | ❌ No |
| Web hosting with SSL, PHP 8+ and cron | Go-live | About ₹1,500–6,000 per year for basic shared hosting (varies) | ❌ Not for a real business. Free hosts are unreliable and often block cron or email. |
| SSL certificate | Go-live | ₹0 (free Let's Encrypt from the host) | ✅ Yes |
| Razorpay transaction fee | Each live payment | 2% + 18% GST on the fee (standard domestic) | ❌ Deducted from each payment received |
| Business email | Go-live | ₹0 with Gmail + FluentSMTP | ✅ Yes |

## 6. Compatibility summary

| Item | Minimum |
|---|---|
| WordPress | 7.0+ (required by WooCommerce 11.3) |
| PHP | 8.0+ (8.2 or newer recommended) |
| Database | MySQL 5.7+/8.0 or MariaDB 10.4+ (WooCommerce minimum: MySQL 5.5.5 / MariaDB 10.1) |
| HTTPS | Required on staging and live (Razorpay and logins) |
| Server cron | Required on live (Phase 9) |
| Memory limit | 256 MB recommended (`WP_MEMORY_LIMIT`) |
