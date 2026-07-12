# Example audit: UNSAFE, a repackaged CLI clone hiding a malware dropper

A real audit run of a young, low-star GitHub repo advertising an installable CLI (`skills` / `agent-skills` bins). It looked like an ordinary small utility. It is a repackaged clone of a legitimate project with an injected Windows dropper, distributed through a fake "download and run the installer" README. This is the shape of the thing the skill exists to catch: the code that ran the search and reads was never executed against the target, only read.

Names below are the real public repositories. Every command is one you can run yourself.

## Verdict: UNSAFE
Target: `stbarbe/agent-skills-cli@fc1876caf56391a31b418441291e38e2653b834c` (repo HEAD). This is NOT the npm package `agent-skills-cli` (that resolves to the legitimate upstream, see below); the target is specifically this GitHub repository as a code/download source.

Scope:
- Read in full: README.md (and its full git diff vs the inherited upstream), package.json, all 13 TypeScript source files under src/, both SKILL.md files, the git history, and the metadata (not the bytes) of the checked-in archive.
- Characterized read-only, NEVER extracted or executed: `skills/deep-researcher/cli-skills-agent-retainableness.zip` (sha256 `2763e2dc86450b13aead6f2e8395822c05eeca75e9900e8be74682a1d2b2a1b3`). Its central directory was listed and magic bytes / the 26-byte launcher read in memory; the .exe and payload were never run, unpacked to disk, or imported.
- NOT CHECKED: dynamic behaviour of the dropped executable (out of scope for a read-only audit; would need a disposable sandbox). It does not need to run to reach the verdict.

Evidence:
1. Undeclared repackaged clone. GitHub reports `isFork:false`, `parent:null`, yet `git log` shows the history is another author's project up to a shared commit (`d563c24`), authored by "Karanjot786" / "Karanjot Singh". package.json still declares `"author": "Karanjot786"` and `"repository": "https://github.com/Karanjot786/agent-skills-cli"`: it points at a different owner than the repo hosting it. Owner `stbarbe` is a near-empty account (created 2025-01-04; at the audit date, 0 followers and 2 public repos, figures that can drift). The legitimate upstream `Karanjot786/agent-skills-cli` has an order of magnitude more stars, a real multi-year author, and is what the npm package points to. Karanjot786 is named here only as the legitimate author whose project was copied; nothing in this report implies any wrongdoing on their part.
2. Injected dropper. The only substantive changes on top of the clone (`git diff d563c24..HEAD`): deleted issue/PR templates, rewrote README.md, and added one binary, `skills/deep-researcher/cli-skills-agent-retainableness.zip` (542 KB). The archive contains exactly three members:
   - `gcm.exe` (771,584 bytes), a Windows PE executable (magic bytes `MZ` / `4d5a90...`).
   - `bytecode.txt` (299,641 bytes), an obfuscated payload; first bytes are a string-assembly deobfuscator wrapper `return(function(...)local J=function(M)... for X=1,#M/2 ...` (Lua-style obfuscation).
   - `Launcher.cmd` (26 bytes), contents verbatim: `start gcm.exe bytecode.txt` (runs the executable with the obfuscated blob as its argument).
3. Social-engineering funnel. The rewritten README is a fake "app installer" page aimed at non-technical users: a "Download Now" badge and repeated "Download the Latest Version" links, all pointing at the repo-hosted ZIP, with step-by-step "Run the Installer / Windows Users: Double-click the .exe" prose. The badge URL is even malformed (two ZIP URLs concatenated with a stray `%`), a hallmark of the auto-generated fake-README campaign. None of this appears in the real upstream README, which documents `npm install -g agent-skills-cli`.
4. Decoy. The dropper sits inside `skills/deep-researcher/` next to a wholly benign, legitimate-looking `SKILL.md`, so the folder reads as a normal skill directory at a glance.
5. The source (src/) is the unmodified upstream and does not reference the ZIP (`rg 'retainableness|\.zip|gcm\.exe|Launcher\.cmd'` over src/ = no matches). package.json has no preinstall/install/postinstall hook. The attack does not auto-run on install; it runs when a human, following the README, downloads and double-clicks the payload. That is the entire point of the fake README, and why "audit what a user is instructed to run" matters as much as "audit what auto-executes".

Red flags: every item above is a flag. The decisive one: a checked-in Windows executable plus an obfuscated blob plus a `.cmd` that launches them, distributed through a README that instructs the reader to run them.

Conditions: none. There is no safe way to use this repository. Do not clone it as a dependency source, do not download or run the release ZIP. If the CLI's functionality is actually wanted, audit the upstream `Karanjot786/agent-skills-cli` (npm `agent-skills-cli`) separately on its own merits; this report does not clear it, it only identifies it as the thing that was copied.

Verify yourself (all read-only, none run the payload):
1. `git clone https://github.com/stbarbe/agent-skills-cli && cd agent-skills-cli && git log --format='%an' | sort -u`: the history authors are Karanjot786, not stbarbe.
2. `python -c "import zipfile; print(zipfile.ZipFile('skills/deep-researcher/cli-skills-agent-retainableness.zip').namelist())"`: lists gcm.exe, bytecode.txt, Launcher.cmd without extracting.
3. `python -c "import zipfile; print(zipfile.ZipFile('skills/deep-researcher/cli-skills-agent-retainableness.zip').read('Launcher.cmd'))"`: prints `b'start gcm.exe bytecode.txt'`.
4. Open README.md and confirm the "Download / double-click the .exe" instructions and the malformed badge URL; compare to the upstream README's `npm install` quickstart.
