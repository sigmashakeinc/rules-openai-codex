# rules-openai-codex

OpenAI Codex governance rules — guards against the documented sandbox escapes, GitHub token theft, DNS-based data exfiltration, and branch command injection vulnerabilities that put codebases and cloud credentials at risk.

**12 rules · 3 files**

![rules-openai-codex — AI agent governance demo](demo.cast)

> [▶ Watch interactive demo on SigmaShake Hub](https://hub.sigmashake.com/ruleset/rules-openai-codex)

## Install

```bash
ssg hub pull rules-openai-codex
```

Available on the [SigmaShake Hub](https://hub.sigmashake.com) — the open registry for AI agent governance rules. Compatible with Claude Code, Cursor, Windsurf, Aider, and any AI coding agent using the `ssg` hook protocol.

## Rules

### codex_exec_injection.rules — Execution injection safety (5 rules)

| Rule | Decision | Severity | Description |
|------|----------|----------|-------------|
| `no-codex-branch-command-injection` | DENY | error | Blocks git commands with shell metacharacters used for lateral codebase access |
| `no-codex-dns-exfiltration` | DENY | error | Blocks dig/nslookup/host and known OAST/Burp Collaborator exfiltration endpoints |
| `no-codex-github-token-access` | DENY | error | Blocks GitHub Installation Access Token exposure in shell commands |
| `ask-codex-inline-interpreter` | ASK | warning | Prompts before python -c, node -e, ruby -e one-liner execution |
| `log-codex-sandbox-probe` | LOG | info | Audits env/whoami/id/proc reconnaissance commands |

### codex_network_exfiltration.rules — Network exfiltration prevention (4 rules)

| Rule | Decision | Severity | Description |
|------|----------|----------|-------------|
| `no-codex-outbound-http-with-data` | DENY | error | Blocks curl/wget POST requests that can transmit stolen data |
| `no-codex-base64-in-url` | DENY | error | Blocks long base64-encoded strings in URLs (data encoding for exfiltration) |
| `ask-codex-outbound-network` | ASK | warning | Prompts before any outbound curl/wget request |
| `log-codex-raw-socket-tools` | LOG | warning | Audits netcat/socat/bash /dev/tcp (reverse shell indicators) |

### codex_write_persistence.rules — Persistence prevention (3 rules)

| Rule | Decision | Severity | Description |
|------|----------|----------|-------------|
| `no-codex-git-config-write` | DENY | error | Blocks writing .gitconfig and .git/hooks (persistence mechanism) |
| `ask-codex-workflow-modification` | ASK | warning | Prompts before modifying GitHub Actions workflows |
| `log-codex-container-config-write` | LOG | info | Audits Dockerfile and docker-compose writes for sandbox escape patterns |

## Why this matters

OpenAI's Codex operates as an autonomous agent with access to your entire codebase and Git history, making it a high-value target for three documented attack classes:

- **Branch command injection** (reported Dec 2025, patched Feb 2026): Injecting shell metacharacters into git branch names granted lateral movement and read/write access to the victim's entire codebase. The attack could also steal GitHub Installation Access Tokens and execute bash on the code review container.
- **DNS-based exfiltration** (OpenAI security research): A hidden DNS-based communication path was used as a "covert transport mechanism" to exfiltrate data and establish remote shell access inside the Linux runtime — bypassing HTTP-level safeguards.
- **Arbitrary code execution** (Check Point Research, Aug 2025, CVE patched in v0.23.0): A flaw allowed arbitrary code execution within the Codex CLI sandbox.

## Compatible AI clients

- Any agent using OpenAI Codex or Codex CLI
- Works alongside these rules: `rules-security`, `rules-secrets`, `rules-git`

## About

Part of the [SigmaShake Hub](https://hub.sigmashake.com) — open-source governance rules for AI coding agents.
Install the `ssg` CLI to enforce these rules: `npm install -g @sigmashake/ssg`
