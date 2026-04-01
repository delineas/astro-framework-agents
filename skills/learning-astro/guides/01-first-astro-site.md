# Lesson 1: Your First Astro Site

**Time**: ~45 minutes
**Goal**: Create the foundation of your personal blog with pages, components, layouts, and styling.

By the end of this lesson, you'll have a multi-page site with reusable components, a shared layout, and scoped styles — all shipping zero JavaScript.

---

## Part 1: Create Project & First Pages (~15 min)

### Architecture Overview

Before writing code, load `concepts/file-based-routing.md` and briefly explain how Astro maps files to URLs.

**Key concept**: Every file in `src/pages/` becomes a page on your site. `index.astro` → `/`, `about.astro` → `/about`. No router configuration needed.

### Step 1: Create the project

Guide the user through the CLI:

```bash
npm create astro@latest my-blog
```

Walk through each prompt:
- **Where should we create your project?** → `my-blog` (or their choice)
- **How would you like to start?** → "Empty" (we'll build everything from scratch)
- **Install dependencies?** → Yes
- **Initialize a git repository?** → Yes
- **TypeScript?** → Yes, strict

Then enter the project:

```bash
cd my-blog
```

### Step 2: Explore the file structure

```
my-blog/
├── astro.config.mjs    ← Astro configuration
├── tsconfig.json       ← TypeScript config
├── package.json
├── src/
│   └── pages/
│       └── index.astro ← Home page (/)
└── public/             ← Static assets (images, fonts, favicon)
```

Explain each directory's purpose:
- `src/pages/` — Every file here becomes a URL on your site
- `src/` — All your source code lives here (components, layouts, styles)
- `public/` — Files served as-is (not processed by Astro)
- `astro.config.mjs` — Project configuration

### Step 3: Start the dev server

```bash
npm run dev
```

Open `http://localhost:4321/` in the browser. They should see the default welcome page.

### Step 4: Edit the home page

Open `src/pages/index.astro` and replace its content:

```astro
---
const pageTitle = "My Blog";
const name = "<!-- user's name -->";
---

<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width" />
    <title>{pageTitle}</title>
  </head>
  <body>
    <nav>
      <a href="/">Home</a>
      <a href="/about">About</a>
    </nav>
    <h1>{pageTitle}</h1>
    <p>Welcome to {name}'s blog. This is where I'll share what I'm learning.</p>
  </body>
</html>
```

**Teaching moment**: Point out the frontmatter (`---` fences). This is server-side JavaScript — it runs at build time, not in the browser. The `{pageTitle}` syntax injects variables into the HTML.

### Step 5: Create the About page

Create `src/pages/about.astro`:

```astro
---
const pageTitle = "About Me";
---

<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width" />
    <title>{pageTitle}</title>
  </head>
  <body>
    <nav>
      <a href="/">Home</a>
      <a href="/about">About</a>
    </nav>
    <h1>{pageTitle}</h1>
    <p>I'm learning Astro and building this blog to document my journey.</p>
  </body>
</html>
```

### Step 6: Verify

- Visit `http://localhost:4321/` — home page works
- Visit `http://localhost:4321/about` — about page works
- Click navigation links — they switch between pages

**Reflect**: Load `reflect/project-setup.md`

---

## Part 2: Components, Props, Slots & Layouts (~15 min)

### Architecture Overview

Load `concepts/component-model.md` and explain the three-section anatomy of an Astro component.

**Key concept**: Components let you reuse HTML. Layouts are components that wrap entire pages with shared structure (head, nav, footer).

**Problem to solve**: The navigation and HTML boilerplate is duplicated in both pages. Let's fix that.

### Step 1: Create Navigation component

Create `src/components/Navigation.astro`:

```astro
---
// No props needed yet — this component is self-contained
---

<nav>
  <a href="/">Home</a>
  <a href="/about">About</a>
  <a href="/blog">Blog</a>
</nav>
```

### Step 2: Create a Card component (introduces Props)

Create `src/components/Card.astro`:

```astro
---
interface Props {
  title: string;
  body: string;
  href?: string;
}

const { title, body, href } = Astro.props;
---

<article class="card">
  <h3>
    {href ? <a href={href}>{title}</a> : title}
  </h3>
  <p>{body}</p>
</article>
```

**Teaching moment**: Explain `interface Props` — it defines what data the component accepts. `href?` means it's optional. `Astro.props` is how you access the values passed by the parent.

### Step 3: Create BaseLayout (introduces Slots)

Create `src/layouts/BaseLayout.astro`:

```astro
---
import Navigation from '../components/Navigation.astro';

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
  </head>
  <body>
    <Navigation />
    <main>
      <slot />
    </main>
    <footer>
      <p>&copy; {new Date().getFullYear()} My Blog</p>
    </footer>
  </body>
</html>
```

**Teaching moment**: `<slot />` is where child content goes. When a page uses this layout, everything the page writes goes into that slot. The navigation and footer are shared automatically.

### Step 4: Refactor pages to use the layout

Update `src/pages/index.astro`:

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
import Card from '../components/Card.astro';
---

<BaseLayout title="My Blog">
  <h1>My Blog</h1>
  <p>Welcome! Here's what I'm learning.</p>

  <Card
    title="What is Astro?"
    body="Astro is a web framework for building fast, content-driven websites."
    href="https://astro.build"
  />
  <Card
    title="Why Astro?"
    body="Zero JavaScript by default. Your site is fast because it ships less code."
  />
</BaseLayout>
```

Update `src/pages/about.astro`:

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
---

<BaseLayout title="About Me">
  <h1>About Me</h1>
  <p>I'm learning Astro and building this blog to document my journey.</p>
</BaseLayout>
```

**Teaching moment**: Look how much cleaner this is! No duplicated HTML, no duplicated navigation. The layout handles all the boilerplate.

### Step 5: Verify

- Both pages still work and look the same
- Navigation appears on both pages (from the layout)
- Footer appears on both pages
- Card component renders with different props

**Reflect**: Load `reflect/components-layouts.md`

---

## Part 3: Styling (~15 min)

### Step 1: Scoped CSS

Add styles to `Card.astro`:

```astro
<!-- Add after the template -->
<style>
  .card {
    padding: 1.5rem;
    border: 1px solid #e2e8f0;
    border-radius: 0.5rem;
    margin-bottom: 1rem;
    transition: box-shadow 0.2s;
  }

  .card:hover {
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  }

  h3 {
    margin: 0 0 0.5rem;
    font-size: 1.25rem;
  }

  h3 a {
    color: #4f46e5;
    text-decoration: none;
  }

  p {
    margin: 0;
    color: #64748b;
  }
</style>
```

**Teaching moment**: These styles are **scoped** — they ONLY affect this component. The `h3` style won't change any other `h3` on the site. Astro achieves this by adding unique class hashes to elements. Inspect in DevTools to see it!

### Step 2: Global styles

Create `src/styles/global.css`:

```css
/* Reset and base styles */
*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

:root {
  --color-text: #1e293b;
  --color-text-light: #64748b;
  --color-primary: #4f46e5;
  --color-bg: #ffffff;
  --color-bg-alt: #f8fafc;
  --font-body: system-ui, -apple-system, sans-serif;
  --font-mono: 'Courier New', monospace;
  --max-width: 48rem;
}

html {
  font-family: var(--font-body);
  color: var(--color-text);
  background: var(--color-bg);
}

body {
  max-width: var(--max-width);
  margin: 0 auto;
  padding: 1rem 1.5rem;
  line-height: 1.6;
}

a {
  color: var(--color-primary);
}
```

Import it in `BaseLayout.astro` frontmatter:

```astro
---
import Navigation from '../components/Navigation.astro';
import '../styles/global.css';

interface Props {
  title: string;
}
const { title } = Astro.props;
---
```

**Teaching moment**: Importing a CSS file in the frontmatter applies it globally. CSS variables (custom properties) defined in `:root` are accessible everywhere.

### Step 3: Style the Navigation

Add styles to `Navigation.astro`:

```astro
<style>
  nav {
    display: flex;
    gap: 1.5rem;
    padding: 1rem 0;
    border-bottom: 1px solid #e2e8f0;
    margin-bottom: 2rem;
  }

  a {
    color: var(--color-text);
    text-decoration: none;
    font-weight: 500;
  }

  a:hover {
    color: var(--color-primary);
  }
</style>
```

### Step 4: Introduce class:list

Show the `class:list` directive for conditional classes. Add to a component:

```astro
---
interface Props {
  title: string;
  body: string;
  href?: string;
  featured?: boolean;
}

const { title, body, href, featured = false } = Astro.props;
---

<article class:list={['card', { featured }]}>
  <!-- ... -->
</article>

<style>
  .card { /* base styles */ }
  .featured {
    border-color: var(--color-primary);
    background: var(--color-bg-alt);
  }
</style>
```

**Teaching moment**: `class:list` accepts an array. Strings are always included. Objects include the key as a class only when the value is truthy. So `{ featured }` adds the class `featured` only when the prop is `true`.

### Step 5: Verify

- Styles look polished and consistent
- Card hover effects work
- Navigation is styled
- Inspect element: see Astro's scoped class hashes
- Confirm: Card styles don't affect other components

**Reflect**: Load `reflect/styling.md`

---

## Lesson 1 Complete!

**What you built:**
- A multi-page Astro site with file-based routing
- Reusable components with Props (Card) and Slots (BaseLayout)
- A shared layout with Navigation and Footer
- Scoped and global CSS with CSS variables

**What you learned:**
- Astro component anatomy: frontmatter → template → styles
- File-based routing: files in `src/pages/` become URLs
- Props and Slots for component composition
- Scoped CSS keeps styles isolated by default

**Git commit:**
```bash
git add .
git commit -m "Lesson 1: First Astro site with pages, components, layouts, and styling"
```

Ready for Lesson 2? We'll add blog posts using Content Collections — Astro's type-safe content management system.
