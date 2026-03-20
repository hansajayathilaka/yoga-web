# Feature Research

**Domain:** Yoga teacher / independent wellness instructor website
**Researched:** 2026-03-21
**Confidence:** MEDIUM — WebSearch and WebFetch were unavailable during this session. Findings draw on training data (through August 2025) grounded in general wellness/yoga website conventions and validated against the PROJECT.md scope decisions already made. Low-confidence items are flagged explicitly.

---

## Feature Landscape

### Table Stakes (Users Expect These)

Features users assume exist on any yoga teacher website. Missing these causes visitors to leave or distrust the site.

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| Hero section with clear identity | Visitors need to know immediately: who is this person, what do they teach, where are they based | LOW | Full-viewport image or video, tagline, single CTA button. Calm/natural imagery is the genre convention. |
| Teacher bio / About page | Trust is the primary conversion lever for yoga — students choose a teacher, not just a service | LOW | Photo, credentials, years of experience, teaching philosophy. 27 yrs + 385 students is strong social proof — must surface this. |
| Contact information visible | Students need to reach the teacher to ask about classes or pricing | LOW | Phone + email + location at minimum. WhatsApp is critical in Sri Lanka — more than email for the local audience. |
| Contact form | Reduces friction vs. copying an email address; Netlify Forms makes this zero-cost | LOW | Name, email/phone, message. Netlify Forms handles backend; no server needed. |
| Mobile-responsive design | 70–80% of traffic to local wellness sites in South Asia arrives on mobile (LOW confidence — unverified stat) | LOW | Must be tested on Android Chrome. Not a differentiator — it is a baseline expectation in 2025. |
| Clear location / service area | Students need to know if classes are within reach before investing time | LOW | Kuliyapitiya base + 3 branches (Kuliyapitiya, Kurunegala, Colombo) should be listed. Embed Google Maps or a simple address block. |
| Class / schedule information | The primary conversion action — "can I join a class?" needs an answer | MEDIUM | Even a static list of class types, days, and contact-to-enroll CTA covers this for v1. Full timetable with CMS management is v2. |
| Page load speed | Visitors on mobile data in Sri Lanka will leave a slow site within 3 seconds | LOW | Static site (Astro) gives this almost for free; image optimization (Cloudinary) is the only risk area. |
| SSL / HTTPS | Browsers show security warnings on non-HTTPS sites; users abandon | LOW | Netlify provides this automatically on free tier. Zero effort. |

### Differentiators (Competitive Advantage)

Features that distinguish Arjuna's site from a generic yoga directory listing or a competitor's minimal website.

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| Photo gallery of real events | Builds authenticity and trust. Stock photos are a red flag in this genre; real event photos show real students and real activity | MEDIUM | Decap CMS + Cloudinary integration. Teacher uploads via CMS; images served optimized from Cloudinary CDN. Event albums (not just a grid) add context. |
| Blog / knowledge-sharing articles | Positions Arjuna as an authority, improves SEO for searches like "yoga therapy Sri Lanka", creates return visits | MEDIUM | Decap CMS folder collection with rich-text editor. Teacher writes in a familiar UI; publishes as Markdown. Tag/category support valuable but not required for v1. |
| Instructor profiles (multi-teacher) | Shri Yogi Foundation has 3 instructors. Showing all three signals a real organization, not a one-person hobby project | LOW | Static data in Decap CMS or simple YAML data file. Photo, name, specialization per instructor. |
| Yoga styles / specializations listed | "Hatha Vinyasa, Yin Yoga, Meditation, Pranayama, Yoga Philosophy" — specificity attracts serious students and filters out poor-fit inquiries | LOW | Static content in About page or a dedicated section. No CMS complexity needed; rarely changes. |
| WhatsApp CTA | Sri Lankan audience overwhelmingly uses WhatsApp for first contact. A click-to-WhatsApp button outperforms a form for local conversion | LOW | A single `<a href="https://wa.me/94789702702">` link. No backend. Extremely high local value. |
| Social proof numbers | "27 years experience, 385+ students, 3 branches" — quantified credibility that most yoga sites skip | LOW | Surface in hero or About section as a stat bar. Static content, easy to maintain. |
| Sri Lanka / Sinhala audience localization | A site that feels locally grounded (imagery, language options, cultural resonance) will outperform a generic Western yoga aesthetic | MEDIUM | At minimum: correct timezone, local contact norms (WhatsApp), Sri Lankan imagery. Bilingual content (English + Sinhala) is a strong differentiator if content is available — but adds CMS complexity. |
| CMS-managed class timetable (v2) | Enables teacher to update schedule without developer; critical once class content is ready | MEDIUM | Decap CMS structured collection with fields: class name, day, time, duration, level, instructor. Frontend renders as a responsive timetable grid. Deferred to v2 per PROJECT.md scope decision. |

