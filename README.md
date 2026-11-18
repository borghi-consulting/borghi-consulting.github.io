# borghi-consulting.github.io

Public website of **Uncrypto The Crypto™ by Borghi Consulting™**. Static site on GitHub Pages: plain HTML and CSS, no build step, no backend.

This README is the working document for the site. It is written from two seats: the **web architect** (what the system is, why it is shaped this way, how the pieces connect) and the **senior web developer** (how it is built, verified and released). Each phase says what to do, what "done" means, and who owns it.

> Educational information only, not financial advice. This applies to every page and every line of copy on the site.

---

## 1. Goal and scope

**Goal.** Turn a set of separate pages (currently each linked on its own from Linktree) into one coherent website with a front door, so that a stranger can understand the offer in 30 seconds and take one of two free first steps: the **Quick Check** or a **free 30-minute call**.

**In scope**
- A new home page (`index.html`) in the current design system.
- Consistency across the existing pages (navigation, links, footer, form links).
- Discoverability by search engines and AI assistants (Phase 6).
- A custom domain bought on GoDaddy, pointed at GitHub Pages (Phase 10).
- A lead path from first visit to a booked call or a subscribed programme.
- Performance, SEO, privacy and release hygiene.

**Out of scope for now**
- The free lesson video (it has to be shot first; a ready slot is already in `index.html`, see Phase 4).
- A custom backend, user accounts or gated content.
- An Italian version of the site (listed under open decisions).

---

## 2. Current state (audit)

Audited from the repository `borghi-consulting/borghi-consulting.github.io` (branch `main`, 19 commits, deployed with GitHub Pages).

| File | Size | Design generation | Notes |
|---|---|---|---|
| `index.html` | 4.1 MB | **Old** (replaced by the new home) | 4.05 MB was two embedded base64 images. Old palette and layout. It linked to `terms.html`, which no longer exists: the terms are `terms_UtC.html` and `terms_MtM.html`. |
| `services.html` | ~296 KB | Current | Eight areas in one long page, with the in-page menu. Carries the Quick Check link. |
| `services_pricing.html` | ~297 KB | Current | Same page plus AUD prices and Stripe Payment Links on the four plans. |
| `about.html` | ~508 KB | Current | Story page. Three embedded JPEGs (~100 KB each). |
| `uncrypto_live_and_history.html` | ~281 KB | Current | Live converter and price history tool (one script). |
| `gain_loss_calculator.html` | ~267 KB | Current | Calculator (one script). |
| `glossary.html` | ~25 KB | Mixed | Older font weights; not yet on the current tokens. |
| `terms_UtC.html`, `terms_MtM.html` | ~113 KB, ~116 KB | Current | One per brand line. |

**Findings that drive the plan**

1. The old `index.html` is the only page in the old style, and it is the one a visitor lands on at `borghi-consulting.github.io`.
2. Every current page is self-contained: it repeats 24–40 KB of CSS and embeds its own images. Consistent, but costly to change (a fix has to be made in several files).
3. The current pages do not link to each other except `about.html` → `services.html`. There is no shared header, so a visitor on a tool page has no way home.
4. Four pages (`services`, `services_pricing`, `about`, `uncrypto_live_and_history`) linked to the **previous** form (`forms.gle/EdtVTVutX6EJcTwe7`). **Fixed 2026-11-18:** all now point to the Quick Check, `forms.gle/SapWaQJCXMnXCYEQA`.
5. Pricing is public and static: Stripe Payment Links, no gate in the current file.
6. The site speaks to a multi-country audience (the Quick Check lists AU, NZ, Italy and about 30 more countries), but the only language is English.
7. No page had a meta description, canonical URL, social tags, structured data or favicon, and `services` and `services_pricing` shared one title. **Fixed 2026-11-18** (Phase 6).
8. Four pages (`services`, `services_pricing`, `about`, `uncrypto_live_and_history`) have one stray `</div>` before the "Menu" button. Browsers ignore it; clean it up in Phase 2.
9. `terms_UtC.html` and `terms_MtM.html` each have two `<h1>`. Left as is (legal text); fix when the terms are next edited.

---

## 3. Architecture

### 3.1 System context

