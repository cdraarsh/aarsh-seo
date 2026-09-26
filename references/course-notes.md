# Advanced SEO Course Notes (condensed)

Source: Aarsh's notes from the Advanced SEO course, Classes 1–28. The source has no Class 13. Instructor opinions are marked as such.

---

## Class 1: SEO vs GEO vs AEO

- A search engine works in 5 steps: discovery → crawling → rendering → indexing → ranking. SEO optimises for all five, then covers content creation, analytics (data by people, region, engine and page), and reporting (proof of work).
- A site with only Home/About/Service pages ranks for "service + brand", not "why should I use service". That needs content.
- Instructor (Amit Tiwari): don't just "make blog posts". Forge them deliberately. AI or human writing is both fine.
- **AIO**: buzzword. Marketers don't optimise AI.
- **AEO** (answer engine optimisation): ChatGPT and AI Mode give answers, not result lists.
- **AI SEO**: a normal term. Google has used AI (RankBrain, BERT) for years.
- **GEO** (generative engine optimisation) = AEO. Normal SEO work covers it.
- The instructor's rebuttals to "GEO is different": SEOs already write TL;DR summaries, contextual content, silo structures, intent-based funnels, multiple formats (articles, news, testimonials, case studies, JSON-LD) and research-driven strategy. No AI platform (ChatGPT, Perplexity, Gemini/AI Mode) gives impression or click data, so GEO performance tracking has no data behind it. The instructor also claimed Google ships an update every ~2.5 hours; treat that as their claim, not a documented figure.
- **LLMO**: buzzword. We can't optimise LLMs.

## Class 2: How search engines work

