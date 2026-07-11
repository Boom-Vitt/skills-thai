---
"mattpocock-skills": patch
---

Make `/setup-matt-pocock-skills` friendlier and align the local-markdown tracker with the current spec.

- **Triage labels** are now asked about only when the `triage` skill is installed, and then as a single recommended-yes question ("keep the default triage labels?") instead of an override interrogation. When `triage` isn't installed, the section — and `docs/agents/triage-labels.md` — are skipped.
- **External PRs as a request surface** is no longer a setup question. The GitHub/GitLab templates still carry the flag, defaulted off; a user can flip it in `docs/agents/issue-tracker.md` later.
- **Domain docs** default to single-context without asking; multi-context is only offered when the repo shows monorepo signals.
- **Local-markdown tickets** are now one file per ticket under `.scratch/<feature>/issues/<NN>-<slug>.md` — never a single combined `tickets.md`. `/to-tickets` and the local issue-tracker template now agree, and the spec file is `spec.md` (not `PRD.md`) to match `/to-spec`.

Docs pages for `setup-matt-pocock-skills` and `to-tickets` re-synced.
