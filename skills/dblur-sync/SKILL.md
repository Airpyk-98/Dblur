# Dblur Sync Skill

This skill enables Hermes agents to maintain a synchronized context using a GitHub repository named `Dblur` under the user's account. The agent will periodically pull and push to this repository to ensure context persistence across VPS migrations and sessions.

## Repository Details
- **URL**: https://github.com/Airpyk-98/Dblur
- **Visibility**: Public
- **Main Files**:
  - `Dblur_file`: A concise summary of the current context (updated every 5 minutes)
  - `logs/`: Directory containing detailed log files with timestamps
  - `context/`: Directory containing detailed context files, each referenced by a summary entry in `Dblur_file`

## Sync Pattern (Every 5 Minutes)
1. **Pull** the latest changes from the repository to ensure we have the most recent context.
2. **Read** `Dblur_file` to get the current context summary.
3. **Perform** the agent's task (this is outside the skill; the skill only handles sync).
4. **Update** `Dblur_file` with any new important context (keep it concise, preferably under 20 lines).
5. **Log** detailed information: create a new log file in `logs/` with a timestamp, or append to a session log.
6. **Create** detailed context files in `context/` as needed, and reference them in `Dblur_file` (e.g., `See context/2026-05-18_12-00-00_task_details.md`).
7. **Commit** and **push** the changes back to the repository.

## File Conventions
- **Dblur_file**: Should start with a timestamp and a brief summary. Example:
  ```
  # Dblur Context Summary
  Last updated: 2026-05-18 12:00:00 UTC

  ## Current Context
  - Task: Setting up GitHub sync.
  - Next steps: Test pull/push cycle.
  - Related details: See context/2026-05-18_12-00-00_setup.md
  ```
- **Log Files**: Named `logs/sync_YYYY-MM-DD_HH-MM-SS.log` or `logs/session_<session_id>.log`.
- **Context Files**: Named `context/YYYY-MM-DD_HH-MM-SS_description.md`.

## Implementation Notes for Agents
- Use the GitHub token provided by the user (or authenticate via `gh` CLI if available).
- If pushing fails due to authentication, the agent should notify the user (via the current conversation) that manual intervention may be needed.
- The agent should handle network failures gracefully (retry a few times, then skip the sync cycle and continue).
- To avoid excessive commits, only push if there are actual changes to `Dblur_file` or new log/context files.

## Setup Instructions (for the user or another agent)
1. Ensure the repository https://github.com/Airpyk-98/Dblur exists and is public.
2. Configure Git credentials (token or SSH) so that the agent can push.
3. The agent can then invoke this skill's sync routine every 5 minutes (e.g., via a cron job or internal timer).

## Example Commands (for reference)
```bash
# Clone (if not already present)
git clone https://github.com/Airpyk-98/Dblur.git

# Configure git (if needed)
git config user.email "hermes@agent.local"
git config user.name "Hermes Agent"

# Pull latest
git pull

# After working, update files...
git add Dblur_file logs/ context/
git commit -m "Context update: <brief description>"
git push
```

## Recovery Procedure
If the agent is started on a new VPS:
1. Clone the repository (if not already present).
2. Run the sync pull to get the latest `Dblur_file`.
3. Read `Dblur_file` to understand the current context.
4. Proceed with the task, then continue with the regular 5-minute sync cycle.
