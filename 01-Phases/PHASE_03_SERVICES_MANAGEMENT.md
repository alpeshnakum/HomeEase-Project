# Phase 3 – Service and Category Management

| Item | Detail |
|---|---|
| Phase | 3 of 10 |
| Estimated effort | 4 working days (fresher) |
| Depends on | [Phase 1](PHASE_01_WORDPRESS_SETUP.md), [Phase 2](PHASE_02_WEBSITE_DESIGN.md) |
| Unlocks | Phase 4 (booking needs services, areas, allowed slots), Phase 5 (packages reuse the product setup) |
| Business rules used | BR-B-03, BR-B-04, BR-B-07, BR-B-15, BR-M-06, BR-M-07 |

Back to: [MASTER.md](../MASTER.md) · Previous: [Phase 2](PHASE_02_WEBSITE_DESIGN.md) · Next: [Phase 4](PHASE_04_BOOKING_SYSTEM.md)

---

## 1. Phase title

Service and Category Management: the HomeEase service catalogue (7 categories, about 25 services, service areas with PIN codes) managed by staff in the WordPress admin and shown on the Home, Services, category and service detail pages.

## 2. Phase objective

Staff can add, edit, price, enable or disable services, organise them into categories, and set where and when each service is available, without a developer. Customers can browse all services with prices, durations and areas, and open a detailed page for each service.

## 3. Business context

The service catalogue is the "menu" of the business. Prices and durations must be clear, so customers trust the booking and staff aren't asked the same questions by phone. The catalogue also sets the rules the booking system uses later:

- which areas a service covers,
- which time slots it can start in,
- whether members get discounts or a free visit charge.

## 4. Scope of work

**In scope**

- Service = WooCommerce product setup
- Custom fields for services and categories
- Service Area taxonomy with PIN codes
- Permalink bases
- `/shop/` redirect
- Admin labels
- Shortcodes for category cards, service cards, grouped services and areas
- Service detail page layout
- Sample catalogue entry
- Connecting the Phase 2 placeholders on Home, Services, About and Contact

**Out of scope**

- Booking form and slot logic (Phase 4)
- Package products (Phase 5)
- Coupons (Phase 6)

## 5. Detailed functional requirements

### 5.1 Services

| ID | Requirement | Priority |
|---|---|---|
| FR-03-01 | Each service must be a WooCommerce **Simple** product marked **Virtual** (no shipping), with "HomeEase item type" = **Service**. | MUST |
| FR-03-02 | Staff must be able to **add** a service with: name, featured image, short description (≤ 200 characters), full description, category, regular price, optional sale price with schedule dates, and the custom fields in section 11.1. | MUST |
| FR-03-03 | Staff must be able to **edit** any field of a service. Changes appear on the website immediately (or after the page cache expires on live). | MUST |
| FR-03-04 | Staff must be able to **delete** a service (move to Trash). Trashed services disappear from the website. Past bookings of a trashed service still show the service name (stored on the booking, Phase 4). | MUST |
| FR-03-05 | Staff must be able to **disable** a service in two ways: (a) set it to **Draft**, which hides it completely; or (b) turn off **Booking enabled**, which keeps the page visible but replaces the booking form with "This service is temporarily unavailable for online booking. Call [he_phone] or WhatsApp us." | MUST |
| FR-03-06 | Each service must have exactly **one** service category (one of the 7). If more than one is ticked, the first one counts for slot capacity, and the admin sees a warning notice "Select only one category for a service." | MUST |
| FR-03-07 | Each service must show a **price type**: **Fixed price** (shown as "₹599") or **Visit charge** (shown as "Visit charge ₹199 – final price quoted on site"). See BR-B-15. | MUST |
| FR-03-08 | Each service must have an **estimated duration** in minutes (15–600), shown to customers rounded to a readable form: "approx. 45 min", "approx. 2 hrs", "approx. 4–5 hrs" (use the optional "Duration text" field when filled). | MUST |
| FR-03-09 | Each service must list its **service areas** (one or more). The service page shows "Available in: Zone A – City Centre, Zone B – North …". A service with no areas can't be booked and shows "Not yet available in your area". | MUST |
| FR-03-10 | Each service must have **Allowed slots** (checkboxes of the slots defined in HomeEase → Settings, Phase 4). Default: all slots. | MUST |
| FR-03-11 | Each service must have **Member discount eligible** (default Yes) and, for visit-charge services only, **Visit-charge waiver eligible** (default Yes). See BR-M-06 and BR-M-07. | MUST |
| FR-03-12 | Each service must have **What's included** and **What's not included** lists (one item per line), shown as tick and cross lists on the service page. | MUST |
| FR-03-13 | When a sale price is active, service cards and the service page must show the old price struck through and the new price, for example "~~₹699~~ ₹599". | SHOULD |
| FR-03-14 | Staff must be able to set a **Display order** number. Services within a category are listed by display order, then by name. | SHOULD |
| FR-03-15 | The standard WooCommerce **quantity box, SKU, tags, reviews tab and "Additional information" tab** must be hidden for services. | MUST |
| FR-03-16 | The default WooCommerce "Add to cart" button must be **hidden** on service pages. The booking form (Phase 4) replaces it. Until Phase 4 is done, show the placeholder "[Dynamic – Phase 4] Booking form". | MUST |

