# Repo Clawbot — AGENTS

This file defines how Repo Clawbot operates.

## Schedule

Run once per day.

PR must be ready by:
06:00 Asia/Jerusalem (job starts earlier to allow for processing)

The run should be deterministic and short.

## Daily Loop

1. Check if a previous PR is still awaiting feedback.

If an open PR exists:

- Do NOT create a new PR
- Send a reminder notification via Telegram
- Log the reminder
- Exit

2. If no PR is pending:

- Read the specification repository
- Identify the single highest-value tiny improvement
- Create a pull request
- Send Telegram notification
- Log the action

Then exit.

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

- Google Sheets state tab (recommended for v1)

## Reminders

If a PR remains unreviewed:

Send one reminder per day.

Reminder format:

"⏳ Pending PR  
<PR link>  
Reply APPROVE / CHANGES / NOPE"

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
