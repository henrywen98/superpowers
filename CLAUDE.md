# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Superpowers is a Claude Code / OpenCode plugin that provides 15 reusable workflow skills for AI coding assistants. Skills are Markdown files with YAML frontmatter — there is no traditional build system, package manager, or compiled code. The plugin enforces structured development workflows (TDD, systematic debugging, brainstorming-before-coding, etc.) through automatic skill triggering.

## Architecture

### Plugin Entry Points

- **Claude Code**: `hooks/hooks.json` defines a `SessionStart` hook → runs `hooks/session-start.sh` → injects `using-superpowers` skill content into session context
- **OpenCode**: `.opencode/plugins/superpowers.js` exports a plugin that uses system prompt transform to inject bootstrap context and symlinks skills to `~/.config/opencode/skills/superpowers/`

### Skill System

Each skill lives in `skills/<skill-name>/SKILL.md` with YAML frontmatter (`name`, `description`) and Markdown body containing process instructions, checklists, anti-patterns, and examples.

**Skill resolution priority** (first match wins): project skills → personal skills (`~/.claude/skills/`) → bundled superpowers skills. This shadowing logic is in `lib/skills-core.js`.

**Auto-triggering**: The `using-superpowers` meta-skill is loaded on every session start. It checks each user message for applicable skills and invokes them via the `Skill` tool before responding.

### Key Files

| File | Purpose |
|------|---------|
| `lib/skills-core.js` | Skill discovery, frontmatter parsing, shadowing, update checking |
| `hooks/session-start.sh` | Claude Code bootstrap — reads and injects using-superpowers content |
| `.opencode/plugins/superpowers.js` | OpenCode bootstrap — system prompt transform |
| `hooks/hooks.json` | Hook configuration for SessionStart event |
| `.claude-plugin/plugin.json` | Plugin manifest (name, version, keywords) |
| `commands/*.md` | Slash command shortcuts (`/brainstorm`, `/write-plan`, `/execute-plan`) |
| `agents/code-reviewer.md` | Code review subagent prompt template |

### Skill Categories

- **Design & Planning**: brainstorming → writing-plans → validating-plans → executing-plans / subagent-driven-development
- **Testing**: test-driven-development, verification-before-completion
- **Debugging**: systematic-debugging
- **Engineering**: using-git-worktrees, requesting-code-review, receiving-code-review, finishing-a-development-branch
- **Coordination**: dispatching-parallel-agents, using-superpowers (meta), writing-skills

## Testing

Integration tests live in `tests/`. They run real Claude Code sessions in headless mode and parse `.jsonl` transcripts to verify skill invocation and task completion. Tests take 10-30 minutes.

```bash
cd tests/claude-code
./test-subagent-driven-development-integration.sh
```

There is no unit test framework — the project is primarily Markdown content with minimal JS utilities.

## Conventions

- **Line endings**: LF enforced for all text files (`.gitattributes`)
- **Shell scripts**: POSIX-compatible with `set -euo pipefail`
- **JavaScript**: ES modules (`import`/`export`), async/await
- **Skill naming**: kebab-case directories, each containing `SKILL.md`
- **Commit messages**: Bilingual (English + Chinese), conventional commit style with emoji prefixes
- **Skill frontmatter format**:
  ```yaml
  ---
  name: skill-name
  description: Use when [condition] - [what it does]
  ---
  ```

## Cross-Platform

Windows support uses `hooks/run-hook.cmd` (polyglot bash/cmd wrapper). Symlink creation requires admin or Developer Mode on Windows.
