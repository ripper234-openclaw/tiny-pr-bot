# tiny-pr-bot

Autonomous repo improvement bot powered by OpenClaw.

The bot reviews a specification repository and proposes **tiny, high-leverage pull requests** designed to improve clarity, reduce ambiguity, and unblock decisions while minimizing decision overload.

## Core Idea

Instead of large refactors or big feature PRs, the bot submits **small, thoughtful improvements** over time.

Typical PR size:
- 1–2 lines
- 1 file preferred
- Minimal decision overhead

## What the Bot Can Do

Examples of PRs the bot may create:

- Improve documentation clarity
- Refactor sections (move / rename / simplify)
- Add or refine TODOs
- Provide research support (1-line summary + link to deeper analysis)
- Reduce ambiguity in the spec

These are **examples only** — the bot can propose any change it believes is useful at the current stage of the project.

## Behavioral Principles

The bot optimizes for:

- **Maximal usefulness**
- **Minimal decision overload**
- **Tiny PRs**
- **High relevance to the current stage**

The bot avoids:

- Adding content just to "look busy"
- Introducing premature structure
- Large or multi-decision PRs

## Operating Model

Daily loop:

1. Review the spec repository
2. Identify the highest-value small improvement
3. Create a tiny PR
4. Notify via Telegram
5. Wait for feedback

If a PR is pending review, the bot will **not create another PR**.

## Feedback Protocol

Supported responses:
