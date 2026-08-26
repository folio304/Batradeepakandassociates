# Batra Deepak & Associates — Website

Public marketing and resource website for **Batra Deepak & Associates (BD&A)**, Chartered Accountants, established 1989.

**Live site:** https://bdaca.com
**Build/dev host:** `https://folio304.github.io/Batradeepakandassociates/` (GitHub Pages)

---

## Table of contents

1. [Overview](#1-overview)
2. [Technology stack](#2-technology-stack)
3. [Repository structure](#3-repository-structure)
4. [Shared conventions](#4-shared-conventions)
5. [Page reference](#5-page-reference)
6. [Data maintained inside the code](#6-data-maintained-inside-the-code)
7. [Third-party integrations](#7-third-party-integrations)
8. [Contact details used across the site](#8-contact-details-used-across-the-site)
9. [Assets](#9-assets)
10. [Responsive behaviour](#10-responsive-behaviour)
11. [Deployment](#11-deployment)
12. [Maintenance guide — how to make common changes](#12-maintenance-guide--how-to-make-common-changes)
13. [Known issues](#13-known-issues)

---

## 1. Overview

An **8-page static website**. Every page is a self-contained HTML file with its CSS in a `<style>` block and its JavaScript in a `<script>` block at the bottom. There is no build step, no framework, no package manager and no server-side code.

| | |
|---|---|
| Pages | 8 |
| Total size | ~355 KB of HTML (CSS and JS inline) |
| External dependencies | 3 CDN libraries (Utilities page only), Google Fonts, GA4 |
| Backend | None, except one Google Apps Script endpoint for the careers form |
| Build step | None — files deploy as-is |

**What the site does:**

- Presents the firm, its 6 core service lines and 12 industry specialisms
- Publishes a compliance calendar and 8 financial calculators (the most substantial feature)
- Links out to 10 government/regulatory portals
- Hosts articles and tax/regulatory news
- Advertises 7 open positions and accepts applications with CV upload
- Lists 9 office locations with an interactive India map
- Provides call / WhatsApp / email contact throughout

---

## 2. Technology stack

| Layer | Choice |
|---|---|
| Markup | Hand-written HTML5, one file per page |
| Styling | Vanilla CSS with CSS custom properties (`:root` tokens), **declared separately in each file** |
| Scripting | Vanilla ES6+ JavaScript, no framework, no bundler |
| Layout | CSS Grid and Flexbox; `position: fixed`/`sticky` navigation |
| Animation | CSS keyframes + `IntersectionObserver` for scroll reveals |
| Icons | Unicode emoji (🏦 🏥 🏗️ …) and inline SVG for brand logos |
| Fonts | Verdana / Georgia system fonts; Google Fonts linked on 5 pages |
| Analytics | Google Analytics 4 — property `G-0D7XWQZYL6` |
| Forms | Google Apps Script web app (careers page only) |
| Hosting | Static file host (GitHub Pages) |

---

## 3. Repository structure

```
/
├── index.html                  Home
├── industries-we-serve.html    Industries (12 sectors, searchable)
├── utilities.html              8 calculators + compliance calendar
├── career_options.html         Vacancies + application form
├── knowledge-centre.html       Government portals + tax news
├── our-articles.html           Published articles (PDF links)
├── voices-of-bda.html          Employee video testimonials
├── connect-us.html             Offices map + contact + social
│
└── assets/
    ├── logo.png                Firm logo — used in nav on all 8 pages
    ├── hero_bg_3.jpg           Home hero photograph
    ├── monogram.jpg            Home "About" section image
    ├── india_map.jpg           Base map for the office pins
    ├── career-hero-bg.jpg      Careers hero background
    ├── connect-hero-bg.jpg     Connect Us hero background
    ├── industries-hero-bg.jpg  Industries hero background
    ├── knowledge-hero-bg.jpg   Shared by Knowledge Centre, Articles, Voices
    └── utilities-hero.jpg      Utilities hero background
```

> **Note on naming:** `career_options.html` uses an underscore; every other page uses hyphens.

---

## 4. Shared conventions

### 4.1 Navigation

Every page carries the same two-part header:

1. **Logo row** — the firm logo on the left, a hamburger button on the right (hamburger hidden above 900px)
2. **Nav strip** — a centred horizontal row of uppercase links (hidden below 900px)

Below 900px the strip is replaced by a **right-side slide-in menu** with numbered circular badges (`01`–`07`). The menu closes on outside click. The current page carries `class="active"` which turns the link gold.

**Standard menu order:**

```
01 Home                  index.html
02 Industries We Serve   industries-we-serve.html
03 Utilities             utilities.html
04 Career Options        career_options.html
05 Knowledge Centre      knowledge-centre.html
06 Our Articles          our-articles.html
07 Connect Us            connect-us.html
```

`voices-of-bda.html` carries an 8-item variant that inserts **Voices of BD&A** at position 07 and moves Connect Us to 08. **The other seven pages do not include this link.**

### 4.2 Design tokens

Each page declares its own `:root` block. The variable *names* are shared; the *values* are not.

| Page | `--navy` | `--gold` | Body font | Nav height |
|---|---|---|---|---|
| `index.html` | `#000000` | `#B8923A` | Verdana | 80px |
| `industries-we-serve.html` | `#1a2744` | `#b8972a` | Inter | 80px |
| `career_options.html` | `#1B2B4B` | `#B8923A` | Verdana | 80px |
| `utilities.html` | `#1a2744` | `#b8962e` | Georgia (serif) | 64px |
| `connect-us.html` | `#1a2744` | `#b8962e` | Georgia (serif) | 64px |
| `knowledge-centre.html` | `#0d1a36` | `#b8954a` | Verdana | 64px |
| `our-articles.html` | `#0d1a36` | `#b8954a` | Verdana | 64px |
| `voices-of-bda.html` | `#0d1a36` | `#b8954a` | Verdana | 64px |

Common additional tokens: `--white`, `--cream` / `--bg` / `--offwhite`, `--border`, `--muted`, `--radius`.

**Visual language shared across all pages:**

- Navy + gold palette, gold used for accents, eyebrows, rules and hover states
- 48px gold divider rule under section titles
- Uppercase letter-spaced "eyebrow" labels above headings
- Cards with 1px borders, small shadows, lift-on-hover (`translateY(-2px … -6px)`)
- Modal overlays with backdrop blur, closed by ✕ button, outside click, or `Escape`

### 4.3 JavaScript patterns

Repeated across pages:

- **Hamburger toggle** — `classList.toggle('open')` on button and menu, plus a document-level click listener that closes on outside click
- **Nav scroll shadow** — `nav.classList.toggle('scrolled', window.scrollY > 20)`
- **Scroll reveal** — `IntersectionObserver` at threshold `0.12`–`0.15` adds a `visible` / `in-view` class; staggered by index
- **Modals** — `openModal(id)` / `closeModal(id)`, sets `document.body.style.overflow = 'hidden'` while open

---

## 5. Page reference

### 5.1 `index.html` — Home

**Title:** `Batra Deepak & Associates | Chartered Accountants`

| Section | Detail |
|---|---|
| Hero | Full-width photograph (`hero_bg_3.jpg`) + two buttons: **Book Appointment** (opens contact modal) and **Our Services ↓** (anchor scroll) |
| Ticker strip | Infinite marquee, 25s loop, 6 service names repeated twice |
| About | Two-column: values paragraph + 4 "pillar" cards (Integrity & Ethics, Collaborative Excellence, Insight-Driven Approach, People & Community Focus); image with `37+ Years of Excellence` badge |
| Services | 3-column grid of **6 clickable cards**, each opening a detail modal |
| Why Us | "The journey of Integrity since 1989" + 2 stat cards (`37+` Years of Practice, `18` Professional Partners) |
| Footer | 4 columns — ICAI disclaimer, Our Services (modal links), Quick Links, Contact |

**Modals:** `contactModal` (Call / WhatsApp / Email) and `serviceModal` (populated from the `serviceDetails` object).

**The 6 services** — each has a title, description and a bullet list rendered into the modal:

1. Management Consulting (9 items — incl. IPO migration assistance, ESG advisory, M&A integration, valuation)
2. Transaction Advisory (6 items)
3. Audit & Assurance Service (8 items — statutory, tax, internal, process, social, forensic, ASM)
4. Tax Advisory (6 items — incl. appeals before CIT/ITAT/High Court)
5. Equity & Debt Finance (6 items — CMA data, syndication, project finance)
6. Corporate Law (10 items — incorporation through mergers and ROC compliance)

**ICAI disclaimer:** present in the footer of this page only.

---

### 5.2 `industries-we-serve.html` — Industries We Serve

**Title:** `Industries We Serve | BD&A – Batra Deepak & Associates`

| Feature | Detail |
|---|---|
| Sticky filter bar | Sits below the nav at `top: 116px`; contains a live search box and 6 sector chips |
| Search | `oninput` filters cards against a `data-name` keyword string on each card |
| Sector chips | All Sectors · Manufacturing · Services · Trade & Retail · Infrastructure · Finance; matched against `data-sector` |
| Results counter | "Showing *n* industries" |
| Grid | 12 cards, `auto-fill minmax(360px, 1fr)` |
| Card contents | Emoji icon, industry name, sector tag, description, 4 "key service" pills, "Know More" button, client count |
| Empty state | "No industries found for *x*" with the searched term echoed back |
| Detail modal | Our Approach (paragraph) · Services We Offer (7–8 pills, first two highlighted) · Typical Clients (paragraph) · "Book a Consultation" button |
| Contact popup | Second-level overlay (z-index 3000) with Call / WhatsApp / Email |

**The 12 industries** (with `data-sector`):

| # | Industry | Sector | Modal key |
|---|---|---|---|
| 1 | Banking, NBFC & Financial Services | finance | `finserv` |
| 2 | Healthcare & Pharmaceuticals | services | `health` |
| 3 | Real Estate & Construction | infra | `realestate` |
| 4 | Transport & Logistics | services | `logistics` |
| 5 | Manufacturing & Production | manufacturing | `mfg` |
| 6 | Trading, Retail & Wholesale | trade | `trading` |
| 7 | Education & Coaching Institutes | services | `edu` |
| 8 | Startups & MSMEs | services | `startup` |
| 9 | Hospitality & Tourism | services | `hotel` |
| 10 | IT & Technology Firms | services | `it` |
| 11 | Agriculture & Agro-Processing | manufacturing | `agri` |
| 12 | NGOs, Trusts & Societies | services | `ngo` |

---

### 5.3 `utilities.html` — Financial Utilities

**Title:** `Utilities — Batra Deepak & Associates`
**The largest page on the site (~120 KB).** A tool-switcher grid of 8 buttons reveals one panel at a time.

Deep-linkable: `utilities.html?tool=emi` opens a specific tool on load.

#### Tool 1 — Income Tax Calculator
Not a calculator. A styled redirect card pointing to the official Income Tax Department calculator at `incometaxindia.gov.in/income-tax-calculator`. Deliberate choice — avoids maintaining slab logic.

#### Tool 2 — Loan Repayment Schedule Builder
The most complex tool on the site.

- **Inputs:** loan amount, interest rate, disbursement date, tenure (months), moratorium (months), repayment mode
- **Two modes:** Fixed EMI or Fixed Principal
- **Moratorium support:** interest-only rows during the moratorium, principal repayment starts after
- **EMI confirmation modal:** shows the computed EMI and lets the user override it by up to ±5% (banks often round EMIs); the final instalment absorbs the difference
- **Outputs:** summary strip, full month-by-month amortisation table (opening balance, payable, principal, interest, closing balance) with sticky headers and a totals row, plus a **financial-year-wise summary** table
- **Exports:** branded PDF (jsPDF + autoTable) and Excel (SheetJS)
- Interest computed on reducing monthly balance

#### Tool 3 — EMI Calculator
Inputs: principal, annual rate, tenure in years, processing fee %. Outputs: monthly EMI, total payable, total interest, processing fee, effective total cost, interest-to-principal ratio.

#### Tool 4 — FD Maturity Calculator
Inputs: principal, rate, tenure, compounding frequency (quarterly/monthly/half-yearly/annual), investor type. Senior citizen adds a hardcoded +0.5%. Outputs: principal, interest earned, maturity amount, effective annual yield. Includes a TDS note (₹40,000 / ₹50,000 thresholds, Form 15G/15H).

#### Tool 5 — SIP Calculator
Inputs: monthly amount, expected annual return, period, optional annual step-up %. Two calculation paths — closed-form future-value formula when step-up is 0, month-by-month loop when it isn't. Outputs: total invested, estimated returns, total corpus, wealth multiplier, XIRR (approx.).

#### Tool 6 — Compliance Calendar
- **128 compliance entries** for FY 2025-26 / AY 2026-27, plus **8 recurring monthly compliances** in a separate table
- Two filters: category (7 options) and month (12 options), applied together
- Rendered as month cards in financial-year order (April → March), each entry colour-tagged by category
- **PDF export** with a full branded layout: navy header bar, logo drawn onto a canvas and embedded, firm name, applied filters, paginated table with repeating headers and page numbers

**Categories:** ITR Filing · Advance Tax · TDS/TCS · GST · Audit Reports · MCA/ROC · Other Compliances

**Coverage includes:** ITR deadlines by form and audit status, all four advance-tax instalments, monthly TDS/TCS deposits and quarterly returns (24Q/26Q/27Q), SFT Form 61A, MCA/ROC filings for Pvt Ltd, Public Ltd and LLP (AOC-4, MGT-7/7A, ADT-1, DPT-3, BEN-2, MSME Form-I, LLP Form 8/11), SEBI LODR items, GST monthly (GSTR-1, GSTR-3B), GST QRMP (quarterly + PMT-06), GSTR-5/5A/6/7/8, annual GSTR-9/9C/4, NBFC supervisory returns (NBS series), SEBI AIF quarterly reporting, PMLA cross-border transfers, SEZ/STPI SERF, FLA Return under FEMA, and cost audit.

#### Tool 7 — Age Calculator
Date of birth and an "as of" date → age in years, months and days.

#### Tool 8 — Financial Ratio Analyzer
**10 ratios**, each with its own input fields, formula, benchmark set and traffic-light verdict:

| Ratio | Formula |
|---|---|
| DSCR | (PAT + Depreciation + Interest) ÷ (Principal + Interest) |
| Interest Coverage Ratio | EBIT ÷ Interest Expense |
| Debt-Equity Ratio | Total Debt ÷ Net Worth |
| TOL / TNW | Total Outside Liabilities ÷ Total Net Worth |
| Current Ratio | Current Assets ÷ Current Liabilities |
| EBITDA Margin | (EBITDA ÷ Net Revenue) × 100 |
| ROCE | (EBIT ÷ Capital Employed) × 100 |
| Fixed Asset Coverage Ratio | Net Fixed Assets ÷ Outstanding Term Loan |
| Net Working Capital | Current Assets − Current Liabilities |
| Inventory & Debtor Holding | DIO = (Inventory ÷ COGS) × 365; DSO = (Debtors ÷ Sales) × 365 |

Each result shows a **verdict badge** (green / amber / red), a **bank benchmark note** (e.g. Tandon Committee 1.33:1 for current ratio, PSU bank DSCR ≥ 1.50x, TOL/TNW cap of 3:1) and a **sector benchmark note**. Genuinely useful for credit appraisal discussions.

---

### 5.4 `career_options.html` — Career Options

**Title:** `Career Options — Batra Deepak & Associates`

| Section | Detail |
|---|---|
| Hero | "We Are Hiring!" over `career-hero-bg.jpg` |
| Quick facts bar | 3 cards — Location (Raipur, Chhattisgarh), CTC Range (₹4.5 – ₹12 LPA), Positions |
| Vacancies | 7 role cards, each with "Hiring Now" badge, role tags, description, eligibility line, location/qualification/type meta, and an **Apply Now** button |
| Eligibility & skills | Grid of criteria cards and skill pills |
| About / Grow banner | Firm blurb + "Grow Your Career. Make An Impact." |
| Values strip | Integrity · Excellence · Trust |

**The 7 open roles:**

1. Articled Assistant — Finance, Audit, Tax, Litigation & Corporate
2. Manager — Direct Taxation
3. Manager — Indirect Taxation (GST)
4. Manager — Audit & Assurance
5. Manager — Financial Reporting
6. Manager — Financial Modelling & Business Advisory
7. Manager — Corporate Compliances

**Application form** (opens in a popup, pre-selects the role that was clicked):

| Field | Type | Validation |
|---|---|---|
| Full Name | text | Required, non-empty |
| Gmail ID | email | Required, **must match `@gmail.com`** |
| Mobile No. | tel | Required, exactly 10 digits |
| Qualification | text | Required |
| Job Role | select | Required; 7 roles + "Other" |
| Work Experience (Years) | number | Required, ≥ 0, step 0.5 |
| Current CTC | text | Required |
| Resume | file | Required, PDF only, max 5 MB |

**Submission flow:** the PDF is read via `FileReader` into base64, packed into a JSON payload with the form fields, and `POST`ed to the Google Apps Script web app. The script's JSON response is parsed; success shows a green confirmation and auto-closes after 2.5s, failure shows a red message with an error reference code (`ERR-PARSE`, `ERR-SERVER`, `ERR-NETWORK`) and a fallback phone/email.

This is the best-engineered form on the site — inline field errors, disabled submit during upload, and graceful degradation.

---

### 5.5 `knowledge-centre.html` — Knowledge Centre

**Title:** `Knowledge Centre – BD&A`

**Section 1 — Important Government Links.** 10 portal cards, each opening in a new tab:

| Portal | URL |
|---|---|
| GST Portal | gst.gov.in |
| Income Tax Portal | incometax.gov.in |
| ICAI Portal | icai.org |
| MCA Portal | mca.gov.in |
| TRACES Portal | tds.gov.in |
| IBBI Portal | ibbi.gov.in |
| SEBI eFiling | efiling.sebi.gov.in |
| EPFO Portal | epfindia.gov.in |
| ICEGATE / CBIC | eportal.cbic.gov.in |
| RBI Portal | rbi.org.in |

**Section 2 — Recent Tax & Regulatory Updates.** 6 news cards dated June 2026, colour-tagged by category, covering: the new Income Tax Act 2025 in force, ITR filing for AY 2026-27, CBDT compulsory scrutiny guidelines, Income Tax Rules 2026, GST 2.0 slabs (5% / 18% / 40%), and changes to ITR-1/2/4.

Five of the six link to external sites (cleartax.in, caclubindia.com, saginfotech.com); one links to a Google Drive PDF.

---

### 5.6 `our-articles.html` — Our Articles

**Title:** `Our Articles – BD&A`

A vertical list of article cards with scroll-reveal animation. Each card: gradient icon tile, category tag, date label, headline, summary paragraph, "Read Full Article ↗" button. Cards link to **Google Drive PDFs**, opening in a new tab.

**Currently published (2):**

1. **MSME Act — Key Compliances Every Buyer Should Know** — Section 15 payment obligations, Section 16 interest on delay, Section 22 disclosures, Schedule III ageing, Section 43B deduction link
2. **CBDT Guidelines for Compulsory Scrutiny Selection of ITRs — FY 2026-27** — the six mandatory categories and the 30 June 2026 deadline for Section 143(2) notices

Footer note: "More articles from our team will be added here as they're published."

---

### 5.7 `voices-of-bda.html` — Voices of BD&A

**Title:** `Voices of BD&A – BD&A`

Video testimonial cards from former team members. Each card shows a YouTube thumbnail (pulled from `img.youtube.com/vi/{id}/hqdefault.jpg`), a gold-ringed play button, name, role tag and quote, and opens a **video modal** on click.

**Modal behaviour:** loads a `youtube-nocookie.com` iframe with autoplay. Responsive layout — on mobile a 4:5 cropped window with the iframe oversized and centred so YouTube Shorts letterboxing is cropped out; on tablet/desktop (≥701px) the video docks left in a 9:16 column with the name and quote to the right. The quote renders in the **Caveat** handwriting font. Playback stops on close by clearing the iframe.

**Currently published (2):** Mohak Tiwari (`iw_T9FogDHo`), Ranu Agrawal (`0F9NViANva8`).

> ⚠️ Both cards currently contain **placeholder text** — "Replace this with [name]'s actual testimonial text…". This is live.

> ⚠️ This page is **not linked from the navigation on any other page.**

---

### 5.8 `connect-us.html` — Connect Us

**Title:** `Connect Us — Batra Deepak & Associates`

| Section | Detail |
|---|---|
| Hero | "Connect With Us" — "Nine offices. One standard." |
| Interactive map | `india_map.jpg` with 9 absolutely-positioned pins at percentage coordinates; **hover** shows a tooltip with office name, address, click-to-call phone and a Google Maps link |
| Contact cards | 3 cards — Head Office (Delhi), Phone/WhatsApp, Email |
| Social | 6 pills with brand-coloured inline SVG icons |
| CTA | "Book a Free Consultation" → appointment popup with Call / Email / WhatsApp (WhatsApp link carries a pre-filled message) |

**The 9 office pins:**

| Office | Address | Pin (left/top) |
|---|---|---|
| Raipur — Office 1 | 1st Floor, Ashirwad Bhawan, Byron Bazar, Raipur 492001 | 51% / 52% |
| Raipur — Office 2 | Office No. 28, 2nd Floor, Ashoka Plaza Complex, Vidhan Sabha Road, Raipur 492001 | 52% / 52% |
| Delhi — Office 1 | 904, Aggarwal Millennium Tower-1, Netaji Subhash Place, Delhi 110034 | 36% / 33% |
| Delhi — Office 2 | 1-B 1/17, Lalita Park, Laxmi Nagar, Vikas Marg, Delhi 110092 | 35% / 31% |
| Mumbai | A 219, Kanakia Zillion, SCLR & LBS Road, BKC Annex, Mumbai 400070 | 24% / 61% |
| Durg | 303-306, Chauhan Towers, G E Road, Supela, Durg 490001 | 49% / 50% |
| Jagdalpur | Moti Talab Para, Rammaiyya Ward, Jagdalpur 494004 | 48% / 60% |
| Giridih | 1st Floor, Near Khalsa Lodge, Buxidih Road, Giridih 815301 | 63% / 45% |
| Bhopal | Plot No. 2, Khasra 398/1/6, Bag Mugalia, Bhopal 462043 | 37% / 48% |

**Social links:** YouTube (`@batradeepak`) · LinkedIn (Deepak Batra profile) · Facebook (`/batradeepak`) · Instagram (`@batradeepak`) · Naukri.com (job listing) · WhatsApp (`wa.me/917828780780`)

---

## 6. Data maintained inside the code

There is no CMS or database. All content lives in the HTML/JS and must be edited by hand.

| Data | File | Location |
|---|---|---|
| `serviceDetails` — 6 services with bullet lists | `index.html` | `<script>` at bottom |
| `modals` — 12 industry detail objects | `industries-we-serve.html` | `<script>` at bottom |
| `COMPLIANCE` — 128 compliance entries | `utilities.html` | `const COMPLIANCE = [...]` |
| `REGULAR_MONTHLY` — 8 recurring compliances | `utilities.html` | `const REGULAR_MONTHLY = [...]` |
| `RATIOS` — 10 ratio definitions with benchmarks | `utilities.html` | `const RATIOS = {...}` |
| 7 job vacancies | `career_options.html` | HTML `.dept-card` blocks **and** the `<select>` options — both must be updated |
| 10 government portals | `knowledge-centre.html` | HTML `.portal-card` blocks |
| 6 news items | `knowledge-centre.html` | HTML `.news-card` blocks |
| 2 articles | `our-articles.html` | HTML `.article-card` blocks |
| 2 video testimonials | `voices-of-bda.html` | HTML `data-*` attributes on `.voice-card` |
| 9 office addresses | `connect-us.html` | HTML `.pin` blocks with inline `style` coordinates |

---

## 7. Third-party integrations

| Service | Where | Notes |
|---|---|---|
| **Google Analytics 4** | All 8 pages | Property `G-0D7XWQZYL6`. Tag is placed *before* `<meta charset>`. Pageviews only — no event tracking on calls, WhatsApp clicks or form submits. No cookie consent banner. |
| **Google Apps Script** | `career_options.html` | Web app endpoint receives the application JSON + base64 CV. URL is exposed in client-side JS. No captcha or rate limiting. |
| **Google Fonts** | index, industries, knowledge-centre, our-articles, voices | Playfair Display, Inter, Caveat. Only Caveat (on the Voices modal) and Inter (on Industries) are actually applied — the rest load unused. |
| **jsPDF 2.5.1** | `utilities.html` | Loaded in `<head>`; used for the compliance calendar and repayment schedule PDFs |
| **jspdf-autotable 3.8.1** | `utilities.html` | Table rendering inside the PDFs |
| **SheetJS (xlsx) 0.18.5** | `utilities.html` | Excel export of the repayment schedule |
| **YouTube (nocookie)** | `voices-of-bda.html` | Embedded iframes + thumbnail images |
| **Google Drive** | our-articles, knowledge-centre | Article PDFs hosted on Drive rather than on the site |
| **Google Maps** | `connect-us.html` | Deep links per office (`maps.app.goo.gl` short links and query URLs) |

All three CDN scripts load from `cdnjs.cloudflare.com` in the page head, without `defer` or SRI hashes.

---

## 8. Contact details used across the site

> These are **not consistent** across pages. Documented as-is.

| Channel | Value | Used on |
|---|---|---|
| Phone / WhatsApp | +91 78287 80780 | index, connect-us, industries — and all 9 map pins |
| Phone (careers) | +91 88393 42250 | career_options only |
| Email | frontdesk0.bda@gmail.com | index, connect-us |
| Email | info@batradeepak.com | industries-we-serve |
| Email | jeetendra.eduvistaa@gmail.com | career_options |
| Head office (contact card) | S-517, 2nd Floor, Shakarpur, Delhi 110092 | index footer, connect-us |
| Head office (map tooltip) | Ashirwad Bhawan, Byron Bazar, Raipur | connect-us |

---

## 9. Assets

All images live in `/assets/`. They are referenced with relative paths, so the folder must sit alongside the HTML files.

- **`logo.png`** — used in the nav on all 8 pages at 44px or 50px height. Also drawn onto a canvas and embedded into the compliance-calendar PDF, so it must be same-origin or CORS-enabled.
- **Hero backgrounds** — applied via CSS `background-image`, most with a white or navy gradient overlay for text legibility.
- **`india_map.jpg`** — the base layer for the office pins. **Pin coordinates are percentages of this image**, so replacing it with a differently-cropped map will move every pin.
- **`monogram.jpg`** — has an `onerror` fallback that reveals a "BDA" text monogram if the file is missing.

No `width`/`height` attributes and no `loading="lazy"` except on the YouTube thumbnails.

---

## 10. Responsive behaviour

| Breakpoint | What changes |
|---|---|
| **≤ 900px** | Nav strip hidden, hamburger + slide-menu shown. Nav height drops 80px → 64px on some pages. Hero paddings reduce. Home: about grid → 1 column, services → 2 columns, hero stats hidden. |
| **≤ 700px** | Voices modal switches from side-by-side (video left, text right) to stacked with a 4:5 cropped video. |
| **≤ 640px** | Industries grid → 1 column. Utilities tool grid → 3 fixed columns, panel padding reduces. |
| **≤ 600px** | Home services and why-grid → 1 column, footer → 1 column. Careers: facts, eligibility, contact and values grids all → 1 column. Articles cards stack vertically. |
| **≤ 520px** | Utilities ratio input grids → 1 column. |
| `@media (hover: none)` | Utilities tool tooltips are hidden entirely on touch devices. |

**Known mobile gap:** the Connect Us office tooltips are triggered by `:hover` only, so office details are unreachable on touch devices.

---

## 11. Deployment

Static files — copy the eight HTML files and the `assets/` folder to the web root. No build, no install, no environment variables.

**Local preview:** open `index.html` directly, or run any static server (`python3 -m http.server`) from the project root. A server is preferable, since the compliance-calendar PDF export reads the logo through a canvas and can hit `file://` restrictions.

**After deploying:**
- Confirm the ICAI website notification requirement has been met (site URL must be reported to ICAI within 30 days of launch)
- Verify the careers form end-to-end — the Apps Script deployment must be set to "Anyone" access

---

## 12. Maintenance guide — how to make common changes

### Add or change a navigation item
Edit **all 8 files**. Each has two copies of the menu — the desktop `.nav-strip` and the mobile `.slide-menu` — so that is **16 edits**. Renumber the `01`–`0n` badges in the slide menus.

### Publish an article
In `our-articles.html`, copy an existing `.article-card` block and update the `href`, icon emoji, `.article-tag`, `.article-date`, `<h3>` and description. Cards animate in automatically via the existing `IntersectionObserver`.

### Add a news item
In `knowledge-centre.html`, copy a `.news-card` block. Category colour comes from the class on `.news-category` — available classes are `cat-gst`, `cat-itr`, `cat-mca`, `cat-ita`, `cat-tds`. Update the "Curated news from …" line in the section description.

### Post or close a vacancy
In `career_options.html`, **two places must match**:
1. The `.dept-card` HTML block, including the string passed to `openPopup('…')`
2. The `<option>` list inside `#fld_role`

If the strings differ, the popup falls back to selecting "Other".

### Update the compliance calendar (annually, each April)
In `utilities.html`, edit the `COMPLIANCE` array. Each entry is `{ month, date, cat, desc }` where `month` is a 3-letter code and `cat` is one of `itr`, `advtax`, `tds`, `gst`, `audit`, `mca`, `other`. Also update:
- The `.calc-sub` heading ("FY 2025–26 | AY 2026–27")
- The PDF header string inside `downloadCompliancePDF()`
- Any FY-specific text inside individual entries (e.g. "Q1 (Apr–Jun 2025)")

### Add an industry
In `industries-we-serve.html`: add the `.ind-card` HTML (set `data-sector`, `data-name` keywords and the `onclick` modal key) **and** a matching entry in the `modals` object. Update the hardcoded count in `#countSpan`.

### Add a service
In `index.html`: add a `.service-card` with an `onclick` key, add the matching entry to `serviceDetails`, and add a `.footer-link` span in the footer's "Our Services" column.

### Add an office
In `connect-us.html`: copy a `.pin` block, set `left`/`top` as percentages of `india_map.jpg`, and fill in the name, address, `tel:` link and Maps URL. Check it does not overlap an existing pin. Update the "Nine offices" line in the hero and the city list in the intro paragraph.

### Add a video testimonial
In `voices-of-bda.html`: copy a `.voice-card`, set `data-video-id`, `data-name`, `data-role`, `data-experience`, and update the visible `<h3>`, `<p>` and thumbnail `src` (which uses the same video ID). Portrait/Shorts videos work best given the modal's crop.

### Change a colour or font
There is no shared stylesheet — the `:root` block must be edited in **each of the 8 files**, and the values currently differ between them (see §4.2).

---

## 13. Known issues

A full audit of 74 findings is documented separately in **`website-review-aug-2026.html`** (in the project). The headline items:

**Broken or wrong**
- Placeholder testimonial text is live on `voices-of-bda.html`
- `voices-of-bda.html` is not linked from the other 7 pages
- Logo links on `knowledge-centre.html`, `our-articles.html` and `voices-of-bda.html` point to `folio304.github.io` instead of the live domain; `index.html` logo is `href="#"`
- Two different head offices are stated, and the one on the contact card has no map pin
- Map pins overlap: Raipur 1/2 and Delhi 1/2 sit on top of each other
- Industries counter is hardcoded to 13; there are 12 cards
- Compliance calendar cites s.234I for a late filing fee (should be 234F)
- Careers form rejects any non-Gmail address
- Four different contact identities across the site
- Years of experience stated as 37+, 36 and "over two decades" on the same site

**Structural**
- No shared CSS or nav/footer — every change means 8 (or 16) edits
- Services and industries exist only in JS modals, so none of that content is indexable
- No meta descriptions, Open Graph tags, canonicals, favicon, structured data, `sitemap.xml`, `robots.txt` or 404 page on any page
- No Our People / Leadership page
- Design tokens diverge across all 8 pages (4 navys, 4 golds, 3 fonts, 2 nav heights)
- Office tooltips are hover-only, so unusable on mobile
- Clickable cards are `<div onclick>` — not keyboard accessible
- No privacy policy or consent capture, despite collecting CVs and phone numbers
- ICAI disclaimer appears on 1 page out of 8

**Content review pending**
- Several items need an ICAI compliance view: the testimonials page, client counts, "Book a Free Consultation", the CTC range, and the "Designed with precision — like our audit work" slogan

---

*Last reviewed: 14 August 2026 · Batra Deepak & Associates, Chartered Accountants*
