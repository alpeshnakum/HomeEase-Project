# Phase 2 – Website Design and Public Pages

| Item | Detail |
|---|---|
| Phase | 2 of 10 |
| Estimated effort | 10 working days (fresher) |
| Depends on | [Phase 1](PHASE_01_WORDPRESS_SETUP.md) |
| Unlocks | Phase 3 (service pages use this design), every later phase |
| Business rules used | BR-B-12, BR-B-13, BR-M-02, BR-M-11, BR-M-12 (policy text) |

Back to: [MASTER.md](../MASTER.md) · Previous: [Phase 1](PHASE_01_WORDPRESS_SETUP.md) · Next: [Phase 3](PHASE_03_SERVICES_MANAGEMENT.md)

---

## 1. Phase title

Website Design and Public Pages: the HomeEase visual identity, the global header and footer, and all 13 public pages with their sections, built responsively with Kadence, Kadence Blocks (free blocks only) and core WordPress blocks.

## 2. Phase objective

A visitor on mobile or desktop sees a professional, consistent home-services website. They can understand what HomeEase offers, read every public page, contact the business, and reach the **Book Now** path. Dynamic lists (services, packages, offers) appear as clearly marked placeholders and are connected in Phases 3, 5 and 6.

## 3. Business context

For a new home-services brand, the website is the first trust signal. Customers decide in seconds whether to let a stranger into their home. The design must therefore show:

- clear prices,
- verified technicians,
- easy contact (phone and WhatsApp),
- visible policies.

Razorpay also reviews the live website (contact details, pricing, policy pages) before activating live payments. The policy pages in this phase are therefore a business requirement, not just a formality.

## 4. Scope of work

**In scope**

- Design system (colours, typography, spacing, buttons, cards, badges) set up in Kadence
- Header (desktop + mobile)
- Footer
- Floating WhatsApp button
- All 13 public pages: layout, static content and placeholders for dynamic blocks
- Contact form (Contact Form 7 + Flamingo)
- Basic styling of WooCommerce pages (cart, checkout, account) to match the brand
- Three sample blog posts
- Draft policy texts for the owner to review

**Out of scope**

- Service data and service detail page content (Phase 3)
- Booking form (Phase 4)
- Package cards logic (Phase 5)
- Offers logic (Phase 6)
- Customer dashboard tabs (Phase 7)
- SEO metadata (Phase 9)

## 5. Detailed functional requirements

| ID | Requirement | Priority |
|---|---|---|
| FR-02-01 | Every page must use the design tokens in section 8.1 (colours, fonts, spacing, radius) through Kadence global settings. No page may use one-off colours outside the palette. | MUST |
| FR-02-02 | The header must show the logo, the main menu, a click-to-call phone link, a **Login / My Account** link and a **Book Now** button on desktop (≥1025 px). On mobile (≤767 px) it must show the logo, a call icon and a hamburger menu that opens an off-canvas menu with all links plus the Book Now button. | MUST |
| FR-02-03 | The header must be **sticky** (stays visible when scrolling) on desktop and mobile, with a white background and a light shadow after scrolling. | SHOULD |
| FR-02-04 | The "Login / My Account" link must say **Login** for logged-out visitors and **My Account** for logged-in users. Both go to `/my-account/`. | MUST |
| FR-02-05 | The main menu must contain: Home, Services (dropdown with the 7 categories, connected in Phase 3), Pricing, Offers, How It Works, About Us, Blog, Contact Us. FAQs is linked in the footer and on the How It Works and Contact pages. | MUST |
| FR-02-06 | The footer must have four columns on desktop, two on tablet and one on mobile: (1) logo, a one-line description and social icons; (2) Services (7 category links); (3) Company (About, How It Works, Pricing, Offers, Blog, FAQs, Contact); (4) Contact info (phone, WhatsApp, email, address, hours) using the Phase 1 shortcodes. | MUST |
| FR-02-07 | A bottom footer bar must show "© {current year} HomeEase. All rights reserved." plus links to Privacy Policy, Terms and Conditions, and Cancellation and Refund Policy, and the text "Secure payments powered by Razorpay". The year must update automatically. | MUST |
| FR-02-08 | A floating WhatsApp button (bottom-right, 56 × 56 px, WhatsApp green `#25D366`, white icon, `aria-label="Chat with HomeEase on WhatsApp"`) must appear on all public pages and link to `[he_whatsapp_link]`'s URL with the pre-filled text "Hi HomeEase, I need help with a booking". It must **not** cover the Book Now or checkout buttons on mobile. Hide it on `/checkout/`. | SHOULD |
| FR-02-09 | Every public page in section 6 must be built with the sections listed in section 7, in that order. Sections may be adjusted only with owner approval. | MUST |
| FR-02-10 | Each page must have exactly one H1 (the page title or hero heading). Section titles use H2 and card titles use H3. | MUST |
| FR-02-11 | Every image must have descriptive alt text (for example "Technician servicing a split AC unit"), not a file name. Decorative images use empty alt. | MUST |
| FR-02-12 | Images must be compressed before upload: hero images ≤ 200 KB at 1920 px wide, card images ≤ 80 KB at 800 px wide, WebP or JPEG. | MUST |
| FR-02-13 | The Contact Us form (Contact Form 7) must have the fields in section 11.1, send an email to the support email, send an auto-reply to the sender, store each message in Flamingo, and be protected by the CAPTCHA integration available in Contact Form 7 (reCAPTCHA v3 or Turnstile). | MUST |
| FR-02-14 | A Google Maps **embed** (iframe from Google Maps → Share → Embed) of the office location must appear on the Contact page, set to `loading="lazy"`. No Maps API key may be used. | SHOULD |
| FR-02-15 | Dynamic areas must contain a clearly visible placeholder, for example a grey box reading "Service cards – connected in Phase 3", until the phase that connects them. Placeholders must never be visible on staging when the owner reviews a milestone that should already include the feature. | MUST |
| FR-02-16 | All three policy pages must contain the draft text outlines in section 6.11–6.13, marked "DRAFT – to be reviewed by the owner". The owner approves them before go-live. | MUST |
| FR-02-17 | Testimonials and statistics ("5,000+ homes served") **must be real**. Sample testimonials may be used locally and on staging only, and must be replaced or the section hidden before go-live. Publishing invented reviews as genuine is not allowed. | MUST |
| FR-02-18 | WooCommerce Cart, Checkout and My Account pages must use the brand fonts, colours and button styles, with no default purple WooCommerce buttons. | MUST |
| FR-02-19 | The site must have no horizontal scrolling at 360 px width on any page. | MUST |
| FR-02-20 | All interactive elements must have a minimum tap target of 44 × 44 px on mobile and a visible keyboard focus outline. | MUST |
| FR-02-21 | Text must meet WCAG 2.1 AA contrast (4.5:1 for body text, 3:1 for large text and UI elements). | MUST |
| FR-02-22 | Three sample blog posts must be published (section 6.10) with featured images, categories and a "Book a service" CTA box at the end. | SHOULD |
| FR-02-23 | A 404 page must show "Sorry, we couldn't find that page", a search box and buttons to Home and Services. | SHOULD |
| FR-02-24 | Reusable sections (Contact CTA, Packages CTA) must be saved as **synced patterns** so editing one copy updates every page that uses it. | SHOULD |

