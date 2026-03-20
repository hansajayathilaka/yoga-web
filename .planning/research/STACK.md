# Stack Research

**Domain:** Zero-cost static yoga teacher website (SSG + Git CMS + Netlify)
**Researched:** 2026-03-21
**Confidence:** MEDIUM — Training data verified against known release history through Aug 2025. Version numbers marked individually. Web verification was unavailable during this research session; flag versions for confirmation before pinning in package.json.

---

## Recommended Stack

### Core Technologies

| Technology | Version | Purpose | Why Recommended |
|------------|---------|---------|-----------------|
| Astro | ^5.x (latest 5.x) | Static site generator, routing, content collections | Island architecture keeps JS payload near-zero. Content Collections API (stable since v2, greatly improved in v5) is purpose-built for Markdown-driven blogs and galleries. Largest free template ecosystem among SSGs in 2025. |
| Decap CMS | ^3.x | Git-based CMS admin UI | Open-source, zero-cost, commits Markdown directly to GitHub. Native Netlify Identity integration means no custom OAuth. The only Git CMS with a first-class Cloudinary media library widget. Rebranded from Netlify CMS; actively maintained by Decap org. |
| Netlify (hosting) | Free tier (Starter) | CI/CD, hosting, forms, identity | Free tier covers 100 GB bandwidth/month, 300 build minutes/month — far beyond a yoga studio's needs. Provides three critical zero-cost services this stack depends on: Netlify Identity (CMS auth), Netlify Forms (contact form backend), and automatic GitHub deploy hooks. |
| Cloudinary | Free tier (25 credits/month) | Image hosting and optimization | Prevents Git repo bloat from high-res event photos. Free tier provides ~25 GB storage + transformations. Decap CMS has a first-party Cloudinary media library widget — images upload via CMS UI, get stored in Cloudinary, and URLs land in Markdown frontmatter. |
| Tailwind CSS | ^3.4.x | Utility-first styling | Ships as Astro's recommended styling approach. Most free Astro templates use Tailwind. Generates minimal CSS (purge-on-build). Avoids writing custom SCSS for a site that should be template-based. |

**Confidence:** Astro ^5.x — MEDIUM (v5 released late 2024, active in 2025; exact patch unknown without web access). Decap ^3.x — MEDIUM (v3 GA confirmed through training data). Netlify/Cloudinary free tiers — HIGH (stable, well-documented, unlikely to change).

---

### Supporting Libraries

| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| `@astrojs/mdx` | bundled with Astro | MDX support for blog posts | Use if teacher wants to embed interactive components in blog posts. Skip if plain Markdown is sufficient — it usually is. |
| `@astrojs/image` | deprecated — use Astro's built-in `<Image>` | Image optimization | Astro's native `<Image>` component (built-in since v3) handles WebP conversion and lazy loading. Do not install `@astrojs/image` separately — it was merged into core. |
| `@astrojs/sitemap` | bundled integration | Auto-generate sitemap.xml | Add to `astro.config.mjs` integrations. Improves SEO for a local Sri Lanka yoga business with no extra effort. |
| `decap-cms-app` | ^3.x | Decap CMS bundle (CDN alternative) | Prefer loading Decap via CDN script tag in `admin/index.html` rather than npm install — keeps build lighter and ensures you always get the patched version. Only use npm package if you need custom widgets. |
| `netlify-identity-widget` | ^1.9.x | Netlify Identity JS client | Must be loaded on every page (not just admin) so the CMS redirect-after-login works. Load via CDN or npm. Required for the `window.netlifyIdentity` hook that Decap CMS listens for. |

**Confidence:** `@astrojs/sitemap` — HIGH. `netlify-identity-widget` pattern — HIGH. MDX integration — HIGH.

---

### Development Tools

| Tool | Purpose | Notes |
|------|---------|-------|
| Node.js 20 LTS | Astro build runtime | Astro 5.x requires Node >= 18.17.1. Node 20 LTS is the safe choice. Pin version in `.nvmrc` or `engines` field in package.json. |
| `npm` or `pnpm` | Package manager | Astro's own docs use npm. pnpm is faster and has stricter hoisting — fine for Astro, but template starters default to npm. Use whichever is already on the developer machine. |
| Astro VS Code Extension | Syntax highlighting, IntelliSense for `.astro` files | Install `astro-build.astro-vscode`. Required for comfortable development; without it `.astro` files are unsyntaxed. |
| Prettier + `prettier-plugin-astro` | Code formatting | Keeps `.astro` templates readable. Add `"plugins": ["prettier-plugin-astro"]` to `.prettierrc`. |
| Netlify CLI | Local testing of Netlify Identity + Forms | `npm install -g netlify-cli`. Run `netlify dev` instead of `astro dev` to test Identity redirects and form submissions locally — `astro dev` alone cannot emulate these. |

