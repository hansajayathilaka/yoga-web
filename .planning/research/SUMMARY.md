# Project Research Summary

**Project:** Yoga Teacher Website — Shri Yogi Foundation
**Domain:** Zero-cost static brochure site with Git-based CMS (independent wellness instructor)
**Researched:** 2026-03-21
**Confidence:** MEDIUM

---

## Executive Summary

This is a zero-cost, self-managed brochure site for an independent yoga teacher with 27 years of experience operating across three locations in Sri Lanka. The well-established pattern for this class of site is Astro (static site generator) + Decap CMS (Git-based CMS) + Netlify (hosting, auth, forms) + Cloudinary (image CDN). All four components are free within their stated tiers and are purpose-built to work together. The stack requires no server, no database, and no recurring cost. The teacher manages content through a browser-based admin panel that commits Markdown files to GitHub, which triggers an automatic site rebuild — no developer involvement after initial setup.

The recommended approach is to start from an existing Astro template (AstroWind or the official blog starter), wire in Decap CMS with Netlify Identity for authentication, and configure Cloudinary as the media library before the teacher uploads a single photo. The feature set for v1 is well-scoped: hero section, about page, contact form with WhatsApp CTA, blog, and photo gallery. The class timetable is explicitly deferred to v2 because the content is not ready at launch. This scope is achievable in a single focused build sprint and produces a site that is genuinely more capable than most Sri Lankan yoga teacher sites currently online.

The primary risks are infrastructure configuration mistakes, not code complexity. Netlify Identity, Git Gateway, and Cloudinary must all be activated and verified end-to-end before the teacher is onboarded. Skipping or deferring any of these three setup steps causes compounding failures that are harder to untangle later. The pitfalls research identifies six critical failure modes — all preventable with a structured setup checklist executed in the correct order.

---

## Key Findings

### Recommended Stack

The Astro + Decap CMS + Netlify + Cloudinary stack is the canonical answer for a zero-cost, Git-backed content site in 2025–2026. Each component fulfills a distinct role with no overlap and no paid dependency. Astro's island architecture keeps JavaScript near-zero on the public site, which is essential for mobile users in Sri Lanka on variable data connections. Decap CMS commits Markdown directly to GitHub, keeping the teacher's content under version control with full history and easy recovery. Netlify provides three critical free services in one platform: CI/CD builds, Netlify Identity (CMS authentication), and Netlify Forms (contact form backend). Cloudinary prevents Git repository bloat by keeping binary image files entirely out of the repository.

**Core technologies:**
- **Astro ^5.x**: Static site generator with Content Collections API — purpose-built for Markdown-driven, near-zero-JS sites; largest free template ecosystem of any SSG
- **Decap CMS ^3.x**: Git-based CMS admin UI — commits Markdown to GitHub, no server required; native Netlify Identity integration; first-class Cloudinary media widget
- **Netlify (free Starter tier)**: Hosting + CI/CD + Netlify Identity (CMS auth) + Netlify Forms (contact backend) — all four services needed are free on one platform
- **Cloudinary (free tier)**: External image CDN — 25 GB storage, 25 GB bandwidth/month; prevents repo bloat; auto-optimizes images via URL parameters
- **Tailwind CSS ^3.4.x**: Utility-first styling — default on most Astro templates; generates minimal CSS; avoids custom SCSS for a template-based site
- **Node.js 20 LTS**: Build runtime — required by Astro 5.x (minimum Node 18.17.1); pin in `.nvmrc`

**Template recommendation:** Start from AstroWind (most-starred free Astro template, covers all v1 UI needs) or the official Astro blog starter (simpler baseline, full layout control). Do not start from scratch.

**Critical version note:** Verify current Astro and Decap versions at project start (`npm create astro@latest`). Training data is through August 2025; patch versions may have advanced.

### Expected Features