## 6. Page-wise requirements

General rule: **every page ends with a CTA section** (the synced pattern "Contact CTA" or "Book CTA") followed by the footer.

### 6.1 Home (`/`)

The goal of the home page is to explain the service in 5 seconds and get the visitor to click **Book a Service** or **View Packages**. Sections are listed in section 7.1.

### 6.2 About Us (`/about-us/`)

The goal is to build trust: who we are, how we vet technicians, and where we work.

### 6.3 Services (`/services/`)

The goal is to see all 7 categories and their services with prices, and jump into any service.

### 6.4 Individual Service Details (`/service/<slug>/`)

This is the WooCommerce single product page. **In Phase 2:** apply the brand styling to the Kadence WooCommerce single product layout (fonts, buttons, spacing) and hide the elements not used:

- Hide the product SKU, product tags, "Reviews" tab and quantity box.
- Hide the "Additional information" tab.
- Hide related products, which are replaced by "Other services in this category" in Phase 3.

The section layout is defined in [Phase 3](PHASE_03_SERVICES_MANAGEMENT.md) and the booking form in [Phase 4](PHASE_04_BOOKING_SYSTEM.md).

### 6.5 Pricing & Maintenance Packages (`/pricing/`)

The goal is to compare the 3 packages, understand **prepaid, manual renewal** clearly, and buy. Package cards are connected in Phase 5.

### 6.6 Offers & Discounts (`/offers/`)

The goal is to see current offers and coupon codes and learn how to apply them. Offer cards are connected in Phase 6.

### 6.7 How It Works (`/how-it-works/`)

The goal is to explain booking, payment, the technician visit and memberships step by step, and what each booking status means.

### 6.8 Contact Us (`/contact-us/`)

The goal is for the visitor to reach us by phone, WhatsApp, email or the form, and to see the office location and service areas.

### 6.9 FAQs (`/faqs/`)

The goal is to answer common questions so the visitor doesn't need to call. There must be at least **24 questions** across 6 groups (section 7.9). Answers must match the business rules in [PROJECT_SCOPE.md](../00-Project-Planning/PROJECT_SCOPE.md).

### 6.10 Blog (`/blog/` and single posts)

The goal is useful home-care tips that support SEO and lead to bookings. Sample posts:

1. "5 Signs Your AC Needs Servicing Before Summer"
2. "How to Prevent Kitchen Pipe Leaks: A Simple Monthly Checklist"
3. "Is a Home Maintenance Plan Worth It? Monthly vs Yearly Explained"

