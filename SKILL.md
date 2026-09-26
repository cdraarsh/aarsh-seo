---
name: aarsh-seo
description: Aarsh's own SEO methodology, distilled from his Advanced SEO course notes (Classes 1–28). Use for any SEO diagnosis, audit, or strategy work done Aarsh's way, including Google Search Console errors and reports (indexing, canonical, soft 404, crawled/discovered not indexed, crawl stats, removals, change of address, users/tokens), robots.txt, meta robots and X-Robots-Tag, sitemaps, .htaccess redirects, crawl budget, internal linking (pillar → cluster → money page), schema/JSON-LD and enhancement reports, Core Web Vitals (LCP/CLS/INP), keyword research and search intent, content structure, WordPress/Shopify/Wix/cPanel setup, and SEO vs GEO/AEO/AIO/LLMO questions. Trigger on "aarsh-seo", "my SEO notes", "SEO the way I learned it", or any of the topics above when Aarsh is asking.
---

# Aarsh SEO

Aarsh's SEO playbook. Answer from this file first; open [references/course-notes.md](references/course-notes.md) for depth (Google's architecture, discovery methods, ranking systems, LLM/RAG, platform setup, schema detail).

Where the course and Google's official docs disagree, this skill follows the docs.

## Mental model: 5 steps

Every SEO problem sits at one of these steps. Find which one breaks first.

1. **Discovery**: Google learns the URL exists (sitemap, internal/external links, GSC submission, RSS…)
2. **Crawling**: Googlebot fetches it
3. **Rendering**: Google reads it like a browser. If users see it but Googlebot doesn't, it can't rank.
4. **Indexing**: Google saves a copy. Not indexed = can't rank.
5. **Ranking**: Google shows it for a query

Then: content creation → analytics → reporting (proof of work for the client). A well-managed site converts better.

**Core idea:** SEO = match the user's *query* (what they type, uncontrolled) with your page via the *keyword* you target (controlled).

## GSC indexing status → cause → fix

| Status | Meaning | Fix |
|---|---|---|
| **Soft 404** | Live page Google thinks has no value | Add real content, or delete it (return 404/410) |
| **Duplicate, without user-selected canonical** | Duplicates found, no canonical set | Add canonical on all versions → preferred URL |
| **Duplicate, Google chose different canonical** | Google overrode your canonical (stronger link profile elsewhere) | More internal + external links to preferred URL; remove internal links to the other |
| **Alternate page with proper canonical tag** | Working as intended | Ignore |
| **Page with redirect** | Inspected URL redirects | Expected for redirected URLs; remove the redirect only if the page should be indexed |
| **Excluded by noindex tag** | `noindex` present (an instruction, not a hint) | Remove noindex if it should be indexed; drop from sitemap if not |
| **Discovered – currently not indexed** | Known, not yet crawled (queue busy or URL pattern looks low-quality) | Wait 7–10 days → better URL/title/description → more internal links → backlinks |
| **Crawled – currently not indexed** | Crawled, judged not good enough | Improve content (examples, infographics, external refs), more internal links |
| **Video is not on the watch page** (90–95% of video issues) | Video embedded in a text-first page | One video per dedicated watch page: URL contains `/video/`, title = video title, short description. Embed elsewhere after it's indexed. |

Canonical = a **hint**, not an instruction. Link equity from duplicates consolidates onto the chosen canonical.

**Validate Fix**: click only after the fix is live. Repeated false validations make Google ignore future ones.

## Status codes

2xx ok · 3xx redirect (301 permanent, 302 temp) · 304 not modified (skip recrawl) · 4xx client (404 gone, 410 gone for good, 403 forbidden) · 5xx server/host problem.
robots.txt returning 404 → Google crawls everything; 5xx → Google stops crawling.

## Directives

| Tool | Controls | Notes |
|---|---|---|
| robots.txt | **Crawling**, not indexing | Must be `/robots.txt` at root. Never `Disallow` a page to deindex it: Google can't see the noindex. Use noindex + allow crawl. |
| `<meta name="robots">` | Indexing/snippets | `noindex`, `nofollow`, `none` (= both), `nosnippet`, `max-snippet:N`, `indexifembedded`, `unavailable_after:DATE` |
| `X-Robots-Tag` header | Same, for non-HTML (PDFs, images) | Set via .htaccess |
| canonical | Preferred URL among duplicates | Hint; self-referencing canonical on every page |

robots.txt precedence: when Allow and Disallow both match, the most specific (longest) rule wins. Least restrictive (Allow) only breaks a tie between rules of equal length.

## Benchmarks

- **Server response (Crawl Stats):** ideal < 200 ms, not > 500 ms, hard ceiling 1000 ms. Faster = more crawling.
- **404s in Crawl Stats:** ≤ 10% (these are mostly assets such as CSS/images, not pages)
- **CWV:** LCP < 2.5 s · CLS < 0.1 · INP < 200 ms (INP replaced FID). Ranking uses **field data** (CrUX / real users), not lab data.
- **Sitemap:** ≤ 50,000 URLs and ≤ 50 MB uncompressed per file; use a sitemap index beyond that. Google uses `<loc>` + `<lastmod>`, ignores `<priority>`/`<changefreq>`.
- **GSC:** 16 months of data (Bing: 24), default view 3 months; exports cap at 1,000 rows (2,000 via API). BigQuery bulk export = full data kept indefinitely (paid).
- **Removal tool:** hides in ~2–3 h, lasts **6 months**. Permanent removal requires the page to return 404/410.
- **Change of Address:** 180-day window where it can be cancelled.
- **Crawl budget** matters mainly at 10k+ pages.
- **Ranking signals:** "200 signals" is a made-up number. Know ~19–20, work daily with 5–6.

