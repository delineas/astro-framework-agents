# Lesson 3: Interactivity & Production

**Time**: ~45 minutes
**Goal**: Add interactive components (islands), smooth page transitions, and deploy the site.
**Prerequisites**: Completed Lessons 1-2 (pages, components, content collections, dynamic routes)

By the end of this lesson, your blog will have interactive Preact components, SPA-like navigation with view transitions, and be ready for production deployment.

---

## Part 1: Islands — Interactive Components (~15 min)

### Architecture Overview

Load `concepts/islands-architecture.md` and explain the core concept:

```
Your page = ocean of static HTML + islands of interactivity
```

**Key concept**: Everything you've built so far ships ZERO JavaScript. Astro only sends JS to the browser when you explicitly ask for it with a `client:` directive. This is called "islands architecture" — most of the page is static HTML, with small interactive islands where needed.

### Step 1: Install Preact

```bash
npx astro add preact
```

This does two things:
1. Installs `@astrojs/preact` and `preact`
2. Adds the integration to `astro.config.mjs`

**Why Preact?** It's a 3KB alternative to React with the same API. For a blog, it keeps your JS bundle tiny. The user can substitute React if they prefer — the concepts are identical.

### Step 2: Create a LikeButton (demonstrates islands)

Create `src/components/LikeButton.tsx`:

```tsx
import { useState } from 'preact/hooks';

export default function LikeButton() {
  const [likes, setLikes] = useState(0);

  return (
    <button
      onClick={() => setLikes(likes + 1)}
      style={{
        display: 'inline-flex',
        alignItems: 'center',
        gap: '0.5rem',
        padding: '0.5rem 1rem',
        border: '1px solid #e2e8f0',
        borderRadius: '0.5rem',
        background: likes > 0 ? '#fef2f2' : '#ffffff',
        cursor: 'pointer',
        fontSize: '1rem',
        transition: 'all 0.2s',
      }}
    >
      {likes > 0 ? '❤️' : '🤍'} {likes} {likes === 1 ? 'like' : 'likes'}
    </button>
  );
}
```

### Step 3: First — use it WITHOUT a client directive

Add to `BlogPost.astro`, after the `<Content />` slot:

```astro
---
import LikeButton from '../components/LikeButton';
---

<!-- In the template, after <slot /> -->
<div class="post-actions">
  <LikeButton />
</div>
```

**Teaching moment**: Visit a blog post. The button appears! But click it — nothing happens. Inspect the page source: you'll see static HTML (`<button>🤍 0 likes</button>`) but NO JavaScript was loaded.

This is the Astro default: framework components render to static HTML. They look right but have no interactivity.

### Step 4: Add client:load — make it interactive

Change the usage to:

```astro
<LikeButton client:load />
```

Now click the button — it works! Open the Network tab in DevTools:
- You'll see a small JS file loaded (the Preact runtime + LikeButton code)
- This is the ONLY JavaScript on the page
- Everything else remains static HTML

**Teaching moment**: `client:load` tells Astro: "Send the JavaScript for this component to the browser and hydrate it immediately." Without the directive, the component is server-rendered only.

### Step 5: Create a SearchPosts component (demonstrates client:idle)

Create `src/components/SearchPosts.tsx`:

```tsx
import { useState } from 'preact/hooks';

interface Post {
  title: string;
  description: string;
  slug: string;
}

interface Props {
  posts: Post[];
}

export default function SearchPosts({ posts }: Props) {
  const [query, setQuery] = useState('');

  const filtered = posts.filter(
    post =>
      post.title.toLowerCase().includes(query.toLowerCase()) ||
      post.description.toLowerCase().includes(query.toLowerCase())
  );

  return (
    <div style={{ marginBottom: '2rem' }}>
      <input
        type="search"
        placeholder="Search posts..."
        value={query}
        onInput={(e) => setQuery((e.target as HTMLInputElement).value)}
        style={{
          width: '100%',
          padding: '0.75rem 1rem',
          border: '1px solid #e2e8f0',
          borderRadius: '0.5rem',
          fontSize: '1rem',
          marginBottom: '1rem',
        }}
      />
      {query && (
        <p style={{ color: '#64748b', fontSize: '0.9rem' }}>
          {filtered.length} {filtered.length === 1 ? 'result' : 'results'} for "{query}"
        </p>
      )}
      <ul style={{ listStyle: 'none', padding: 0 }}>
        {filtered.map(post => (
          <li key={post.slug} style={{ marginBottom: '1rem' }}>
            <a href={`/blog/${post.slug}`} style={{ textDecoration: 'none', color: '#4f46e5' }}>
              <strong>{post.title}</strong>
            </a>
            <p style={{ color: '#64748b', margin: '0.25rem 0 0' }}>{post.description}</p>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

Add to `src/pages/blog/index.astro`:

```astro
---
import SearchPosts from '../../components/SearchPosts';
// ... existing imports and data fetching ...

