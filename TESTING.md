# Deduplicate scan findings — evaluation guide

Version under test: `1.0.0`. Use normalized fixture records with pre-registered expected clusters, tiers, and possible-duplicate pairs. Keep development and held-out fixtures separate.

| Fixture | Expected behavior |
|---|---|
| Same CodeQL alert in two runs, lines moved, same `primaryLocationLineHash` | One cluster, Tier A. |
| Semgrep and CodeQL both report SQL injection at the same sink line | One cluster, Tier B, conflicts recorded if CWEs differ. |
| Same rule, same file, two different sinks | Two clusters. |
| Same CVE in one package version via two manifests | Two clusters (later one causal group). |
| GHSA from one tool and CVE alias from another, same package and version | One cluster after alias resolution. |
| Same CVE, same package, different installed versions | Two clusters. |
| Identical records in two different services | Two clusters. |
| Same secret hash at two locations | Two clusters. |
| Two SAST records that match on file and CWE, lines from different revisions, no fingerprint | Possible duplicate; not merged. |
| Prior ledger marks a record false positive; code at that location changed | Linked, marked `stale-decision`; dismissal not applied. |

## Scoring and release checks

Report merge precision (merged pairs that are truly duplicates) and recall separately. Weigh a wrong merge as worse than a missed merge, because a wrong merge hides a real finding. Fail the release on any deleted record, any cross-target merge, any merge without cited keys, or any prior dismissal applied to changed code. Check count reconciliation by code.