```
 Social / Linktree / newsletter / QR
              │
              ▼
      index.html  (front door)
   ┌──────┬─────┴──────┬───────────┬──────────────┐
   ▼      ▼            ▼           ▼              ▼
services  services_   about     tools:          glossary
.html     pricing     .html     live+history,
          .html                 gain/loss
   │         │
   │         └──► Stripe Payment Links (checkout, hosted by Stripe)
   │
   ├──► Quick Check (Google Form)  ──► Google Sheet ──► (Phase 5) Apps Script ──► CRM / ledger
   ├──► Free 30-min call (Google Calendar booking page)
   └──► Community: Beehiiv newsletter, Telegram, Facebook Group, WhatsApp channel
```

### 3.2 Information architecture

| Level | Page | Job |
|---|---|---|
| Front door | `index.html` | Explain the offer, route to the right page, push the two free first steps |
| Offer | `services.html` | How we work, the Hive system, what each programme includes |
| Offer + price | `services_pricing.html` | Same, with prices and checkout |
| Trust | `about.html` | Why we exist, who Alessandro and Giulia are |
| Free value | `uncrypto_live_and_history.html`, `gain_loss_calculator.html`, `glossary.html` | Useful on their own, bring people back |
| Legal | `terms_UtC.html`, `terms_MtM.html` | Terms and privacy per brand line |

### 3.3 Primary user journey

1. **Arrive** from Linktree, social, newsletter or search → `index.html`.
2. **Understand** in the hero (what we do, in two sentences) and the programme cards.
3. **Take a free first step**: Quick Check (about 2 minutes) or the free 30-minute call.
4. **Choose** a programme on `services.html` / `services_pricing.html`.
5. **Pay** through the Stripe Payment Link for that plan.
6. **Onboard**: welcome email, Hive Risk Profile™, first session.

Every page ends with a path forward; no page is a dead end.

### 3.4 Integrations

| Integration | Used for | Owner of the data | Notes |
|---|---|---|---|
| Google Forms (Quick Check) | Intake: name, email, mobile, country, social link, source, household, role, then 6 more pages of branching questions | Borghi Consulting | Created in another Claude project, where its branching logic is documented. Collects personal data, so the privacy terms must cover it. |
| Google Calendar booking page | Free 30-minute call | Borghi Consulting | Same link on all pages. |
| Stripe Payment Links | Programme checkout | Stripe / Borghi Consulting | One link per plan. |
| Beehiiv | Hive5 Dispatch newsletter, access link to the private community | Borghi Consulting | |
| Telegram, Facebook, WhatsApp | Community and updates | Platform | External links only. |
| Google Fonts | Bebas Neue, DM Sans, Dancing Script | Google | See Phase 3 for the self-hosting decision. |

### 3.5 Architecture decisions

**ADR-1: static site on GitHub Pages.**
Context: no server, no budget for hosting, content changes rarely. Decision: keep pure static HTML/CSS. Consequences: fast, free, nothing to patch or attack. No server-side logic, so anything that needs one (lead routing, email, gating) goes through Google Forms, Stripe or Apps Script. Hidden content is **not** secure on this stack: anything in the HTML can be read.

**ADR-2: one self-contained file per page, for now.**
Context: the upload workflow ("Add file → Upload files") makes single files easy to replace. Decision: keep inlined CSS until Phase 3. Consequences: no cross-file breakage, but the same CSS is duplicated and every design fix must be applied to each page. Phase 3 revisits this with a shared `assets/site.css`.

**ADR-3: no JavaScript on the home page.**
Context: the home page only needs links, layout and a few visual components. Decision: zero scripts. Consequences: nothing to break, best possible performance, no consent banner needed for the page itself. Interaction lives in the tool pages.

**ADR-4: Google Forms as the intake layer, Apps Script as the automation layer.**
Context: the Quick Check already exists and works. Decision: do not rebuild it in HTML. Connect its response sheet to Apps Script (Phase 5) for routing, CRM entry and follow-up. Consequences: no new infrastructure; the form's look stays Google's.

**ADR-5: the design system is the existing one, not a new one.**
Context: the current pages already share one visual language. Decision: the new home reuses its tokens and components unchanged (§4). Consequences: the site feels like one product; new pages cost little.

---

## 4. Design system (as found in the current pages)

### 4.1 Tokens

| Token | Value | Use |
|---|---|---|
| `--black` | `#000` | Dark sections, primary buttons |
| `--yellow` | `#FFD100` | Accent, rules, highlighted band |
| `--azure` | `#00A3E0` | Script text, links on hover, icons |
| `--azure-soft` | `#A8D8EA` | SEED block, soft fills |
| `--bg` / `--surface` | `#F5F7FA` / `#fff` | Page background / alternating sections |
| `--ink` / `--body` / `--muted` | `#111` / `#3A3A3A` / `#6f6f6f` | Headings / text / secondary text |
| Block colours | SEED `#A8D8EA`, CORE `#00A3E0`, FLOW `#FFD100`, INNOVATION `#FF9F43`, YIELD `#A8E6CF` | The five Hive blocks, never reused for anything else |

