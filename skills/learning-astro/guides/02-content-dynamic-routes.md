# Lesson 2: Content & Dynamic Routes

**Time**: ~45 minutes
**Goal**: Add blog posts to your site using Content Collections with type-safe schemas and dynamic routing.
**Prerequisites**: Completed Lesson 1 (pages, components, layout, styling)

By the end of this lesson, your blog will have real posts with validated frontmatter, individual post pages generated from dynamic routes, a sorted blog index, and an RSS feed.

---

## Part 1: Content Collections (~15 min)

### Architecture Overview

Load `concepts/content-layer.md` and explain the Content Layer pipeline:

```
Markdown files → glob loader → Zod schema validation → Type-safe data → Your pages
```

**Key concept**: Content Collections give you type-safe content management. Define a schema once, and Astro validates every blog post at build time. A missing title or invalid date? You'll know immediately — not after deploying.

### Step 1: Create blog posts

Create the content directory and 3 sample posts:

Create `src/content/blog/first-post.md`:

```markdown
---
title: "My First Blog Post"
description: "I just started learning Astro and I'm already loving it."
pubDate: 2024-01-15
tags: ["astro", "learning"]
---

# Getting Started with Astro

Today I started learning Astro. What immediately stood out to me is that it ships **zero JavaScript** by default. That means my blog is incredibly fast right from the start.

## What I've Learned So Far

- Astro uses file-based routing
- Components have a frontmatter section for server-side code
- Styles are scoped by default
- Layouts use slots for composability

I'm excited to keep learning!
```

Create `src/content/blog/second-post.md`:

```markdown
---
title: "Understanding Astro Components"
description: "A deep dive into how Astro components work."
pubDate: 2024-02-01
tags: ["astro", "components"]
---

# How Astro Components Work

Astro components are divided into three sections:

1. **Frontmatter** — Server-side JavaScript between `---` fences
2. **Template** — HTML with expressions like `{variable}`
3. **Styles** — Scoped CSS that only affects this component

The key insight is that the frontmatter runs at **build time**, not in the browser. This is why Astro sites are so fast — there's no JavaScript to download and execute.
```

Create `src/content/blog/third-post.md`:

```markdown
---
title: "Building Layouts and Reusable Components"
description: "How to use Props and Slots to build reusable Astro components."
pubDate: 2024-03-10
tags: ["astro", "components", "layouts"]
draft: false
---

# Layouts and Components

One of the best things about Astro is how clean the component model is.

## Props for Data

Components accept props through an `interface Props` definition. This gives you TypeScript autocomplete and type checking — catch errors before they hit production.

## Slots for Composition

Layouts use `<slot />` to define where child content goes. This is similar to React's `children` prop but feels more natural in HTML.
```

### Step 2: Define the content collection

Create `src/content.config.ts`:

```typescript
import { defineCollection } from 'astro:content';
import { glob } from 'astro/loaders';
import { z } from 'astro/zod';

const blog = defineCollection({
  loader: glob({ base: './src/content/blog', pattern: '**/*.{md,mdx}' }),
  schema: z.object({
    title: z.string(),
    description: z.string(),
    pubDate: z.coerce.date(),
    updatedDate: z.coerce.date().optional(),
    tags: z.array(z.string()).default([]),
    draft: z.boolean().default(false),
  }),
});

export const collections = { blog };
```

**Teaching moment — explain each part:**

- **`glob` loader**: Reads all Markdown files from `./src/content/blog`. Each file becomes one entry.
- **`z.coerce.date()`**: Frontmatter dates are strings like `"2024-01-15"`. `coerce` automatically converts them to JavaScript Date objects.
- **`z.array(z.string()).default([])`**: Tags are optional — if missing, defaults to an empty array.
- **`z.boolean().default(false)`**: The `draft` field defaults to `false` (published).
- **`export const collections`**: This registers the collection with Astro. The key `blog` is the name you'll use in `getCollection('blog')`.

**Important**: The file is `src/content.config.ts` (at the `src/` root), NOT `src/content/config.ts`. This is the Astro 5+ convention.

### Step 3: Show type safety in action

Temporarily break a post to demonstrate validation. Remove the `title` from one post's frontmatter, then run:

```bash
npm run build
```

Astro will show a clear error like:
```
[ERROR] blog → first-post.md: "title" is required
```

**Teaching moment**: This is the power of schema validation — errors are caught at build time, not after deployment. Restore the title and continue.

### Step 4: Use the collection in a page

Update `src/pages/index.astro` to list blog posts:

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
import Card from '../components/Card.astro';
import { getCollection } from 'astro:content';

