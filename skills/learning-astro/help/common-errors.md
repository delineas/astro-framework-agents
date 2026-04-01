# Common Errors

A catalog of errors you may encounter during the tutorial, organized by category. Each entry has: **Symptom**, **Cause**, **Solution**.

## Table of Contents

- [Project Setup Errors](#project-setup-errors)
- [Component Errors](#component-errors)
- [Content Collection Errors](#content-collection-errors)
- [Routing Errors](#routing-errors)
- [Styling Errors](#styling-errors)
- [Islands / Hydration Errors](#islands--hydration-errors)
- [View Transitions Errors](#view-transitions-errors)
- [Build / Deploy Errors](#build--deploy-errors)

---

## Project Setup Errors

### "Cannot find module 'astro'"

**Symptom**: Error when running any `astro` command

**Cause**: Dependencies not installed, or running the command outside the project directory

**Solution**:
1. Make sure you're in the project directory: `cd my-blog`
2. Install dependencies: `npm install`
3. Try again: `npm run dev`

### Port 4321 already in use

**Symptom**: `Error: listen EADDRINUSE: address already in use :::4321`

**Cause**: Another process (perhaps a previous dev server) is using port 4321

**Solution**:
1. Kill the existing process: `lsof -i :4321` then `kill <PID>`
2. Or use a different port: `npm run dev -- --port 3000`

### Node.js version too old

**Symptom**: Syntax errors or "unsupported engine" warnings during `npm create astro`

**Cause**: Astro requires Node.js 18+

**Solution**:
1. Check your version: `node -v`
2. If below 18, update Node.js: visit nodejs.org or use `nvm install 18`

---

## Component Errors

### "window is not defined" / "document is not defined"

**Symptom**: Error during build or dev server startup

**Cause**: Accessing browser-only APIs (`window`, `document`, `localStorage`) in the component frontmatter, which runs on the server

**Solution**:
Move browser API usage to a `<script>` tag (runs in browser) or into a framework component with a `client:` directive:
```astro
<!-- Wrong: browser API in frontmatter -->
---
const width = window.innerWidth; // ERROR!
---

<!-- Right: browser API in script tag -->
<script>
  const width = window.innerWidth;
  console.log(width);
</script>
```

### Props not passing correctly

**Symptom**: Component renders but props are `undefined`

**Cause**: Forgot to define `interface Props` or destructure from `Astro.props`

**Solution**:
```astro
---
interface Props {
  title: string;
}
const { title } = Astro.props;  // Must destructure from Astro.props
---
<h1>{title}</h1>
```

### Slot content not rendering

**Symptom**: Content passed to a component doesn't appear

**Cause**: Missing `<slot />` in the component template

**Solution**: Add `<slot />` where child content should appear:
```astro
<!-- Layout.astro -->
<main>
  <slot />  <!-- This is required for child content to render -->
</main>
```

### "Expected a default export"

**Symptom**: Error when importing a component

**Cause**: Component file is empty or has no template section

**Solution**: Ensure the `.astro` file has at least a template (HTML) section, even if the frontmatter is empty.

---

## Content Collection Errors

### "Content config file not found"

**Symptom**: Content collections don't work; `getCollection()` returns nothing or errors

**Cause**: Using the wrong config file path. Astro 5+ uses `src/content.config.ts`, NOT `src/content/config.ts` (the Astro 4 path)

**Solution**: Rename or move your config file:
```bash
# If you have src/content/config.ts, move it:
mv src/content/config.ts src/content.config.ts
```

### Schema validation error

**Symptom**: Build error mentioning Zod validation failure on a specific field

**Cause**: A blog post's frontmatter doesn't match the schema. Common culprits: missing required field, wrong date format, wrong type.

**Solution**:
1. Read the error message — it tells you exactly which field failed and why
2. Fix the frontmatter in the offending file
3. Example: if `pubDate` fails, ensure it's a valid date string: `pubDate: 2024-01-15`

### "Cannot find module 'zod'"

**Symptom**: Import error when defining schemas

**Cause**: Importing Zod from the `zod` package instead of `astro/zod`

**Solution**:
```typescript
// Wrong:
import { z } from 'zod';

// Right:
import { z } from 'astro/zod';
```

### "Collection not found"

**Symptom**: `getCollection('blog')` returns undefined or errors

**Cause**: Collection name mismatch between `content.config.ts` and usage, or the config file isn't exporting `collections`

**Solution**: Ensure the name matches exactly:
```typescript
// In content.config.ts:
export const collections = { blog: blogCollection };

// In your page:
const posts = await getCollection('blog'); // Must match 'blog'
```

### "`entry.render()` is not a function"

**Symptom**: Error when trying to render a content entry

**Cause**: Astro 5 changed the render API. `entry.render()` was the Astro 4 pattern.

**Solution**: Use the new top-level `render()` import:
```typescript
// Wrong (Astro 4):
const { Content } = await entry.render();

// Right (Astro 5+):
import { render } from 'astro:content';
const { Content } = await render(entry);
```

---

## Routing Errors

### 404 on dynamic routes

**Symptom**: Blog post URLs return 404

**Cause**: Missing `getStaticPaths()` in the dynamic route file (required in static mode)

**Solution**: Add `getStaticPaths()` that returns all possible paths:
```astro
---
export async function getStaticPaths() {
  const posts = await getCollection('blog');
  return posts.map(post => ({
    params: { slug: post.id },
    props: { post },
  }));
}
---
```

### "getStaticPaths() function required for dynamic routes"

**Symptom**: Build error on a `[slug].astro` or `[...slug].astro` file

**Cause**: Dynamic route files MUST export `getStaticPaths()` in static output mode

**Solution**: Add the function as shown above.

### Wrong URL structure

**Symptom**: Pages exist but at unexpected URLs

**Cause**: File placement doesn't match expected URL

**Solution**: Remember the mapping:
- `src/pages/blog/index.astro` → `/blog`
- `src/pages/blog/[slug].astro` → `/blog/my-post`
- `src/pages/blog/[...slug].astro` → `/blog/any/nested/path`

---

## Styling Errors

### Styles not applying to child components

**Symptom**: CSS in a parent component doesn't affect elements in child components

**Cause**: Astro scopes CSS by default — styles only affect the component they're defined in

**Solution**: Use `:global()` to target child elements, or pass classes as props:
```astro
<style>
  /* Scoped: only affects elements in THIS component */
  h1 { color: blue; }

  /* Global: affects elements everywhere */
  :global(.child-class) { color: red; }
</style>
```

### Global styles overriding everything

**Symptom**: Styles defined in one place affect the entire site

**Cause**: Using `is:global` too broadly or importing a CSS file that doesn't use scoped styles

**Solution**: Remove `is:global` from `<style>` tags unless intentional. Use scoped styles by default.

---

## Islands / Hydration Errors

### Component renders but isn't interactive

**Symptom**: A React/Preact component appears on the page but clicking buttons does nothing

**Cause**: Missing `client:` directive. Without it, the component renders to static HTML only.

**Solution**: Add the appropriate directive:
```astro
<!-- Static (no interactivity): -->
<Counter />

<!-- Interactive: -->
<Counter client:load />
```

### "Cannot find package 'preact'" (or react, vue, svelte)

**Symptom**: Error when using a framework component

**Cause**: The framework integration isn't installed

**Solution**: Install it using Astro's CLI:
```bash
npx astro add preact
```
This installs the package AND configures `astro.config.mjs` automatically.

### `client:only` without framework name

**Symptom**: Error or unexpected behavior with `client:only`

**Cause**: `client:only` requires specifying which framework to use

**Solution**: Always include the framework name:
```astro
<!-- Wrong: -->
<Component client:only />

<!-- Right: -->
<Component client:only="preact" />
```

### Hydration mismatch warning

**Symptom**: Console warning about server/client HTML mismatch

**Cause**: Component renders differently on server vs client (common with random values, dates, or browser-dependent logic)

**Solution**: Ensure the component renders the same output on both server and client. For browser-dependent rendering, use `client:only` to skip server rendering entirely.

---

## View Transitions Errors

### Scripts not re-running after navigation

**Symptom**: JavaScript in `<script>` tags only works on the first page load

**Cause**: With view transitions, scripts don't re-execute on navigation by default

**Solution**: Listen to the `astro:page-load` event:
```astro
<script>
  document.addEventListener('astro:page-load', () => {
    // This runs on every navigation, including the first page load
    setupMyFeature();
  });
</script>
```

### "ViewTransitions is not exported from 'astro:transitions'"

**Symptom**: Import error for `ViewTransitions`

**Cause**: Astro 5 renamed `ViewTransitions` to `ClientRouter`

**Solution**:
```astro
<!-- Wrong (Astro 4): -->
import { ViewTransitions } from 'astro:transitions';
<ViewTransitions />

<!-- Right (Astro 5+): -->
import { ClientRouter } from 'astro:transitions';
<ClientRouter />
```

---

## Build / Deploy Errors

### "Astro.request is not available in prerendered pages"

**Symptom**: Build error referencing `Astro.request`

**Cause**: Accessing request-time data (headers, cookies, URL params) in a page rendered at build time

**Solution**: Either:
1. Mark the page as server-rendered: `export const prerender = false;`
2. Or switch to `hybrid` output mode in `astro.config.mjs`

### Build fails with "missing adapter"

**Symptom**: Error when building with `output: 'hybrid'` or `output: 'server'`

**Cause**: SSR modes require a deployment adapter

**Solution**: Install an adapter:
```bash
npx astro add netlify  # or vercel, node, cloudflare
```

### Build succeeds but pages are blank

**Symptom**: HTML files in `dist/` are empty or have no content

**Cause**: Components or pages have errors that silently fail during build

**Solution**: Check `npm run build` output carefully for warnings. Run `npm run preview` to test the built site locally.
