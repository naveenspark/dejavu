# Deja Vu

> Persistent memory for Claude Code mistakes.

Claude Code forgets everything between sessions. You fix a mistake, close the session, start a new one, and Claude makes the same mistake again. Wrong approach. Ignored the docs. Confidently wrong about something it was confidently wrong about last time.

I started tracking these in a `LEARNINGS.md` file and it grew to hundreds of lines of painful lessons. I hoped Claude would pay attention to it, but it never did. Then I found [EveryInc's Compound Engineering](https://github.com/EveryInc/compound-engineering-plugin) plugin, which solves this exact problem. It's pretty neat, but it felt heavy: YAML schemas, category directories, npm packages. I wanted something simpler.

Deja Vu is two things:

1. **`/dejavu`** is a slash command you run after Claude screws up (and it will, over and over again). It captures what happened, tags it, and writes it to `LEARNINGS.md`.
2. **"facepalm"** is a word you type when Claude is doing something dumb again. Claude stops what it's doing, searches its file of shame for the relevant lesson, and course-corrects on the spot.

That's it. One file, freeform tags, and grep.

## Install

```bash
# 1. Copy the slash command (capture mode)
cp dejavu.md ~/.claude/commands/

# 2. Create your learnings file
cp LEARNINGS-template.md ~/.claude/LEARNINGS.md
```

Then add this to your `CLAUDE.md` so Claude knows what "facepalm" means:

```markdown
## Rules

When the user says "facepalm":
STOP. Something you're doing has been done wrong before.
Search ~/.claude/LEARNINGS.md for error messages, symptoms, or keywords
from whatever just went wrong. Show matching entries. If a past lesson
applies, follow it immediately.

Before starting any task, check LEARNINGS.md for relevant past mistakes.
```

Two behaviors, three files.

## Usage

**After Claude screws up:**

```
/dejavu                        # Full reflection (5 questions)
/dejavu "auth migration"       # Specific topic
/dejavu --quick                # 3 questions, fast capture
```

**When Claude is doing it again:**

```
you: "facepalm"
```

Claude stops, searches the learnings file for whatever it's screwing up, and shows you the relevant past entry. You don't need to remember the tags. Claude figures out the search terms from context.

## What you get

Each `/dejavu` entry looks like this:

```markdown
## 2026-02-04 - Claude Ignored Project Docs Again
**Tags:** docs-ignored, false-confidence, verification

**Context:** Asked Claude to integrate with an API. It confidently wrote
the integration without reading the existing docs, got the auth flow
completely wrong, and wasted 45 minutes.

**Worked Well:**
- Caught it when the first request returned 401

**Pain Points:**
- Claude never checked the README that had the exact auth pattern
- Said "Fixed!" twice before actually fixing it

**Changes Made:**
- Added rule to CLAUDE.md: read target docs before writing integration code

**Future Improvements:**
- [ ] Add a pre-task checklist for integration work
```

Next time Claude starts writing integration code without reading the docs, "facepalm" finds this instantly.

## Pattern promotion

When the same tag shows up in 3+ entries, `/dejavu` suggests promoting it to a permanent rule in your `CLAUDE.md`. Recurring mistakes become guardrails automatically.

Claude has ignored your docs three times? That's not a learning anymore. That's a rule.

## How it differs from Compound Engineering

[Compound Engineering](https://every.to/guides/compound-engineering) is a comprehensive system. YAML-validated schemas, category directories, multi-agent review. Well-designed for teams building big applications.

Deja Vu is for everyday vibe coders who want the compounding insight without the infrastructure:

| | Compound Engineering | Deja Vu |
|---|---|---|
| **Structure** | YAML frontmatter, category directories | Freeform tags, one flat file |
| **Search** | Category-based retrieval | `grep` + "facepalm" trigger |
| **Pattern detection** | Manual cross-referencing | Auto-suggests promotion at 3+ occurrences |
| **Install** | Plugin with npm package | Copy one file, paste one block |
| **Files created** | One per problem, organized in directories | One file, append-only |

## License

MIT