### Anti-Features (Commonly Requested, Often Problematic)

Features that seem appealing but create cost, complexity, or maintenance burden for a zero-cost independent teacher site.

| Feature | Why Requested | Why Problematic | Alternative |
|---------|---------------|-----------------|-------------|
| Online booking / booking system | Reduces friction to register for a class | Requires a backend, database, or paid third-party service (Calendly, Acuity). Breaks the zero-cost constraint. Ongoing maintenance burden. | "Contact to book" CTA with WhatsApp / contact form. Sufficient for small class sizes. |
| E-commerce / payment processing | Sell class packs or merchandise online | Stripe / payment gateways require backend logic, PCI compliance handling, and ongoing account management. Well outside zero-cost scope. | Direct bank transfer or cash, coordinated via WhatsApp. Common and expected for local Sri Lanka micro-businesses. |
| Student login / member portal | Students access exclusive content or track attendance | Requires auth system, database, user management. Massive complexity leap. Netlify Identity is lightweight but not designed for multi-user student portals. | Share resources publicly on the blog or via a WhatsApp group. |
| Live chat widget | Instant responsiveness looks professional | Third-party widgets (Intercom, Drift) add significant JS weight and cost after free tier. The teacher cannot monitor chat full-time — unanswered chats damage trust more than not having chat. | Prominent WhatsApp button. Sets correct expectations (async response). |
| Video streaming / embedded classes | Online yoga classes or recordings | Large video files cannot be committed to Git. CDN streaming costs money at scale. | Embed YouTube unlisted videos. Zero cost, no storage concern, works on all devices. |
| Newsletter subscription system | Build a mailing list | Requires a backend email service (Mailchimp free tier is viable but adds a third-party dependency and GDPR/privacy considerations). | Link to a Google Form or use the existing contact form for "notify me" requests. Add Mailchimp only when content cadence is proven. |
| Instagram/social media auto-feed | Show latest Instagram posts on site | Third-party widgets that pull Instagram feeds have repeatedly broken as Meta changes their API. Maintenance nightmare. | Static gallery managed in CMS. Teacher controls which photos appear and how they're organized. |
| Google Maps embed with custom pins | Fancy multi-location map | Heavy iframe, potential GDPR cookie banner triggers, API key management. | Static text addresses with a plain "Get Directions" link to Google Maps URL. Achieves the same goal with zero complexity. |

---

## Feature Dependencies

```
[Contact Form]
    └──requires──> [Netlify Forms setup]
                       └──requires──> [Netlify deployment]

[Photo Gallery]
    └──requires──> [Cloudinary account + Media Library config in Decap CMS]
                       └──requires──> [Decap CMS admin panel]
                                          └──requires──> [Netlify Identity]

[Blog]
    └──requires──> [Decap CMS folder collection (blog)]
                       └──requires──> [Decap CMS admin panel]
                                          └──requires──> [Netlify Identity]

[CMS-managed Class Timetable] (v2)
    └──requires──> [Decap CMS structured collection (classes)]
                       └──requires──> [Decap CMS admin panel]  ← already built in v1

[WhatsApp CTA] ──enhances──> [Contact Page]
[Social proof stats] ──enhances──> [Hero Section]
[Instructor Profiles] ──enhances──> [About Page]

[Blog] ──enhances──> [SEO / organic discovery]
[Gallery] ──enhances──> [Social proof / trust]
```

### Dependency Notes

- **Decap CMS admin panel is the shared prerequisite:** Both the blog and gallery depend on it. It must be built before either content type is usable by the teacher.
- **Netlify Identity is required before the CMS is usable:** The teacher cannot log into the CMS admin without Netlify Identity configured and an invitation sent.
- **Cloudinary integration is required before the gallery is usable:** Without it, image uploads go to the Git repo (bloat risk). Cloudinary config must be set up in the same phase as the gallery.
- **WhatsApp CTA requires no dependencies:** It is a static link. Can be added at any stage.
- **Class timetable is explicitly deferred (v2):** It shares the CMS dependency, so once v1 CMS is live, v2 timetable can be added without rebuilding infrastructure.

---

## MVP Definition

### Launch With (v1)

