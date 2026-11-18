# 🧰 Agent Skills Setup Guide for Web & App Dev

A practical guide to installing and using **skills** in **Claude Code** and **OpenAI Codex CLI**, with a curated stack for web and mobile development, plus **REA** (Reverse Engineer Anything).

> Skills are plain-text instruction folders (`SKILL.md` + optional scripts) that your agent loads **only when relevant**. They are not executable plugins by default, but some ship helper scripts, so **read a skill before you install it.**

---

## 📑 Table of Contents

1. [What is a skill?](#-what-is-a-skill)
2. [Prerequisites](#-prerequisites)
3. [Where skills live](#-where-skills-live)
4. [Install methods](#-install-methods)
5. [Recommended skill stack](#-recommended-skill-stack)
6. [REA: Reverse Engineer Anything](#-rea-reverse-engineer-anything)
7. [Verify, use, and update](#-verify-use-and-update)
8. [Write your own skill](#-write-your-own-skill)
9. [Troubleshooting](#-troubleshooting)
10. [Security checklist](#-security-checklist)

---

## 🧠 What is a skill?

A skill is a folder with this shape:

```
my-skill/
├── SKILL.md        # required: YAML frontmatter + instructions
├── scripts/        # optional: helper scripts
├── references/     # optional: extra docs the agent reads on demand
└── assets/         # optional: templates, images, etc.
```

Minimal `SKILL.md`:

```markdown
---
name: my-skill
description: One clear sentence on WHAT it does and WHEN to use it.
---

# Instructions
Step-by-step guidance the agent follows.
```

The `description` is what the agent uses to decide whether to load the skill. A vague description means the skill never triggers.

**Skills vs. the alternatives**

| Mechanism | Loaded | Best for |
|---|---|---|
| `CLAUDE.md` / `AGENTS.md` | Always | Project rules, commands, conventions |
| **Skills** | On demand | Repeatable workflows and expert knowledge |
| **MCP servers** | When connected | Giving the agent live tools and external data |

---

## ✅ Prerequisites

- **Node.js 18+** (for `npx skills ...`)
- **Git**
- **Claude Code** and/or **Codex CLI** installed and logged in
- A terminal you trust (macOS, Linux, or WSL on Windows)

```bash
node -v
git --version
```

---

## 📂 Where skills live

| Agent | Project-level | Global (all projects) |
|---|---|---|
| **Claude Code** | `.claude/skills/<name>/SKILL.md` | `~/.claude/skills/<name>/SKILL.md` |
| **Codex CLI** | `.agents/skills/` (newer) or `.codex/skills/` | `~/.agents/skills/` or `~/.codex/skills/` |

> Codex path conventions have changed between versions. Run `/skills` inside Codex to see what it actually detects, and restart Codex after installing.

**Project-level** skills can be committed to git so your whole team gets them. **Global** skills follow you everywhere.

---

## 🚀 Install methods

### Method 1: `skills` CLI (recommended, works for both agents)

```bash
# Install everything from a repo (interactive: pick agents + skills)
npx skills add <owner>/<repo>

# Install ONE skill, for specific agents, globally, no prompts
npx skills add <owner>/<repo> --skill <skill-name> -g -a claude-code -a codex -y
```

| Flag | Meaning |
|---|---|
| `--skill <name>` | Cherry-pick one skill from a multi-skill repo |
| `-g` | Install globally (personal library) instead of the project |
| `-a <agent>` | Target a specific agent (`claude-code`, `codex`, `cursor`, ...) |
| `-y` | Skip confirmation prompts |

### Method 2: Claude Code plugin marketplace

Inside Claude Code:

```
/plugin marketplace add anthropics/skills
/plugin
```

Then browse and install from the UI.

### Method 3: Codex built-in installer

Inside a Codex session:

```
$skill-installer <skill-name or GitHub URL>
```

Restart Codex afterwards.

### Method 4: Manual (always works)

```bash
git clone https://github.com/<owner>/<repo>.git /tmp/skills-repo

# Claude Code (global)
cp -r /tmp/skills-repo/skills/<skill-name> ~/.claude/skills/

# Codex (global)
cp -r /tmp/skills-repo/skills/<skill-name> ~/.agents/skills/
```

---

## ⭐ Recommended skill stack

### 🎨 UI & design

| Skill | Source | Why |
|---|---|---|
| `frontend-design` | `anthropics/skills` | Pushes the agent away from generic "AI-looking" UI toward intentional design (typography, color, layout) |
| `web-design-guidelines` | `vercel-labs/agent-skills` | Audits UI code against 100+ rules: accessibility, focus states, forms, performance, UX |

```bash
npx skills add anthropics/skills --skill frontend-design -g -a claude-code -a codex -y
npx skills add vercel-labs/agent-skills --skill web-design-guidelines -g -a claude-code -a codex -y
```

### ⚛️ React / Next.js

| Skill | Source | Why |
|---|---|---|
| `vercel-react-best-practices` | `vercel-labs/agent-skills` | 40+ prioritized performance rules from Vercel Engineering: waterfalls, bundle size, re-renders |
| `vercel-composition-patterns` | `vercel-labs/agent-skills` | Cleaner component architecture, avoiding prop drilling and boolean-prop sprawl |
| `vercel-react-view-transitions` | `vercel-labs/agent-skills` | View transitions in Next.js App Router |

```bash
npx skills add vercel-labs/agent-skills --skill vercel-react-best-practices -g -a claude-code -a codex -y
npx skills add vercel-labs/agent-skills --skill vercel-composition-patterns -g -a claude-code -a codex -y
```

### 📱 Mobile (React Native / Expo)

| Skill | Source | Why |
|---|---|---|
| `vercel-react-native-skills` | `vercel-labs/agent-skills` | React Native and Expo best practices: performance, architecture, platform-specific patterns |

```bash
npx skills add vercel-labs/agent-skills --skill vercel-react-native-skills -g -a claude-code -a codex -y
```

### 🧪 Testing

| Skill | Source | Why |
|---|---|---|
| `webapp-testing` | `anthropics/skills` | Drives local web apps with Playwright: verify UI behavior, capture screenshots, read browser logs |

```bash
npx skills add anthropics/skills --skill webapp-testing -g -a claude-code -a codex -y
```

### 🔌 Integrations & tooling

| Skill | Source | Why |
|---|---|---|
| `mcp-builder` | `anthropics/skills` | Guide for building MCP servers (Python FastMCP or Node/TypeScript SDK) |
| `deploy-to-vercel` | `vercel-labs/agent-skills` | Deploy straight from the agent |
| `skill-creator` | `anthropics/skills` | Create, test, and improve your own skills |

### 🧱 Design-to-code (Figma users)

OpenAI's curated catalog includes `figma` and `figma-implement-design` for turning Figma designs into production code (requires the Figma MCP server).

### 🔍 Reverse engineering

| Skill | Source | Why |
|---|---|---|
| `reverse-engineer-anything` (**REA**) | `morluto/rea` | See the [REA section](#-rea-reverse-engineer-anything) below |

### One-shot "starter pack"

```bash
npx skills add anthropics/skills --skill frontend-design --skill webapp-testing --skill skill-creator -g -a claude-code -a codex -y
npx skills add vercel-labs/agent-skills --skill vercel-react-best-practices --skill web-design-guidelines -g -a claude-code -a codex -y
```

> ⚠️ **Don't install everything.** Every skill's name and description sits in the agent's context. Install what matches your stack, not the whole catalog.

---

## 🔬 REA: Reverse Engineer Anything

**REA** lets an agent investigate an existing app or binary (decompile, trace code paths, collect evidence) and then use what it learned in your own build session.

**Important to know up front:**

- It is **not** a general web/app dev skill. It is for **analyzing existing software**.
- The full version exposes a large MCP tool set (70+ in the project's own description; counts differ by version), and its setup is **macOS-oriented** (it integrates with Hopper Disassembler).
- Use it **only on software you built, own, or are authorized to analyze.** The project's disclaimer excludes things like DRM/license bypass, game cheats, and authentication bypass.

### Option A: Full REA (MCP tools + skill)

```bash
npm install --global rea-agents
rea setup
```

`rea setup` detects installed agents (Claude Code, Codex, Cursor, Gemini CLI, and others), prints every proposed change, and **asks before applying**. It registers the MCP server and installs the `reverse-engineer-anything` skill.

Check your install:

```bash
npx -y rea-agents doctor
```

Remove only REA-owned registrations later with the project's uninstall command (see the upstream README).

### Option B: Skill only

```bash
npx skills add morluto/rea
```

### Option C: Lean REA (lightweight alternative)

A stripped-down, single-skill-file take on REA that routes work to native shell commands instead of a large MCP server. Lighter on context, with fewer capabilities.

```bash
npx skills add a16z/lean-rea
```

### Using REA

```text
Use REA to analyze <my app>. Start with an overview, then explain how <feature> works.
```

Tips:

- Always name **which app** and **confirm you have the right to analyze it**.
- Skip REA for ordinary repos where you already have the source. Plain code reading is faster.
- Upstream: <https://github.com/morluto/rea>

---

## 🔎 Verify, use, and update

**Check what's installed**

```bash
ls ~/.claude/skills        # Claude Code global
ls .claude/skills          # Claude Code project
ls ~/.agents/skills        # Codex global (or ~/.codex/skills)
```

Inside Codex, run `/skills`.

**Trigger a skill**

- **Automatically:** describe the task naturally ("review this component for performance issues").
- **Explicitly in Claude Code:** `/skill-name`, or mention it by name.
- **Explicitly in Codex:** mention it by name, or use `$skill-name`.

**Update**

```bash
npx skills add <owner>/<repo> --skill <name> -g -y   # re-run to pull latest
```

---

## ✍️ Write your own skill

1. Create the folder: `mkdir -p .claude/skills/my-skill`
2. Add `SKILL.md` with `name` and `description` frontmatter.
3. Keep `SKILL.md` focused. Move long reference material into `references/` and link to it.
4. Write the `description` as **"does X. Use when Y."** so it triggers reliably.
5. Test with real prompts. If it doesn't trigger, the description is the first thing to fix.

Or let the agent help: install `skill-creator` and ask it to build and test a skill for you.

**Template**

```markdown
---
name: api-endpoint-scaffold
description: Scaffold a typed REST endpoint with validation and tests. Use when the user asks to add a new API route.
---

# API Endpoint Scaffold

## Steps
1. Read existing routes in `src/routes/` to match conventions.
2. Create the handler, schema, and test file.
3. Run the test suite and fix failures before finishing.

## Rules
- Never skip input validation.
- Follow the project's existing error format.
```

---

## 🛠 Troubleshooting

| Problem | Fix |
|---|---|
| Skill installed but never triggers | Make the `description` specific; mention the skill by name to test |
| Codex doesn't see new skills | Restart Codex; run `/skills`; check `.agents/skills` vs `.codex/skills` |
| `npx skills` can't find an agent | Make sure the agent's config folder exists (e.g. `.claude`), or use manual install |
| Agent feels slow or confused | Too many skills installed; remove the ones you don't use |
| REA setup fails | Run `npx -y rea-agents doctor` and read what it reports |
| Permission errors on global install | Fix directory ownership; don't use `sudo` with `npx` |

---

## 🔐 Security checklist

- [ ] **Read `SKILL.md` and any `scripts/` before installing.** A skill can tell your agent to run commands.
- [ ] Prefer **official or well-known sources** (`anthropics/skills`, `vercel-labs/agent-skills`).
- [ ] Be careful with skills that need shell access, tokens, or network calls.
- [ ] Pin or review versions for team and production use.
- [ ] Never put secrets in a skill file.
- [ ] Use reverse-engineering tools like REA **only on software you're authorized to analyze.**

---

## 🔗 Links

- Anthropic skills: <https://github.com/anthropics/skills>
- Vercel agent skills: <https://github.com/vercel-labs/agent-skills>
- REA: <https://github.com/morluto/rea>
- Lean REA: <https://github.com/a16z/lean-rea>

---

## 📄 License

MIT (or your choice). Skills listed here belong to their respective authors and carry their own licenses.
