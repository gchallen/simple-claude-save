# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

simple-claude-save saves Claude Code session transcripts into a project's `.claude-sessions/` directory so they get committed alongside the code. It works via two mechanisms:

- **Claude SessionEnd hook**: copies transcripts when a Claude session ends
- **Git pre-commit hook**: copies transcripts and stages them before each commit

Both paths use the same script (`.claude/hooks/save-session.sh`) which finds transcripts at `~/.claude/projects/<encoded-path>/*.jsonl`.

## How it works

Projects that consume this repo copy or symlink `.claude/settings.json` and `.claude/hooks/` into their own `.claude/` directory, and `.githooks/pre-commit` into their `.githooks/`.

- `SessionStart` hook runs `git config core.hooksPath .githooks` to activate git hooks
- `SessionEnd` hook runs `save-session.sh` to copy transcripts
- `.githooks/pre-commit` delegates to `save-session.sh` which copies transcripts and stages them

The pre-commit hook will **fail** if `.claude-sessions/` is in `.gitignore` (since there's no point copying files that won't be committed). This repo itself gitignores `.claude-sessions/` since it's the tooling repo, not a consumer — so commits here require `--no-verify`.

## Committing changes

Since `.claude-sessions/` is gitignored in this repo, commits require `--no-verify` to bypass the pre-commit hook.