The research confirms that the v1 scope maps cleanly to what users of yoga teacher sites expect as table stakes and what distinguishes this site from local competitors.

**Must have (table stakes) — v1:**
- Hero section with real teacher photo and location (Kuliyapitiya, Sri Lanka) — first-impression trust signal
- About page with bio, credentials, and quantified social proof (27 years, 385+ students, 3 branches)
- Contact page — Netlify form + WhatsApp CTA (`wa.me` link) + phone/email/address; WhatsApp is the primary first-contact channel for the Sri Lankan audience
- Mobile-responsive design tested on Android Chrome — 70–80% of local traffic is mobile
- SSL/HTTPS — automatic on Netlify free tier
- Blog with CMS-managed posts — positions teacher as authority, enables SEO for "yoga therapy Sri Lanka" queries
- Photo gallery — real event photos build authenticity; stock photos erode trust in this genre
- Decap CMS admin panel with Netlify Identity — teacher can self-manage all content without developer
- Instructor profiles for all three instructors — signals a real organization

**Should have (competitive differentiators):**
- WhatsApp click-to-chat button — outperforms a contact form for local conversion; single `<a href="wa.me/...">` link, zero complexity
- Social proof stat bar (years, students, branches) — most local competitors skip quantified credibility
- Cloudinary-backed gallery with album/event organization (not an undifferentiated grid) and lightbox viewing
- Blog with cover images and author attribution on every post

**Defer (v2+):**
- CMS-managed class timetable — content not ready; infrastructure already built in v1 so adding in v2 is a low-effort extension
- Blog tag/category filtering — not needed until 10+ posts exist
- Bilingual English/Sinhala content — significant CMS and content complexity; defer until v1 proves value
- Mailchimp newsletter integration — add only after teacher has proven content cadence
- Analytics (Plausible or GA) — add after launch to understand traffic patterns

**Anti-features to avoid:** Online booking systems, e-commerce, student login portals, live chat widgets, Instagram auto-feed embeds, and autoplay video in the hero. All break the zero-cost constraint, add backend complexity, or create ongoing maintenance burden.

### Architecture Approach

The architecture follows a four-layer static pattern: an authoring layer (Decap CMS browser app + direct Git commits), a repository layer (GitHub as single source of truth), a build layer (Netlify running `astro build` on every push), and a delivery layer (Netlify CDN serving static HTML/CSS/JS). Images flow through a separate Cloudinary CDN and are never stored in Git. The content authoring flow from teacher edit to live site takes 1–3 minutes end-to-end with no developer involvement.

**Major components:**
1. **GitHub repository** — single source of truth; all Markdown content, Astro components, and CMS config live here
2. **Decap CMS (`public/admin/`)** — browser SPA served at `/admin`; teacher's content management interface; commits via Netlify Git Gateway
3. **Netlify Identity** — handles CMS authentication; requires explicit activation and invite-only registration mode
4. **Astro Content Collections (`src/content/`)** — type-safe data layer; Zod schemas validate every Markdown frontmatter field at build time; prevents runtime surprises
5. **Astro Pages (`src/pages/`)** — route generation; dynamic `[slug].astro` files use `getStaticPaths()` to pre-render one HTML page per blog post and gallery album
6. **Netlify Forms** — contact form backend; requires `data-netlify="true"` in static HTML output, not in client-side JS
7. **Cloudinary** — binary image storage and CDN; wired to Decap CMS via `media_library` config block; images served with URL-parameter optimization (`f_auto,q_auto,w_800`)

**Key architectural constraint:** CMS config (`public/admin/config.yml`) must be in `public/`, not `src/`. Astro processes `src/` through its build pipeline; `public/` is copied verbatim. This is a common structural mistake with hard-to-debug consequences.

**Recommended build sequence:** scaffold → static pages (no CMS) → Netlify Forms → Decap CMS admin + Identity + Git Gateway → wire CMS to pages → blog collection → Cloudinary + gallery → polish. Each step produces a deployable site, making problems easier to isolate.

