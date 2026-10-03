# Project Memory Curator

> Structured, auditable project memory for AI-assisted development — using nothing but Markdown files.

**Project Memory Curator** is an agent skill that teaches AI coding assistants how to maintain a long-term, human-readable knowledge base for any project. It lives entirely in `.agent-knowledge/` — six categorized Markdown files that any file-capable agent can read and update. No databases, no APIs, no platform lock-in.

## Why

AI coding agents forget everything between sessions. Existing solutions store memory in proprietary clouds, vector databases, or hidden caches that you can't easily review, edit, or version-control. Project Memory Curator keeps memory where it belongs: **in the repo**, as plain Markdown, fully transparent and git-trackable.

## What It Does

- **Before work** — retrieves only the most relevant memory entries for the task at hand (not the whole knowledge base)
- **After work** — evaluates whether the completed task produced durable, reusable knowledge worth keeping
- **Preview-then-save** — shows a structured preview before writing anything; user confirms, edits, or skips
- **Auto-save toggle** — a single magic comment (`<!-- project-memory-curator:auto-save=on -->`) in the README controls persistent auto-save, without any platform hooks

## Knowledge Categories

| File | Purpose | Anti-pattern |
|---|---|---|
| `project.md` | Tech stack, directory structure, module boundaries | Temporary implementation details |
| `preferences.md` | Confirmed long-term preferences, constraints, collaboration habits | Inferred preferences |
| `runbook.md` | Verified commands: start, test, build, deploy, recover | Untested commands, credentials |
| `decisions.md` | Architecture decisions with context, rationale, and impact | Unsupported assumptions |
| `todo-memory.md` | Candidates that need further verification — **never treated as fact** | Actionable facts |

> **Clear Separation**: Engineering troubleshooting, debugging, and bugfix lessons belong in `intelligent-experience-extractor`, **never** in project memory.

## Hard Rules

- **No engineering troubleshooting lessons.** Bugs, fix root causes, and outage notes belong in the companion experience extractor. Keep memory clean.
- **No secrets.** Never stores keys, tokens, passwords, credentials, private URLs, emails, or customer data.
- **No guesses.** Unconfirmed observations go to `todo-memory.md` with `置信度：待确认` (confidence: unconfirmed); they are never applied as facts.
- **No chat logs.** Only conclusions with clear future reuse value are kept — raw conversations and full logs are skipped.
- **Respect existing norms.** `AGENTS.md`, `CLAUDE.md`, `.cursor/rules`, and similar project-level instructions take precedence. Conflicts are surfaced to the user immediately.
- **Minimal edits.** Before writing, the target file is re-read; only the minimal diff is applied; after writing, the result is re-read and verified.

## Structure

```
project-memory-curator/
├── SKILL.md                          # The skill definition (Chinese-first, works with any language)
├── agents/
│   └── openai.yaml                   # Agent interface metadata
├── references/
│   └── memory-entry-format.md        # Entry format specification
├── assets/
│   └── agent-knowledge/
│       ├── README.md                 # Knowledge base index and auto-save toggle
│       ├── project.md                # Tech stack & architecture template
│       ├── preferences.md            # User preferences template
│       ├── runbook.md                # Commands & operations template
│       ├── decisions.md              # Architecture decisions template
│       └── todo-memory.md            # Unconfirmed candidates template
├── README.md
├── LICENSE
└── .gitignore
```

## Platform-Agnostic by Design

The skill only uses capabilities every competent agent already has: directory listing, text search, file read, and file write. No hooks, no custom tools, no network calls, no database — just Markdown on disk. This means it works across Claude Code, Codex, Cursor, and any other AI coding tool with filesystem access.

## How to Use

### As a skill (Claude Code / Codex / compatible platforms)

Drop the entire `project-memory-curator/` directory into your skills directory (e.g., `.claude/skills/` or `.codex/skills/`). The agent will load it automatically when triggered by relevant engineering scenarios.

### As a standalone knowledge base template

Copy `assets/agent-knowledge/` into your project root as `.agent-knowledge/` and start filling in the templates. Any AI agent capable of reading Markdown can consume and maintain it.

### Triggering

The skill activates when an engineering task involves: code changes, debugging, testing, builds, deployment, dependencies, CI/CD, environment configuration, architecture, refactoring, databases, performance, security, reliability, project conventions, or user preferences.

## Language

The skill definition is written in Chinese and defaults to the user's current language for new knowledge bases. All templates and structures are language-agnostic — entries can be written in any language.

## License

MIT
