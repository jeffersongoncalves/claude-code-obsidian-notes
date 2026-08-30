<div class="filament-hidden">

![Obsidian Notes](https://raw.githubusercontent.com/jeffersongoncalves/claude-code-obsidian-notes/main/art/jeffersongoncalves-claude-code-obsidian-notes.png)

</div>

# Obsidian Notes (Claude Code plugin)

<p align="center">
  <a href="https://github.com/jeffersongoncalves/claude-code-obsidian-notes/releases/latest"><img src="https://img.shields.io/github/v/release/jeffersongoncalves/claude-code-obsidian-notes" alt="Latest Release" /></a>
  <a href="LICENSE"><img src="https://img.shields.io/github/license/jeffersongoncalves/claude-code-obsidian-notes" alt="License" /></a>
</p>

Claude Code plugin: a skill and a `/obsidian-note` command that persist documentation, decisions, and summaries as Markdown notes in your Obsidian vault, by shelling out to the [`obsidian-notes-cli`](https://github.com/jeffersongoncalves/obsidian-notes-cli). This repo has no logic of its own beyond that — no parsing, no vault access, no frontmatter building. The CLI does all of it.

Part of a 3-repo integration:

- [`obsidian-notes-cli`](https://github.com/jeffersongoncalves/obsidian-notes-cli) — does the actual writing; this plugin is a thin wrapper around it.
- [`obsidian-claude-notes`](https://github.com/jeffersongoncalves/obsidian-claude-notes) — Obsidian plugin (sidebar, live refresh) for browsing what gets written.
- **`claude-code-obsidian-notes`** (this repo).

## Requirements

- `obsidian-notes-cli` on `PATH` (or resolvable another way — see Install)
- A vault path the CLI can resolve: `OBSIDIAN_VAULT` env var, or a `.claude-notes.json` findable by walking up from your project directory

## What's in this repo

| Path | What it is |
|---|---|
| `skills/obsidian-notes/SKILL.md` | The skill: teaches Claude Code when to persist a note (trigger phrases) and exactly how — vault/project resolution, the CLI invocation shape, what to do if the CLI isn't installed. |
| `commands/obsidian-note.md` | The `/obsidian-note "<title>"` slash command — same skill, explicit trigger with an explicit title. |
| `.claude-plugin/plugin.json` / `marketplace.json` | Plugin + marketplace manifests, so `/plugin install` can find it. |

## Install

```bash
composer global require jeffersongoncalves/obsidian-notes-cli
```

Then in Claude Code:

```
/plugin marketplace add jeffersongoncalves/claude-code-obsidian-notes
/plugin install obsidian-notes
```

Set `OBSIDIAN_VAULT` to your vault's path, or run `obsidian-notes vault:init <path>` once so `.claude-notes.json` is discoverable from your project directories:

```bash
export OBSIDIAN_VAULT=/path/to/vault
# or, per-vault:
obsidian-notes vault:init /path/to/vault
```

## Usage

```
/obsidian-note "Decision: use SQLite"
```

saves the current conversation's relevant content under that title. Or just ask in plain language — any of these trigger the skill:

- "salva isso no obsidian"
- "documenta essa decisão no obsidian"
- "anota isso no obsidian"
- "save this to my Obsidian notes"
- "persist this as an Obsidian note"

Nothing is written automatically at the end of a session, and there's no hook wired up for it — only an explicit ask (skill trigger or slash command) ever writes a note. If you want session-end auto-capture, that's a deliberate scope cut, not an oversight — add a `Stop` hook calling the CLI yourself if you want it.

## How it resolves things

- **Vault**: `OBSIDIAN_VAULT` env var first; otherwise the nearest ancestor `.claude-notes.json`, found by walking up from the current working directory.
- **Project**: the current repo's folder name, or its `git remote get-url origin` basename when available — this is what groups notes both in the vault's folder structure and in the Obsidian plugin's sidebar.
- **Body**: written as clean Markdown and piped to the CLI over stdin — no frontmatter added here, the CLI handles `source`/`project`/`title`/`tags`/`created`/`updated` itself.

If neither `OBSIDIAN_VAULT` nor a `.claude-notes.json` resolves, the skill asks for a vault path rather than guessing one.

See `skills/obsidian-notes/SKILL.md` for the exact logic Claude Code follows.

## License

[MIT](LICENSE).
