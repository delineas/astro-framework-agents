# Content Layer

## What is the Content Layer?

The Content Layer is Astro's system for loading, validating, and querying structured content. It transforms raw files (Markdown, JSON, etc.) into type-safe, queryable data.

```
┌──────────────────┐    ┌──────────────┐    ┌───────────────┐    ┌──────────────┐
│  Content Source   │───▶│   Loader     │───▶│  Zod Schema   │───▶│  Type-safe   │
│  (md, json, API) │    │  (glob/file) │    │  (validation) │    │  Data Store  │
└──────────────────┘    └──────────────┘    └───────────────┘    └──────────────┘
                                                                        │
                                                                        ▼
                                                                ┌──────────────┐
                                                                │  Your Pages  │
                                                                │  getCollection()
                                                                │  render()    │
                                                                └──────────────┘
```

## Why Use Content Collections?

Without collections, you'd manually import files, parse frontmatter, and hope your data is consistent. Content collections give you:

1. **Schema validation**: A typo in your blog post date? Astro catches it at build time with a clear error message.
2. **Type safety**: Your editor knows exactly what fields exist on a blog post. Autocomplete works.
3. **Performance**: Astro optimizes data loading and caching behind the scenes.
4. **Consistency**: All entries in a collection share the same shape. No surprises.

## Configuration (Astro 5+)

Collections are defined in `src/content.config.ts` — note the file location (NOT `src/content/config.ts`, which was the Astro 4 pattern):

```typescript
// src/content.config.ts
import { defineCollection } from 'astro:content';
import { glob } from 'astro/loaders';
import { z } from 'astro/zod';  // Import Zod from astro/zod, NOT from 'zod'

const blog = defineCollection({
  loader: glob({ base: './src/content/blog', pattern: '**/*.{md,mdx}' }),
  schema: z.object({
    title: z.string(),
    description: z.string(),
    pubDate: z.coerce.date(),       // coerce: converts string "2024-01-15" to Date
    updatedDate: z.coerce.date().optional(),
    tags: z.array(z.string()).default([]),
    draft: z.boolean().default(false),
  }),
});

export const collections = { blog };
```

### Key Details

- **`z.coerce.date()`**: Frontmatter dates come as strings. `coerce` converts them to Date objects automatically. Using `z.date()` would fail.
- **`z.array(z.string()).default([])`**: Makes tags optional with a default empty array.
- **`z.boolean().default(false)`**: Draft posts default to published.
- **Import `z` from `'astro/zod'`**: Not from the `zod` package directly. Astro ships its own Zod version for compatibility.

## Built-in Loaders

### `glob()` — Multiple local files

Loads entries from a directory of files. Each file becomes one entry. The file name (minus extension) becomes the entry's `id`.

```typescript
import { glob } from 'astro/loaders';

loader: glob({ base: './src/content/blog', pattern: '**/*.{md,mdx}' })
```

### `file()` — Single data file

Loads entries from a single JSON or YAML file. Each top-level item becomes one entry.

```typescript
import { file } from 'astro/loaders';

loader: file('src/data/authors.json')
```

## Querying Content

### Get all entries

```typescript
import { getCollection } from 'astro:content';

// All blog posts
const allPosts = await getCollection('blog');

// Filtered: only published posts, sorted by date
const publishedPosts = await getCollection('blog', ({ data }) => !data.draft);
const sorted = publishedPosts.sort(
  (a, b) => b.data.pubDate.valueOf() - a.data.pubDate.valueOf()
);
```

### Get a single entry

```typescript
import { getEntry } from 'astro:content';

const post = await getEntry('blog', 'my-first-post');
```

## Rendering Content

In Astro 5+, rendering uses a top-level `render()` function (not `entry.render()`):

```typescript
import { render } from 'astro:content';

const post = await getEntry('blog', 'my-first-post');
const { Content, headings } = await render(post);
```

Then in the template:

```astro
<article>
  <h1>{post.data.title}</h1>
  <time>{post.data.pubDate.toLocaleDateString()}</time>
  <Content />
</article>
```

## Common Gotchas

1. **Wrong config path**: Use `src/content.config.ts`, not `src/content/config.ts`
2. **Wrong Zod import**: Use `import { z } from 'astro/zod'`, not `from 'zod'`
3. **Legacy `entry.render()`**: In Astro 5+, use `import { render } from 'astro:content'` then `render(entry)`
4. **Missing loader**: Astro 5+ requires a `loader` property — there's no implicit file loading
5. **Date validation**: Use `z.coerce.date()` for frontmatter dates, not `z.date()`
