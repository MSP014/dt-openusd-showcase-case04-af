# Session End Protocol (Case 04)

"Shift complete. Ground Ops secured."

1. **Summary**:
    * Briefly summarise what was achieved during the session.
    * Update documentation if logic or structure changed.

2. **Dependency Check**:
    * **Caution:** If new packages were installed, run `pip freeze > requirements.txt` (or `pip-compile requirements.in`).

3. **Jira Lifecycle (Pre-commit Phase)**:
    * **Update**: Use `python tools/jira_link.py` to update task statuses.
    * **Log Work:** `python tools/jira_link.py worklog <KEY> <TIME>`.
    * **Sync Plan**: Run `python tools/sync_jira.py --save` (Check dry-run first!) to ensure `implementation.md` is up-to-date.
    * **Close**: If complete, `python tools/jira_link.py status <KEY> Done`.

4. **Hygiene & Git (Single Commit Phase)**:
    * Check `tools/` for temporary scripts. Delete or move to `_deprecated`.
    * Stage all files (including `requirements.txt` and `implementation.md` updates): `git add .`
    * Run hooks if in doubt: `pre-commit run --all-files`.
    * **PowerShell Constraint**: **FORBIDDEN** from using `&&`. Use separate `run_command` calls.
    * Commit message MUST be in **British English**.

5. **Weekly Report & Changelog**:
    * Write ONE concise sentence in **Russian** for the Friday Report (e.g., "Настроен VRP солвер для routing танкеров").
    * **Changelog Autobuild (Condition):** Check the last entry in the `README.md` Changelog. If the difference between today and the last entry is **> 7 days**, you MUST do the following:
        1. Query `git log` since the last update to see the technical history.
        2. Read the latest `Weekly Projects Green Reviews` notes in `g:\Мой диск\Obsidian\SpeLL_Obsidian\All Projects\_Global\Weekly Projects Green Reviews` to understand the business context.
        3. Synthesize the combined data into a concise, 1-sentence English summary.
        4. Prepend this entry to the `## 📜 Changelog` in `README.md`, formatted exactly as: `- **Week of [Mon Date], [Year]:** [Summary]`.

6. **Final**:
    * "Arthur signing off."