const postData = sortedPosts.map(post => ({
  title: post.data.title,
  description: post.data.description,
  slug: post.id,
}));
---

<BaseLayout title="Blog">
  <h1>Blog</h1>
  <SearchPosts posts={postData} client:idle />
  <!-- ... rest of the page ... -->
</BaseLayout>
```

**Teaching moment**: `client:idle` means "hydrate this component when the browser is idle." The search isn't critical for the initial page load — the user sees the full post list first (server-rendered HTML). The search becomes interactive once the browser has finished its priority work.

### Step 6: Verify

- LikeButton is interactive (clicking increments the count)
- SearchPosts filters posts as you type
- Network tab shows small JS bundles only for these components
- Remove `client:load` from LikeButton temporarily → button stops working (proves the point)

**Reflect**: Load `reflect/islands.md`

---

## Part 2: View Transitions (~15 min)

### Key Concept

View Transitions make navigation feel instant — like a SPA — without actually being one. Instead of a full page reload (white flash, all resources re-fetched), Astro fetches only the new page's HTML and smoothly swaps the content.

### Step 1: Add ClientRouter

Update `src/layouts/BaseLayout.astro`:

```astro
---
import Navigation from '../components/Navigation.astro';
import { ClientRouter } from 'astro:transitions';
import '../styles/global.css';

interface Props {
  title: string;
}
const { title } = Astro.props;
---

<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width" />
    <title>{title}</title>
    <link rel="alternate" type="application/rss+xml" title="My Blog" href="/rss.xml" />
    <ClientRouter />
  </head>
  <!-- ... rest of layout ... -->
</html>
```

**Teaching moment**: `<ClientRouter />` intercepts link clicks. Instead of a full page load, it fetches the new page's HTML, then swaps the `<body>` content with a smooth animation. The result feels instant.

**Important**: In Astro 5+, this component is called `ClientRouter`. In Astro 4 it was called `ViewTransitions`. If the user sees errors about `ViewTransitions`, they need to update to `ClientRouter`.

### Step 2: Test the basic transition

Click between pages. Notice:
- No white flash between pages
- The transition is smooth
- The URL changes correctly
- The browser's back/forward buttons work

### Step 3: Add transition animations

Add `transition:animate` directives to elements in `BaseLayout.astro`:

```astro
<body>
  <Navigation />
  <main transition:animate="fade">
    <slot />
  </main>
  <footer>
    <p>&copy; {new Date().getFullYear()} My Blog</p>
  </footer>
</body>
```

Built-in animations:
- `fade` — fades content in/out (good default)
- `slide` — slides content left/right
- `none` — no animation (instant swap)

### Step 4: Add shared element transitions

Give blog post titles a `transition:name` so they animate between the list and detail page:

In `src/pages/blog/index.astro`, update the post title:

```astro
<h3 transition:name={`post-title-${post.id}`}>{post.data.title}</h3>
```

In `src/layouts/BlogPost.astro`, update the title:

```astro
<h1 transition:name={`post-title-${Astro.url.pathname.split('/').pop()}`}>{title}</h1>
```

**Teaching moment**: When both pages have an element with the same `transition:name`, Astro animates between them — the title smoothly morphs from the list view to the detail view. This creates a polished, app-like feel.

### Step 5: Handle scripts with view transitions

If you have any `<script>` tags that set up event listeners:

```astro
<script>
  // This runs on every navigation (not just the first page load)
  document.addEventListener('astro:page-load', () => {
    // Setup code here
    console.log('Page loaded:', window.location.pathname);
  });
