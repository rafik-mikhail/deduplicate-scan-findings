# Notes: Deduplicate scan findings

## Gotchas

- **Duplicate is not related.** The same CVE in two manifests, or the same secret in two files, are separate findings. Merging them loses a location that still needs a fix. They belong in one root-cause group instead.
- **Tool IDs never cross tools.** DefectDojo matches across tools only through a hash of shared fields; one tool's unique ID means nothing to another.
- **Line numbers drift.** Without fingerprints, a line-based match across revisions is a guess. GitHub notes that inconsistent file paths across runs make each analysis open a new alert and close the old one.
- **Hash fields must be reported the same way.** Two SCA tools match only if they report vulnerability ID, component name, and version identically. Alias resolution (GHSA, CVE, OSV) is often the missing step.
- **Service or asset is always part of identity.** DefectDojo adds the service field to every hash, so findings in different services never match.
- **Prior decisions go stale.** A false positive from last quarter may be wrong after a refactor. Carry it forward as context, not as a verdict.

## Areas for improvement

- Add a deterministic matching script with configurable keys per category, so the agent only reviews Tier C pairs.
- Add a shared CWE and rule-equivalence map for common SAST tools.
- Add code-context hashing (normalized surrounding lines) as a fallback when tools give no fingerprint.

## Sources

- [DefectDojo: Deduplication across tools](https://docs.defectdojo.com/triage_findings/finding_deduplication/deduplication_across_tools/): algorithms, hash fields, cross-tool rules.
- [DefectDojo: About deduplication](https://docs.defectdojo.com/en/working_with_findings/finding_deduplication/about_deduplication/): endpoint handling per algorithm.
- [GitHub: SARIF fingerprints](https://docs.github.com/en/code-security/reference/code-scanning/sarif-files/sarif-support): `partialFingerprints` and path consistency.
- [DefectDojo UVM buyer's guide](https://defectdojo.com/hubfs/Whitepapers/UVM_Buyers_Guide_DefectDojo_Pro.pdf): collapse the same CVE reported by several tools into one finding.
