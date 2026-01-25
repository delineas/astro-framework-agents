# Astro Framework Skill

> Comprehensive Astro framework development guide for AI coding agents. Build fast, content-driven websites using islands architecture with expert guidance.

[![Agent Skills](https://img.shields.io/badge/Agent%20Skills-Compatible-blue)](https://agentskills.io)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](../../LICENSE)
[![Version](https://img.shields.io/badge/version-1.0.0-brightgreen)]()

## Overview

This skill provides AI coding agents with deep expertise in Astro framework development. It enables agents to build performant, content-focused websites using Astro's islands architecture, content collections, and hybrid rendering strategies.

### Key Capabilities

- **Islands Architecture** - Selective hydration for optimal performance
- **Content Collections** - Type-safe content management with Zod schemas
- **Hybrid Rendering** - Static, SSR, and on-demand rendering strategies
- **View Transitions** - SPA-like navigation without full page reloads
- **Multi-Framework Support** - React, Vue, Svelte, and Solid integration
- **Image Optimization** - Automatic image processing and optimization
- **SSR Adapters** - Deploy anywhere (Vercel, Netlify, Cloudflare, Node)

## Installation

### Using npx (Recommended)

```bash
# Install just this skill
npx skills add https://github.com/delineas/astro-framework-agents --skill astro-framework

```

### Using Claude Code Plugin

```bash
/plugin marketplace add delineas/astro-framework-agents
/plugin install astro-framework@delineas-astro-framework-agents
```

### Manual Installation

Clone the repository and copy the skill to your skills directory:

```bash
git clone https://github.com/delineas/astro-framework-agents.git
cp -r astro-framework-agents/skills/astro-framework ~/.claude/skills/
```

## Usage

Once installed, the skill activates automatically when working with Astro projects. The agent will:

1. **Detect Context** - Recognize Astro-related tasks from your prompts
2. **Load References** - Pull relevant documentation based on the specific task
3. **Apply Best Practices** - Follow established patterns and avoid common pitfalls
4. **Generate Code** - Produce production-ready Astro components and configurations

### Trigger Phrases

The skill activates on keywords like:
- "Astro component", "islands architecture"
- "content collections", "client directives"
- "view transitions", "Astro SSR"
- "astro.config", "hybrid rendering"

### Example Prompts

```
Create an Astro blog with content collections and tags
```

```
Add a React counter component with client:visible hydration
```

```
Configure SSR with the Vercel adapter
```

```
Implement view transitions for smooth page navigation
```

## Directory Structure

```
astro-framework/
├── SKILL.md              # Main skill instructions (required)
├── README.md             # This documentation file
├── LICENSE               # MIT License
├── references/           # Detailed reference documentation
│   ├── components.md          # Component patterns and Props
│   ├── client-directives.md   # Hydration strategies
│   ├── content-collections.md # Schema definitions and queries
│   ├── routing.md             # File-based routing and endpoints
│   ├── ssr-adapters.md        # SSR configuration
│   ├── view-transitions.md    # Navigation animations
│   ├── actions.md             # Form handling and validation
│   ├── middleware.md          # Request/response middleware
│   ├── styling.md             # CSS scoping and class:list
│   ├── images.md              # Image optimization
│   └── configuration.md       # astro.config.mjs options
└── rules/                # Context-specific guidelines
    ├── astro-components.rule.md
    ├── client-hydration.rule.md
    ├── content-collections.rule.md
    ├── astro-routing.rule.md
    ├── astro-ssr.rule.md
    ├── astro-images.rule.md
    └── astro-typescript.rule.md
```

## How It Works

### Progressive Disclosure

This skill uses progressive disclosure to minimize context usage:

1. **Metadata (~100 tokens)** - Name and description loaded at startup
2. **Instructions (<5000 tokens)** - Full SKILL.md loaded when activated
3. **References (on-demand)** - Specific docs loaded only when needed

### Reference Loading

When you ask about specific topics, the agent loads the relevant reference file:

| Task | Reference Loaded |
|------|------------------|
| "Create a header component" | `references/components.md` |
| "Add hydration to this React component" | `references/client-directives.md` |
| "Set up a blog collection" | `references/content-collections.md` |
| "Configure Vercel deployment" | `references/ssr-adapters.md` |
| "Add page transitions" | `references/view-transitions.md` |

### Rules System

Context-specific rules are applied based on file patterns:

- `**/*.astro` files → Component and hydration rules
- `src/content/**` → Content collection rules
- `astro.config.*` → Configuration rules
- `src/pages/api/**` → Endpoint rules

## Reference Documentation

### Components (`references/components.md`)

Covers Astro component structure, Props interface, slots (default and named), expressions, dynamic attributes, `class:list`, Fragment, and the Astro global object.

### Client Directives (`references/client-directives.md`)

Explains hydration strategies:
- `client:load` - Immediate hydration
- `client:idle` - Hydrate when idle
- `client:visible` - Hydrate on viewport entry
- `client:media` - Conditional media query hydration
- `client:only` - Client-side only rendering

### Content Collections (`references/content-collections.md`)

Details type-safe content management:
- Collection definition with Zod schemas
- `getCollection()` and `getEntry()` queries
- Content and data collections
- Custom loaders and references

### Routing (`references/routing.md`)

Covers Astro's file-based routing:
- Static and dynamic routes
- Rest parameters and pagination
- API endpoints (GET, POST, etc.)
- Redirects and rewrites

### SSR & Adapters (`references/ssr-adapters.md`)

Explains server-side rendering:
- Output modes: `static`, `server`, `hybrid`
- Adapter configuration (Vercel, Netlify, Cloudflare, Node)
- Server islands for partial hydration
- Request/response handling

### View Transitions (`references/view-transitions.md`)

Covers SPA-like navigation:
- `<ClientRouter />` component
- Transition animations and directives
- State persistence across navigations
- Lifecycle events

### Actions (`references/actions.md`)

Details form handling:
- `defineAction()` for server functions
- Input validation with Zod
- Progressive enhancement
- Error handling

### Middleware (`references/middleware.md`)

Explains request processing:
- `onRequest` handler
- `sequence()` for chaining middleware
- `context.locals` for request data
- Authentication patterns

### Styling (`references/styling.md`)

Covers CSS in Astro:
- Scoped styles (default behavior)
- Global styles with `is:global`
- `class:list` directive
- CSS imports and preprocessors

### Images (`references/images.md`)

Details image optimization:
- `<Image />` component
- `<Picture />` for art direction
- Remote image handling
- Build-time optimization

### Configuration (`references/configuration.md`)

Covers `astro.config.mjs`:
- Integrations setup
- Build options
- Vite configuration
- Environment variables

## Rules Reference

### `astro-components.rule.md`
Applies to: `**/*.astro`

Enforces proper component structure: frontmatter organization, Props interface, slot patterns, and conditional rendering.

### `client-hydration.rule.md`
Applies to: Components using `client:*` directives

Guides hydration decisions: when to hydrate, which directive to use, and common mistakes to avoid.

### `content-collections.rule.md`
Applies to: `src/content/**`

Ensures type-safe collections: schema definitions, Zod validation, and query patterns.

### `astro-routing.rule.md`
Applies to: `src/pages/**`

Enforces routing best practices: file naming, dynamic parameters, and endpoint patterns.

### `astro-ssr.rule.md`
Applies to: SSR configurations

Guides server-side rendering: adapter selection, output modes, and deployment considerations.

### `astro-images.rule.md`
Applies to: Image handling in Astro

Enforces image optimization: using `<Image />`, import patterns, and responsive images.

### `astro-typescript.rule.md`
Applies to: TypeScript configuration

Ensures type safety: strict mode, Props interfaces, and content collection types.

## Best Practices

### DO

- Use islands architecture—only hydrate interactive components
- Define `interface Props` for type safety
- Use content collections for structured content
- Optimize images with `<Image />` and `<Picture />`
- Choose the right client directive for each use case
- Configure the appropriate adapter for your deployment target

### DON'T

- Hydrate components that don't need interactivity
- Use `client:only` without specifying the framework
- Import images as string paths
- Skip Zod schemas in content collections
- Access `Astro.request` in prerendered pages
- Use browser APIs in component frontmatter

## Compatibility

This skill is compatible with:

| Agent | Status |
|-------|--------|
| Claude Code | Fully supported |
| Cursor | Fully supported |
| Cline | Fully supported |
| OpenAI Codex | Compatible |
| GitHub Copilot | Compatible |
| Windsurf | Compatible |

## Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch
3. Add/update references or rules
4. Submit a pull request

### Adding a New Reference

1. Create a new `.md` file in `references/`
2. Add the reference to the table in `SKILL.md`
3. Update this README

### Adding a New Rule

1. Create a new `.rule.md` file in `rules/`
2. Include frontmatter with `description` and `globs`
3. Add the rule to the list in `SKILL.md`
4. Update this README

## Changelog

### v1.0.0

- Initial release
- 11 reference documents covering all major Astro features
- 7 context-specific rules
- Full Agent Skills specification compliance

## License

MIT License - see [LICENSE](LICENSE) for details.

## Resources

- [Astro Documentation](https://docs.astro.build)
- [Agent Skills Specification](https://agentskills.io/specification)
- [Skills.sh Directory](https://skills.sh)

---

Built with expertise from the Astro community. Designed for AI coding agents.

---

**[Subscribe to Web Reactiva Newsletter](https://webreactiva.com/newsletter)** — Weekly insights on web development, AI tools, and modern frameworks.

---

Built with love for Astro and the open web and the Malandriner Community

Made by [Dani Primo](https://webreactiva.com) — [@webreactiva-devs](https://github.com/webreactiva-devs)
