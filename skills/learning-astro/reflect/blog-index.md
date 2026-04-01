# Reflect: Blog Index & RSS

Let's pause and make sure everything is working before we keep building.

## Let's make sure it clicked

1. **How does Astro filter draft posts when using `getCollection()`?**
   <details>
   <summary>Answer</summary>

   You pass a filter function as the second argument: `getCollection('blog', ({ data }) => !data.draft)`. This returns only entries where `draft` is falsy. Since our schema defaults `draft` to `false`, posts without an explicit `draft: true` will be included.
   </details>

2. **Why is the RSS feed a `.ts` file and not `.astro`?**
   <details>
   <summary>Answer</summary>

   It's an API endpoint, not an HTML page. The `.ts` file exports a `GET` function that returns an XML Response object (the RSS feed). Astro pages (`.astro`) always return HTML. API endpoints (`.ts`/`.js`) can return any content type — XML, JSON, plain text, etc.
   </details>

3. **What does `paginate()` do in `getStaticPaths`?**
   <details>
   <summary>Answer</summary>

   `paginate()` takes an array of items and a page size, then automatically generates paginated routes. For example, with 15 posts and `pageSize: 5`, it creates `/blog` (page 1), `/blog/2` (page 2), `/blog/3` (page 3). It also provides `page.url.prev`, `page.url.next`, and other navigation helpers.
   </details>

## Quick review

### 1. Blog Index Page

Show me `src/pages/blog/index.astro`:

- [ ] Fetches posts with `getCollection('blog')`
- [ ] Filters out draft posts: `({ data }) => !data.draft`
- [ ] Sorts by date (newest first)
- [ ] Renders a list of posts with title, description, and date
- [ ] Each post links to `/blog/[slug]`

### 2. RSS Feed

Show me `src/pages/rss.xml.ts`:

- [ ] Imports `rss` from `'@astrojs/rss'`
- [ ] Exports a `GET` function
- [ ] Fetches and filters posts (same as blog index)
- [ ] Returns RSS with title, description, site, and items array
- [ ] Each item has title, pubDate, description, and link

### 3. Configuration

- [ ] `@astrojs/rss` is installed (`package.json`)
- [ ] `astro.config.mjs` has a `site` property set
- [ ] `BaseLayout.astro` has an RSS `<link>` in `<head>`

### 4. Navigation

- [ ] Navigation includes a "Blog" link pointing to `/blog`

## If something's not working

### RSS package not installed

**Cause**: `@astrojs/rss` not in dependencies.

**Fix**: `npx astro add rss` or `npm install @astrojs/rss`

### RSS feed shows "site is required"

**Cause**: Missing `site` in `astro.config.mjs`.

**Fix**: Add `site: 'https://example.com'` to the config.

### Draft posts still showing

**Cause**: Missing filter in `getCollection()`.

**Fix**: Add the filter: `getCollection('blog', ({ data }) => !data.draft)`

### Pagination not working

**Cause**: Wrong file naming for paginated route.

**Fix**: The file must use rest params: `src/pages/blog/[...page].astro` (not `[page].astro`). The `...` is required because page 1 is `/blog` (no param), not `/blog/1`.

## Ready to move on?

- [ ] `/blog` shows all published posts sorted by date
- [ ] Draft posts (if any) are excluded from the list
- [ ] Each post links to its individual page
- [ ] `/rss.xml` returns valid XML
- [ ] RSS feed includes all published posts
- [ ] Navigation includes Blog link

## Go further

These are optional explorations if you're curious — skip them and come back later if you prefer:

- Try implementing pagination with `paginate()` in a `[...page].astro` file
- Add a tag cloud or category filter to the blog index
- Validate your RSS feed at https://validator.w3.org/feed/

## Next Steps

**Lesson 2 is complete!** Commit your progress:

```bash
git add .
git commit -m "Lesson 2: Content collections, dynamic routes, blog index, and RSS"
```

**Next**: Lesson 3 — Interactivity & Production. We'll add interactive islands and prepare for deployment.