### Critical Pitfalls

Six critical failure modes were identified, all concentrated in the infrastructure and CMS integration phases:

1. **Netlify Identity not enabled before CMS testing** — admin panel loads but login silently fails; enable Identity immediately after first deploy and set registration to "Invite only" before any other step
2. **Git Gateway not enabled** — teacher can log in but "Save" produces API errors; enable under Netlify Identity > Services > Git Gateway as part of the same setup session as Identity
3. **Identity widget missing from the site root layout** — teacher's confirmation email link redirects to `/`, not `/admin/`; without the widget on every page, account confirmation never completes; add the script tag and login redirect handler to `BaseLayout.astro`
4. **Cloudinary not configured before first teacher upload** — without Cloudinary wired in `config.yml`, images commit directly to Git; even 20–30 event photos bloat the repo past GitHub warnings; configure Cloudinary before teacher onboarding, verify with a test upload
5. **`backend.branch` in `config.yml` pointing to wrong branch** — CMS saves succeed but no Netlify deploy fires; match the branch value exactly to Netlify's configured production branch (typically `main`)
6. **`media_folder`/`public_folder` path mismatch** — images upload correctly but render as 404s on the live site; for Astro, the correct values are `media_folder: "public/images/uploads"` and `public_folder: "/images/uploads"`

---

## Implications for Roadmap

Based on the combined research, a four-phase structure is recommended. Each phase produces a deployable, testable output. Phases are ordered to follow the build sequence validated in ARCHITECTURE.md, to front-load infrastructure pitfalls, and to defer complexity until the foundation is proven.

### Phase 1: Infrastructure and Deployment Pipeline

**Rationale:** The entire project depends on Netlify CI/CD, GitHub, and the Astro build working correctly. Establishing this first means every subsequent phase has a live, testable deployment from day one. The six critical pitfalls are all infrastructure or CMS integration issues — proving the pipeline early surfaces them before any content work is done.

**Delivers:** GitHub repository connected to Netlify, automatic deploys on push, a working `netlify.toml`, Node 20 build environment, and a deployed placeholder homepage that proves the pipeline end-to-end.

**Addresses:** Pipeline stability; Netlify free tier limits verified; `output: 'static'` explicitly set in `astro.config.mjs`

**Avoids:** Pitfall 1 (Identity not enabled), Pitfall 6 (Git Gateway not enabled) — both must be activated as part of this phase, not deferred

### Phase 2: Static Pages, Branding, and Contact Form

**Rationale:** Build the visible site without CMS complexity first. Hard-coded content for hero, about, and contact pages validates the Astro template choice, Tailwind customization, and mobile responsiveness before the CMS layer is introduced. Netlify Forms is simpler to debug without CMS involvement.

**Delivers:** Hero section with teacher photo and tagline, About page with bio and social proof stats, Contact page with Netlify form and WhatsApp CTA, instructor profiles section, mobile-responsive layout across all pages, `robots.txt` with `/admin/` disallowed, form verified in Netlify dashboard

**Addresses:** All table-stakes features (hero, about, contact, mobile, SSL); WhatsApp CTA differentiator; social proof stat bar

**Uses:** AstroWind or blog starter template, Tailwind CSS, Netlify Forms with honeypot spam protection, `<Image>` component for static assets

**Avoids:** Netlify Forms pitfall (form must be in static HTML, not client-side JS); no Astro React/Vue islands on the contact page

### Phase 3: CMS Integration (Decap CMS + Netlify Identity + Cloudinary)

**Rationale:** This is the highest-risk phase and should be completed in a single focused session to avoid partial states. Netlify Identity, Git Gateway, and Cloudinary are all configured together, then verified end-to-end with test content before the teacher is invited. The Decap CMS admin must be working fully before blog and gallery content is added in Phase 4.

