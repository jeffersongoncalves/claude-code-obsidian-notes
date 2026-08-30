<div class="filament-hidden">

![Obsidian Notes](https://raw.githubusercontent.com/jeffersongoncalves/claude-code-obsidian-notes/main/art/jeffersongoncalves-claude-code-obsidian-notes.png)

</div>

# Obsidian Notes (Claude Code plugin)

<p align="center">
  <a href="https://github.com/jeffersongoncalves/claude-code-obsidian-notes/releases/latest"><img src="https://img.shields.io/github/v/release/jeffersongoncalves/claude-code-obsidian-notes" alt="Latest Release" /></a>
  <a href="LICENSE"><img src="https://img.shields.io/github/license/jeffersongoncalves/claude-code-obsidian-notes" alt="License" /></a>
</p>

Claude Code plugin: a skill and a `/obsidian-note` command that persist documentation, decisions, and summaries as Markdown notes in the user's Obsidian vault, by shelling out to the [`obsidian-notes-cli`](https://github.com/jeffersongoncalves/obsidian-notes-cli).

Part of a 3-repo integration:

- [`obsidian-notes-cli`](https://github.com/jeffersongoncalves/obsidian-notes-cli) — does the actual writing; this plugin is a thin wrapper around it.
- [`obsidian-claude-notes`](https://github.com/jeffersongoncalves/obsidian-claude-notes) — Obsidian plugin (sidebar, live refresh) for browsing what gets written.
- **`claude-code-obsidian-notes`** (this repo).

## Install

```bash
composer global require jeffersongoncalves/obsidian-notes-cli
```

Then in Claude Code:

```
/plugin marketplace add jeffersongoncalves/claude-code-obsidian-notes
/plugin install obsidian-notes
```

Set `OBSIDIAN_VAULT` to your vault's path, or run `obsidian-notes vault:init <path>` once so `.claude-notes.json` is discoverable from your project directories.

## Usage

- `/obsidian-note "Decision: use SQLite"` — saves the current conversation's relevant content under that title.
- Or just ask: "documenta essa decisão no obsidian" / "save this to my Obsidian notes" — the `obsidian-notes` skill picks it up.

Nothing is written automatically at the end of a session — only on an explicit ask.

## How it resolves things

- **Vault**: `OBSIDIAN_VAULT` env var, or the nearest ancestor `.claude-notes.json` walking up from the current directory.
- **Project**: the current repo's folder/remote name — groups notes in the vault and in the Obsidian plugin's sidebar.

See `skills/obsidian-notes/SKILL.md` for the exact resolution and CLI invocation logic.