Each sample post must be 500–800 words of original text.

### 6.11 Privacy Policy (`/privacy-policy/`) – required content outline

1. Who we are and how to contact us about privacy.
2. **What we collect:** name, email, mobile number, service address, booking details, instructions, membership and order history, contact form messages, technical data (IP address, cookies).
3. **Why we collect it:** to deliver services, send booking and renewal emails, give support, keep legal and accounting records.
4. **Payments:** card, UPI and bank details are entered on Razorpay's secure window and **are not stored by HomeEase**. We receive only a payment reference and status.
5. **Sharing:** the assigned technician receives only the customer's name, phone, address and job instructions for that job. Data is shared with service providers (hosting, email delivery, Razorpay) only to run the service. We never sell data.
6. **Cookies:** cookies for login, cart and security. No advertising cookies at launch.
7. **Retention:** account data is kept while the account is active. Order records are kept as long as accounting or tax law requires.
8. **Your rights:** access, correction and deletion requests through Account Settings or by email. Response within 30 days.
9. Security measures (HTTPS, restricted staff access, backups).
10. Policy changes and the "last updated" date.

Note for the owner: the policy should be reviewed for India's Digital Personal Data Protection Act, 2023 and any rules under it. A legal review is recommended.

### 6.12 Terms and Conditions (`/terms-and-conditions/`) – required content outline