**Delivers:** `/admin` route serving Decap CMS; Netlify Identity enabled with invite-only registration; Git Gateway enabled; teacher invited and confirmed (full invite-confirm flow tested with a secondary email); `config.yml` with `pages` file collection wired to `src/content/pages/`; home and about page content editable from CMS; Cloudinary integrated and verified with a test image upload; `backend.branch` verified against Netlify production branch

**Addresses:** Teacher self-management capability; content ownership

**Avoids:** All six critical pitfalls — this phase is specifically designed around the PITFALLS.md checklist

**Verification gate (required before Phase 4):** Teacher logs in, edits the hero tagline, publishes, and the change is live on the site within 3 minutes. A test image upload confirms it lands in Cloudinary, not Git.

### Phase 4: Blog and Photo Gallery

**Rationale:** Both features share the CMS infrastructure built in Phase 3. Gallery requires Cloudinary (already live). Blog requires only the Decap CMS folder collection and Astro dynamic routes. Both are built together because they have the same dependency chain and can be developed in parallel.

**Delivers:** Blog folder collection (CMS + Astro `src/content/blog/` + `src/pages/blog/[slug].astro` + listing page); photo gallery folder collection with per-album pages, lightbox viewing, lazy loading; Cloudinary URL transformations applied for gallery thumbnails (`w_400,f_auto,q_auto`) and full-size images (`w_1200,f_auto,q_auto`); content schemas validated with Zod; draft field on blog posts; alt text field on all gallery images

**Addresses:** Blog differentiator (authority, SEO), gallery differentiator (authenticity, trust), mobile gallery with `object-fit: cover` for mixed portrait/landscape photos

**Uses:** Astro `getCollection()`, `getStaticPaths()`, Cloudinary URL transformation pattern

**Avoids:** Git image bloat (Cloudinary already wired); base64 images in Markdown; lightbox libraries over 15KB

### Phase 5: Polish, Performance, and Teacher Onboarding

**Rationale:** Performance audit and teacher handoff belong together at the end, once all content types are live and the site is populated with real content. Lighthouse scores should be run against real gallery photos to surface any Cloudinary optimization gaps.

**Delivers:** Lighthouse mobile score >80 (LCP in particular); `@astrojs/sitemap` integration generating `sitemap.xml`; form submission redirects to `/thank-you/` page; CMS field labels reviewed for plain-language clarity; teacher walkthrough of complete CMS workflow (blog post + gallery album + page edit); contact form email notification configured in Netlify; Netlify Deploy Previews enabled so teacher can preview before publishing

**Addresses:** Page load speed for mobile Sri Lankan audience; SEO for local yoga queries; teacher CMS confidence

**Avoids:** UX pitfalls (confusing CMS labels, missing form success state, unoptimized Cloudinary delivery)

### Phase Ordering Rationale

- Pipeline first because all six critical pitfalls are deployment and integration issues; surfacing them in Phase 1 prevents compounding failures
- Static pages before CMS because simpler scope is easier to debug; validates template and routing before introducing the CMS layer
- CMS integration as a single dedicated phase because partial CMS states (Identity without Git Gateway, or CMS without Cloudinary) are harder to diagnose than building everything in one session
- Blog and gallery together because they share the same CMS dependency and can be parallelized; building them in the same phase avoids redundant CMS config passes
- Polish last because performance optimization against real content is more accurate than against placeholder content

### Research Flags

Phases likely needing deeper research during planning:
- **Phase 3 (CMS Integration):** Decap CMS v3 Cloudinary widget bundling needs verification — training data notes it may have changed from a separate package (`netlify-cms-media-library-cloudinary`) to built-in; check `decapcms.org/docs/cloudinary/` before implementing
- **Phase 3 (CMS Integration):** Confirm current Decap CMS v3 exact version and any breaking changes from v2 config format before writing `config.yml`

