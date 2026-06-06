# Session Handoff — 2026-06-06
> Supersedes session-handoff-2026-06-03. Upload at the start of a new Claude Code session to resume with full context.

This session focused on **solving the skipped-routines problem** and making each run produce **richer daily growth**. Big changes below.

---

## Who the user is
- **Name:** Lorenzo Albertinazzi · **Email:** l.albertinazzi.1@gmail.com · **GitHub:** lorenzoalbertinazzi
- **Timezone:** Europe/Rome (CEST = UTC+2). **Style:** wants full autonomous control — solve problems without asking; terse responses.

## What this project is
An Obsidian knowledge vault auto-maintained by 5 scheduled remote Claude agents (cloud) that push commits to GitHub. The local PC pulls every 3 min via Task Scheduler. **NEW:** a local **watchdog** now guarantees each daily run happens even when the cloud scheduler drops it.

## Vault location
`C:\Users\lalbe\My Vault` · remote `https://github.com/lorenzoalbertinazzi/My-Vault` · folders: Finance(18) Psychology(17) Tech & AI(15) Geopolitics(13) World Events(13).

---

## ★ THE SKIPPED-ROUTINES FIX (the core of this session)

### Diagnosis
- The skips were **NOT** Claude skipping complete-looking notes. The bulletproof-commit writes a `.vault-run-log` heartbeat even on zero edits, so a fired-but-lazy run would still commit. Days with **zero commits** (e.g. Jun 4) prove the routine was **never invoked** — a cloud scheduler/execution drop, not a content problem.
- Pattern: 06:00 & 09:00 fire reliably; 12:00 was dead Jun 1–5; 15:00/18:00 often fire hours late. Consistent with **capacity/overload throttling** dropping later-in-day runs (a local headless test hit `529 Overloaded`).
- **`last_fired_at` is unreliable** — it lagged ~30 min behind a real run this session. **The git commit is the source of truth.**

### Fix 1 — Local self-healing watchdog  (this is what guarantees "no skips")
- Script: `C:\Users\lalbe\vault-watchdog.ps1` · Task: **"Vault Routine Watchdog"** (`schtasks`, runs **hourly**, `/IT` = when user logged on).
- Each hour it fetches origin/main and, for any of the 5 slots whose commit is missing **and** is past due+grace (4h), it runs that slot's enrichment **LOCALLY** via `claude -p` (headless, `--dangerously-skip-permissions`) and pushes. Bypasses the flaky cloud entirely. Local push works via Git Credential Manager (no token needed).
- Safeguards: lock file (no overlap), max 3 attempts/slot/day, one heavy catch-up per hourly tick, per-folder commits preserve partial work.
- Prompt source: `C:\Users\lalbe\vault-routines\_common.md` (ASCII; `{{SLOT}}`/`{{ASSIGNMENT}}` substituted by the watchdog). Slot assignments are defined in the `$Slots` array inside the .ps1.
- Logs: `vault-routines\watchdog.log`, per-run `run-<date>-<slot>.log`, state `watchdog-state.json`.
- Run a manual dry-run anytime: `powershell -File C:\Users\lalbe\vault-watchdog.ps1 -DryRun`

### Fix 2 — All 5 cloud trigger prompts rewritten (2026-06-06, ~10:10 UTC)
Same content now lives in the cloud triggers AND `_common.md`. Key upgrades vs old prompts:
1. **Ironclad NEVER-SKIP:** "a note that already looks complete is NEVER a reason to skip — complete notes get the DEEPEST additions"; padding/bumping last_updated = failure.
2. **Per-note minimum:** 400–600 words of genuinely NEW substance (worked examples w/ real numbers, historical cases, opposing views, 2026 developments).
3. **Commit after EACH folder** (not once at end) — fixes the "partial — agents still running" truncation and prevents lost work.
4. **Bounded scope** per slot so runs finish; **final self-check** re-Globs each folder to verify coverage.

---

## The 5 routines (cloud) — Manage at https://claude.ai/code/routines
| Slot (Rome) | ID | Cron UTC | due UTC | Folders |
|---|---|---|---|---|
| 06:00 | `trig_01EESqkmoLNgevjppSKEKmfN` | `0 4 * * *` | 04 | Finance + Psychology |
| 09:00 | `trig_015Bb5vzomrZpfQu2BhXVh8D` | `0 7 * * *` | 07 | Tech & AI (+Finance) |
| 12:00 | `trig_01RPgxdHJiD14LyJ5eE3xoLT` | `0 10 * * *` | 10 | Psychology + Finance (2nd pass) |
| 15:00 | `trig_01NDy4TyP8w12T2HPhrsByMT` | `0 13 * * *` | 13 | ALL folders (cross-connections) |
| 18:00 | `trig_01T8uj7dAeESF9ViTky7RWPr` | `0 16 * * *` | 16 | Geopolitics + World Events |

Weekly logic in every prompt: **Mon–Fri** deep enrichment · **Sat** expansion (deep-enrich + 2 new notes/folder) · **Sun** formatting + cross-linking.
> SECURITY: GitHub PAT is embedded in the cloud commit blocks. Acknowledge it exists; never echo it.

---

## Other fixes this session
- **CLAUDE.md path (issue #2) — PERMANENTLY fixed.** The `My Vault` path correction had only ever been a local uncommitted change (survived via stash/pop) and was never pushed, so origin kept reverting it. Committed + pushed to origin/main this session. Should no longer recur.

## Known open items / watch-list
- The cloud scheduler may still drop runs — that's now backstopped by the watchdog, so it's no longer urgent. Monitor `watchdog.log` to see catch-ups.
- 15:00 (ALL folders) is the heaviest slot and may not finish every note in one cloud run; per-folder commits + watchdog mitigate. Tighten its scope if it consistently truncates.
- Watchdog runs only when the user is logged on (`/IT`). PC is configured 24/7; if it ever logs off, catch-ups pause. Switch to a service account if needed.

## Local automation files
| Path | Purpose |
|---|---|
| `C:\Users\lalbe\vault-watchdog.ps1` | **NEW** hourly self-healing watchdog |
| `C:\Users\lalbe\vault-routines\_common.md` | **NEW** shared enrichment prompt (watchdog + cloud source of truth) |
| `C:\Users\lalbe\vault-routines\watchdog.log` | **NEW** watchdog activity log |
| `C:\Users\lalbe\git-pull-vault.ps1` | git pull every 3 min (stash/pop around pull) |
| `C:\Users\lalbe\My Vault\CLAUDE.md` | vault instructions (grep before WebSearch) |

## Health check
```powershell
git -C "C:\Users\lalbe\My Vault" fetch origin
git -C "C:\Users\lalbe\My Vault" log origin/main --format="%ai %s" -10   # expect ~5 commits/day, one per slot
Get-Content C:\Users\lalbe\vault-routines\watchdog.log -Tail 20          # watchdog decisions/catch-ups
```
*Generated by Claude Code — 2026-06-06*
