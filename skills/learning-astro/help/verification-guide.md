# Verification Guide

Systematic testing procedures for each component type built during the tutorial.

## Project Verification

Run after initial setup (Lesson 1, Part 1):

```bash
# 1. Dev server starts without errors
npm run dev

# 2. Check the terminal output for:
#    - "Local: http://localhost:4321/"
#    - No error messages

# 3. In the browser:
#    - http://localhost:4321/ loads the home page
#    - http://localhost:4321/about loads the about page
#    - Navigation links work between pages

# 4. Hot reload works:
#    - Edit index.astro, save, see changes in browser without manual refresh
```

## Component Verification

Run after creating components and layouts (Lesson 1, Part 2):

```bash
# 1. All pages use the layout
#    - View source in browser: check for consistent HTML structure
#    - Header/footer appear on every page

# 2. Props pass correctly
#    - Card component renders with different title/body values
#    - Change a prop value, verify the output changes

# 3. Slots render child content
#    - Content passed to BaseLayout appears inside <main>
#    - Named slots (if used) render in the correct position

# 4. Imports work
#    - No "Cannot find module" errors in terminal
#    - Components render where placed in templates
```

## Styling Verification

Run after adding styles (Lesson 1, Part 3):

```bash
# 1. Scoped styles apply only to their component
#    - Inspect element in browser DevTools
#    - Look for Astro's hash-based class attributes (e.g., class="astro-XXXXXX")
#    - Verify styles from one component don't leak to others

# 2. Global styles apply site-wide
#    - Body font, colors, reset styles apply on all pages
#    - Check that global.css is imported in the layout

# 3. class:list works
#    - Conditional classes appear/disappear based on conditions
#    - Inspect element to verify correct classes
```

## Content Collection Verification

Run after setting up content collections (Lesson 2, Part 1):

```bash
# 1. Build catches schema errors
npm run build
# Should succeed with valid posts. To test validation:
# - Temporarily remove 'title' from a post's frontmatter
# - Run npm run build — should show a clear Zod validation error
# - Restore the title

# 2. getCollection() returns typed data
# In your page, temporarily add:
# console.log(posts.length, posts[0].data.title);
# Check the terminal output during dev

# 3. Content config is found
# If getCollection returns empty, check:
# - File is at src/content.config.ts (not src/content/config.ts)
# - File exports `collections` object
# - Collection names match between config and usage
```

## Dynamic Route Verification

Run after creating dynamic routes (Lesson 2, Part 2):

```bash
# 1. All expected URLs resolve
#    - Visit /blog/[each-post-slug] in browser
#    - None should return 404

# 2. getStaticPaths generates correct params
npm run build
# Check terminal: should list all generated pages like:
# /blog/my-first-post/index.html
# /blog/second-post/index.html

# 3. Content renders correctly
#    - Blog post content (markdown) renders as formatted HTML
#    - Frontmatter data (title, date, tags) displays correctly
#    - Layout wraps the content properly

# 4. Navigation works
#    - Blog index links to individual posts
#    - Posts link back to blog index
```

## Blog Index Verification

Run after building the blog index (Lesson 2, Part 3):

```bash
# 1. Posts are sorted by date (newest first)
#    - Check the order on /blog

# 2. Draft posts are filtered out
#    - Add draft: true to a post's frontmatter
#    - Verify it doesn't appear on /blog

# 3. Pagination works (if implemented)
#    - /blog shows first N posts
#    - /blog/2 shows next N posts
#    - Prev/Next links navigate correctly

# 4. RSS feed is valid
#    - Visit /rss.xml in browser
#    - Should display XML content
#    - Validate at https://validator.w3.org/feed/ (optional)
```

## Island Verification

Run after adding interactive components (Lesson 3, Part 1):

```bash
# 1. Without client directive: component is static
#    - Render a Preact component WITHOUT client:load
#    - View source in browser: HTML is there but no JS loaded
#    - Click buttons: nothing happens (no interactivity)

# 2. With client directive: component is interactive
#    - Add client:load to the component
#    - Click buttons: interactivity works
#    - Check Network tab: JS bundle loaded for this component

# 3. Different directives behave differently
#    - client:load → JS loads immediately
#    - client:idle → JS loads after browser idle
#    - client:visible → JS loads when component scrolls into view

# 4. JS bundle size is minimal
#    - Open Network tab in DevTools
#    - Filter by JS
#    - Should see small bundles only for island components
#    - Static components should NOT appear in JS requests
```

## View Transitions Verification

Run after adding view transitions (Lesson 3, Part 2):

```bash
# 1. Navigation is instant
#    - Click links between pages
#    - Page should NOT do a full reload (no white flash)
#    - Watch the Network tab: only fetches HTML, not full page resources

# 2. Animations play
#    - transition:animate directives produce visible effects
#    - Content fades or slides as configured

# 3. Scripts work after navigation
#    - Navigate to a page with interactive elements
#    - Verify they still work (not broken by transition)
#    - If broken: ensure scripts use astro:page-load event
```

## Build Verification

Run after completing the site (Lesson 3, Part 3):

```bash
# 1. Build completes without errors
npm run build

# 2. Check dist/ folder structure
ls dist/
# Should contain: index.html, about/index.html, blog/*/index.html, etc.

# 3. Static pages have zero or minimal JS
# Check a pure-Astro page (like /about):
# - Open dist/about/index.html
# - No <script> tags (or very minimal)

# 4. Island pages have targeted JS
# Check a page with islands:
# - Open its HTML file
# - Should see <script> tags only for island components

# 5. Preview works
npm run preview
# Visit http://localhost:4321/
# All pages work, all links work, islands are interactive
```

## Authentication Setup (for testing API routes)

If you've created API endpoints during the tutorial:

```bash
# Test GET endpoints
curl http://localhost:4321/rss.xml

# Verify response is valid XML/JSON
# Check status code is 200
# Check Content-Type header matches expected format
```