1. Acceptance of terms; eligibility (18+).
2. Services and service areas; the right to decline a booking outside service areas.
3. **Pricing:** "Fixed price" vs "Visit charge" services (BR-B-15). Spare parts and extra work are quoted on site and paid separately.
4. Booking, time slots as arrival windows, rescheduling by HomeEase.
5. Customer responsibilities (access to the home, an adult present, a safe working area, water and electricity available).
6. **Maintenance packages are prepaid for a fixed period and do NOT renew or charge automatically** (BR-M-02). Benefits, validity and expiry.
7. Coupons and offers: one coupon per order, the business may withdraw offers, coupons have no cash value.
8. Cancellations and refunds: see the Cancellation and Refund Policy.
9. Service warranty (*sample*: 7-day re-service on the same issue for labour). The owner confirms or removes this.
10. Limitation of liability, customer property, damage reporting within 48 hours.
11. Account security, prohibited misuse.
12. Governing law (India) and jurisdiction (the owner's city courts).
13. Contact details.

### 6.13 Cancellation and Refund Policy (`/cancellation-refund-policy/`) – required content outline

1. **Customer cancellation of a booking:** online up to 12 hours before the slot starts (BR-B-12), with a full refund (BR-B-13). Within 12 hours, call support (refund at the business's discretion, *sample*: ₹100 late-cancellation fee).
2. **Cancellation by HomeEase** (technician unavailable, area not serviceable): full refund or free reschedule, the customer's choice.
3. **No-show by the customer** (no access at the address): *sample* the visit charge is not refunded.
4. **Membership cancellation:** request from My Packages (BR-M-11). Refund rules exactly as BR-M-12, with a worked example: "Yearly Standard ₹5,999 bought on 1 Jan, cancelled on 10 Mar after using 2 visit-charge waivers worth ₹199 each → full months remaining 9 → (5,999 ÷ 12) × 9 = ₹4,499.25 − ₹398 = ₹4,101.25, rounded to **₹4,101**."
5. **Refund method and timing:** to the original payment method via Razorpay. Initiated within 2 working days of approval. Banks usually credit it within 5–7 working days.
6. **Failed payments:** if money was debited but the order shows failed, the bank or Razorpay normally reverses it automatically. Contact us with the payment reference if it doesn't.
7. How to contact us about a refund.

## 7. Required page sections

Notation: **[Dynamic – Phase N]** means a placeholder now, connected later.

### 7.1 Home

| # | Section | Content and requirements |
|---|---|---|
| 1 | Header & navigation | Global header (FR-02-02) |
| 2 | Hero banner | H1 "Trusted Home Services, Booked in 60 Seconds". Sub-text "Cleaning, plumbing, electrical, AC, painting, pest control and appliance repair, by verified technicians at fixed, upfront prices." Background photo of a technician at work with a dark overlay (text contrast ≥ 4.5:1). Desktop: text left, image right or full-width. Height 520–640 px desktop, auto on mobile. |
| 3 | Main CTA buttons | Primary "Book a Service" → `/services/`. Secondary (outline) "View Maintenance Plans" → `/pricing/`. Below them, 3 trust points with icons: "Verified technicians", "Upfront pricing", "Secure online payment". |
| 4 | Services overview | H2 "Our Services". **[Dynamic – Phase 3]** 7 category cards (image or icon, name, "Starting from ₹X", "View services" link). Desktop grid 4 columns (7 cards: 4 + 3 centred), tablet 2, mobile 1 (or a horizontal scroll row of cards). Button "See all services" → `/services/`. |
| 5 | How It Works | H2 "How HomeEase Works". 4 numbered steps with icons: 1 Choose a service → 2 Pick date & time → 3 Pay securely online → 4 Technician arrives & completes the job. Link "Learn more" → `/how-it-works/`. |
| 6 | Featured maintenance packages | H2 "Maintenance Plans That Save You Money". **[Dynamic – Phase 5]** 3 package cards (Standard highlighted "Most popular"). Note under the cards: "Prepaid plans. No automatic charges. Renew only when you want." Button "Compare all plans" → `/pricing/`. |
| 7 | Special offers | H2 "Current Offers". **[Dynamic – Phase 6]** up to 3 active offer cards with coupon code and "Copy code" button. Hide the whole section if there are no active offers. |
| 8 | Why Choose Us | H2 "Why Homeowners Choose HomeEase". 6 Info Box items: Background-verified technicians · Fixed upfront prices · On-time arrival windows · 7-day service warranty* · Secure payments via Razorpay · Easy online tracking of every booking. (*Only if the owner confirms the warranty.) |
| 9 | Testimonials | H2 "What Our Customers Say". Kadence Testimonials block (free), 3–6 testimonials: photo optional, first name + area, 5-star display, quote ≤ 40 words. Real testimonials only on live (FR-02-17). |
| 10 | FAQs | H2 "Frequently Asked Questions". 5 key questions (accordion), "View all FAQs" → `/faqs/`. |
| 11 | Contact CTA | Synced pattern "Contact CTA": full-width primary-colour band. Text "Need help choosing a service? Talk to us." Buttons "Call [he_phone]" (tel: link), "Chat on WhatsApp", and "Book Now". |
| 12 | Footer | Global footer (FR-02-06, FR-02-07) |

### 7.2 About Us

| # | Section | Content |
|---|---|---|
| 1 | Page hero | H1 "About HomeEase". One line: "Making home maintenance simple, safe and on time." Light background, no large image needed. |
| 2 | Our story | 2 short paragraphs + photo (team or van). Why the business started and the problem it solves (see [MASTER.md](../MASTER.md)). |
| 3 | Mission & values | 4 cards: Safety first · Honest pricing · Respect your home · Always on time |
| 4 | Our technicians | How we vet technicians: ID verification, background check, skill test, training, uniform and ID card. Use an Icon List block. |
| 5 | In numbers | Count Up blocks (free): homes served, technicians, service areas, average rating. **Real numbers only.** Hide the section until real numbers exist. |
| 6 | Areas we serve | List of service-area names. **[Dynamic – Phase 3]** shortcode `[he_service_areas]`. |
| 7 | CTA | Synced "Book CTA" |

### 7.3 Services

| # | Section | Content |
|---|---|---|
| 1 | Page hero | H1 "Home Services". Sub-text "Choose a service, see the price, book a slot." |
| 2 | Category quick links | 7 icon buttons that jump to each category section on the same page (anchor links). On mobile, a horizontal scrolling chip row. |
| 3 | Services by category | **[Dynamic – Phase 3]** For each category: H2 category name, a one-line description and service cards (section 8.4). |
| 4 | Not sure what you need? | Small box: "Book a visit-charge inspection and our expert will quote on site", with a link to the plumber/electrician inspection services. |
| 5 | Packages promo | Synced pattern "Packages CTA": "Save up to 15% on every service with a maintenance plan" → `/pricing/` |
| 6 | CTA | Synced "Contact CTA" |

### 7.4 Individual Service Details

Defined in [Phase 3, section 7](PHASE_03_SERVICES_MANAGEMENT.md). Phase 2 only provides the styling (section 6.4).

### 7.5 Pricing & Maintenance Packages

| # | Section | Content |
|---|---|---|
| 1 | Page hero | H1 "Maintenance Plans". Sub-text "One small prepaid fee. Discounts, free visit charges and priority booking all period." |
| 2 | Monthly / Yearly toggle | Two-option switch, default **Yearly**, label "Yearly – save up to 2 months". **[Dynamic – Phase 5]** |
| 3 | Package cards | **[Dynamic – Phase 5]** 3 pricing cards (section 8.5) |
| 4 | Comparison table | Core Table block with rows from [PROJECT_SCOPE.md §7.5](../00-Project-Planning/PROJECT_SCOPE.md). On mobile the table scrolls horizontally inside its own box (the page itself must not scroll sideways), with the first column sticky if possible. |
| 5 | How memberships work | 4 steps: Buy a plan → Book services with member benefits → Get reminders before expiry → Renew in one click. **Highlighted box:** "Plans are prepaid and do not auto-renew. We never charge your card or UPI automatically." |
| 6 | Package FAQs | 6 questions: When does my plan start? What happens at expiry? Can I upgrade? Can I cancel? What is a visit-charge waiver? Can I use a coupon with my member discount? |
| 7 | CTA | Synced "Contact CTA" |

### 7.6 Offers & Discounts

| # | Section | Content |
|---|---|---|
| 1 | Page hero | H1 "Offers & Discounts" |
| 2 | Active offers | **[Dynamic – Phase 6]** Offer cards (section 8.6). Empty state text: "No live offers right now. Check back soon or join a maintenance plan for year-round savings." |
| 3 | How to use a coupon | 3 steps with a screenshot: Add a service → Enter the code in the cart's "Coupon code" box → Click Apply and see your discount. |
| 4 | Offer terms | Bullet list: one coupon per order, valid until the date shown, can't be exchanged for cash, the business may withdraw offers (see Terms). |
| 5 | CTA | Synced "Packages CTA" |

### 7.7 How It Works

| # | Section | Content |
|---|---|---|
| 1 | Page hero | H1 "How HomeEase Works" |
| 2 | Booking in 4 steps | Same 4 steps as Home, each with 2–3 lines of detail and an illustration or screenshot |
| 3 | After you book | Status timeline using the **status badges** (section 8.7): Pending ("we've received your booking"), Confirmed ("slot confirmed"), Assigned ("technician assigned, name shared"), In Progress, Completed, Cancelled. One sentence each. |
| 4 | Pricing explained | Two cards: **Fixed price** vs **Visit charge** (BR-B-15), with an example for each |
| 5 | Maintenance plans in brief | 3 lines + link to `/pricing/` |
| 6 | Our safety promise | Icon list: verified IDs, masks/shoe covers, clean-up after work, warranty* |
| 7 | Short FAQs | 4 questions + link to `/faqs/` |
| 8 | CTA | Synced "Book CTA" |

### 7.8 Contact Us

| # | Section | Content |
|---|---|---|
| 1 | Page hero | H1 "Contact Us". "We reply within working hours: [he_hours]" |
| 2 | Contact cards | 4 cards: Call (tel: link), WhatsApp (wa.me link), Email (mailto: link), Visit (address). Each uses the Phase 1 shortcodes. |
| 3 | Contact form | Contact Form 7 form (section 11.1). Success message: "Thank you! We'll get back to you within 1 working day." |
| 4 | Map | Google Maps embed (FR-02-14) |
| 5 | Service areas | **[Dynamic – Phase 3]** `[he_service_areas]` + "Not in the list? Message us and we'll let you know." |
| 6 | FAQ link | "Have a quick question? See our FAQs" → `/faqs/` |

### 7.9 FAQs

| # | Section | Content |
|---|---|---|
| 1 | Page hero | H1 "Frequently Asked Questions" |
| 2 | Group jump links | Booking · Payments · Maintenance Plans · Cancellations & Refunds · Technicians & Safety · Account |
| 3 | FAQ groups | One H2 per group with at least 4 questions each, using the Kadence Accordion block (or the core Details block). Only one answer open at a time. |
| 4 | Still need help? | Synced "Contact CTA" |

Required minimum questions (answers must follow the business rules):

- **Booking:** How do I book? · Which areas do you serve? · How far ahead can I book? · Can I book for today?
- **Payments:** Which payment methods are accepted? · Is online payment safe? · My payment failed but money was debited, what now? · Can I pay in cash? (Answer: online only for bookings; spare parts and extra work are paid on site.)
- **Maintenance Plans:** Do plans auto-renew? (No.) · When does my plan start? · What is a visit-charge waiver? · How do I renew?
- **Cancellations & Refunds:** How do I cancel a booking? · When will I get my refund? · Can I cancel my plan? · What if the technician doesn't arrive?
- **Technicians & Safety:** Are technicians verified? · Will I know who's coming? · Do I need to be home? · Is there a warranty?
- **Account:** How do I reset my password? · How do I update my address? · Can I see past bookings? · How do I delete my account?

### 7.10 Blog

**Blog list page:**

| # | Section | Content |
|---|---|---|
| 1 | Page hero | H1 "HomeEase Blog – Home Care Tips" |
| 2 | Post grid | Kadence **Posts** block (free) or the theme archive. Cards show featured image, category, title, excerpt (25 words), date and "Read more". 3 columns desktop, 2 tablet, 1 mobile. 9 posts per page with pagination. |
| 3 | CTA | Synced "Book CTA" |

**Single post page:**

| # | Section | Content |
|---|---|---|
| 1 | Title area | H1 title, date, category and reading time (optional) |
| 2 | Featured image | 16:9, ≤ 150 KB |
| 3 | Content | Max width 760 px for readability. Headings H2/H3. |
| 4 | In-post CTA box | Synced pattern "Blog CTA": "Need a professional? Book [category] service" → `/services/` |
| 5 | Related posts | 3 posts from the same category (Kadence Posts block filtered by category, or theme option). COULD. |

### 7.11–7.13 Policy pages

| # | Section | Content |
|---|---|---|
| 1 | Page hero | H1 = policy name + "Last updated: DD/MM/YYYY" |
| 2 | Table of contents | Kadence Table of Contents block (free) |
| 3 | Policy text | Content outline from 6.11 / 6.12 / 6.13. Readable width (760 px). |
| 4 | Contact for questions | Support email and phone shortcodes |

## 8. UI components

### 8.1 Design tokens (set in Customizer → Kadence → Colors / Typography)

| Token | Value | Use |
|---|---|---|
| Primary | `#1A56DB` | Primary buttons, links, active states |
| Primary dark | `#123C99` | Button hover, footer background |
| Accent | `#F59E0B` | "Most popular" ribbon, offer highlights (never for body text on white) |
| Success | `#16A34A` | Completed status, success messages |
| Warning | `#D97706` | Expiring soon, pending |
| Danger | `#DC2626` | Errors, cancelled |
| Text | `#1F2937` | Body text |
| Muted text | `#6B7280` | Secondary text, labels |
| Border | `#E5E7EB` | Card borders, dividers |
| Light background | `#F3F6FB` | Alternating section backgrounds |
| White | `#FFFFFF` | Page background, cards |

| Typography | Desktop | Mobile | Font |
|---|---|---|---|
| H1 | 44 px / 1.2, weight 700 | 32 px | Poppins |
| H2 | 32 px / 1.25, weight 600 | 26 px | Poppins |
| H3 | 22 px / 1.3, weight 600 | 20 px | Poppins |
| Body | 16 px / 1.6, weight 400 | 16 px | Inter |
| Small / labels | 14 px / 1.5 | 14 px | Inter |
| Buttons | 16 px, weight 600 | 16 px | Inter |

| Spacing & shape | Value |
|---|---|
| Content width | 1200 px max |
| Section vertical padding | 80 px desktop, 48 px mobile |
| Spacing scale | 4, 8, 16, 24, 32, 48, 64, 80 px |
| Border radius | 8 px buttons and inputs, 12 px cards |
| Card shadow | `0 2px 8px rgba(17, 24, 39, 0.08)`; on hover `0 6px 16px rgba(17, 24, 39, 0.12)` |
| Breakpoints | Mobile ≤ 767 px · Tablet 768–1024 px · Desktop ≥ 1025 px (Kadence defaults) |

### 8.2 Buttons

| Type | Style | Example use |
|---|---|---|
| Primary | Primary background, white text, height ≥ 48 px, padding 0 24 px | Book Now, Pay, Add to cart |
| Secondary | White background, primary border and text | View plans, Learn more |
| Text link | Primary colour, underline on hover | "View all FAQs" |
| Disabled | Grey `#9CA3AF` background, `cursor: not-allowed` | Full slot, invalid form |
| Danger | Danger outline | Cancel booking, Request cancellation |

### 8.3 Form inputs

- Label always visible above the field (no placeholder-only labels).
- Height ≥ 48 px, font 16 px (prevents iOS zoom), border `#E5E7EB`, primary-colour focus ring.
- Error text in danger colour below the field, starting with the field name ("Mobile number must be 10 digits").
- Required fields marked with `*` and "(required)" for screen readers.

### 8.4 Service card (used from Phase 3)

Image (4:3), category label, service name (H3), price line ("₹599" or "Visit charge ₹199"), duration ("⏱ approx. 60 min"), **Book Now** button. The whole card links to the service page. Equal card heights in a row.

### 8.5 Package / pricing card (used from Phase 5)

Tier name, price per period ("₹599 /month" or "₹5,999 /year" + "Save ₹1,189"), 5–6 benefit lines with check icons, a **Buy {Tier}** button and a small note "Prepaid · No auto-renewal". The Standard card has an accent "Most popular" ribbon and a primary border.

### 8.6 Offer card / banner (used from Phase 6)

Banner image or a coloured background, headline, sub-text, coupon code in a dashed box with a **Copy code** button (shows "Copied!" for 2 seconds), validity line ("Valid till 31/03/2027"), and a CTA button.

### 8.7 Status badges (used in Phases 4, 5, 7, 8)

Pill shape, 12–14 px text, 4 × 10 px padding:

| Status | Background / text |
|---|---|
| Pending | `#FEF3C7` / `#92400E` |
| Confirmed | `#DBEAFE` / `#1E40AF` |
| Assigned | `#EDE9FE` / `#5B21B6` |
| In Progress | `#FFEDD5` / `#9A3412` |
| Completed | `#DCFCE7` / `#166534` |
| Cancelled | `#FEE2E2` / `#991B1B` |
| Membership Active | Completed colours |
| Membership Upcoming | Confirmed colours |
| Expiring soon | Pending colours |
| Expired / Cancelled | Cancelled colours |
| Cancellation Requested | In Progress colours |

These are defined once as CSS classes `.he-badge`, `.he-badge--pending`, `.he-badge--confirmed` and so on in `he-front.css` and reused everywhere.

### 8.8 Notices

Use WooCommerce notice styles (success, error, info), restyled with the brand colours and an icon. They are used for coupon, cart and booking messages.

## 9. Customer interactions

| # | Interaction | Expected behaviour |
|---|---|---|
| CI-1 | Visitor taps the phone link in the header | The phone dialler opens with the support number (`tel:+91…`) |
| CI-2 | Visitor taps the WhatsApp button | WhatsApp opens with the pre-filled message |
| CI-3 | Visitor opens the mobile menu | Off-canvas menu slides in. All links work. Closes on the X or a tap outside. |
| CI-4 | Visitor submits the contact form with an empty required field | Inline error on that field. Form not sent. |
| CI-5 | Visitor submits a valid contact form | Success message shown. Admin email + auto-reply sent. Message visible in Flamingo. |
| CI-6 | Visitor clicks an FAQ question | The answer expands. Clicking another question closes the first. |
| CI-7 | Visitor clicks "Copy code" on an offer (Phase 6) | Code copied to clipboard. Button text changes to "Copied!". |

## 10. Administrator interactions

| # | Interaction | Expected behaviour |
|---|---|---|
| AI-1 | Owner edits the phone number in HomeEase → Settings | Header, footer, Contact page and CTA bands all show the new number |
| AI-2 | Editor edits the "Contact CTA" synced pattern | The change appears on every page using it |
| AI-3 | Editor publishes a new blog post | It appears first on `/blog/` |
| AI-4 | Staff open Flamingo → Inbound Messages | All contact form messages listed with date, name and email |
| AI-5 | Owner edits FAQ text on the FAQs page | Saved via the block editor. No developer needed. |

## 11. Required data fields

### 11.1 Contact form fields (Contact Form 7)

| Field | Type | Required | Validation |
|---|---|---|---|
| Full name | text | Yes | 2–60 characters |
| Email | email | Yes | Valid email |
| Mobile number | tel | Yes | 10 digits, starts 6–9 (CF7 pattern via `minlength`/`maxlength` + custom validation, or plain tel + note) |
| Service needed | select | No | Options: the 7 categories + "Maintenance plan" + "Other" |
| Area / PIN code | text | No | Max 40 characters |
| Message | textarea | Yes | 10–1000 characters |
| Consent | acceptance | Yes | "I agree to be contacted about my enquiry and accept the Privacy Policy" (linked) |

**Admin email:** subject "New enquiry from [your-name] – HomeEase website", body with all fields.
**Auto-reply:** subject "We received your message – HomeEase", body "Hi [your-name], thanks for contacting HomeEase. Our team will reply within 1 working day. For urgent help call [he_phone]."

### 11.2 Content inputs the owner provides

Logo (SVG or PNG, at least 400 px wide, transparent background), favicon (512 × 512 PNG), social profile URLs, about-us story text, real testimonials (name, area, quote, permission to publish), real statistics, office location for the map, and approved policy texts.

## 12. WordPress implementation expectations

1. **Kadence Customizer** for global colours (set the palette in Kadence's global palette so blocks can pick from it), fonts (Google Fonts Poppins + Inter), header builder (desktop and mobile rows), footer builder (columns, bottom bar), button styles, and WooCommerce styling.
2. **Pages are built with core blocks + free Kadence Blocks only** (see the allowed list in [FREE_TECH_STACK.md](../00-Project-Planning/FREE_TECH_STACK.md) §3.3). If the block inserter shows a block or pattern with a "Pro" label, don't use it.
3. **Synced patterns** (Appearance → Editor → Patterns, or the block editor "Create pattern" → Synced): "Contact CTA", "Book CTA", "Packages CTA", "Blog CTA".
4. **Floating WhatsApp button:** output from the child theme `functions.php` on `wp_footer` (simple HTML link + CSS in the child stylesheet). Use `is_checkout()` to hide it on the checkout page.
5. **Menus:** Appearance → Menus (Kadence uses classic menus): "Primary", "Footer – Services", "Footer – Company". Menu locations are set in the Kadence header and footer builder.
6. **Login / My Account label (FR-02-04):** a small filter in the child theme (`wp_nav_menu_items` or a custom menu item using `is_user_logged_in()`), or two Kadence header "Button" elements with visibility conditions if the free version offers them. Choose the simpler working option and note it in `CHANGELOG.txt`.
7. **Placeholders** are normal Group blocks with a dashed border and the text "[Dynamic – Phase N]". Search the site for "Dynamic – Phase" before each milestone review.
8. **Fresher notes:**
   - Build one page fully (Home), check it on mobile, *then* copy its section styles to the other pages. Don't style 13 pages and fix mobile at the end.
   - Use Chrome DevTools device mode at 360, 390, 768, 1024 and 1440 px widths.
   - Keep custom CSS short and in the child theme. If you need more than about 300 lines of CSS, ask whether a Kadence setting already does it.

## 13. Free plugins or custom development requirements

| Need | Solution | Free? |
|---|---|---|
| Header/footer builder, global styles | Kadence theme (free) | ✅ |
| Hero, cards, info boxes, testimonials, accordion, count-up, posts grid, table of contents | Kadence Blocks free blocks + core blocks | ✅ |
| Contact form + stored messages | Contact Form 7 + Flamingo | ✅ |
| Spam protection | CF7 CAPTCHA integration (reCAPTCHA v3 / Turnstile free tier) | ✅ |
| Map | Google Maps iframe embed (no API key) | ✅ |
| WhatsApp button | Child theme code + `wa.me` link | ✅ |
| Fonts | Google Fonts via Kadence | ✅ |
| Images | Owner photos / Unsplash / Pexels + Squoosh | ✅ |

## 14. Dependencies

| Needs | From |
|---|---|
| Pages, child theme, contact shortcodes | Phase 1 |
| Logo, contact details, texts, photos | Owner (samples allowed until Milestone M1) |
| Dynamic service cards, service areas | Phase 3 |
| Package cards | Phase 5 |
| Offer cards | Phase 6 |

## 15. Expected deliverables

1. Kadence global styles configured (palette, typography, buttons).
2. Desktop and mobile header. Footer with bottom bar.
3. 13 public pages built with all sections from section 7, plus placeholders.
4. 4 synced patterns.
5. Contact Form 7 form + Flamingo storage + CAPTCHA.
6. 3 blog posts.
7. Branded WooCommerce cart, checkout and account pages.
8. Floating WhatsApp button.
9. Screenshots of Home, Pricing and Contact at 390 px and 1440 px for owner approval (Milestone M1).

## 16. Acceptance criteria

| ID | Criterion |
|---|---|
| AC-02-01 | All 13 public pages open without errors and contain every section listed in section 7 (placeholders allowed only for items marked Dynamic). |
| AC-02-02 | At 360 px width, no page scrolls horizontally, no text overlaps, and all buttons are at least 44 px tall. |
| AC-02-03 | The header shows "Login" when logged out and "My Account" when logged in. |
| AC-02-04 | The phone link starts a call on a mobile phone, and the WhatsApp link opens WhatsApp with the pre-filled text. |
| AC-02-05 | A contact form submission sends the admin email and the auto-reply (visible in Mailpit) and appears in Flamingo. An empty required field blocks submission with a visible error. |
| AC-02-06 | Each page has exactly one H1 (checked with a browser extension or by viewing the page source). |
| AC-02-07 | Changing the phone number in HomeEase → Settings updates the header, footer and Contact page without editing any page. |
| AC-02-08 | Editing a synced pattern once updates every page that uses it. |
| AC-02-09 | No image in the Media Library used on public pages is larger than 200 KB. |
| AC-02-10 | Lighthouse (Chrome DevTools) Accessibility score is ≥ 90 on Home, Pricing and Contact. |
| AC-02-11 | The policy pages contain every numbered item from 6.11–6.13 and the "DRAFT" note. |
| AC-02-12 | No block or template marked "Pro" is used anywhere (owner spot-checks 3 pages in the editor). |
| AC-02-13 | The owner approves the Home page design at Milestone M1 (written approval: email or message). |

## 17. Testing scenarios

| ID | Scenario | Steps | Expected result |
|---|---|---|---|
| TC-02-01 | Browse the website (Scenario 1, part 1) | Open Home on a phone → scroll all sections → open the menu → visit every menu link | Every page loads, the layout is correct and links work |
| TC-02-02 | Mobile menu | At 390 px, open the hamburger menu, tap "Pricing" | Pricing page opens and the menu closes |
| TC-02-03 | Contact form success | Fill all fields validly → submit | Success message, 2 emails sent, Flamingo entry |
| TC-02-04 | Contact form validation | Submit with an invalid email and an empty message | Field errors shown, nothing sent |
| TC-02-05 | Footer links | Click each footer link | Correct page, no 404 |
| TC-02-06 | Keyboard navigation | Use Tab through the Home header and hero | Visible focus outline. Book Now is reachable and activates with Enter. |
| TC-02-07 | 404 page | Visit `/does-not-exist/` | Custom 404 with search and buttons |
| TC-02-08 | Cross-browser | Open Home and Pricing in Chrome, Firefox, Edge and Safari (or iOS Safari) | Consistent layout |

## 18. Known limitations

- Kadence free starter templates are optional. Many attractive templates and design-library patterns are Pro and can't be used. Pages are built section by section instead.
- Kadence Blocks free has no slider (Advanced Slider is Pro). The hero is a static image, which is also better for speed.
- Kadence free doesn't include conditional header elements by user role in every case. The Login/My Account switch may need a small code snippet (section 12, item 6).
- The FAQ block won't produce FAQ rich results in Google for most commercial sites (Google limits FAQ rich results), but the content still helps visitors and SEO.
- Policy texts are drafts written for structure. They aren't legal advice, and the owner should get them reviewed.
