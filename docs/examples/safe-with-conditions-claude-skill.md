# Example audit: SAFE-WITH-CONDITIONS — a third-party Claude skill

A real audit of a mid-size community Claude skill (hundreds of stars, real merged community PRs). Its whole purpose is to reprogram how the agent plans, delegates, and verifies, so it is exactly the kind of agent-facing surface where a prompt-injection attack would hide. That makes Phase 5 (prompt injection in agent files) the core of this audit. Every file in the repo is agent-facing text, so the "read 100% of every file" rule for agent surfaces applies and was met.

The point of this example: a clean result is not "no findings, ship it". It is a security-clean verdict with named, honest conditions, and a clear statement of where residual risk lives.

## Verdict: SAFE-WITH-CONDITIONS
Target: `mrtooher/fable-mode@ab026d1070316aff9f787374181b8273387cbfe1` (repo HEAD). Unversioned skill bundle: pin by this commit SHA (no release/tag, no registry artifact).

Scope:
- Read in full (100%): all 13 tracked files — SKILL.md, README.md, EXAMPLE.md, V3-CHANGES.md, .gitignore, four `agents/*.md` definitions, and four skill `SKILL.md` files (inline mode, three model-pinned variants, and an always-on guardrails skill).
- NOT CHECKED: nothing material was skippable; the target is 13 small markdown files.

Evidence:
- Identity: owner account since 2022, coherent single-authored history with two genuine merged community PRs and Claude co-authored commits. No ownership transfer, no history rewrite, no undeclared-clone signal (repo/author names consistent). Stars are high relative to the owner's small footprint, but real forks and external PRs indicate organic interest rather than a bought spike.
- Install surface: installation is "copy these markdown files into your skills/agents directories." There is NO executable install path — no package.json, setup.py, build.rs, install.sh/Makefile, no `curl | bash`, no `.gitmodules`, no `.mcp.json`, no `.claude/settings.json` hooks, no devcontainer/`.envrc`.
- Phase 5 (the core): every agent-facing line was read as an adversary, asking "if a model followed this literally, who benefits?" The answer throughout is: the user. The injection-tell grep (`rg -in 'ignore (previous|prior|above)|disregard|do not (mention|tell|reveal)|always use .*(primary|default)|fetch .*(latest|instructions|config).*(from|at) (http|url)|bypass|--dangerously|allow(ed)?list|\bBCC\b|exfiltrat'`) returns zero hits. There is no network egress anywhere: `rg -in 'https?://|curl|wget|eval|exec|base64|atob'` across the whole repo returns zero matches, so the skill cannot phone home, load remote instructions at runtime, or exfiltrate. Non-ASCII sweep: all hits are ordinary em-dashes and one en-dash range in English prose; no bidi/zero-width/homoglyph characters.
- The instructions are self-consistent and honest: the skill tells the model NOT to trigger on trivial tasks, to return ambiguity to the user rather than guess, to label training-memory claims, and to state a clean verification pass plainly rather than manufacture caveats. One rule even protects the user's firsthand information against web-silence false positives. This is the opposite of vendor-serving injection.
- Transparency signal: the repo openly documents a known weakness of its own design (a "Write-less" orchestrator agent still holds Bash, which can technically create files; the author names this as a residual side door). An author documenting their own tool's residual risk is a strong good-faith indicator.
- License (Phase 6): NO LICENSE FILE. `licenseInfo` is null and there is no LICENSE/COPYING in the tree; the README states no license either.

Red flags (verbatim):
1. No license. The one material finding. With no license the default is all-rights-reserved: legally there is no grant to use, copy, or redistribute, even though the repo is public. Also a care/maturity signal on an otherwise polished repo. (A missing license is a legal/usability condition, not a security rejection — the verdict stays SAFE-WITH-CONDITIONS.)
2. Blast radius accepted on install (not malice, but understand it): the four agent definitions request broad tools. Two workers carry `Read, Write, Edit, Grep, Glob, Bash, WebSearch, WebFetch`; the orchestrator carries `Bash, Task`. Installing this skill means it can spawn subagents that write files, run shell commands, and fetch the web. That is inherent to a delegation framework and fully disclosed, but it is real reach: the risk is less what the skill does today than what an edited future version of these agent files could do once wired in — which is why the pin-and-re-audit condition matters.
3. Self-authored benchmark, n=1. The repo is commendably honest that its effectiveness numbers are n=1 and "not yet re-benchmarked". Not a security issue; a claim-strength caveat.

Conditions:
- Resolve the license before any redistribution or commercial use: treat as all-rights-reserved until the author adds one (open an issue asking for an explicit license).
- Pin to commit `ab026d1` and record it. Re-audit on every update: because the agents carry Write/Edit/Bash/WebFetch, a future commit could change their behaviour, and a moved skill bundle is unaudited by definition.
- Understand the tool grants before installing the agent definitions. If you only want the discipline and not the delegation reach, install just the inline skill and the guardrails skill and skip the `agents/` directory (the skills fall back to inline mode when the agents are absent).

Verify yourself:
1. `gh repo view mrtooher/fable-mode --json licenseInfo` — confirms `licenseInfo` is null.
2. `git clone https://github.com/mrtooher/fable-mode && cd fable-mode && rg -in 'https?://|curl|wget|eval|exec|base64' .` — confirms no network egress or code-exec anywhere.
3. `rg -n 'tools:' agents/*.md` — read the exact tool grant each installed agent carries before wiring it in.