### 5.2 Categories

| ID | Requirement | Priority |
|---|---|---|
| FR-03-17 | These 7 product categories must exist with these slugs: Home Cleaning (`home-cleaning`), Plumbing Services (`plumbing`), Electrical Repairs (`electrical`), AC Repair & Maintenance (`ac-repair`), Painting Services (`painting`), Pest Control (`pest-control`), Appliance Repair (`appliance-repair`). An 8th category **Maintenance Packages** (`maintenance-packages`) is created in Phase 5 and is **never** shown in service menus or service grids. | MUST |
| FR-03-18 | Staff must be able to add, rename, reorder and delete categories, and set each category's image, icon, tagline and **slot capacity** (default 2, range 1–20). | MUST |
| FR-03-19 | Deleting a category that still has services must be prevented, or must show a clear warning. WooCommerce moves the products to "Uncategorized", so staff are trained to move the services first (owner guide, Phase 10). | SHOULD |
| FR-03-20 | Each category page (`/service-category/<slug>/`) must show: H1 category name, tagline, description, the service cards in that category, and the Packages CTA. | MUST |
| FR-03-21 | The "Services" dropdown in the main menu must list the 7 categories (linked to their category pages). | MUST |

### 5.3 Service areas

| ID | Requirement | Priority |
|---|---|---|
| FR-03-22 | A taxonomy **Service Areas** (`he_service_area`) must be attached to products, with checkbox selection (hierarchical) on the service edit screen. | MUST |
| FR-03-23 | Each area must have a name (for example "Zone A – City Centre"), a list of **6-digit PIN codes** (one per line or comma separated) and an **Active** switch. Inactive areas are hidden from the website and from the booking form. | MUST |
| FR-03-24 | Saving an area with an invalid PIN code (not exactly 6 digits) must show an admin error naming the invalid value, and the invalid value must not be saved. | SHOULD |
| FR-03-25 | The same PIN code must not appear in two active areas. If it does, show an admin warning naming both areas. | SHOULD |
| FR-03-26 | The shortcode `[he_service_areas]` must list all active area names (and optionally their PIN codes with `show_pins="yes"`). It is used on the About and Contact pages. | MUST |

### 5.4 Website display