const posts = await getCollection('blog');
const sortedPosts = posts.sort(
  (a, b) => b.data.pubDate.valueOf() - a.data.pubDate.valueOf()
);
---

<BaseLayout title="My Blog">
  <h1>My Blog</h1>
  <p>Welcome! Here's what I've been writing about.</p>

  <section>
    {sortedPosts.map(post => (
      <Card
        title={post.data.title}
        body={post.data.description}
        href={`/blog/${post.id}`}
      />
    ))}
  </section>
</BaseLayout>
```

**Teaching moment**: `getCollection('blog')` returns all entries, fully typed. `post.data.title` has autocomplete because of the Zod schema. The data lives in `post.data`, and `post.id` is the filename without extension.

### Step 5: Verify

- Home page lists all 3 posts sorted by date (newest first)
- Post data comes from frontmatter (title, description)
- Links point to `/blog/[slug]` (will 404 until Part 2 — that's expected)

**Reflect**: Load `reflect/content-collections.md`

---

## Part 2: Dynamic Routes for Blog Posts (~15 min)

### Architecture Context

Briefly revisit `concepts/file-based-routing.md` — specifically the dynamic routes section.

**Key concept**: We need to create one page template that generates a unique page for every blog post. That's what dynamic routes do — `[...slug].astro` tells Astro: "generate a page for each entry returned by `getStaticPaths()`."

### Step 1: Create the blog post layout

Create `src/layouts/BlogPost.astro`:

```astro
---
import BaseLayout from './BaseLayout.astro';

interface Props {
  title: string;
  pubDate: Date;
  description: string;
  tags: string[];
}

const { title, pubDate, description, tags } = Astro.props;
---

<BaseLayout title={title}>
  <article>
    <header>
      <h1>{title}</h1>
      <p class="description">{description}</p>
      <div class="meta">
        <time datetime={pubDate.toISOString()}>
          {pubDate.toLocaleDateString('en-US', {
            year: 'numeric',
            month: 'long',
            day: 'numeric',
          })}
        </time>
        {tags.length > 0 && (
          <div class="tags">
            {tags.map(tag => <span class="tag">{tag}</span>)}
          </div>
        )}
      </div>
    </header>
    <hr />
    <slot />
  </article>
</BaseLayout>

<style>
  article {
    max-width: 65ch;
  }

  header {
    margin-bottom: 2rem;
  }

  h1 {
    font-size: 2rem;
    margin-bottom: 0.5rem;
  }

  .description {
    color: var(--color-text-light);
    font-size: 1.1rem;
    margin-bottom: 0.5rem;
  }

  .meta {
    display: flex;
    gap: 1rem;
    align-items: center;
    color: var(--color-text-light);
    font-size: 0.9rem;
  }

  .tags {
    display: flex;
    gap: 0.5rem;
  }

  .tag {
    background: var(--color-bg-alt);
    padding: 0.15rem 0.5rem;
    border-radius: 0.25rem;
    font-size: 0.8rem;
  }

  hr {
    border: none;
    border-top: 1px solid #e2e8f0;
    margin: 1.5rem 0;
  }
</style>
```

### Step 2: Create the dynamic route

Create `src/pages/blog/[...slug].astro`:

```astro
---
import { getCollection, render } from 'astro:content';
import BlogPost from '../../layouts/BlogPost.astro';

export async function getStaticPaths() {
  const posts = await getCollection('blog');
  return posts.map(post => ({
    params: { slug: post.id },
    props: { post },
  }));
}

const { post } = Astro.props;
const { Content } = await render(post);
---

<BlogPost
  title={post.data.title}
  pubDate={post.data.pubDate}
  description={post.data.description}
  tags={post.data.tags}
>
  <Content />
</BlogPost>
```

**Teaching moment — explain each part:**

1. **`getStaticPaths()`**: Astro calls this at build time. It returns an array: one entry per blog post. Each entry has `params` (the URL slug) and `props` (the data passed to the page).

2. **`params: { slug: post.id }`**: `post.id` is the filename without extension. So `first-post.md` → `slug: "first-post"` → URL `/blog/first-post`.

3. **`render(post)`**: Converts the Markdown content into a renderable `<Content />` component. This is the Astro 5+ API — imported from `'astro:content'`.

4. **`<Content />`**: Placed inside the `<slot />` of BlogPost layout, rendering the Markdown as formatted HTML.

### Step 3: Verify

- Visit `/blog/first-post` — the post renders with title, date, tags, and content
- Visit `/blog/second-post` and `/blog/third-post` — each works
- The layout wraps each post consistently
- Links from the home page work

**Reflect**: Load `reflect/dynamic-routes.md`

---

## Part 3: Blog Index, Pagination & RSS (~15 min)

### Step 1: Create a dedicated blog index page

Create `src/pages/blog/index.astro`:

```astro
---
import BaseLayout from '../../layouts/BaseLayout.astro';
import { getCollection } from 'astro:content';

