# Architecture Research

**Domain:** Static Site Generator + Git-based CMS (Astro + Decap CMS + Netlify)
**Researched:** 2026-03-21
**Confidence:** HIGH (stable, well-established stack; patterns unchanged since 2023)

---

## Standard Architecture

### System Overview

```
┌──────────────────────────────────────────────────────────────────────┐
│                         AUTHORING LAYER                               │
│  ┌──────────────────────┐         ┌──────────────────────────────┐   │
│  │   Decap CMS Admin UI  │         │  Direct Git commit (developer) │ │
│  │  /admin (browser app) │         │  (markdown files, config)     │  │
│  └──────────┬───────────┘         └──────────────┬───────────────┘   │
│             │ Netlify Identity auth               │                   │
└─────────────┼───────────────────────────────────-┼───────────────────┘
              │ Git commit (Markdown + frontmatter) │
              ▼                                     ▼
┌──────────────────────────────────────────────────────────────────────┐
│                        REPOSITORY LAYER                               │
│                     GitHub (source of truth)                          │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌────────────────────┐   │
│  │ src/     │  │ public/  │  │ admin/   │  │ netlify.toml       │   │
│  │ content/ │  │ (static) │  │ config.yml│  │ astro.config.mjs   │   │
│  └──────────┘  └──────────┘  └──────────┘  └────────────────────┘   │
└──────────────────────────────┬───────────────────────────────────────┘
                               │ webhook on push
                               ▼
┌──────────────────────────────────────────────────────────────────────┐
│                          BUILD LAYER                                  │
│                       Netlify Build Runner                            │
│  npm run build → astro build                                          │
│  ┌─────────────────────────────────────────────────────────────┐     │
│  │  Astro reads src/content/ → generates static HTML/CSS/JS    │     │
│  │  Each content entry → one HTML page (blog posts, gallery)   │     │
│  │  Collections queried in .astro components via getCollection()│     │
│  └─────────────────────────────────────────────────────────────┘     │
└──────────────────────────────┬───────────────────────────────────────┘
                               │ deploy to CDN
                               ▼
┌──────────────────────────────────────────────────────────────────────┐
│                          DELIVERY LAYER                               │
│              Netlify CDN (global edge network)                        │
│  ┌──────────────┐  ┌────────────────┐  ┌─────────────────────┐      │
│  │ Static pages │  │ Netlify Forms  │  │ Netlify Identity    │      │
│  │ (HTML/CSS/JS)│  │ (contact form  │  │ (CMS login auth)    │      │
│  │              │  │  submissions)  │  │                     │      │
│  └──────────────┘  └────────────────┘  └─────────────────────┘      │
└──────────────────────────────────────────────────────────────────────┘
                               │ image URLs
                               ▼
┌──────────────────────────────────────────────────────────────────────┐
│                         IMAGE LAYER                                   │
│                  Cloudinary (free tier, external CDN)                 │
│  - Teacher uploads photos via Decap CMS media library widget          │
│  - Cloudinary stores + serves optimized images (auto-format, resize)  │
│  - Git repo never receives binary image blobs                         │
└──────────────────────────────────────────────────────────────────────┘
```

### Component Responsibilities

| Component | Responsibility | Implementation |
|-----------|----------------|----------------|
| GitHub repo | Single source of truth for all content and code | Markdown files, `.astro` components, `config.yml` |
| Decap CMS (`/admin`) | GUI for teacher to write/edit content without coding | `index.html` + `config.yml` in `public/admin/` |
| Netlify Identity | Authentication for Decap CMS login | Enabled in Netlify dashboard; `netlify-identity-widget` script on site |
| Astro Content Collections | Type-safe content layer mapping Markdown to typed objects | `src/content/` directory + `src/content/config.ts` schema |
| Astro Pages | Route generation — one file per URL pattern | `src/pages/` directory, `[slug].astro` for dynamic routes |
| Astro Components | Reusable UI building blocks (Header, Footer, Card, etc.) | `src/components/` directory, `.astro` files |
| Netlify Build | Runs `astro build` on every Git push, produces static HTML | Configured via `netlify.toml` |
| Netlify CDN | Serves static files globally with edge caching | Automatic; no configuration needed |
| Netlify Forms | Captures contact form submissions without backend | `data-netlify="true"` attribute on `<form>` |
| Cloudinary | Stores and serves optimized images | Free account; Decap CMS media_library widget config |

