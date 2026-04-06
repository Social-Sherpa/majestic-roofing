# Native Multi-Step Quote Form - Component Specification

> This document is the single source of truth for the Social Sherpa native multi-step quote form component. It contains every design decision, behaviour rule, and the full source code. Use this to recreate the form for any new client.

---

## Overview

A native Astro multi-step lead generation form that replaces third-party form embeds (e.g. Growform). Built for Google Ads landing pages where the single goal is generating inbound enquiries. The form uses a "focus mode" pattern that removes all page distractions once the user commits to the first question.

---

## Design Decisions & Rules

### Typography
- All text uses the site's primary font (Montserrat in the roofing template)
- The main title ("Get a FREE Quote For Your Roofing Job") uses the same size as h2 headings across the site: `clamp(1.75rem, 3.5vw, 2.4rem)`
- Step 1 question text is **not bold** and uses `--text-secondary` colour (lighter, less prominent since the h2 title is above it)
- Steps 2-4 question text **is bold** (h3 elements) because the h2 title is hidden in focus mode, so the question becomes the primary heading
- Question font size: `clamp(1.2rem, 2.5vw, 1.5rem)`

### Colour Scheme
- Selected option border and radio fill: `#2a9d5c` (green) - NOT the brand colour
- Selected option background: `rgba(42,157,92,0.06)` (very light green tint)
- Radio circle unselected border: `#ccc`
- Progress bar fill: `#2a9d5c` (same green)
- Progress bar track: `--light-grey`
- Submit button: `--brand` colour (the site's primary brand colour)
- Error text: `#b91c1c` on `#fef2f2` background
- Success icon: `--green-tick`

### Option Buttons
- Styled as bordered cards, NOT native radio inputs or dropdowns
- Each option has a circular radio indicator on the left + text label
- Border: 2px solid, border-radius: 10px (rounder than the rest of the site)
- On hover: border turns green
- On selected: green border, green radio fill with white dot inside, light green background tint, subtle box-shadow
- Desktop: 2-column grid layout
- Mobile (below 640px): single column stack
- The `--stack` modifier forces single column at any screen size (used for the urgency step which only has 3 options)

### Focus Mode (Lead Commitment Pattern)
- When the user clicks ANY option on Step 1, the page enters "focus mode"
- Every section between the header and footer is instantly hidden (display: none)
- Only the header, form, footer, and mobile sticky bar remain visible
- The h2 title ("Get a FREE Quote...") is hidden via CSS `:global(body.form-focus) .nqf__title { display: none }`
- A history state is pushed so the browser back button works
- This creates psychological commitment: the user has started the process and sees no distractions

### Back Button Behaviour
- Hidden on Step 1 (no previous step to go back to)
- Visible on Steps 2-4
- On Step 2, clicking back exits focus mode entirely: all sections reappear, form resets to Step 1, all selections cleared
- On Steps 3-4, clicking back goes to the previous step (stays in focus mode)
- Browser back button (popstate) also exits focus mode
- Styled as a subtle grey text link with a chevron icon, not a prominent button

### Auto-Advance
- Clicking an option auto-advances to the next step after a 300ms delay
- On advance, the page scrolls so the form card top is visible just below the sticky header
- Uses `requestAnimationFrame` + instant `scrollTo` (no smooth behaviour) for reliable mobile support
- The scroll offset accounts for the sticky header height + 12px padding

### Form Validation (Step 4 - Contact Details)
- UK phone validation: strips non-digits, checks starts with 0 or +44, 10-11 digit pattern
- UK postcode validation: regex `^[A-Z]{1,2}\d[A-Z\d]?\s?\d[A-Z]{2}$`
- Email validation: basic regex `^[^\s@]+@[^\s@]+\.[^\s@]{2,}$`
- Inline error messages appear below each field on validation failure
- Errors clear on input (per-field)

### Submission
- POST to the `webhookUrl` prop as JSON
- Fields sent: `service`, `property`, `urgency`, `name`, `email`, `phone`, `postcode`, `source`
- `source` is always "Landing Page"
- On success: all steps hidden, success message shown (green tick + "Thanks! We'll be in touch shortly."), back button hidden, then redirect to `/thank-you/` after 1.5 seconds
- On error: red error box with fallback phone number CTA

### bfcache Handling
- When user returns to the page from the thank-you page (or switches tabs and back), the `pageshow` event with `e.persisted` resets the form completely: success/error hidden, title/progress/phone restored, focus mode exited, step 1 shown

### Roofing-Specific Questions (Current Implementation)
1. **What type of roofing service do you need?** (2x2 grid, light/non-bold question text)
   - Completely Replace Roof
   - Repair Existing Roof
   - Flat Roofing
   - Other
2. **What type of property do you have?** (2x3 grid on desktop, stacked on mobile)
   - Detached
   - Semi-Detached
   - Terraced
   - Bungalow
   - Flat / Apartment
   - Commercial
3. **How soon do you need help?** (single column stack, centred, max-width 380px)
   - ASAP
   - This Month
   - Next 2-3 Months
4. **Where should we send your quote?** (contact form)
   - Your Name *
   - Email *
   - Phone Number *
   - Postcode *
   - Submit button: "Get Your FREE Roofing Quote"

### Webhook Payload Example
```json
{
  "service": "Repair Existing Roof",
  "property": "Semi-Detached",
  "urgency": "This Month",
  "name": "John Smith",
  "email": "john@example.co.uk",
  "phone": "07700 900000",
  "postcode": "BA1 1AA",
  "source": "Landing Page"
}
```

### Props
| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `phone` | string | `'01225 985713'` | Phone number shown in "Or call us now" and error fallback |
| `webhookUrl` | string | `''` | Webhook endpoint for form submissions (e.g. GHL webhook URL) |

### Usage
```astro
<NativeQuoteForm
  phone="01225 985713"
  webhookUrl="https://services.leadconnectorhq.com/hooks/xxx/webhook-trigger/yyy"
/>
```

### CSS Variables Required
The component expects these CSS custom properties to be defined in `:root`:
- `--white`, `--off-white`, `--light-grey`, `--mid-grey`
- `--border` (option borders, input borders)
- `--text`, `--text-secondary`, `--text-light` (text colours)
- `--brand`, `--action-hover` (submit button)
- `--green-tick` (success icon)
- `--radius` (input border-radius)

### Responsive Breakpoints
- **Desktop (> 640px):** Options in 2-column grid, card padding 36px 32px
- **Mobile (< 640px):** Options in single column, card padding 28px 20px, option padding reduced

---

## Full Component Source Code

```astro
---
interface Props {
  phone?: string;
  webhookUrl?: string;
}

const { phone = '01225 985713', webhookUrl = '' } = Astro.props;
const phoneHref = `tel:${phone.replace(/\s/g, '')}`;
---

<section class="nqf" id="quote-form">
  <div class="nqf__inner container">
    <div class="nqf__card">
      <h2 class="nqf__title">Get a FREE Quote For Your Roofing Job</h2>

      <!-- Step 1: Service -->
      <div class="nqf__step nqf__step--active" data-step="1">
        <p class="nqf__question nqf__question--light">What type of roofing service do you need?</p>
        <div class="nqf__options">
          <button type="button" class="nqf__option" data-value="Completely Replace Roof">
            <span class="nqf__radio"></span>
            Completely Replace Roof
          </button>
          <button type="button" class="nqf__option" data-value="Repair Existing Roof">
            <span class="nqf__radio"></span>
            Repair Existing Roof
          </button>
          <button type="button" class="nqf__option" data-value="Flat Roofing">
            <span class="nqf__radio"></span>
            Flat Roofing
          </button>
          <button type="button" class="nqf__option" data-value="Other">
            <span class="nqf__radio"></span>
            Other
          </button>
        </div>
      </div>

      <!-- Step 2: Property -->
      <div class="nqf__step" data-step="2">
        <h3 class="nqf__question">What type of property do you have?</h3>
        <div class="nqf__options">
          <button type="button" class="nqf__option" data-value="Detached">
            <span class="nqf__radio"></span>
            Detached
          </button>
          <button type="button" class="nqf__option" data-value="Semi-Detached">
            <span class="nqf__radio"></span>
            Semi-Detached
          </button>
          <button type="button" class="nqf__option" data-value="Terraced">
            <span class="nqf__radio"></span>
            Terraced
          </button>
          <button type="button" class="nqf__option" data-value="Bungalow">
            <span class="nqf__radio"></span>
            Bungalow
          </button>
          <button type="button" class="nqf__option" data-value="Flat / Apartment">
            <span class="nqf__radio"></span>
            Flat / Apartment
          </button>
          <button type="button" class="nqf__option" data-value="Commercial">
            <span class="nqf__radio"></span>
            Commercial
          </button>
        </div>
      </div>

      <!-- Step 3: Urgency -->
      <div class="nqf__step" data-step="3">
        <h3 class="nqf__question">How soon do you need help?</h3>
        <div class="nqf__options nqf__options--stack">
          <button type="button" class="nqf__option" data-value="ASAP">
            <span class="nqf__radio"></span>
            ASAP
          </button>
          <button type="button" class="nqf__option" data-value="This Month">
            <span class="nqf__radio"></span>
            This Month
          </button>
          <button type="button" class="nqf__option" data-value="Next 2-3 Months">
            <span class="nqf__radio"></span>
            Next 2-3 Months
          </button>
        </div>
      </div>

      <!-- Step 4: Contact details -->
      <div class="nqf__step" data-step="4">
        <h3 class="nqf__question">Where should we send your quote?</h3>
        <form class="nqf__form" data-webhook-url={webhookUrl}>
          <label for="nqf-name">Your Name *</label>
          <input type="text" id="nqf-name" name="name" placeholder="Full name" required />

          <label for="nqf-email">Email *</label>
          <input type="email" id="nqf-email" name="email" placeholder="Your email address" required />
          <span class="nqf__field-error" data-error="email" hidden>Please enter a valid email address</span>

          <label for="nqf-phone">Phone Number *</label>
          <input type="tel" id="nqf-phone" name="phone" placeholder="Best number to reach you" required />
          <span class="nqf__field-error" data-error="phone" hidden>Please enter a valid UK phone number</span>

          <label for="nqf-postcode">Postcode *</label>
          <input type="text" id="nqf-postcode" name="postcode" placeholder="e.g. BA1 1AA" required />
          <span class="nqf__field-error" data-error="postcode" hidden>Please enter a valid UK postcode</span>

          <button type="submit" class="nqf__submit">
            <span class="nqf__submit-text">Get Your FREE Roofing Quote</span>
            <span class="nqf__submit-loading" hidden>Sending...</span>
          </button>
        </form>
      </div>

      <!-- Success -->
      <div class="nqf__success" hidden>
        <div class="nqf__success-icon">&#10003;</div>
        <p>Thanks! We'll be in touch shortly.</p>
      </div>

      <!-- Error -->
      <div class="nqf__error" hidden>
        <p>Something went wrong. Please call us on <a href={phoneHref}>{phone}</a>.</p>
      </div>

      <!-- Back button -->
      <button type="button" class="nqf__back" hidden>
        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="15 18 9 12 15 6"></polyline></svg>
        Back
      </button>

      <!-- Progress bar -->
      <div class="nqf__progress">
        <div class="nqf__progress-bar" style="width: 25%"></div>
      </div>

      <p class="nqf__or">Or call us now: <a href={phoneHref}><strong>{phone}</strong></a></p>
    </div>
  </div>
</section>

<style>
  .nqf {
    background: var(--white);
    padding: 0 0 32px;
  }

  .nqf__inner {
    max-width: 640px;
    text-align: center;
  }

  .nqf__card {
    background: var(--white);
    padding: 36px 32px 24px;
  }

  .nqf__title {
    font-family: 'Montserrat', sans-serif;
    font-size: clamp(1.75rem, 3.5vw, 2.4rem);
    font-weight: 700;
    line-height: 1.15;
    color: var(--text);
    margin-bottom: 16px;
  }

  :global(body.form-focus) .nqf__title {
    display: none;
  }

  .nqf__step {
    display: none;
  }

  .nqf__step--active {
    display: block;
  }

  .nqf__question {
    font-family: 'Montserrat', sans-serif;
    font-size: clamp(1.2rem, 2.5vw, 1.5rem);
    font-weight: 700;
    line-height: 1.3;
    color: var(--text);
    margin-bottom: 28px;
  }

  .nqf__question--light {
    font-weight: 400;
    color: var(--text-secondary);
  }

  .nqf__options {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 12px;
    margin-bottom: 8px;
  }

  .nqf__options--stack {
    grid-template-columns: 1fr;
    max-width: 380px;
    margin-left: auto;
    margin-right: auto;
  }

  .nqf__option {
    display: flex;
    align-items: center;
    gap: 14px;
    padding: 18px 22px;
    background: var(--white);
    border: 2px solid var(--border);
    border-radius: 10px;
    font-family: 'Montserrat', sans-serif;
    font-size: 0.92rem;
    font-weight: 500;
    color: var(--text);
    cursor: pointer;
    text-align: left;
    transition: border-color 0.2s, background 0.2s, box-shadow 0.2s;
  }

  .nqf__option:hover {
    border-color: #2a9d5c;
  }

  .nqf__option--selected {
    border-color: #2a9d5c;
    background: rgba(42,157,92,0.06);
    box-shadow: 0 0 0 1px #2a9d5c;
  }

  .nqf__radio {
    width: 22px;
    height: 22px;
    border-radius: 50%;
    border: 2px solid #ccc;
    flex-shrink: 0;
    position: relative;
    transition: border-color 0.2s;
  }

  .nqf__option:hover .nqf__radio {
    border-color: #2a9d5c;
  }

  .nqf__option--selected .nqf__radio {
    border-color: #2a9d5c;
    background: #2a9d5c;
  }

  .nqf__option--selected .nqf__radio::after {
    content: '';
    position: absolute;
    top: 4px;
    left: 4px;
    width: 10px;
    height: 10px;
    border-radius: 50%;
    background: var(--white);
  }

  /* Back button */
  .nqf__back {
    display: inline-flex;
    align-items: center;
    gap: 4px;
    background: none;
    border: none;
    font-family: 'Montserrat', sans-serif;
    font-size: 0.88rem;
    font-weight: 500;
    color: var(--text-light);
    cursor: pointer;
    padding: 8px 0;
    margin-top: 12px;
    transition: color 0.2s;
  }

  .nqf__back:hover {
    color: var(--text);
  }

  .nqf__back[hidden] {
    display: none;
  }

  /* Progress bar */
  .nqf__progress {
    height: 6px;
    background: var(--light-grey);
    border-radius: 3px;
    margin: 28px 0 16px;
    overflow: hidden;
  }

  .nqf__progress-bar {
    height: 100%;
    background: #2a9d5c;
    border-radius: 3px;
    transition: width 0.4s ease;
  }

  /* Form (step 4) */
  .nqf__form {
    text-align: left;
    max-width: 420px;
    margin: 0 auto;
  }

  .nqf__form label {
    display: block;
    font-size: 0.92rem;
    font-weight: 600;
    color: var(--text);
    margin-bottom: 5px;
  }

  .nqf__form input {
    width: 100%;
    padding: 12px 14px;
    border: 1.5px solid var(--border);
    border-radius: var(--radius);
    font-family: 'Montserrat', sans-serif;
    font-size: 0.95rem;
    margin-bottom: 14px;
    transition: border-color 0.2s, box-shadow 0.2s;
    background: var(--white);
    color: var(--text);
  }

  .nqf__form input:focus {
    outline: none;
    border-color: var(--brand);
    box-shadow: 0 0 0 3px rgba(45,52,128,0.08);
  }

  .nqf__field-error {
    display: block;
    color: #b91c1c;
    font-size: 0.78rem;
    margin-top: -8px;
    margin-bottom: 10px;
    text-align: left;
  }

  .nqf__field-error[hidden] {
    display: none;
  }

  .nqf__submit {
    width: 100%;
    padding: 15px 28px;
    background: var(--brand);
    color: var(--white);
    border: none;
    border-radius: var(--radius);
    font-family: 'Montserrat', sans-serif;
    font-size: 1rem;
    font-weight: 600;
    cursor: pointer;
    transition: background 0.2s, transform 0.2s, box-shadow 0.2s;
    margin-top: 4px;
  }

  .nqf__submit:hover {
    background: var(--action-hover);
    transform: translateY(-2px);
    box-shadow: 0 6px 20px rgba(45,52,128,0.3);
  }

  .nqf__submit:disabled {
    opacity: 0.7;
    cursor: not-allowed;
    transform: none;
    box-shadow: none;
  }

  .nqf__submit-loading[hidden] {
    display: none;
  }

  /* Or call */
  .nqf__or {
    font-size: 1rem;
    color: var(--text-secondary);
    margin-top: 0;
  }

  .nqf__or a {
    color: var(--brand);
  }

  /* Success / Error */
  .nqf__success {
    text-align: center;
    padding: 24px 0;
  }

  .nqf__success-icon {
    width: 48px;
    height: 48px;
    border-radius: 50%;
    background: var(--green-tick);
    color: var(--white);
    font-size: 1.5rem;
    display: flex;
    align-items: center;
    justify-content: center;
    margin: 0 auto 12px;
  }

  .nqf__success p {
    font-weight: 600;
    color: var(--text);
  }

  .nqf__error {
    text-align: center;
    padding: 12px;
    margin-top: 12px;
    background: #fef2f2;
    border-radius: var(--radius);
    color: #b91c1c;
    font-size: 0.85rem;
  }

  .nqf__error a {
    color: #b91c1c;
    font-weight: 700;
  }

  @media (max-width: 640px) {
    .nqf__card {
      padding: 28px 20px 20px;
    }

    .nqf__options {
      grid-template-columns: 1fr;
    }

    .nqf__option {
      padding: 16px 18px;
      font-size: 0.9rem;
    }
  }
</style>

<script>
  const card = document.querySelector('.nqf__card') as HTMLElement;
  if (card) {
    const steps = card.querySelectorAll('.nqf__step');
    const progressBar = card.querySelector('.nqf__progress-bar') as HTMLElement;
    const successEl = card.querySelector('.nqf__success') as HTMLElement;
    const errorEl = card.querySelector('.nqf__error') as HTMLElement;
    const titleEl = card.querySelector('.nqf__title') as HTMLElement;
    const progressWrap = card.querySelector('.nqf__progress') as HTMLElement;
    const orEl = card.querySelector('.nqf__or') as HTMLElement;

    const backBtn = card.querySelector('.nqf__back') as HTMLButtonElement;
    const hiddenEls: HTMLElement[] = [];

    let currentStep = 1;
    const totalSteps = 4;
    const answers: Record<string, string> = {};

    function goToStep(n: number) {
      currentStep = n;
      steps.forEach((s) => {
        const stepNum = Number((s as HTMLElement).dataset.step);
        s.classList.toggle('nqf__step--active', stepNum === n);
      });
      progressBar.style.width = `${(n / totalSteps) * 100}%`;
      backBtn.hidden = n <= 1;
    }

    function enterFocusMode() {
      if (document.body.classList.contains('form-focus')) return;
      document.body.classList.add('form-focus');
      history.pushState({ formFocus: true }, '');
      const formEl = document.querySelector('.nqf') as HTMLElement;
      document.body.querySelectorAll(':scope > *').forEach((el) => {
        if (el === formEl) return;
        if (el.classList.contains('header')) return;
        if (el.classList.contains('footer')) return;
        if (el.classList.contains('mobile-bar')) return;
        (el as HTMLElement).style.display = 'none';
        hiddenEls.push(el as HTMLElement);
      });
    }

    function exitFocusMode() {
      document.body.classList.remove('form-focus');
      hiddenEls.forEach((el) => el.style.display = '');
      hiddenEls.length = 0;
      goToStep(1);
      card.querySelectorAll('.nqf__option--selected').forEach((b) =>
        b.classList.remove('nqf__option--selected')
      );
    }

    function goBack() {
      if (currentStep <= 1) return;
      if (currentStep === 2) {
        exitFocusMode();
        return;
      }
      goToStep(currentStep - 1);
    }

    backBtn.addEventListener('click', () => {
      if (currentStep === 2) {
        exitFocusMode();
      } else {
        goBack();
      }
    });

    window.addEventListener('popstate', (e) => {
      if (document.body.classList.contains('form-focus')) {
        exitFocusMode();
      }
    });

    window.addEventListener('pageshow', (e) => {
      if (e.persisted) {
        successEl.hidden = true;
        errorEl.hidden = true;
        backBtn.hidden = true;
        titleEl.hidden = false;
        progressWrap.hidden = false;
        orEl.hidden = false;
        exitFocusMode();
      }
    });

    steps.forEach((step) => {
      const btns = step.querySelectorAll('.nqf__option');
      btns.forEach((btn) => {
        btn.addEventListener('click', () => {
          btns.forEach((b) => b.classList.remove('nqf__option--selected'));
          btn.classList.add('nqf__option--selected');

          const stepNum = Number((step as HTMLElement).dataset.step);
          const value = (btn as HTMLElement).dataset.value || '';

          if (stepNum === 1) answers.service = value;
          if (stepNum === 2) answers.property = value;
          if (stepNum === 3) answers.urgency = value;

          enterFocusMode();

          setTimeout(() => {
            if (stepNum < totalSteps) {
              goToStep(stepNum + 1);
              requestAnimationFrame(() => {
                const target = card.getBoundingClientRect().top + window.scrollY;
                const header = document.querySelector('.header') as HTMLElement;
                const offset = header ? header.offsetHeight + 12 : 0;
                window.scrollTo(0, target - offset);
              });
            }
          }, 300);
        });
      });
    });

    const form = card.querySelector('.nqf__form') as HTMLFormElement;
    if (form) {
      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]{2,}$/;
      const ukPostcodeRegex = /^[A-Z]{1,2}\d[A-Z\d]?\s?\d[A-Z]{2}$/i;

      function isValidUkPhone(val: string): boolean {
        const digits = val.replace(/[^0-9+]/g, '');
        if (digits.startsWith('+44')) {
          const num = digits.replace('+44', '0');
          return /^0[1-37]\d{8,9}$/.test(num);
        }
        return /^0[1-37]\d{8,9}$/.test(digits);
      }

      function showError(field: string) {
        const el = card.querySelector(`[data-error="${field}"]`) as HTMLElement;
        if (el) el.hidden = false;
      }

      function hideError(field: string) {
        const el = card.querySelector(`[data-error="${field}"]`) as HTMLElement;
        if (el) el.hidden = true;
      }

      ['email', 'phone', 'postcode'].forEach((name) => {
        const input = form.querySelector(`[name="${name}"]`) as HTMLInputElement;
        if (input) input.addEventListener('input', () => hideError(name));
      });

      form.addEventListener('submit', async (e) => {
        e.preventDefault();
        errorEl.hidden = true;

        const emailVal = (form.elements.namedItem('email') as HTMLInputElement).value.trim();
        const phoneVal = (form.elements.namedItem('phone') as HTMLInputElement).value.trim();
        const postcodeVal = (form.elements.namedItem('postcode') as HTMLInputElement).value.trim();
        const nameVal = (form.elements.namedItem('name') as HTMLInputElement).value.trim();

        let valid = true;

        if (!emailRegex.test(emailVal)) { showError('email'); valid = false; }
        if (!isValidUkPhone(phoneVal)) { showError('phone'); valid = false; }
        if (!ukPostcodeRegex.test(postcodeVal)) { showError('postcode'); valid = false; }
        if (!nameVal) { valid = false; }

        if (!valid) return;

        const btn = form.querySelector('.nqf__submit') as HTMLButtonElement;
        const btnText = form.querySelector('.nqf__submit-text') as HTMLElement;
        const btnLoading = form.querySelector('.nqf__submit-loading') as HTMLElement;

        btnText.hidden = true;
        btnLoading.hidden = false;
        btn.disabled = true;

        const data = {
          ...answers,
          name: nameVal,
          email: emailVal,
          phone: phoneVal,
          postcode: postcodeVal,
          source: 'Landing Page',
        };

        const webhookUrl = form.dataset.webhookUrl;

        try {
          if (webhookUrl) {
            const res = await fetch(webhookUrl, {
              method: 'POST',
              headers: { 'Content-Type': 'application/json' },
              body: JSON.stringify(data),
            });
            if (!res.ok) throw new Error('Failed');
          }

          steps.forEach((s) => s.classList.remove('nqf__step--active'));
          titleEl.hidden = true;
          progressWrap.hidden = true;
          orEl.hidden = true;
          backBtn.hidden = true;
          successEl.hidden = false;

          setTimeout(() => {
            window.location.href = '/thank-you/';
          }, 1500);
        } catch {
          errorEl.hidden = false;
        } finally {
          btnText.hidden = false;
          btnLoading.hidden = true;
          btn.disabled = false;
        }
      });
    }
  }
</script>
```