Minimum set that makes the site useful and trustworthy for prospective students.

- [ ] Hero section — establishes identity, builds immediate trust
- [ ] About page — teacher bio, credentials, social proof numbers (27 yrs, 385 students, 3 branches)
- [ ] Contact page — Netlify form + WhatsApp CTA + phone/email/address
- [ ] Decap CMS admin panel + Netlify Identity — teacher can log in and manage content
- [ ] Blog collection — teacher can publish articles via CMS
- [ ] Photo gallery — event photos managed via Decap CMS + Cloudinary
- [ ] Mobile-responsive design — tested on Android Chrome
- [ ] Instructor profiles section — signals a real organization (can be static initially)

### Add After Validation (v1.x)

Features to add once v1 is live and the teacher is actively using the CMS.

- [ ] Tag/category filtering on blog — add when there are 10+ articles to organize
- [ ] Yoga styles / specializations detail page — add when teacher wants to attract specific student segments
- [ ] Sinhala language content — add if teacher identifies local-language traffic as valuable
- [ ] Google Analytics or Plausible — add once site is live to understand traffic patterns

### Future Consideration (v2+)

Features to defer until the site is validated and the teacher is comfortable managing content.

- [ ] CMS-managed class timetable — content not ready; add in v2 per PROJECT.md decision
- [ ] Mailchimp newsletter integration — add only when teacher has consistent content cadence
- [ ] YouTube video embeds for recorded classes — add if online teaching becomes a goal
- [ ] Bilingual (English + Sinhala) full site — significant content and CMS complexity; defer until v1 proves value

---

## Feature Prioritization Matrix

| Feature | User Value | Implementation Cost | Priority |
|---------|------------|---------------------|----------|
| Hero section | HIGH | LOW | P1 |
| About page | HIGH | LOW | P1 |
| Contact form + WhatsApp CTA | HIGH | LOW | P1 |
| Mobile responsive design | HIGH | LOW | P1 |
| Decap CMS + Netlify Identity | HIGH | MEDIUM | P1 |
| Photo gallery (Cloudinary) | HIGH | MEDIUM | P1 |
| Blog | HIGH | MEDIUM | P1 |
| Instructor profiles | MEDIUM | LOW | P1 |
| Social proof stat bar | MEDIUM | LOW | P1 |
| Class timetable (CMS-managed) | HIGH | MEDIUM | P2 |
| Blog tags / categories | LOW | LOW | P2 |
| Yoga styles detail page | MEDIUM | LOW | P2 |
| Analytics | MEDIUM | LOW | P2 |
| Sinhala language support | MEDIUM | HIGH | P3 |
| Mailchimp newsletter | LOW | MEDIUM | P3 |
| YouTube video embeds | MEDIUM | LOW | P3 |

**Priority key:**
- P1: Must have for launch
- P2: Should have, add when possible
- P3: Nice to have, future consideration

---

## Competitor Feature Analysis

Note: Direct competitor analysis was not possible in this session (WebSearch and WebFetch unavailable). The following is based on knowledge of the Sri Lankan yoga/wellness website landscape through August 2025. Confidence: LOW — verify against actual competitor sites before finalizing design decisions.

| Feature | Typical small yoga site (SL) | Typical global yoga template | Our Approach |
|---------|-------------------------------|-------------------------------|--------------|
| Hero section | Full-page image, generic tagline | Full-page video or image, generic | Real photo of Arjuna teaching + specific tagline with location |
| Contact | Phone + email only; no form | Contact form, no WhatsApp | Form + WhatsApp button + phone/email |
| Gallery | Often absent or Instagram embed | Grid of stock/event photos | CMS-managed event albums via Cloudinary |
| Blog | Absent on most small SL sites | Present but infrequently updated | CMS-enabled so teacher can publish without developer |
| Class schedule | Static text table, often outdated | CMS-managed or booking widget | v1: static with contact CTA; v2: CMS-managed timetable |
| Instructor bios | Usually just the main teacher | Main teacher only | All 3 instructors listed (signals organization scale) |
| Social proof | Rarely quantified | Testimonials common | Stat bar (years, students, branches) + testimonial section (future) |
| Mobile | Varies widely; some sites break | Generally responsive | Mobile-first, tested on Android Chrome |

---

## Hero / Landing Patterns — Detailed Notes

The hero section is the highest-stakes design decision on a yoga teacher site. Based on training-data knowledge of the genre (MEDIUM confidence):

