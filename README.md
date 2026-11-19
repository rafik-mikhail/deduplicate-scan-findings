# Deduplicate Scan Findings

An agent skill that links normalized finding records that report the same finding, from the same tool across runs or from different tools, into clusters. It merges only on exact tool identity or a full category key match, lists near matches for human review, and never deletes a record. It also tags each cluster with a fix target that is known without judging the finding, such as a dependency and lockfile, base image, IaC module, or secret, and counts findings per rule.

## Use the skill

Use [`SKILL.md`](SKILL.md) in your agent’s skill directory under `deduplicate-scan-findings/`. Provide the normalized records from [Normalize Scan Report](https://github.com/rafik-mikhail/normalize-scan-report). Optionally provide a prior decision ledger and a rule-equivalence map. The skill writes `dedup/clusters.jsonl` (with `fix_target`), `dedup/possible-duplicates.md`, and `dedup/report.md` (with fix targets and rule volumes).

## Use as agent instructions

Copy the [Deduplicate Scan Findings instruction gist](https://gist.github.com/rafik-mikhail/73ff169f5a38594ac520f7919164d431) into your agent’s instruction file. The gist contains the same guidance with `applyTo: "**"` front matter for tools that support it. If your agent accepts plain Markdown instructions, copy the body without the front matter.

## Where it fits

This skill is step 2 of a six-step workflow that takes a received scan report to fix pull requests. Steps 1–3 work on the whole report, steps 4–5 work on one finding at a time, and step 6 works on one batch at a time.

| Step | Skill | Works on |
|---|---|---|
| 1 | [Normalize Scan Report](https://github.com/rafik-mikhail/normalize-scan-report) | whole report |
| 2 | **Deduplicate Scan Findings** | whole report |
| 3 | [Prioritize Scan Findings](https://github.com/rafik-mikhail/prioritize-scan-findings) | whole report: sets the order and route; again for confirmed findings |
| 4 | [Validate Scan Finding](https://github.com/rafik-mikhail/validate-scan-finding) | one finding at a time (`validate` route and rule samples) |
| 5 | [Decide Finding Action](https://github.com/rafik-mikhail/decide-finding-action) | one finding at a time; one rule per rule sample |
| 6 | [Batch Finding Fixes](https://github.com/rafik-mikhail/batch-finding-fixes) | one batch at a time |

Step 3 sends each finding down one route:

| Route | When | Path |
|---|---|---|
| `fix-first` | Dependency or base-image upgrade, patch or minor within the major version, tests in CI | Decide (`not-validated`, `fix`) → Batch. A failing test sends the findings to Validate. |
| `sample-rule` | Many findings from one rule, none `immediate` or `out-of-cycle` | Validate about 5 → Decide. If refuted for one shared reason, propose a rule change and hold the rest until rescan. |
| `validate` | Everything else | Validate → Decide → Batch |
| `defer-track` | SSVC `defer` | No review now; reassess on a KEV listing, a new exploit, an exposure change, or a new fix. |

Findings that share a fix are linked in step 2 only when the fix is known mechanically (dependency, image, IaC module, secret). Shared code causes are grouped in step 5 through the fix key, after validation. [Group Findings by Root Cause](https://github.com/rafik-mikhail/group-findings-by-root-cause) is retired.

Verification by rescan and closing with regression monitoring are not covered yet.

## How to test

Follow the [evaluation guide](TESTING.md).

## Notes

[NOTES.md](NOTES.md) covers gotchas, areas for improvement, and the sources behind the guidance.
