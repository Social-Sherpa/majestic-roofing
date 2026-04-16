# Native Quote Form (NQF) — Design & Implementation Reference

> Multi-step survey form used on the Majestic Roofing landing pages. Designed to qualify roofing leads before they hit GHL, reduce friction with icon-based choices, and build trust with a credibility bar during the form journey.

---

## Why a multi-step survey instead of a single form

A single 4-field form converts, but it tells us nothing about the job. Multi-step qualification:

- **Segments the lead before it reaches the pipeline.** Service type, property type and urgency arrive with the contact, so GHL automations can route instantly (e.g. "ASAP + Detached" goes to a priority queue).
- **Lowers perceived effort.** One choice per screen feels lighter than a wall of fields — even though total input is higher.
- **Engages before asking for personal info.** Steps 1–3 are zero-risk taps. By the time we ask for email/phone the user is invested.

---

## Steps

### Step 1 — "What type of roofing service do you need?"

| Option | Icon (SVG) | data-value |
|---|---|---|
| Completely Replace Roof | `new-roof.svg` | `Completely Replace Roof` |
| Repair Existing Roof | `roof-repair.svg` | `Repair Existing Roof` |
| Flat Roofing | `flat-roof.svg` | `Flat Roofing` |
| Other | `other.svg` | `Other` |

- **Grid:** 2-column (`1fr 1fr`), both desktop and mobile.
- **Question styling:** `nqf__question--light` (font-weight 400, secondary colour) — intentionally lighter because the section heading "Get a FREE Quote For Your Roofing Job" sits directly above it and carries the visual weight.

### Step 2 — "What type of property do you have?"

| Option | Icon (SVG) | data-value |
|---|---|---|
| Detached | `detached.svg` | `Detached` |
| Semi-Detached | `semi-detached.svg` | `Semi-Detached` |
| Terraced | `terraced.svg` | `Terraced` |
| Bungalow | `bungalow.svg` | `Bungalow` |
| Flat / Apartment | `apartment-flats.svg` | `Flat / Apartment` |
| Commercial | `commercial-premises.svg` | `Commercial` |

- **Grid:** 2-column (`1fr 1fr`), giving 3 rows of 2.
- UK property types specific — these are the categories homeowners actually search with.

### Step 3 — "How soon do you need help?"

| Option | Icon (SVG) | data-value |
|---|---|---|
| ASAP | `asap.svg` | `ASAP` |
| This Month | `this-month.svg` | `This Month` |
| Next 2-3 Months | `next-month.svg` | `Next 2-3 Months` |

- **Grid:** 3-column (`repeat(3, 1fr)`) via `.nqf__options--three` modifier — all three sit in one row. Urgency is a single-axis decision, so a single row scans faster than stacking.

### Step 4 — "Where should we send your quote?"

Standard contact form. Not an icon-card step.

| Field | Type | Placeholder | Validation |
|---|---|---|---|
| Your Name * | text | Full name | Presence check only |
| Email * | email | Your email address | `/^[^\s@]+@[^\s@]+\.[^\s@]{2,}$/` |
| Phone Number * | tel | Best number to reach you | UK phone: starts 01/02/03/07, 10-11 digits; +44 prefix accepted |
| Postcode * | text | e.g. BA1 1AA | `/^[A-Z]{1,2}\d[A-Z\d]?\s?\d[A-Z]{2}$/i` |

- Submit button: "Get Your FREE Roofing Quote"
- Loading state: "Sending..."
- Error display: inline `<span>` below each field, shown on submit, cleared on input.

---

## Icon card design

All steps 1–3 use icon-on-top cards instead of radio-button rows.

**Why icons instead of radio buttons:**
- Visual recognition is faster than reading. A house silhouette communicates "Detached" before the label is processed.
- Tappable cards (bigger hit-area) outperform inline radio rows on mobile.
- Matches the style of high-converting roofing survey forms (reference screenshots in `/reference/`).