**Type.** Bebas Neue for display and headings, DM Sans for body (400–700), Dancing Script 600 for the "Alessandro & Giulia" signature only. Scale: H1 `clamp(2.5rem, 6.4vw, 5.6rem)`, H2 `clamp(2.4rem, 4.8vw, 3.6rem)`, body `.95rem`, label `.75rem`. Section padding `6rem` (`4.5rem` on mobile).

**Shape.** Hexagon (`clip-path` 25/75% polygon) is the brand shape: background pattern, honeycomb, bullet markers. Squared corners everywhere else.

### 4.2 Components in use

`nav` (fixed, logo + Menu), `.hero`, `.menu-box` / `.menu-list`, `.section` / `.alt` / `.dark`, `.plans` / `.plan` / `.plan.feat`, `.band` (yellow call-to-action strip), `.steps3`, `.honey` / `.hx`, `.card` / `.cta-big` / `.chan`, `.btn` / `.btn.ghost`, `footer` + `.disc` (disclaimer), `.to-top`.

### 4.3 Rules for any new page

- Start from the `:root` tokens; do not introduce new colours.
- One H1 per page; headings in order.
- Visible keyboard focus (the home adds a 3 px azure outline).
- Respect `prefers-reduced-motion` (already in the shared CSS).
- **Programmes are shown as equals.** Four identical cards, each with a yellow top edge. Pathway is only slightly stronger: a thicker edge and a soft yellow tint. No inverted (black) card, no "Signature" badge, no text highlighter (tried and dropped: it looked cheap). One visually dominant plan reads as pushing a single sale.
- The disclaimer block goes in every footer, word for word.
- No claims of returns, results or "guaranteed" outcomes, and no testimonials about money made.

---

## 5. Repository layout

**Today (flat, upload-friendly):**

```
index.html                       NEW home (this delivery)
services.html
services_pricing.html
about.html
uncrypto_live_and_history.html
gain_loss_calculator.html
glossary.html
terms_UtC.html
terms_MtM.html
README.md                        this file
robots.txt                       NEW (crawler rules + sitemap location)
sitemap.xml                      NEW (nine pages)
llms.txt                         NEW (plain-text summary for AI assistants)
template_insight.html            NEW (answer-first article template, set to noindex)
```

**Target after Phase 3:**

```
index.html … (pages as above)
assets/
  site.css                       shared tokens + components
  img/                           bee, team photos, og-image.png (WebP/PNG, sized)
  fonts/                         only if fonts are self-hosted
(robots.txt, sitemap.xml and llms.txt stay at the root)
```

Naming: lowercase, underscores for multi-word pages (existing convention: `services_pricing.html`), brand suffix for legal pages (`terms_UtC.html`). Keep URLs stable: once a page is public or on Linktree, do not rename it.

---

## 6. Delivery phases

Status key: ✅ done · ▶ next · ⏸ on hold · ☐ later.

### Phase 0: Audit and freeze ✅
**Done:** repository read, pages inventoried, design tokens extracted, Quick Check page 1 reviewed (§2).
**Before you replace `index.html`:** create a tag `legacy-home` on the current commit (GitHub → Releases → Create a new release → tag only). The 19-commit history already keeps the old file, the tag makes it easy to find.

### Phase 1: New home page ✅
**Deliverable:** `index.html` (~115 KB, no scripts), built from the current design system.
**Contents, in order:** hero with the two free first steps → site menu (the six pages that Linktree currently lists one by one) → four programmes → yellow "Not sure which one fits?" band → three steps to the first session → Hive Block Strategy™ honeycomb → About teaser → community channels → footer with disclaimer and both terms pages.
**Programme cards:** four equal cards with a yellow top edge; Pathway slightly stronger (thicker edge, soft tint); no inverted card, no "Signature" label.
**Answer-first block (added after the competitor teardown, Phase 6):** "What is Uncrypto The Crypto™?", problems we work on, what we are not (not financial advisers), an at-a-glance fact list and eight FAQ answers with matching `FAQPage` markup.
**Head:** unique title and description, canonical, social tags, favicon and JSON-LD (`Organization`, `WebSite`).
**Copy rules applied:** every programme line and the disclaimer come from the current `services.html`; nothing new is promised.
**Checked locally:** no duplicate IDs, balanced tags, every internal link and anchor resolves, no horizontal scroll at 390 px and 1440 px.
**Acceptance:** the page renders correctly on phone and desktop, and both buttons in the hero open the Quick Check and the calendar.

