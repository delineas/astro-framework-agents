# Reflect: Dynamic Routes

Let's pause and make sure everything is working before we keep building.

## Let's make sure it clicked

1. **Why do we need `getStaticPaths()` for `[...slug].astro`?**
   <details>
   <summary>Answer</summary>

   In static mode (Astro's default), all pages must be generated at build time. Astro needs to know every possible URL in advance to create the corresponding HTML files. `getStaticPaths()` tells Astro: "Here are all the pages you need to create and what data each one needs."
   </details>

2. **What does `render()` from `astro:content` return?**
   <details>
   <summary>Answer</summary>

   It returns an object with a `Content` component (the rendered Markdown as an Astro component you can use in your template) and `headings` (an array of heading elements found in the content). In Astro 5+, you import `render` from `'astro:content'` and call it as `render(entry)`.
   </details>

3. **What is the `...` in `[...slug]` for?**
   <details>
   <summary>Answer</summary>

   The `...` makes it a "rest parameter" that captures the entire remaining URL path, including nested segments. For example, `[...slug].astro` matches `/blog/my-post` but also `/blog/2024/my-post`. Plain `[slug].astro` would only match a single segment.
   </details>

## Quick review

### 1. Dynamic Route File

Show me `src/pages/blog/[...slug].astro`:

- [ ] Exports `getStaticPaths()` function
- [ ] `getStaticPaths` calls `getCollection('blog')`
- [ ] Returns `params: { slug: post.id }` for each post
- [ ] Returns `props: { post }` for each post
- [ ] Uses `render(post)` from `'astro:content'` (not `post.render()`)
- [ ] Destructures `{ Content }` from the render result
- [ ] Wraps content in `BlogPost` layout

### 2. Blog Post Layout

Show me `src/layouts/BlogPost.astro`:

- [ ] Accepts props: `title`, `pubDate`, `description`, `tags`
- [ ] Uses BaseLayout for consistent site structure
- [ ] Displays formatted date
- [ ] Shows tags (if present)
- [ ] Has `<slot />` for the rendered Markdown content

### 3. URLs Work

- [ ] `/blog/first-post` loads the first blog post
- [ ] `/blog/second-post` loads the second blog post
- [ ] `/blog/third-post` loads the third blog post
- [ ] None return 404

## If something's not working

### 404 on blog post URLs

**Cause**: `getStaticPaths()` missing or returning wrong params.

**Fix**: Ensure you're returning `params: { slug: post.id }` for each post. The `slug` param name must match the `[...slug]` in the filename.

### "`entry.render()` is not a function"

**Cause**: Using the Astro 4 API. In Astro 5+, `render` is a standalone import.

**Fix**:
```typescript
// Wrong (Astro 4):
const { Content } = await post.render();

// Right (Astro 5+):
import { render } from 'astro:content';
const { Content } = await render(post);
```

### Content renders but looks unstyled

**Cause**: Markdown-generated HTML doesn't have your component's scoped class hashes.

**Fix**: Add styles for Markdown elements using `:global()` in the BlogPost layout, or use a global CSS file for article content styling.

### Missing layout wrapper

**Cause**: Content renders without navigation/footer.

**Fix**: Ensure BlogPost.astro uses BaseLayout internally.

## Ready to move on?

- [ ] Each blog post has its own URL at `/blog/[slug]`
- [ ] Post title, date, and tags display correctly
- [ ] Markdown content renders as formatted HTML
- [ ] Layout wraps each post (navigation, footer visible)
- [ ] Links from the home page navigate to individual posts
- [ ] `npm run build` lists all generated blog post pages

## Go further

These are optional explorations if you're curious — skip them and come back later if you prefer:

- Try creating a tag page: `src/pages/tags/[tag].astro` that lists posts by tag
- Explore what happens if you rename `[...slug].astro` to `[slug].astro` (without the rest param)
- Add previous/next post navigation to the blog post layout

## Next Steps

All good? Here's what's next:

1. **Dynamic routes** generate a page for each blog post
2. **Next**: Build the blog index with sorting, filtering, and RSS (Part 3 of Lesson 2)

**Ready to continue?**
