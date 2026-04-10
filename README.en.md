# github-upload-skill

> [中文版](README.md) | English

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Claude Code Skill](https://img.shields.io/badge/Claude%20Code-Skill-blue)](https://claude.ai/code)
[![GitHub Automation](https://img.shields.io/badge/GitHub-Automation-green)](https://github.com/jiayu6954-sudo/github-upload-skill)

An AI-driven Claude Code Skill that guides you through the complete workflow — pre-upload checks, repository creation, and push — for **any local project**.

---

## How Is This Different from `gh` or `auto-publish-github`?

> **In one sentence**: It's not a standalone CLI tool. It's an **AI-driven interactive guide** with a standardized workflow and real-world pitfall experience built in.

| Dimension | `gh` CLI | This Skill |
|-----------|---------|------------|
| Type | Standalone CLI tool | Claude Code AI interactive guide |
| Install | Requires binary installation | Copy one `.md` file |
| Guidance | Executes commands, no guidance | Step-by-step, with active pre-checks at key points |
| Sensitive file scan | Not included | ✅ Built-in, blocks on detection |
| LICENSE placeholder check | Not included | ✅ Auto-detects `[Author]` placeholder |
| Push failure handling | Errors out, self-debug | ✅ Self-healing guide + auto-retry script |
| Pitfall knowledge | None | ✅ 10+ real pitfalls from actual projects |

---

## Quick Install (30 seconds)

```bash
# 1. Clone or download github-upload.md
# 2. Copy to Claude Code skills directory

# Windows
copy github-upload.md %USERPROFILE%\.claude\skills\github-upload.md

# macOS / Linux
cp github-upload.md ~/.claude/skills/github-upload.md
```

Then type `/github-upload` in Claude Code to trigger.

---

## Key Features

### Smart Pre-checks (Phase 1)

No more manual eyeballing — AI runs and interprets checks automatically:

- **LICENSE placeholder detection**: Checks if `[Author]` is replaced; blocks and alerts if not
- **Sensitive file scan**: Scans source files for password/key keywords; stops on detection
- **Standard file completeness**: Prompts and provides templates for missing README / LICENSE / .gitignore

### Failure Self-Healing (Phase 5)

No more confusion when push fails:

| Failure Type | Self-Healing |
|-------------|-------------|
| Network timeout | Auto-retry after 30s (built-in retry script) |
| Proxy/firewall blocking | Guide to switch to SSH remote |
| Token expired | Direct link to regeneration page |
| Remote conflict | `git pull --rebase` then re-push |

---

## Full Workflow (6 Phases)

```
Phase 1: Smart pre-upload check  → LICENSE check + secret scan + file validation
Phase 2: Credential binding      → Store Token once, valid forever on this machine
Phase 3: Create repository       → GitHub API, never touches existing repos
Phase 4: Git initialization      → git init + remote add + address verification
Phase 5: Commit & push           → git add + commit + push + self-healing
Phase 6: Verify results          → Check files, rendering, no secrets exposed
```

---

## Compatibility

- **Any language**: Python / Java / Go / TypeScript / Rust / C++ …
- **Any scale**: personal projects, team projects, open-source libraries
- **Any OS**: Windows / macOS / Linux
- **Any AI tool**: `github-upload.md` can be copy-pasted into any LLM

---

## Known Pitfalls (from real usage)

| Pitfall | Symptom | Fix |
|---------|---------|-----|
| Non-ASCII in curl description | `Problems parsing JSON` | Use plain English, wrap JSON in single quotes |
| Wrong remote URL | Overwrites existing repo | `git remote -v` to verify before pushing |
| Token missing `repo` scope | `403 Forbidden` | Regenerate Token with `repo` permission |
| `[Author]` not replaced in LICENSE | No legal standing | Phase 1 smart pre-check blocks this automatically |
| Intermittent network failure | push interrupted | Built-in retry script handles it |

---

## Part of a Larger Ecosystem

This Skill is a sub-project of **[AI-Project-OS](https://github.com/jiayu6954-sudo/AI-Project-OS)** — an industrial-grade AI collaboration methodology library featuring a 6-phase workflow, code review checklist, constraint prompt library, and case study knowledge base.

---

## License

[MIT License](LICENSE) © 2026 jiayu6954-sudo

---

*Version: v1.2 · Updated: 2026-04-10*  
*Keywords: claude-code-skill · github-automation · ai-workflow · git-upload · one-click-deploy*