## Playbooks

### Page not indexed
1. URL Inspection: discovered? crawled? What canonical did Google pick?
2. Check blockers: robots.txt disallow, noindex, canonical pointing elsewhere, redirect, non-200 status.
3. Rendering: does Googlebot see the content (JS)? Mobile-first, so check the mobile version.
4. Quality: thin/duplicate → soft 404 / crawled-not-indexed → improve content.
5. Importance: add internal links from strong pages (high in main content), add to sitemap, earn backlinks.
6. Only then Request Indexing. Don't spam it. Deleting and resubmitting all sitemaps is a "steroid" for big site changes only (redesign, malware cleanup, mass content update).

### Crawl budget
Fix soft 404s (deleted pages → 404/410, not 200) · block junk parameters (`?utm_`, `?filter=`) in robots.txt · collapse redirect chains A→B→C into A→C · speed up TTFB · remove low-value/duplicate pages. In Crawl Stats, low "Discovery" % while publishing a lot = budget problem.

### Domain migration (Change of Address)
1. Same content and URL structure on old and new domains (design may change).
2. Page-to-page 301 for every URL (.htaccess). Home→home alone passes validation but kills traffic.
3. Verify new domain in GSC.
4. Old property → Settings → Change of Address → Validate and Update.
5. Monitor for 180 days.

### Internal linking funnel
- **Pillar (hub)** → links to **cluster** content near the top.
- **Cluster** (guides, stories, articles) → links to the **money page** near the top.
- Main-content links > sidebar/footer (Reasonable Surfer). Higher in the text > lower.
- Don't spend top-of-content links on low-value pages (Contact Us) from money pages.

### GSC security hygiene
Settings → Ownership verification → check **Ownership history** and **Unused ownership tokens** → Remove any unused tokens (otherwise an ex-owner can regain access and, say, remove the homepage). Give clients/vendors **Full** (not Owner) access. Remove client sites from your own account when an engagement ends.

### Keyword research
Intents: navigational · informational · commercial · transactional. Start with **3–6 word, medium-difficulty long-tail** keywords (lower volume, higher conversion). Sources: Google Autosuggest, Keyword Planner, **GSC queries with high impressions and low clicks** (= page to create or improve). Short-tail is for branding.

### Content
- Service pages rank for "service + brand"; "why should I use X" queries need content.
- AI content is fine; **scaled content abuse** is what gets penalised. Use few-shot prompts (give examples) and instruct the model to collect facts and cite sources first.
- Guide: H1 title → H2 steps → H3 details → CTA. Case study: Challenge → Solution → Result → Testimonial.
- H1 (for users) ≈ meta title (for SERP), not necessarily identical. Google rewrites titles/descriptions ~70% of the time.
- Images: descriptive filename, alt text, caption explaining relevance, explicit width/height.
- Forms: one "Full Name" field; real labels, not placeholder-only.
- Add a TL;DR summary up top (helps both SEO and answer engines).

### Schema
JSON-LD in `<head>`. Organization (+ `sameAs` for socials/directories → knowledge graph), LocalBusiness (exact NAP, add `areaServed`/`founder`), Product (name, image, brand, sku, offers, aggregateRating), Article/NewsArticle, Breadcrumb. Generate with technicalseo.com, test with the Rich Results Test, monitor under GSC Enhancements (Invalid = no rich result; Valid with warnings = eligible).
- Course: breadcrumb list starts at the first category, not the homepage.
- Schema must match what's visible on the page. Never stuff extra keywords into `description` or any other field; a mismatch can earn a manual action (Class 26).

### Core Web Vitals fixes
- **LCP:** preload the LCP image, never lazy-load it, WebP, cut TTFB, split huge text blocks.
- **CLS:** width/height on images, `min-height` reserved for ads/banners/dynamic slots.
- **INP:** less main-thread JS, simpler DOM/CSS.

## GEO / AEO stance (instructor's opinion)
- **AIO** and **LLMO** are buzzwords: marketers don't optimise AI or LLMs.
- **AEO = GEO**: optimising for answer-giving generative platforms (ChatGPT, Perplexity, AI Mode).
- **AI SEO** is legit, but Google has used AI (RankBrain, BERT) for years.
- Solid SEO (intent, summaries, silo structure, structured data, varied formats, research-driven strategy) already covers GEO. No AI platform gives impression/click data, so "GEO performance tracking" is mostly unmeasurable today.
- Mechanics worth knowing: LLMs predict the next token; RAG tools such as Perplexity chunk → embed → retrieve (vector DB + live web search) → generate. Embedding closeness between query and page is the basis of semantic SEO. Details in the reference.