**Confidence:** Node 20 requirement — HIGH. Netlify CLI for local Identity testing — HIGH (this is a critical dev workflow note).

---

## Astro Template Recommendations

This project should start from a template, not from scratch. The teacher's design needs are standard: hero, blog list, gallery grid, about, contact.

### Primary Recommendation: AstroWind

**Repo:** `withastro/astrowind` (GitHub)
**Why:** AstroWind is the most-starred free Astro template (~10k+ stars as of training cutoff). It includes hero section, features grid, blog with pagination, about page, and contact form stub — covering all v1 requirements. Uses Tailwind CSS. Well-maintained.
**Limitation:** Slightly corporate/SaaS aesthetic out of the box; requires visual customization (colors, fonts, imagery) to feel like a yoga brand.
**Confidence:** MEDIUM (popularity confirmed through training; verify it still receives updates before adopting).

### Alternative: Astro Starter Blog

**Command:** `npm create astro@latest -- --template blog`
**Why:** Official Astro starter. Minimal, well-structured, easy to extend. Best choice if the developer prefers building the gallery and landing sections from scratch rather than customizing a heavier template.
**When to use:** When AstroWind feels too opinionated and the developer wants full layout control.
**Confidence:** HIGH (official template, maintained by Astro core team).

### Alternative: Accessible Astro Starter

**Repo:** `markteekman/accessible-astro-starter`
**Why:** Strong accessibility posture (WCAG 2.1 AA), which matters for a public-facing wellness site. Includes blog, navigation, and modular components. Less polished visually but more adaptable.
**Confidence:** MEDIUM.

**What NOT to use for templates:**
- WordPress themes ported to Astro — usually poorly maintained, dependency-heavy
- Paid Astro themes — violates zero-cost constraint and unnecessary for this scope
- Hugo themes — wrong SSG entirely; don't cross-convert

---

## Decap CMS Configuration Patterns

### Collection Structure for This Project

```yaml
# admin/config.yml (skeleton — not production-ready)

backend:
  name: git-gateway
  branch: main

media_folder: "src/assets/uploads"   # ONLY for small non-photo assets
public_folder: "/assets/uploads"
# NOTE: For the gallery, override media_library to use Cloudinary (see below)

media_library:
  name: cloudinary
  config:
    cloud_name: YOUR_CLOUD_NAME
    api_key: YOUR_API_KEY

collections:
  # Static pages (file collection)
  - name: "pages"
    label: "Pages"
    files:
      - name: "home"
        label: "Home Page"
        file: "src/content/pages/home.md"
        fields:
          - { label: "Hero Tagline", name: "tagline", widget: "string" }
          - { label: "Hero Subtitle", name: "subtitle", widget: "text" }
          - { label: "CTA Button Text", name: "cta_text", widget: "string" }
      - name: "about"
        label: "About Page"
        file: "src/content/pages/about.md"
        fields:
          - { label: "Title", name: "title", widget: "string" }
          - { label: "Bio", name: "body", widget: "markdown" }
          - { label: "Profile Photo", name: "photo", widget: "image" }

  # Blog posts (folder collection)
  - name: "blog"
    label: "Blog Posts"
    folder: "src/content/blog"
    create: true
    slug: "{{year}}-{{month}}-{{day}}-{{slug}}"
    fields:
      - { label: "Title", name: "title", widget: "string" }
      - { label: "Publish Date", name: "pubDate", widget: "datetime" }
      - { label: "Cover Image", name: "cover", widget: "image" }
      - { label: "Description", name: "description", widget: "text" }
      - { label: "Body", name: "body", widget: "markdown" }
      - { label: "Tags", name: "tags", widget: "list", default: ["yoga"] }

  # Gallery entries (folder collection)
  - name: "gallery"
    label: "Gallery"
    folder: "src/content/gallery"
    create: true
    fields:
      - { label: "Event Name", name: "title", widget: "string" }
      - { label: "Date", name: "date", widget: "datetime" }
      - { label: "Photos", name: "photos", widget: "list",
          field: { label: "Photo", name: "image", widget: "image" } }
      - { label: "Description", name: "description", widget: "text" }
```

**Key pattern:** "file" collections for singleton pages (home, about) and "folder" collections for repeating content (blog, gallery). This is the standard Decap CMS two-collection-type model.

**Confidence:** HIGH — this is the canonical Decap CMS pattern documented in official Decap docs.

---

## Cloudinary Integration

### Free Tier Limits (as of training cutoff)

| Limit | Value | Adequate for yoga site? |
|-------|-------|------------------------|
| Storage | 25 GB | YES — hundreds of event photos |
| Monthly transformations | 25 credits (~25,000 transformations) | YES — well within needs |
| Bandwidth | 25 GB/month | YES for typical yoga studio traffic |
| Products/account | 1 | YES |

