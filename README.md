# Deduplicate Scan Findings

An agent skill that links normalized finding records that report the same finding, from the same tool across runs or from different tools, into clusters. It merges only on exact tool identity or a full category key match, lists near matches for human review, and never deletes a record.

## Use the skill

Use [`SKILL.md`](SKILL.md) in your agent’s skill directory under `deduplicate-scan-findings/`. Provide the normalized records from [Normalize Scan Report](https://github.com/rafik-mikhail/normalize-scan-report). Optionally provide a prior decision ledger and a rule-equivalence map. The skill writes `dedup/clusters.jsonl`, `dedup/possible-duplicates.md`, and `dedup/report.md`.

## Use as agent instructions

Copy the [Deduplicate Scan Findings instruction gist](https://gist.github.com/rafik-mikhail/73ff169f5a38594ac520f7919164d431) into your agent’s instruction file. The gist contains the same guidance with `applyTo: "**"` front matter for tools that support it. If your agent accepts plain Markdown instructions, copy the body without the front matter.

## Where it fits

This skill is step 2 of a seven-step workflow that takes a received scan report to fix pull requests. Steps 1–4 work on the whole report, steps 5–6 work on one finding at a time, and step 7 works on one batch at a time.

| Step | Skill | Works on |
|---|---|---|
| 1 | [Normalize Scan Report](https://github.com/rafik-mikhail/normalize-scan-report) | whole report |
| 2 | **Deduplicate Scan Findings** | whole report |
| 3 | [Group Findings by Root Cause](https://github.com/rafik-mikhail/group-findings-by-root-cause) | whole report |
| 4 | [Prioritize Scan Findings](https://github.com/rafik-mikhail/prioritize-scan-findings) | whole report, then again for confirmed findings |
| 5 | [Validate Scan Finding](https://github.com/rafik-mikhail/validate-scan-finding) | one finding at a time |
| 6 | [Decide Finding Action](https://github.com/rafik-mikhail/decide-finding-action) | one finding at a time |
| 7 | [Batch Finding Fixes](https://github.com/rafik-mikhail/batch-finding-fixes) | one batch at a time |

Verification by rescan and closing with regression monitoring are not covered yet.

## How to test

Follow the [evaluation guide](TESTING.md).

## Notes

[NOTES.md](NOTES.md) covers gotchas, areas for improvement, and the sources behind the guidance.
