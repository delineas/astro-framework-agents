# Astro Component Model

## Anatomy of an Astro Component

Every `.astro` file has up to three sections:

```
┌─────────────────────────────────────────┐
│  --- (frontmatter fence)                │
│  // Server-side JavaScript/TypeScript   │
│  // Runs at build time (or request time │
│  // in SSR). Top-level await allowed.   │
│  // No browser APIs here.               │
│  ---                                    │
├─────────────────────────────────────────┤
│  <!-- Template (HTML output) -->        │
│  Uses {expressions} for dynamic content │
│  Supports components, slots, directives │
│  Uses `class` not `className`           │
├─────────────────────────────────────────┤
│  <style>                                │
│  /* Scoped CSS by default */            │
│  /* Only affects THIS component */      │
│  </style>                               │
└─────────────────────────────────────────┘
```

## Why This Model Matters

The frontmatter/template separation is the foundation of Astro's performance story:

```
Traditional SPA (React/Next):
  Server renders HTML → Browser downloads JS bundle → JS re-renders everything
  Result: Every component ships JavaScript

Astro:
  Frontmatter runs on server → HTML generated → Sent to browser. Done.
  Result: Zero JavaScript by default
```

The key insight: most website content doesn't need interactivity. A blog post, a navigation bar, a product description — these are static. By running all the "logic" at build time in the frontmatter, Astro generates pure HTML with no runtime cost.

## Frontmatter: Server Code

The code between `---` fences runs on the server (at build time for static sites, at request time for SSR pages):

```astro
---
// Imports (components, utilities, data)
import BaseLayout from '../layouts/BaseLayout.astro';
import Card from '../components/Card.astro';

// Data fetching (top-level await works!)
const response = await fetch('https://api.example.com/posts');
const posts = await response.json();

// Computation
const sortedPosts = posts.sort((a, b) => b.date - a.date);
const pageTitle = "My Blog";

// Props (in components)
const { title, description } = Astro.props;
---
```

**What you CAN do in frontmatter:**
- Import components and modules
- Fetch data (top-level `await`)
- Access `Astro.props`, `Astro.url`, `Astro.request` (SSR)
- Run any Node.js-compatible code
- Read environment variables

**What you CANNOT do in frontmatter:**
- Access `window`, `document`, `localStorage` (browser APIs)
- Use React hooks (`useState`, `useEffect`)
- Add event listeners directly

## Template: HTML Output

The template section generates HTML using expressions:

```astro
---
const items = ['Astro', 'React', 'Vue'];
const showBanner = true;
---

<!-- Simple expressions -->
<h1>{pageTitle}</h1>

<!-- Conditionals -->
{showBanner && <div class="banner">Welcome!</div>}

<!-- Iteration -->
<ul>
  {items.map(item => <li>{item}</li>)}
</ul>

<!-- Components -->
<Card title="My Card" />
```

**Key difference from JSX:**
- Use `class` not `className`
- Use `for` not `htmlFor`
- Boolean attributes work naturally: `<input disabled />`
- No need to import `React`

## Props: Passing Data to Components

```astro
---
// Define the shape of accepted props
interface Props {
  title: string;
  description?: string;  // Optional
  tags?: string[];        // Optional with type
}

// Destructure with defaults
const { title, description = "No description", tags = [] } = Astro.props;
---

<article>
  <h2>{title}</h2>
  <p>{description}</p>
  {tags.length > 0 && (
    <div class="tags">
      {tags.map(tag => <span class="tag">{tag}</span>)}
    </div>
  )}
</article>
```

## Slots: Composing Components

Slots allow parent components to inject content into children:

```astro
<!-- BaseLayout.astro -->
---
interface Props {
  title: string;
}
const { title } = Astro.props;
---

<html>
  <head>
    <title>{title}</title>
    <slot name="head" />  <!-- Named slot for extra head content -->
  </head>
  <body>
    <nav>...</nav>
    <main>
      <slot />  <!-- Default slot: main content goes here -->
    </main>
    <footer>...</footer>
  </body>
</html>
```

```astro
<!-- Using the layout -->
---
import BaseLayout from '../layouts/BaseLayout.astro';
---

<BaseLayout title="About Me">
  <link slot="head" rel="stylesheet" href="/about.css" />
  
  <h1>About Me</h1>
  <p>This goes into the default slot.</p>
</BaseLayout>
```

**Conditional slots** — check if a slot was provided:

```astro
---
const hasSidebar = Astro.slots.has('sidebar');
---

<div class={hasSidebar ? 'with-sidebar' : 'full-width'}>
  <main><slot /></main>
  {hasSidebar && (
    <aside><slot name="sidebar" /></aside>
  )}
</div>
```

## When to Use .astro vs .tsx/.jsx

```
.astro files (majority of your site — 90%+)
├── Static content: pages, layouts, cards, headers, footers
├── Server-side data fetching
├── Composition with slots
└── Zero JavaScript shipped

.tsx/.jsx files (islands — only when needed)
├── Interactive components: forms, counters, toggles, search
├── State management (useState, useEffect, signals)
├── Event handlers that modify the UI
└── Requires a client: directive to hydrate
```

The rule of thumb: start with `.astro`. Only create a `.tsx` file when you need client-side interactivity that can't be achieved with a simple `<script>` tag.
