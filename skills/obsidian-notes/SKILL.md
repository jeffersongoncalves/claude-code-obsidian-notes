---
name: obsidian-notes
description: This skill should be used when the user asks to "salva isso no obsidian", "documenta essa decisão no obsidian", "anota isso no obsidian", "persist this as an Obsidian note", "write this decision to Obsidian", "save this to my notes", or invokes /obsidian-note. Writes a Markdown note (with proper frontmatter) into the user's Obsidian vault via the obsidian-notes-cli, so it shows up organized and searchable in Obsidian.
version: 1.0.1
---

# Obsidian Notes

Persists a piece of documentation, a decision, or a summary as a Markdown note in the user's Obsidian vault by shelling out to the [`obsidian-notes-cli`](https://github.com/jeffersongoncalves/obsidian-notes-cli). Only trigger on an explicit ask (a slash command, or the user directly asking to save/document something in Obsidian) — never write a note automatically at the end of a session; that produces noise nobody asked for.

## Resolving the vault

The CLI resolves the vault itself, in this order:

1. `--vault` flag, if passed explicitly.
2. `OBSIDIAN_VAULT` environment variable.
3. The persisted default from `vault:config` (stored in `~/.obsidian-notes-cli/config.json`, set by `vault:init` or `vault:config <path>`).

Don't pass `--vault` unless the user gave an explicit path — let the CLI fall back to `OBSIDIAN_VAULT` or its persisted default. If none of those resolve (the CLI errors that no vault is configured), ask the user for a vault path — or offer to run `obsidian-notes vault:init <path>` for a new vault, or `obsidian-notes vault:config <path>` to set an existing one as default — rather than guessing.

`.claude-notes.json` lives in the vault root (scaffolded by `vault:init`) and holds `folderPattern`/`frontmatterDefaults` for how notes get organized in the vault — it's not part of vault-path resolution and isn't found by walking up from the cwd.

## Resolving the project name

Use the current repo's folder name (or `git remote get-url origin` basename if available) as `--project`. This groups notes by project in the vault's folder structure and in the Obsidian plugin's sidebar.

## Writing the note

1. Write the note body as clean Markdown (no need to add your own frontmatter — the CLI adds `source`, `project`, `title`, `tags`, `created`, `updated` automatically).
2. Run:

   ```bash
   echo "<body>" | obsidian-notes note:create --project=<project> --title="<title>" --tags=<tag1> --tags=<tag2>
   ```

   Only add `--vault="<vault path>"` if the user gave an explicit path this run — otherwise let the CLI resolve it (env var / persisted default) as described above. Prefer piping a real multi-line body (a heredoc or a temp file) over `echo` for anything longer than one line.
3. Report back the path the CLI printed (`Note written to ...`) — don't just say "done".

## If the CLI isn't installed

Tell the user to install it (`composer global require jeffersongoncalves/obsidian-notes-cli`, or download the phar from its releases) rather than trying to replicate its frontmatter/path logic by hand — the [`obsidian-claude-notes`](https://github.com/jeffersongoncalves/obsidian-claude-notes) Obsidian plugin's sidebar and the vault's `.claude-notes.json` contract both depend on notes being written the same way every time.
