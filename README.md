# Dblur Context Sync Repository

This repository is used by Hermes agents to maintain synchronized context across sessions and VPS migrations.

## Structure

- `Dblur_file` - Main context summary file (updated every 5 minutes)
- `logs/` - Detailed log files with timestamps
- `context/` - Detailed context files referenced by summaries

## Sync Pattern

Agents should:
1. Pull from this repository every 5 minutes
2. Read `Dblur_file` for current context summary
3. Push updates to `Dblur_file` and log files every 5 minutes
4. Create new detailed context files in `context/` as needed, referenced in the summary