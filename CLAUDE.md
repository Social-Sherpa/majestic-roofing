# CLAUDE.md

> This file is the single source of truth for this project. Read it fully before making any changes.

## Business

- **Client:** Majestic Services Ltd
- **Trading as:** Majestic Roofing / Majestic Property Services
- **Website:** https://get.majesticroofers.co.uk (this project — Astro landing pages for Google Ads)
- **Google listing:** "Majestic Services Ltd" (Bath)
- **Services:** New roofs / re-roofs, roof repairs, flat roofs (EPDM, fibreglass, felt, asphalt), guttering and fascias, chimney repairs and repointing, conservatory roofs, landscaping
- **Primary service area:** Bath & Somerset
- **Areas covered:** Midsomer Norton, Radstock, Frome, Trowbridge, Keynsham, Wells, Shepton Mallet, Glastonbury and surrounding villages
- **Key differentiator:** Family-run, John personally oversees every job, 20+ years experience, 15-year warranty, no hidden fees

## Owner and team

- **John** — owner, runs every project. Frequently mentioned by name in reviews. Personally quotes every job.
- **Max** — team member, mentioned in reviews for garage roof work.
- **Cam** and **James** — team members, mentioned in reviews for summer-house roof work.

## Voice and tone

- Write as the business, first person plural ("we", "our team")
- Straight-talking, local tradesman tone. Not a marketing agency voice.
- Confident but not salesy. Honest, grounded, direct.
- Never use em dashes. Use commas, full stops, or restructure the sentence.
- Never use: "we pride ourselves", "second to none", "bespoke", "competitive prices", "passionate about", "look no further"
- Short sentences. Maximum two sentences per paragraph on the landing page.
- Use real numbers: 30 years, 15-year warranty, 97+ reviews, 24/7.
- British English throughout: colour, neighbour, recognise, kerb, authorised.

## Brand and design

### Colours

