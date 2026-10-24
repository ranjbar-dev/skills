# skills

A collection of AI agent skills that work with **Claude Code**, **Codex CLI**, **Cursor**, **Gemini CLI**, **GitHub Copilot**, **OpenCode**, **Windsurf**, **Cline** and [many more agents](https://github.com/vercel-labs/skills/blob/main/docs/agents.md).

| Skill | What it does |
|-------|--------------|
| [`clarify-to-plan`](skills/clarify-to-plan/SKILL.md) | Interviews you about an idea or task until every choice is clear, then writes one complete plan you can paste into a new AI session. |

---

## Installation

Skills are installed with the open-source [`skills`](https://github.com/vercel-labs/skills) CLI. You don't need to clone this repo. Node.js 22.20 or later is required.

### Interactive install (recommended)

Run this from your project folder:

```bash
npx skills add ranjbar-dev/skills
```

The CLI detects the AI tools you have installed, then asks which skills to install and which agents to install them for.

### Install only `clarify-to-plan`

```bash
npx skills add ranjbar-dev/skills --skill clarify-to-plan
```

### Install for a specific tool

```bash
# Claude Code
npx skills add ranjbar-dev/skills --skill clarify-to-plan -a claude-code

# Codex CLI
npx skills add ranjbar-dev/skills --skill clarify-to-plan -a codex

# Cursor
npx skills add ranjbar-dev/skills --skill clarify-to-plan -a cursor
```

Other agent identifiers include `gemini-cli`, `github-copilot`, `opencode`, `windsurf` and `cline`. The [agents list](https://github.com/vercel-labs/skills/blob/main/docs/agents.md) has the full set.

### Install globally (all projects)

Without `-g`, the skill is installed into the current project only. Add `-g` to make it available in every project:

```bash
npx skills add ranjbar-dev/skills --skill clarify-to-plan -a claude-code -g
```

### Where the files go

| Tool | Project install | Global install (`-g`) |
|------|-----------------|-----------------------|
| Claude Code | `.claude/skills/` | `~/.claude/skills/` |
| Codex CLI | `.agents/skills/` | `~/.agents/skills/` |
| Cursor | `.agents/skills/` | `~/.cursor/skills/` |
| Gemini CLI | `.agents/skills/` | `~/.gemini/skills/` |
| GitHub Copilot | `.agents/skills/` | `~/.copilot/skills/` |
| OpenCode | `.agents/skills/` | `~/.config/opencode/skills/` |
| Windsurf | `.windsurf/skills/` | `~/.codeium/windsurf/skills/` |

### Manual install (no npx)

A skill is just a folder that contains a `SKILL.md` file. You can also copy it yourself:

```bash
git clone https://github.com/ranjbar-dev/skills.git
cp -r skills/skills/clarify-to-plan ~/.claude/skills/     # Claude Code
cp -r skills/skills/clarify-to-plan ~/.agents/skills/     # Codex CLI
```

Restart your AI tool after installing so it picks up the new skill.

### Update to the latest version

Run the same install command again. It overwrites the installed copy with the latest version from GitHub:

```bash
npx skills add ranjbar-dev/skills --skill clarify-to-plan -a claude-code -g
npx skills add ranjbar-dev/skills --skill clarify-to-plan -a codex -g
```

Leave out `-g` if you installed into a project. If you installed manually, run `git pull` in your clone and copy the folder again. Then restart your AI tool, or start a new session.

---

## clarify-to-plan

Turn a rough idea into a plan that another AI can carry out **without guessing what you want**.

AI agents often fill gaps with their own assumptions, such as a framework you didn't pick, a feature you didn't ask for, or a layout you never described. `clarify-to-plan` stops that. It asks you questions until every choice that matters is settled, and only then writes the plan.

### How it works

1. **You give it an idea or task.**
2. **It asks questions only**, and keeps going until the relevant details and choices are clear. There is no fixed question limit. By default it asks one focused question per turn. In Claude Code and Codex CLI you **select the answer** from a list of options, or pick "Other" to type your own (see [Selectable questions](#selectable-questions)).
3. **It doesn't fill gaps with guesses or defaults.** If it proposes a choice, or you say "you choose", you must confirm that choice before it counts.
4. **It asks:** *"Are you ready for me to write the final plan, or would you like to add or change any details first?"*
5. **If you aren't ready**, it collects your additional details and asks follow-up questions as needed.
6. **Once you confirm you're ready**, it gives you **one complete plan to paste into a new session**. The plan keeps your choices, exclusions and requirements.

### Selectable questions

When a question has a small set of distinct answers, the skill asks it through the host's built-in question tool, so you pick an option instead of typing:

| Tool | Question tool | Notes |
|------|---------------|-------|
| Claude Code | `AskUserQuestion` | Works in every mode. Supports multi-select. |
| Codex CLI | `request_user_input` | Works in **Plan mode** (press `Shift+Tab` to switch to it). To use it in Default mode, enable the experimental feature: `codex --enable default_mode_request_user_input`, or add `default_mode_request_user_input = true` under `[features]` in `~/.codex/config.toml`. |
| Other agents | none | Questions are shown as a numbered list, and you reply with a number or your own answer. |

Open-ended questions, such as names, numbers or descriptions, are still asked as plain text. If Codex's tool is unavailable, the skill switches to numbered plain-text questions.

### What the final plan contains

The plan is a single Markdown code block, ready to copy. It includes:

- **Task and execution instructions.** Tells the next AI to keep your decisions, add nothing unrequested, and ask before making any choice you didn't specify.
- **Context and starting point.** What exists, what should change, and what must stay unchanged. It also lists any files or screenshots you need to attach.
- **Confirmed choices and boundaries.** Every decision you made, including exact names, values, technologies and things you explicitly don't want.
- **Ordered implementation steps.** The work in dependency order, with each step linked to the decisions it must preserve.
- **Acceptance and verification.** Checks that show the result matches your requirements.
- **Final delivery and deferred decisions.** What the end result should be, and any decisions you postponed. The next AI must stop and ask you before working on those.

### What it won't do

- Write code or build anything. The plan is the only output.
- Produce a PRD, design document or draft summary along the way.
- Treat "common practice" as your preference.
- Start building after the plan or hand off to another skill automatically.

### Usage

After installing, ask your AI tool for it by name, or describe what you want:

```text
Use clarify-to-plan: I want to build a habit-tracking mobile app.
```

```text
Interview me before planning. I want to add dark mode to my website.
```

In Claude Code you can also run it directly as a slash command:

```text
/clarify-to-plan add CSV export to the reports page
```

Answer the questions, confirm when you're ready, then copy the plan into a **new session**. The new session gets a clean context and follows the plan exactly.

---

## Repository layout

```
skills/
└── clarify-to-plan/
    └── SKILL.md
```

To add a new skill, create `skills/<skill-name>/SKILL.md` with `name` and `description` frontmatter. The `npx skills add` command finds it automatically.

## License

No license has been chosen yet.
