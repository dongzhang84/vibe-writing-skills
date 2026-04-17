# Vibe Writing Skills

**English | [中文](./README.md)**

A Claude Code skill that engineers your personal writing workflow. AI handles fact-checking, verification, proofreading, and citation management; you only own topic selection, thesis, judgment, and voice.

Inspired by the article *Vibe Writing Era Has Arrived*: **the soul of the article is mine, the team is AI.**

## What it is

A reusable, append-only writing skill loaded via Claude Code's skill mechanism. Core design:

- **SKILL.md**: orchestration layer. Defines the pipeline (gather material → fact-check → expand → self-audit → output)
- **persona.md**: author identity (who you are, voice, hard rules)
- **style.md**: language, paragraph length, number formatting, regional scope
- **structure.md**: section numbering, case requirements, tables, conclusion standards
- **error-log.md**: append-only mistake log. Every time you give feedback "don't do X again", append one entry. Next run, it's a hard constraint
- **output-format.md**: the three required sections at the end of every article (other articles / references / appendix of original draft)
- **references/images.md**: priority list for image sources
- **ARCHITECTURE.md**: engineering rationale and explicit non-goals

## Why split into multiple files

The old monolithic `SKILL.md` mixed flow, persona, style, error rules, and output format in one place. Once it grew past 500 lines, nobody could maintain it. After splitting:

- Append a mistake rule → only touch `error-log.md`
- Tweak author identity → only touch `persona.md`
- Each file has a single responsibility, diffs are clean, files evolve independently

All files get loaded into context during skill execution, so token cost is the same as a single-file skill. Multiple files exist for **human maintainability**, not for token efficiency.

## Installation

### Drop it into your Claude Code project

```bash
# From your project root
cd your-project
mkdir -p .claude/skills/write-article
cp -R /path/to/vibe-writing-skills/* .claude/skills/write-article/
```

**The path must be `.claude/skills/write-article/`** because `SKILL.md` uses this absolute path to load config files at runtime.

Then in Claude Code, invoke with `/write-article <draft-file-path or topic>`.

### Customize

After installation, change at least these three files to make the skill yours:

1. **`persona.md`**: replace with your identity, voice, and hard rules
2. **`style.md`**: your language preferences, paragraph length, number formatting, regional scope
3. **`structure.md`**: your section numbering convention, case requirements

Don't pre-edit `error-log.md`. Every time Claude writes something you dislike, give short feedback and append one entry to this file. Future runs avoid the mistake automatically.

Edit `output-format.md` if you want different trailing sections (the default is "other articles / references / original-draft appendix").

## Workflow

```
You write a draft (.md file)
   ↓
/write-article <draft-path>
   ↓
Claude automatically:
  1. Loads config (persona / style / structure / error-log / output-format)
  2. Gathers material (reads your draft)
  3. Analyzes & plans (core thesis, section breakdown)
  4. Fact-checks (WebSearch for numbers, dates, names, events)
  5. Expands (fills the outline, following every config rule)
  6. Self-audits (checks against error-log entry by entry)
  7. Outputs (.md file + references + original-draft appendix)
   ↓
You review → give feedback → append to error-log → next run is tighter
```

## Design principles

See [ARCHITECTURE.md](./ARCHITECTURE.md). Short version:

- **Single responsibility**: each file owns one concern
- **Explicit imports**: SKILL.md explicitly `Read`s every config file. No implicit loading
- **Independent evolution**: the error log changes frequently; persona barely changes. Physically separate them
- **Append-first**: error log is append-only, minimizing write friction

## What this skill does NOT do

See the "Non-goals" section in ARCHITECTURE.md. Explicitly out of scope:

- Multi-agent orchestration (style-checker agent, reviewer agent, etc.)
- Pre-writing Document Spec / Definition of Done checklists
- PDF ingestion, AI-taste detection APIs
- JSON memory databases

Rationale: for a solo blog, the maintenance cost of these features doesn't pay back.

## License

MIT
