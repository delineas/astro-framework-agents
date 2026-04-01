# Reflect: View Transitions

Let's pause and make sure everything is working before we keep building.

## Let's make sure it clicked

1. **What does `<ClientRouter />` do?**
   <details>
   <summary>Answer</summary>

   It intercepts link clicks and, instead of doing a full page navigation (which causes a white flash and reloads all resources), it fetches the new page's HTML in the background and smoothly swaps the content. This makes navigation feel instant, like a single-page app, while keeping the benefits of multi-page architecture.
   </details>

2. **When would you use `transition:name`?**
   <details>
   <summary>Answer</summary>

   To create shared element transitions between pages. When two pages have elements with the same `transition:name` value, Astro animates between them during navigation. For example, a blog post title in the list view can morph into the same title on the detail page, creating a polished, connected feel.
   </details>

3. **What event fires after a view transition completes?**
   <details>
   <summary>Answer</summary>

   `astro:page-load` fires after every navigation (including the initial page load). This is important because regular `<script>` tags only run once with view transitions enabled. If you have setup code (event listeners, DOM manipulation), use `document.addEventListener('astro:page-load', () => { ... })` to ensure it runs on every page.
   </details>

## Quick review

### 1. ClientRouter Setup

Show me `src/layouts/BaseLayout.astro`:

- [ ] Imports `ClientRouter` from `'astro:transitions'`
- [ ] `<ClientRouter />` is placed inside `<head>`

### 2. Transition Animations

- [ ] `transition:animate="fade"` (or other animation) on main content area
- [ ] Content fades/slides during page transitions

### 3. Shared Element Transitions (if implemented)

- [ ] Blog post titles have matching `transition:name` on list and detail pages
- [ ] Titles animate between views during navigation

### 4. Script Handling

- [ ] Any `<script>` tags that need to run on every page use `astro:page-load`
- [ ] Interactive features still work after navigating between pages

## If something's not working

### "ViewTransitions is not exported"

**Cause**: Using the Astro 4 name.

**Fix**: Astro 5+ renamed it:
```astro
<!-- Wrong -->
import { ViewTransitions } from 'astro:transitions';
<!-- Right -->
import { ClientRouter } from 'astro:transitions';
```

### Scripts stop working after navigation

**Cause**: `<script>` tags only execute on the initial page load when view transitions are active.

**Fix**: Wrap setup code in the `astro:page-load` event:
```html
<script>
  document.addEventListener('astro:page-load', () => {
    // This runs on every navigation
  });
</script>
```

### Animations not playing

**Cause**: Missing `transition:animate` directive or browser doesn't support View Transitions API.

**Fix**: Ensure `transition:animate` is on the elements you want to animate. Astro provides a fallback for browsers without native support, but animations may be simpler.

### Flicker during navigation

**Cause**: Layout shift from content differences between pages.

**Fix**: Ensure consistent layout structure across pages (which our shared BaseLayout already provides).

## Ready to move on?

- [ ] Clicking links navigates without a full page reload (no white flash)
- [ ] Content transitions smoothly (fade, slide, or custom animation)
- [ ] Browser back/forward buttons work correctly
- [ ] URL updates correctly in the address bar
- [ ] Interactive islands still work after navigation
- [ ] `astro:page-load` scripts run on every page

## Go further

These are optional explorations if you're curious — skip them and come back later if you prefer:

- Try `transition:animate="slide"` instead of `"fade"` and see the difference
- Create a custom animation using `transition:animate` with CSS
- Add `transition:persist` to an island to keep its state across navigations

## Next Steps

All good? Here's what's next:

1. **View transitions** provide smooth navigation
2. **Next**: Build for production and deploy (Part 3 of Lesson 3)

**Ready to continue?**
