# Repo Clawbot — Feedback Protocol

This file defines how the human communicates decisions to Repo Clawbot.

Feedback can be sent through:

- GitHub PR comments
- Telegram messages

The bot should interpret both structured commands and free-text feedback.

---

# Primary Commands

APPROVE

Meaning:
The PR is good and should be merged.

Bot behavior:
- reset effort level to baseline
- clear open_pr state
- resume normal schedule

---

CHANGES

Meaning:
The direction is correct but the PR needs adjustment.

Bot behavior:
- keep current effort level
- incorporate feedback
- retry in the next scheduled run

---

NOPE

Meaning:
The PR was not useful.

Bot behavior:
- increase effort by +40%
- try a different improvement next run

---

NOW

Meaning:
Run immediately instead of waiting for the next scheduled run.

Bot behavior:
- execute one immediate run
- follow normal PR rules

---

PAUSE

Meaning:
Stop creating new PRs.

Bot behavior:
- set paused = true
- continue logging and responding to commands

---

RESUME

Meaning:
Resume normal behavior.

Bot behavior:
- set paused = false
- continue daily runs

---

# Optional Commands

BUDGET <n>

Example:

BUDGET 10

Meaning:
Set daily LLM budget limit.

---

EFFORT +

Meaning:
Increase reasoning depth next run.

---

EFFORT -

Meaning:
Reduce reasoning depth next run.

---

# Free-Text Feedback

The bot should attempt to interpret natural language feedback.

Examples:

"this change is useful but the wording isn't great"

Interpretation:
CHANGES

---

"this doesn't help right now"

Interpretation:
NOPE

---

"perfect"

Interpretation:
APPROVE

---

# Open PR Rule

If a PR is awaiting feedback:

The bot must **not create a new PR**.

Instead:

- send one reminder per day
- include PR link
- request feedback

Example reminder:

⏳ Pending PR review  
<PR link>

Reply with: APPROVE / CHANGES / NOPE

---

# Safety Rule

If feedback is unclear:

The bot should ask **one short clarification question** rather than guessing.
