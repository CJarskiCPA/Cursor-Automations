# Automation instructions — GitHub → Notion sync

**How to use:** Create one Cursor Automation. Attach this repository (`CJarskiCPA/Cursor-Automations`). Trigger: **GitHub push to `main`**. Add the **Notion** MCP via Add Tool or MCP. Paste everything inside the fence below into the automation prompt.

Modeled on **Sync Coding Agent Skills Library to Notion** (push to `main` → one-way Notion refresh; never edit the repo).

```text
Keep the Notion Cursor Automations catalog in sync with this repository. This is a one-way sync: GitHub is the source of truth. Never edit files, commit, push, or open pull requests in this repository. Only update the Notion pages and database listed below — nowhere else.

Notion targets
- Parent catalog page: https://app.notion.com/p/4183fcffe35181eb85b6ee39f4855c55
- Automations database: https://app.notion.com/p/4183fcffe3518168956de474daadf35e
  (data source collection://4183fcff-e351-81e0-b652-000bfe1fd425)
- Known detail page (existing row): Ramp → Puzzle classification
  https://app.notion.com/p/4183fcffe3518130bb01d4558d5a74c2

1. If the git history is shallow, run `git fetch --unshallow` so per-file history is accurate.
2. Read `automations/catalog.json`. For every entry, collect:
   - Name, Description, Schedule, Status (from the JSON)
   - Repo / Instructions: https://github.com/CJarskiCPA/Cursor-Automations/blob/main/<instructions>
   - Version: `git log -1 --format=%h -- <docs>` (fall back to <instructions> if docs is missing)
   - Last changed date: `git log -1 --format=%cs -- <docs>` (or instructions)
   - Docs body: full contents of the docs file when present
   - Skill path (optional): if `skill` is set, note the GitHub blob URL for links
3. Use @[MCP: notion] to read the parent catalog page and every row of the Cursor Automations Library database.
4. Parent page (catalog):
   - Keep a short gray callout that points to
     https://github.com/CJarskiCPA/Cursor-Automations and says setup lives in GitHub.
   - Keep the inline database. Do not delete the database or unrelated blocks.
   - If the callout text is stale vs README.md purpose, update only that callout.
5. Match database rows to catalog entries by the Name column.
   - Create a row for any catalog entry that has none.
   - Update Name, Description, Schedule, Status, and Repo / Instructions when they differ.
   - Replace the row's page content (layout in step 6) when Version changed, the row is new, or the page has no "Synced from GitHub" callout yet; otherwise leave page content untouched.
6. Detail page content layout for each automation:
   - A gray callout with the 🔄 icon:
     "Synced from GitHub: [<docs or instructions path>](https://github.com/CJarskiCPA/Cursor-Automations/blob/main/<path>). Edit it in GitHub; changes made here are overwritten on the next sync."
   - A short Overview section summarizing Description + Schedule + Status from catalog.json (1–3 sentences).
   - A level-2 heading whose text is the docs filename formatted as inline code (or `instructions` if no docs).
   - The complete docs file (or instructions file if no docs), copied exactly, in a markdown code block. Make the fence one backtick longer than the longest run of backticks in the file.
   - A Links section with GitHub URLs for the repo, docs, instructions, and skill (when present).
7. If a database row's Name is no longer in catalog.json, set Status to "Retired" and leave its page content as is. Never delete or archive rows or pages.
8. End with a short summary listing which automations were created, updated, retired, or unchanged. If everything already matches, make no Notion edits and say so.
```
