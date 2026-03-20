# Pitfalls Research

**Domain:** Static yoga teacher website — Astro + Decap CMS + Netlify + Cloudinary
**Researched:** 2026-03-21
**Confidence:** MEDIUM (training data through Aug 2025; WebSearch/WebFetch unavailable — verified against known official docs patterns)

---

## Critical Pitfalls

### Pitfall 1: Netlify Identity Not Enabled Before First Deploy

**What goes wrong:**
The Decap CMS admin panel loads at `/admin/` but the login widget silently fails or shows a blank screen. The teacher cannot log in even after registration. The OAuth callback from Netlify Identity never completes because the service was never activated for the site.

**Why it happens:**
Netlify Identity is a per-site feature that must be manually enabled in the Netlify dashboard under Site Settings > Identity. It does not activate automatically on deploy — even when `netlify-identity-widget` is wired correctly in the HTML. Developers assume the Netlify build process infers this from the codebase.

**How to avoid:**
Enable Netlify Identity immediately after the first successful deploy, before any CMS testing. Set registration to "Invite only" (not "Open") immediately — otherwise anyone can create an account. Then send the teacher an invite via the Identity tab. Document this as a required post-deploy step.

**Warning signs:**
- `/admin/` loads but clicking "Login with Netlify Identity" produces no popup or a popup that closes immediately
- Netlify dashboard shows 0 users under Identity even after attempted registration
- Browser console shows `GoTrue` or `netlify-identity-widget` network errors

**Phase to address:** Infrastructure / Deployment setup phase (before handing off to teacher)

---

### Pitfall 2: Missing `netlify-identity-widget` Script on the Main Site Index

**What goes wrong:**
The Netlify Identity confirmation email link (for teacher account creation) redirects to the site root (`/`), not to `/admin/`. If the `netlify-identity-widget` script is NOT loaded on the root `index.html` page, the `#confirmation_token=` hash fragment is never processed. The teacher clicks the email link and sees a normal homepage — the account is never confirmed.

**Why it happens:**
Developers correctly add the identity widget script inside `/public/admin/index.html` for the CMS login flow but miss the requirement to also add it to the site root. The Netlify Identity confirmation/recovery email links always redirect to the site root first.

**How to avoid:**
Add the following script tag to the site's root layout/index page (not just the admin page):
```html
<script src="https://identity.netlify.com/v1/netlify-identity-widget.js"></script>
```
Also add the redirect handler in the root page's script:
```js
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

**Warning signs:**
- Teacher says "the email link doesn't work" or "it just shows the homepage"
- Netlify Identity dashboard shows user as "unconfirmed" after teacher clicked the email link

**Phase to address:** CMS integration phase — must be part of the admin setup task

---

### Pitfall 3: Decap CMS `config.yml` — Wrong `media_folder` and `public_folder` Paths

**What goes wrong:**
Images uploaded via the CMS media library are saved to the wrong directory, or image URLs in the rendered site are broken (404). This is one of the most frequently reported Decap CMS issues.

**Why it happens:**
`media_folder` is the path where files are saved relative to the **Git repo root**. `public_folder` is the URL path prefix Decap CMS inserts into Markdown content. For Astro, the static asset directory is `public/`, so `media_folder: "public/images/uploads"` and `public_folder: "/images/uploads"` is correct. Developers often set both to the same value or use relative paths that break in Astro's build output.

**How to avoid:**
For Astro specifically, use this configuration:
```yaml
media_folder: "public/images/uploads"
public_folder: "/images/uploads"
```
Test by uploading one image via the CMS, checking it appears in the Git commit under `public/images/uploads/`, and verifying the rendered `<img>` src resolves correctly on the deployed site.

**Warning signs:**
- Images show in the CMS preview but are broken on the live site
- Git commits from the CMS show image files in unexpected directories
- `<img>` tags in generated Markdown contain double slashes or wrong path prefixes

**Phase to address:** CMS integration phase — verify with a test upload before launch

---

### Pitfall 4: Cloudinary Integration Bypassed — Images Uploaded Directly to Git

**What goes wrong:**
The teacher uploads high-resolution event photos (3–8 MB each) directly through the Decap CMS media library without Cloudinary configured. These files are committed to the GitHub repository. After 20–30 events, the repo exceeds GitHub's soft limits (repository over 1 GB warning), build times increase significantly, and Netlify free tier bandwidth is consumed by serving unoptimized images.

**Why it happens:**
Cloudinary integration requires additional `config.yml` configuration and a Cloudinary account setup. Without it, the default Decap CMS media library writes files directly to Git. Developers defer "proper image handling" to a later phase that never comes.

**How to avoid:**
Configure Cloudinary in `config.yml` from the start — before the teacher uploads any photos:
```yaml
media_library:
  name: cloudinary
  config:
    cloud_name: [your-cloud-name]
    api_key: [your-api-key]
