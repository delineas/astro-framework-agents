# Reflect: Content Collections

Let's pause and make sure everything is working before we keep building.

## Let's make sure it clicked

1. **Why do we use `z.coerce.date()` instead of `z.date()` for frontmatter dates?**
   <details>
   <summary>Answer</summary>

   Frontmatter dates come as strings (e.g., `"2024-01-15"`), not JavaScript Date objects. `z.coerce.date()` automatically converts the string to a Date object. `z.date()` would expect an actual Date object and fail validation.
   </details>

2. **What happens if a blog post has frontmatter that doesn't match the schema?**
   <details>
   <summary>Answer</summary>

   Astro shows a clear build error with the exact field that failed validation and why (e.g., `"title" is required`). This is one of the main benefits of Content Collections — errors are caught at build time, not after deployment.
   </details>

3. **Why is the config file `src/content.config.ts` and not `src/content/config.ts`?**
   <details>
   <summary>Answer</summary>

   This is the Astro 5+ convention. The file moved from `src/content/config.ts` (Astro 4) to `src/content.config.ts` (at the src root) as part of the Content Layer API redesign. Using the old path will cause collections not to be found.
   </details>

4. **What does the `glob` loader do?**
   <details>
   <summary>Answer</summary>

   The `glob` loader reads all files matching a pattern from a directory. Each file becomes one collection entry. The file name (without extension) becomes the entry's `id`. For example, `glob({ base: './src/content/blog', pattern: '**/*.md' })` reads all Markdown files from the blog directory.
   </details>

## Quick review

### 1. Content Config

Show me `src/content.config.ts`:

- [ ] File is at `src/content.config.ts` (NOT `src/content/config.ts`)
- [ ] Imports `defineCollection` from `'astro:content'`
- [ ] Imports `glob` from `'astro/loaders'`
- [ ] Imports `z` from `'astro/zod'` (NOT from `'zod'`)
- [ ] Defines a `blog` collection with `glob` loader
- [ ] Schema includes: `title` (string), `description` (string), `pubDate` (coerce.date)
- [ ] Exports `collections` object with the `blog` key

### 2. Blog Posts

```bash
ls src/content/blog/
```

**Expected**: At least 3 `.md` files with valid frontmatter matching the schema.

### 3. Usage in Pages

- [ ] `index.astro` uses `getCollection('blog')` to fetch posts
- [ ] Posts are sorted by date (newest first)
- [ ] Each post displays title, description, and links to its URL

### 4. Schema Validation Works

- [ ] `npm run build` succeeds with valid posts
- [ ] Temporarily removing a required field (like `title`) causes a clear build error

## If something's not working

### "Content config file not found"

**Cause**: Config file at wrong path.

**Fix**: Ensure file is at `src/content.config.ts` (not inside `src/content/`).

### "Cannot find module 'zod'"

**Cause**: Wrong import path.

**Fix**: Change `import { z } from 'zod'` to `import { z } from 'astro/zod'`.

### getCollection returns empty array

**Cause**: Collection name mismatch or missing `export const collections`.

**Fix**: Verify the name in `getCollection('blog')` matches the key in `export const collections = { blog: ... }`.

### Schema validation error on dates

**Cause**: Using `z.date()` instead of `z.coerce.date()`.

**Fix**: Change to `pubDate: z.coerce.date()`.

## Ready to move on?

- [ ] `npm run build` succeeds
- [ ] Home page lists blog posts from the collection
- [ ] Posts are sorted by date (newest first)
- [ ] Post data (title, description) comes from frontmatter
- [ ] Adding a post with invalid frontmatter causes a build error
- [ ] TypeScript autocomplete works for `post.data.title` etc.

## Go further

These are optional explorations if you're curious — skip them and come back later if you prefer:

- Try adding a new field to the schema (e.g., `author: z.string()`) and see how it propagates
- Create a second collection (e.g., `projects`) with a different schema
- Experiment with `z.enum()` for a field like `category: z.enum(['tech', 'life', 'tutorial'])`

## Next Steps

All good? Here's what's next:

1. **Content collections** are set up with schema validation
2. **Next**: Create dynamic routes to give each blog post its own page (Part 2 of Lesson 2)

**Ready to continue?**