| ID | Requirement | Priority |
|---|---|---|
| FR-03-27 | `[he_service_categories]` must show one card per service category (not Maintenance Packages) with image or icon, name, tagline, "Starting from ₹X" (the lowest current price among its published, booking-enabled services) and a link to the category page. It replaces the Home "Services overview" placeholder. | MUST |
| FR-03-28 | `[he_services category="<slug>" limit="12"]` must show service cards (Phase 2 §8.4) for one category. `[he_services_grouped]` must show every category in display order, each with its services, and anchors matching the Services page quick links. | MUST |
| FR-03-29 | Only **published** services with item type "Service" appear in these shortcodes. Drafts, trashed services and packages never appear. | MUST |
| FR-03-30 | Visiting `/shop/` or the WooCommerce product archive must **redirect (301)** to `/services/`. | MUST |
| FR-03-31 | Permalinks: product base `/service/`, product category base `service-category` (Settings → Permalinks). | MUST |
| FR-03-32 | In the admin, the "Products" menu must be labelled **Services & Packages**, with list columns for Price type, Duration, Booking enabled and Areas (count). | SHOULD |
| FR-03-33 | The product list must be filterable by category (WooCommerce built-in) so staff can see "all Plumbing services". | MUST |

## 6. Page-wise requirements

| Page | What this phase adds |
|---|---|
| Home (`/`) | Replace the "Services overview" placeholder with `[he_service_categories]` |
| Services (`/services/`) | Replace the placeholder with `[he_services_grouped]`. Quick-link anchors = category slugs. |
| Category page (`/service-category/<slug>/`) | New template (section 7.2) |
| Service details (`/service/<slug>/`) | New layout (section 7.1) |
| About Us, Contact Us | Replace the area placeholders with `[he_service_areas]` |
| Main menu | Services dropdown with the 7 category links |

## 7. Required page sections

### 7.1 Individual Service Details page

| # | Section | Content and rules |
|---|---|---|
| 1 | Breadcrumb | Home › Services › {Category} › {Service} (Kadence or Yoast breadcrumbs) |
| 2 | Service summary (top) | Left: featured image (4:3, optional gallery). Right: H1 service name, category label, **price box** (price + price type explanation), duration, "Available in" areas, short description, **Book Now** button (scrolls to the booking form). |
| 3 | Member savings box | "Members pay less: Basic 5% · Standard 10% · Premium 15% off" + "View plans" link. For visit-charge services: "Free visit charge with any plan". Shown only if Member discount eligible = Yes. The percentages are read from the package products (Phase 5). Until then, hard-code the sample values and mark them as a placeholder. |
| 4 | Booking form | **[Dynamic – Phase 4]** id `book` (anchor target for Book Now) |
| 5 | What's included / not included | Two columns (one on mobile) with ✓ and ✕ icon lists |
| 6 | Service description | Full description (headings, lists, images allowed) |
| 7 | How it works (mini) | The 4 steps (synced pattern or a short version) |
| 8 | Other services in this category | Up to 4 service cards (same category, excluding this one) |
| 9 | CTA | Synced "Contact CTA" |

**Price box examples:**