- **Brand / primary (roofing pages):** #2D3480 (deep blue) — used on header, footer, final CTA band, step number circles, NQF submit button
- **Trust strip:** #0c1a8c (darker blue)
- **Action / CTA:** #2D3480 (same as brand on roofing pages)
- **Hero background:** #1a1a1a (neutral dark, NOT branded blue — deliberate design choice to keep hero neutral)
- **Body backgrounds:** White (#ffffff), off-white (#f9f9f7), light grey (#f2f1ef) — alternating sections
- **Text:** Near-black (#1a1a1a) for headings, dark grey (#4a4a4a) for body copy
- **NQF selected state:** #2a9d5c (green) for option borders and radio fills

### Design principles

- Extreme colour restraint. The brand colour appears on maybe 5% of the page surface.
- White/off-white/grey section rhythm creates separation without borders.
- Real project photography, never stock images. British houses, British weather.
- Trust signals front-loaded: review platform logos with scores visible near the top.
- Every section ends with a CTA (button, phone number, or both).
- Phone number appears at least 5 times across the page.
- Clean, professional, generous whitespace. Looks like a premium trades business, not a template.

### Headline and subheadline wrapping

All headlines (`h1`, `h2`, `h3`) and subheadlines/lead paragraphs (`.hero__subtitle`, `.section__header p`, `.final-cta__subtitle`, `.service-detail__left p`, `.testimonial-card__headline`) use `text-wrap: balance`.

Behaviour:
- If the text fits on one line in the available container width, it stays on one line.
- If it must wrap, lines are balanced so no single word is orphaned on its own line.
- Use two or three lines only when space genuinely requires it.

This is applied globally in `src/styles/global.css` and `src/styles/roofing.css`. When adding new heading/lead-paragraph classes, include them in the `text-wrap: balance` rule so the behaviour stays consistent.

### Typography

- Headings: Montserrat 700 (sans-serif)
- Body: Montserrat 400/500 (sans-serif)
- NQF title: Montserrat 700, clamp(1.75rem, 3.5vw, 2.4rem)
- Subheadlines: 20px desktop, 18px mobile

### Visual references

- **Primary reference:** Hodgson Roofing landing page (get.hodgsonroofing.com/survey-quote/) - best example of restraint, whitespace, trust presentation
- **Secondary reference:** Aventus Roofing (get.aventusroofing.co.uk/oxfordshire/) - good header structure, trust strip, testimonial layout
- **Tertiary reference:** Prestige Roofers (prestigeroofers.co.uk) - FAQ layout, form + trust signal pairing
- Screenshots of all three are in the `/reference/` directory

## SEO conventions

### Local hierarchy

Street > Town > Borough > County

- **Town** is the primary local keyword (matches how people search)
- **Borough** is structural (used in taxonomy/categories, not repeated in copy)
- **County** is covered by brand name/domain, never included at page level

### Meta titles

- Landing page: `Trusted Roofers Bath | Majestic Roofing`
- Location variants: `[Service] in [Town] | Majestic Roofing`

### Image naming

`[descriptive-subject]-[location].webp`

Alt text always includes the subject and location.

### Schema markup

- LocalBusiness on every page
- Service schema for roofing services
- FAQPage schema for the FAQ section
- Include geo coordinates for the business address

## Reviews

- **Google:** 40+ reviews (mix of roofing, landscaping, general services)
- **Checkatrade:** 40+ reviews, rated 10/10
- **Aggregate claim used on page:** "Rated 5/5 by customers" (hero), "Rated 10/10 (40+ Reviews)" (header Checkatrade badge)
- **Testimonials used on /roofing-v2/:** Rob Mason (Full Re-Roof), Lizzie McCall (Roof Repair), Jordan H (Garage Roof), Christine Thomas (Roof & Gutters), Briony Dowsett (hero — Summer House Re-Roof). All verified 5-star Google reviews.
- **Rule:** Only use real 5-star reviews. Never fabricate. Never include customer location in the review card (just first name).

## Content collections

Not applicable yet. This project is a single landing page. Content collections will be set up if/when the site expands to a full multi-page site.

## Technical

- **Framework:** Astro (static output, no SSR)
- **Styling:** Vanilla CSS with custom properties. `src/styles/global.css` for /bath/ pages, `src/styles/roofing.css` for /roofing/ and /roofing-v2/ pages (isolated, loaded via `RoofingLayout.astro`).
- **Icons (page sections):** Heroicons (raw SVGs). Solid set for trust strip, outline set for USP cards.
- **Icons (NQF form):** Flaticon "Basic Straight Lineal" style SVGs in `src/assets/roofing/icons/`. Stroke-based, `#000000`, no fill. 14 icons covering service types, property types, and urgency.
- **Forms:** Native multi-step quote form (`NativeQuoteForm.astro`). 4 steps: service type, property type, urgency, contact details. Icon-based option cards. Full design doc at `reference/native-quote-form-design.md`.
- **Form webhook:** GoHighLevel (LeadConnector): `https://services.leadconnectorhq.com/hooks/S7ZvkZk7rAVrb2ysJIR1/webhook-trigger/7402c2c7-8229-4c50-99f9-b08182ed13d7`
- **Images:** Real client photography in `src/assets/roofing/images/bath/` (optimised WebP, max 1600px, quality 82). Source originals in `01-roofing/`, `02-guttering/`, `03-chimney/`. Naming convention: `[descriptive-subject]-bath.webp`.
- **Tracking:** Google Ads tag + conversion tracking needed. UTM params captured on page load and included in webhook payload.

## Deployment

- **Hosting:** Cloudflare Pages (connected to GitHub via git integration)
- **Repository:** https://github.com/Social-Sherpa/majestic-roofing
- **Custom domain:** https://get.majesticroofers.co.uk
- **Auto-deploy:** Push to `master` triggers a Cloudflare Pages build and deploys to production automatically.
- **Build command:** `npm run build`
- **Build output directory:** `dist`
- **Local development:** `npm install` then `npm run dev` (runs on http://localhost:4321)
- **Local project path:** `/Users/christianbragg/code/Social Sherpa/majestic-roofing`

### Live pages

| Route | Layout | Hero type | Form type | Notes |
|---|---|---|---|---|
| `/bath/` | LandingLayout (global.css) | Compact hero + scroll arrow | NativeQuoteForm (NQF) | Original Bath page |
| `/roofing/` | RoofingLayout (roofing.css) | Full hero with inline form card | QuoteForm (simple 4-field) | Full-hero variant |
| `/roofing-v2/` | RoofingLayout (roofing.css) | Compact hero + scroll arrow | NativeQuoteForm (NQF) with icon cards | Current active variant — compact hero + multi-step NQF |
| `/thank-you/` | RoofingLayout (roofing.css) | None | None | Post-submission confirmation |

### Section order (`/roofing-v2/`)

1. Header (sticky, white bg, logo left, Checkatrade + phone right)
2. TrustStrip (#0c1a8c bg, 4 USPs)
3. Hero compact (dark bg with image, headline + subtitle + scroll arrow)
4. NativeQuoteForm (multi-step icon-based survey)
5. Accreditations (light grey bg, 6 logos)
6. Testimonials ("Five Star Reviews From Our Customers")
7. About (image slider left, copy right)
8. ServicesGrid ("Professional Roofing Services in Bath")
9. PlatformRatings
10. WhyChooseUs
11. RoofRepairs
12. Steps
13. NewRoofs
14. Faq
15. FinalCta
16. UspBar
17. Footer
18. MobileBar (sticky bottom, mobile only)

## Design system goals

This project is intended as a **reusable template for trade/service business landing pages**. The long-term goal is composable section components and a design token system that can be themed per client and deployed rapidly.

### Current status

The design system is partially there. CSS variables handle colours and spacing. Most components accept a `phone` prop. But many components still have hardcoded content (testimonials, services, trust strip items, platform ratings). These need refactoring into prop-driven or data-driven components.

### Design tokens (CSS variables)

Defined in `:root` in `global.css`. These are the theming layer for new clients:

- `--brand`, `--brand-light` (primary colour)
- `--action`, `--action-hover` (CTA colour)
- `--white`, `--off-white`, `--light-grey`, `--mid-grey` (backgrounds)
- `--text`, `--text-secondary`, `--text-light` (copy)
- `--gold` (stars)
- `--radius`, `--radius-lg`, `--max-width`
- `--shadow-sm`, `--shadow-md`, `--shadow-lg`

### Responsive breakpoints

Two breakpoints in `global.css`:
- **Tablet:** `max-width: 960px` (header hides review badges, shows logo + checkatrade + phone. Grids reduce columns.)
- **Mobile:** `max-width: 640px` (header: logo left, checkatrade right, phone hidden. Hero hides bullets and CTAs. Services 2x3 grid. Testimonials become auto-sliding carousel. USP bar 2x2 grid. Mobile sticky bar appears.)

## Component catalogue

### Prop-driven (configurable per client)

| Component | File | Props | Notes |
|---|---|---|---|
| Hero | `Hero.astro` | `headline`, `subtitle`, `phone`, `bullets[]` | Hero bg image hardcoded via import |
| Faq | `Faq.astro` | `faqs[]` (question/answer pairs) | Fully data-driven |
| FinalCta | `FinalCta.astro` | `phone` | Headline/subtitle hardcoded |
| Footer | `Footer.astro` | `phone` | Column content hardcoded |
| MobileBar | `MobileBar.astro` | `phone` | |

### Partially configurable (need refactoring)

| Component | File | What's hardcoded | To do |
|---|---|---|---|
| Header | `Header.astro` | Logo imports, badge images, review counts | Extract to props or config |
| ServicesGrid | `ServicesGrid.astro` | Service list with images | Accept services[] prop with image imports |
| About | `About.astro` | All copy, team image, checklist items | Extract to props |
| Testimonials | `Testimonials.astro` | All review data, avatar images, platform logos | Move to content collection or props |

### Fully hardcoded (need refactoring)

| Component | File | Notes |
|---|---|---|
| TrustStrip | `TrustStrip.astro` | Items and icons defined inline |
| PlatformRatings | `PlatformRatings.astro` | Platform data and logos inline |
| Accreditations | `Accreditations.astro` | 6 logos, greyscale with hover. Equal-width grid cells. |
| WhyChooseUs | `WhyChooseUs.astro` | USP items and icons inline |
| Steps | `Steps.astro` | Step content inline |
| UspBar | `UspBar.astro` | 4 items with icons |

### Section order (page: `/enfield/`)

1. Header (sticky, white bg, logo left, badges + phone right)
2. TrustStrip (navy bg, 4 USPs with Heroicon solid icons)
3. Hero (dark bg with background image, headline + form card)
4. Accreditations (light grey bg, 6 logos in a row with dividers, greyscale with hover colour)
5. ServicesGrid (3x2 photo cards with generated images)
6. About (image left, copy right, orange check list)
7. Testimonials (2x2 grid desktop, auto-sliding carousel on mobile with dots)
8. PlatformRatings (Google, Facebook, Checkatrade with logos and stars)
9. WhyChooseUs (2x2 USP cards with Heroicon outline icons)
10. Steps (3-step numbered process)
11. Faq (accordion, 6 questions)
12. FinalCta (brand bg, phone, buttons)
13. UspBar (4 items with icons)
14. Footer (3 columns)
15. MobileBar (sticky bottom, mobile only)

## Phone numbers

- **Primary (use everywhere):** 01225 985713 (Bath landline)

## Accreditations

- Fully insured (confirmed)
- TrustMark (PLACEHOLDER)
- NFRC member (PLACEHOLDER)

## Image assets

### Real client photography (`src/assets/roofing/images/bath/`)

All optimised to WebP (quality 82, max 1600px wide via sharp). Named `[subject]-bath.webp`.

- `hero-slate-reroof-bath.webp` — hero desktop background
- `hero-mobile-clay-tile-bath.webp` — hero mobile fallback
- `new-roof-grey-tile-bath.webp` — ServicesGrid "New Roof", NewRoofs section
- `new-roof-brown-tile-bath.webp` — Testimonials (re-roof)
- `roof-repair-ridge-tiles-bath.webp` — ServicesGrid "Roof Repairs"
- `roof-ridge-repair-bath.webp` — RoofRepairs section (in-progress ridge repair)
- `flat-roof-grp-bath.webp` — ServicesGrid "Flat Roofs", Testimonials (garage)
- `guttering-fascias-bath.webp` — ServicesGrid "Guttering", Testimonials (gutters)
- `chimney-leadwork-bath.webp` — ServicesGrid "Chimney Repairs"
- `emergency-roof-repair-bath.webp` — ServicesGrid "Emergency"
- `conservatory-roof-lantern-bath.webp` — About gallery
- `clay-tile-close-up-bath.webp` — About gallery
- `new-tile-roof-brick-house-bath.webp` — About gallery
- `gutters-blue-house-bath.webp` — About gallery
- `chimney-lead-flashing-bath.webp` — About gallery
- `majestic-logo-bath.webp` — About gallery (Majestic Property Services logo slide)

### Source originals (not directly referenced by components)
- `01-roofing/` — roofing project photos from client
- `02-guttering/` — guttering photos from client
- `03-chimney/` — chimney repair photos from client

### NQF icons (`src/assets/roofing/icons/`)

All SVGs from Flaticon "Basic Straight Lineal" style. Stroke `#000000`, no fill, consistent weight.

**Step 1 (service):** `new-roof.svg`, `roof-repair.svg`, `flat-roof.svg`, `other.svg`
**Step 2 (property):** `detached.svg`, `semi-detached.svg`, `terraced.svg`, `bungalow.svg`, `apartment-flats.svg`, `commercial-premises.svg`
**Step 3 (urgency):** `asap.svg`, `this-month.svg`, `next-month.svg`
**Unused:** `home.svg` (originally used for Detached, replaced by `detached.svg`)

### Brand logos (`src/assets/roofing/logos/`)
- `majestic-logo.png` — main header logo
- `checkatrade-approved-roofer.png`
- `google-colorful-logo.png`, `google-g-logo-colorful.jpg`
- `Facebook.png`
- Accreditation logos: `NHBC.webp`, `Constructiononline.webp`, `MasterBuilders.webp`, `CHAS.webp`, `NFRC.webp`, `Confederation-of-roofing-contractors.webp`

### Brand assets (`src/assets/brand/`)
- `Majestic Services.jpg` — full logo with deer (landscape)
- `Majestic property services 500x200.png` — header logo
- `Majestic Services Ltd - No background 500x500 px.png` — transparent variant

## Notes

- This is a Google Ads landing page. Single goal: generate inbound enquiries (calls + form submissions).
- No navigation links that take the user away from the page.
- Experience claim: "20+ years" refers to John's personal experience.
- The multi-step NQF form is now live on `/roofing-v2/`. Full design rationale in `reference/native-quote-form-design.md`.
- Real client photography has replaced all AI-generated placeholders on roofing pages.
- All images must be SVG (for icons) or WebP (for photos). Never PNG for photos.
- Do not use branded colours for hero backgrounds — use neutral dark (#1a1a1a). This was a deliberate design decision.
- Header desktop: Checkatrade logo stacks vertically (logo top, "Rated 10/10 (40+ Reviews)" below). Phone icon sits inline next to the number, "Call Us For a Free Quote" centred below. Both subheadings align on the same horizontal baseline via `align-items: flex-end` on `.header__right`.
- `worker/wrangler.toml` still references Hammerslate ALLOWED_ORIGINS — needs updating to majesticroofers domain.
- To spin up a new location page, copy `/roofing-v2/` to `/[town]/` and adjust headline, subtitle, location prop, meta title, FAQ areas.
