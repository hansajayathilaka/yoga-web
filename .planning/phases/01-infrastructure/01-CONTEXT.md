# Phase 1: Infrastructure - Context

**Gathered:** 2026-03-21
**Status:** Ready for planning

<domain>
## Phase Boundary

Scaffold the AstroWind Astro project locally, configure it with Tailwind CSS, and push to GitHub. This is a local-only setup phase — no Netlify deployment yet. The goal is a working local dev environment with the correct project structure committed to GitHub.

Netlify deployment, Netlify Identity, and Git Gateway are deferred to Phase 5 (Polish and Launch), when the full site is ready and the custom domain switch will happen simultaneously.

</domain>

<decisions>
## Implementation Decisions

### Astro Template
- Use **AstroWind** as the starting template — full-featured with Tailwind, hero section, blog, landing page sections already scaffolded
- Use **Tailwind CSS** for all styling — AstroWind includes it; keep it as the styling system throughout the project

### Deployment Strategy
- Phase 1 deliverable is **local only**: Astro project running on `localhost`, committed to GitHub
- No Netlify connection in Phase 1
- Phases 2–4 will build all features (pages, CMS config, blog, gallery) locally
- Phase 5 will handle: Netlify site creation, deployment, Netlify Identity activation, Git Gateway, and custom domain switch

### Domain
- Current live site: yogatherapylk.com (hosted by Macro Systems — DNS access not yet obtained)
- DNS credentials from Macro Systems need to be obtained before Phase 5
- During development: site runs on localhost only; no staging URL needed

### Package Manager
- Use **npm** (default) — simpler setup, no extra configuration needed for Netlify build

### Claude's Discretion
- Exact AstroWind configuration and which demo sections to keep vs remove initially
- Node.js version pinning (.nvmrc)
- .gitignore contents

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Project requirements
- `.planning/PROJECT.md` — Project context, constraints, tech stack decisions
- `.planning/REQUIREMENTS.md` — v1 requirements; INFRA-01 to INFRA-05 are Phase 1 targets (note: INFRA-02 to INFRA-05 are now Phase 5 due to local-first decision)
- `.planning/ROADMAP.md` — Phase structure and success criteria

### Research
- `.planning/research/STACK.md` — Astro version recommendations, AstroWind notes, Tailwind integration
- `.planning/research/ARCHITECTURE.md` — Directory structure, Decap CMS placement in public/admin/, content collections layout
- `.planning/research/PITFALLS.md` — Common mistakes (useful for scaffolding step)

No external specs — requirements are fully captured in decisions above and planning files.

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- None yet — greenfield project

### Established Patterns
- None yet — this phase establishes the patterns

### Integration Points
- AstroWind scaffold → will be extended in Phase 2 with yoga-specific content and branding
- `src/content/` directory → will receive blog and gallery Astro Content Collections in Phase 4
- `public/admin/` directory → will receive Decap CMS admin files in Phase 3

</code_context>

<specifics>
## Specific Ideas

- AstroWind is the chosen base — check astro.build/themes or the AstroWind GitHub repo for the latest version compatible with Astro 5.x
- The local dev experience should feel complete: `npm run dev` starts a working site at localhost

</specifics>

<deferred>
## Deferred Ideas

- Netlify deployment → Phase 5
- Netlify Identity + Git Gateway activation → Phase 5
- Custom domain (yogatherapylk.com) switch → Phase 5 (DNS access from Macro Systems needed first)
- netlify.toml configuration → Phase 5

</deferred>

---

*Phase: 01-infrastructure*
*Context gathered: 2026-03-21*
