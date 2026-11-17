# Automation instructions — Consolidated GitHub → Notion sync

**How to use:** Create **one** Cursor Automation that replaces the old separate syncs.

| Setting | Value |
|---------|--------|
| Name | Sync GitHub repos to Notion |
| Triggers | Push to `main` on each of: `CJarskiCPA/coding-agent-skills-library`, `CJarskiCPA/Cursor-Automations`, `CJarskiCPA/ai-employee-toolkit` |
| Tools | Notion MCP |
| Repo write / open PR | Off |

Paste everything inside the fence below into the automation prompt. Delete or disable the old automations named **Sync Cursor Automations to Notion** and **Sync Coding Agent Skills Library to Notion** after this one is saved.

```text
You keep Notion catalogs in sync with GitHub. This is a one-way sync: GitHub is the source of truth. Never edit files, commit, push, or open pull requests. Only update the Notion targets listed for the repo that triggered this run.

Detect which repository was checked out for this run (git remote, repo root files, or the push event). Then run ONLY the matching catalog section below. Do not sync the other catalogs on this run.

If the git history is shallow, run `git fetch --unshallow` so per-item history is accurate. Use @[MCP: notion] for all Notion reads and writes.

============================================================
A) Repo: CJarskiCPA/coding-agent-skills-library
============================================================
Notion target: Coding Agent Skills Library database
https://app.notion.com/p/77ffe8ae4a994c4a9d755bbe89e70073
data source collection://2247f347-6478-4f0c-a811-781be7eea82d

1. For every folder in `skills/` that contains a `SKILL.md`, collect:
   - Skill: the `name` from the SKILL.md frontmatter
   - Description: the `description` from the frontmatter, copied exactly
   - Category and Status: from that skill's entry in `catalog.json`. If it has no entry, leave Category empty and set Status to "Draft".
   - Version: `git log -1 --format=%h -- skills/<folder>`
   - Last updated: `git log -1 --format=%cs -- skills/<folder>` (date only)
   - GitHub source: https://github.com/CJarskiCPA/coding-agent-skills-library/tree/main/skills/<folder>
2. Read every row of the Coding Agent Skills Library database.
3. Match rows by the Skill column. Create a row for any skill that has none. Update fields that differ. Replace page content (layout below) only when Version changed, the row is new, or the page has no SKILL.md section yet.
4. Page content layout:
   - Gray callout with 🔄 icon: "Synced from GitHub: [skills/<folder>/SKILL.md](https://github.com/CJarskiCPA/coding-agent-skills-library/blob/main/skills/<folder>/SKILL.md). Edit it in GitHub; changes made here are overwritten on the next sync."
   - Level-2 heading whose text is `SKILL.md` as inline code
   - Complete SKILL.md in a markdown code block; fence one backtick longer than the longest run inside the file
5. If a row's skill no longer exists in `skills/`, set Status to "Retired". Never delete or archive rows.
6. If a Category value is not an existing option, add it.
7. Summarize created / updated / retired / unchanged.

============================================================
B) Repo: CJarskiCPA/Cursor-Automations
============================================================
Notion targets
- Parent catalog page: https://app.notion.com/p/4183fcffe35181eb85b6ee39f4855c55
- Automations database: https://app.notion.com/p/4183fcffe3518168956de474daadf35e
  data source collection://4183fcff-e351-81e0-b652-000bfe1fd425

1. Read `automations/catalog.json`. For every entry, collect Name, Description, Schedule, Status, Repo / Instructions URL, Version (`git log -1 --format=%h -- <docs or instructions>`), Last changed date (`%cs`), and docs body when present.
2. Read the parent catalog page and every automations database row.
3. Parent page: keep a short gray callout pointing at https://github.com/CJarskiCPA/Cursor-Automations; keep the inline database; do not delete unrelated blocks.
4. Match rows by Name. Create missing rows. Update Name, Description, Schedule, Status, and Repo / Instructions when they differ. Replace page content only when Version changed, the row is new, or there is no "Synced from GitHub" callout yet.
5. Detail page layout:
   - Gray 🔄 callout linking to the docs or instructions path on GitHub
   - Short Overview (1–3 sentences from catalog fields)
   - Level-2 heading with the docs/instructions filename as inline code
   - Full docs (or instructions) in a markdown code block with a longer fence if needed
   - Links section for repo, docs, instructions, and skill when present
6. If a row Name is no longer in catalog.json, set Status to "Retired". Never delete rows.
7. Summarize created / updated / retired / unchanged.

============================================================
C) Repo: CJarskiCPA/ai-employee-toolkit
============================================================
Notion targets
- Parent page (AI Employee Toolkit): https://app.notion.com/p/41b3fcffe3518048a8a5e45b3b0b8fee
- Shared skills database titled Marketing (INLINE on that parent page — one table for all departments):
  https://app.notion.com/p/41b3fcffe35181f788b4c4fb594552a8
  data source collection://41b3fcff-e351-8154-9454-000b5022b9b2

Structure rules
- Keep the shared skills database inline on the AI Employee Toolkit page.
- Never create per-department databases or a Marketing subpage.
- Department must be one of: Operations, Finance, Sales, People, HR, Marketing.

1. Read `catalog.json`. For every skill key, resolve its folder (`folder` field or the key). Collect:
   - Skill title: catalog key (e.g. ai-media-buyer)
   - Department: catalog `department` (required)
   - Description: SKILL.md frontmatter description
   - Category: from catalog when present; otherwise blank
   - Status: from catalog (default Active)
   - GitHub source: https://github.com/CJarskiCPA/ai-employee-toolkit/tree/main/<folder URL-encoded>
   - Version: `git log -1 --format=%h -- "<folder>"`
   - Last updated: `git log -1 --format=%cs -- "<folder>"`
   - Skill body: full `<folder>/SKILL.md`
2. Read the parent page and every shared-skills database row.
3. Parent page: keep the database inline; do not create Marketing child pages or extra department databases.
4. Match rows by Skill. Create missing rows. Update Department, Description, Category (when set), GitHub source, Status, Version, and Last updated when they differ. Replace page content only when Version changed, the row is new, or there is no "Synced from GitHub" callout yet.
5. Detail page layout (same shape as Coding Agent Skills Library):
   - Gray 🔄 callout linking to `<folder>/SKILL.md` on GitHub
   - Level-2 heading with `SKILL.md` as inline code
   - Complete SKILL.md in a markdown code block with a longer fence if needed
   - No extra Guides/PDF/reference sections on the Notion page
6. If a Skill is no longer in catalog.json, set Status to "Retired". Never delete rows.
7. Summarize created / updated / retired / unchanged.

If the checked-out repo is none of the three above, stop and say which repo was detected.
```
