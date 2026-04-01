# File-Based Routing

## How It Works

Astro maps files in `src/pages/` directly to URLs:

```
src/pages/
├── index.astro        → /
├── about.astro        → /about
├── blog/
│   ├── index.astro    → /blog
│   └── [...slug].astro → /blog/my-first-post (dynamic)
├── rss.xml.ts         → /rss.xml (API endpoint)
└── 404.astro          → /404 (custom error page)
```

The mapping is straightforward: the file path becomes the URL path. Astro strips the file extension and converts `index` files to directory roots.

## Static Routes

Every `.astro`, `.md`, or `.mdx` file in `src/pages/` becomes a page:

```astro
<!-- src/pages/about.astro → /about -->
---
import BaseLayout from '../layouts/BaseLayout.astro';
---

<BaseLayout title="About">
  <h1>About Me</h1>
</BaseLayout>
```

## Dynamic Routes

For pages generated from data (like blog posts), use bracket syntax:

### Single Parameter

```astro
<!-- src/pages/blog/[slug].astro → /blog/my-post -->
---
import { getCollection } from 'astro:content';

export async function getStaticPaths() {
  const posts = await getCollection('blog');
  return posts.map(post => ({
    params: { slug: post.id },
    props: { post },
  }));
}

const { post } = Astro.props;
---

<h1>{post.data.title}</h1>
```

### Rest Parameters

```astro
<!-- src/pages/blog/[...slug].astro → /blog/2024/my-post -->
```

The `...` captures the full remaining path, including nested segments.

### Why `getStaticPaths()` is Required

In static mode (default), Astro needs to know ALL possible URLs at build time to generate HTML files. `getStaticPaths()` tells Astro: "Here are all the pages you need to create."

```
Build time:
  getStaticPaths() returns 50 posts
  → Astro generates 50 HTML files
  → Each lives at /blog/[slug]/index.html
  → Pure static files, no server needed
```

In `hybrid` or `server` mode, dynamic routes without `getStaticPaths()` are rendered on-demand per request.

## API Endpoints

`.ts` or `.js` files in `src/pages/` that export HTTP method handlers become API endpoints:

```typescript
// src/pages/rss.xml.ts → /rss.xml
import rss from '@astrojs/rss';
import { getCollection } from 'astro:content';

export async function GET(context) {
  const posts = await getCollection('blog');
  return rss({
    title: 'My Blog',
    description: 'A blog about learning Astro',
    site: context.site,
    items: posts.map(post => ({
      title: post.data.title,
      pubDate: post.data.pubDate,
      link: `/blog/${post.id}/`,
    })),
  });
}
```

## Pagination

Astro provides built-in pagination through `paginate()`:

```astro
<!-- src/pages/blog/[...page].astro -->
---
import { getCollection } from 'astro:content';

export async function getStaticPaths({ paginate }) {
  const posts = await getCollection('blog');
  const sorted = posts.sort((a, b) => b.data.pubDate - a.data.pubDate);
  return paginate(sorted, { pageSize: 5 });
}

const { page } = Astro.props;
// page.data = posts for this page
// page.url.prev / page.url.next = navigation URLs
// page.currentPage, page.lastPage = page numbers
---

{page.data.map(post => <PostCard post={post} />)}

<nav>
  {page.url.prev && <a href={page.url.prev}>Previous</a>}
  <span>Page {page.currentPage} of {page.lastPage}</span>
  {page.url.next && <a href={page.url.next}>Next</a>}
</nav>
```

This generates:
- `/blog` → page 1
- `/blog/2` → page 2
- `/blog/3` → page 3

## Route Priority

When multiple files could match a URL, Astro uses this priority:
1. Static routes (`/about.astro`) — highest priority
2. Dynamic routes with named params (`/blog/[slug].astro`)
3. Rest parameters (`/blog/[...slug].astro`) — lowest priority