**What works:**
- Single full-viewport image of the teacher actively teaching or in a pose — not stock photography
- Calm, warm color palette (earth tones, greens, soft whites) — cold blues or dark themes feel wrong for this genre
- Tagline that names the specific benefit ("Find balance, strength, and peace through authentic yoga therapy") rather than a generic slogan
- One prominent CTA — "Book a Class" or "Contact Us" — not multiple competing buttons
- Location visible in or immediately below the hero ("Kuliyapitiya, Sri Lanka") — establishes local relevance instantly

**What to avoid:**
- Autoplay video — slow on mobile data, drains battery, frustrates users
- Multiple CTAs competing for attention
- Stock photography — immediately erodes trust in a "personal teacher" context
- Animated carousels with multiple slides — low engagement, high complexity

---

## Gallery UX Best Practices — Detailed Notes

Based on training-data knowledge of gallery UX (MEDIUM confidence):

**What works:**
- Organized by event/album rather than a single undifferentiated grid — gives context ("Yoga Retreat 2024", "Colombo Branch Opening")
- Lightbox/modal for full-size viewing — avoid navigating away from the gallery page
- Lazy loading — critical for mobile performance when viewing large numbers of photos
- Cloudinary image transformations — auto-format (WebP on supporting browsers), responsive srcset, quality auto — these are built-in and should be used

**What to avoid:**
- Storing originals in Git repo — even a few high-res JPEGs will bloat the repo and slow Netlify builds
- Infinite scroll without pagination — difficult to manage with a static site generator
- Lightbox libraries that add heavy JS — prefer a lightweight option (e.g., simple CSS lightbox or GLightbox at ~10KB)

---

## Blog / Knowledge Sharing — Detailed Notes

Based on training-data knowledge of yoga blog best practices (MEDIUM confidence):

**What works:**
- Articles that demonstrate expertise: yoga philosophy, pranayama techniques, benefits of specific practices
- Short-form posts (500–1000 words) — teacher can write these without heavy time investment
- Cover image per post — Decap CMS supports this; Cloudinary handles the image
- Author attribution — always show Arjuna's name and photo on posts (trust signal)
- Reading time estimate — minor UX enhancement, low complexity

**What to avoid:**
- Complex tag/category taxonomy at launch — premature organization for a blog with 5–10 posts
- Comments section — requires moderation, spam risk, adds backend dependency
- SEO keyword-stuffing — Sri Lankan audience will find this through social sharing and WhatsApp more than Google in early phase

---

## Contact Form Patterns — Detailed Notes

Based on training-data knowledge of contact UX and Netlify Forms (HIGH confidence for Netlify-specific behavior):

**What works:**
- Short form: name, phone/email (one field), message — fewer fields = higher completion rate
- WhatsApp CTA alongside the form — gives users choice of channel
- Immediate feedback on submission: "Thank you, we'll respond within 24 hours"
- Netlify Forms `data-netlify="true"` attribute — zero backend required, submissions appear in Netlify dashboard and can trigger email notifications

**What to avoid:**
- CAPTCHA on the form unless spam becomes a problem — adds friction unnecessarily
- Requiring both phone AND email — choose one; phone is more relevant for Sri Lankan audience
- Embedding a full Google Map iframe — cookie/GDPR banner trigger risk; plain address + "Get Directions" link is sufficient

---

## Sources

Note: External sources were not accessible during this session (WebSearch and WebFetch permissions denied). Findings are based on:

- Training data through August 2025 covering yoga/wellness website conventions, Astro, Decap CMS, Netlify, and Cloudinary documentation
- PROJECT.md for validated project context, scope decisions, and teacher profile
- CLAUDE.md for the agreed architecture and feature mapping
- General UX research knowledge on form design, gallery patterns, and hero section conventions

**Confidence by section:**
- Table stakes features: MEDIUM (well-established conventions; no novel claims)
- Differentiators: MEDIUM (grounded in project context + domain knowledge)
- Anti-features: HIGH (zero-cost constraint is hard; Netlify Forms and WhatsApp behavior are well-documented)
- Hero/Gallery/Blog/Contact detailed notes: MEDIUM
- Competitor analysis table: LOW (no direct competitor research performed)

**Recommended verification:**
- Browse 5–10 actual Sri Lankan yoga teacher websites to validate gallery, contact, and class schedule conventions for the local market
- Check yogatherapylk.com (current site) to understand what the teacher is used to and what patterns existing students expect

---

*Feature research for: Yoga teacher website — Shri Yogi Foundation*
*Researched: 2026-03-21*