---

## Recommended Project Structure

```
yoga-web/
├── public/
│   ├── admin/
│   │   ├── index.html          # Decap CMS entry point (loads CMS SPA)
│   │   └── config.yml          # CMS collection definitions (THE key config file)
│   ├── favicon.svg
│   └── robots.txt
│
├── src/
│   ├── content/
│   │   ├── config.ts           # Astro collection schema definitions (Zod)
│   │   ├── blog/               # One .md file per blog post
│   │   │   ├── yoga-basics.md
│   │   │   └── meditation-guide.md
│   │   ├── gallery/            # One .md file per gallery album/event
│   │   │   ├── workshop-2025.md
│   │   │   └── retreat-2025.md
│   │   └── pages/              # File collections for static pages
│   │       ├── home.md         # Hero, intro, CTA content
│   │       └── about.md        # Bio, credentials, mission
│   │
│   ├── pages/
│   │   ├── index.astro         # Home page (reads from content/pages/home.md)
│   │   ├── about.astro         # About page
│   │   ├── contact.astro       # Contact page (Netlify form)
│   │   ├── gallery/
│   │   │   ├── index.astro     # Gallery listing page
│   │   │   └── [slug].astro    # Individual album page (dynamic route)
│   │   └── blog/
│   │       ├── index.astro     # Blog listing page
│   │       └── [slug].astro    # Individual blog post page (dynamic route)
│   │
│   ├── components/
│   │   ├── layout/
│   │   │   ├── BaseLayout.astro    # HTML shell, head, header, footer
│   │   │   ├── Header.astro
│   │   │   └── Footer.astro
│   │   ├── home/
│   │   │   ├── HeroSection.astro
│   │   │   └── IntroSection.astro
│   │   ├── blog/
│   │   │   └── BlogCard.astro      # Post preview card for listing
│   │   ├── gallery/
│   │   │   └── GalleryGrid.astro   # Photo grid component
│   │   └── ui/
│   │       ├── Button.astro
│   │       └── ContactForm.astro   # Netlify form wrapper
│   │
│   └── styles/
│       └── global.css          # Global styles, CSS custom properties
│
├── astro.config.mjs            # Astro config (integrations, output: 'static')
├── netlify.toml                # Build command, publish dir, redirects
├── package.json
└── tsconfig.json
```

### Structure Rationale

- **`public/admin/`** — Decap CMS lives here because Astro serves `public/` as-is (no processing). The CMS is a static SPA that Netlify hosts at `yoursite.com/admin`.
- **`src/content/`** — Astro's dedicated content layer. Files here are not routes; they are data sources queried by pages. Schema validation happens at build time.
- **`src/content/config.ts`** — Central schema file. Defines what fields are valid in each collection using Zod. Prevents build if a Markdown file has missing required fields.
- **`src/pages/`** — Everything here becomes a URL. Dynamic routes (`[slug].astro`) use `getStaticPaths()` to pre-render one HTML page per collection entry.
- **`src/components/`** — No business logic here; pure presentation. Grouped by feature area, not by type (avoids flat component sprawl).

---

## Architectural Patterns

### Pattern 1: Astro Content Collections with Schema Validation

**What:** Define a Zod schema for each content type. Astro validates every Markdown frontmatter field at build time, giving type-safe access in `.astro` files.

**When to use:** Always — for every CMS-managed content type (blog, gallery, pages).

**Trade-offs:** Small upfront setup cost; pays back immediately with IDE autocomplete and build-time errors instead of runtime surprises.

