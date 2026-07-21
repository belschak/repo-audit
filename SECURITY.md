# Security policy

## Reporting

Report suspected vulnerabilities privately through GitHub's private vulnerability
reporting: the "Report a vulnerability" button under this repository's Security
tab. Please do not open a public issue for those.

One person maintains this repo. In a normal week I read a report within a few
days. If you have heard nothing after a week, open a public issue that says you
sent a private report, with no details in it, and I will pick it up.

## Scope

This repository is a Claude Code skill: Markdown instructions and reference docs.
It ships no executable code, no service, and nothing to keep patched. The reports
that matter here are about the instructions themselves. An example: a step in the
procedure that would push an auditing agent into running something from the repo
it is auditing, or a convention that leads a user to paste private data into a
public place.

Text in this repo reaches other people's agents, because the skill is meant to be
copied into a `~/.claude` setup and run there. A prompt injection hidden in the
procedure is in scope for that reason.

## Not a vulnerability

False positives, false negatives, and gaps in the detection patterns are ordinary
public issues, and they are the contribution this repo wants most (CONTRIBUTING,
points 1 and 4). An example audit you believe is inaccurate goes through the
public correction path in the README's Disclaimer section: open an issue with
evidence.