### Phase 2: Consistency pass across existing pages ▶ (partly done)
Small, mechanical edits. Each is a find-and-replace.

| # | Change | Files |
|---|---|---|
| 2.1 | ✅ Replace the previous form link `forms.gle/EdtVTVutX6EJcTwe7` with `forms.gle/SapWaQJCXMnXCYEQA` | `services`, `services_pricing`, `about`, `uncrypto_live_and_history` |
| 2.2 | Point the logo to the home: `href="#top"` → `href="index.html"` | all current pages |
| 2.3 | Add a "Home" entry to each page's menu or footer | all current pages |
| 2.4 | Footer year © 2025 → © 2026 (only where present) | `glossary`, others as found |
| 2.5 | Bring `glossary.html` onto the current tokens (same fonts, colours, nav, footer) | `glossary` |
| 2.6 | ✅ Unique `<title>` and meta description on every page | all |
| 2.7 | ✅ Programme cards: four equal cards with a yellow top edge, Pathway slightly stronger; no inverted card, no "Signature" badge | `services`, `services_pricing` (and the home) |
| 2.8 | ✅ Heading order: former `<h4>` (disclaimer, "how the system fits" list) are now `<h3>` | `services`, `services_pricing`, `about`, `uncrypto_live_and_history`, `gain_loss_calculator` |
| 2.9 | Remove the stray `</div>` before the "Menu" button | `services`, `services_pricing`, `about`, `uncrypto_live_and_history` |

**Acceptance:** from any page, one click reaches the home; no link in the site points to the previous form or to a missing file.

### Phase 3: Performance and structure ☐
- **Images.** Extract the embedded bee PNG (about 73 KB, repeated in each page) and the three team JPEGs in `about.html` (~100 KB each) into `assets/img/`, convert to WebP, set `width` and `height`, add `loading="lazy"` below the fold.
- **Shared CSS.** Move the common tokens and components into `assets/site.css`; each page keeps only its own rules. Decide this after Phase 2: it is the point where ADR-2 is revisited.
- **Fonts.** Today they come from Google Fonts. Because the audience includes the EU, consider self-hosting the three families (WOFF2, `font-display: swap`) to avoid sending visitors' IP addresses to Google.
- **Targets (Lighthouse, mobile):** Performance ≥ 90, Accessibility ≥ 95, Best Practices ≥ 95, SEO ≥ 95. Largest Contentful Paint under 2.5 s on a mid-range phone.

### Phase 4: Free lesson video ⏸ (on hold: needs to be shot)
**Why it matters:** it is the cheapest way to build trust before anyone fills in a form.
**Slot already in place:** a commented-out section in `index.html` marked `VIDEO SLOT`. To activate: remove the comment markers and replace `VIDEO_ID`.
**Production spec**
- Length 3–6 minutes. One idea: why structure comes before speculation.
- Shoot horizontal (16:9) for the page; cut a vertical version for social.
- Captions as an `.srt` file (accessibility, and most people watch muted).
- Host on YouTube; embed with `youtube-nocookie.com` (no tracking cookies until played).
- End on one call to action: the Quick Check.
- No performance claims, no price talk, disclaimer on screen at the end.
**Acceptance:** video plays on phone and desktop, the page layout does not shift when it loads, the Quick Check button follows it.

### Phase 5: Lead pipeline (Apps Script) ☐
Goal: every Quick Check response becomes a CRM record and a next action, with no manual copying.
1. Link the form to a response Google Sheet (native Forms setting).
2. Apps Script, installable trigger on form submit:
   - `normaliseResponse()`: clean name, email, country, branch (the answer to "Which describes you best?").
   - `routeLead()`: assign the suggested programme path from the branch and answers.
   - `writeToLedger()`: append to the CRM / ledger sheet with a lead ID and a timestamp.
   - `notifyOwner()`: email or chat message to the owner with the summary.
   - `sendWelcome()`: optional first reply with the calendar link (only after the privacy wording is confirmed).