**Example — `src/content/config.ts`:**
```typescript
import { defineCollection, z } from 'astro:content';

const blog = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string(),
    pubDate: z.date(),
    description: z.string(),
    author: z.string().default('Arjuna Wickramaarachchi'),
    coverImage: z.string().optional(),  // Cloudinary URL
    tags: z.array(z.string()).default([]),
    draft: z.boolean().default(false),
  }),
});

const gallery = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string(),
    eventDate: z.date(),
    description: z.string().optional(),
    coverImage: z.string(),             // Cloudinary URL (required)
    images: z.array(z.string()),        // Array of Cloudinary URLs
  }),
});

export const collections = { blog, gallery };
```

### Pattern 2: Dynamic Routes via `getStaticPaths()`

**What:** One `.astro` file generates many HTML pages — one per collection entry — at build time.

**When to use:** Blog posts, gallery albums — any collection where each entry needs its own URL.

**Trade-offs:** Zero server cost (static output); requires full rebuild when content changes (acceptable because Netlify builds are fast and free).

**Example — `src/pages/blog/[slug].astro`:**
```astro
---
import { getCollection } from 'astro:content';
import BaseLayout from '../../components/layout/BaseLayout.astro';

export async function getStaticPaths() {
  const posts = await getCollection('blog', ({ data }) => !data.draft);
  return posts.map(post => ({
    params: { slug: post.slug },
    props: { post },
  }));
}

const { post } = Astro.props;
const { Content } = await post.render();
---
<BaseLayout title={post.data.title}>
  <article>
    <h1>{post.data.title}</h1>
    <Content />
  </article>
</BaseLayout>
```

### Pattern 3: Decap CMS File Collection for Singleton Pages

**What:** A "file" collection in Decap CMS config maps a single YAML/Markdown file to one editable page. The teacher edits fields directly (no need to create/delete entries).

**When to use:** Home page hero text, about page bio, contact details — pages that exist once and are always edited, never created or deleted.

**Trade-offs:** Teacher sees a clean form (not a document list). Fields must be defined explicitly, which takes more CMS config writing upfront.

**Example — portion of `public/admin/config.yml`:**
```yaml
collections:
  - name: "pages"
    label: "Site Pages"
    files:
      - name: "home"
        label: "Home Page"
        file: "src/content/pages/home.md"
        fields:
          - { label: "Hero Tagline", name: "heroTagline", widget: "string" }
          - { label: "Hero Subtext", name: "heroSubtext", widget: "text" }
          - { label: "CTA Button Text", name: "ctaText", widget: "string" }
          - { label: "Introduction", name: "intro", widget: "markdown" }
          - { label: "Hero Image", name: "heroImage", widget: "image" }
      - name: "about"
        label: "About Page"
        file: "src/content/pages/about.md"
        fields:
          - { label: "Title", name: "title", widget: "string" }
          - { label: "Bio", name: "bio", widget: "markdown" }
          - { label: "Photo", name: "photo", widget: "image" }
          - { label: "Years of Experience", name: "yearsExp", widget: "number" }
          - { label: "Student Count", name: "studentCount", widget: "number" }
```

### Pattern 4: Decap CMS Folder Collection for Blog and Gallery

**What:** A "folder" collection creates/deletes entries as Markdown files in a directory. Each teacher-authored blog post or gallery album becomes one `.md` file.

**When to use:** Blog posts, gallery albums — any content type with multiple growing entries.