**Confidence:** MEDIUM — Cloudinary periodically revises free tier limits. Verify current limits at cloudinary.com/pricing before launch.

### Integration Method

Decap CMS uses Cloudinary as a media library widget, not as a custom upload endpoint. The setup is:

1. Create a free Cloudinary account and get `cloud_name` and `api_key`
2. Add `media_library` block to `admin/config.yml` (see pattern above)
3. In Cloudinary dashboard: Settings > Security > Allowed fetch domains — add your Netlify domain
4. No backend code required — Decap handles the upload via the Cloudinary Upload Widget JS

**Important:** The `api_key` in `config.yml` is the public API key (safe to commit). Never put the `api_secret` in any frontend file. Cloudinary's Upload Widget uses unsigned uploads or upload presets — configure an unsigned upload preset in Cloudinary dashboard.

**Confidence:** HIGH — this is the documented Decap CMS Cloudinary widget pattern.

---

## Netlify Identity Setup (Single-User CMS Access)

### Flow

```
Teacher visits /admin → Netlify Identity widget intercepts →
Teacher logs in (email + password, no OAuth needed) →
Decap CMS loads with git-gateway backend →
Teacher edits content → Decap commits Markdown to GitHub →
Netlify detects push → rebuilds site → teacher sees change live
```

### Setup Steps (for STACK.md — detailed implementation goes in roadmap)

1. **Enable Netlify Identity** in Netlify dashboard: Site Settings > Identity > Enable
2. **Set registration to "Invite only"** — do not leave open registration enabled
3. **Enable Git Gateway** in Netlify dashboard: Identity > Services > Git Gateway > Enable
4. **Invite the teacher** via email from Identity dashboard — he sets his own password
5. **Add Identity widget script** to every page layout (not just admin):
   ```html
   <script src="https://identity.netlify.com/v1/netlify-identity-widget.js"></script>
   ```
6. **Add redirect hook** to site's main `<script>` (usually in a layout component):
   ```javascript
   if (window.netlifyIdentity) {
     window.netlifyIdentity.on("init", user => {
       if (!user) {
         window.netlifyIdentity.on("login", () => {
           document.location.href = "/admin/";
         });
       }
     });
   }
   ```

**Why step 6 matters:** Without the redirect hook, after login the teacher lands on the homepage, not the CMS admin panel. This is the most commonly missed step in Decap + Netlify Identity setups.

**Confidence:** HIGH — documented in both Netlify Identity and Decap CMS official docs.

---

## Netlify Forms Setup

For the contact page — zero backend required:

```html
<form name="contact" method="POST" data-netlify="true" netlify-honeypot="bot-field">
  <input type="hidden" name="form-name" value="contact" />
  <p hidden><input name="bot-field" /></p>
  <!-- form fields -->
</form>
```

**Critical:** The `data-netlify="true"` attribute must be present in the **deployed HTML** — not injected by JavaScript. Netlify's build bot scrapes static HTML to register forms. Forms rendered only by client-side JS are not detected.

In Astro, place the form in a `.astro` component (server-rendered HTML) — do not put it inside a React/Vue island that renders on the client.

**Confidence:** HIGH.

---

## Alternatives Considered

| Recommended | Alternative | When to Use Alternative |
|-------------|-------------|------------------------|
| Astro | Hugo | Hugo is faster to build (Go-based). Choose Hugo if build times become an issue (unlikely for a yoga site with <100 pages) or if the developer is already Hugo-fluent. |
| Astro | Next.js | Next.js is React-based and overkill for a static brochure site. Introduces unnecessary complexity and larger bundle sizes. |
| Decap CMS | Tina CMS | Tina has a better editing experience (in-context visual editing) but requires a cloud backend (Tina Cloud) for the free tier. Adds dependency risk. Use Tina if the teacher finds Decap's separate admin panel confusing after launch. |
| Decap CMS | Forestry/Siteleaf | Both are deprecated or sunset. Do not use. |
| Decap CMS | Contentful/Sanity | Both have free tiers but are API-based (not Git-based), require more setup, and Contentful's free tier has content limits. Overkill for this project. |
| Netlify | Vercel | Vercel lacks Netlify Identity (would need Auth0 or similar). Netlify Forms also unavailable on Vercel. Vercel is the better choice for Next.js apps, not for Decap CMS + static sites. |
| Netlify | GitHub Pages | No form handling, no Identity service, no serverless functions. Fine for pure static but doesn't meet this project's zero-backend CMS auth requirement. |
| Cloudinary | Git LFS | LFS costs money beyond the free threshold and is opaque to non-technical users. Cloudinary's UI is simpler for a teacher uploading photos. |
| Cloudinary | Imgur/public CDN | Not manageable via CMS, no organization, no transformations. |

