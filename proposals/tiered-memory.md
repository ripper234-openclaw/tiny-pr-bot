# Proposal: Tiered Markdown Policy + Deterministic GitHub Write Guard (v1)

## Problem

LLM agents forget rules they themselves codified — sometimes within the same session. This isn't an alignment failure; it's a structural one. Rules stored in long-term files (MEMORY.md, SOUL.md) are only useful if the agent reads them at the right moment. In practice:

- Long files get skimmed or skipped under context pressure
- Rules learned in one session don't reliably transfer to the next
- There's no mechanism to ensure critical rules are checked before every action
- "Lessons learned" sections grow indefinitely and lose signal

**The core insight:** Markdown improves cognition and recall, but it cannot enforce behavior. High-risk actions (GitHub writes, external notifications) must not rely on LLM compliance alone. Enforcement must happen outside the LLM, at side-effect boundaries.

## Two-Track Solution

### Track A: Tiered Markdown Memory

Model memory after human cognitive architecture: reflexes, working memory, and long-term memory.

#### Tier 1: HOT.md (always loaded, always checked)

**File:** `HOT.md`
**Size limit:** ~20 lines max. Ruthlessly curated.
**When read:** Before every reply in the main session. Non-negotiable.
**What goes here:** Only rules that have been violated AND caused real harm.

HOT.md is a checklist, not prose. Binary pass/fail items.

#### Tier 2: Session Context (loaded at session start)

**Files:** `SOUL.md`, `USER.md`, `memory/YYYY-MM-DD.md` (today + yesterday)
**When read:** Once at session start.
**What goes here:** Identity, preferences, recent context, active projects.

#### Tier 3: Long-Term Memory (loaded on demand)

**Files:** `MEMORY.md`, `memory/*.md` (older days), `LESSONS.md`
**When read:** During heartbeats, memory maintenance, or when explicitly relevant.
**What goes here:** Historical context, lessons learned, accumulated knowledge.

This tier is a reference library. Rules here are "known" but not "enforced." Do NOT rely on this tier for safety.

#### Promotion/Demotion

```
Incident occurs
    → Lesson written to Tier 3 (MEMORY.md / LESSONS.md)
    → If lesson violated again → Promoted to Tier 1 (HOT.md)
    → If HOT.md rule followed consistently for 30 days → Demoted back to Tier 3
    → If HOT.md exceeds 20 lines → Oldest stable rule demoted to make room
```

Nightly cron handles promotion/demotion during its review cycle.

### Track B: Deterministic GitHub Write Guard (v1)

All GitHub write actions are wrapped by a non-LLM Python guard (`scripts/github_write_guard.py`). The guard:

- Accepts structured JSON input, returns structured PASS/FAIL
- Runs preflight checks before any mutation
- On FAIL: allows up to 2 retries
- After 2 failed attempts: blocks the action
- Terminal failure triggers Telegram alert and one best-effort meta PR
- If verification is unavailable: **fail closed**

#### Preflight Checks (v1)

1. **ACTION_ALLOWLIST** — only known actions proceed
2. **REQUEST_ID_REQUIRED** — every write has a unique request ID
3. **TARGET_MUST_BE_OPEN** — PR/issue state verified immediately before mutation
4. **EDIT_ONLY_BOT_PRS** — can only edit PRs authored by the bot (dynamic identity via `GET /user`)
5. **CREATE_PR_MIN_SCHEMA** — base, head, title, non-empty diff, no duplicate open PR

#### Critical Edge Cases

- **Race conditions:** Always fetch latest state BEFORE action
- **Idempotency:** Request ID tracked; duplicate open PR check for creates
- **Fail closed on uncertainty:** API timeout = FAIL, missing data = FAIL
- **No alternate write paths:** ALL GitHub writes go through the guard

## Policy Hygiene

- HOT.md and other policy-critical files must not contain hidden bidi/control Unicode characters
- Policy lint (`scripts/policy_lint.py`) fails if such characters are present (trojan source prevention)

## Architecture

```
┌──────────────────────────────────────────────┐
│  Every Reply                                 │
│  ┌────────────────────────┐                  │
│  │ Tier 1: HOT.md         │ ← ~20 lines     │
│  │ (pre-send checklist)   │   always loaded  │
│  └────────────────────────┘                  │
├──────────────────────────────────────────────┤
│  Session Start                               │
│  ┌────────────────────────┐                  │
│  │ Tier 2: Session Context│ ← SOUL, USER,   │
│  │ (identity + recent)    │   recent memory  │
│  └────────────────────────┘                  │
├──────────────────────────────────────────────┤
│  On Demand / Maintenance                     │
│  ┌────────────────────────┐                  │
│  │ Tier 3: Long-Term      │ ← MEMORY.md,    │
│  │ (reference library)    │   old daily logs │
│  └────────────────────────┘                  │
├──────────────────────────────────────────────┤
│  Side-Effect Boundary (deterministic)        │
│  ┌────────────────────────┐                  │
│  │ GitHub Write Guard     │ ← Python script  │
│  │ (can't be forgotten)   │   preflight+exec │
│  └────────────────────────┘                  │
└──────────────────────────────────────────────┘
```

## Limitations

1. **LLM compliance is probabilistic.** Even HOT.md will sometimes be skipped.
2. **Promotion/demotion requires judgment.** The nightly cron deciding what's "stable" is itself an LLM call.
3. **Guard only covers GitHub writes.** Other side effects (Telegram messages, file operations) remain unguarded.
4. **Doesn't solve the root cause.** LLMs lack persistent working memory. This is a structural workaround.

## Open Questions

1. Should HOT.md be injected into the system prompt (guaranteed loaded) or read as a file (could be skipped)?
2. Is 20 lines the right cap?
3. Should guard failures hard-block or soft-warn?
4. Should this be proposed as an OpenClaw platform feature?