**Example — blog + gallery in `public/admin/config.yml`:**
```yaml
backend:
  name: git-gateway
  branch: main

media_library:
  name: cloudinary
  config:
    cloud_name: your-cloud-name
    api_key: your-api-key

collections:
  - name: "blog"
    label: "Blog Posts"
    folder: "src/content/blog"
    create: true
    slug: "{{year}}-{{month}}-{{day}}-{{slug}}"
    fields:
      - { label: "Title", name: "title", widget: "string" }
      - { label: "Publish Date", name: "pubDate", widget: "datetime" }
      - { label: "Description", name: "description", widget: "text" }
      - { label: "Cover Image", name: "coverImage", widget: "image" }
      - { label: "Tags", name: "tags", widget: "list" }
      - { label: "Draft", name: "draft", widget: "boolean", default: false }
      - { label: "Body", name: "body", widget: "markdown" }

  - name: "gallery"
    label: "Gallery Albums"
    folder: "src/content/gallery"
    create: true
    slug: "{{slug}}"
    fields:
      - { label: "Album Title", name: "title", widget: "string" }
      - { label: "Event Date", name: "eventDate", widget: "datetime" }
      - { label: "Description", name: "description", widget: "text", required: false }
      - { label: "Cover Image", name: "coverImage", widget: "image" }
      - label: "Photos"
        name: "images"
        widget: "list"
        field: { label: "Photo", name: "image", widget: "image" }
```

---

## Data Flow

### Content Authoring Flow (Teacher updates content)

```
Teacher opens yoursite.com/admin
    │
    ▼
Netlify Identity authenticates teacher (email/password)
    │
    ▼
Decap CMS SPA loads in browser — shows form for selected collection
    │
    ▼
Teacher edits fields (text, images) and clicks "Publish"
    │
    ├─ (if image) → Upload to Cloudinary → Cloudinary returns URL
    │                                         │
    │                                         ▼
    │               Cloudinary URL stored as field value in Markdown
    │
    ▼
Decap CMS commits new/updated .md file to GitHub (via Git Gateway)
    │
    ▼
GitHub webhook fires → Netlify detects push
    │
    ▼
Netlify runs: npm run build (astro build)
    │
    ▼
Astro reads all .md files in src/content/ → validates schemas
    │
    ▼
Astro generates static HTML for every page/route
    │
    ▼
Netlify deploys new static build to CDN (~1-3 minutes total)
    │
    ▼
Teacher's change is live on the public site
```

### Contact Form Submission Flow

```
Visitor fills out contact form → submits
    │
    ▼
Netlify Forms intercepts POST (no backend required)
    │
    ▼
Submission stored in Netlify dashboard (Forms tab)
    │
    ▼ (optional)
Email notification sent to yogatherapylk@gmail.com
```

### Image Delivery Flow (Cloudinary)

```
Astro component renders <img src={cloudinaryUrl} />
    │
    ▼
Browser requests image from Cloudinary CDN (not Netlify, not GitHub)
    │
    ▼
Cloudinary serves optimized image (auto-format WebP, resized to fit)
    │
    ▼
Browser displays image
```

---

## Netlify Build Configuration

**`netlify.toml`** (place at repo root):
```toml
[build]
  command = "npm run build"
  publish = "dist"

[build.environment]
  NODE_VERSION = "20"

# Redirect /admin to CMS (Netlify serves SPA from public/admin/index.html)
[[redirects]]
  from = "/admin/*"
  to = "/admin/index.html"
  status = 200

# Fallback 404 page
[[redirects]]
  from = "/*"
  to = "/404"
  status = 404
```

**`astro.config.mjs`:**
```javascript
import { defineConfig } from 'astro/config';

export default defineConfig({
  output: 'static',          // Required: tells Astro to build static HTML
  site: 'https://yogatherapylk.netlify.app',  // Update with real domain
});
```

---

## Cloudinary Integration Pattern

**Setup (one-time developer task):**
1. Create free Cloudinary account at cloudinary.com
2. Note `cloud_name` from dashboard
3. Create an "Upload preset" set to "Unsigned" (required for CMS uploads)
4. Add `cloud_name` and `api_key` to Decap CMS `config.yml` media_library section

**CMS config (`public/admin/config.yml`):**
```yaml
media_library:
  name: cloudinary
  config:
    cloud_name: shri-yogi-foundation
    api_key: YOUR_API_KEY
    # api_secret is NOT stored here (frontend-only; unsigned preset handles auth)
```

