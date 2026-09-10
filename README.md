# Batra Deepak & Associates — Website

Public marketing and resource website for **Batra Deepak & Associates (BDA)**, Chartered Accountants, established 1989.

**Live site:** https://bdaca.com

---

## Table of contents

1. [Overview](#1-overview)
2. [Technology stack](#2-technology-stack)
3. [Repository structure](#3-repository-structure)
4. [Design system](#4-design-system)
5. [Shared conventions](#5-shared-conventions)
6. [Page reference](#6-page-reference)
7. [Data maintained inside the code](#7-data-maintained-inside-the-code)
8. [Third-party integrations](#8-third-party-integrations)
9. [Contact details used across the site](#9-contact-details-used-across-the-site)
10. [Assets](#10-assets)
11. [Responsive behaviour](#11-responsive-behaviour)
12. [Deployment](#12-deployment)
13. [Maintenance guide](#13-maintenance-guide)
14. [Open items and known issues](#14-open-items-and-known-issues)

---

## 1. Overview

A **7-page static website**, plus one redirect stub. Every page is a self-contained HTML file with its CSS in a `<style>` block and its JavaScript in a `<script>` block at the bottom. There is no build step, no framework, no package manager and no server-side code.

| | |
|---|---|
| Pages | 7 (+ 1 redirect stub) |
| External dependencies | 3 CDN libraries (Utilities page only), GA4 |
| Backend | None, except one Google Apps Script endpoint for the careers form |
| Build step | None — files deploy as-is |
| Fonts | Verdana throughout, no web fonts loaded |

**What the site does:**

- Presents the firm, its 6 core service lines and 12 industry specialisms
- Publishes a compliance calendar and 8 financial calculators (the most substantial feature)
- Hosts articles and briefings, and links out to 10 government/regulatory portals
- Advertises 7 open positions and accepts applications with CV upload
- Lists 10 offices across 7 cities on an interactive map
- Provides call / WhatsApp / email contact throughout

---

## 2. Technology stack

| Layer | Choice |
|---|---|
| Markup | Hand-written HTML5, one file per page |
| Styling | Vanilla CSS with custom properties (`:root` tokens), declared per file |
| Scripting | Vanilla ES6+, no framework, no bundler |
| Layout | CSS Grid and Flexbox; `position: fixed` navigation |
| Icons | Inline SVG throughout — **no emoji anywhere on the site** |
| Analytics | Google Analytics 4 — property `G-0D7XWQZYL6` |
| Forms | Google Apps Script web app (careers page only) |
| Hosting | Static file host |

---

## 3. Repository structure

```
/
├── index.html                  Home
├── industries-we-serve.html    Industries (12 sectors, searchable)
├── utilities.html              8 calculators + compliance calendar
├── career_options.html         Vacancies + application form
├── knowledge-centre.html       Articles + government portals
├── voices-of-bda.html          Employee video stories
├── connect-us.html             Offices map + contact + social
├── our-articles.html           Redirect stub → knowledge-centre.html#articles
│
└── assets/
    ├── logo.png                Firm logo — nav on all pages
    ├── hero_bg_3.jpg           Home hero photograph
    ├── monogram.jpg            Home "About" section image
    ├── india_map.jpg           Base map for the office pins
    ├── career-hero-bg.jpg      Careers hero background
    ├── connect-hero-bg.jpg     Connect Us hero background
    ├── industries-hero-bg.jpg  Industries hero background
    ├── knowledge-hero-bg.jpg   Knowledge Centre + Voices hero background
    ├── utilities-hero.jpg      Utilities hero background
    └── industries/             One photo per industry (see §10)
```

> **Note on naming:** `career_options.html` uses an underscore; every other page uses hyphens. Kept as-is so existing links and job postings do not break.

---

## 4. Design system

Every page declares the **same** `:root` block. When this is eventually extracted into a shared `brand.css`, that block is the file.

```css
:root {
  --ink:        #1B1B19;   /* headings, primary text */
  --ink-2:      #33332F;   /* body copy */
  --ink-3:      #5A5A54;   /* meta, captions */

  /* ── ACCENT — change these four lines to reskin the whole site ── */
  --accent:      #6E2436;  /* oxblood burgundy — 10.7:1 on white */
  --accent-2:    #8A3247;  /* hover / lighter */
  --accent-wash: rgba(110,36,54,.07);
  --accent-line: rgba(110,36,54,.26);

  --white:  #ffffff;
  --paper:  #F2F2EF;       /* default page background */
  --paper-2:#E7E7E2;       /* alternating band */

  --line:   rgba(27,27,25,.14);
  --line-2: rgba(27,27,25,.24);

  --pad:    20px;          /* full-bleed gutter, stepped by media query */
  --nav-h:  64px;
}
```

**Palette rationale.** Oxblood burgundy against near-black text on warm neutral greys. There is no navy, no gold and no green anywhere. The accent is dark enough (10.7:1 on white) to be used as a text colour, not only as decoration.

**The one exception** is the hiring bell, which uses a light yellow (`#FBE7A1` with `#4A3708` text, 9.3:1). That colour appears nowhere else, so it reads as a notice rather than as part of the brand.

**Typography.** Verdana throughout. No Google Fonts are loaded on any page. Body copy runs 13.5–15.5px, headings 17–30px, page titles fluid via `clamp()`.

**Layout.** Full-bleed. There is no fixed-width container; sections run edge to edge with only the `--pad` gutter, which steps 20 → 32 → 44 → 56px by media query.

> ### Important: never put `clamp()` inside a custom property
> If a browser cannot compute a value that arrives through a custom property, CSS falls back to `initial` — which for `padding` is **0**, not to whatever declaration came before. This produced a real bug (zero side gutters on every page). The gutter is now a plain length stepped by media queries, which has no such failure mode. `clamp()` is still used for font sizes, where each use is preceded by a plain fallback declaration and does not go through a variable.

---

## 5. Shared conventions

### 5.1 Navigation

Every page carries the same two-part header:

1. **Logo row** — logo on the left; hiring bell and hamburger on the right
2. **Nav strip** — a centred row of uppercase links (hidden below 980px)

Below 980px the strip is replaced by a right-side slide-in menu. The menu closes on outside click and on `Escape`. The current page carries `class="active"` and `aria-current="page"`.

**Menu order (7 items, identical on every page):**

```
Home                  index.html
Industries We Serve   industries-we-serve.html
Utilities             utilities.html
Career Options        career_options.html
Knowledge Centre      knowledge-centre.html
Voices of BDA        voices-of-bda.html
Connect Us            connect-us.html
```

### 5.2 Hiring bell

An amber "We're Hiring" pill in the right of the logo row on desktop; a floating circle at the top right on mobile, where the logo row's right corner is already taken by the hamburger.

- Links to `career_options.html` on every page; on the careers page itself it jumps to `#vacancies`
- Two animations on a 2.8s loop: the bell swings, and a ring expands and fades. The button does not move, so nothing shifts around it
- Both animations stop under `prefers-reduced-motion`
- `z-index: 900` — below the mobile slide-menu (1200), so opening the menu covers it

### 5.3 Footer

A compact single-line bar on every page: firm name on the left, a short disclaimer on the right, centred and stacked on narrow screens. The homepage keeps a fuller four-column footer above that bar (disclaimer, services, quick links, contact).

### 5.4 Typographic conventions

- **Justified prose.** Running copy is justified Word-style — `text-align: justify` with `hyphens: none`, so lines are flushed by stretching the spaces between words and no word is ever split. Applied to hero paragraphs, section descriptions and card body copy; **not** to short text in narrow columns, where justification produces gaps rather than tidy edges.
- **Icons** are inline SVG, stroke-based, 1.4–1.8px weight, inheriting `currentColor`.

### 5.5 JavaScript patterns

- **Hamburger toggle** — `classList.toggle('open')`, document-level outside-click listener, `Escape` to close, `aria-expanded` kept in sync
- **Scroll reveal** — `IntersectionObserver` adds `.visible` / `.in-view`. **Always guarded:** reveal classes start at `opacity: 0`, so if the observer is unavailable or the visitor prefers reduced motion, everything is revealed at once. Without that guard the page renders blank
- **Modals** — `openModal()` / `closeModal()`, sets `document.body.style.overflow = 'hidden'` while open. Connect Us uses the native `<dialog>` element, which handles focus trapping and `Escape` for free
- **Keyboard access** — every clickable `<div>` carries `role="button"`, `tabindex="0"` and an Enter/Space handler

---

## 6. Page reference

### 6.1 `index.html` — Home

| Section | Detail |
|---|---|
| Hero | Full-width photograph + **Book Appointment** (contact modal) and **Our Services ↓** (anchor scroll) |
| Ticker strip | Infinite marquee, 6 service names, stops under reduced motion |
| About | Values paragraph + 4 pillar cards; image with `37+ Years of Excellence` badge |
| Services | Grid of **6 clickable cards**, each opening a detail modal |
| Why Us | 2 stat cards (`37+` Years of Practice, `18` Professional Partners) |
| Footer | 4 columns — ICAI disclaimer, Our Services, Quick Links, Contact — plus the compact bar |

**Modals:** `contactModal` (Call / WhatsApp / Email) and `serviceModal` (populated from `serviceDetails`).

**The 6 services:** Management Consulting · Transaction Advisory · Audit & Assurance · Tax Advisory · Equity & Debt Finance · Corporate Law.

**ICAI disclaimer:** present in the footer of this page only.

> **The hero has no headline.** It is a photograph and two buttons. The CSS for `.hero-content`, `.hero h3` and `.hero-desc` still exists with no matching HTML — copy was there once and was deleted. This is the largest content gap on the site.

### 6.2 `industries-we-serve.html` — Industries We Serve

| Feature | Detail |
|---|---|
| Sticky filter bar | Live search box + 6 sector chips, offset from the nav height via CSS variable |
| Search | Matches the hidden `data-name` keywords **and** the visible card text |
| Results counter | Counts the cards present in the DOM — not hardcoded |
| Grid | 12 cards, each with a photo banner, name, sector tag, description, 4 service pills |
| Detail modal | Photo banner · Our Approach · Services We Offer · Typical Clients · Book Appointment |

**The 12 industries:** Banking/NBFC (finance) · Healthcare (services) · Real Estate (infra) · Transport & Logistics (services) · Manufacturing (manufacturing) · Trading & Retail (trade) · Education (services) · Startups & MSMEs (services) · Hospitality (services) · IT (services) · Agriculture (manufacturing) · NGOs & Trusts (services).

Client counts have been removed from all cards.

### 6.3 `utilities.html` — Financial Utilities

The largest page on the site. A tool-switcher grid of 8 buttons reveals one panel at a time. Deep-linkable: `utilities.html?tool=emi`.

1. **Income Tax Calculator** — a styled redirect to the official Income Tax Department calculator. Deliberate: avoids maintaining slab logic
2. **Loan Repayment Schedule Builder** — the most complex tool. Fixed EMI or Fixed Principal, moratorium support, ±5% EMI override for bank rounding, month-by-month amortisation, FY-wise summary, PDF and Excel export
3. **EMI Calculator**
4. **FD Maturity Calculator** — compounding options, TDS note
5. **SIP Calculator** — closed-form or month-by-month with step-up
6. **Compliance Calendar** — 128 entries for FY 2025-26 plus 8 recurring monthly, filterable by category and month, with branded PDF export
7. **Age Calculator**
8. **Financial Ratio Analyzer** — 10 ratios, each with formula, bank and sector benchmarks and a traffic-light verdict

### 6.4 `career_options.html` — Career Options

Hero, quick-facts bar (location, CTC range, positions), 7 vacancy cards, about banner, values strip, and an application popup.

**The 7 open roles:** Articled Assistant · Manager Direct Taxation · Manager Indirect Taxation (GST) · Manager Audit & Assurance · Manager Financial Reporting · Manager Financial Modelling & Business Advisory · Manager Corporate Compliances.

**Application form fields:** Full Name, Email, Mobile (10 digits), Qualification, Job Role, Experience, Current CTC, Resume (PDF, max 5MB). The PDF is base64-encoded and POSTed with the form fields to the Apps Script endpoint; the response drives a success or error message with a reference code (`ERR-PARSE`, `ERR-SERVER`, `ERR-NETWORK`) and a phone/email fallback.

This is the best-engineered form on the site — inline field errors, disabled submit during upload, graceful degradation.

### 6.5 `knowledge-centre.html` — Knowledge Centre

**Section 1 — Articles & compliance briefings.** 4 pieces, each a full-width document row with icon, tag, title, summary and a link to the PDF:

1. IBC (Amendment) Act, 2026 — creditors and corporate debtors *(April 2026)*
2. Reassessment under income tax — old law, new regime, 2025 Act *(September 2026)*
3. CBDT guidelines for compulsory scrutiny — FY 2026-27 *(June 2026)*
4. MSME Act — key compliances every buyer should know

**Section 2 — Important government links.** 10 portal cards: GST · Income Tax · ICAI · MCA · TRACES · IBBI · SEBI eFiling · EPFO · ICEGATE/CBIC · RBI.

The old "Recent Tax & Regulatory Updates" section has been removed, and the separate Our Articles page merged into this one.

### 6.6 `voices-of-bda.html` — Voices of BDA

Video story cards from former team members. Each shows a YouTube thumbnail, play button, name, role tag and quote, and opens a video modal.

**Modal:** loads a `youtube-nocookie.com` iframe with autoplay in a portrait 9:16 frame; above 760px the video docks left with the text to the right. Playback stops on close. Focus returns to the card that opened it.

**Currently published (3):** Mohak Tiwari (`iw_T9FogDHo`) · Ranu Agrawal (`OuXpa1K-x0A`) · CA Talsi Mahant (`YXneVnLUo24`).

### 6.7 `connect-us.html` — Connect Us

| Section | Detail |
|---|---|
| Hero | "Seven cities. One standard." |
| Interactive map | `india_map.jpg` with 7 pins, one per city. Details open on hover on pointer devices and on tap or keyboard anywhere. Cities with more than one office list them all in a single tooltip, which stops pins overlapping |
| Contact cards | Head Office · Phone/WhatsApp · Email |
| CTA | **Book Appointment** → native `<dialog>` with Call / Email / WhatsApp |
| Social | YouTube · LinkedIn · Facebook · Instagram · WhatsApp |

**The 10 offices:**

| City | Offices |
|---|---|
| Delhi | Shakarpur (**Head Office**) · Netaji Subhash Place · Laxmi Nagar |
| Raipur | Byron Bazar · Vidhan Sabha Road |
| Mumbai | BKC Annex |
| Durg | Supela |
| Jagdalpur | Moti Talab Para |
| Giridih | Buxidih Road |
| Bhopal | Bag Mugalia |

On a phone the tooltip opens as a panel across the foot of the screen, so it cannot be clipped by the edge of the map.

`AccountingService` JSON-LD with the Delhi head office is present on this page.

---

## 7. Data maintained inside the code

There is no CMS or database. All content lives in the HTML/JS and must be edited by hand.

| Data | File | Location |
|---|---|---|
| `serviceDetails` — 6 services | `index.html` | `<script>` at bottom |
| `modals` — 12 industry objects | `industries-we-serve.html` | `<script>` at bottom |
| `COMPLIANCE` — 128 entries | `utilities.html` | `const COMPLIANCE = [...]` |
| `REGULAR_MONTHLY` — 8 recurring | `utilities.html` | `const REGULAR_MONTHLY = [...]` |
| `RATIOS` — 10 definitions | `utilities.html` | `const RATIOS = {...}` |
| 7 job vacancies | `career_options.html` | `.dept-card` blocks **and** the `<select>` options — both must match |
| 10 government portals | `knowledge-centre.html` | `.portal-card` blocks |
| 4 articles | `knowledge-centre.html` | `.article-row` blocks |
| 3 video stories | `voices-of-bda.html` | `data-*` attributes on `.voice-card` |
| 10 office addresses | `connect-us.html` | `.pin` blocks with inline `style` coordinates |

---

## 8. Third-party integrations

| Service | Where | Notes |
|---|---|---|
| **Google Analytics 4** | All pages | Property `G-0D7XWQZYL6`, placed after `<meta charset>`. Pageviews only — no event tracking on calls, WhatsApp clicks or form submits. No cookie consent banner |
| **Google Apps Script** | `career_options.html` | Receives the application JSON + base64 CV. URL is exposed in client-side JS. No captcha or rate limiting |
| **jsPDF 2.5.1 + autoTable 3.8.1** | `utilities.html` | Compliance calendar and repayment schedule PDFs |
| **SheetJS 0.18.5** | `utilities.html` | Excel export of the repayment schedule |
| **YouTube (nocookie)** | `voices-of-bda.html` | Embedded iframes + thumbnails |
| **Google Drive** | `knowledge-centre.html` | Article PDFs hosted on Drive rather than on the site |
| **Google Maps** | `connect-us.html` | Deep links per office |

All three CDN scripts load from `cdnjs.cloudflare.com` in the page head, without `defer` or SRI hashes.

---

## 9. Contact details used across the site

| Channel | Value | Used on |
|---|---|---|
| Phone / WhatsApp | +91 78287 80780 | All pages |
| Email | frontdesk0.bda@gmail.com | All pages |
| Head office | S-517, 2nd Floor, Shakarpur, Delhi – 110 092 | index footer, connect-us |
| Phone (careers fallback) | +91 88393 42250 | `career_options.html` only |
| Email (careers fallback) | jeetendra.eduvistaa@gmail.com | `career_options.html` only |

The last two are inconsistent with the rest of the site — see §14.

---

## 10. Assets

All images live in `/assets/`, referenced with relative paths, so the folder must sit alongside the HTML files.

**`logo.png`** is used in the nav on every page at 44–50px height. It is also drawn onto a canvas and embedded into the compliance-calendar PDF, so it must be same-origin or CORS-enabled.

**`india_map.jpg`** is the base layer for the office pins. Pin coordinates are percentages of this image, so replacing it with a differently-cropped map will move every pin.

### Industry photos

Drop landscape photographs into `assets/industries/` named after the industry. `.jpg` is tried first, then `.png`:

```
banking-nbfc-financial-services      hospitality-tourism
healthcare-pharmaceuticals           it-technology-firms
real-estate-construction             agriculture-agro-processing
transport-logistics                  ngos-trusts-societies
manufacturing-production             education-coaching-institutes
trading-retail-wholesale             startups-msmes
```

Each becomes a 16:9 banner on its card and a banner in that industry's detail modal. Roughly 800×450 or larger works well. They are `loading="lazy"`.

**If a file is absent or misnamed**, the card falls back to a line-drawn sector icon and the modal omits the banner — nothing breaks, and photos can be added one at a time.

---

## 11. Responsive behaviour

| Breakpoint | What changes |
|---|---|
| **≤ 980px** | Nav strip hidden, hamburger + slide-menu shown. Hiring bell becomes a floating circle |
| **≤ 900px** | Home: about grid → 1 column |
| **≤ 860px** | Careers: about banner → 1 column |
| **≤ 760px** | Card grids → 1 column. Connect Us tooltips become a bottom sheet. Voices modal stacks. Footers centre |
| **≤ 420px** | Hero jump links and office action buttons go full width; portal grid → 1 column |

Gutter steps at 600px, 1000px and 1400px; vertical section padding steps at 800px and 1200px.

---

## 12. Deployment

Static files — copy the HTML files and the `assets/` folder to the web root. No build, no install, no environment variables.

**Local preview:** run any static server (`python3 -m http.server`) from the project root. A server is preferable to opening the files directly, since the compliance-calendar PDF export reads the logo through a canvas and can hit `file://` restrictions.

**After deploying:**
- Keep `our-articles.html` in place so old links and shared PDFs still resolve, or replace it with a proper 301 redirect if the host supports one
- Verify the careers form end-to-end — the Apps Script deployment must be set to "Anyone" access
- Confirm the ICAI website notification requirement has been met (site URL reported to ICAI within 30 days of launch)
- Check the hero photograph on a real phone; `hero_bg_3.jpg` has no `width`/`height` attributes, so setting the real pixel dimensions would remove the layout shift as it loads

---

## 13. Maintenance guide

### Change a colour across the whole site
Edit the four `--accent` lines in the `:root` block of each page. Nothing else references a colour directly. Extracting this block into a shared `brand.css` is the single highest-value refactor available.

### Add or change a navigation item
Edit every HTML file. Each has two copies of the menu — the desktop `.nav-strip` and the mobile `.slide-menu`.

### Publish an article
In `knowledge-centre.html`, copy an existing `.article-row` block and update the `href`, the SVG icon, `.article-tag`, `.article-date`, `<h3>` and the summary paragraph.

### Post or close a vacancy
In `career_options.html`, **two places must match**: the `.dept-card` HTML block, including the string passed to `openPopup('…')`, and the `<option>` list inside `#fld_role`. If the strings differ, the popup falls back to "Other".

### Update the compliance calendar (annually, each April)
In `utilities.html`, edit the `COMPLIANCE` array. Each entry is `{ month, date, cat, desc }` where `cat` is one of `itr`, `advtax`, `tds`, `gst`, `audit`, `mca`, `other`. Also update the `.calc-sub` heading, the PDF header string inside `downloadCompliancePDF()`, and any FY-specific text inside individual entries.

### Add an industry
In `industries-we-serve.html`: add the `.ind-card` HTML (set `data-sector`, `data-name` keywords, the `onclick` modal key, and the photo path) **and** a matching entry in the `modals` object. The results counter updates itself.

### Add an office
In `connect-us.html`: either add an office to an existing city's `.pin` tooltip, or copy a whole `.pin` block and set `left`/`top` as percentages of `india_map.jpg`. Check it does not sit within ~30px of an existing pin. Update the hero line and the map caption.

### Add a video story
In `voices-of-bda.html`: copy a `.voice-card`, set `data-video-id`, `data-name`, `data-role`, `data-experience`, and update the visible `<h3>`, `<p>` and thumbnail `src`. Portrait/Shorts videos suit the modal's crop.

---

## 14. Open items and known issues

### Content decisions pending

- **The homepage hero has no headline.** A first-time visitor's first screen says nothing about who the firm is or what it does
- **`career_options.html` rejects every non-Gmail address.** Validation is `/^[^\s@]+@gmail\.com$/i` and the label reads "Gmail ID". Applicants using Outlook, Yahoo, a current employer's address or Google Workspace on a custom domain are blocked
- **"Current CTC" is a mandatory field.** Freshers have none
- **Careers fallback contact** is a personal address for an unrelated brand and a phone number used nowhere else on the site
- **"Designed with precision — like our audit work."** in the homepage footer — catchy slogans are on ICAI's prohibited list
- **The Voices section heading is "Testimonials"**, the exact word ICAI prohibits. These are ex-employees rather than clients, but the label invites scrutiny. "Life at BDA" would do the same job
- **No documented consent** for the named individuals appearing in the video stories
- **Years of experience** appear as "37+" and "more than three decades" on the same page. "Established 1989" never goes stale
- **Confirm Shakarpur is a separate office** from the Laxmi Nagar one — both are 110092

### Technical items not yet done

- **No shared stylesheet.** The `:root` block and the nav/footer markup are duplicated across every file
- **Services and industries live in JS modals**, so that content is not indexable and cannot be linked to
- **No `sitemap.xml`, `robots.txt` or 404 page**
- **No privacy policy or consent capture**, despite collecting CVs and phone numbers — the DPDP Act 2023 requires notice and consent at the point of collection
- **No Our People / Leadership page.** The site claims 18 partners and names none of them
- **The ICAI disclaimer appears on the homepage only**
- **GA4 measures pageviews only** — no events on `tel:`, `wa.me` or `mailto:` clicks, appointment modals, form submits or calculator use
- **The careers Apps Script endpoint has no captcha, honeypot or rate limit**
- **SIP "XIRR (approx.)"** echoes the input rate rather than computing a return from the cash flows
- **The FD calculator's +0.5% senior citizen rate** is hardcoded and stated as fact, though it is bank-specific
- **The Utilities calculators use `alert()`** for validation; the careers form's inline errors are the pattern to copy
- **jsPDF, autoTable and SheetJS load on every visit** to Utilities, even for visitors who never download anything
- **No "Last reviewed" stamp** on the compliance calendar or the calculators
- **The Industries "Services" filter** returns 7 of the 12 cards, so it barely narrows anything

---

*Batra Deepak & Associates, Chartered Accountants*
