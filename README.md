# Deja Vu

> Stop debugging the same bug twice.

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) slash command that captures post-mortems after debugging sessions, tags them for search, and surfaces them before you waste two hours on a problem you already solved.

Inspired by [EveryInc's Compound Engineering](https://github.com/EveryInc/compound-engineering-plugin) plugin by Kieran Klaassen — specifically the `/compound-docs` skill that captures solved problems as structured documentation. Deja Vu takes the core insight (each fix should teach the system) and strips away the parts that don't survive contact with real usage: YAML validation gates, 13-enum category schemas, and per-problem directory hierarchies. What's left is one file, freeform tags, and grep.

## How it works

**Capture:** After finishing significant work, run `/dejavu`. It asks what worked, what hurt, and what you'd do differently. Writes a tagged entry to `LEARNINGS.md`.

**Retrieve:** Before debugging, grep your learnings for the error or symptom. The tags make this fast — `grep -i "tags:.*env-var" LEARNINGS.md` finds every time you've been burned by environment variables.

**Promote:** When the same tag appears in 3+ entries, Deja Vu suggests promoting it to a permanent rule in your `CLAUDE.md`. Recurring mistakes become guardrails automatically.

## Install

```bash
# Copy the slash command
cp dejavu.md ~/.claude/commands/

# Create your learnings file
cp LEARNINGS-template.md ~/.claude/LEARNINGS.md
```

That's it. Run `/dejavu` after your next debugging session.

## Usage

```
/dejavu                        # Full reflection (5 questions)
/dejavu "redis connection bug" # Specific topic
/dejavu --quick                # 3 questions, fast capture
```

## What you get

Each entry looks like this:

```markdown
## 2026-02-04 - Redis Connection Pool Exhaustion
**Tags:** redis, connection-pool, silent-failure, timeout

**Context:** App started timing out under load. No errors in logs.

**Worked Well:**
- Thread dump immediately showed 200 blocked connections waiting on pool

**Pain Points:**
- Default pool size (10) was never increased from starter template
- No monitoring on pool utilization — failed silently for weeks

**Changes Made:**
- Increased pool to 50, added pool utilization to /health endpoint

**Future Improvements:**
- [ ] Alert when pool utilization > 80%
- [ ] Add connection pool size to deploy checklist
```

Next time you see a timeout with no errors, `grep -i "tags:.*silent-failure"` finds this instantly.

## How it differs from compound-docs

[Compound Engineering](https://every.to/guides/compound-engineering) is a comprehensive system — plan/work/review/compound workflow, multi-agent code review, YAML-validated problem schemas, category directory trees. It's well-designed for teams building large applications.

Deja Vu is for practitioners who want the compounding insight without the infrastructure:

| | Compound Engineering | Deja Vu |
|---|---|---|
| **Structure** | YAML frontmatter, category directories, schema validation | Freeform tags, one flat file, grep |
| **Search** | Category-based retrieval | `grep -i "tags:.*keyword"` |
| **Scope** | Full workflow (plan/work/review/compound) | Capture + retrieve only |
| **Pattern detection** | Manual cross-referencing | Auto-suggests promotion at 3+ tag occurrences |
| **Install** | Plugin with npm package | Copy one file |
| **Files created** | One per problem, organized in directories | One file, append-only |

The tradeoff is clear: compound-docs scales better for large teams and complex projects. Deja Vu is simpler and more likely to actually get used.

## The behavioral change that matters

The command itself is table stakes. The real value is the habit:

**Before you start debugging, check if you've solved this before.**

Add this to your `CLAUDE.md` or agent instructions:

```
Before starting any debugging session, grep LEARNINGS.md for the
error message, symptom, or domain. Past post-mortems often contain
the exact fix.
```

This one line makes 600+ lines of accumulated wisdom actually useful.

## License

MIT