**How images flow:**
- Teacher clicks image field in CMS → Cloudinary widget opens in browser
- Photo selected/uploaded → stored in Cloudinary (never in Git)
- Cloudinary returns a URL like `https://res.cloudinary.com/shri-yogi-foundation/image/upload/v1234/photo.jpg`
- That URL is saved as the field value in the Markdown frontmatter
- Astro renders it as a normal `<img src="...">` tag — no special handling needed
- Cloudinary free tier: 25 GB storage, 25 GB bandwidth/month — sufficient for a yoga site

**Cloudinary URL transformation (optional, powerful):**
```astro
---
// Resize image to 800px wide on the fly via URL parameter
const optimizedUrl = imageUrl.replace('/upload/', '/upload/w_800,f_auto,q_auto/');
---
<img src={optimizedUrl} alt={title} />
```

---

## Integration Points

### External Services

| Service | Integration Pattern | Notes |
|---------|---------------------|-------|
| Netlify Identity | Script tag on every page + `/admin/index.html`; teacher logs in at `/admin` | Enable in Netlify dashboard → Site settings → Identity; invite teacher by email |
| Cloudinary | Decap CMS media_library config in `config.yml` | Requires free Cloudinary account + unsigned upload preset |
| GitHub | Netlify connected via OAuth app; Decap CMS uses `git-gateway` backend | Git Gateway is a Netlify service — enable in Identity settings |
| Netlify Forms | `data-netlify="true"` attribute on `<form>` tag + `netlify-honeypot` for spam | Form must be present in static HTML at build time for Netlify to detect it |

### Internal Boundaries

| Boundary | Communication | Notes |
|----------|---------------|-------|
| Decap CMS → GitHub | Decap commits Markdown via Netlify Git Gateway API | Teacher never touches Git; CMS handles all commits |
| GitHub → Netlify Build | GitHub webhook triggers Netlify; no manual configuration needed after initial connect | Build typically takes 1-3 minutes |
| Astro Content → Astro Pages | `getCollection('blog')` in page files; typed via `InferEntrySchema` | All reads are at build time — no runtime DB queries |
| Astro Pages → Components | Props passed from page to component via Astro.props | Standard component composition |
| Contact Form → Netlify | HTML form with `data-netlify="true"`; Netlify intercepts at CDN edge | No JavaScript needed; works even if JS is disabled |

---

## Build Order for a Working Site

Build in this sequence to always have a deployable site at each step:

1. **Scaffold and deploy empty shell** — `npm create astro@latest`, add `netlify.toml`, push to GitHub, connect to Netlify. Proves the pipeline works before writing any real code.

2. **Add BaseLayout + static pages (Home, About, Contact)** — Hard-coded content first. No CMS yet. Proves routing and layout work.

3. **Add Netlify Forms to Contact page** — Test form submission before CMS is involved. Simpler to debug in isolation.

4. **Add Decap CMS admin panel** — Create `public/admin/index.html` and `config.yml` with `pages` file collection only. Enable Netlify Identity. Invite teacher. Verify login works.

5. **Wire CMS pages to Astro pages** — Replace hard-coded content in `index.astro` and `about.astro` with data read from `src/content/pages/`.

6. **Add blog collection** — CMS folder collection + `src/content/blog/` + `src/pages/blog/index.astro` + `src/pages/blog/[slug].astro`. Add schema in `config.ts`.

7. **Add Cloudinary + gallery collection** — Connect Cloudinary in CMS config. Add gallery folder collection. Build gallery listing and album pages.

8. **Polish and theme** — Tailwind/CSS styling, responsive design, performance audit.

---

## Anti-Patterns

### Anti-Pattern 1: Storing Images in Git

**What people do:** Upload photos via `git add`, commit JPEGs into `src/assets/` or `public/`.

**Why it's wrong:** Git is not designed for binary blobs. A yoga site with 200 event photos will bloat the repo to hundreds of MBs. `git clone` gets slower over time. GitHub has a 100MB file limit and recommends repos stay under 1GB total.

