**What does this PR change?**

**Why?**
<!-- Link the issue if one exists. For a new detection pattern: the public example that motivates it and what the audit currently misses. -->

**How was it tested?**
<!-- The exact grep or command you ran and its output (minus anything private). For a new example audit: the public target, pinned by SHA/hash. "Doc-only, untested" is honest and acceptable for wording changes. -->

**Checklist**
- [ ] No target was installed, built, or executed; all work is read-only
- [ ] No secrets, tokens, private URLs, or personal paths in the skill, docs, or examples
- [ ] Any new example is sanitized and pins its target by commit SHA or artifact hash
- [ ] SKILL.md stays compact (edited an existing phase rather than adding one, where possible)
- [ ] Any new command runs where it is documented to work (portable `rg`/`gh`/`git` in bash and PowerShell; Unix pipe helpers and `curl` noted as Git Bash on Windows)
