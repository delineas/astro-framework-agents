# Reflect: Build & Deploy

Let's pause and make sure everything is working before we keep building.

## Let's make sure it clicked

1. **What is the difference between `static`, `hybrid`, and `server` output modes?**
   <details>
   <summary>Answer</summary>

   **Static** (default): All pages are pre-built as HTML files at build time. No server needed — deploy anywhere. Best performance.

   **Hybrid**: Static by default, with opt-in SSR per page (add `export const prerender = false`). Good for mostly-static sites that need a few dynamic pages (login, dashboard, API routes).

   **Server**: All pages rendered per request on a server. Needed for highly dynamic applications. Requires a deployment adapter.
   </details>

2. **When do you need a deployment adapter?**
   <details>
   <summary>Answer</summary>

   Only for `hybrid` or `server` output modes — these need a server runtime to handle on-demand rendering. Static sites don't need an adapter because they're just HTML files that any web server can serve. Adapters are platform-specific: `@astrojs/netlify`, `@astrojs/vercel`, `@astrojs/node`, `@astrojs/cloudflare`.
   </details>

3. **What does `npm run build` produce for a static Astro site?**
   <details>
   <summary>Answer</summary>

   A `dist/` folder containing plain HTML, CSS, and JavaScript files — ready to upload to any static hosting provider. Each page becomes an `index.html` inside a directory matching its URL path. The `_astro/` subfolder contains hashed CSS and JS bundles (only for pages with islands).
   </details>

## Quick review

### 1. Build Succeeds

```bash
npm run build
```

- [ ] Build completes without errors
- [ ] Terminal shows list of generated pages

### 2. Output Structure

```bash
ls dist/
```

- [ ] `index.html` exists (home page)
- [ ] `about/index.html` exists
- [ ] `blog/index.html` exists
- [ ] `blog/[slug]/index.html` exists for each post
- [ ] `rss.xml` exists
- [ ] `_astro/` folder contains CSS and (minimal) JS bundles

### 3. Preview Works

```bash
npm run preview
```

- [ ] Site loads at `http://localhost:4321/`
- [ ] All pages are accessible
- [ ] Navigation works
- [ ] Islands are interactive
- [ ] View transitions work
- [ ] RSS feed is accessible

### 4. JavaScript Audit

- [ ] Static pages (about, blog index) have zero or near-zero JS
- [ ] Pages with islands have targeted, small JS bundles
- [ ] No unnecessary JavaScript is shipped

### 5. Deployment (optional)

If deployed:
- [ ] Site is accessible at the deployed URL
- [ ] All pages load correctly
- [ ] Islands work in production

## If something's not working

### Build error: "Astro.request is not available in prerendered pages"

**Cause**: Using request-time APIs (`Astro.request`, `Astro.cookies`) in a statically rendered page.

**Fix**: Either remove the request-time code, or mark the page for SSR: `export const prerender = false;` (requires `hybrid` output mode and an adapter).

### Build error: "missing adapter"

**Cause**: Using `output: 'hybrid'` or `'server'` without installing an adapter.

**Fix**: Install one: `npx astro add netlify` (or vercel/node/cloudflare).

### Preview shows 404 for some pages

**Cause**: Pages that failed silently during build.

**Fix**: Check the build output for warnings. Run `npm run build` again and look for any skipped pages.

### Large JS bundle

**Cause**: Too many components with `client:load`, or a heavy framework component.

**Fix**: Audit your client directives. Consider using `client:idle` or `client:visible` for non-critical components. Check if a `<script>` tag could replace a framework component.

## Ready to move on?

- [ ] `npm run build` completes without errors
- [ ] `npm run preview` serves a working site
- [ ] All pages load correctly
- [ ] All links work
- [ ] Islands are interactive in preview
- [ ] View transitions work in preview
- [ ] RSS feed is valid
- [ ] JavaScript payload is minimal

## Go further

These are optional explorations if you're curious — skip them and come back later if you prefer:

- Try switching to `output: 'hybrid'` in `astro.config.mjs` and marking one page as `export const prerender = false`
- Explore the `_astro/` directory in `dist/` — compare JS bundle sizes between pages with and without islands
- Set up a simple CI/CD pipeline (GitHub Actions → Netlify/Vercel) for automatic deploys

## Tutorial Complete!

Commit your final work:

```bash
git add .
git commit -m "Lesson 3: Islands, view transitions, and production deployment"
```

**What you've accomplished:**
- Built a complete personal blog with Astro
- Mastered the component model, content layer, and islands architecture
- Created type-safe content collections with Zod schemas
- Added interactive components with selective hydration
- Implemented smooth view transitions
- Produced a production-ready, deployable site

**Where to go next:**
- Explore SSR with `hybrid` mode for dynamic features
- Try server islands (`server:defer`) for personalized content
- Add Astro Actions for type-safe form handling
- Set up i18n routing for multilingual content
- All covered in the `astro-framework` skill references