---

## What NOT to Use

| Avoid | Why | Use Instead |
|-------|-----|-------------|
| `@astrojs/image` (npm package) | Deprecated and removed — merged into Astro core as built-in `<Image>` component in v3+. Installing it causes build errors on Astro 4+. | Astro built-in `<Image>` and `<Picture>` components |
| WordPress | Violates zero-cost constraint (hosting costs $) and zero-dev-needed-post-launch goal. The teacher cannot commit Markdown from a WP admin without custom plugin work. | Astro + Decap CMS |
| React/Vue/Svelte SPA | Unnecessary client-side JS for a content site. Kills Lighthouse scores and complicates Netlify Forms (forms must be in static HTML). | Astro `.astro` components with islands only where interactivity is genuinely needed |
| Netlify CMS (npm package branded as such) | The package `netlify-cms-app` is the old name. The actively maintained package is `decap-cms-app`. The Netlify-branded version stopped receiving updates when it was rebranded. | `decap-cms-app` or CDN-loaded Decap CMS |
| Open registration on Netlify Identity | Any visitor could sign up as a CMS editor. | Set to "Invite only" immediately after enabling Identity |
| Storing full-res photos in Git repo | Git history will balloon to gigabytes quickly. Clones become slow, Netlify build minutes increase, repo becomes unmaintainable. | Cloudinary via Decap CMS media library widget |

---

## Version Compatibility

| Package | Compatible With | Notes |
|---------|-----------------|-------|
| Astro ^5.x | Node >= 18.17.1 (recommended: Node 20 LTS) | Node 16 is EOL — do not use |
| Astro ^5.x | Tailwind CSS ^3.4.x | Use `@astrojs/tailwind` integration. Tailwind v4 alpha exists but is not stable for production as of training cutoff. |
| Decap CMS ^3.x | Netlify Identity widget ^1.9.x | These two are designed to work together. No compatibility issues at these versions. |
| Decap CMS ^3.x | Cloudinary media library | Requires `decap-cms-media-library-cloudinary` or is bundled — check Decap v3 release notes; in v2 it was a separate package `netlify-cms-media-library-cloudinary`. In v3, Cloudinary support may be built in. **Flag for verification.** |
| `netlify-identity-widget` ^1.9.x | Modern browsers | No IE11 support needed for a 2025 site — fine. |

**Version verification note:** All versions above reflect training knowledge through August 2025. Before scaffolding the project, run `npm create astro@latest` and check the scaffolded `package.json` for Astro's own recommended version of its integrations.

---

## Installation

```bash
# Scaffold from AstroWind template
npm create astro@latest -- --template withastro/astrowind yoga-web
cd yoga-web

# Or scaffold from official blog starter (simpler baseline)
npm create astro@latest -- --template blog yoga-web

# Add Astro integrations
npx astro add tailwind
npx astro add sitemap
npx astro add mdx   # only if MDX needed for blog

# Netlify Identity widget (load via CDN is preferred, but npm option:)
npm install netlify-identity-widget

# Netlify CLI for local dev with Identity + Forms emulation
npm install -g netlify-cli
```

**Decap CMS — do not npm install for standard setup.** Create `public/admin/index.html` and `public/admin/config.yml` manually. Load Decap from CDN:

```html
<!-- public/admin/index.html -->
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Content Manager</title>
    <script src="https://identity.netlify.com/v1/netlify-identity-widget.js"></script>
  </head>
  <body>
    <script src="https://unpkg.com/decap-cms@^3.0.0/dist/decap-cms.js"></script>
  </body>
</html>
```

This is the canonical Decap CMS setup. Using unpkg CDN means no npm dependency, no build step for the CMS admin, and automatic patch-version updates.

---

## Sources

- Training knowledge (Astro v1–v5 release history, through Aug 2025) — MEDIUM confidence on exact versions
- Decap CMS official documentation patterns (decapcms.org) — reflected in training — HIGH confidence on configuration patterns
- Netlify Identity + Decap CMS integration pattern — HIGH confidence (canonical, widely documented)
- Cloudinary media library widget for Decap — HIGH confidence on integration method; MEDIUM on current free tier limits
- Netlify Forms static HTML requirement — HIGH confidence (official Netlify behavior)

**Verification recommended before project start:**
- Confirm Astro current version: `npm create astro@latest` or https://astro.build
- Confirm Decap v3 Cloudinary bundling: https://decapcms.org/docs/cloudinary/
- Confirm Netlify free tier limits: https://www.netlify.com/pricing/
- Confirm Cloudinary free tier limits: https://cloudinary.com/pricing

---

*Stack research for: Zero-cost yoga teacher website — Astro + Decap CMS + Netlify*
*Researched: 2026-03-21*
