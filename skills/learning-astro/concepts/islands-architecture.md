# Islands Architecture

## The Core Idea

Islands architecture means your page is an ocean of **static HTML** with small islands of **interactivity** where needed.

```
┌─────────────────────────────────────────────────────────────┐
│                     Static HTML (ocean)                      │
│  ┌──────────┐                                               │
│  │  Header   │  ← Pure HTML, zero JS                        │
│  └──────────┘                                               │
│                                                             │
│  ┌──────────────────────────────────────┐                   │
│  │  Blog Post Content                   │  ← Markdown → HTML│
│  │  Pure text, images, formatting       │     zero JS       │
│  └──────────────────────────────────────┘                   │
│                                                             │
│  ┌─────────────────┐   ┌──────────────┐                     │
│  │ 🏝️ Like Button  │   │ 🏝️ Search    │  ← Interactive     │
│  │  (client:load)   │   │ (client:idle) │    islands with JS │
│  └─────────────────┘   └──────────────┘                     │
│                                                             │
│  ┌──────────┐                                               │
│  │  Footer   │  ← Pure HTML, zero JS                        │
│  └──────────┘                                               │
└─────────────────────────────────────────────────────────────┘
```

## Why Zero JavaScript by Default?

Consider a typical blog page:
- **Header**: Static navigation links. No JS needed.
- **Blog content**: Rendered from Markdown. No JS needed.
- **Sidebar**: Static list of categories. No JS needed.
- **Like button**: Needs to track clicks. **JS needed** (island).
- **Footer**: Static links. No JS needed.

In a traditional SPA framework (React, Next.js, SvelteKit), **everything** ships JavaScript — even the static parts. The browser downloads a JS bundle, parses it, and re-renders the entire page.

In Astro, **only the Like button** ships JavaScript. Everything else is pure HTML. The result:

```
Traditional SPA:  200KB+ JavaScript bundle
Astro blog page:  3KB JavaScript (just the island)
```

This directly impacts:
- **Page load speed**: Less JS = faster load
- **Core Web Vitals**: Better LCP, FID, CLS scores
- **Mobile performance**: Especially on low-powered devices
- **Battery life**: Less CPU work = less battery drain

## The Decision Tree

When adding a component, ask yourself:

```
Does this component need client-side interactivity?
│
├── NO → Use .astro component (zero JS)
│        Examples: headers, footers, cards, blog content, image galleries
│
└── YES → Does the user interact with it immediately?
          │
          ├── YES → client:load
          │         Examples: navigation menu, login form, hero carousel
          │
          └── NO → Is it below the fold (not initially visible)?
                   │
                   ├── YES → client:visible
                   │         Examples: comments section, newsletter signup
                   │
                   └── NO → client:idle
                            Examples: sidebar widget, search, feedback form
```

### Special Cases

- **`client:media`**: Hydrate only when a media query matches. Perfect for a chart that's only useful on desktop: `client:media="(min-width: 768px)"`
- **`client:only="preact"`**: Skip server rendering entirely. Use when the component can't render on the server (e.g., uses `window` during initialization).

## How Hydration Works

```
Server / Build time:
  1. Astro runs the component's render function
  2. Generates static HTML
  3. Marks islands with hydration metadata

Browser:
  1. Receives pure HTML (renders instantly — no JS needed)
  2. Loads JS only for marked islands
  3. Islands "hydrate" — attach event listeners, become interactive
  4. Static content remains untouched
```

The key insight: the page is **immediately visible** as HTML. JavaScript loads afterward only for the parts that need it. This is why Astro sites feel so fast — the user sees content before any JS executes.

## Framework Agnostic

One of Astro's unique features: different islands can use different frameworks on the same page:

```astro
---
import ReactCounter from './ReactCounter';
import SvelteToggle from './SvelteToggle.svelte';
import VueWidget from './VueWidget.vue';
---

<ReactCounter client:load />
<SvelteToggle client:visible />
<VueWidget client:idle />
```

Each framework's JS is loaded independently, only for its island. There's no shared runtime overhead.

## Comparison

| Approach | JavaScript Shipped | When to Use |
|----------|-------------------|-------------|
| **SPA** (React, Vue) | Everything | Highly interactive apps (dashboards, editors) |
| **MPA** (plain HTML) | Nothing | Pure static sites with zero interactivity |
| **Islands** (Astro) | Only interactive parts | Content sites with some interactivity |

## Anti-Patterns to Avoid

1. **`client:load` on everything** — Defeats the purpose. If every component ships JS, just use a SPA.
2. **Hydrating navigation for a toggle** — A mobile menu toggle is a `<script>` with 5 lines of vanilla JS. Don't hydrate an entire React component for it.
3. **Large framework bundles via `client:load`** — If a component's bundle is >50KB, consider: can the static part be an Astro component, with only the interactive bit as an island?
4. **`client:visible` on above-the-fold** — It's already visible, so IntersectionObserver fires immediately. Use `client:load` directly.
