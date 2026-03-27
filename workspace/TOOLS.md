# Repo Clawbot — TOOLS

This file defines the external tools Repo Clawbot is allowed to use.

Only these tools should be used.

## GitHub Write Guard

- All GitHub write actions must pass through the guarded Python wrapper (`scripts/github_write_guard.py`)
- Never bypass guard logic
- If GitHub verification is unavailable, fail closed
- Existing restrictions below stay in force unless explicitly changed

## GitHub

Capabilities:

- Read repository contents
- Read issues
- Read pull requests
- Create pull requests
- Read PR comments
- Post PR comments (optional)

Restrictions:

- Never push directly to the default branch
- Never merge PRs
- Never modify CI, infrastructure, or secrets

Scope:

Specification files only (README, docs, spec files).

## Telegram

Purpose:

User notifications and feedback.

Capabilities:

- Send messages
- Receive user commands

Typical messages:

PR notification

Example:

🦞 New tiny PR  
<PR link>

TLDR: clarified decision section

Effort: 1.0  
Cost: $0.11


Reminder message:

⏳ Pending PR review  
<PR link>

Reply APPROVE / CHANGES / NOPE


Weekly pulse message:

📊 Weekly Stats Pulse  
PRs: <n>  
Approval rate: <pct>%  
Cost: $<total>

Histogram: approval rate vs PR generation cost

## Google Sheets

Purpose:

Logging and lightweight state storage.

Capabilities:

- Append log rows
- Read existing rows
- Read/write small key-value state

Sheet structure:

### Log Sheet

Columns:

Timestamp  
ActionType  
Repo  
PR_URL  
PR_Title  
PR_TLDR  
Effort  
CostUSD  
Feedback  
Status  
Notes

### State Sheet

Key-value pairs:

open_pr_url  
open_pr_timestamp  
effort  
paused

## Google Docs

Purpose:

Long-form research documents when needed.

Used only for research-backed PRs.

PR contains:

- one-line summary
- link to the Google Doc

The Google Doc contains:

- analysis
- pros and cons
- recommendation

## LLM Provider

Used for:

- reasoning about improvements
- summarization
- research analysis

Constraints:

- respect daily budget cap
- prioritize minimal compute usage
- increase effort only after repeated NOPE feedback

## Security Rules

Never access:

- credentials
- secrets
- infrastructure configuration
- deployment systems

The bot operates **only on specification content**.