- Fixed: "**₹599** · Fixed price · includes labour for the job described".
- Visit charge: "**Visit charge ₹199** · An expert inspects and quotes the final price on site. The visit charge is adjusted in the final bill." (*sample*: the owner confirms whether it's adjusted.)

### 7.2 Service category page

| # | Section | Content |
|---|---|---|
| 1 | Hero | H1 category name, tagline, category image as background or side image |
| 2 | Description | Category description (from the category edit screen) |
| 3 | Service cards | All published services in the category, by display order |
| 4 | Packages CTA | Synced "Packages CTA" |
| 5 | FAQs (optional) | 3–4 category-specific questions (COULD) |

## 8. UI components

| Component | Where | Notes |
|---|---|---|
| Category card | Home, Services | Icon (64 px) or image, name (H3), tagline, "Starting from ₹X", arrow link. Whole card clickable. |
| Service card | Services, category, service page | Phase 2 §8.4 |
| Price box | Service page | Large price (28 px, primary dark), price type label, help text in muted colour |
| Duration chip | Cards, service page | Clock icon + "approx. 60 min" |
| Area list | Service page | Comma list. If more than 5 areas: show 5 + "and N more" (expandable). |
| Included / excluded lists | Service page | Kadence Icon List style, green ✓ and red ✕ |
| Unavailable notice | Service page | Info notice (FR-03-05 text) in place of the booking form |

## 9. Customer interactions

| # | Interaction | Expected behaviour |
|---|---|---|
| CI-1 | Customer opens Home and taps the "Plumbing Services" card | The Plumbing category page opens with all plumbing services |
| CI-2 | Customer opens `/services/` and taps the "AC Repair" quick link | The page scrolls to the AC section |
| CI-3 | Customer opens "Tap / Mixer Repair" | Sees the price, price type, duration, areas, included items and the Book Now button |
| CI-4 | Customer taps **Book Now** | The page scrolls smoothly to the booking form (Phase 4) |
| CI-5 | Customer opens a service with Booking enabled = No | Sees the unavailable notice with call and WhatsApp links. No form. |
| CI-6 | Customer opens `/shop/` | Redirected to `/services/` |

## 10. Administrator interactions

| # | Interaction | Expected behaviour |
|---|---|---|
| AI-1 | Staff add a new service "Geyser Installation" in Electrical, ₹499 fixed, 60 min, all areas | It appears on `/services/`, on the Electrical category page and in category "Starting from" (if it's the lowest price) |
| AI-2 | Staff change a service price from ₹249 to ₹299 | The new price shows on the site. Existing bookings keep the old price (stored on the order). |
| AI-3 | Staff schedule a sale price ₹199 from 1st–7th of the month | The struck-through price shows only between those dates |
| AI-4 | Staff bulk-increase all Plumbing prices by 10% | Products list → filter by Plumbing → select all → Bulk Edit → Price "Increase existing price by (fixed amount or %)" → 10% → Update |
| AI-5 | Staff turn off "Booking enabled" for AC Gas Refill | The page stays live with the unavailable notice |
| AI-6 | Staff add a new area "Zone F – Airport Road" with 3 PIN codes and tick it on 10 services | The area appears in `[he_service_areas]` and on those service pages |
| AI-7 | Staff set a category's slot capacity to 3 | Used by the Phase 4 slot logic for that category |

## 11. Required data fields

### 11.1 Service fields (SCF field group "HomeEase – Item Details", location: Post Type = Product)

| Label | Key | Type | Rules |
|---|---|---|---|
| HomeEase item type | `he_item_type` | Radio: `service` / `package` | Required. Default `service`. Controls which fields show (conditional logic). |
| Price type | `he_price_type` | Radio: `fixed` / `visit` | Required for services |
| Estimated duration (minutes) | `he_duration_minutes` | Number 15–600, step 15 | Required for services |
| Duration text (optional) | `he_duration_text` | Text, max 30 | Overrides the automatic text, for example "4–5 hrs" |
| Booking enabled | `he_booking_enabled` | True/False | Default true |
| Allowed slots | `he_allowed_slots` | Checkbox (choices loaded from settings) | At least one required when booking is enabled |
| What's included | `he_included` | Textarea (one per line) | Required, max 10 lines |
| What's not included | `he_excluded` | Textarea (one per line) | Optional, max 10 lines |
| Member discount eligible | `he_member_discount` | True/False | Default true |
| Visit-charge waiver eligible | `he_waiver_eligible` | True/False | Shown only when price type = visit. Default true. |
| Display order | `he_display_order` | Number | Default 10 |

Standard WooCommerce fields used: Product name, Description, Short description, Regular price, Sale price + schedule, Product image, Product gallery, Product categories, Service Areas (taxonomy box), Status.

### 11.2 Category fields (SCF field group on taxonomy `product_cat`)

| Label | Key | Type | Rules |
|---|---|---|---|
| Thumbnail | (WooCommerce built-in) | Image | 800 × 600 |
| Icon | `he_cat_icon` | Image (SVG/PNG) | 128 × 128, optional |
| Tagline | `he_cat_tagline` | Text, max 80 | Required for service categories |
| Slot capacity | `he_slot_capacity` | Number 1–20 | Default 2 |
| Display order | `he_cat_order` | Number | Default 10 |
| Hide from service lists | `he_cat_hidden` | True/False | True only for "Maintenance Packages" |

### 11.3 Service area fields (taxonomy `he_service_area`, term fields via SCF)

| Label | Key | Type | Rules |
|---|---|---|---|
| Area name | (term name) | Text | Required, unique |
| PIN codes | `he_pincodes` | Textarea | 6-digit numbers, comma or newline separated |
| Active | `he_area_active` | True/False | Default true |

### 11.4 Sample catalogue (owner confirms all prices and durations)

| Category | Service | Price type | Price (₹) | Duration | Allowed slots |
|---|---|---|---|---|---|
| Home Cleaning | Full Home Deep Cleaning – 1 BHK | Fixed | 2,499 | 4–5 hrs | 08–10, 10–12 |
| Home Cleaning | Full Home Deep Cleaning – 2 BHK | Fixed | 3,499 | 5–6 hrs | 08–10 |
| Home Cleaning | Bathroom Deep Cleaning (per bathroom) | Fixed | 499 | 60 min | All |
| Home Cleaning | Kitchen Deep Cleaning | Fixed | 1,299 | 2.5 hrs | 08–10, 10–12, 12–14 |
| Home Cleaning | Sofa Shampoo Cleaning (5-seater) | Fixed | 799 | 90 min | All |
| Plumbing | Plumber Visit & Inspection | Visit | 199 | 30–60 min | All |
| Plumbing | Tap / Mixer Repair or Replacement (labour) | Fixed | 249 | 45 min | All |
| Plumbing | Pipe Leakage Repair (labour) | Fixed | 399 | 60 min | All |
| Plumbing | Water Tank Cleaning (up to 1000 L) | Fixed | 899 | 2 hrs | 08–10, 10–12, 12–14 |
| Electrical | Electrician Visit & Inspection | Visit | 199 | 30–60 min | All |
| Electrical | Ceiling Fan Installation | Fixed | 249 | 45 min | All |
| Electrical | Switchboard Repair | Fixed | 199 | 30 min | All |
| AC Repair & Maintenance | AC Service – Split AC | Fixed | 599 | 60 min | All |
| AC Repair & Maintenance | AC Service – Window AC | Fixed | 499 | 60 min | All |
| AC Repair & Maintenance | AC Repair Inspection (gas/cooling issue) | Visit | 299 | 45 min | All |
| AC Repair & Maintenance | Split AC Installation | Fixed | 1,499 | 2–3 hrs | 08–10, 10–12, 12–14 |
| Painting | Painting Consultation & Site Measurement | Visit | 199 | 45 min | All |
| Painting | Wall Touch-up (up to 2 walls, labour) | Fixed | 1,499 | 4 hrs | 08–10, 10–12 |
| Pest Control | General Pest Control – 1 BHK | Fixed | 899 | 60 min | All |
| Pest Control | General Pest Control – 2 BHK | Fixed | 1,199 | 90 min | All |
| Pest Control | Termite Inspection | Visit | 299 | 45 min | All |
| Appliance Repair | Washing Machine Repair Visit | Visit | 249 | 60 min | All |
| Appliance Repair | Refrigerator Repair Visit | Visit | 249 | 60 min | All |
| Appliance Repair | RO Water Purifier Service | Fixed | 399 | 45 min | All |
| Appliance Repair | Microwave Repair Visit | Visit | 199 | 45 min | All |

**Sample service areas:** Zone A – City Centre, Zone B – North, Zone C – South, Zone D – East, Zone E – West, each with 3–6 placeholder PIN codes until the owner supplies the real list. For testing, give **Zone A** the PIN codes `400001, 400002` and keep one PIN code (`999999`) out of every area so the "not served" message can be tested.

## 12. WordPress implementation expectations

1. **SCF field groups:**
   - Create them in **SCF → Field Groups** using the keys above.
   - Turn on **Local JSON** and save it into the plugin: add a filter in HomeEase Core `add_filter( 'acf/settings/save_json', fn() => HE_PATH . 'acf-json' );` and the matching `acf/settings/load_json` filter. Field groups are then saved as files in `homeease-core/acf-json/` and travel with the plugin to staging and live.
2. **Service Areas taxonomy:** register with **SCF → Taxonomies** (or in code in `post-types.php`):
   - key `he_service_area`, hierarchical **true** (checkbox UI), attached to `product`
   - public **false**, show in admin UI **true**, show admin column **true**
   - menu label "Service Areas" under Products
3. **Dynamic slot choices:** populate `he_allowed_slots` from HomeEase settings with the `acf/load_field/name=he_allowed_slots` filter. Slot IDs stay fixed (`s1`…`s5`), and only their labels come from settings. Until Phase 4 adds the slot settings, hard-code the 5 sample slots in the helper `he_get_slots()`. Phase 4 then reads them from settings.
4. **Display code** (`includes/services.php`):
   - Shortcodes use `wc_get_products()` with `status => 'publish'`, `category => [slug]`, and a `meta_query` on `he_item_type = service`, ordered by `he_display_order`.
   - Prices use `$product->get_price_html()`, so sale prices format correctly.
   - Get service-page sections onto the single product template with WooCommerce hooks: remove `woocommerce_template_single_add_to_cart` (and meta, tabs, related products) for services, and add HomeEase sections on `woocommerce_single_product_summary` / `woocommerce_after_single_product_summary`. Don't copy WooCommerce template files unless a hook can't do the job.
   - Hide the quantity field with the `woocommerce_is_sold_individually` filter, or let Phase 4 control quantity.
5. **Redirect `/shop/`:** on `template_redirect`, if `is_shop()` or `is_post_type_archive( 'product' )`, call `wp_safe_redirect( home_url( '/services/' ), 301 ); exit;`.
6. **Admin label change:** use the `woocommerce_register_post_type_product` filter to change the menu name to "Services & Packages". Add list columns with `manage_edit-product_columns` and `manage_product_posts_custom_column`.
7. **PIN code validation on save:** hook `acf/validate_value/name=he_pincodes` and return an error string naming the first invalid value.
8. **Fresher notes:**
   - After setting the permalink bases, **save permalinks** and test a category URL and a service URL.
   - Use `get_field( 'he_duration_minutes', $product_id )` to read fields. For a taxonomy term, use `get_field( 'he_slot_capacity', 'product_cat_' . $term_id )`.
   - Keep the display code in the plugin, not in page content, so staff can't break it while editing pages.

## 13. Free plugins or custom development requirements

| Need | Solution |
|---|---|
| Services, prices, sale schedule, categories, bulk price edit | WooCommerce (free) |
| Extra service/category/area fields, area taxonomy | Secure Custom Fields (free) |
| Category/service cards, service page sections, redirect, admin columns | **Custom**: HomeEase Core `services.php` |
| Breadcrumbs | Kadence theme or Yoast SEO (free) |

## 14. Dependencies

| Needs | From |
|---|---|
| Card styles, page layouts, synced patterns | Phase 2 |
| Real service list, prices, durations, areas, PIN codes | Owner (samples until then) |
| Slot labels for "Allowed slots" | Phase 4 settings (temporary hard-coded helper until then) |
| Membership discount percentages on the service page | Phase 5 (sample text until then) |

## 15. Expected deliverables

1. The 7 service categories with images, icons, taglines and capacities.
2. Around 25 sample services from section 11.4 with images and all fields filled.
3. 5 sample service areas with PIN codes.
4. SCF field groups saved as JSON in `homeease-core/acf-json/`.
5. Shortcodes `[he_service_categories]`, `[he_services]`, `[he_services_grouped]` and `[he_service_areas]`, placed on their pages.
6. Service detail and category page layouts.
7. `/shop/` redirect.
8. Admin label and columns.
9. Main menu Services dropdown.

## 16. Acceptance criteria

| ID | Criterion |
|---|---|
| AC-03-01 | `/services/` lists all 7 categories with their published services. No package product and no draft service appears. |
| AC-03-02 | Every service card shows a name, image, price (with the "Visit charge" label where relevant), duration and Book Now. |
| AC-03-03 | A service detail page shows every section in 7.1 (booking form placeholder allowed until Phase 4). There is no quantity box, Add to cart button, reviews tab or SKU. |
| AC-03-04 | A new service created by `staff1` appears on the site without developer help, in the right category and in the right order. |
| AC-03-05 | Setting a service to Draft removes it from every list. Setting Booking enabled = No shows the unavailable notice. |
| AC-03-06 | Category "Starting from ₹X" equals the lowest current price in that category. |
| AC-03-07 | `/shop/` returns a 301 redirect to `/services/`. |
| AC-03-08 | Service URLs follow `/service/<slug>/` and category URLs `/service-category/<slug>/`. |
| AC-03-09 | Saving a service area with the PIN code `12345` shows an error, and the value isn't saved. |
| AC-03-10 | `homeease-core/acf-json/` contains the field group JSON files. |
| AC-03-11 | A scheduled sale price appears and disappears on the configured dates (test by setting today as the start date). |

## 17. Testing scenarios

| ID | Scenario | Steps | Expected result |
|---|---|---|---|
| TC-03-01 | Browse services (Scenario 1) | Home → tap "Plumbing Services" → open "Tap / Mixer Repair" | Category page and service page load with full details |
| TC-03-02 | Select a plumbing service (Scenario 2) | On "Pipe Leakage Repair", tap Book Now | Scrolls to the booking form area |
| TC-03-03 | Admin creates a new service (Scenario 14) | As staff1: add "Geyser Installation", Electrical, ₹499, fixed, 60 min, areas A–C, slots all, included list → Publish | Visible on `/services/`, the Electrical page and its own URL with correct details |
| TC-03-04 | Disable a service | Set "Sofa Shampoo Cleaning" to Draft | Gone from all lists. Its URL shows 404. |
| TC-03-05 | Booking disabled | Turn off Booking enabled on "AC Repair Inspection" | Page visible with the unavailable notice |
| TC-03-06 | Price change | Change "Switchboard Repair" to ₹229 | New price on the card and page |
| TC-03-07 | Bulk price increase | Increase all Pest Control prices by 10% with bulk edit | All 3 prices increase by 10% and display as whole rupees. Staff then round odd values (for example ₹988.90 → ₹989) by hand. |
| TC-03-08 | Invalid PIN code | Add PIN `12A456` to Zone A | Error shown, not saved |
| TC-03-09 | Shop redirect | Visit `/shop/` | Lands on `/services/` |
| TC-03-10 | Mobile layout | Open `/services/` and one service page at 360 px | Single-column cards, readable price box, no horizontal scroll |

## 18. Known limitations

- WooCommerce allows several categories per product. The business rule is one category per service (FR-03-06). This is enforced by a warning, not by blocking.
- Services are WooCommerce "products" internally. Some WooCommerce admin screens and emails use the word "product". Staff are told this in the owner guide (Phase 10).
- Per-service blocked dates aren't included. Global holidays (Phase 4) and the Booking enabled switch cover the small-business need.
- Visit-charge services take only the visit charge online. Final repair amounts are agreed and paid on site and aren't part of website payments.