```
Store `api_key` as a Netlify environment variable, not hardcoded in `config.yml`. The Cloudinary free tier (25 GB storage, 25 GB bandwidth/month) is ample for a yoga site. Verify the integration with a test upload before the teacher is onboarded.

**Warning signs:**
- CMS commits contain `.jpg`/`.png` files alongside Markdown content
- Git repo size growing rapidly (check with `git count-objects -vH`)
- No Cloudinary uploads appearing in the Cloudinary media library dashboard

**Phase to address:** CMS integration phase — Cloudinary must be wired before teacher onboarding

---

### Pitfall 5: Decap CMS `backend.branch` Pointing to Wrong Branch

**What goes wrong:**
The CMS commits content to a different branch than what Netlify is watching for deploys. Content changes appear to save successfully in the CMS but the live site never updates. Or worse, the CMS pushes to `master` while the deployed branch is `main`, creating a diverged history.

**Why it happens:**
GitHub's default branch changed from `master` to `main` in 2020 but many Decap CMS tutorials and boilerplates still show `branch: master`. Developers copy-paste without verifying their actual default branch.

**How to avoid:**
Explicitly set `backend.branch` in `config.yml` to match the Netlify deploy branch:
```yaml
backend:
  name: git-gateway
  branch: main
```
Verify: Netlify dashboard > Site Settings > Build & Deploy > Branches — note the "Production branch" value and match it exactly.

**Warning signs:**
- CMS save succeeds but no new Netlify deploy is triggered
- GitHub shows commits on a branch that is not the production branch
- Netlify deploy log shows "No changes detected" after a CMS edit

**Phase to address:** CMS integration phase — verify end-to-end publish flow before teacher handoff

---

### Pitfall 6: Git Gateway Not Enabled in Netlify

**What goes wrong:**
The CMS admin panel opens, the teacher logs in via Netlify Identity, but clicking "Save" or browsing collections produces a "Failed to persist entry: API_ERROR" or "Git Gateway not found" error. No content can be saved.

**Why it happens:**
Decap CMS uses Git Gateway (a Netlify service) to make authenticated commits to the GitHub repo without exposing GitHub credentials to the browser. Git Gateway must be enabled separately in Netlify under Site Settings > Identity > Services > Enable Git Gateway. It is not enabled by default even when Netlify Identity is enabled.

**How to avoid:**
Enable Git Gateway in Netlify immediately after enabling Identity. The setup sequence must be:
1. Enable Netlify Identity
2. Enable Git Gateway under Identity > Services
3. Configure `config.yml` with `backend.name: git-gateway`
4. Invite the teacher user

**Warning signs:**
- Login works but "Save" produces an API error
- Browser network tab shows 404 on `/.netlify/git/github/...` requests
- CMS shows collections but "New Entry" fails to save

**Phase to address:** Infrastructure setup phase — part of Netlify configuration checklist

---

## Technical Debt Patterns

| Shortcut | Immediate Benefit | Long-term Cost | When Acceptable |
|----------|-------------------|----------------|-----------------|
| Hardcoding Cloudinary `api_key` in `config.yml` | Faster setup | Key exposed in public GitHub repo; Cloudinary account can be hijacked | Never — use Netlify env vars |
| Using a pre-built Astro theme without understanding its content structure | Faster UI | CMS config must match the theme's frontmatter fields exactly; mismatch causes silent data loss or broken renders | Acceptable if theme is audited before CMS config is written |
| Skipping Decap CMS editorial workflow (draft/review) | Simpler config | Teacher publishes directly to live site; typos go live immediately | Acceptable for single-author site — teacher is sole editor |
| Inline styles on Astro template components instead of Tailwind/CSS classes | Quick customization | Hard to maintain theme consistency; conflicts if template is updated | Acceptable for MVP; refactor in polish phase |
| Not setting `registration: invite_only` in Netlify Identity | One less config step | Anyone can register as a CMS user via the public API; spam/abuse risk | Never — always set invite-only |

---

## Integration Gotchas

| Integration | Common Mistake | Correct Approach |
|-------------|----------------|------------------|
| Decap CMS + Netlify Identity | Adding identity widget script only to `/admin/index.html` | Add to site root layout too — confirmation email links redirect to `/` |
| Decap CMS + Cloudinary | Using `api_key` directly in config.yml committed to GitHub | Store as Netlify environment variable; reference via `process.env` or Netlify's CMS env injection |
| Netlify Forms + Astro | Adding `data-netlify="true"` to the Astro component's JSX — Netlify's build bot parses static HTML, not JSX | Ensure the form renders in the **static HTML output**; add a hidden `<input type="hidden" name="form-name" value="contact" />` field or Netlify won't detect it |
| Decap CMS + Astro content collections | Defining Astro content collections schema that doesn't match CMS frontmatter field names | Write the CMS `config.yml` fields first, then derive the Astro `defineCollection` schema from them — not the reverse |
| Cloudinary free tier | Exceeding 25 GB bandwidth with unoptimized image delivery | Use Cloudinary's URL transformation parameters (`f_auto,q_auto,w_800`) in Astro `<Image>` src to serve compressed images |
| Git Gateway + GitHub | Repo is private but Git Gateway wasn't granted access to the GitHub app | In Netlify, re-authorize the GitHub app to include the private repo after enabling Git Gateway |

---

## Performance Traps

| Trap | Symptoms | Prevention | When It Breaks |
|------|----------|------------|----------------|
| Serving original-resolution photos from Cloudinary without transformations | Gallery page loads slowly on mobile; Core Web Vitals LCP > 4s | Always use Cloudinary URL transformations: `f_auto,q_auto,w_1200` for gallery, `w_400` for thumbnails | From the first high-res photo upload |
| Astro template with unoptimized `<img>` tags | All images load at original size; mobile performance poor | Use Astro's built-in `<Image>` component which generates `srcset` and proper dimensions | From first deployment |
| Blog post Markdown with embedded base64 images | Markdown files become huge (MB range), slow Git operations and builds | Configure CMS to use external image URLs (Cloudinary) rather than inline; educate teacher never to paste images as base64 | After ~5 posts with embedded images |
| Netlify free tier build minutes (300/month) exhausted | Deploys start failing or queuing for hours | Every CMS content save triggers a full site rebuild; for a small yoga site 300 min is ~600 deploys — more than sufficient, but monitor if adding complex build steps | At ~600+ CMS saves per month (unlikely for one teacher) |

---

## Security Mistakes

| Mistake | Risk | Prevention |
|---------|------|------------|
| Netlify Identity registration left as "Open" | Anyone on the internet can create a CMS account and modify the site | Set registration to "Invite only" in Netlify Identity settings immediately on first enable |
| Cloudinary API key committed to public GitHub repo | Cloudinary account can be used to serve malicious content at teacher's expense; account suspended | Store in Netlify environment variables; reference in `config.yml` only via env var |
| No `robots.txt` blocking `/admin/` | Admin panel discoverable and indexed by search engines; increases spam attack surface | Add `Disallow: /admin/` to `robots.txt` |
| Netlify form submissions not protected with honeypot | Contact form receives spam; Netlify free tier form limit (100 submissions/month) consumed by bots | Add `data-netlify-honeypot="bot-field"` to the contact form |
| Using Decap CMS editorial workflow with public GitHub repo | Draft posts are visible in the `cms/` branch before publication | Either use a private repo or disable editorial workflow for single-author sites |

---

## UX Pitfalls

| Pitfall | User Impact | Better Approach |
|---------|-------------|-----------------|
| CMS rich text editor behavior is unfamiliar to non-technical users | Teacher formats text incorrectly, breaks layouts, or abandons the CMS entirely | Use Decap CMS `body` field as `widget: markdown` (default) with a preview pane enabled; walk the teacher through one complete blog post during handoff |
| CMS collection field labels use technical names (`slug`, `frontmatter`, `pubDate`) | Teacher is confused about what to fill in | Use `label:` in `config.yml` to give fields plain-language names: `label: "Publish Date"` instead of `pubDate` |
| No image alt text field in the CMS gallery collection | Teacher uploads images with no accessible descriptions; also harms SEO | Add an `alt` field to every image-bearing collection in `config.yml`; make it required |
| Teacher cannot preview changes before they go live | Teacher publishes mistakes and waits for a rebuild to correct them | Enable Netlify Deploy Previews so CMS draft saves trigger a preview URL; or enable Decap CMS editorial workflow for a staging branch |
| Gallery expects a specific image aspect ratio but teacher uploads portrait photos | Gallery layout breaks with mixed orientations | Use CSS `object-fit: cover` on gallery images with fixed container dimensions; test with portrait and landscape images before launch |
| Contact form success state is invisible (default browser behavior) | Teacher thinks form is broken; visitors submit multiple times | Configure a Netlify Form success redirect to a dedicated `/thank-you` page with clear confirmation |

---

## "Looks Done But Isn't" Checklist

- [ ] **Netlify Identity:** Registration is set to "Invite only" — verify in Netlify dashboard > Identity > Registration preferences
- [ ] **Git Gateway:** Enabled under Netlify Identity > Services > Git Gateway — verify by attempting a CMS save
- [ ] **Identity widget on root page:** Confirm email confirmation link works by doing a full invite flow test with a secondary email account
- [ ] **Cloudinary integration:** Verify a CMS image upload goes to Cloudinary (not to Git) by checking the GitHub commit after upload
- [ ] **`backend.branch` matches deploy branch:** Confirm CMS save triggers a Netlify deploy by watching the Netlify deploys dashboard in real time
- [ ] **Netlify Forms detection:** Confirm Netlify detected the contact form by checking Netlify dashboard > Forms — the form should appear there after first deploy
- [ ] **Form spam protection:** Honeypot field is present in the rendered static HTML (not just the Astro component)
- [ ] **Media folder paths:** Upload a test image, publish a post referencing it, and verify the image renders on the deployed site (not just in CMS preview)
- [ ] **`robots.txt`:** `/admin/` is disallowed
- [ ] **Mobile gallery:** Test gallery with mixed portrait/landscape images on a real mobile device

---

## Recovery Strategies

| Pitfall | Recovery Cost | Recovery Steps |
|---------|---------------|----------------|
| Images committed to Git instead of Cloudinary | HIGH | Remove large files from Git history with `git filter-repo`; reconfigure Cloudinary; educate teacher; force push (coordinate with Netlify) |
| Wrong `backend.branch` — CMS committed to wrong branch | MEDIUM | Merge or cherry-pick CMS commits to the correct branch; update `config.yml`; verify deploy triggers |
| Netlify Identity registration left open and spam accounts created | LOW–MEDIUM | Delete unwanted users from Netlify Identity dashboard; set registration to Invite Only; rotate any exposed secrets |
| `media_folder` misconfigured — images in wrong directories | MEDIUM | Move image files to correct path; update all Markdown frontmatter references; push corrected state |
| Teacher accidentally deleted a blog post in CMS | LOW | Restore from Git history — every CMS action is a commit; `git revert` or restore from GitHub UI |
| Netlify form limit (100/month free) exceeded | LOW | Upgrade Netlify plan or add a free-tier Formspree/Web3Forms alternative; swap out `data-netlify` for the new provider |

---

## Pitfall-to-Phase Mapping

| Pitfall | Prevention Phase | Verification |
|---------|------------------|--------------|
| Netlify Identity not enabled | Infrastructure / Deployment | Teacher can log in to `/admin/` and see collections |
| Git Gateway not enabled | Infrastructure / Deployment | Teacher can save a draft post |
| Identity widget missing from root page | CMS Integration | Do full invite-confirm flow with test email |
| Wrong `backend.branch` | CMS Integration | CMS save triggers visible Netlify deploy |
| `media_folder`/`public_folder` misconfigured | CMS Integration | Test image renders on deployed site |
| Cloudinary not wired before teacher upload | CMS Integration / Image Gallery | Verify GitHub commit after CMS upload contains no image files |
| Netlify Forms not detected | Contact Page | Form appears in Netlify dashboard > Forms after first deploy |
| Form honeypot missing | Contact Page | Inspect rendered HTML for `bot-field` hidden input |
| Unoptimized Cloudinary image delivery | Image Gallery | Lighthouse mobile score > 80; check LCP metric |
| CMS fields labeled with technical names | Teacher Onboarding | Walk through CMS with teacher; no confused questions about field names |
| No thank-you redirect on form submission | Contact Page | Submit test form entry; verify redirect to `/thank-you/` |

---

## Sources

- Decap CMS official documentation — backend configuration and Netlify Identity integration (training data, HIGH confidence for stable features)
- Netlify Identity documentation — registration modes, Git Gateway setup (training data, HIGH confidence)
- Astro documentation — `public/` directory structure, `<Image>` component, content collections (training data, HIGH confidence for Astro 4.x patterns)
- Cloudinary free tier limits and URL transformation API (training data, MEDIUM confidence — limits may have changed)
- Community-reported Decap CMS issues: `media_folder`/`public_folder` mismatch, `backend.branch` divergence, missing root-page identity widget — recurring patterns across GitHub issues and community forums (training data, MEDIUM confidence)
- Netlify Forms limits and honeypot documentation (training data, HIGH confidence for documented free tier behavior)

**Note:** WebSearch and WebFetch were unavailable during this research session. All findings are from training knowledge (through August 2025). Confidence is MEDIUM overall. The Decap CMS + Netlify Identity pitfalls (especially the root-page identity widget and Git Gateway activation) are well-established, recurring issues with HIGH confidence. Cloudinary free tier limits should be re-verified at project start as they may change.

---
*Pitfalls research for: Astro + Decap CMS + Netlify yoga teacher website*
*Researched: 2026-03-21*
