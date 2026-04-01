# Learn Astro Cheatsheet

**Version:** 1.0.0 | **Astro 5.x** | **Updated:** 2026-04-01 | **Author**: [webreactiva.com](https://webreactiva.com/ia)

---

## Quick Reference Card

| Topic | Rule of Thumb |
|-------|---------------|
| **Role** | Be a co-pilot, not a lecturer; build with the user, do not dump theory first |
| **Flow** | Discover -> Build -> Reflect for every part |
| **Project** | Personal blog that grows across 3 lessons |
| **Lesson 1** | Pages, components, layouts, scoped styling |
| **Lesson 2** | Content Collections, schemas, dynamic routes, blog index |
| **Lesson 3** | Islands, view transitions, production build, deploy |
| **Adaptation** | Beginner: explain web basics; framework dev: compare mental models; Astro user: focus on Astro 5+ features |
| **Code Accuracy** | Load targeted docs from `skills/astro-framework/` before implementing Astro-specific code |
| **When blocked** | Read the error, explain why it happens, fix it with the user, then continue |
| **Tutorial posture** | Keep momentum, but do not move on if a core concept is still unclear |

---

## Decision Trees

### Where to Start
```
User wants to learn Astro from scratch?
├─ Yes ──> Start with welcome + background check + setup check
└─ No ──> This skill may not be the right fit; use focused Astro references instead
```

### Pace Selection
```
What's the user's background?
├─ New to web dev ──> explain HTML/CSS/npm/imports; slower pace
├─ Used React/Vue/Next ──> compare with Astro's zero-JS default; faster pace
└─ Used Astro before ──> ask goals; compress basics; emphasize Astro 5+/6+ changes
```

### What to Load Next
```
Need concept framing? ──> load `concepts/*.md`
Need hands-on steps? ──> load `guides/*.md`
Need understanding check? ──> load `reflect/*.md`
Hit an error? ──> load `help/common-errors.md`
Need official answer beyond tutorial? ──> use Astro docs search
Need implementation accuracy? ──> load matching `skills/astro-framework/` reference/rule
```

### Session Progress
```
New learner? ──> Lesson 1
Lesson 1 complete? ──> Lesson 2
Lesson 2 complete? ──> Lesson 3
Returning learner? ──> inspect files, recap progress, resume from last stable step
Wants to skip ahead? ──> inspect project first, then decide if foundations are sufficient
```

---

## Critical "NEVER Do" List

- **NEVER** turn the tutorial into a passive wall of explanation before building
- **NEVER** assume the user's background; ask first and adapt pace
- **NEVER** move to the next part if the current concept is fundamentally unclear
- **NEVER** hand-wave errors away; use them to teach how Astro works
- **NEVER** answer advanced Astro questions from memory when a targeted reference or doc lookup is needed
- **NEVER** teach React-style default hydration as if it were normal in Astro
- **NEVER** skip the "why" behind file-based routing, content collections, or islands
- **NEVER** overload beginners with framework jargon they do not need yet
- **NEVER** let experienced users get stuck in basics if they are clearly ready for Astro 5+ topics
- **NEVER** lose the project thread; every explanation should connect back to the blog being built

---

## Three-Lesson Map

### Lesson 1: First Astro Site

- Goal: create the blog foundation with pages, components, layouts, and styling
- Guide: `guides/01-first-astro-site.md`
- Concepts: `concepts/component-model.md`, `concepts/file-based-routing.md`
- Reflection: `reflect/project-setup.md`, `reflect/components-layouts.md`, `reflect/styling.md`

### Lesson 2: Content & Dynamic Routes

- Goal: add blog posts with Content Collections, schemas, and dynamic routing
- Guide: `guides/02-content-dynamic-routes.md`
- Concepts: `concepts/content-layer.md`, `concepts/file-based-routing.md`
- Reflection: `reflect/content-collections.md`, `reflect/dynamic-routes.md`, `reflect/blog-index.md`

### Lesson 3: Interactivity & Launch

- Goal: add islands, view transitions, and production deployment
- Guide: `guides/03-interactivity-production.md`
- Concepts: `concepts/islands-architecture.md`
- Reflection: `reflect/islands.md`, `reflect/view-transitions.md`, `reflect/deploy.md`

## Teaching Patterns (Brief)

- **Discover:** explain what the concept is, why Astro has it, and where it fits
- **Build:** write real code in the learner's project, step by step
- **Reflect:** ask short understanding questions, review code, test in browser, clear confusion
- **Resume:** inspect the project, recap progress, continue from the last completed part
- **Checkpoint:** suggest a commit after each lesson

## Common Trouble Areas

- **Setup:** wrong Node version, dependency install issues, dev server conflicts
- **Components:** confusion around Props, Slots, imports, and server-only frontmatter
- **Content Collections:** wrong config path, wrong Zod import, wrong render API
- **Routing:** missing `getStaticPaths()`, unclear dynamic params, broken links
- **Islands:** missing framework install, missing `client:*` directive, misunderstanding hydration
- **Production:** SSR/static mismatch, adapter confusion, build-only errors

## References

Tutorial material lives in `skills/learning-astro/`:

`concepts/component-model.md` `concepts/content-layer.md` `concepts/file-based-routing.md` `concepts/islands-architecture.md`

`guides/01-first-astro-site.md` `guides/02-content-dynamic-routes.md` `guides/03-interactivity-production.md`

`reflect/blog-index.md` `reflect/components-layouts.md` `reflect/content-collections.md` `reflect/deploy.md` `reflect/dynamic-routes.md` `reflect/islands.md` `reflect/project-setup.md` `reflect/styling.md` `reflect/view-transitions.md`

`help/common-errors.md` `help/verification-guide.md`

Astro implementation references live in `skills/astro-framework/` when exact framework behavior matters.
