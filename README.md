# Astro Framework Agents

> Agent Skills for building with Astro. Compatible with Claude Code, Cursor, Cline, OpenAI Codex, and other agents supporting the [Agent Skills specification](https://agentskills.io).

[![Agent Skills](https://img.shields.io/badge/Agent%20Skills-Compatible-blue)](https://agentskills.io)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

---

**[Subscribe to Web Reactiva Newsletter](https://webreactiva.com/newsletter)** — Weekly insights on web development, AI tools, and modern frameworks.

---

## Why Astro?

Astro made me fall in love with web development again.

After years of complexity, heavy frameworks, and JavaScript fatigue, Astro reminded me that the web can be simple, fast, and beautiful. It embraces HTML as a first-class citizen. It ships zero JavaScript by default. It lets you use React, Vue, Svelte, or Solid—or none of them—without judgment.

Astro is plural: it welcomes every developer, every framework, every approach. Astro is universal: it works for blogs, docs, e-commerce, and everything in between. Astro is honest: it doesn't pretend the web is something it's not.

This repository is my way of sharing that love with the AI tools we use every day.

— [Dani Primo](https://webreactiva.com)

---

## Available Skills

| Skill | Description | Install |
|-------|-------------|---------|
| [astro-framework](skills/astro-framework/) | Comprehensive Astro framework development guide for islands architecture, content collections, SSR, and view transitions | `npx skills add delineas/astro-framework-agents/astro-framework` |

## Quick Start

### Install a Single Skill

```bash
npx skills add https://github.com/delineas/astro-framework-agents --skill astro-framework
```

### Manual Installation

Clone the repository and copy the desired skill folder to your agent's skills directory:

```bash
git clone https://github.com/delineas/astro-framework-agents.git
cp -r astro-framework-agents/skills/astro-framework ~/.claude/skills/
```

## Repository Structure

```
astro-framework-agents/
├── README.md                 # This file (repository index)
├── LICENSE                   # MIT License
├── .gitignore
└── skills/                   # All skills live here
    ├── astro-framework/      # Astro framework skill
    │   ├── SKILL.md          # Main skill instructions
    │   ├── README.md         # Skill documentation
    │   ├── AGENTS.md         # Compiled guidelines
    │   ├── references/       # Detailed reference docs
    │   └── rules/            # Context-specific rules
    └── [future-skill]/       # Add more skills here
        └── SKILL.md
```

## Creating a New Skill

1. Create a new directory under `skills/`:

```bash
mkdir -p skills/my-new-skill
```

2. Create the required `SKILL.md` with frontmatter:

```yaml
---
name: my-new-skill
description: Description of what this skill does and when to use it.
license: MIT
metadata:
  author: your-name
  version: "1.0.0"
---

# My New Skill

Instructions for the agent...
```

3. (Optional) Add supporting files:
   - `README.md` - Human-readable documentation
   - `AGENTS.md` - Compiled guidelines for agents
   - `references/` - Detailed reference documentation
   - `rules/` - Context-specific rules with glob patterns

4. Update this README to include your new skill in the table.

## Skill Format Specification

Each skill follows the [Agent Skills Specification](https://agentskills.io/specification):

### Required Files

- `SKILL.md` - Main skill file with YAML frontmatter and instructions

### Required Frontmatter

```yaml
---
name: skill-name          # Must match directory name
description: "..."        # What it does and when to use it
---
```

### Optional Frontmatter

```yaml
---
license: MIT
metadata:
  author: your-name
  version: "1.0.0"
  category: framework
  tags: tag1, tag2
compatibility: Requires Node.js 18+
allowed-tools: Bash(npm:*) Read
---
```

### Optional Directories

| Directory | Purpose |
|-----------|---------|
| `references/` | Detailed documentation loaded on-demand |
| `rules/` | Context-specific rules with glob patterns |
| `scripts/` | Executable scripts the agent can run |
| `assets/` | Templates, images, data files |

## Compatibility

These skills are compatible with:

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
2. Create a new skill in `skills/your-skill-name/`
3. Follow the [Agent Skills Specification](https://agentskills.io/specification)
4. Add your skill to the table in this README
5. Submit a pull request

### Guidelines

- Keep `SKILL.md` under 500 lines (use `references/` for detailed docs)
- Use progressive disclosure (metadata → instructions → references)
- Include clear examples in your instructions
- Add `rules/` files with glob patterns for context-specific guidance

## License

MIT License - see [LICENSE](LICENSE) for details.

## Resources

- [Agent Skills Specification](https://agentskills.io/specification)
- [Skills.sh Directory](https://skills.sh)
- [Anthropic Skills Repository](https://github.com/anthropics/skills)
- [Vercel Agent Skills](https://github.com/vercel-labs/agent-skills)

---

Built with love for Astro and the open web and the Malandriner Community

Made by [Dani Primo](https://webreactiva.com) — [@webreactiva-devs](https://github.com/webreactiva-devs)