**Card sizing:**
| Property | Desktop | Mobile |
|---|---|---|
| Icon | 44 x 44 px | 38 x 38 px |
| Card min-height | 132px | 120px |
| Card padding | 24px 16px | 20px 12px |
| Label font-size | 0.92rem | 0.88rem |
| Label font-weight | 600 | 600 |

**States:**
- Default: `border: 2px solid var(--border)`, white background
- Hover: `border-color: #2a9d5c`
- Selected: `border-color: #2a9d5c`, light green background (`rgba(42,157,92,0.06)`), green ring (`box-shadow: 0 0 0 1px #2a9d5c`)

**Icons:** All SVGs from Flaticon "Basic Straight Lineal" style, stored in `src/assets/roofing/icons/`. Stroke `#000000`, no fill. Imported as Astro assets and rendered as `<img src={icon.src}>`. Cannot be recoloured via CSS (would need inline SVG for `currentColor`).

---

## Focus mode

**Why:** When the user commits to the form (clicks any Step 1 option), we strip the page down to just the form + header + trust signals. Removes distractions, scroll temptation, and competing CTAs. The user stays in a clean funnel until they submit or back out.

**Trigger:** First option click in Step 1 calls `enterFocusMode()`.

**What happens:**
1. `body.form-focus` class added.
2. `history.pushState()` called — enables browser back button to exit focus mode.
3. All direct children of `<body>` are hidden (`display: none`) except:
   - `.nqf` (the form section)
   - `.header`
   - `.mobile-bar` (mobile CTA strip)
   - `.nqf-trust-bar` (credibility footer)
4. The NQF title ("Get a FREE Quote...") is hidden via CSS (`:global(body.form-focus) .nqf__title { display: none }`).

**Exiting:**
- **Back button (step 2):** Calls `exitFocusMode()` — full page restore, form resets to Step 1, all selections cleared.
- **Back button (steps 3–4):** Goes to previous step, stays in focus mode.
- **Browser back button:** `popstate` listener calls `exitFocusMode()`.
- **bfcache restore:** `pageshow` with `e.persisted` resets everything.

---

## Trust bar (credibility footer)

Replaces the site footer during focus mode. Provides social proof and security reassurance at the moment the user is deciding whether to submit personal details.

**Three items:**
1. Clock icon — "The latest request submitted **7 minutes ago**" (hardcoded, not dynamic)
2. Location pin icon — "Serving **{location}** for **{yearsServing}+ years**" (props from page)
3. Padlock icon — "**SSL** Secure website"

**Why these three:**
- **Recency** ("7 minutes ago") creates urgency and proves the business is active — someone else just enquired.
- **Local authority** ("Serving Bath for 20+ years") reinforces the business is established and local, not a national call centre.
- **Security** ("SSL Secure") reassures users handing over phone/email that the connection is encrypted.

**Positioning:**
| Breakpoint | Position | Notes |
|---|---|---|
| Desktop (>640px) | `position: fixed; bottom: 0` | Stays visible like a footer |
| Mobile (≤640px) | `position: static` | Flows inline below the form so fields and submit button are never blocked by a sticky bar |

**Styling:**
- Background: `var(--off-white)`
- Border-top: `1px solid var(--border)`
- Icons: `22px` desktop / `18px` mobile, `color: var(--brand)`
- Text: `0.85rem` desktop / `0.72rem` mobile, `var(--text-secondary)`, bold values in `var(--text)`
- Layout: `flex; justify-content: space-around`, wraps on mobile
- Hidden by default (`display: none`), shown only when `body.form-focus` is active

---

## Webhook & submission

**Endpoint:** GoHighLevel webhook (LeadConnector), passed as `webhookUrl` prop.

