---
description: Save a note to the user's Obsidian vault via obsidian-notes-cli
argument-hint: "<title>"
---

Write a Markdown note titled "$ARGUMENTS" to the user's Obsidian vault, following the `obsidian-notes` skill: resolve the vault (`OBSIDIAN_VAULT` env or an ancestor `.claude-notes.json`), resolve the project name from the current repo, then run `obsidian-notes note:create` with the current conversation's relevant content as the note body piped via stdin.

If no title was given as an argument, ask for one instead of inventing something generic.