Basic parts: **crawler** (fetches) → **indexer** (Google's is Caffeine, which processes the fetched pages) → **index** (storage) ← **query engine** (carries the query from the **interface** to the index and returns the results).

### Early Google architecture (4 stages: acquisition, indexing, retrieval, ranking)
These are kept as separate systems so each can scale on its own.

| System | Job |
|---|---|
| URL Server | Queue of millions of URLs; sends batches to the crawler by schedule/urgency; dedupes. GSC submissions are treated as urgent. |
| Crawler | C++ program. Downloads HTML and assets and renders with headless Chrome. Only downloads; doesn't understand or rank. |
| Store Server | Compresses pages and assigns an internal doc ID. |
| Repository | Petabytes of compressed originals, a snapshot of the web. |
| Indexer (Caffeine) | Decompresses. (1) **Hit generation**: word, word ID, position, font size, colour, case. (2) **Link extraction** with anchor text. |
| Anchor system | Stores anchor text. |
| URL Resolver | Relative → absolute URLs; gives newly found URLs their own IDs. |
| Links system | Builds the web's link map → feeds PageRank. |
| PageRank | Weights links using the map. |
| Doc Index | Short summary per page (length, embeddings) for millisecond lookups; feeds new internal-link URLs back to the URL Server. |
| Lexicon | Word ↔ word ID mapping for pages and queries (answers ~85% of known queries quickly). |
| Barrels | Forward index: doc ID → word IDs. |
| Sorter | Inverts it: word ID → doc IDs (the inverted index used for ranking). |
| Searcher | Builds the SERP from PageRank, Doc Index, Lexicon and Barrels. Ranking is the final real-time step. |

"200 ranking signals" is a made-up number. Learn ~19–20 signals; daily work relies on 5–6.

### 10 ways Google discovers URLs
1. Sitemap (.xml/.txt)
2. Internal links (these take the long route: crawler → store → indexer → doc index → URL server)
3. External links
4. URL guessing (pattern gaps, e.g. /121, /122, [123?], /125)
5. Open server/error logs
6. New sites: homepage of a newly connected domain/CMS
7. Domain registrars (the instructor's claim: Google buys lists of new registrations; not documented by Google)
8. RSS feeds
9. GSC URL submission (goes straight to the URL Server)
10. JS rendering (reveals more links)

## Class 3: How ChatGPT and Perplexity work

- LLMs are prediction engines: they predict the next most probable word. Computers only handle numbers, so "Large Numbers Model" would be the more accurate name. A lookup table maps words to numbers, the model computes, and the output maps back to words.
- ChatGPT is an app (a wrapper). The models are GPT-4, GPT-4.5, GPT-5 and so on.
- Training: **pre-training** (patterns from massive text) → **SFT** (Q&A pairs teach it to answer) → **RLHF** (safety: refuse harmful requests).
- N-gram models needed ~2.5B calculations per word (50k × 50k). **Transformers** ("Attention Is All You Need", Google) attend to context and prune irrelevant words, cutting that to millions.
- **Vectors/embeddings**: score an entity's properties (0–1) across hundreds or thousands of dimensions. Distance between vectors = relatedness (dentist ~ doctor, far from farmer). Turning queries, keywords and pages into embeddings and optimising their closeness is the basis of **semantic SEO**.
- **RAG** (retrieval-augmented generation) lets a model use new information without retraining. *Static RAG* feeds in updated documents; *real-time RAG* searches the web live (Perplexity).
  - Build: chunk documents → embedding model → vector DB.
  - Answer: embed the query → retrieve from the vector DB **and** run a live search → the LLM gets query + DB chunks + live results → answer.

## Classes 4–7: Google Search Console

### Setup
- GSC is low-memory, low-performance, low-priority (it's free), so expect lag and bugs.
- Add both a **Domain** property (verify with a DNS TXT record; URL-prefix properties then auto-verify) and **URL-prefix** properties.

### Overview and Performance
- The Overview shows Performance, Indexing, Experience and Recommendations. Data takes time to show up after connecting; the default range is 3 months, the max is 16 months.
- GSC counts **Google Search clicks only**. Onward navigation inside the site is Google Analytics territory.
- Impression = URL shown in results. Click = impression + click. CTR = clicks/impressions. Sitewide average position isn't worth focusing on.
- Filters: date/compare (use YoY for seasonal businesses), search type (web/image/video/news; image matters for photographers), query (contains / not contains / exact → split branded vs non-branded), page (single or pattern), country, device (e.g. mobile traffic to B2B SaaS means off-hours research, so look at those queries), search appearance (review/product snippets).
- A query with high impressions and low clicks, or with no dedicated page, is an opportunity: build or improve a page for it. Click a query to see its pages, or a page to see its queries.
- `site:domain` gives a rough indexed count, never an exact one.

### Indexing (see the SKILL.md table)
Status codes, soft 404, canonical variants, redirect, noindex, discovered/crawled not indexed, video watch pages.

### Sitemaps
HTML sitemaps are for humans only. Google accepts XML, RSS/Atom and TXT. Limits are 50k URLs / 50 MB; use a sitemap index above that. `loc` and `lastmod` are used; `priority` is ignored. "Couldn't fetch" usually clears in minutes to a day; if it doesn't, run URL Inspection. "Sitemap appears to be HTML" is usually a caching plugin caching the XML, so exclude sitemaps from caching. Exports cap at 1,000 rows (2,000 via API). Don't routinely use Request Indexing. A forced recrawl (delete and resubmit sitemaps) is for major changes only; abusing it gets future requests ignored.

### Removals
Temporary removal takes ~2–3 h and lasts 6 months. Permanent removal needs the page deleted so it returns 404. A prefix removal takes out a whole pattern. Other options: Clear cached snippet, the Outdated content tab (user reports) and the SafeSearch filtering tab.

### Experience
- CWV: Mobile and Desktop reports, URLs rated Good / Needs improvement / Poor, based on **field data**. Indexing is mobile-first. Test with PageSpeed Insights. CWV used to be a tiebreaker; now it feeds "helpfulness", so extremely slow pages can be demoted.
- HTTPS tab: SSL issues, useful across subdomains on different hosts.

### Settings
- **robots.txt report**: current file, fetch status, request recrawl, version history (catches accidental edits).
- **Crawl Stats** (last 3 months): total requests, download size, average response time (< 200 ms ideal, ≤ 500 ms, < 1000 ms max), host status (robots.txt fetch, DNS, connectivity; errors here mean talk to the host), breakdown by response code (304 = unchanged; asset 404s should stay ≤ 10%), purpose (Discovery vs Refresh; low discovery while publishing a lot = crawl budget issue).
- **Manual actions**: penalties, with a review request. **Security issues**: by the time you see one, rankings are usually already hurt. Shopping/Enhancements come from schema.
- **Users and permissions**: Restricted / Full / Owner. Full sees everything but can't add users. Delegated owner = someone shared access with you; verified owner = you verified it yourself.
- **Ownership history**: the property's full access log, useful for disputes.
- **Unused ownership tokens**: remove them, or ex-owners can re-gain access.
- **Associations**: other properties (GA, YouTube) link *to* GSC, and you accept the requests there.
- **Change of Address**: see the migration playbook in SKILL.md. A 180-day probation, cancellable.
- **Bulk data export → BigQuery**: paid (~$20–40, plus query costs), unsampled, kept forever. GSC keeps 16 months, Bing 24.

## Class 8: Domain and hosting

- A domain maps an IP to a human-readable name. Google treats TLDs mostly equally; ccTLDs (.in) suit local audiences. Keep names short and memorable, with no numbers, hyphens or deliberate misspellings (Google may "correct" users to the famous brand).
- Hosting options: shared (cheap, bad-neighbour risk), VPS, dedicated (full control, costly), managed WordPress (good but restrictive). Site files live in `public_html`. "Unlimited" plans still cap inodes, so check File Usage in cPanel.
- cPanel: install SSL (SSL/TLS, Let's Encrypt); run the latest stable PHP 8.x via Select PHP Version with zlib and redis enabled; domain email accounts.

## Class 9: WordPress

- **Posts** are chronological blog entries in the archive; use them for news and updates. **Pages** are hierarchical; use them for services, about and categories (parent > child).
- Settings: HTTPS in the WordPress and Site Address fields; site language matching the content language (sets the HTML `lang`); Reading → static homepage; Permalinks → Post name (saving Permalinks unchanged flushes .htaccess and fixes many 404 and sitemap errors); Discussion → comments off for business sites.
- Security: delete unused themes and plugins; hide the login URL (WPS Hide Login); disable file editing in `wp-config.php` (`DISALLOW_FILE_EDIT`).

## Class 10: Shopify and Wix

- Both are closed SaaS: less freedom, less maintenance.
- Shopify: SEO fields sit at the bottom of each product page. Product, Breadcrumb and Merchant schema are built in. The `/products/` and `/collections/` prefixes are fixed, and changing a handle changes the URL (add a redirect). Yoast for Shopify is paid only.
- Wix: a dedicated SEO Settings panel with bulk meta editing, a per-page noindex toggle, custom schema in the Advanced tab, and robots.txt editable in the dashboard.

## Class 11: Google ranking systems

- Positive systems (16 documented): **BERT** (context around words, which killed keyword stuffing), **crisis information** (authoritative sources in emergencies), **deduplication** (limits one site crowding the results; also canonicalises featured snippets), **exact-match domain** (dampened), **freshness** (QDF topics such as news and weather, not evergreen), **PageRank** (one signal among many), **neural matching** (concepts over keywords), **RankBrain** (concepts and entities), and others.
- From the leak or internal documents: **NavBoost** (click and dwell data), small personal sites (a diversity boost), "Baby Panda" (a spam/quality filter).
- Negative: **removals** (legal or personal-information takedowns) override SEO.

## Class 12: Keyword research

- Query = what users type. Keyword = what you target.
- Short-tail: high volume, low conversion, vague intent; good for branding. Long-tail: low volume, high conversion; best for beginners.
- Intents: navigational ("facebook login"), informational ("how to tie a tie"), commercial ("best dentist in Delhi"), transactional ("buy iPhone 15 online").
- Start with 3–6 word, medium-difficulty keywords. Use Autosuggest and Keyword Planner (free) before paid tools like Semrush.

## Class 14: Content writing with AI

- Google penalises **scaled content abuse**, not AI itself.
- Zero-shot prompts give generic output; one-shot or few-shot (with tone/style examples) is much better. Always tell the model to "collect facts first and cite sources".
- Formats: guides (H1 → H2 steps → H3 details → CTA), case studies (challenge → solution → result → testimonial), pillar pages (hub linking to clusters, which builds topical authority).

## Class 15: Publishing content

- H1 is for users (on-page); the meta title is for the SERP. Keep them similar, not necessarily identical.
- The H1 should be the biggest text on the page. CTAs should be large, in a contrasting colour.
- Images: descriptive filename, alt text describing the image, a caption explaining why it's there.
- Forms: a single "Full Name" field; labels, not placeholder-only.

## Class 16: Meta tags and X-Robots-Tag

- Meta title and description are your billboard. Google rewrites them about 70% of the time.
- Robots values: `noindex`, `nofollow`, `none`, `nosnippet`, `max-snippet:N`, `indexifembedded`, `unavailable_after`.
- `X-Robots-Tag` in the HTTP header (.htaccess) applies the same rules to PDFs and images.

## Class 17: robots.txt

- It's the first file a bot requests, at root only. Syntax: `User-agent`, `Disallow`, `Allow`.
- Course: when Allow and Disallow both apply, Google takes the least restrictive (Allow). **Docs say:** the most specific (longest path) rule wins; Allow only wins ties.
- Don't Disallow a page to deindex it; use noindex and leave it crawlable.
- A 404 robots.txt means crawl everything; a 5xx means Google stops crawling.

## Class 18: Crawl budget

= how many pages Googlebot can and wants to crawl. It matters at 10k+ pages. It's hurt by slow servers (high TTFB) and by low-value or duplicate pages. Fixes: real 404/410 for deleted pages, block parameter URLs in robots.txt, remove redirect chains.

## Class 19: Sitemaps

XML is standard; TXT, RSS and Atom are also accepted. 50k URLs / 50 MB uncompressed per file, with a sitemap index beyond that. `<loc>` is required, `<lastmod>` is respected, and `<priority>`/`<changefreq>` are ignored. For "appears to be HTML", exclude the sitemap from caching.

## Class 20: .htaccess

It's a hidden file in `public_html` (turn on Show Hidden Files). Uses: server-level 301s (faster than plugins), URL rewriting, blocking IPs and bots, custom 404/410 pages, and headers such as X-Robots-Tag. Back it up before every edit, because one typo takes the site down. Use AI to write redirect regex.

## Classes 21–22: Internal linking

- Reasonable Surfer: main-content links outweigh footer and sidebar links, and higher links outweigh lower ones.
- Pillar (hub) → cluster (guides, stories) → money page, with links near the top at each step, funnels authority to the money page.
- Don't link to low-value pages (Contact Us) from the prime spots on money pages.

## Classes 23–25: Schema

- Use JSON-LD in `<head>`.
- Organization: `sameAs` pointing to socials and directories (IndiaMART, Justdial) builds the knowledge graph.
- LocalBusiness: exact NAP. Can be extended with properties like service area and founder.
- Product: name, image, brand, SKU, offers (price, availability), aggregateRating.
- Article/NewsArticle for posts.
- Breadcrumb: the course says to leave out the homepage and start at the first category.
- The course's "supercharging" tip is to put extra keywords in the schema `description`. **Docs say:** structured data must reflect visible content, and mismatches risk a manual action. Avoid it.
- Tools: technicalseo.com to generate, the Rich Results Test to validate, and "Schema & Structured Data for WP" or `header.php` to implement.

## Class 26: Enhancement reports

These live under GSC Enhancements/Shopping. Invalid = critical error, so no rich result. Valid with warnings = eligible, but can be improved. Click an error to see the missing field (e.g. `price`), and only press Validate Fix after fixing. Schema that conflicts with the page (e.g. the wrong hiring organisation in a JobPosting) can trigger a manual action.

## Classes 27–28: Core Web Vitals

- **LCP** < 2.5 s: preload the LCP image, don't lazy-load it, use WebP, cut TTFB; splitting large text blocks shrinks the LCP element.
- **CLS** < 0.1: explicit image width and height; `min-height` for ads, banners and dynamic content.
- **INP** < 200 ms (replaced FID): cut heavy JS and CPU work; simplify the DOM and CSS.
- Lab data (PSI simulation) is for debugging. Field data (CrUX, real users) is what affects ranking.