**Payload (JSON POST):**
```json
{
  "service": "Completely Replace Roof",
  "property": "Semi-Detached",
  "urgency": "ASAP",
  "name": "Rob Mason",
  "email": "rob@example.co.uk",
  "phone": "07700 900000",
  "postcode": "BA1 1AA",
  "source": "Landing Page",
  "utm_source": "google",
  "utm_medium": "cpc",
  "utm_campaign": "...",
  "utm_term": "...",
  "utm_content": "...",
  "gclid": "...",
  "fbclid": "...",
  "msclkid": "...",
  "landing_page": "https://get.majesticroofers.co.uk/roofing-v2/?utm_source=google",
  "referrer": "https://google.com"
}
```

**UTM capture:** On page load, 9 URL params (`utm_source`, `utm_medium`, `utm_campaign`, `utm_term`, `utm_content`, `device`, `utm_adgroup`, `utm_adID`, `gclid`) are stored in `sessionStorage`. On submit, these are merged into the payload. `landing_page` and `referrer` are always included.

**Post-submit flow:**
- Success: show tick + "Thanks! We'll be in touch shortly." → redirect to `/thank-you/` after 1.5s.
- Failure: show error with fallback phone number link.

---

## Progress bar

- 4 steps, starting at 25% width.
- Formula: `(currentStep / 4) * 100`%.
- Track: `height: 6px; background: var(--light-grey); border-radius: 3px`.
- Fill: `background: #2a9d5c; transition: width 0.4s ease`.
- Visible on all steps. Hidden on success/error state.

---

## Headline & subheadline rules

All NQF headings follow the project-wide balanced-wrapping standard (documented in CLAUDE.md):

| Element | Font size | Weight | text-wrap |
|---|---|---|---|
| `.nqf__title` (h2) | `clamp(1.75rem, 3.5vw, 2.4rem)` | 700 | `balance` |
| `.nqf__question` (h3) | `clamp(1.2rem, 2.5vw, 1.5rem)` | 700 | `balance` |
| `.nqf__question--light` (p, step 1) | same clamp | 400 | `balance` |

On `/roofing-v2/`, `text-wrap: balance` is applied via a `<style is:global>` block (since the NQF scoped styles don't include it). The NQF inner container is widened to `900px !important` on desktop so the headline stays on one line where possible, while the option grid and form fields are constrained back to `580px` centred.

---

## Responsive breakpoints

| Breakpoint | Key NQF behaviour |
|---|---|
| Desktop (>640px) | 2-col options (3-col for urgency), 900px inner, 580px grid/form, fixed trust bar |
| Mobile (≤640px) | 2-col options (3-col for urgency), full-width inner, static trust bar, smaller icons/labels, tighter padding |

---

## File locations

| File | Purpose |
|---|---|
| `src/components/NativeQuoteForm.astro` | Component: HTML, scoped CSS, all JS logic |
| `src/assets/roofing/icons/*.svg` | All step icons (14 SVGs) |
| `src/pages/roofing-v2/index.astro` | Page using NQF with compact hero; contains CSS overrides for NQF inner width, padding, text-wrap |
| `src/pages/bath/index.astro` | Original page also using NQF (pre-icon-card version) |
| `reference/native-quote-form-spec.md` | Original spec (now outdated on several points — see below) |

---

## Spec vs live divergences

| Feature | Original spec | Live implementation |
|---|---|---|
| Props | `phone`, `webhookUrl` | Adds `location`, `yearsServing` |
| Step 3 layout | Single column (`--stack`, max-width 380px) | 3-column row (`--three`) |
| Option style | Radio-button rows | Icon-on-top cards for all 3 steps |
| Trust bar | Not specified | Fully new credibility section replacing footer in focus mode |
| UTM capture | Not specified | 9 params + landing_page + referrer captured and included in payload |
| Focus mode | Keeps `.footer` visible | Hides footer, keeps `.nqf-trust-bar` visible instead |
| Mobile trust bar | N/A | `position: static` (non-sticky) so form fields aren't blocked |
