# Obsidian Vault Config File Setup

**Date:** 2026-01-09
**Time:** 21:52

## What Was Created

Created a configuration file to store the location of the Obsidian vault for easy reference in future sessions.

## File Location

**Config File:** `/home/tom/.obsidian-config`

## File Contents

```bash
# Obsidian Vault Configuration
# This file stores the location of your Obsidian vault for easy reference

OBSIDIAN_VAULT_PATH=/mnt/c/Users/Tom/Documents/Dev
OBSIDIAN_VAULT_WINDOWS_PATH=C:\Users\Tom\Documents\Dev

# Usage:
# In future conversations, I can read this file to find your vault location
# You can also source this in bash: source ~/.obsidian-config
```

## How It Works

- **Location-Independent:** Works from any directory you run Claude Code from
- **Absolute Paths:** Uses absolute paths to your home directory (`/home/tom/`)
- **Cross-Platform:** Contains both Linux (WSL) and Windows path formats
- **Future Reference:** Can be read in future conversations to find vault location

## Usage in Future Sessions

When working with Obsidian in future conversations:
1. Mention checking the config file: "Check my .obsidian-config file"
2. Or simply mention: "Save this to my Obsidian vault"
3. The config file can be read to find: `/mnt/c/Users/Tom/Documents/Dev`

## Manual Usage (Optional)

You can also source this file in your bash session:
```bash
source ~/.obsidian-config
echo $OBSIDIAN_VAULT_PATH
```

---

**Note:** While this config file exists, Claude Code doesn't have automatic persistent memory across sessions. You may need to remind it to check this file when mentioning your Obsidian vault in new conversations.
