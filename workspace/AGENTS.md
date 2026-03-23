# Repo Clawbot — AGENTS

This file defines how Repo Clawbot operates.

## Operating Loop

The bot maintains a continuous loop with two layers:

### Polling layer (lightweight, no LLM)

Every 5 minutes, a simple script checks:

1. Is there an open PR awaiting review? → Do nothing.
2. Has a PR just been approved/rejected? → Trigger the generation layer.
3. Is the bot currently generating a PR? → Do nothing.

This check is a constant-cost API call (GitHub PR status), not an LLM call.

### Generation layer (LLM-powered)

Triggered only when no PR is pending and none is being prepared:

1. Read the specification repository
2. Identify the single highest-value tiny improvement
3. Create a pull request
4. Send Telegram notification
5. Log the action

If no genuinely useful improvement exists, the bot may enter a **idle state** instead of submitting filler. It should log "no useful PR found" and retry on the next polling cycle.

Allow up to 60 minutes for generation to ensure quality over speed.

### Invariant

At all times, exactly one of the following must be true:

- A PR is submitted and awaiting review
- A PR is being generated right now
- The bot is in idle state (no useful PR to propose)

### Reminders

If a PR remains unreviewed for 24 hours, send one reminder per day.

## PR Constraints

Default constraints:

- Prefer **1–2 line changes**
- Prefer **one file**
- Never exceed **3 files**

PRs must minimize decision overhead.

If a change would require multiple decisions, do not submit it.

## Effort Model

The bot uses an effort parameter that controls the depth of reasoning.

Baseline:
effort = 1.0

Feedback adjusts effort:

APPROVE → reset effort to baseline  
CHANGES → keep effort same  
NOPE → increase effort by +40%

Effort increase is capped by the daily cost limit.

## Budget

Daily cost cap:

$10

If the cap is reached:

- Stop generating PRs
- Send alert
- Resume next day

## State Tracking

The bot maintains minimal state:

- open_pr_url
- open_pr_timestamp
- effort_level
- paused

State can be stored in:

- Google Sheets state tab

## Weekly Stats Pulse

Once per week generate a report containing:

- total PRs (week)
- total PRs (all time)
- approval rate %
- average PR cost
- total weekly cost
- median time to approval

Include a histogram:

PR approval rate (%) vs PR generation cost

Cost buckets example:

$0–0.10  
$0.10–0.25  
$0.25–0.50  
$0.50–1  
$1–2  
$2–5  
$5–10

Send the report via Telegram and log it.

## Pause Mode

If the user sends:

PAUSE

The bot must stop generating PRs.

RESUME reactivates normal behavior.

## Immediate Run

Command:

NOW

Triggers a single immediate run without waiting for the next scheduled cycle.
