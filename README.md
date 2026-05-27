# git-monitor

Tiny LuaJIT wrapper around GitHub CLI for brute-force GitHub Actions polling.

Canonical skill/tool contents live in [`gh_monitor/`](gh_monitor/).

## Why

Sometimes `gh` watch flows are not good enough operationally. The failure mode is a long blind sleep, then a stale refresh after the job finished ages ago.

`git-monitor` fixes that by polling fresh `gh` state on a short interval and returning as soon as the run or PR checks change state.

## Requirements

- `gh`
- `jq`
- `luajit`

## Layout

Bundled in [`gh_monitor/`](gh_monitor/):

| File | Role |
|------|------|
| [`gh_monitor/SKILL.md`](gh_monitor/SKILL.md) | Skill instructions for agents |
| [`gh_monitor/git_monitor.lua`](gh_monitor/git_monitor.lua) | Executable LuaJIT polling tool |

## Install


**Codex / Claude skill install:**

```bash
mkdir -p ~/.codex/skills
git clone https://github.com/simbo1905/git-monitor.git ~/.codex/skills/gh_monitor
```

```sh
chmod +x gh_monitor/git_monitor.lua
export PATH="$PWD/gh_monitor:$PATH"
```

Then use it as a git-style subcommand:

```sh
git monitor run <run-id> --repo owner/repo
```

Details and operator guidance: [`gh_monitor/SKILL.md`](gh_monitor/SKILL.md).

## Usage

### Watch one run

```sh
git monitor run 26059219391 \
  --repo livemorecapital/livemore-pricing-engine \
  --interval 15 \
  --soft-deadline 480 \
  --timeout 1200
```

### Watch PR checks

```sh
git monitor pr-checks 389 \
  --repo livemorecapital/livemore-pricing-engine \
  --interval 15 \
  --soft-deadline 480 \
  --timeout 1200 \
  --fail-fast
```

## Semantics

- polls on a short fixed interval
- exits early on success
- exits early on failure
- supports a soft deadline for “something looks wrong; re-check now”
- supports a hard timeout so it never waits forever

## Exit codes

- `0` success
- `2` monitored run/checks failed
- `3` soft deadline exceeded while still pending
- `4` hard timeout exceeded while still pending
- `5` tool / `gh` error

## Validation

Validated against:

- completed CI run
- completed PR checks
- live GitHub Actions workflow that failed fast
- live GitHub Actions scheduled-task deploy that ran ~6 minutes and completed successfully
