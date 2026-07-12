# Contributing

This is a security-audit procedure, and procedures improve by being run. Every hardening in this skill came from auditing a real repo and finding a gap: a grep that missed a pattern, an assumption that did not hold, a platform quirk. That is the contribution this repo wants most, and you do not need to know the codebase to make it.

## What is most welcome

1. **A new detection pattern with a real example.** You found a repo doing something the audit would miss, and the check that would have caught it. Open an issue with the (public) repo or package, what the audit misses, and the grep or reasoning that catches it. A pattern without a motivating example is hard to weigh, so bring one.
2. **A sanitized example audit for `docs/examples/`.** A full run against a public target in the exact verdict format, with no local paths, private names, or unpinned references. These are what make the skill trustworthy; a good one can carry the whole repo.
3. **A platform fix.** Every command in `SKILL.md` must run unchanged in both bash and PowerShell. If one does not, that is a bug worth a PR.
4. **A false-positive or false-negative report.** A red flag the skill raises that is actually benign, or a benign-looking construct that is actually dangerous, both make the procedure sharper.

## Your first 30 minutes

Issues labeled [`good first issue`](https://github.com/belschak/repo-audit/issues?q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22) are scoped to 30 to 60 minutes and need no prior knowledge of the project. Each one states its acceptance criteria in the body.

## Setup

There is no build and there are no dependencies.

```bash
git clone https://github.com/belschak/repo-audit.git
cd repo-audit
```

To test the skill itself, copy (or clone) the folder into your agent's skills directory (`~/.claude/skills/` for Claude Code) and point it at a public repo you want vetted.

## Ground rules for contributors (they are also the skill's)

- **Never run the target.** All development and examples are read-only. If a PR's example required installing, building, or executing an audited repo, it does not merge; that is the one thing the skill exists to prevent.
- **Examples are sanitized.** No local paths (`C:\Users\...`, home directories), no private URLs, no personal or third-party names beyond what a target's own public metadata contains. Pin every target by commit SHA or artifact hash.
- **`SKILL.md` stays compact.** It is loaded into an agent's context; every sentence costs tokens. Prefer editing an existing phase over adding a new one.
- **Evidence, not assertion.** A detection pattern claim states the real example that motivates it. A red flag in an example cites `file:line` or command output.

## PR process

1. Open an issue first for anything beyond a small fix, so the pattern or change can be discussed before you build it.
2. Fork, branch, commit. Keep the diff focused on one change.
3. In the PR description: what changed, why, and (for a new pattern) the public example that motivates it.
4. Expect a review within a few days. Small, evidence-backed PRs merge fastest.

## Code of conduct

This project follows the [Contributor Covenant](CODE_OF_CONDUCT.md). Short version: be decent, assume good faith.