3. Log every run (success and error) to a `_log` sheet so failures are visible.
**Source tracking.** Google Forms does not read UTM parameters by itself. Either add a hidden pre-filled "source" question (via the form's pre-filled link) or rely on the existing "How did you find us?" dropdown. Decide before building.
**Privacy.** The form collects name, email, mobile, country and household. The Terms and Privacy pages must say what is collected, why, where it is stored, for how long, and how to ask for deletion. Review them against the Quick Check before launch.
**Acceptance:** a test submission appears in the ledger within a minute, with the right branch and no duplicated row.

### Phase 6: Search and AI discoverability ◐ (mostly done)

**What makes a page findable and quotable by search engines and AI assistants**
1. The content is real text in the HTML (not an image, not built by script). The whole site already works this way, so crawlers and AI assistants read exactly what visitors read.
2. A clear page structure: one `<h1>`, headings in order, landmarks (`<nav>`, `<main>`, `<footer>`), descriptive link text.
3. A unique title and description per page, a canonical URL, and a sitemap.
4. Structured data that states plainly who the business is.
5. Crawlers are allowed to read the site.
6. Stable URLs and fast pages.

**Done (2026-11-18)**
- Unique `<title>` and `<meta name="description">` on all nine pages; `services` and `services_pricing` no longer share a title.
- `<link rel="canonical">`, Open Graph and Twitter tags, robots meta and a favicon on all nine pages.
- JSON-LD on the home: `Organization` (name, alternate names, URL, Perth WA, social profiles) and `WebSite`. Type is `Organization`, not a financial-services type: the business sells education and consulting and the schema should not suggest a licence it does not claim. Check that the social URLs in `sameAs` are the live accounts.
- `robots.txt`: all crawlers allowed, sitemap declared.
- `sitemap.xml`: nine pages.
- `llms.txt`: a plain-text summary of the offer, pages and contact points for AI assistants. This is an emerging convention; no major search engine has confirmed that it uses it, so treat it as low-cost insurance, not as a ranking factor.
- Heading order fixed on the pages that had a skipped level.
- Home: answer-first definition, "what we help with", "we are not financial advisers", at-a-glance list and eight FAQ answers with `FAQPage` and `WebPage` markup matching the visible text.
- `template_insight.html`: the article template (set to `noindex` until copied and filled in).

**AI crawlers.** Today everything is open, so the site can be quoted by AI search and assistants. If you ever want to keep the site out of AI **training** while staying visible in AI **search**, add separate `User-agent` blocks to `robots.txt` for the training crawlers (for example GPTBot, ClaudeBot, Google-Extended, CCBot) and leave the search crawlers allowed. This is a business decision, not a technical one.

**Teardown of a well-positioned competitor (collectiveshift.io, read 18 November 2026)**

Collective Shift is an Australian crypto research and education company. It is useful as a model because it earns search and AI visibility without presenting itself as a financial adviser. What it does, and what we copy (structure only, never wording or claims):

| What they do | Why it works for search and AI | What we do |
|---|---|---|
| The home answers "What is [brand], and is it right for you?" in the first screen: who, where, since when, what members get, and who it is **not** for | AI assistants quote a self-contained definition; "not for" removes wrong-fit traffic | Added to the home: definition, problems we work on, "we are not financial advisers" |
| An "at a glance" fact list with a date ("as of November 2026") | Short, factual, easy to extract, shows freshness | Added to the home, with "Last updated" |
| The scope limit is repeated everywhere: home, FAQ, article footer ("we do not manage money, hold client crypto or give personal financial advice") | Keeps the compliance position consistent and explicit | Same wording on the home, in the FAQ, in `llms.txt` and in the article template |
| A named method (5-step SHIFT) spelled out in plain text | A named framework becomes something people and AI can cite | Our named frameworks (Hive Block Strategy™, Hive Risk Profile™, HQEA™) are explained in text, not only in images |
| A visible FAQ with direct answers (5 questions on the home, 5 on each article) | Matches how people ask questions to assistants | 8 questions on the home with `FAQPage` markup that matches the visible text |
| An "Insights" hub of articles titled as real questions ("How Long Should You Hold Crypto?"), each with a date and an Australia-specific angle | Each article targets one search or assistant query | Content plan below; template provided |
| Article template: question as H1, byline with author profile, published and updated dates, **short answer** box, key points, contents list, H2s that are questions, FAQ, **"where this guide comes from"** (official sources first), author box, disclaimer | Answer-first structure and cited sources are what assistants prefer to quote | `template_insight.html` follows the same order |
| Text under every embedded video ("Key takeaway") | Crawlers cannot watch video; the text carries the content | Rule for Phase 4: every video gets a text summary and a transcript |
| "Summarise with AI" buttons (ChatGPT, Perplexity, Claude, Grok) that pre-fill a prompt with the page URL | Makes it easy for readers to feed the page to an assistant | Included in the article template |
| Trust pages: About with named team and credentials, Trust and Transparency, Is it legit, Official channels, Important information | Trust signals matter more in crypto; "official channels" protects against impersonation | Planned (see page types below) |
| Social proof: Trustpilot rating, member numbers, press logos | Credibility | **Do not copy.** Use only proof we can verify and publish. Never invent counts, ratings or logos. |
| Published and modified dates in the page head, one primary call to action repeated | Freshness signal; clear next step | Dates in the article template; Quick Check and free call as the two actions |

Not visible from a text view, so not verified: their structured data, `robots.txt`, sitemap and any `llms.txt`. To check, open the page source and search for `ld+json`. The site runs on WordPress with an SEO plugin; ours is hand-written static HTML, so the same result is achieved with templates.

**Page types for the Uncrypto site**

| Page type | Purpose | Status |
|---|---|---|
| Home (entity page) | Define the brand, scope, who it is for, FAQ | ✅ done |
| Service pages | `services`, `services_pricing` | ✅ exist |
| Insights hub (`insights.html`) | Lists every article by topic | ☐ |
| Insight articles | One question per page, answer first | ☐ template ready |
| About with credentials | Named people, real background, verifiable links | ☐ add credentials and links to `about.html` |
| Trust and transparency | What we are and are not, how we are paid, how to verify us | ☐ |
| Official channels | The only real accounts, to protect against impersonation | ☐ |
| Important information | Disclaimer in full, jurisdiction notes | ☐ (can reuse the terms) |

**Topic map: what people ask, in our scope.** Titles are written as "how it works" questions. No title or answer tells anyone what to buy or sell. Anything that touches tax, trusts, estates or company accounting needs review by a licensed professional in the relevant country before publishing.

| Audience or problem | Example article titles |
|---|---|
| Families | How can a family keep crypto safe and accessible? · What happens to crypto if the person who holds it is unavailable? · How do you explain crypto to a partner? |
| Founders | How should a founder think about crypto alongside the business? · What records should a business keep for crypto? |
| Portfolio analysis | How do you analyse a crypto portfolio? · What is a crypto risk profile, and why does it come first? |
| Market analysis | How do you read the crypto market without the noise? · What are market cycles, and why do people talk about them? |
| Diversification and rebalancing | What does diversification mean in crypto? · How do you rebalance a crypto portfolio? · What are the five blocks of a crypto portfolio? |
| Security | How do you spot a crypto scam? · What is self-custody? |
| Trust | Is Uncrypto The Crypto™ legit? · Are Uncrypto The Crypto™ financial advisers? |

**Rules for every article:** one question per page; short answer in 40–60 words at the top; headings that are questions; sources listed (official first); author and dates visible; the FAQ text and the `FAQPage` markup identical; scope statement and disclaimer at the end; one link to the relevant service page; one link to a related article; the two calls to action at the bottom.

**Still to do**
- Create `assets/img/og-image.png` (1200 × 630) and reference it in `og:image` and `twitter:image`; then switch `twitter:card` to `summary_large_image`.
- After the domain change (Phase 10): replace the base URL everywhere (one find-and-replace) and add the site to Google Search Console and Bing Webmaster Tools, submitting `sitemap.xml`.
- Replace the FAQ answers with real client questions as they come in; keep the markup identical to the visible text.
- Write the first three articles from the topic map, then build `insights.html`, the trust page and the official-channels page.
- Keep one page per topic and never rename a public URL; if you must, add a redirect page.

**Acceptance:** Google's Rich Results Test shows the home's structured data without errors; Search Console reports nine indexed pages; asking an AI assistant "what is Uncrypto The Crypto?" a few weeks after indexing returns an answer drawn from the site.

### Installable app (PWA): decision note

A PWA is a website that can be installed on a phone's home screen and, optionally, work offline. GitHub Pages can host one (HTTPS is already there): it needs a `manifest.webmanifest`, icons and, for offline use, a service worker.
**Verdict: possible, not worth it now.** The site is a marketing and education site with live tools; installability adds little for first-time visitors, and a service worker adds a cache that can serve stale pages after an update. Revisit if the tools (converter, calculator) become a product people open daily. Cost then: about half a day, no change to the architecture.

### Phase 7: Analytics and privacy ☐
- Choose one privacy-friendly, cookieless analytics tool, or none. A cookieless tool avoids a consent banner.
- Track four events only: click on Quick Check, click on free call, click on a plan's checkout, newsletter subscribe click.
- Standard link tags for Linktree and social: `?utm_source=linktree&utm_medium=bio` (pages ignore them, analytics reads them).
- Confirm the Terms and Privacy pages match what the site actually does (forms, analytics, fonts, embeds).

### Phase 8: QA and release ☐
Run before every release (details in §7).

### Phase 9: Operations ☐
- **Release:** upload the changed files to `main` (or push); GitHub Pages republishes in a minute or two.
- **Rollback:** revert the commit in GitHub; the previous version is live again.
- **Monthly:** open every page, click every external link, run Lighthouse on the home.
- **Change log:** add a line per release (§10).

### Phase 10: Custom domain on GoDaddy ▶

Goal: the site answers on your own domain (for example `yourdomain.com`), over HTTPS, with `www` redirecting to it. The steps below follow GitHub's current documentation; GoDaddy's screens change, so look for the names, not the positions.

**Decide first**
- Primary address: the bare domain (`yourdomain.com`) or `www.yourdomain.com`. GitHub recommends setting up both. If the custom domain in GitHub is the bare one, `www` redirects to it, and the reverse. Recommended: bare domain as primary.
- Keep email working: do not touch MX or TXT records used for email.

**Step 1: GitHub first (before touching DNS)**
1. Repository → **Settings → Pages → Custom domain**, type the domain, **Save**. GitHub adds a `CNAME` file to the root of the repository. Never delete it, and when you upload files by the web, do not overwrite it.
2. Recommended: verify the domain on your GitHub account (**Settings → Pages → Add a domain**), which gives you a TXT record to add at GoDaddy. It prevents someone else from claiming your domain on their own GitHub Pages site.

**Step 2: GoDaddy DNS** (domain → **DNS** / **Manage DNS**)

| Type | Name | Value |
|---|---|---|
| A | `@` | `185.199.108.153` |
| A | `@` | `185.199.109.153` |
| A | `@` | `185.199.110.153` |
| A | `@` | `185.199.111.153` |
| AAAA (optional, IPv6) | `@` | `2606:50c0:8000::153`, `2606:50c0:8001::153`, `2606:50c0:8002::153`, `2606:50c0:8003::153` (four records) |
| CNAME | `www` | `borghi-consulting.github.io` |

- Delete GoDaddy's default records that conflict: the `A` record for `@` that points to a parked page or GoDaddy website builder, and the default `www` record if it points elsewhere.
- Turn off any **domain forwarding** set at GoDaddy for this domain.
- Do not use wildcard records (`*`).

**Step 3: wait and check**
- DNS changes can take up to 24 hours. Check from the Terminal: `dig yourdomain.com +noall +answer -t A` should list the four GitHub addresses; `dig www.yourdomain.com +noall +answer` should show the CNAME.
- In **Settings → Pages**, when the DNS check passes, tick **Enforce HTTPS** (the certificate can take a while to appear).

**Step 4: switch the site over**
1. In every file, replace the base URL `https://borghi-consulting.github.io` with `https://yourdomain.com`. It appears in canonical tags, `og:url`, JSON-LD, `sitemap.xml`, `robots.txt` and `llms.txt`. One find-and-replace; Claude can regenerate the files.
2. Upload the files to `main`.
3. Update Linktree, social bios, the newsletter footer and the Quick Check description to the new address.
4. Add the domain to Google Search Console (a "Domain" property verifies with a DNS TXT record at GoDaddy) and submit `sitemap.xml`.

**Step 5: test**
- `https://yourdomain.com` and `https://www.yourdomain.com` both open the home over HTTPS, one redirecting to the other.
- The old address `https://borghi-consulting.github.io` redirects to the new domain.
- Every page, the Quick Check and the Stripe links still work. No browser warning about mixed content.

**Rollback:** remove the custom domain in Settings → Pages. The site is back on `borghi-consulting.github.io`; restore the previous URLs in the files.

**Acceptance:** both addresses load over HTTPS, canonical tags show the new domain, Search Console accepts the sitemap.

---

## 7. Quality assurance

### 7.1 Automated checks (run on every changed file)
1. Script syntax: `node --check` on each inline script (pages with tools).
2. HTML structure: parse with BeautifulSoup (`html.parser` and `lxml`), no parse warnings.
3. Duplicate IDs: none.
4. `getElementById` calls cross-checked against declared IDs.
5. Tag balance: opening and closing counts match for every tag.
6. Internal links: every `href` to a local file exists, every `#anchor` exists in its target file.
7. Diff against the previous verified version: only the intended lines changed.

### 7.2 Manual checks
- Phone (390 px), tablet (768 px), desktop (1440 px): no horizontal scroll, no overlapping text.
- Keyboard only: every link and button reachable, focus always visible.
- Screen reader spot check on the home: heading order, link names make sense out of context.
- Every external link opens the right destination (Quick Check, calendar, Stripe, Beehiiv, Telegram, Facebook, WhatsApp).
- Stripe: one real test payment per plan before launch, then refunded.

### 7.3 Browser matrix
Latest Safari (macOS and iOS), Chrome, Firefox, Edge.

### 7.4 What was and was not verified in this delivery
- Verified: structure, IDs, internal links, layout at 390 px and 1440 px.
- **Not verified:** the Google Fonts rendering (the build environment has no access to Google Fonts, so layout was checked with fallback fonts); the Stripe links; GitHub Pages settings; whether Google and AI assistants have indexed the pages (only visible weeks after upload). Check the pages with the real fonts after upload.

---

## 8. Compliance notes (not legal advice)

The business teaches crypto and structures portfolios. It is not financial advice, and the site must never read as if it were.

- Keep the disclaimer in every footer and in the Quick Check description.
- No return figures, no "guaranteed" or "risk-free" language, no urgency timers, no earnings screenshots.
- The audience is multi-country (the Quick Check offers about 30). Rules on promoting crypto and financial products differ by country (for example ASIC in Australia, the FMA in New Zealand, and the rules of each EU state). Have the site copy, the Quick Check and the Terms reviewed by a qualified professional before any paid promotion.
- Affiliate or referral links, if added later, must be disclosed next to the link.

---

## 9. Open decisions and risks

| # | Item | Owner | Needed for |
|---|---|---|---|
| 1 | Shoot the free lesson video | Alessandro | Phase 4 |
| 2 | Which page is the public price page: `services_pricing.html`, or `services.html` with prices added? The home links to both ("See what's included" and "See plans and prices") | Alessandro | Phase 2 |
| 3 | Linktree: keep individual links, or point to the home plus two or three deep links? Recommended: home, Quick Check, free call, newsletter | Alessandro | After Phase 2 |
| 4 | Quick Check: confirm the branching questions and the data collected match the privacy wording (the form was created in another Claude project) | Alessandro | Phase 5 |
| 5 | UTM / source tracking approach for the Quick Check | Alessandro | Phase 5 |
| 6 | Which GoDaddy domain, and bare domain or `www` as primary | Alessandro | Phase 10 |
| 7 | Italian version of the site (the Quick Check lists Italy) | Alessandro | After Phase 8 |
| 8 | Self-host fonts (EU privacy) | Alessandro | Phase 3 |
| 9 | Allow AI training crawlers or only AI search (today: all allowed) | Alessandro | Phase 6 |
| 10 | Confirm these statements are true for the business before publishing: "we do not manage money, hold your crypto, trade for you or give personal financial advice" | Alessandro | Phase 6 |
| 11 | First three articles from the topic map; who drafts and who approves each | Alessandro | Phase 6 |
| 12 | Credentials and verifiable links for Alessandro and Giulia on `about.html` | Alessandro | Phase 6 |

**Risks**
- Hidden or "gated" content on GitHub Pages is readable by anyone who opens the page source. Do not put anything confidential in the repository.
- The same CSS lives in several files. Until Phase 3, a design fix has to be repeated on every page.
- Personal data in the Quick Check: privacy wording must be in place before traffic is sent to it.

---

## 10. Change log

| Date | Change |
|---|---|
| 2026-11-18 (3) | Programme cards redesigned (highlighter dropped): four equal cards with a yellow top edge, Pathway slightly stronger. Competitor teardown added to Phase 6. Home gains the answer-first block, "not financial advisers" scope, at-a-glance list and FAQ with markup. `template_insight.html` added. |
| 2026-11-18 (2) | Existing pages: Quick Check link updated (4 pages); Pathway no longer shown as a contrast card, no "Signature" badge, highlighter on all four programme names (Pathway stronger); SEO head on all pages (titles, descriptions, canonical, social tags, favicon); JSON-LD on the home; heading order fixed; `robots.txt`, `sitemap.xml`, `llms.txt` added; PWA decision note; Phase 10 (GoDaddy domain) written. |
| 2026-11-18 | Audit completed. New `index.html` built on the current design system. Quick Check link set to `forms.gle/SapWaQJCXMnXCYEQA`. Video slot prepared (commented out). README written. |