</script>
```

**Teaching moment**: With view transitions, regular scripts only run on the first page load. The `astro:page-load` event fires on every navigation, including the initial load. Use it for any setup code that needs to run on each page.

### Step 6: Verify

- Navigation between pages is instant (no white flash)
- Content fades in/out smoothly
- Blog post titles animate between list and detail views
- Browser back/forward buttons work correctly
- Any scripts still work after navigation

**Reflect**: Load `reflect/view-transitions.md`

---

## Part 3: Build & Deploy (~15 min)

### Step 1: Understand output modes

Explain the three modes:

```
static (default — what we're using)
├── All pages pre-built as HTML files
├── No server needed — deploy anywhere
├── Best performance
└── Use for: blogs, docs, portfolios, marketing sites

hybrid
├── Static by default + opt-in SSR per page
├── Add `export const prerender = false` to SSR pages
├── Use for: mostly static + login pages, API routes, user dashboards

server
├── All pages rendered per request
├── Requires a server runtime
├── Use for: highly dynamic apps (dashboards, SaaS)
```

For our blog, static is perfect — all content is known at build time.

### Step 2: Build the site

```bash
npm run build
```

Explore the output:

```bash
ls dist/
```

**Teaching moment**: Point out the structure:
```
dist/
├── index.html          ← Home page
├── about/index.html    ← About page
├── blog/
│   ├── index.html      ← Blog index
│   ├── first-post/index.html
│   ├── second-post/index.html
│   └── third-post/index.html
├── rss.xml             ← RSS feed
└── _astro/             ← CSS and JS bundles (only for islands!)
```

Open a static page (like `about/index.html`). Notice: minimal or zero `<script>` tags. Open a page with islands: you'll see targeted script tags only for the interactive components.

### Step 3: Preview the built site

```bash
npm run preview
```

Visit `http://localhost:4321/` — this serves the `dist/` folder exactly as a hosting provider would. Verify everything works:
- All pages load
- Links work
- Islands are interactive
- View transitions work
- RSS feed accessible

### Step 4: Deploy

**Option A: Static hosting (no adapter needed)**

For static sites, deploy the `dist/` folder to any static host:

- **Netlify**: `npx netlify deploy --dir=dist --prod` (or connect Git repo)
- **Vercel**: `npx vercel --prod` (auto-detects Astro)
- **GitHub Pages**: Push `dist/` to the `gh-pages` branch
- **Cloudflare Pages**: Connect Git repo, set build command to `npm run build`

Walk the user through one option (suggest Netlify or Vercel as simplest for beginners).

**Option B: SSR hosting (adapter required)**

If they later want SSR (`hybrid` or `server` mode):

```bash
npx astro add netlify  # or vercel, node, cloudflare
```

This installs the adapter and configures `astro.config.mjs`.

### Step 5: What to explore next

After completing the tutorial, suggest areas for growth:
- **Server Islands** (`server:defer`) — Personalized content on static pages
- **Astro Actions** — Type-safe form handling
- **Sessions** — Server-side user state
- **i18n Routing** — Multilingual sites
- **MDX** — Use components inside Markdown
- **Image optimization** — `<Image />` and `<Picture />` components

All of these are covered in the `astro-framework` skill references.

### Step 6: Verify

- `npm run build` completes without errors
- `npm run preview` serves a working site
- All pages accessible, islands interactive
- (Optional) Site deployed and accessible online

**Reflect**: Load `reflect/deploy.md`

---

## Lesson 3 Complete! Tutorial Finished!

**What you built in Lesson 3:**
- Interactive islands with Preact (LikeButton, SearchPosts)
- Client directives (`client:load`, `client:idle`) for selective hydration
- View transitions with `ClientRouter` for SPA-like navigation
- Shared element transitions between pages
- Production build and deployment

**What you built across all 3 lessons:**
- A complete personal blog with Astro
- Pages, components, layouts with Props and Slots
- Scoped and global CSS
- Content collections with Zod schema validation
- Dynamic routes with `getStaticPaths()`
- Blog index with sorting, filtering, pagination
- RSS feed as an API endpoint
- Interactive islands shipping minimal JavaScript
- View transitions for smooth navigation
- Production build ready for deployment

**What you deeply understand:**
- Astro's component model: frontmatter runs on the server, templates generate HTML
- File-based routing: files → URLs, dynamic routes with `getStaticPaths()`
- Content Layer: loaders, schemas, querying, rendering
- Islands architecture: zero JS default, selective hydration with client directives
- The decision framework for when to use static vs interactive components

**Git commit:**
```bash
git add .
git commit -m "Lesson 3: Islands, view transitions, and production deployment"
```

Congratulations! You now have the foundation to build any content-driven website with Astro. The patterns you've learned — components, content collections, islands — scale from personal blogs to documentation sites to e-commerce storefronts.
