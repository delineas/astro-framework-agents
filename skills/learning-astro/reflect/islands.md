# Reflect: Islands — Interactive Components

Let's pause and make sure everything is working before we keep building.

## Let's make sure it clicked

1. **What happens when you use a Preact component WITHOUT a `client:` directive?**
   <details>
   <summary>Answer</summary>

   The component is rendered to static HTML on the server. The HTML appears in the page, but no JavaScript is sent to the browser. The component looks correct but has zero interactivity — clicking buttons, typing in inputs, etc., won't do anything.
   </details>

2. **What is the difference between `client:load` and `client:idle`?**
   <details>
   <summary>Answer</summary>

   `client:load` hydrates the component immediately when the page loads — the JavaScript is loaded as a high-priority resource. `client:idle` waits until the browser is idle (via `requestIdleCallback`) before loading the JavaScript. Use `client:load` for immediately needed interactivity and `client:idle` for lower-priority features.
   </details>

3. **Why is Astro's approach called "islands architecture"?**
   <details>
   <summary>Answer</summary>

   The page is like an ocean of static HTML (no JavaScript) with small "islands" of interactivity where needed. Most of the page — headers, content, footers — is static. Only the components that truly need client-side JavaScript (like buttons, forms, search) get hydrated as islands.
   </details>

4. **When should you NOT use a client directive?**
   <details>
   <summary>Answer</summary>

   When the component has no interactivity — which should be the vast majority of your components (~90%+). Headers, footers, cards, blog content, navigation links — these are all static and should remain as `.astro` components or framework components without client directives. Only add a directive when you need event handlers, state changes, or other client-side behavior.
   </details>

## Quick review

### 1. Preact Integration

- [ ] `@astrojs/preact` is in `package.json` dependencies
- [ ] `astro.config.mjs` includes the Preact integration

### 2. LikeButton Component

Show me `src/components/LikeButton.tsx`:

- [ ] Uses `useState` from `preact/hooks`
- [ ] Has a click handler that updates state
- [ ] Displays the current like count
- [ ] Exports a default function component

### 3. LikeButton Usage

- [ ] Used in BlogPost layout or blog post pages
- [ ] Has `client:load` directive
- [ ] Button is interactive (clicking increments count)

### 4. SearchPosts Component (if implemented)

- [ ] Accepts posts data as props
- [ ] Filters posts based on text input
- [ ] Uses `client:idle` directive
- [ ] Search input works and filters results

### 5. Zero-JS Verification

Open the Network tab in DevTools:
- [ ] Pages without islands load zero or minimal JavaScript
- [ ] Pages with islands load small, targeted JS bundles
- [ ] Removing `client:load` from LikeButton makes it non-interactive (confirms the directive is doing its job)

## If something's not working

### "Cannot find package 'preact'"

**Cause**: Preact integration not installed.

**Fix**: `npx astro add preact` (installs both the package and configures the integration).

### Component renders but buttons don't work

**Cause**: Missing `client:` directive.

**Fix**: Add `client:load` (or `client:idle` / `client:visible`) to the component tag.

### "Cannot find module 'preact/hooks'"

**Cause**: Preact package not installed correctly.

**Fix**: `npm install preact @astrojs/preact`

### Hydration mismatch warning

**Cause**: Component renders differently on server vs client.

**Fix**: Ensure the initial render is deterministic (no random values, no `window` checks during render). If the component can't render on the server, use `client:only="preact"`.

## Ready to move on?

- [ ] LikeButton is visible on blog post pages
- [ ] Clicking the LikeButton increments the count
- [ ] SearchPosts filters posts as you type (if implemented)
- [ ] Network tab confirms small JS bundles for islands only
- [ ] Without client directive, component is static (no interactivity)
- [ ] Rest of the page (header, content, footer) has no associated JS

## Go further

These are optional explorations if you're curious — skip them and come back later if you prefer:

- Try using `client:visible` on a component below the fold and watch the Network tab
- Create a small component with `client:only="preact"` and see how it differs (no SSR)
- Experiment: what happens if you pass a function as a prop to an island? (Spoiler: it doesn't work — props must be serializable)

## Next Steps

All good? Here's what's next:

1. **Islands** are working with selective hydration
2. **Next**: Add view transitions for smooth page navigation (Part 2 of Lesson 3)

**Ready to continue?**
