# Reflect: Project Setup & First Pages

Let's pause and make sure everything is working before we keep building.

## Let's make sure it clicked

A few questions to check understanding — think about these before peeking at the answers:

1. **What does the `src/pages/` directory do in Astro?**
   <details>
   <summary>Answer</summary>

   Every file in `src/pages/` automatically becomes a page on your site. The file path maps directly to the URL: `index.astro` → `/`, `about.astro` → `/about`. This is called file-based routing — no router configuration needed.
   </details>

2. **Why does `about.astro` become `/about` and not `/about.astro`?**
   <details>
   <summary>Answer</summary>

   Astro strips the file extension during the build process and generates an `about/index.html` file. This creates clean URLs without file extensions, which is the standard convention for websites.
   </details>

3. **What runs in the frontmatter fence (`---`)?**
   <details>
   <summary>Answer</summary>

   Server-side JavaScript that runs at build time (or request time in SSR mode). This is where you import components, fetch data, and define variables. It does NOT run in the browser — that's why you can't use `window` or `document` here.
   </details>

4. **What is the `public/` directory for?**
   <details>
   <summary>Answer</summary>

   Files in `public/` are served as-is, without any processing by Astro. This is where you put static assets like favicons, robots.txt, or images that don't need optimization. A file at `public/logo.png` is accessible at `/logo.png`.
   </details>

## Quick review

### 1. Project Structure

Run this command and share the output:

```bash
ls src/pages/
```

**Expected**:
```
about.astro  index.astro
```

### 2. Dev Server

```bash
npm run dev
```

**Expected**: Terminal shows `Local: http://localhost:4321/` with no errors.

### 3. Pages

- [ ] `http://localhost:4321/` loads the home page with your name
- [ ] `http://localhost:4321/about` loads the about page
- [ ] Both pages have navigation links
- [ ] Clicking links navigates between pages

### 4. Frontmatter

- [ ] `index.astro` has a frontmatter fence with `const pageTitle = "..."` (or similar)
- [ ] Template uses `{pageTitle}` to display the variable

## If something's not working

### "Cannot find module 'astro'"

**Cause**: Dependencies not installed or wrong directory.

**Fix**:
1. Make sure you're in the project: `cd my-blog`
2. Install dependencies: `npm install`
3. Retry: `npm run dev`

### Port 4321 already in use

**Cause**: Previous dev server still running.

**Fix**: `lsof -i :4321` then `kill <PID>`, or use `npm run dev -- --port 3000`

### Page shows raw Astro syntax

**Cause**: Viewing the `.astro` file directly instead of through the dev server.

**Fix**: Open `http://localhost:4321/` in the browser, not the file path.

## Ready to move on?

- [ ] Dev server starts without errors
- [ ] Home page loads and shows content
- [ ] About page loads and shows content
- [ ] Navigation links work between both pages
- [ ] Frontmatter variables render correctly in the template
- [ ] Hot reload works (edit a file, see changes in browser)

## Go further

These are optional explorations if you're curious — skip them and come back later if you prefer:

- Try creating a third page (e.g., `contact.astro`) and see it appear at `/contact`
- Experiment with adding more frontmatter variables and using them in the template
- Check out the [Astro docs on project structure](https://docs.astro.build/en/basics/project-structure/)

## Next Steps

All good? Here's what's next:

1. **Project structure** is set up and working
2. **Next**: Create reusable components and a shared layout (Part 2 of Lesson 1)

You have duplicated HTML in both pages (the `<html>`, `<head>`, navigation). In Part 2, we'll fix this with components and layouts.

**Ready to continue?** Let me know when all checks pass.
