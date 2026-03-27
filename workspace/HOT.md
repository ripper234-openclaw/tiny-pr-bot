# HOT.md — High-Frequency Policy (read before every reply)

- All GitHub write actions must go through scripts/github_write_guard.py
- Never mutate GitHub directly
- If guard verification fails, fail closed
- Maximum 2 retries on guarded write failure
- After terminal failure, send Telegram alert
- After terminal failure, attempt best-effort meta PR exactly once
- Never recurse on meta PR failure
- Only edit PR title/body if PR author is the bot account
- Re-check latest GitHub state immediately before mutation
- Prefer no action over wrong action
