# ml-ops — ML Notebook Lifecycle Suite

@import SOUL.md

## Architecture

Three independently installable Claude Code plugins for ML notebook lifecycle governance.
Each targets a specific cloud GPU platform; the universal version covers all four.

```
ml-ops/                          ← Umbrella (this repo)
├── ml-preflight/                ← Universal: Kaggle + Colab + SageMaker + HF Jobs
├── kaggle-ml-preflight/         ← Kaggle-only: P100/T4, 30hr quota, kernel-metadata.json
└── colab-ml-preflight/          ← Colab-only: Drive persistence, disconnect handling, tier detection
```

All three share the same 6-stage lifecycle:
```
build → check → launch → monitor → (fail?) → blackbox + handbook (on-demand)
```

Each product ships: 6 skills, 6 commands, 4 scripts, 10 case studies, 5 adapters, references, templates.

### Product Matrix

| Product | Platform | GPU Concerns | Key Differentiator |
|---------|----------|-------------|-------------------|
| ml-preflight | All 4 | Platform-generic | `--platform` flag on all scripts |
| kaggle-ml-preflight | Kaggle | P100/T4, 30hr quota | No `--platform` flag needed |
| colab-ml-preflight | Colab | T4/L4/A100, preemption | Drive persistence, heartbeat cells |

### Relationship to agents-infra

ml-ops is NOT part of the Agent Rigor suite (agents-infra). It is a sibling umbrella under `~/vibe-coding/Agents/`. Agents-infra governs AI agent behavior; ml-ops governs ML notebook execution.

## Commands

All three products share the same 6 commands:
`/build`, `/check`, `/launch`, `/monitor`, `/blackbox`, `/handbook`

Scripts (per product): `preflight_check.py`, `env_parity.py`, `poll_monitor.py`, `triage.py`

## Conventions

- **Sub-projects are standalone repos** — Each has its own `.git`, `package.json`, `README.md`, `CLAUDE.md`. The umbrella coordinates but does not own.
- **No `--platform` in platform-specific variants** — kaggle-ml-preflight and colab-ml-preflight hardcode their target. Only ml-preflight uses `--platform`.
- **Case studies are generalized** — Source material is GordonAI's `MLOPS_GOVERNANCE.md` (438 lines). Zero GordonAI references in shipped products.
- **Skills are self-contained** — Load one skill at the trigger point, not the whole plugin.
- **Scripts are importable AND standalone** — No ML framework dependencies.
- **Governance rules use S-numbers** — S1 (Deterministic), S2 (Env Hardening), etc. Traced from case studies.
- **Platform snapshots are JSON** — In `platforms/`, updatable independently.
- **MIT licensed** — All three products.

## Key Files

| Path | Purpose |
|------|---------|
| `*/CLAUDE.md` | Per-product architecture (sub-project CLAUDEs override this file) |
| `*/package.json` | Product metadata (no build scripts) |
| `*/install.sh` | Product installer (copies into target repo) |
| `*/scripts/` | 4 Python validation/monitoring scripts |
| `*/case_studies/` | 10 generalized failure case studies |
| `*/platforms/` | Platform environment snapshots (JSON) |
| `*/adapters/` | Claude Code, Codex, Cursor, Aider, Generic |
| `*/references/` | GPU matrix, error taxonomy, triage tree, cheatsheet |

## Gotchas

- **No build step** — These are prompt-based plugins. `package.json` is metadata only. No `npm install/build/test`.
- **Each sub-project is its own git repo** — The umbrella `ml-ops/` is a separate repo from the sub-projects.
- **install.sh is destructive** — Copies files into target repo, overwrites existing prompts/commands/skills.
- **Sub-project CLAUDEs override this file** — When working inside a sub-project, its CLAUDE.md takes precedence.
- **Python 3.10+ required** — Scripts use modern syntax (match/case, type unions).
- **Platform snapshots go stale** — Kaggle/Colab environments update. Snapshots in `platforms/` need periodic refresh.
