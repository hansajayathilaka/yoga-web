# Yoga Therapy — Website Rebuild

## What This Is

A zero-cost, CMS-managed website for **Arjuna Wickramaarachchi** (Yoga Master, founder of the Shri Yogi Foundation), based in Kuliyapitiya, Sri Lanka. Built with Astro (SSG) + Decap CMS + Netlify so the teacher can update content independently without technical knowledge. The site serves dual goals: attracting new students and providing ongoing resources for the existing community.

## Core Value

The teacher can update content himself — without developer help — and every change goes live automatically.

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] Hero landing section with tagline, intro, and CTA
- [ ] About page (teacher bio, credentials, mission)
- [ ] Contact page with working Netlify form
- [ ] Photo gallery managed via Decap CMS (event/class photos, Cloudinary integration)
- [ ] Blog with rich-text editor (teacher writes articles, CMS publishes as Markdown)
- [ ] Decap CMS admin panel with Netlify Identity auth for teacher login
- [ ] Site deployed to Netlify with zero-cost hosting
- [ ] Git repository on GitHub triggering auto-deploy on content changes

### Out of Scope (v1)

- Classes timetable — deferred to v2; content not fully ready
- E-commerce / online booking — out of scope entirely (zero-cost constraint)
- Custom backend — Netlify handles forms/auth, no server needed
- User accounts for students — not needed in v1

## Context

- **Existing site**: yogatherapylk.com (powered by Macro Systems, not self-managed)
- **Teacher profile**: Arjuna Wickramaarachchi, 27 years of experience, 385+ students, 3 branches (Kuliyapitiya, Kurunegala, Colombo)
- **Organization name**: Shri Yogi Foundation
- **Contact**: yogatherapylk@gmail.com | +94 78 970 27 02
- **Location**: Kurunegala-Narammala-Madampe Rd, Kuliyapitiya, Sri Lanka
- **Content status**: Partially ready — some bio and photos exist, class schedule in progress
- **Yoga styles taught**: Hatha Vinyasa, Yin Yoga, Asana, Meditation, Mudra, Bandhas, Pranayama, Yoga Philosophy
- **Instructors**: Arjuna Wickramaarachchi (Founder), Keshara Lakshani, Chandana Bandara

## Constraints

- **Budget**: Zero cost — Netlify free tier, GitHub free, Cloudinary free tier, Decap CMS is open-source
- **Tech Stack**: Astro (SSG) + Decap CMS + Netlify + Cloudinary (images) — decided upfront
- **Maintainability**: Teacher must be able to update content via CMS UI; no coding required post-launch
- **Git-based workflow**: All content changes are Markdown commits to GitHub, triggering Netlify rebuild
- **Image storage**: v1 and v2 store images in the Git repo; Cloudinary migration planned for v3 once repo size becomes a concern

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Astro over Hugo | Larger template ecosystem, better component model, strong 2025 community | — Pending |
| Decap CMS over Contentful/Sanity | Git-based, zero-cost, integrates with Netlify Identity out of the box | — Pending |
| Netlify over Vercel/GitHub Pages | Decap CMS needs Netlify Identity; Netlify Forms for zero-backend contact | — Pending |
| Cloudinary deferred to v3 | v1/v2 use Git repo for images (simpler setup); migrate to Cloudinary in v3 when repo size warrants it | — Pending |
| Classes timetable deferred | Content not ready; landing + blog + gallery is sufficient for launch | — Pending |

---
*Last updated: 2026-03-21 after initialization*
