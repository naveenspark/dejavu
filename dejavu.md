# /dejavu

Capture learnings after completing significant work. Tag them. Retrieve them before you debug the same thing twice.

## Philosophy

Each unit of engineering work should make subsequent units easier. This command forces a reflection step that compounds knowledge over time. The tags make it searchable. The pattern detection makes it self-reinforcing.

## Usage

```
/dejavu [topic]
```

**Examples:**
- `/dejavu` - General reflection on recent work
- `/dejavu "auth migration"` - Specific topic learnings
- `/dejavu --quick` - Abbreviated version (3 questions)

## Instructions for Claude

When this command is invoked:

### 1. Gather Context

Review the recent conversation/work to understand what was accomplished:
- What task was completed?
- What files were modified?
- What problems were solved?

### 2. Ask Reflection Questions

Present these questions (use AskUserQuestion for structured input):

**Full version:**
1. **What worked well?** - Patterns, approaches, or tools that made this easier
2. **What was harder than expected?** - Friction points, unexpected complexity
3. **What would you do differently?** - Hindsight insights
4. **What should be documented?** - Missing docs, unclear code, tribal knowledge
5. **What should be automated?** - Repetitive steps that could be scripted

**Quick version (--quick):**
1. What worked well?
2. What was painful?
3. What should change?

### 3. Generate Tags and Capture Learnings

Based on responses, **generate 3-6 freeform tags** for this entry. Tags should be:
- Lowercase, hyphenated (e.g., `env-vars`, `false-positive`, `config-drift`)
- A mix of: symptom (`silent-failure`), domain (`postgres`, `auth`), pattern (`baseline-check`, `doc-drift`)
- Grep-friendly — think "what would I search for next time I hit this?"

Then update the appropriate files:

**For project-specific learnings:**
- Add to `docs/LEARNINGS.md` (create if doesn't exist)
- Update `CLAUDE.md` if it affects how to work in the codebase

**For cross-project learnings:**
- Add to `~/.claude/LEARNINGS.md` (create if doesn't exist)

**For tooling/automation ideas:**
- Add to project's `TODO.md` or `IDEAS.md`

### 4. Format for Learnings File

```markdown
## [Date] - [Topic]
**Tags:** [tag1], [tag2], [tag3], ...

**Context:** [Brief description of the work]

**Worked Well:**
- [bullet points]

**Pain Points:**
- [bullet points]

**Changes Made:**
- [Any immediate updates to docs/code]

**Future Improvements:**
- [ ] [Actionable items]
```

### 5. Suggest Immediate Actions

If any learnings can be acted on immediately:
- Offer to update CLAUDE.md
- Offer to create a script
- Offer to add a health check
- Offer to document a process

### 6. Pattern Detection

After writing the entry, check for recurring tags:

```bash
grep -i "^**Tags:**" ~/.claude/LEARNINGS.md | tr ',' '\n' | sed 's/.*\*\*Tags:\*\* //' | sed 's/^ *//' | sort | uniq -c | sort -rn | head -10
```

If any tag appears in **3+ entries**, suggest promoting it to a rule in CLAUDE.md:

> "The tag `[tag]` appears in [N] learnings entries: [entry titles]. This looks like a recurring pattern. Want me to add a rule to CLAUDE.md to catch this earlier?"

Only suggest — don't auto-add. The user decides what becomes a rule.