**Do this instead:** Cloudinary for all photos uploaded by the teacher. Developer-owned assets (logo, favicon, icons) can live in `public/` but keep them small.

### Anti-Pattern 2: Using Astro's `src/assets/` for CMS-managed Images

**What people do:** Configure Decap CMS `media_folder` to point to `src/assets/` (Astro's local image optimization folder) and `public_folder` to `/_astro/`.

**Why it's wrong:** Astro processes images in `src/assets/` with its own asset pipeline, generating content-hashed filenames. Decap CMS stores the pre-processed filename in Markdown. The reference breaks after the first build because the hashed output filename differs.

**Do this instead:** Either (a) use Cloudinary for all CMS images (recommended), or (b) use `public/uploads/` as media_folder and `public_folder: /uploads`. Files in `public/` are served as-is, no processing, filenames stable.

### Anti-Pattern 3: Skipping `output: 'static'` in Astro Config

**What people do:** Leave Astro in default mode and deploy to Netlify, then wonder why server-side rendering features or API routes behave unexpectedly.

**Why it's wrong:** Astro's default output is `'static'` but can be switched to `'server'` or `'hybrid'`. If another integration changes this implicitly, the build output changes from static HTML to a serverless function bundle — which works on Netlify but is unnecessary complexity and breaks the zero-maintenance model.

**Do this instead:** Explicitly set `output: 'static'` in `astro.config.mjs`. Makes intent clear and prevents accidental mode switches.

### Anti-Pattern 4: Putting CMS Config in `src/` Instead of `public/`

**What people do:** Create `src/admin/config.yml` expecting Astro to route it to `/admin`.

**Why it's wrong:** Astro processes everything in `src/` through its build pipeline. The CMS `index.html` and `config.yml` are plain static files that must be served exactly as written — no processing. Astro will either error on them or transform them incorrectly.

**Do this instead:** Always place `public/admin/index.html` and `public/admin/config.yml`. Astro copies `public/` to `dist/` verbatim, no transformation.

---

## Scaling Considerations

| Scale | Architecture Adjustments |
|-------|--------------------------|
| 0-1k visitors/month | Current architecture is exactly right — static CDN handles this trivially |
| 1k-50k visitors/month | Still fine on Netlify free tier; consider adding Cloudinary image transformations for performance |
| 50k+ visitors/month | Netlify free tier bandwidth limit (100GB/month) may be reached; upgrade to Netlify Pro ($19/mo) or migrate to Cloudflare Pages (more generous free tier) |

### Scaling Priorities

1. **First bottleneck: Cloudinary bandwidth** (25GB/month free). Mitigate with aggressive caching headers and Cloudinary `q_auto,f_auto` URL transformations to minimize file sizes.
2. **Second bottleneck: Netlify build minutes** (300 min/month free). Each content update triggers a full rebuild (~1-3 min). At 100+ content updates/month this becomes a concern; mitigate by batching CMS changes or enabling Netlify's incremental builds.

---

## Sources

- Astro Content Collections: https://docs.astro.build/en/guides/content-collections/ (HIGH confidence — stable API since Astro 2.0)
- Decap CMS Configuration: https://decapcms.org/docs/configuration-options/ (HIGH confidence — formerly Netlify CMS, stable config format)
- Decap CMS Cloudinary integration: https://decapcms.org/docs/cloudinary/ (MEDIUM confidence — integration pattern unchanged; verify current widget version)
- Netlify Git Gateway: https://docs.netlify.com/visitor-access/git-gateway/ (HIGH confidence)
- Netlify Forms: https://docs.netlify.com/forms/setup/ (HIGH confidence — stable product)
- Netlify Identity: https://docs.netlify.com/visitor-access/identity/ (HIGH confidence)
- Astro + Decap CMS guide: https://docs.astro.build/en/guides/cms/decap-cms/ (MEDIUM confidence — verify guide still current as of 2026)

---

*Architecture research for: Yoga Teacher Website (Astro + Decap CMS + Netlify + Cloudinary)*
*Researched: 2026-03-21*