Phases with standard, well-documented patterns (research-phase can be skipped):
- **Phase 1 (Infrastructure):** Netlify + GitHub + Astro CI/CD pipeline is extremely well-documented; follow official Netlify deploy guide
- **Phase 2 (Static Pages):** Astro routing, Tailwind, and Netlify Forms are all stable, high-confidence patterns
- **Phase 4 (Blog + Gallery):** Astro Content Collections with `getStaticPaths()` is the canonical Astro pattern; no novel ground

---

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | MEDIUM | Core choices (Astro, Decap, Netlify, Cloudinary) are HIGH confidence; exact version numbers are MEDIUM — verify at project start with `npm create astro@latest`; Cloudinary free tier limits are MEDIUM — verify at cloudinary.com/pricing |
| Features | MEDIUM | Table stakes and anti-features are HIGH confidence; competitor analysis (Sri Lankan yoga market) is LOW — WebSearch was unavailable; verify against 5–10 actual local competitor sites before finalizing design |
| Architecture | HIGH | Astro + Decap CMS + Netlify architecture is stable and unchanged since 2023; all patterns are from official documentation; component boundaries and data flow are well-established |
| Pitfalls | HIGH for integration pitfalls; MEDIUM for limits | The six critical pitfalls (Identity, Git Gateway, Cloudinary, branch config, media paths, root-page widget) are recurring, well-documented community issues — HIGH confidence; Cloudinary and Netlify free tier limits are MEDIUM — may have changed since training cutoff |

**Overall confidence:** MEDIUM — sufficient to begin implementation; gaps are narrow and addressable at project start

### Gaps to Address

- **Decap CMS v3 Cloudinary bundling:** Verify whether `decap-cms-media-library-cloudinary` is still a separate npm package or is now bundled into Decap v3 core; this affects the CMS config and install steps. Check `decapcms.org/docs/cloudinary/` before Phase 3.
- **Current Astro/Decap version pinning:** Run `npm create astro@latest` at project start and capture the scaffolded version numbers; do not pin based on training data alone.
- **Cloudinary free tier current limits:** Verify 25 GB storage / 25 GB bandwidth is still accurate at cloudinary.com/pricing; limits have historically changed without warning.
- **Local competitor research:** Browse 5–10 Sri Lankan yoga teacher sites before finalizing hero and gallery design decisions; training-data competitor analysis is LOW confidence for the local market.
- **Existing site audit:** Check yogatherapylk.com (teacher's current site) to understand what existing students expect and what content already exists in a usable format.

---

## Sources

### Primary (HIGH confidence)
- Astro Content Collections official docs (`docs.astro.build/en/guides/content-collections/`) — collection schemas, `getCollection()`, `getStaticPaths()` patterns
- Decap CMS configuration docs (`decapcms.org/docs/configuration-options/`) — file vs folder collections, CMS config structure
- Netlify Identity official docs — registration modes, Git Gateway activation, invite flow
- Netlify Forms official docs — `data-netlify="true"` behavior, static HTML requirement, honeypot pattern
- Astro + Decap CMS integration guide (`docs.astro.build/en/guides/cms/decap-cms/`) — canonical integration pattern

### Secondary (MEDIUM confidence)
- Training data on Astro v1–v5 release history (through Aug 2025) — version numbers
- Training data on Cloudinary free tier limits — 25 GB storage/bandwidth figures
- Training data on AstroWind template (~10k+ stars) — template recommendation
- Community-reported Decap CMS issues — `media_folder`/`public_folder` mismatch, missing root-page identity widget, wrong `backend.branch` — recurring patterns across GitHub issues

### Tertiary (LOW confidence)
- Training data on Sri Lankan yoga/wellness website landscape — competitor feature analysis; needs direct verification
- General yoga teacher site genre conventions — hero section patterns, gallery UX best practices; grounded in training data, not verified against current sites

---

*Research completed: 2026-03-21*
*Ready for roadmap: yes*
