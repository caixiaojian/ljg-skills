# CLAUDE.md

This file provides guidance when working with this repository from either Claude Code or Codex.

## Overview

This is a personal agent skills repository. Each skill is a self-contained directory that can be installed to `~/.codex/skills/` for Codex or `~/.claude/skills/` for Claude Code.

## Repository Structure

```
ljg-skills/
├── ljg-*/              # Each skill is a directory with "ljg-" prefix
│   ├── SKILL.md        # Skill definition with YAML frontmatter
│   ├── references/     # Reference docs for complex skills
│   ├── assets/         # Templates, images, scripts
│   └── scripts/        # Helper scripts (bash, node)
├── README.md
└── .gitignore          # Ignores everything except ljg-*/ and specific files
```

## Skill Format

Each `SKILL.md` follows this structure:

```yaml
---
name: skill-name
description: "What this skill does. Use when user says..."
user_invocable: true|false
version: "x.x.x"
---

# Skill content in markdown...
```

## Skill Inventory

| Skill | Purpose | External Dependencies |
|-------|---------|----------------------|
| `ljg-card` | Content → PNG visuals (long cards, infographs, posters) | Node.js + Playwright |
| `ljg-paper` | Academic paper analysis pipeline | None |
| `ljg-paper-flow` | Paper workflow (paper + card combined) | None |
| `ljg-plain` | Plain language rewriter | None |
| `ljg-skill-map` | Visual overview of installed skills | Bash |
| `ljg-word` | English word deep-dive | None |
| `ljg-writes` | Writing engine for thinking through ideas | None |

## Commands

### Install ljg-card Dependencies

`ljg-card` requires Playwright for screenshot capture:

```bash
cd ljg-card && npm install && npx playwright install chromium
```

### Test ljg-skill-map Scanner

```bash
bash ljg-skill-map/scripts/scan.sh
```

### Install Skills (for users)

```bash
# Copy all skills to Codex
mkdir -p ~/.codex/skills
cp -r skills/ljg-* ~/.codex/skills/
```

## Architecture Notes

### Skill Invocation

- Skills with `user_invocable: true` can be triggered via `/skill-name` or natural language, depending on the host agent
- Trigger phrases are defined in each skill's `description` field
- Workflow skills may invoke other skills as sub-steps

### Content Processing Pipeline

Several skills share a common pattern for content ingestion:
- **URL** → fetch or browse the page content
- **File path** → read the local file
- **Raw text** → Direct use

### ljg-card Architecture

The most complex skill with multiple rendering modes:

1. **HTML Templates**: Stored in `assets/` (long_template.html, infograph_template.html, poster_template.html)
2. **Capture Script**: `assets/capture.js` uses Playwright to screenshot HTML → PNG
3. **Reference Docs**: `references/taste.md` (design guidelines), `references/mode-*.md` (mode-specific instructions)
4. **Output**: PNG files written to `~/Downloads/`

### Shared Conventions

**Org-mode output** (ljg-paper, ljg-plain, ljg-writes):
- Bold: `*text*` (single asterisk, not `**`)
- Filenames: `{timestamp}--{title}__{type}.org`
- Output directory: `~/Documents/notes/`
- Timestamps: `date +%Y%m%dT%H%M%S`

**ASCII Art**:
- Allowed: `+ - | / \ > < v ^ * = ~ . : # [ ] ( ) _ , ; ! ' "`
- Forbidden: Unicode box-drawing characters

## Development Guidelines

- Skills are atomic units—each skill directory is self-contained
- Version numbers are manually maintained in SKILL.md frontmatter
- The `.gitignore` ignores all files by default; explicitly unignore with `!pattern`
- When modifying skill logic, update both the SKILL.md and any referenced files in `references/`

## Testing Changes

After modifying a skill:
1. Copy to `~/.codex/skills/` or `~/.claude/skills/`
2. Reload the host agent if needed
3. Test via natural language trigger or `/skill-name`
