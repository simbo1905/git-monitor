---
name: gh-actions-poll
description: Brute-force GitHub Actions monitoring that polls fresh `gh` state until a run or PR checks finish, fail, or cross operator deadlines.
---

# GH Actions Poll

Use this when `gh ... --watch` is not trustworthy enough and you need a blocking poll loop that notices state changes quickly instead of sleeping through them.

## Tool

`gh_monitor/git-monitor`

Examples:

```sh
gh_monitor/git-monitor run 26059219391 \
  --repo livemorecapital/livemore-pricing-engine \
  --interval 15 \
  --soft-deadline 480 \
  --timeout 1200
```

```sh
gh_monitor/git-monitor pr-checks 389 \
  --repo livemorecapital/livemore-pricing-engine \
  --interval 15 \
  --soft-deadline 480 \
  --timeout 1200 \
  --fail-fast
```

Exit codes:

- `0` success
- `2` monitored thing failed
- `3` soft deadline exceeded while still pending
- `4` hard timeout exceeded while still pending
- `5` tool or `gh` error
