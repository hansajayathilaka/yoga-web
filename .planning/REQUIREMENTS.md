# Requirements: Yoga Therapy Website

**Defined:** 2026-03-21
**Core Value:** The teacher can update content himself — without developer help — and every change goes live automatically.

---

## v1 Requirements

### Infrastructure & Deployment (INFRA)

- [ ] **INFRA-01**: Site is deployed to Netlify free tier from a GitHub repository
- [ ] **INFRA-02**: Every push to the main branch triggers an automatic Netlify rebuild and deploy
- [ ] **INFRA-03**: Site is served over HTTPS with a custom domain (yogatherapylk.com or similar)
- [ ] **INFRA-04**: Netlify Identity is enabled (invite-only mode) so the teacher can log into the CMS
- [ ] **INFRA-05**: Netlify Git Gateway is enabled so Decap CMS can commit content to GitHub

### CMS / Content Management (CMS)

- [ ] **CMS-01**: Decap CMS admin panel is accessible at `/admin/` on the live site
- [ ] **CMS-02**: Teacher can log into the CMS admin using email + password (Netlify Identity)
- [ ] **CMS-03**: Teacher can create, edit, and publish blog posts via the CMS rich-text editor
- [ ] **CMS-04**: Teacher can upload photos to the gallery via the CMS (images stored in the GitHub repository)
- [ ] **CMS-05**: Teacher can update the About page content (bio, description) via the CMS

### Static Pages (PAGE)

- [ ] **PAGE-01**: Hero section displays teacher name ("Yoga Therapy / Shri Yogi Foundation"), a tagline, location (Kuliyapitiya, Sri Lanka), and a single CTA button
- [ ] **PAGE-02**: Hero section includes a real photo of Arjuna teaching (not stock photography)
- [ ] **PAGE-03**: About page includes teacher bio, credentials, and teaching philosophy
- [ ] **PAGE-04**: About page includes a social proof stat bar (27 years experience, 385+ students, 3 branches)
- [ ] **PAGE-05**: About page lists all 3 instructors (Arjuna Wickramaarachchi, Keshara Lakshani, Chandana Bandara) with photo and role
- [ ] **PAGE-06**: About page lists yoga styles and disciplines taught (Hatha Vinyasa, Yin Yoga, Meditation, Pranayama, Yoga Philosophy, Mudra, Bandhas)
- [ ] **PAGE-07**: Contact page displays phone number (+94 78 970 27 02), email (yogatherapylk@gmail.com), and physical address
- [ ] **PAGE-08**: Contact page has a WhatsApp CTA button that opens a WhatsApp chat to the teacher's number
- [ ] **PAGE-09**: Navigation links to all main sections: Home, About, Gallery, Blog, Contact

### Blog (BLOG)

- [ ] **BLOG-01**: Blog listing page shows all published articles with cover image, title, excerpt, and publish date
- [ ] **BLOG-02**: Each blog post has a dedicated page with title, cover image, publish date, and full rich-text body
- [ ] **BLOG-03**: Blog posts are authored via Decap CMS with fields: title, excerpt, cover image, publish date, body (rich text)
- [ ] **BLOG-04**: Blog posts are stored as Markdown files in the GitHub repository

### Photo Gallery (GALLERY)

- [ ] **GALLERY-01**: Gallery page displays all photos in a responsive grid layout
- [ ] **GALLERY-02**: Each photo has a title/caption for context (managed via CMS)
- [ ] **GALLERY-03**: Photos are stored in the GitHub repository and served via Netlify CDN
- [ ] **GALLERY-04**: Gallery items are managed via Decap CMS with fields: photo, title/caption
- [ ] **GALLERY-05**: Gallery images support lightbox/modal for full-size viewing

### Design & UX (UX)

- [ ] **UX-01**: Site is fully mobile-responsive and tested on Android Chrome
- [ ] **UX-02**: Site loads fast on mobile data (static HTML, Cloudinary-optimized images)
- [ ] **UX-03**: Color palette and visual style reflects the yoga/wellness genre (calm, warm tones)

---

## v2 Requirements

Deferred to future release. Infrastructure exists in v1 to support these with minimal effort.

### Classes Timetable

- **CLASS-01**: Teacher can manage class schedule via Decap CMS (fields: class name, day, time, duration, level, instructor, location)
- **CLASS-02**: Classes page renders a responsive timetable from CMS data
- **CLASS-03**: Each class entry has a "Contact to Join" CTA linking to the contact page

### Blog Enhancements

- **BLOG-05**: Blog posts can be tagged by category/topic
- **BLOG-06**: Blog listing page can be filtered by tag
- **BLOG-07**: Blog posts show estimated reading time

### Contact Enhancements

- **CONTACT-01**: Contact form (Netlify Forms) added to contact page if direct contact proves insufficient

### Analytics

- **ANALYTICS-01**: Site traffic tracked (Plausible or Google Analytics — free tier)

### Image Migration to Cloudinary (v3)

- **CDN-01**: Cloudinary integrated as the Decap CMS media library replacing Git-stored images
- **CDN-02**: Existing Git-stored images migrated to Cloudinary CDN
- **CDN-03**: All new image uploads go to Cloudinary (not Git repo) after migration

---

## Out of Scope

| Feature | Reason |
|---------|--------|
| Online booking system | Requires backend/paid service; breaks zero-cost constraint |
| E-commerce / payment processing | Backend + PCI compliance; well outside scope |
| Student login / member portal | Netlify Identity not designed for multi-user student portals |
| Live chat widget | Paid after free tier; teacher cannot monitor 24/7 |
| Sinhala language content | Adds CMS complexity; defer until v1 proves value |
| Newsletter / email list | Adds third-party dependency; add only when content cadence is proven |
| Instagram / social media auto-feed | Meta API changes break these repeatedly; use static CMS gallery instead |
| Google Maps iframe embed | Cookie banner trigger risk; plain address + "Get Directions" link is sufficient |
| Video streaming | Large files; use YouTube embed if needed in future |
| Cloudinary CDN (v1/v2) | Images stored in Git repo for v1 and v2; Cloudinary migration planned for v3 |

---

## Traceability

Which phases cover which requirements. Updated during roadmap creation.

| Requirement | Phase | Status |
|-------------|-------|--------|
| INFRA-01 to INFRA-05 | TBD | Pending |
| CMS-01 to CMS-06 | TBD | Pending |
| PAGE-01 to PAGE-09 | TBD | Pending |
| BLOG-01 to BLOG-04 | TBD | Pending |
| GALLERY-01 to GALLERY-05 | TBD | Pending |
| UX-01 to UX-03 | TBD | Pending |
