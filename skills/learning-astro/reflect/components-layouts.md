# Reflect: Components, Props & Layouts

Let's pause and make sure everything is working before we keep building.

## Let's make sure it clicked

1. **What is the difference between the frontmatter and the template in an Astro component?**
   <details>
   <summary>Answer</summary>

   The frontmatter (between `---` fences) is server-side JavaScript that runs at build time — imports, data fetching, prop access happen here. The template is HTML that gets sent to the browser, with `{expressions}` for dynamic content. The frontmatter is never visible to the user.
   </details>

2. **How do you pass data to a component?**
   <details>
   <summary>Answer</summary>

   Via attributes when using the component: `<Card title="Hello" />`. The component accesses them through `Astro.props`: `const { title } = Astro.props;`. You define the expected shape with `interface Props { title: string; }` for type safety.
   </details>

3. **What does `<slot />` do in a layout?**
   <details>
   <summary>Answer</summary>

   `<slot />` is a placeholder that renders whatever child content the parent passes in. When you write `<BaseLayout><h1>Hello</h1></BaseLayout>`, the `<h1>Hello</h1>` appears where `<slot />` is placed in BaseLayout. It's similar to React's `children` prop.
   </details>

4. **Why don't we need `import React from 'react'` in `.astro` files?**
   <details>
   <summary>Answer</summary>

   Astro has its own template syntax — it's not JSX. While it looks similar (using `{expressions}`), it's actually HTML with Astro-specific features. You use `class` instead of `className`, `for` instead of `htmlFor`, and you don't need a React runtime at all.
   </details>

## Quick review

### 1. Component Files Exist

```bash
ls src/components/ src/layouts/
```

**Expected**:
```
src/components/:
Card.astro  Navigation.astro

src/layouts/:
BaseLayout.astro
```

### 2. Card Component

Show me `src/components/Card.astro`:

- [ ] Has `interface Props` with `title: string` and `body: string`
- [ ] Destructures from `Astro.props`
- [ ] Renders title and body in the template
- [ ] Optional `href` prop used for linking

### 3. BaseLayout

Show me `src/layouts/BaseLayout.astro`:

- [ ] Imports Navigation component
- [ ] Has `interface Props` with `title: string`
- [ ] Contains full HTML structure (`<html>`, `<head>`, `<body>`)
- [ ] Uses `{title}` in `<title>` tag
- [ ] Has `<slot />` in the `<main>` section
- [ ] Includes Navigation and Footer

### 4. Pages Use Layout

- [ ] `index.astro` imports and uses `<BaseLayout title="...">`
- [ ] `about.astro` imports and uses `<BaseLayout title="...">`
- [ ] Neither page has its own `<html>` or `<head>` tags (layout handles those)

## If something's not working

### Component not rendering

**Cause**: Forgot to import the component in the frontmatter.

**Fix**: Add `import Card from '../components/Card.astro';` in the `---` fences.

### Props are undefined

**Cause**: Not destructuring from `Astro.props`.

**Fix**: Use `const { title } = Astro.props;` (not just `const title = ...`).

### Slot content not appearing

**Cause**: Missing `<slot />` in the layout template.

**Fix**: Add `<slot />` where child content should render.

### TypeScript error on Props

**Cause**: Missing or incorrect `interface Props` definition.

**Fix**: Ensure the interface matches what you're passing: `interface Props { title: string; }`.

## Ready to move on?

- [ ] All pages render with the shared layout (Navigation + Footer visible)
- [ ] Card component renders with different props on the home page
- [ ] Navigation appears on every page
- [ ] Changing the layout updates all pages at once
- [ ] No duplicated HTML boilerplate in page files

## Go further

These are optional explorations if you're curious — skip them and come back later if you prefer:

- Try creating a named slot: `<slot name="sidebar" />` and pass content to it with `slot="sidebar"`
- Create a component that conditionally renders based on a boolean prop
- Explore `Astro.slots.has('sidebar')` for conditional slot rendering

## Next Steps

All good? Here's what's next:

1. **Components and Layout** are working with Props and Slots
2. **Next**: Add styling — scoped CSS, global styles, and class:list (Part 3 of Lesson 1)

**Ready to continue?**
