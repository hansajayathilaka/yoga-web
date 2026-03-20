# Roadmap: Yoga Therapy — Shri Yogi Foundation Website

## Overview

Five phases to take the project from an empty repository to a live, self-managed website. Phase 1 establishes the deployment pipeline so every subsequent phase has a testable live URL. Phase 2 builds the visible site — all static pages, branding, and contact — before any CMS complexity is introduced. Phase 3 wires in Decap CMS, Netlify Identity, and Git Gateway in a single focused session, which is the highest-risk phase. Phase 4 builds the blog and photo gallery on top of the CMS infrastructure. Phase 5 audits performance against real content and hands the site off to the teacher.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [x] **Phase 1: Infrastructure** - Scaffold AstroWind project locally and push to GitHub (Netlify deferred to Phase 5) (completed 2026-03-20)
- [ ] **Phase 2: Static Pages** - All visible pages built, branded, mobile-responsive, and deployed
- [ ] **Phase 3: CMS Integration** - Decap CMS, Netlify Identity, and Git Gateway wired and verified end-to-end
- [ ] **Phase 4: Blog and Gallery** - Blog and photo gallery live, CMS-managed, and populated with real content
- [ ] **Phase 5: Polish and Launch** - Performance audited, teacher onboarded, site handed off

## Phase Details

### Phase 1: Infrastructure
**Goal**: A working AstroWind project scaffolded locally and pushed to GitHub, ready for subsequent phases to build upon
**Depends on**: Nothing (first phase)
**Requirements**: INFRA-01, INFRA-02, INFRA-03, INFRA-04, INFRA-05
**Note**: INFRA-01 is partially addressed (GitHub repo created). INFRA-02 through INFRA-05 (Netlify auto-deploy, HTTPS/domain, Netlify Identity, Git Gateway) are deferred to Phase 5 per user decision.
**Success Criteria** (what must be TRUE):
  1. AstroWind-based Astro project builds locally without errors (`npm run build` exits 0)
  2. Project is committed on the `main` branch and pushed to a GitHub repository
  3. Node.js version is pinned to 20 via .nvmrc
**Plans**: 1 plan

Plans:
- [x] 01-01-PLAN.md — Scaffold AstroWind project and push to GitHub

### Phase 2: Static Pages
**Goal**: Every public-facing page is visible, correctly branded, and fully functional without a CMS
**Depends on**: Phase 1
**Requirements**: PAGE-01, PAGE-02, PAGE-03, PAGE-04, PAGE-05, PAGE-06, PAGE-07, PAGE-08, PAGE-09, UX-01, UX-02, UX-03
**Success Criteria** (what must be TRUE):
  1. Visitor lands on the homepage and sees teacher name, tagline, location, a real photo of Arjuna, and a CTA button
  2. Visitor can read the About page and see the bio, social proof stats (27 years / 385+ students / 3 branches), all three instructor profiles, and the list of yoga styles taught
  3. Visitor can reach the Contact page and find the phone number, email, physical address, and a WhatsApp button that opens a chat to the teacher's number
  4. Navigation bar links correctly to Home, About, Gallery, Blog, and Contact on both desktop and Android Chrome mobile
  5. All pages pass a visual check on a mobile viewport — no horizontal scroll, no cut-off text, no broken layout
**Plans**: TBD

Plans:
- [ ] 02-01: Build hero section, navigation, and global layout with Tailwind branding (calm/warm yoga palette)
- [ ] 02-02: Build About page — bio, stat bar, instructor profiles, yoga styles list
- [ ] 02-03: Build Contact page — contact details, WhatsApp CTA, placeholder gallery and blog stubs

### Phase 3: CMS Integration
**Goal**: The teacher can log into /admin/, edit content, publish, and see changes go live — without developer help
**Depends on**: Phase 2
**Requirements**: CMS-01, CMS-02, CMS-03, CMS-04, CMS-05
**Success Criteria** (what must be TRUE):
  1. Navigating to /admin/ shows the Decap CMS login screen (not a 404 or blank page)
  2. Teacher logs in with email and password via Netlify Identity and reaches the CMS dashboard
  3. Teacher edits the About page bio in the CMS, publishes, and the live site reflects the change within 3 minutes
  4. Teacher uploads a test photo via the CMS media library and the image appears in the live site (stored in the GitHub repository)
  5. Teacher can create a draft blog post via the CMS rich-text editor and publish it without any error messages
**Plans**: TBD

Plans:
- [ ] 03-01: Add public/admin/ directory with config.yml — wire backend, pages file collection, and about/home content fields
- [ ] 03-02: Invite teacher via Netlify Identity, test full invite-confirm-login-edit-publish flow end-to-end
- [ ] 03-03: Wire Decap CMS blog folder collection and gallery folder collection; verify media_folder paths

### Phase 4: Blog and Gallery
**Goal**: Teacher can publish blog articles and upload event photos via the CMS, and visitors can read and browse them on the live site
**Depends on**: Phase 3
**Requirements**: BLOG-01, BLOG-02, BLOG-03, BLOG-04, GALLERY-01, GALLERY-02, GALLERY-03, GALLERY-04, GALLERY-05
**Success Criteria** (what must be TRUE):
  1. Visitor opens the Blog page and sees a list of published articles, each showing cover image, title, excerpt, and date
  2. Visitor clicks an article and reads the full post with title, cover image, publish date, and rich-text body
  3. Visitor opens the Gallery page and sees all uploaded photos in a responsive grid with captions
  4. Visitor clicks a gallery photo and it opens in a lightbox for full-size viewing
  5. Teacher publishes a new blog post and uploads a new gallery photo via the CMS, and both appear on the live site without developer involvement
**Plans**: TBD

Plans:
- [ ] 04-01: Build Astro blog collection — src/content/blog/, [slug].astro dynamic route, blog listing page
- [ ] 04-02: Build Astro gallery collection — src/content/gallery/, gallery grid page with lightbox support
- [ ] 04-03: Populate with real content — seed 1-2 blog posts and 6-10 gallery photos with teacher's actual material

### Phase 5: Polish and Launch
**Goal**: The site loads fast on mobile, the teacher is confident managing it independently, and the site is publicly announced
**Depends on**: Phase 4
**Requirements**: (UX requirements verified against real content; no new v1 requirements — all assigned to earlier phases)
**Success Criteria** (what must be TRUE):
  1. Lighthouse mobile performance score is 80 or above (tested with real gallery photos loaded)
  2. Teacher completes a full CMS walkthrough — publishes a blog post, adds a gallery photo, updates the About bio — without asking for help
  3. Submitting the contact form results in a submission appearing in the Netlify Forms dashboard
  4. sitemap.xml is accessible at /sitemap.xml and lists all public pages
**Plans**: TBD

Plans:
- [ ] 05-01: Performance audit — check LCP on gallery page, verify image optimization, add sitemap integration
- [ ] 05-02: Teacher onboarding session — walkthrough CMS workflow, verify contact form submissions, final cross-browser check

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2 → 3 → 4 → 5

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Infrastructure | 1/1 | Complete   | 2026-03-20 |
| 2. Static Pages | 0/3 | Not started | - |
| 3. CMS Integration | 0/3 | Not started | - |
| 4. Blog and Gallery | 0/3 | Not started | - |
| 5. Polish and Launch | 0/2 | Not started | - |
