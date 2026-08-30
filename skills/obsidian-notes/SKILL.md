---
name: obsidian-notes
description: This skill should be used when the user asks to "salva isso no obsidian", "documenta essa decisão no obsidian", "anota isso no obsidian", "persist this as an Obsidian note", "write this decision to Obsidian", "save this to my notes", or invokes /obsidian-note. Writes a Markdown note (with proper frontmatter) into the user's Obsidian vault via the obsidian-notes-cli, so it shows up organized and searchable in Obsidian.
version: 1.0.0
---

# Obsidian Notes

Persists a piece of documentation, a decision, or a summary as a Markdown note in the user's Obsidian vault by shelling out to the [`obsidian-notes-cli`](https://github.com/jeffersongoncalves/obsidian-notes-cli). Only trigger on an explicit ask (a slash command, or the user directly asking to save/document something in Obsidian) — never write a note automatically at the end of a session; that produces noise nobody asked for.

## Resolving the vault

In this order:

1. `OBSIDIAN_VAULT` environment variable, if set.
2. Walk up from the current working directory looking for a `.claude-notes.json` — its containing directory is the vault root.

If neither resolves, ask the user for the vault path (or offer to run `obsidian-notes vault:init <path>` first) rather than guessing one.

## Resolving the project name

Use the current repo's folder name (or `git remote get-url origin` basename if available) as `--project`. This groups notes by project in the vault's folder structure and in the Obsidian plugin's sidebar.

## Writing the note

1. Write the note body as clean Markdown (no need to add your own frontmatter — the CLI adds `source`, `project`, `title`, `tags`, `created`, `updated` automatically).
2. Run:

   ```bash
   echo "<body>" | obsidian-notes note:create --project=<project> --title="<title>" --tags=<tag1> --tags=<tag2> --vault="<vault path>"
   ```

   Prefer piping a real multi-line body (a heredoc or a temp file) over `echo` for anything longer than one line.
3. Report back the path the CLI printed (`Note written to ...`) — don't just say "done".

## If the CLI isn't installed

Tell the user to install it (`composer global require jeffersongoncalves/obsidian-notes-cli`, or download the phar from its releases) rather than trying to replicate its frontmatter/path logic by hand — the [`obsidian-claude-notes`](https://github.com/jeffersongoncalves/obsidian-claude-notes) Obsidian plugin's sidebar and the vault's `.claude-notes.json` contract both depend on notes being written the same way every time.
