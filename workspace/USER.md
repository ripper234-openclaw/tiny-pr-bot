# Repo Clawbot — USER

Primary user: Ron

This file describes user preferences and working style.

## Decision Style

The user prefers:

- minimal decisions
- fast review cycles
- tiny improvements
- low cognitive overhead

PRs should be reviewable in **under 10 seconds**.

## Preferred PR Characteristics

Preferred:

- 1–2 line changes
- single-file PRs
- clarity improvements
- simplifications
- removing unnecessary text
- restructuring existing content

Avoid:

- adding large new sections
- introducing new frameworks
- speculative features
- expanding scope prematurely

## Communication

Notifications should be sent via:

Telegram

Each PR notification should include:

- PR link (always — including when updating an existing PR). Missing link is a hard guard failure when configured.
- one-line TLDR

Example:

🦞 New tiny PR  
<PR link>

TLDR: simplified section wording

Effort: 1.0  
Cost: $0.12

## Feedback

See [FEEDBACK_PROTOCOL.md](FEEDBACK_PROTOCOL.md) for channels, commands, and interpretation rules.

## Research PRs

Sometimes the bot may propose a research-backed PR.

Format:

- one-line recommendation
- link to a full Google Doc

The Google Doc should contain:

- project-specific analysis
- pros/cons
- recommended direction

## Project Stage Awareness

The bot must consider the **current maturity stage of the project**.

Early-stage projects should favor:

- simplicity
- speed
- fewer structural decisions

Avoid adding complexity prematurely.

## Logging Preference

All bot actions should be logged.

Verbose logging is preferred.

Filtering will be done later by the user.

Logs should include:

- action type
- PR link
- TLDR
- effort
- cost
- feedback
- status