const allPosts = await getCollection('blog', ({ data }) => !data.draft);
const sortedPosts = allPosts.sort(
  (a, b) => b.data.pubDate.valueOf() - a.data.pubDate.valueOf()
);
---

<BaseLayout title="Blog">
  <h1>Blog</h1>
  <p>All my posts, newest first.</p>

  <ul class="post-list">
    {sortedPosts.map(post => (
      <li>
        <a href={`/blog/${post.id}`}>
          <h3>{post.data.title}</h3>
          <p class="description">{post.data.description}</p>
          <time>
            {post.data.pubDate.toLocaleDateString('en-US', {
              year: 'numeric',
              month: 'short',
              day: 'numeric',
            })}
          </time>
        </a>
      </li>
    ))}
  </ul>
</BaseLayout>

<style>
  .post-list {
    list-style: none;
    padding: 0;
  }

  .post-list li {
    margin-bottom: 1.5rem;
    padding-bottom: 1.5rem;
    border-bottom: 1px solid #e2e8f0;
  }

  .post-list a {
    text-decoration: none;
    color: inherit;
    display: block;
  }

  .post-list a:hover h3 {
    color: var(--color-primary);
  }

  h3 {
    margin: 0 0 0.25rem;
    font-size: 1.2rem;
  }

  .description {
    color: var(--color-text-light);
    margin: 0 0 0.25rem;
  }

  time {
    color: var(--color-text-light);
    font-size: 0.85rem;
  }
</style>
```

**Teaching moment**: Notice `getCollection('blog', ({ data }) => !data.draft)` — the second argument is a filter function. This excludes any post with `draft: true`.

### Step 2: Add RSS feed

Install the RSS package:

```bash
npx astro add rss
```

Create `src/pages/rss.xml.ts`:

```typescript
import rss from '@astrojs/rss';
import { getCollection } from 'astro:content';
import type { APIContext } from 'astro';

export async function GET(context: APIContext) {
  const posts = await getCollection('blog', ({ data }) => !data.draft);
  const sorted = posts.sort(
    (a, b) => b.data.pubDate.valueOf() - a.data.pubDate.valueOf()
  );

  return rss({
    title: 'My Blog',
    description: 'A blog about learning Astro',
    site: context.site ?? 'https://example.com',
    items: sorted.map(post => ({
      title: post.data.title,
      pubDate: post.data.pubDate,
      description: post.data.description,
      link: `/blog/${post.id}/`,
    })),
  });
}
```

**Teaching moment**: This is an **API endpoint** — a `.ts` file that exports an HTTP handler. It generates XML, not HTML. The file path `src/pages/rss.xml.ts` means it's accessible at `/rss.xml`.

Add the `site` property to `astro.config.mjs`:

```javascript
import { defineConfig } from 'astro/config';

export default defineConfig({
  site: 'https://example.com', // Replace with actual URL when deploying
});
```

Add the RSS link to `BaseLayout.astro` `<head>`:

```html
<link rel="alternate" type="application/rss+xml" title="My Blog" href="/rss.xml" />
```

### Step 3: Update Navigation

Update `src/components/Navigation.astro` to include the blog link (it should already be there from Lesson 1, but verify):

```astro
<nav>
  <a href="/">Home</a>
  <a href="/blog">Blog</a>
  <a href="/about">About</a>
</nav>
```

### Step 4: Verify

- `/blog` shows all posts sorted by date, drafts excluded
- Each post links to its individual page
- `/rss.xml` returns valid XML with all posts
- Navigation includes Blog link

**Reflect**: Load `reflect/blog-index.md`

---

## Lesson 2 Complete!

**What you built:**
- Content collections with Zod schema validation
- Dynamic routes generating individual blog post pages
- A blog index with sorting and draft filtering
- An RSS feed as an API endpoint

**What you learned:**
- Content Layer API: `defineCollection`, `glob` loader, Zod schemas
- `getCollection()` and `render()` from `astro:content`
- `getStaticPaths()` for dynamic route generation
- API endpoints in `src/pages/` (`.ts` files)

**Git commit:**
```bash
git add .
git commit -m "Lesson 2: Content collections, dynamic routes, blog index, and RSS"
```

Ready for Lesson 3? We'll add interactive components using Astro's islands architecture and prepare the site for deployment.
